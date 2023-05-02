---
Status: Draft
Author: Hyeseong Kim <tim@daangn.com>
Start Date: 2023-04-03
RFC PR: (leave this empty)
Related Issues: (leave this empty)
---

# Custom DOM Implementation for Sandbox Runtime

## Summary

This proposes a custom DOM implementation for running UI applications in the JavaScript sandbox, and optimized for **key requirements**:

- It's small enough that it's okay to load modules redundantly
- It's fast enough to not delay UI interactions
- Web APIs behavior can be controlled by the host (provider)

## Motivation

The most popular way to build UI applications with JavaScript today is using the browser's DOM API. However, access to the DOM API is protected, so there is no way to access it in most isolated environments like Web Workers, ShadowRealms, web servers or any embedded JS runtime.

To run DOM applications in a isolated runtime, it need to interface the APIs they depend on, so it should synchronize with the host UI thread to integrate real user interactions.

## Detailed Design

TBD

### Implementation

TBD

## Drawbacks

### Implementing DOM is not trivial

TBD

### Lack of compatibility

TBD

### Bloating bundle size

TBD

## Alternatives

### Running applications on the main thread

For example, [Facebook's Playable Ads is trying to prevent remote access from the environtment by injected setup script](https://gist.github.com/cometkim/3ffad656ca486972266eec1613e7383a)

### Using a proxy to interface DOM

TBD

### Using JSDOM fork

TBD

[JSDOM]

Alternatively, there are some other implementations such as [Happy DOM](https://github.com/capricorn86/happy-dom), [linkedom](https://github.com/WebReflection/linkedom), etc. Most of them have same limitations.

### Provide alternative UI runtime

UI applications doesn't necessaraliy use DOM. Instead, ...

## Prior arts

- **[WorkerDOM]** by AMP Porject\
WorkerDOM is the backend for `<amp-script>` tag hosting third-party content in AMP pages. It actually can run many of SPAs today, but it didn't solve the blocking vs event-driven mismatch problem mentioned in early research like [TreeHouse].
- **[Partytown]** by Builder.io\
  Partytown is a library that automatically relocate third-party scripts into a worker. It proves the concept of [sandboxing](https://partytown.builder.io/sandboxing) by implementing blocking communication using [`Proxy`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) and [`Atomics`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics), but [itself is not suitable for the key requirements](https://partytown.builder.io/trade-offs#ui-intensive-third-party-scripts).



[TreeHouse]: https://www.usenix.org/conference/atc12/technical-sessions/presentation/ingram
[Partytown]: (https://partytown.builder.io/)
[JSDOM]: https://github.com/jsdom/jsdom
[WorkerDOM]: (https://github.com/ampproject/worker-dom)
