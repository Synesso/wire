# Swift Wire Runtime

## Developing

### Developing in Xcode

To develop the runtime or run tests using Xcode, ensure you have the following dependencies installed:

- Xcode
- Ruby
- The [`CocoaPods` Ruby gem](https://guides.cocoapods.org/using/getting-started.html)
- The [`cocoapods-generate` Ruby gem](https://github.com/square/cocoapods-generate)

Then, run the following commands from the root of the repo:

```
pod gen ./Wire.podspec
open gen/Wire/Wire.xcworkspace
```

### Running Tests On the Command Line

To build the runtime and run tests from the command line using Gradle, run the following command from the root of the repo:

```
./gradlew -p wire-library :wire-runtime-swift:build
```

### Publishing the CocoaPods

There are two Podspecs to publish to CocoaPods: the Swift Wire runtime and the Swift Wire compiler. The same version number should be used for both.

CocoaPods are published to the [trunk](https://blog.cocoapods.org/CocoaPods-Trunk/) repo, which is the main public repo for all CocoaPods. If you have not published Wire before then you'll need to [get set up to publish to trunk](https://guides.cocoapods.org/making/getting-setup-with-trunk.html), and be added as a publisher for the Wire Podspecs.

### Setting the Version

When publishing a new version, two things must be done:
1. The version must be tagged in Git. So if you're publishing version `4.0.0-alpha1`, then you'd check out the SHA you want to publish and run:
```
git tag 4.0.0-alpha1
git push origin refs/tags/4.0.0-alpha1
```

2. The version being published needs to be passed into the Podspecs. This is done by setting the `POD_VERSION` environment variable:
```
export POD_VERSION=4.0.0-alpha1
```

If publishing a release version (like `4.0.0` rather than `4.0.0-alpha1`) then setting the `POD_VERSION` is optional and it will be pulled automatically from `wire-library/gradle.properties`.

### Publishing the Podspecs

After setting the version as described above, you can publish the two Podspecs by doing:

```
# Tests are currently failing, thus --skip-tests is required
pod trunk push Wire.podspec --skip-tests
```

and

```
pod trunk push WireCompiler.podspec
```

### Measuring Performance

In order to ensure that Wire is performant and stays performant, there are performance tests included in the test suite. For any major change to the runtime that has the potential to impact Wire's speed of encoding/decoding, run the test suite and include the corresponding numbers as part of the change's commit comment.

To run the performance tests:

- Close as many other programs on your computer as possible.
- Enable the tests in `./wire-library/wire-runtime-swift/src/test/swift/PerformanceTests.swift` by changing `#if false` to `#if true`. The tests are disabled by default so that they don't run on CI.
- Run the tests once without your new changes in order to establish a baseline. Baselines are relative to your computer and are not checked in.
- Use Xcode to actually set these values as the baseline for each test. This can be done by tapping the gray diamond next to the "No baseline set" message for each test, then tapping the gray diamond again to bring up the Baseline popover, then tapping "Set Baseline". This should be done for each test in the file.
- Make your changes.
- Re-run the tests and compare the results. A variance of up to 10% isn't uncommon. If numbers are varying more than that on each run, trying closing more programs, disabling WiFi, or otherwise limiting outside influences.

Include the baseline metrics as well as the updated metrics in the commit message.
