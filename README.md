# Vitagprof

A PS Vita profiling library built on vitasdk and gprof.

It is now time for optimizations !

## Usage

Note : use it at your own risk

A test program using cmake with added profiling is provided inside the test/ folder.
You can build the vitagprof lib + the test program with the following :
```
mkdir build
cd build
cmake ..
make
make install
make test_profiler.vpk-vpk
```

`make install` will install vitagprof.h and libvitagprof.a into vitasdk.
`make test_profiler.vpk-vpk` will build a test app.

Important points :
- You must compile using the `-pg` flag in order to activate functions instrumentations (both with compiler CC and linker LD)
- Using `-pg` implies `-lc_p` (the profiled standard library) which does not exists for the vita. Simple solution, create a symlink :
`ln -s $VITASDK/arm-vita-eabi/lib/libc.a $VITASDK/arm-vita-eabi/lib/libc_p.a` (we do not need to profile the standard library)
- You must create the fself without ASLR (Address Space Layout Randomization), exemple `vita-make-fself -na`
- You must link with libvitagprof.a by adding for example `-lvitagprof` to your LDFLAGS. SceLibKernel_stub is also needed.
- Your program must call `gprof_start()` and then call `gprof_stop("ux0:/data/gmon.out", 1);` (add `#include <vitagprof.h>`). You can choose the destination file. Only calls done between `gprof_start()` and `gprofstop()` will be effectively recorded.

When you app is built you can run it on the Vita and then properly exit the app.
The gmon.out file will be created at the specified destination.
Copy this file on your PC.

You can then use gprof from vitasdk to get performance results (you must provide the elf/self and the gmon.out files) :
```
$ arm-vita-eabi-gprof ./test_profiler ./gmon.out

Flat profile:

Each sample counts as 0.001 seconds.
  %   cumulative   self              self     total           
 time   seconds   seconds    calls   s/call   s/call  name    
 99.99      8.31     8.31       54     0.15     0.15  compute
  0.01      8.31     0.00        1     0.00     8.31  main

...
```

## License

Licensed under the BSD license.
See LICENSE file.

## Acknowledgement

- All the minds behind Vitasdk and the Vita hacking scene
- urchin and all the crazy minds behind PSPSDK (which this code is mostly inspired of)
