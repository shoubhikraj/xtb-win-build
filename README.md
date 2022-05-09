# xtb-win-build
Aims to provide precompiled binaries for xTB, xtb4stda and stda softwares developed by Grimme group. The softwares are natively compiled with Intel C/C++ and Intel Fortran v2021.4 compilers. You also need meson for xTB4sTDA and sTDA softwares. CMake is also required, it is provided by the Visual Studio or Visual Studio build tools. The built executables are found in the release section.

## Method to build xTB

First [download](https://github.com/grimme-lab/xtb/) or clone xtb repository: `git clone https://github.com/grimme-lab/xtb.git`. Then go into the xtb directory, find the `subprojects` directory, go into it and download or clone the `mctc-lib` and `test-drive` projects (in case you don't have access to git on the compiler command line). So essentially, you need to run `git clone https://github.com/grimme-lab/mctc-lib` and `git clone https://github.com/fortran-lang/test-drive` from git bash or maybe from Cygwin. OR, download from github webpage and extract the archive. Then return to the root xtb directory (i.e. where your source files are.)

1. Only CMake compile with NMake generator works, nothing else works so far
2. (optional) The compiler flags are set in `xtb/cmake/CMakeLists.txt`. You can modify the flags for static/dynamic compile or for various SIMD vectorization options. Leaving it unmodified will also work, but the default flags are for Linux only so they are not recognized, and ignored by `ifort`. If compiler flags are unmodified, dynamic linking with Fortran and C/C++ RTL occurs
3. (optional) For static linking, change from line 56 of `xtb/cmake/CMakeLists.txt` like this (also follow step 6 or compilation will fail):
```
if(CMAKE_Fortran_COMPILER_ID MATCHES "Intel")
  set(dialect "-QaxAVX,CORE-AVX2 -4R8 -traceback -MT")
  set(bounds "-check:bounds")
endif()
```
4. Run :
```
set FC=ifort
set CC=icl
cmake -S. -B./build_intel -G"NMake Makefiles" -DCMAKE_BUILD_TYPE=Release -DBLA_STATIC=ON
```
5. (optional) For static linking find `xtb/build_intel/CMakeCache.txt` and replace all instances of /MD with /MT
6. (optional) For high performance build, also find and change `/O2 to /O3` as following in the same `CMakeCache.txt` file:
```
//Flags used by the Fortran compiler during RELEASE builds.
CMAKE_Fortran_FLAGS_RELEASE:STRING=/O3 /DNDEBUG
```
7. Then run `cd build_intel`, and finally `nmake`

The full build (including test modules) will not finish, but the xTB compilation will finish. This means the `xtb.exe` will be available. Simply copy that to the directory you want, and also copy the parameter files, which have the name `param` at the beginning. The above is a very hacky way of compiling, but unfortunately this is the best I can write for now.

One issue with the xTB binary is that it writes greek letters (UTF-8) which can not be displayed by the Windows console unless its code page is changed. I have made an experimental fix in the source code which solves this problem, but it has only been tested in Windows 10 and might show unusual behaviour in Windows XP, 7 or 8. The modified source code can be found in https://github.com/shoubhikraj/xtb. The experimental executables are also available in the release section.

## Method to build xtb4stda

At present, the modified source code for Windows build of xtb4stda can be found at https://github.com/shoubhikraj/xtb4stda-win. Download or clone the repo and then from the source code folder, run:
```
meson setup build_intel --buildtype release -Dstatic
ninja -C build_intel
```
The xtb4stda executable should be compiled successfully.

## Method to build stda

At present, the modified source code for Windows build of stda can be found at https://github.com/shoubhikraj/stda-win. Download and clone, and run:
```
meson setup build_intel --buildtype release
ninja -C build_intel
```
This should build the stda executable.
