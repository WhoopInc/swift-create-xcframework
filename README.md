# swift-create-xcframework

swift-create-xcframework is a very simple tool designed to wrap `xcodebuild` and the process of creating multiple frameworks for a Swift Package and merging them into a single XCFramework.

## Usage

Inside your Swift Package folder you can just run:

```shell
swift create-xcframework
```

By default swift-create-xcframework will build XCFrameworks for all library products in your Package.swift, or any targets you specify on the command line (this can be for any dependencies you include as well).

Then for every target or product specified, swift-create-xcframework will:

1. Generate an Xcode Project for your package (in `.build/swift-create-xcframework`).
2. Build a `.framework` for each supported platform/SDK.
3. Merge the SDK-specific framework into an XCFramework using `xcodebuild -create-xcframework`.
4. Optionally package it up into a zip file ready for a GitHub release.

## Choosing what to build

Let's use an example `Package.swift`:

```swift
var package = Package(
    name: "example-generator",
    platforms: [
	    .ios(.v12),
    	 .macos(.v10_12)
    ],
    products: [
        .library(
            name: "ExampleGenerator",
            targets: [ "ExampleGenerator" ]),
    ],
    dependencies: [],
    targets: [
		...
	]
)
```

By default swift-create-xcframework will build `ExampleGenerator.xcframework` that supports: macosx, iphoneos, iphonesimulator. Additional `.library` products would be built automatically as well.

### Choosing Platforms

You can narrow down what gets built 
If you omit the platforms specification, we'll build for all platforms that support Swift Binary Frameworks, which at the time of writing is just the Apple SDKs: macosx, iphoneos, iphonesimulator, watchos, watchsimulator, appletvos, appletvsimulator.

**Note:** Because only Apple's platforms are supported at this time, swift-create-xcframework will ignore Linux and other platforms in the Package.swift.

You can specify a subset of the platforms to build using the `--platform` option, for example:

```shell
swift create-xcframework --platform ios --platform macos ...
```

### Choosing Products

Because we wrap `xcodebuild`, you can actually build XCFrameworks from anything that will be mapped to an Xcode project as a Framework target. This includes all of the dependencies your Package has.

To see whats available:

```shell
swift create-xcframework --list-products
```

And then to choose what to build:

```shell
swift create-xcframework Target1 Target2 Target3...
```

By default it builds all top-level library products in your Package.swift.

## Command Line Options

Because of the low-friction to adding command line options with [swift-argument-parser](https://github.com/apple/swift-argument-parser), there are a number of useful command line options available, so `--help` should be your first port of call.

## Packaging for distribution

swift-create-xcframework provides a `--zip` option to automatically zip up your newly created XCFrameworks ready for upload to GitHub as a release artefact, or anywhere you choose.

If the target you are creating an XCFramework happens to be a dependency, swift-create-xcframework will look back into the package graph, locate the version that dependency resolved to, and append the version number to your zip file name. eg: `ArgumentParser-0.0.6.zip`

If the target you are creating is a product from the root package, unfortunately there is no standard way to identify the version number. For those cases you can specify one with `--zip-version`.

## Installation

You can install using mint:

```shell
mint install unsignedapps/swift-create-xcframework@1.0.0
```

Or manually:

```shell
git clone https://github.com/unsignedapps/swift-create-xcframework.git
cd swift-create-xcframework
make install
```

Either should pop the swift-create-xcframework binary into `/usr/local/bin`. And because the `swift` binary is extensible, you can then call it as a subcommand of `swift` itself:

```shell
swift create-xcframework --help
```

## Contributing

Please read the [Contribution Guide](CONTRIBUTING.md) for details on how to contribute to this project.

## License

swift-create-xcframework is available under the MIT license. See the [LICENSE](LICENSE) file for more info.