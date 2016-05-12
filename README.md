# MinGW V8 Build
=================

Doc Contributors
-----------------
Caleb Champlin (@cchamplin) <caleb.champlin@gmail.com>


## Environment
* Windows 7 x64
* Mingw: GCC 5.3.0
* Visual Studio 2015 Community
 * gclient will complain during fetch if you don't have visual studio I believe


## Getting v8
Start in mingw shell with depot_tools in path, and python in path set to depot_tools/python276_bin
> DEPOT_TOOLS_WIN_TOOLCHAIN=0 GYP_MSVS_VERSION=2015 fetch v8
> cd v8
> git checkout 5.0-lkgr
> DEPOT_TOOLS_WIN_TOOLCHAIN=0 GYP_MSVS_VERSION=2015 gclient sync


## Patches
* **bits.h.patch** _(src/base/bits.h)_ - undef RotateLeft32,RotateRight32,RotateLeft64,RotateRight64
* **circular-queue-ihl.h.patch** _(src/profiler/circular-queue-inl.h)_ - Undef MemoryBarrier
* **platform-win32.cc.patch** _(src/base/platform/platform-win32.cc)_ - Use mingw localtime_s
* **mingw-generate-makefiles.sh.patch** _(tools/mingw-generate-makefiles.sh)_
 * Update to use appropriate make call (old one was out of date)
 * Fix issue with linking/archiving too many files on windows, always use the right compiler
* **forking.py.patch** _(third_part/python_26/Lib/multiprocessing/forking.py)_ - I had a weird issue where gyp was causing python to lose sys.path and so fork would causes errors around being unable to find gyp, hard coded path for simplicity sake
 * This might just be something weird with my environment
 * You'll need to update this with your v8 location
 * This is the lazy fix, the real fix would require more work on finding out why gyp has issues with mingw


## Apply patches
1. mingw-generate-makefiles.sh.patch
2. bits.h.patch
5. circular-queue-ihl.h.patch
8. platform-win32.cc.patch

## Generating makes
In terminal with python path set to third_party/python_26
> tools/mingw-generate-makefiles.sh

## Building
>GYPFLAGS="-Dv8_use_external_startup_data=0" CFLAGS=" -std=c++11" CXXFLAGS=" -std=c++11"  CFLAGS_host=" -std=c++11" CXXFLAGS_host=" -std=c++11" LDFLAGS="-static -static-libgcc -static-libstdc++" make i18nsupport=off x64.release

