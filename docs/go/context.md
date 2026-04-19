[See documentation here](https://pkg.go.dev/context)o

The Context type carries deadlines, cancellation signals, and other request-scopes values across API boundaries and between processes. Incoming requests to a server should create a `Context`, and outgoing calls to servers should accept a Context. The chain of function calls between them must propagate the Context, optionally replacing it with a derived Context.

A Context may be cancelled to indicate that work done on its behalf should stop. A Context with a deadline is cancelled after the deadline passes. When a Context is cancelled, all Contexts derived from it are also cancelled.

## Cancellation
When you are requesting some service, you can cancel it while it is in progress. Cancellation propagation allows you to send the cancellation request to the next person.
