C++ ABI Interface Tests
=======================

FNAL's canvas library uses the Itanium C++ ABI objects/interfaces to
check upcast-ability of from-to types. In GCC/libstdc++, these work
directly as libstdc++ (strictly the ABI portion?) exposes the types:

- `__cxxabiv1::__class_type_info`
- `__cxxabiv1::__base_class_type_info`
- `__cxxabiv1::__si_class_type_info`
- `__cxxabiv1::__vmi_class_type_info`

See the [Itanium ABI](https://itanium-cxx-abi.github.io/cxx-abi/abi.html)
for more about these classes.

With Clang/libc++ (Apple, maybe BSD), the libc++abi library implements,
but hides the symbols/declarations of, the above types.

Thus canvas's `maybeCastObj` interfaces are not functional with a
Clang/libc++ toolchain. This project is a basic demonstrator of the
issue, and some test cases of possible hacky resolutions. The
baseline is to obtain identical behaviour on GCC/libstdc++ and
Clang/libc++ for canvas' use cases.

The ROOT based implementation is also shown, though the aim is to
remove this dependency.

Building/Testing
================
The following software is needed to build and run the libraries and tests:

- Linux/macOS (only CentOS7/High Sierra tested)
- C++14 or better compatible compiler (GCC 6.4, clang 5, Xcode 9.1 tested)
- CMake 3.9 or higher
- Boost 1.66.0 or higher, compiled with C++14
- Optional: Root 6 or newer, compiled with C++14

The above can be set up on a system with the LArSoft CVMFS as:

``` console
$ . /cvmfs/larsoft.opensciencegrid.org/products/setups
$ setup cmake v3_10_1
$ setup boost -q <quals> v1_66_0a
```

where `<quals>` should be chosen as needed, e.g. `e15:debug` for GCC 6 and
C++14, or `c2:debug` for clang 5 with C++14.

Then to build:

```console
... start from directory holding this README
$ mkdir build
$ cd build
$ cmake ..
...
$ make
```

When using clang through UPS, you need to use `CC` and `CXX` to point
`cmake` to the right place:

``` console
$ CC=clang CXX=clang++ cmake ..
...
```

Assuming the configure and build run sucessfully, the tests can be
run through ctest:

```console
$ ctest
```

or directly, using the Boost.Unit CLI to increase the logging level:

``` console
$ ./maybeObjCastNative_t -l all
... if Root is available
$ ./maybeObjCastNative_t -l all
```

Tests
=====

The [maybeCastObj_t.cc](./maybeCastObj_t.cc) file implements the main
tests of the use of the ABI classes. At present this has added tests on
a "best guess" basis of required functionality (additional uses could
be found in `canvas_root_io` and `art`?).

More test cases are needed, especially failure cases.

NB: Checks for valid upcasts fail with the Root TClass-based implementation,
possibly due (not confirmed) to lacking dictionary.
