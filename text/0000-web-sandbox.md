---
Status: Draft
Authors:
  - Hyeseong Kim <tim@daangn.com>
Start Date: 2023-01-05
RFC PR:
Related Issues: (leave this empty)
---

# Sandbox Environment for Web Applications

## Summary

Brane Project provides sandbox environment for client-side Web applications, built on top of

1. Cross-origin iframe
2. A DOM implementation for [Web Workers]
3. An unified abstraction for scheduling and fine-grained access control

## Motivation



## Detailed design

Brane Project uses both cross-origin iframe and [Web Workers] to make a sandbox environment.

Even Web Worker alone can isolate the JavaScript context, but it's not a complete sandbox. This is because the worker still has access to IndexedDB, cookie storage, etc from the same origin. To provide a separate origin for the application, a separate subdomain is used and an iframe is used to access it.

But Brane doesn't use the iframe's DOM directly because it can slow down the main thread (even if it runs in a separate process due to [`crossOriginIsolated`](https://developer.mozilla.org/en-US/docs/Web/API/crossOriginIsolated), it turns out that communication with the iframe's worker thread is more efficient than communication with the iframe's main thread as a result of the [benchmark](https://github.com/braneproject/cross-origin-isolated-playground/blob/main/cross-origin-worker)), and also the `sandbox` property frame's origin to `null` so disables browser features that require origin policy.

So the guest application is isolated as a worker launched in the cross-origin iframe.

To run UI applications in Web Workers, a DOM implementation must be provided instead of the browser. And a transfer layer is also necessary to synchronize the interactions with the actual display. Although there are several JavaScript DOM implementations available, they aren't being used, and a custom implementation needs to be developed to work with the transfer layer properly.

The cross-origin iframe runs in `crossOriginIsolated` mode to handle scheduling for DOM requests from the worker. The [`Atomics`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics) API makes it able to implement interfaces that require to be blocking & synchronous.

All of this is should be able to be controlled via shims injected by the provider and policy code exist on the host.

### Implementation

[Working in progress](https://github.com/braneproject/web-sandbox/tree/initial-impl)

## Drawbacks

## Other Approaches

### Isolate JS by embeded JS intepreter

### Isolate JS by ShadowRealm API

## Related Works

- **[TreeHouse](https://www.usenix.org/conference/atc12/technical-sessions/presentation/ingram)** by Lon Ingram and Michael Walfish\
  TreeHouse is a research that has very similar concepts and goals. However, it was from 2012 and had limitations due to browser technology at the time.
- **[WorkerDOM](https://github.com/ampproject/worker-dom)** by AMP Porject\
WorkerDOM is the backend for `<amp-script>` tag hosting third-party content in AMP pages. It actually can run many of SPAs today, but it didn't solve the blocking vs event-driven mismatch problem mentioned in early research like TreeHouse.
- **[Partytown](https://partytown.builder.io/)** by Builder.io\
  Partytown is a library that automatically relocate third-party scripts into a worker. It proves the concept of [sandboxing](https://partytown.builder.io/sandboxing) by implementing blocking communication using `Proxy` and `Atomics`, but [itself is not suitable for MiniApps usecase](https://partytown.builder.io/trade-offs#ui-intensive-third-party-scripts).

## Unresolved Questions

Optional, but suggested for first drafts. What parts of the design are still TBD?

[Web Workers]: https://html.spec.whatwg.org/multipage/#workers
[ShadowRealm]: https://tc39.es/proposal-shadowrealm/
