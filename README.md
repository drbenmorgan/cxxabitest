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

A ROOT based implementation is also shown, though the aim is to
remove this dependency.

