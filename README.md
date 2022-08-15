# totw-cmake
Collection of useful cmake tips
[CMake 202](https://docs.google.com/presentation/d/1GdnVETTJV6leoiyW-3E1iXD8R4OcrYY3Dhtjbx20bz4/edit?usp=sharing)

### 08/15/2022 `GenerateExportHeader`
Use the [GenerateExportHeader](https://cmake.org/cmake/help/latest/module/GenerateExportHeader.html) module to generate a header that defines macros for symbol export. GCC and Clang export all symbols by default but if you want better control over what symbols are public in your .so, you need to use non-portable compile attributes to specify that. This CMake module automates a lot of the busy work required to generate those macros and does so in a way that works on all platforms, including Windows.
I’ve used this before and it’s not too hard to get set up with. I’d definitely recommend this if you’re hardcore about not letter users depend on implementation details.

### 08/11/2022 `CMAKE_` vs `PROJECT_`

When two variables have the same name but one has a `CMAKE_` prefix and the other a `PROJECT_` prefix, the difference is that the `CMAKE_` prefixed version refers to the top level project. The `PROJECT_` prefixed version refers to the whatever project is currently being configured. They often mean the same thing but they’re different when you have projects inside projects like through the use of git submodules or using `FetchContent` to bring it outside projects.
For example, `PROJECT_SOURCE_DIR` is the root of the project currently being configured. If you’re writing a library, this is usually what you should be using since you can be certain that it will always refer to the root of your library even if someone takes your project and puts it inside their project. `CMAKE_SOURCE_DIR` refers to the top level CMakeListst.xt file that was passed to `cmake`. When you run `cmake -S. -B build` you’re saying the current directory is the `CMAKE_SOURCE_DIR`. Within that CMakeLists.txt you can add as many outside projects as you like and each outside project will have a different `PROJECT_SOURCE_DIR` but `CMAKE_SOURCE_DIR` will only ever refer to the same directory no matter where it’s used.

### 08/10/2022 `endif` conditional

Do this
```
if(SOME_CONDITION)
   ...
endif()
```
not this
```
if(SOME_CONDITION)
   ...
endif(SOME_CONDITION)
```
Surprisingly the 2nd form is valid and behaves the same as the first. There’s no good reason to be repeating yourself in the endif call. It’s entirely unnecessary and just makes the code harder to read and maintain.

