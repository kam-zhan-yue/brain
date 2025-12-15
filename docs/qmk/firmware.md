In order to write my own firmware, I had to clone the `qmk` repository and create a symlink. However, this resulted in a few errors due to keyball only being supported on an old version of the repo and things being outdated.

I had Claude solve this for me:
```
  1. Python 3.14 compatibility - Fixed the ast.Num deprecation issue in /Users/kamzhanyue/personal/qmk/lib/python/qmk/math.py:25-36 by using hasattr() to check for ast.Num existence
  before using it, and prioritizing ast.Constant for modern Python versions.
  2. Missing AVR toolchain - Installed avr-gcc and avr-binutils via Homebrew using the osx-cross/avr tap.
  3. Missing LUFA submodule - Initialized all git submodules with git submodule update --init --recursive --force.
```
