# jom

jom is a parallel make tool for Windows. It is an nmake clone with
support for parallel builds.

## Building with XD

`build.sh` builds jom against the XD framework, Visual Studio 2010 or newer
included. Run it from Git Bash, in a Visual Studio developer prompt
(`vcvarsall.bat`):

```sh
./build.sh            # debug build, in ../build/jom-debug
./build.sh --release  # release build, in ../build/jom-release
./build.sh --test     # build, then run the tests
```

It looks for XD in `$XD_ROOT`, then in a sibling `XD5` or `XD` folder (any
letter case), and offers to clone it when none is found. `jom.exe` and the
Qt libraries it needs end up in the build folder's `bin`.

## Building with QMake

```bat
qmake
nmake
```

### Running the tests

```bat
nmake check
```

## Building with CMake

CMake builds must use a separate build directory.

```bat
mkdir build
cd build
cmake ..\jom -G "NMake Makefiles" -DCMAKE_PREFIX_PATH=<qt-path> -DCMAKE_INSTALL_PREFIX=<install-path>
nmake
nmake install
```

### Running the tests

Enable tests during configuration:

```bat
cmake ..\jom -G "NMake Makefiles" -DBUILD_TESTING=ON
nmake
ctest -V
```

## Environment variables

Like nmake, jom reads default command line arguments from an environment
variable: `JOMFLAGS`. If `JOMFLAGS` is not set, `MAKEFLAGS` is read.
This is useful to set up separate flags for nmake and jom, e.g.

```bat
set MAKEFLAGS=L
set JOMFLAGS=Lj8
```

## .SYNC dependents

The `.SYNC` directive on the right side of a description block prevents
jom from running all of its dependents in parallel.

```makefile
all: Init Prebuild .SYNC Build .SYNC Postbuild
```

This adds additional dependencies so that `Init` and `Prebuild` are
built before `Build`, and `Build` is built before `Postbuild`.
