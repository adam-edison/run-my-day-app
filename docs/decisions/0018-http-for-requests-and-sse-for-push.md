# DR-0018: HTTP for Requests, SSE for Push

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Clients send requests to the server, such as outbox entries. The server pushes notices to open clients, such as a change from another device or a takeover of the run.

Requirement: a notice from the server must reach an open client within 2 seconds.

The server uses oRPC. oRPC supports these transports:

* RPC over HTTP
* OpenAPI REST
* RPC over WebSocket
* streaming over HTTP (SSE)

Example: at 10:20 the laptop takes over "Morning." The phone must stop running it by 10:20:02.

## Decision

* Requests use oRPC's RPC protocol over HTTP.
* Push to open clients uses server-sent events, through oRPC streaming.
* After each reconnect, the client fetches every change since its last sync.

Example: the phone's stream drops in a tunnel and reconnects. The fetch after reconnecting brings any change made during the drop.

## Alternatives Considered

WebSocket, rejected:

* It is two-way, and only the server pushes.
* It adds a second kind of connection beside HTTP.
* Some proxies block it.

oRPC over a WebSocket for requests too, rejected:

* Every request would depend on the socket being open.

Polling, rejected:

* A check every 2 seconds sends many requests when nothing has changed.

OpenAPI REST, not used now:

* No client needs it. The same router can serve it later.

## Rationale

* Push only goes from the server to the client. SSE is one-way, from the server to the client.
* Requests and push both use HTTP.
* oRPC documents streaming in Expo on Android and iOS through expo/fetch.
* An outbox entry is a request with a response. HTTP returns the response as the acknowledgment.
* Some proxies cut long-lived connections. The fetch after each reconnect brings the changes made while the stream was down.

## Consequences

* Expo SDK 56 or later is needed for streaming without extra setup.
* `EXPO_PUBLIC_USE_RN_FETCH` must not be set. Setting it turns streaming off.
* When a proxy cuts the stream, the client reconnects and fetches the changes.
* A suspended app receives no push. During a run with the phone locked, the app stays awake only if background audio (iOS) or a foreground service (Android) keeps it running. This is tested in the phone trial from DR-0012.

## Sources

### [RPC - oRPC](https://orpc.dev/docs/rpc)

Retrieved 2026-11-27.

- Fact: oRPC's RPC protocol runs over HTTP and over WebSocket.
  > "RPC is oRPC's default protocol between its own clients and servers. It works over HTTP, WebSocket, and message ports."

### [Adapters - oRPC](https://orpc.dev/docs/adapters)

Retrieved 2026-11-27.

- Fact: oRPC serves RPC and OpenAPI over HTTP, and RPC only over WebSocket.
  > "HTTP adapters serve both RPC and OpenAPI; WebSocket and Message Port adapters serve RPC only."

### [OpenAPI - oRPC](https://orpc.dev/docs/openapi)

Retrieved 2026-11-27.

- Fact: oRPC supports OpenAPI REST, and the same router can serve it later.
  > "The same router can serve the RPC protocol for oRPC clients and a REST API for everyone else."

### [AsyncIteratorObject (SSE) - oRPC](https://orpc.dev/docs/async-iterator-object)

Retrieved 2026-11-27.

- Fact: oRPC supports streaming over HTTP as server-sent events.
  > "AsyncIteratorObject (SSE)"
  > "Stream realtime typesafe data from oRPC procedures with async generators, event validation, resume support, and cleanup on disconnect."

### [Expo Adapter - oRPC](https://orpc.dev/docs/adapters/expo)

Retrieved 2026-11-27.

- Fact: oRPC documents streaming in Expo on Android and iOS through expo/fetch.
  > "expo/fetch is native-backed and streams properly. From SDK 56 it replaces the global fetch on Android and iOS, so there is nothing to wire up."
- Fact: Expo SDK 56 or later is needed for streaming without extra setup.
  > "Use SDK 56 or later to get everything working out of the box. SDK 53 to 55 work too, but you must pass expo/fetch to the link yourself,"
- Fact: Setting `EXPO_PUBLIC_USE_RN_FETCH` turns streaming off.
  > "On SDK 56 and later you can opt back out of expo/fetch by setting EXPO_PUBLIC_USE_RN_FETCH=1. Doing so restores React Native’s XMLHttpRequest polyfill and silently disables streaming, so AsyncIteratorObject and streamed responses stop working."

### [Expo - Expo Documentation (SDK 56)](https://docs.expo.dev/versions/v56.0.0/sdk/expo)

Retrieved 2026-11-27.

- Fact: From SDK 56, expo/fetch is the global fetch on Android and iOS, and `EXPO_PUBLIC_USE_RN_FETCH` turns that off.
  > "On Android and iOS, `expo/fetch` is also installed as the global `fetch`."
  > "set `EXPO_PUBLIC_USE_RN_FETCH=1` in your environment variables."

### [EventSource - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/EventSource)

Retrieved 2026-11-27.

- Fact: SSE push is one-way, which fits push that only goes from the server.
  > "server-sent events are unidirectional; that is, data messages are delivered in one direction, from the server to the client"

### [HTML Standard - Server-sent events](https://html.spec.whatwg.org/multipage/server-sent-events.html)

Retrieved 2026-11-27.

- Fact: SSE pushes data over HTTP.
  > "push data to web pages over HTTP or using dedicated server-push protocols,"
- Fact: Some proxies cut long-lived connections, and the stream reconnects.
  > "Legacy proxy servers are known to, in certain cases, drop HTTP connections after a short timeout."
  > "Clients will reconnect if the connection is closed;"

### [RFC 6455: The WebSocket Protocol](https://www.rfc-editor.org/rfc/rfc6455)

Retrieved 2026-11-27.

- Fact: WebSocket is two-way.
  > "The WebSocket Protocol enables two-way communication between a client running untrusted code in a controlled environment"

### [SockJS Fallback - Spring Framework reference](https://docs.spring.io/spring-framework/reference/web/websocket/fallback.html)

Retrieved 2026-11-27.

- Fact: Some proxies block WebSocket.
  > "Over the public Internet, restrictive proxies outside your control may preclude WebSocket interactions, either because they are not configured to pass on the `Upgrade` header or because they close long-lived connections that appear to be idle."

### [Configuring background execution modes - Apple Developer Documentation](https://developer.apple.com/documentation/xcode/configuring-background-execution-modes)

Retrieved 2026-11-27.

- Fact: An iOS app in the background is suspended unless a background mode such as audio keeps it running.
  > "Typically, an app is in a suspended state when it’s in the background. However, there are a limited number of background execution modes your app can support that enable it to run when in the background, such as playing audio, receiving location updates, or processing scheduled tasks."

### [Foreground services overview - Android Developers](https://developer.android.com/develop/background-work/services/fgs)

Retrieved 2026-11-27.

- Fact: On Android, a foreground service keeps work running while the user is not using the app.
  > "Only use a foreground service when your app needs to perform a task that is noticeable by the user, even when they're not directly interacting with the app."
