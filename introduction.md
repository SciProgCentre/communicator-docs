# Introduction

Communicator is a minimalistic remote procedure call (RPC) protocol with clear transport and serialization abstractions.

The main entities in Communicator are functional clients and servers.

Functional server is an entity bound to a set of functions implemented by a programming language runtime. It handles
requests to invoke these functions.

Functional client is an entity that implements functions in a programming language by making requests to a functional
server.

Datum exchanged by functional servers and clients can have complex structures because of codecs, which are designed to
be easily supported by a variety of programming languages (lists, dictionaries, JSON nodes, etc.).

One of data kinds supported in Communicator is function reference: this allows one to define and invoke higher-order
functions (HOF).

Communicator can be implemented over multiple transport-level protocols.
