[![CI](https://img.shields.io/github/workflow/status/grpc/grpc-swift/CI?event=push)](https://github.com/grpc/grpc-swift/actions/workflows/ci.yaml)
[![Latest Version](https://img.shields.io/github/v/release/grpc/grpc-swift?include_prereleases&sort=semver)](https://img.shields.io/github/v/release/grpc/grpc-swift?include_prereleases&sort=semver)
[![sswg:graduated|104x20](https://img.shields.io/badge/sswg-graduated-green.svg)](https://github.com/swift-server/sswg/blob/main/process/incubation.md#graduated-level)

# gRPC Swift

This repository contains a gRPC Swift API and code generator.

It is intended for use with Apple's [SwiftProtobuf][swift-protobuf] support for
Protocol Buffers. Both projects contain code generation plugins for `protoc`,
Google's Protocol Buffer compiler, and both contain libraries of supporting code
that is needed to build and run the generated code.

APIs and generated code is provided for both gRPC clients and servers, and can
be built either with Xcode or the Swift Package Manager. Support is provided for
all four gRPC API styles (Unary, Server Streaming, Client Streaming, and
Bidirectional Streaming) and connections can be made either over secure (TLS) or
insecure channels.

## Versions

gRPC Swift has recently been rewritten on top of [SwiftNIO][swift-nio] as
opposed to the core library provided by the [gRPC project][grpc].

Version | Implementation | Branch                 | `protoc` Plugin         | CocoaPod              | Support
--------|----------------|------------------------|-------------------------|-----------------------|-----------------------------------------
1.x     | SwiftNIO       | [`main`][branch-new]   | `protoc-gen-grpc-swift` | [gRPC-Swift][pod-new] | Actively developed and supported
0.x     | gRPC C library | [`cgrpc`][branch-old]  | `protoc-gen-swiftgrpc`  | [SwiftGRPC][pod-old]  | No longer developed; security fixes only

The remainder of this README refers to the 1.x version of gRPC Swift.

## Experimental `async`/`await` Support

Swift 5.5 introduces new language features for concurrency. One such feature is
[`async`/`await`][SE-0296].

gRPC Swift includes **experimental** support for async/await. While support is
**experimental**, async/await releases of gRPC Swift *do not guarantees a stable
API*. Features and APIs may change between async/await releases.

To ensure the 1.x release series does maintain a stable API, async/await releases
will be branched from stable releases and tagged in the following format:
`X.Y.Z-async-await.N`. Where `X.Y.Z` indicates the tag of the stable release
from which the branch was created and `N` indicates a release number on that
branch.

The `protoc-gen-grpc-swift` options for generating async/await style clients and
servers are described in [`docs/plugin.md`][docs-plugin].

## Supported Platforms

gRPC Swift's platform support is identical to the [platform support of Swift
NIO][swift-nio-platforms].

The earliest supported Swift version for gRPC Swift 1.8.x and newer is 5.4.
For 1.7.x and earlier the oldest supported Swift version is 5.2.

## Getting gRPC Swift

There are two parts to gRPC Swift: the gRPC library and an API code generator.

### Getting the gRPC library

#### Swift Package Manager

The Swift Package Manager is the preferred way to get gRPC Swift. Simply add the
package dependency to your `Package.swift`:

```swift
dependencies: [
  .package(url: "https://github.com/grpc/grpc-swift.git", from: "1.0.0")
]
```

...and depend on `"GRPC"` in the necessary targets:

```swift
.target(
  name: ...,
  dependencies: [.product(name: "GRPC", package: "grpc-swift")]
]
```

##### Xcode

From Xcode 11 it is possible to [add Swift Package dependencies to Xcode
projects][xcode-spm] and link targets to products of those packages; this is the
easiest way to integrate gRPC Swift with an existing `xcodeproj`.

##### Manual Integration

Alternatively, gRPC Swift can be manually integrated into a project:

1. Build an Xcode project: `swift package generate-xcodeproj`,
1. Add the generated project to your own project, and
1. Add a build dependency on `GRPC`.

#### CocoaPods

gRPC Swift is currently available [from CocoaPods][pod-new]. To integrate, add
the following line to your `Podfile`:

```ruby
    pod 'gRPC-Swift', '~> 1.0.0'
```

Then, run `pod install` from command line and use your project's generated
`.xcworkspace` file. You might also need to add `use_frameworks!` to your `Podfile`.

*⚠️ If you have conficting modules as a result of having a transitive
dependency on '[gRPC-Core][grpc-core-pod]' see [grpc/grpc-swift#945][grpc-swift-945].*

### Getting the `protoc` Plugins

Binary releases of `protoc`, the Protocol Buffer Compiler, are available on
[GitHub][protobuf-releases].

To build the plugins, run `make plugins` in the main directory. This uses the
Swift Package Manager to build both of the necessary plugins:
`protoc-gen-swift`, which generates Protocol Buffer support code and
`protoc-gen-grpc-swift`, which generates gRPC interface code.

To install these plugins, just copy the two executables (`protoc-gen-swift` and
`protoc-gen-grpc-swift`) that show up in the main directory into a directory
that is part of your `PATH` environment variable. Alternatively the full path to
the plugins can be specified when using `protoc`.

Alternatively, you can get the latest precompiled version of the plugins by adding
the following line to your `Podfile`:

```ruby
    pod 'gRPC-Swift-Plugins'
```
The plugins are available in the `Pods/gRPC-Swift-Plugins/` folder afterwards.

#### Homebrew

The plugins are available from [homebrew](https://brew.sh) and can be installed with:
```bash
    $ brew install swift-protobuf grpc-swift
```

## Examples

gRPC Swift has a number of tutorials and examples available. They are split
across two directories:

- [`/Sources/Examples`][examples-in-source] contains examples which do not
  require additional dependencies and may be built using the Swift Package
  Manager.
- [`/Examples`][examples-out-of-source] contains examples which rely on
  external dependencies or may not be built by the Swift Package Manager (such
  as an iOS app).

Some of the examples are accompanied by tutorials, including:
- A [quick start guide][docs-quickstart] for creating and running your first
  gRPC service.
- A [basic tutorial][docs-tutorial] covering the creation and implementation of
  a gRPC service using all four call types as well as the code required to setup
  and run a server and make calls to it using a generated client.
- An [interceptors][docs-interceptors-tutorial] tutorial covering how to create
  and use interceptors with gRPC Swift.

## Documentation

The `docs` directory contains documentation, including:

- Options for the `protoc` plugin in [`docs/plugin.md`][docs-plugin]
- How to configure TLS in [`docs/tls.md`][docs-tls]
- How to configure keepalive in [`docs/keepalive.md`][docs-keepalive]
- Support for Apple Platforms and NIO Transport Services in
  [`docs/apple-platforms.md`][docs-apple]

## Security

Please see [SECURITY.md](SECURITY.md).

## License

gRPC Swift is released under the same license as [gRPC][grpc], repeated in
[LICENSE](LICENSE).

## Contributing

Please get involved! See our [guidelines for contributing](CONTRIBUTING.md).

[docs-apple]: ./docs/apple-platforms.md
[docs-plugin]: ./docs/plugin.md
[docs-quickstart]: ./docs/quick-start.md
[docs-tls]: ./docs/tls.md
[docs-keepalive]: ./docs/keepalive.md
[docs-tutorial]: ./docs/basic-tutorial.md
[docs-interceptors-tutorial]: ./docs/interceptors-tutorial.md
[grpc]: https://github.com/grpc/grpc
[grpc-core-pod]: https://cocoapods.org/pods/gRPC-Core
[grpc-swift-945]: https://github.com/grpc/grpc-swift/pull/945
[protobuf-releases]: https://github.com/protocolbuffers/protobuf/releases
[swift-nio-platforms]: https://github.com/apple/swift-nio#supported-platforms
[swift-nio]: https://github.com/apple/swift-nio
[swift-protobuf]: https://github.com/apple/swift-protobuf
[xcode-spm]: https://help.apple.com/xcode/mac/current/#/devb83d64851
[pod-new]: https://cocoapods.org/pods/gRPC-Swift
[pod-old]: https://cocoapods.org/pods/SwiftGRPC
[branch-new]: https://github.com/grpc/grpc-swift/tree/main
[branch-old]: https://github.com/grpc/grpc-swift/tree/cgrpc
[examples-out-of-source]: https://github.com/grpc/grpc-swift/tree/main/Examples
[examples-in-source]: https://github.com/grpc/grpc-swift/tree/main/Sources/Examples
[SE-0296]: https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md
