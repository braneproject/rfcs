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

In the Brane Project, Non-same-origin requests should be checked on the host thread, but cannot be intercepted since they operate on different context. (every miniapps have its own context)

## Detailed design

To solve this problem, we need a way to delegate fetch requests to different contexts. It has a similar purpose to the [Service Worker's fetch handler], but it should be able to customize origin constraints.

A custom fetch handler is basically a [ponyfill](https://github.com/sindresorhus/ponyfill) to servie worker. It has intentionally similar interface to it to get better interopability.

### Interface

```webidl
interface NewFetchObject {
  [NewObject] Promise<Response> fetch(RequestInfo input, optional RequestInit init = {});
}

interface FetchHandlerInit {
  [NewObject] Promise<Response> fetch(FetchEvent event);
}

interface FetchHandlerContainer {
  [NewObject] NewFetchObject register(FetchHandlerInit init = {});
}
```

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

### Implementation

TBD

[Fetch API]: https://fetch.spec.whatwg.org/#fetch-method
[Service Worker's fetch handler]: https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerGlobalScope/fetch_event
