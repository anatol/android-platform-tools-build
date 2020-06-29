Build script for Android platform tools
==

Android tools like `fastboot` and `adb` are essential system components that help to deal with
Android devices effectively.

Unfortunately Android has a huge and monolithic build system that does not allow to build
these components individually. If one wants to build `fastboot` then the whole Android 
development environment needs to be downloaded. It consists of many gigabytes of source code
and prebuilt binaries. This makes impractical for a Linux ditro manitainer to build
the tools from sources using the standard build system.

This project tries to partially mimic Android build system and provide an easy way to build
the tools using only small subset of the Android source tree.

Using the build scripts
==

To build Android tools at your system please do:
 * Copy `build.ninja` and `deployagent.jar` to you build directory
 * Checkout following Android sources
```
https://android.googlesource.com/platform/frameworks/base
https://android.googlesource.com/platform/frameworks/native
https://android.googlesource.com/platform/system/core
https://android.googlesource.com/platform/system/extras
https://android.googlesource.com/platform/system/tools/mkbootimg
https://android.googlesource.com/platform/external/selinux
https://android.googlesource.com/platform/external/f2fs-tools
https://android.googlesource.com/platform/external/e2fsprogs
https://android.googlesource.com/platform/external/avb
https://android.googlesource.com/platform/external/boringssl
```
for a `platform-tools-$PLATFORM_TOOLS_VERSION` tag (see current version `PLATFORM_TOOLS_VERSION` in [generate_build.rb](generate_build.rb) file).
 * Apply patches from `./patches` directory
 * Build boringssl with `cd boringssl/src/build && cmake -GNinja .. && ninja crypto/libcrypto.a ssl/libssl.a`
 * Build tools with `ninja`
 * There going to be Android binaries ready for use: `fastboot` `adb` `mke2fs.android` `e2fsdroid` `ext2simg` `avb/avbtool`

Rebuilding deplyagent.jar
==

`deployagent.jar` is a binary pre-build from Android sources. Building this java library
requires way too many dependencies: java, protobuf-java, dex compiler, Android base libs.

To avoid the complexity we prebuilt the lib from the Android sources directly
using following instructions:
```
source build/envsetup.sh
lunch full-eng
mmm system/core/adb/
cp ./target/product/generic/system/framework/deployagent.jar .
```

See https://wiki.archlinux.org/index.php/Android for how to setup the dev environment
