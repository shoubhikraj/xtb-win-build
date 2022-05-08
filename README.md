# xtb-win-build
Aims to provide precompiled binaries for xTB, xtb4stda and stda softwares developed by Grimme group. The softwares are natively compiled with Intel C/C++ and Intel Fortran v2021.4 compilers.

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
4. Run `cmake -S. -B./build_intel -G"NMake Makefiles" -DCMAKE_BUILD_TYPE=Release -DBLA_STATIC=ON`.
5. (optional) For static linking find `xtb/build_intel/CMakeCache.txt` and replace all instances of /MD with /MT
6. (optional) For high performance build, also find and change `/O2 to /O3` as following in the same `CMakeCache.txt` file:
```
//Flags used by the Fortran compiler during RELEASE builds.
CMAKE_Fortran_FLAGS_RELEASE:STRING=/O3 /DNDEBUG
```
7. Then run `cd build_intel`, and finally `nmake`

The full build (including test modules) will not finish, but the xTB compilation will finish. This means the `xtb.exe` will be available. Simply copy that to the directory you want, and also copy the parameter files, which have the name `param` at the beginning. The above is a very hacky way of compiling, but unfortunately this is the best I can write for now.

## Method to build xtb4stda

