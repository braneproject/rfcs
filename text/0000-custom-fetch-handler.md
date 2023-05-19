---
Status: Draft
Author: Hyeseong Kim <tim@daangn.com>
Start Date: 2023-04-03
RFC PR: (leave this empty)
Related Issues: (leave this empty)
---

# Custom Fetch Handler

## Summary

This proposes a way to customize the [Fetch API]'s behavior based on well-known Web APIs.

## Motivation

In the Brane Project, every network requests should be checked on the host, but cannot be intercepted since they operate on different context (cross-origin).

This proposal is useful in many situations. When user want to control requests by cross-origin iframes/workers on the host, when creating virtual requests that operate on HTTP semantics, etc.

## Detailed design

To solve this problem, we need a way to delegate fetch requests to different contexts. It has a similar purpose to the [Service Worker's fetch handler], but it should be able to customize origin constraints.

### Interface

```webidl
interface FetchObject : EventTarget {
  [NewObject] Promise<Response> fetch(RequestInfo input, optional RequestInit init = {});
  boolean sendBeacon(USVString url, optional BodyInit? data = null);

  const XMLHttpRequest XMLHttpRequest;


  [NewObject] sequence<Promise<any>> drainExtendLifecyclePromises();

  attribute EventHandler onfetch;
}

interface FetchObjectInit {
  [NewObject] Promise<Response> fetch(FetchEvent event);
}

interface FetchHandlerModule {
  [NewObject] FetchObject register(FetchObjectInit init = {});
}
```

There are definitions inherited from Web's living standard

- [`EventTarget`](https://dom.spec.whatwg.org/#eventtarget), [`EventHandler`](https://html.spec.whatwg.org/multipage/webappapis.html#eventhandler) definition from the DOM standard
- [`Response`](https://fetch.spec.whatwg.org/#response-class), [`RequestInfo`](https://fetch.spec.whatwg.org/#requestinfo), [`RequestInit`](https://fetch.spec.whatwg.org/#requestinit), [`BodyInit`](https://fetch.spec.whatwg.org/#bodyinit) definitions from the Fetch standard
- [`FetchEvent`](https://w3c.github.io/ServiceWorker/#fetchevent) definition from the Service Worker standard
- [`XMLHttpRequest`](https://xhr.spec.whatwg.org/#interface-xmlhttprequest) definition from the XHR standard

#### `FetchHandlerModule`

The `register` method creates a new `FetchObject` instance.

#### `FetchObject`

The `FetchObject` is basically a [ponyfill](https://github.com/sindresorhus/ponyfill) to [`ServiceWorkerGlobalScope`](https://w3c.github.io/ServiceWorker/#serviceworkerglobalscope-interface). It has intentionally subset interface to it to get better interoperability with existing tools for service worker.

The `fetch` method makes as same network request as the [Fetch API], but can be intercepted by the `fetch` event listeners. It should append the result promise to the extend lifecycle promises if `{ keepalive: true }` passed.

The `sendBeacon` method transmits data to url same as the [Beacon API], but can be intercepted by the `fetch` event listener. It should append the result promise to the extend lifecycle promises.

The `XMLHttpRequest` is a constructor for [XMLHttpRequest API] that initiate a network request, but can be intercepted by the the `fetch` event listener. Since the synchronous mode cannot be implemented in a single thread context, the constructor must throw a `TypeError` when the flag is set.

The `drainExtendLifecyclePromises` method should returns extend lifecycle promises made by `event.waitUntil(...)`, `sendBeacon(...)`, fetch with `{ keepalive: true }`, etc. And cleanup the promises.

### Usage

```js
import * as FetchHandler from '@braneproject/fetch-handler';

const originalFetch = globalThis.fetch;

const { fetch } = FetchHandler.register({
  async fetch(event) {
    // Pass-through to original fetch
    if (event.request.url.origin !== self.location.origin) {
      return;
    }
    // Prevent the default, and handle it by custom code.
    return event.respondWith(requestToHost(event.request));
  },
});

globalThis.fetch = fetch;
```

It can be combined with a `sw.js` script.

```js
import * as FetchHandler from '@braneproject/fetch-handler';

const fetchObject = FetchHandler.register();

(function (self) {
  importScripts('sw.js');
})(fetchObject);
```

### Implementation

TBD

[Fetch API]: https://fetch.spec.whatwg.org/#fetch-method
[Beacon API]: https://w3c.github.io/beacon/#sendbeacon-method
[XMLHttpRequest API]: https://xhr.spec.whatwg.org/#interface-xmlhttprequest
[Service Worker's fetch handler]: https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerGlobalScope/fetch_event
