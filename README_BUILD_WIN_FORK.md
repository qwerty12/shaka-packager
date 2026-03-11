(You may wish to [look at the official build instructions first](docs/source/build_instructions.md#windows).)

You need Python 3 and, obviously, Git for Windows already installed.

# Building with MSVC & the Windows SDK

... as recommended by the official build instructions.

If you don't already have Visual Studio installed, look at the official build instructions to install the toolchain with Chocolatey, or...

Use this to have a verified-working-for-this portable install: https://gist.github.com/mmozeiko/7f3162ec2988e81e56d5c4e22cde9977  
If you use that, make sure to run its `msvc\setup_x64.bat` in a Command Prompt to add cl.exe etc. to `%PATH%` for that `cmd` session.

Make sure [CMake](https://cmake.org/download/) and [Ninja](https://ninja-build.org/) are in your `%PATH%` - or just install with [scoop](https://scoop.sh/):  
`scoop install cmake ninja`

Inside the recursively-cloned shaka-packager folder:

1. Configure:

    * To avoid execution of `scoop`'s shims run this: `set "PATH=%USERPROFILE%\scoop\apps\cmake\current\bin;%USERPROFILE%\scoop\apps\ninja\current;%PATH%"`

    ```bat
    cmake -DCMAKE_BUILD_TYPE="Release" -DCMAKE_SKIP_INSTALL_ALL_DEPENDENCY="ON" -DBUILD_SHARED_LIBS="OFF" -DFULLY_STATIC="OFF" -DSKIP_INTEGRATION_TESTS="ON" -S . -B build/ -G "Ninja"
    ```

    * If you want an even more optimized build (you need more CPU resources, RAM, disk space and time), append these flags - adapt for your PC as necessary:

    ```bat
    -DCMAKE_INTERPROCEDURAL_OPTIMIZATION="ON" ^
    -DCMAKE_CXX_FLAGS_RELEASE="/O2 /Ob3 /DNDEBUG /arch:AVX2 /favor:INTEL64 /Qpar /GL /Gw /Zc:inline" ^
    -DCMAKE_C_FLAGS_RELEASE="/O2 /Ob3 /DNDEBUG /arch:AVX2 /favor:INTEL64 /Qpar /GL /Gw /Zc:inline" ^
    -DCMAKE_EXE_LINKER_FLAGS_RELEASE="/OPT:REF /OPT:ICF /DEBUG:NONE /CGTHREADS:8" ^
    -DCMAKE_STATIC_LINKER_FLAGS_RELEASE="/LTCG"
    ```

    * If you did not install Ninja - you should, the default non-parallelised `nmake` is far slower - then omit `-G "Ninja"`.

        * [`jom`](https://wiki.qt.io/Jom) can be used instead of the recommended Ninja: `scoop install jom` and specify `-G "NMake Makefiles JOM"` instead.

2. ​Build:

    ```bat
    cmake --build build/ --config Release --parallel --target mpd_generator packager pssh_box_py
    ```

3. ​Install:

    ```bat
    cmake --install build/ --strip --config Release --prefix="install" && move /Y install\bin\packager.exe install\bin\shaka-packager.exe
    ```

    Place install\bin\shaka-packager.exe whereever you wish.
