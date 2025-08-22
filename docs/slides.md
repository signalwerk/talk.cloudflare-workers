---
theme: signalwerk
title: Cloudflare Workers
---

```fm
style: negative
background: true
```

## Hello _👋_

# {{process.content.frontmatter.title}}

_Serverless JavaScript als Microservices_

<footer>

2025 · DDOS Schaffhausen · Stefan Huber

</footer>

--s--

```fm
style: negative
background: true
```

## Was ist die Idee?

# Anstatt <br> _monolithischer Anwendung_ <br>nur <br>**kleine Funktionen**

--s--

## Warum?

- _Wartung/Betrieb_ einfach
- Statische Seiten mit _kleinen Funktionen_
- _keine Kosten_ (Cloudflare – kostenlose Preisstufe)

--s--

```fm
style: negative
background: true
```

## Demo

# _Hallo Welt!_

--s--

## Hallo Welt!

```js
export default {
  async fetch(request, env, ctx) {
    return new Response("Hallo Welt!");
  },
};
```

<footer>

[Workers Dashboard](https://dash.cloudflare.com/?to=/:account/workers-and-pages)

</footer>

--s--

## Pfad & Suchparameter

<small>

```js
export default {
  async fetch(request, env, ctx) {
    const url = new URL(request.url); // https://domain/pfad/?name=ODW
    const { pathname, searchParams } = url;

    if (pathname === "/pfad/") {
      const name = searchParams.get("name");
      const text = `Willkommen ${name || "Welt!"}`;

      return new Response(text);
    }

    return new Response("Hallo Welt!");
  },
};
```

</small>

<footer>

[Workers Dashboard](https://dash.cloudflare.com/?to=/:account/workers-and-pages)

</footer>

--s--

## Key/Value Speicher

<div style="font-size: 0.3em;">

```js
export default {
  async fetch(request, env) {
    const url = new URL(request.url);
    const page = url.searchParams.get("page") || "default";
    const key = `count:${page}`;

    // CORS
    const cors = {
      "access-control-allow-origin": "*",
      "access-control-allow-methods": "GET,POST,OPTIONS",
      "access-control-allow-headers": "content-type",
      "cache-control": "no-store, no-cache, must-revalidate, max-age=0",
    };

    if (request.method === "OPTIONS") {
      // Preflight: do NOT increment
      return new Response(null, { status: 204, headers: cors });
    }

    if (request.method === "POST") {
      const prev = parseInt((await env.KV.get(key)) || "0", 10);
      const next = prev + 1;
      await env.KV.put(key, String(next));
      return new Response(String(next), {
        headers: { "content-type": "text/plain; charset=utf-8", ...cors },
      });
    }

    // GET (or HEAD) → just read without increment
    const current = (await env.KV.get(key)) || "0";
    return new Response(String(current), {
      headers: { "content-type": "text/plain; charset=utf-8", ...cors },
    });
  },
};
```

</div>

<footer>

[Workers Dashboard](https://dash.cloudflare.com/?to=/:account/workers-and-pages)
<span> · </span>
[KV Dashboard](https://dash.cloudflare.com/?to=/:account/workers/kv/namespaces)
<span> · </span>
[Demo](https://signalwerk.github.io/lightning-talk-cloudflare-worker-demo/)
<span> · </span>
[Demo GitHub](https://github.com/signalwerk/lightning-talk-cloudflare-worker-demo)

</footer>

--s--

## Weitere Anwendungen...

- Proxy und verändern von bestehenden Websites
- Website zu Bild rendern (Bild für [TRMNL](https://usetrmnl.com/))
- Authentifizierung und Autorisierung
- AI-Zeugs
- ...

--s--

```fm
style: negative
background: true
```

## exit 0; thx

# _Fragen?_

<style>
  .contact-links {
    padding-bottom: 1rem;
    font-size: .8rem;
  }
  .contact-links a {
    color: white;
  }
  .contact-links a:after {
    display: none;
  }
</style>

<footer class="contact-links">

**Stefan Huber** · sh@signalwerk.ch  
[mastodon.social/@signalwerk](https://mastodon.social/@signalwerk)  
[instagram.com/signalwerk](https://instagram.com/signalwerk)  
[linkedin.com/in/signalwerk](https://linkedin.com/in/signalwerk)  
[github.com/signalwerk](https://github.com/signalwerk)

</footer>
