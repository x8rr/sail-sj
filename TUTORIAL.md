# Scramjet Tutorial
Welcome! I'll show you how to set up Scramjet on your website with the files in this repository. Follow the steps below.

## Using Static Languages

### Getting Started
First, navigate into your development folder
```bash
cd folder-name
```

Next, clone these files into your directory
```bash
git clone https://github.com/x8rr/sail-sj
```

Then, move everything in the new folder into your directory, except git. You can then delete the folder.
```bash
mv sail-sj/sail/baremux ./
mv sail-sj/sail/scramjet ./
mv sail-sj/sail/libcurl ./
mv sail-sj/sw.js ./
mv sail-sj/index.html ./
rm -rf sail-sj
```

### Setting it Up
Navigate to `index.html` in your directory (the cloned version), and look for each script import. They should look like ``<script src="/path/to/file/"></script>``. Make sure you correct each path to where the files actually are!

Next, go to `sw.js` and also change your script imports. This is very important, or it will not work.

Make sure you also changed the imports for BareMux, Libcurl, and Scramjet. They are in an inline `<script>` tag at the end of the file.

## Using a framework (like React)
Using a framework with this repository is very simple!

### Getting started
First, navigate into your development folder
```bash
cd folder-name
```

Next, clone these files into your directory
```bash
git clone https://github.com/x8rr/sail-sj
```

Then, move everything in the new folder into your **PUBLIC** directory, except git. You can then delete the folder.
```bash
mv sail-sj/sail/baremux ./public
mv sail-sj/sail/scramjet ./public
mv sail-sj/sail/libcurl ./public
mv sail-sj/sw.js ./public
mv sail-sj/index.html ./public
rm -rf sail-sj
```

### Setting it up
Then, navigate to `/index.html`, and add the following to your head:
```html
<script src="/scramjet/scramjet.all.js"></script>
<script src="/baremux/index.js"></script>
<script>
    navigator.serviceWorker.register("/sw.js")
</script>
```

**Make sure your paths match!**

Next, make sure you set up your BareMux controller.

Add this to the body of your HTML.

```html
<script>
    const connection = new BareMux.BareMuxConnection("/baremux/worker.js") // Make sure your path is correct!
    connection.setTransport("/libcurl/index.mjs", [{ websocket: "wss://yourdomain.com/wisp/"}]) // Make sure your path is correct! Replace Wisp server in production.
</script>
```

Now, you can encode URLs to navigate to them!

### Scramjet Controller Config
You can change the following properties on the Controller:
```json
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

For more information about the Scramjet Controller, please read [here](https://mintlify.wiki/mercuryworkshop/scramjet/api/scramjet-controller).