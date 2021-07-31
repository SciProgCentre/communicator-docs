# Codecs

This document describes the codec concept of the Communicator protocol.

Since all data transfer in the Communicator protocol is binary, there should be a set of rules of conversions between
types in programming languages and binary messages passed by Communicator. They're called codecs.

In Communicator libraries, codec can be represented as object that has two methods: encode for creating binary message
from language type and decode for making instance of language's type from given binary message.

Each codec has its identity. Codec identity is UTF-8 encoded string that is transferred during function call to ensure
types of transferred messages. All codec identities must be in the following language:

```
codec_identity ::= plain_identity | compound_identity
compound_identity ::= plain_identity '<' (codec_identity ',' )* codec_identity '>'
```

where `plain_identity` is any char sequence not containing `<`, `>`, and `,`.

The difference between plain identities and compound ones is that the latter ones can define their structure according
to the codecs they are parametrized by. Defining custom generic codecs is discouraged because of possible complexity of
the resulting data structure, which makes it difficult to maintain its proper encoding on different platforms.

There can't be a codec with structure corresponding to an infinite input binary message.

There is an optional convention for codecs to have a namespace inside its identities: `namespace/structure`.

All the Communicator libraries must provide builtin codec implementations for structures listed in further sections.

Tables in codec specification mean structure layout. The columns from left to right correspond to immediately following
fragments of binary message without any delimiters. The first row has the names of the parts, and the second row has
either name of another codec or explanation what bytes stored in the fragment. A shorter structure designation is
"tuple" notation like (`T1`, `T2` ..., `Tn`), which means the same but without names of structure items.

## Plain Codecs

### `common/unit`

Corresponds to empty structure. The length of encoded and decoded payloads is always zero bytes.

### `common/i32`

Corresponds to 32-bit Big Endian signed integer. The length is always 4 bytes.

### `common/i64`

Corresponds to 64-bit Big Endian signed integer. The length is always 8 bytes.

### `common/u64`

Corresponds to 64-bit Big Endian unsigned integer. The length is always 8 bytes.

### `common/f32`

Corresponds to [IEEE 754](https://ieeexplore.ieee.org/document/4610935) single-precision floating point number. The
length is always 4 bytes.

### `common/f64`

Corresponds to [IEEE 754](https://ieeexplore.ieee.org/document/4610935) double-precision floating point number. The
length is always 8 bytes.

### `common/utf8`

| `n`                        | `content`                               |
| -------------------------- | --------------------------------------- |
| [`common/i32`](#commoni32) | `n` bytes corresponding to UTF-8 string |

### `common/json`

| `content`                    |
| ---------------------------- |
| [`common/utf8`](#commonutf8) | 

Implementations of the identity must fail, if the `content` string doesn't contain a
valid [JSON](https://datatracker.ietf.org/doc/html/rfc7159) instance.

### `common/cbor`

| `n`                        | `content`                                    |
| -------------------------- | -------------------------------------------- |
| [`common/i32`](#commoni32) | `n` bytes corresponding to CBOR encoded data |

Implementations of the identity must fail, if the `content` string doesn't contain
valid [CBOR](https://datatracker.ietf.org/doc/html/rfc8949) instance.

## Compound Codecs

### `common/list<T>`

| `n`                        | `e_1`..`e_n`                              |
| -------------------------- | ----------------------------------------- |
| [`common/i32`](#commoni32) | `n ` instances of `T` without delimiters  |

### `common/map<K,V>`

| `n`                        | `e_1`..`e_n`                                   |
| -------------------------- | ---------------------------------------------- |
| [`common/i32`](#commoni32) | `n` instances of (`K`, `V`) without delimiters |

Corresponds to key-value map with `K` keys and `V` values.

### `common/function<T,R>`

| `protocol`                   | `host`                       | `port`                     | `name`                       |
| :--------------------------: | :--------------------------: | :------------------------: | :--------------------------: |
| [`common/utf8`](#commonutf8) | [`common/utf8`](#commonutf8) | [`common/i32`](#commoni32) | [`common/utf8`](#commonutf8) |

Corresponds to the Communicator function reference from `T` to `R` named `name` that can be called the `protocol` and
address with the `host` and the `port`.
