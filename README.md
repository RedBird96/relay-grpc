# Relay gRPC

This repository provides the compiled gRPC code for connecting and submitting blocks to the bloXroute Labs MEV Relays.

## Getting Started

Included in this repository are some helper files to streamline integration;

**conn.go**

- Opens and maintains connection with relay from provided host and auth token

**utils.go**

- Adds methods for converting between standard types and gRPC types

### Importing the module

Import the module inside of the application you wish to add it:

```golang
import (
  grpc "github.com/bloXroute-Labs/relay-grpc"
)
```

### Opening a connection

Using the provided utility method and passing in a standard bloXroute Auth token:

```golang
blockSubmissionChan, err := grpc.NewConnection("{{host}}", "{{your auth token}}")
```

This returns an `error` if there is an issue establishing a connection and a channel that accepts `*grpc.BlockSubmissionRequest`. You will want to store a reference to this channel somewhere that your builder will have access to during the block submission process.

If you don't want to use the provided connection method you can follow a guide online like [this one](https://grpc.io/docs/languages/go/basics/#client) and manage the connection yourself.

### Submitting blocks

Currently relays accept blocks as either JSON or SSZ encoded bytes of `capella.SubmitBlockRequest` from this module `github.com/attestantio/go-builder-client/api/capella`, which means most builders are already generating this paylaod when submitting blocks today.

The provided utility function `CapellaRequestToProtoRequest` accepts `capella.SubmitBlockRequest` and returns `*grpc.SubmitBlockRequest` which can then be sent to the channel returned by `grpc.NewConnection`.

```golang
body := grpc.CapellaRequestToProtoRequest(capellaBlockSubmission)
blockSubmissionChan<-body

// or

blockSubmissionChan<-grpc.CapellaRequestToProtoRequest(capellaBlockSubmission)
```

## All together

``` golang
import (
  grpc "github.com/bloXroute-Labs/relay-grpc"
)

...

blockSubmissionChan, err := grpc.NewConnection("{{host}}", "{{your auth token}}")
if err != nil {
  log.Fatal(err)
}

...

body := grpc.CapellaRequestToProtoRequest(capellaBlockSubmission)

blockSubmissionChan<-body
```
