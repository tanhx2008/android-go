android-go [![Go Report Card](https://goreportcard.com/badge/github.com/xlab/android-go)](https://goreportcard.com/report/github.com/xlab/android-go) ![Lines of Code](https://img.shields.io/badge/lines-7K-blue.svg)
==========

The **android-go** project aims to provide a platform (namely an SDK) for
writing native Android apps in Go programming language. All things here were
designed to be less coupled as possible, thus maintaining a great level of
flexibility while keeping things simple and easy to use. The approach and tools
are different from [gomobile](https://github.com/golang/mobile), please
distinguish them apart.

The project was one of [the best Go Newsletter items of 2016](http://golangweekly.com/issues/140). In this final issue of the year, they look back at the most popular Go news and links of 2016. 🌟

## Project structure

### android [![GoDoc](https://godoc.org/github.com/xlab/android-go/android?status.svg)](https://godoc.org/github.com/xlab/android-go/android)

Package **android** provides Go bindings for the Android NDK API. They've been
automatically generated by [c-for-go](https://git.io/c-for-go) using the
official NDK headers from `android-23` platform. Keep in mind that different NDK
platforms may implement different sets of API available, thus some of features
used by this binding may not be available in older versions of platform. But I
tested with the `android-21` toolchain and got no issues. Some files, like
[android/sensors.go] for example, have been written by hand to expose some
features that CGO does not handle well.

This package allows to write code that works directly with NDK API, bypassing
all the CGO bookkeeping and boilerplate bloat in your code. But safety is
strictly advised. There is also a JNI calling mechanism that allows easy interactions with
Java VM and exposes the full potential of Android SDK! See [android/jni_util.go] for example
of toggling Android keyboard and other JNI-based utils.

**Example usages:** [app/queue.go], [example], [example-egl], [nk-android].

[app/queue.go]: https://github.com/xlab/android-go/blob/master/app/queue.go
[android/jni_util.go]: https://github.com/xlab/android-go/blob/master/android/jni_util.go
[android/sensors.go]: https://github.com/xlab/android-go/blob/master/android/sensors.go

### app [![GoDoc](https://godoc.org/github.com/xlab/android-go/app?status.svg)](https://godoc.org/github.com/xlab/android-go/app)

Package **app** implements a NativeActivity glue layer required to properly
handle the startup process and the native activity events. Import this package
into your Go application to make it Android-compatible. Some pieces required
for a proper `main.main` trampoline have been borrowed from gomobile, the
absolute minimum to get this stuff invokable as a native activity. Most of the
code in this package provides wrappers around NativeActivity event callbacks.

**Example usages:** [example], [example-egl], [nk-android].

[example]: https://github.com/xlab/android-go/tree/master/example
[example-egl]: https://github.com/xlab/android-go/tree/master/example-egl

### egl [![GoDoc](https://godoc.org/github.com/xlab/android-go/egl?status.svg)](https://godoc.org/github.com/xlab/android-go/egl)

Package **egl** provides Go bindings for EGL API. They've been automatically
generated by [c-for-go](https://c-for-go.com) using the official NDK headers
from `android-23` platform. All functions have their reference to the offical
Khronos documentation. Some files, like [egl/errors.go] for example, have been
written by hand to expose some features that would make it more idiomatic in the
Go world.

Examples of usage in conjuction with the **android** package: [example-egl], [nk-android].

[egl/errors.go]: https://github.com/xlab/android-go/blob/master/egl/errors.go

### gles [![GoDoc](https://godoc.org/github.com/xlab/android-go/gles?status.svg)](https://godoc.org/github.com/xlab/android-go/gles)

Package **gles** provides Go bindings for the OpenGL ES v1 API. They've been
automatically generated by [c-for-go](https://c-for-go.com) using the
official NDK headers from `android-23` platform. All functions have their
reference to the offical documentation.

Example of usage in conjuction with the **android** package: [example-egl].

### gles2 [![GoDoc](https://godoc.org/github.com/xlab/android-go/gles2?status.svg)](https://godoc.org/github.com/xlab/android-go/gles2)

Package **gles2** provides Go bindings for the OpenGL ES v2 API. They've been
automatically generated by [c-for-go](https://c-for-go.com) using the
official NDK headers from `android-23` platform. All functions have their
reference to the offical documentation.

Example of usage in conjuction with the **android** package: [nk-android].

### gles3 [![GoDoc](https://godoc.org/github.com/xlab/android-go/gles3?status.svg)](https://godoc.org/github.com/xlab/android-go/gles3)

Package **gles3** provides Go bindings for the OpenGL ES v3 API. They've been
automatically generated by [c-for-go](https://c-for-go.com) using the
official NDK headers from `android-23` platform. All functions have their
reference to the offical documentation.

Example of usage in conjuction with the **android** package: [nk-android].

### gles31 [![GoDoc](https://godoc.org/github.com/xlab/android-go/gles31?status.svg)](https://godoc.org/github.com/xlab/android-go/gles31)

Package **gles31** provides Go bindings for the OpenGL ES v3.1 API. They've been
automatically generated by [c-for-go](https://c-for-go.com) using the
official NDK headers from `android-23` platform. All functions have their
reference to the offical documentation. The OpenGL ES computing API is supported.

## Examples

Refer the example links to get more info about them.

There are three examples. The first [example] is a template app showing how
to create and build an Android application using the absolute minimum of code and
boilerplate. It also shows the primitives of an activity and how to handle
activity events, there is no visual part, so be ready to read the lines from the
ADB logcat. If this one works correctly on your device, my congratulations. If
not, please open an issue.

The [example-egl] leverages all three packages together: **android**, **egl**
and of course **gles** (OpenGL ES 1.0) to create an visual app that animates its color based
on the accelerometer values. It also reads input events such as key events and
multitouch motion events (with pressure, if supported by the device), you can
check these events in the ADB logcat. Please see the video of the expected
behaviour:

[![Golang + EGL/GLES App on Android](https://img.youtube.com/vi/H2cafzATUEw/0.jpg)](https://www.youtube.com/watch?v=H2cafzATUEw)

And recently I took [nuklear] package and implemented a few backends in Go,
including two for Android that initialize OpenGL ES 2 or ES 3 context using
**android**, **egl**, **gles2** and **gles3** packages. They also responsible
for handling touch and other input events. So now it is possible to create GUI
apps for Android, see [nk-android] for an example Nuklear GUI app.

[nuklear]: http://github.com/golang-ui/nuklear
[nk-android]: https://github.com/golang-ui/nuklear/tree/master/cmd/nk-android

[![Nuklear GUI App written in Golang runs on Android](https://img.youtube.com/vi/3-MiceegZlM/0.jpg)](https://www.youtube.com/watch?v=3-MiceegZlM)

Android keyboard toggling [video](https://goo.gl/photos/TFkYFKC48Re6RnbUA).

## On existing tools or why not just use Gomobile

**TL;DR** it's a "three wheel bicycle" in terms of hacking flexibility. Conversely, this
project tries to fill the niche by providing a platform that is minimal as
possible and respects the bleeding-edge features such as OpenGL ES 3.1, ES 3.2
and of course Vulkan API.

GoMobile has different aims and motivation with a lot of implications. First of
all, it has been grown as a bootstrap-script. That's it: a tool with a lot of
hardcoded logic that helped to popularize Go on mobile platforms. Still provides a
simple way to start building Android and iOS apps in no time, without diving
deeply into any of the platform-specific complications. It provides a lot of wrappers and
helpers that abstract all the platform-related boilerplate and logic away,
making your apps look the same on any platform: Android, iOS, PC (Linux/OS
X/Windows).

Also, considering that binding feature of GoMobile, it's a good option to use
when you already have lots of Java/Obj-C code and want to integrate some of Go
libs into the project. The tool is mostly for experienced mobile developers who
just want to try Go sometimes. They provide a framework that hides platform
details, but they also may be to restrictive and limited due to this and can't
be used for crazy experimenting stuff.

An example: for Android developent, gomobile defaults to `r10` SDK with `android-15`
stripped-down NDK, there is a lot of code written to automate this process and
there is no way to override this environment, even by rewriting a lot of
gomobile bootstrapping code. It's a great mess. I wasted too much time trying to
switch to the `r12-beta1` SDK and the `android-23` NDK platform. Instead of providing
a good document how to build the Go-based apps and incorporate them into any of
the existing development process, GoMobile project introduces tons of
hardcoded scripts.

## Contributing

Feel free to share bugs, I expect a lot of weird cases. Please also share this
project in social networks so more people would know how to write Go apps for
Android that are not limited.

## License

All the code except when stated otherwise is licensed under the [MIT license](/LICENSE.txt).
