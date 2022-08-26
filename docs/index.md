# CMakeGuidelines
Collection of useful cmake tips.

### 08/26/2022
Use `CMAKE_INTERPROCEDURAL_OPTIMIZATION` to easily enable link-time optimization (LTO) for a project. On the command line this looks like `-DCMAKE_INTERPROCEDURAL_OPTIMIZATION=ON`. Hardcoding this for your project looks like `set(CMAKE_INTERPROCEDURAL_OPTIMIZATION ON)`. CMake knows whether or not your compiler supports LTO so nothing should fail if your compiler does not support it.
https://cmake.org/cmake/help/latest/variable/CMAKE_INTERPROCEDURAL_OPTIMIZATION.html

### 08/24/2022 `list(APPEND`
Use `list(APPEND` to add elements to a list like so
```
set(MY_VARIABLE element1)
...
list(APPEND MY_VARIABLE element2 element3)
```
over the more verbose alternative
```
set(MY_VARIABLE element1)
...
set(MY_VARIABLE ${MY_VARIABLE} element2 element3)
```
Don’t repeat yourself if you don’t need to!
I’ll also briefly mention `list(PREPEND` if you need to prepend instead of append to a list. `list` has tons of options for manipulating lists that are all better than trying to re-implement it by hand with lots of set calls.

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
