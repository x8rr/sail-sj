# Scramjet Setup Guide

This guide explains how to integrate Scramjet into a static site or a frontend framework project using the `sail-sj` repository.

---

# Requirements

Before starting, ensure you have:

* A modern browser (Chromium-based recommended)
* A local development server (Vite, Next.js, http-server, etc.)
* HTTPS or localhost (required for Service Workers)
* Git installed

---

# Repository Overview

After setup, your project should contain:

```
your-project/
├── index.html
├── sw.js
├── baremux/
│   └── worker.js
├── scramjet/
│   └── scramjet.all.js
├── libcurl/
│   └── index.mjs
```

---

# Installation

## 1. Clone the repository

```bash
cd your-project
git clone https://github.com/x8rr/sail-sj
```

---

## 2. Copy required files

From `sail-sj/sail/`, copy into your project root (or public directory if using a framework):

### Static site setup

```bash
# These commands are for Unix based systems
cp -r sail-sj/sail/baremux ./
cp -r sail-sj/sail/scramjet ./
cp -r sail-sj/sail/libcurl ./
cp sail-sj/sw.js ./
cp sail-sj/index.html ./
rm -rf sail-sj
```

Then, update all paths inside `index.html` accordingly.

For example, `<script src="/sail/scram/scramjet.all.js/">` should become `<script src="/scram/scramjet.all.js">`, or wherever you put your new Scramjet files. Make sure that ALL PATHS MATCH IN ALL FILES! This includes the Service Worker (sw.js).

### Framework setup (React, Vite, etc.)

Copy into your public directory:

```bash
cp -r sail-sj/sail/baremux ./public/
cp -r sail-sj/sail/scramjet ./public/
cp -r sail-sj/sail/libcurl ./public/
cp sail-sj/sw.js ./public/
cp sail-sj/index.html ./public/
rm -rf sail-sj
```

---

# Configuration

## 1. Fix script paths in `index.html`

Open `index.html` and ensure all script paths match your final structure.

Example:

```html
<script src="/scramjet/scramjet.all.js"></script>
<script src="/baremux/index.js"></script>
```

If you placed files in a subdirectory, update paths accordingly.


## 2. Register Service Worker

Add this inside `<head>` or at the end of `<body>`:

```html
<script>
navigator.serviceWorker.register("/sw.js");
</script>
```

### Important Notes

* Must be served over HTTPS or `localhost`
* Path must match actual file location
* If registration fails, check browser console

---

## 3. Configure BareMux

Add this inside `<body>`:

```html
<script>
const connection = new BareMux.BareMuxConnection("/baremux/worker.js");

connection.setTransport(
  "/libcurl/index.mjs",
  [{ websocket: "wss://YOUR_WISP_SERVER_HERE" }]
);
</script>
```

## 4. Initialize Scramjet
```javascript
// After BareMux init
const { ScramjetController } = $scramjetLoadController()
const scramjet = new ScramjetController({
  files: {
    all: pathtoall
    wasm: pathtowasm
    sync: pathtosync
  },
  prefix: "/prefix/here/" // this is optional
}
scramjet.init()
```


---

### Wisp Server

Replace:

```
wss://YOUR_WISP_SERVER_HERE
```

with your deployed Wisp server endpoint.

Example:

```
wss://wisp.example.com/
```

---

# How Navigation Works

Scramjet encodes URLs before navigation. You should use the Scramjet API exposed in the runtime environment:

```js
scramjet.encodeUrl("https://example.com");
scramjet.decodeUrl(encodedUrl);
```

---

# Scramjet Controller Configuration

The Scramjet controller supports the following structure:

```ts
{
  prefix: string;
  globals: {
    wrapfn: string;
    wrappropertybase: string;
    wrappropertyfn: string;
    cleanrestfn: string;
    importfn: string;
    rewritefn: string;
    metafn: string;
    setrealmfn: string;
    pushsourcemapfn: string;
    trysetfn: string;
    templocid: string;
    tempunusedid: string;
  };
  files: {
    wasm: string;
    all: string;
    sync: string;
  };
  flags: Partial<ScramjetFlags>;
  siteFlags?: Record<string, Partial<ScramjetFlags>>;
  codec: {
    encode: (url: string) => string;
    decode: (url: string) => string;
  };
}
```

Documentation reference:
[https://mintlify.wiki/mercuryworkshop/scramjet/api/scramjet-controller](https://mintlify.wiki/mercuryworkshop/scramjet/api/scramjet-controller)

---

# Framework Setup Notes

## Vite / React / SPA frameworks

Place all assets inside `public/`.

Ensure:

* `/sw.js` is accessible at root
* `/baremux/`, `/scramjet/`, `/libcurl/` are publicly served
* No bundler transforms these files

### Example (Vite)

```
public/
├── sw.js
├── baremux/
├── scramjet/
├── libcurl/
```

---

# Troubleshooting

## Service worker does not register

* Ensure HTTPS or localhost
* Check correct path `/sw.js`
* Open DevTools → Application → Service Workers

---

## Blank page or navigation not working

* Verify script paths in `index.html`
* Ensure Scramjet scripts are loading
* Check console for missing imports

---

## BareMux fails to connect

* Verify `/baremux/worker.js` exists
* Confirm Wisp server is reachable
* Check WebSocket URL (`wss://` required in production)

---

## Assets 404

* Ensure correct public directory placement
* Confirm server is serving static files correctly

---

# Production Notes

* Always use HTTPS in production
* Do not expose internal Wisp infrastructure publicly without protection
* Keep Scramjet and BareMux versions consistent across updates
