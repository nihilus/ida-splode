# IDA Splode

A tool that I wrote to help reversing on Windows.  Also proof that I am bad at coming up with catchy names.

## Presentation

See the [presentation](https://docs.google.com/viewer?url=https://github.com/zachriggle/ida-splode/raw/master/slides/slides.pdf) in `slides/` for some examples on the sample application.  I've also included an `.idb` that shows some of the features.  All comments are auto-generated by the tool.  The only input I provided was to give the structures a name, and to select various interesting instructions.

## Requirements

- `%PIN_HOME%` points to an installation of Intel's Pin.
- MongoDB
- IDA Pro
- VS 2010

## Usage

- Run `build.bat` from a MSVC 2010 console
- Optionally, enable page heap for `test.exe` (`gflags /i test.exe +hpa`)
- Run `release.bat` to trace the test.exe program in release mode
- Start MongoDB
- Run `demo.exe.py` to import the traces
- Start IDA Pro, open `demo.exe`
- Run `py\idapython_script.py` from within IDA
- If everything worked, `ida-splode` should automatically recognize all traces for the open binary from the database, and present a list of options.
- Press any of the hotkeys presented to do `${things}`.  
    - The slides should give you a good idea what is avaiable.
    - `Ctrl+Shift+H` reprints the help message

## Tips

- If PageHeap isn't enabled (`+hpa`), it will waste a lot of time [looking for heap metadata at instrumentation-time](https://github.com/zachriggle/ida-splode/blob/master/src/symbolic-heap.cpp#L207).
- For whatever reason, if `_NT_SYMBOL_PATH` includes any `SYM*` paths versus just local paths, it won't find PDBs and you'll only get exports.  Use `_NT_SYMBOL_PATH=C:\symbols` or similar.
- There are lots of twiddly bits to turn on and off.  See [`knobs.cpp`](https://github.com/zachriggle/ida-splode/blob/master/src/knobs.cpp).
- This is generally intended to be run off-line.  Pin alone will make execution slow; my instrumentation has not been profiled or optimized for speed.
    - If you're not using a module white-list, [only the main executable will be instrumented](https://github.com/zachriggle/ida-splode/blob/master/src/ida-splode.cpp#L98).  Syntax for the whitelist is `-m foo.exe -m bar.dll` and is [case-insensitive](https://github.com/zachriggle/ida-splode/blob/master/src/named-image-instrumenter.cpp#L20).
    - To speed things up, consider using the `-r` option to limit instrumentation to inside the scope of a [particular routine](https://github.com/zachriggle/ida-splode/blob/945fb53ada37e7ef736c7b9c6fa538ef39a26d15/src/ida-splode.cpp#L158) (so you can skip all the start-up stuff).  For example, `-r demo!TestCustomMalloc`.


## Caveats

This is pulled from a working copy, so some things may not work properly.  If you run into any issues, feel free to contact me at [@ebeip90](https://twitter.com/ebeip90) or ebeip90 on Freenode.net.
