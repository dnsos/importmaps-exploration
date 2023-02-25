# `importmap`s exploration

This repository is a little exploration of using [`importmap`s](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script/type/importmap) as an alternative to using JavaScript bundlers and build steps. It is loosely following this [introduction blog post](https://www.honeybadger.io/blog/import-maps/) to `importmap`s.

## Setup

There are two simple HTML files here: `index.html` and `about.html`. Their contents are both hardcoded to avoid unnecessary distraction from the main topic of this exploration.

> Note that both files have largely the same content because this exploration is done by importmapping [Turbo](https://turbo.hotwired.dev/) which is a library for intercepting HTTP requests and only updating the HTML content without a full page reload, essentially enabling a Single Page application feel for HTML.

The interesting pieces are the script tags:

```html
<script type="importmap">
  {
    "imports": {
      "@hotwired/turbo": "https://cdn.skypack.dev/@hotwired/turbo"
    }
  }
</script>
```

```html
<script type="module">
  import * as Turbo from "@hotwired/turbo";
</script>
```

It is important that they appear in the HTML in that order ensuring that the `type="module"` knows how to resolve the package.

---

We can verify that the `importmap` is working by starting a server, e.g. via `npx http-server` and typing `Turbo` into the browser console. If no errors show up, the `importmap` is properly resolving.

## Pinning packages

If we want to explicitly pin which version we resolve, we can do so by pinning it like so:

```html
<script type="importmap">
  {
    "imports": {
      "@hotwired/turbo": "https://cdn.skypack.dev/pin/@hotwired/turbo@v7.2.5-jwYMmpCb8mVWq1WRn6YH/mode=imports,min/optimized/@hotwired/turbo.js"
    }
  }
</script>
```

It seems that the only way to find out the URL for pinning on Skypack is via visiting https://cdn.skypack.dev/@hotwired/turbo where something like this should show up:

```js
/*
 * Skypack CDN - @hotwired/turbo@7.2.5
 *
 * Learn more:
 *   📙 Package Documentation: https://www.skypack.dev/view/@hotwired/turbo
 *   📘 Skypack Documentation: https://www.skypack.dev/docs
 *
 * Pinned URL: (Optimized for Production)
 *   ▶️ Normal: https://cdn.skypack.dev/pin/@hotwired/turbo@v7.2.5-jwYMmpCb8mVWq1WRn6YH/mode=imports/optimized/@hotwired/turbo.js
 *   ⏩ Minified: https://cdn.skypack.dev/pin/@hotwired/turbo@v7.2.5-jwYMmpCb8mVWq1WRn6YH/mode=imports,min/optimized/@hotwired/turbo.js
 *
 */

// Browser-Optimized Imports (Don't directly import the URLs below in your application!)
export * from '/-/@hotwired/turbo@v7.2.5-jwYMmpCb8mVWq1WRn6YH/dist=es2019,mode=imports/optimized/@hotwired/turbo.js';
export {default} from '/-/@hotwired/turbo@v7.2.5-jwYMmpCb8mVWq1WRn6YH/dist=es2019,mode=imports/optimized/@hotwired/turbo.js';
```

## Open questions

- How to resolve packages from other CDN's such as [UNPKG](https://unpkg.com/), [JSPM](https://jspm.org/), etc.?
- How to import CSS files from packages that require CSS to function properly such as [MapLibre GL](https://maplibre.org/maplibre-gl-js-docs/api/#quickstart)?