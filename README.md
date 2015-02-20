# IDAPython-free
IDAPython-1.0.0 patches and .def file needed to build for IDA 5.0 free and Python 2.7.9

This is proof-of-concept code, to study the feasibility of such a project. IDAPython does not contain any unit tests whatsoever, and the plugin does not (yet) contain any self-tests to keep it from loading in the wrong IDA. With all relevant imports being via ordinal, using the plugin with the wrong IDA is bound to result in a crash.

A pre-built version of the plugin is in misc/idapython-1.0.0_ida5.0_py2.7_win32.zip, which is the standard package produced by the IDAPython-1.0.0 build process. The plugin and its .pdb should go into $(IDADIR)/plugins, and the py files into $(IDADIR)/python. Tested with Python 2.7.9.

## Building the plugin

To build the plugin you'll need the IDAPython 1.0.0 sources, the IDA 5.0 SDK headers, and several tools:
* [idapython-1.0.0.zip](https://code.google.com/p/idapython/downloads/detail?name=idapython-1.0.0.zip&can=2&q=) https://code.google.com/p/idapython/
* [swigwin-1.3.36.zip](http://sourceforge.net/projects/swig/files/swigwin/swigwin-1.3.36/) http://www.swig.org
* [python-2.7.9](https://www.python.org/downloads/release/python-279/) https://www.python.org/
* GNU-compatible diff/patch (for example those in MinGW's [MSYS](http://www.mingw.org/wiki/MSYS))
* Visual C++ 2008 (Express)
* idasdk5.zip 

Follow the instructions in the IDAPython source distro (BUILDING.txt) for setting up the build environment but do not apply ida51.patch since it does not work with the IDA 5.0 headers. Apply ida50.patch to the IDA 5.0 headers which you should have put in
```
swigsdk-versions/5.0/include/
```
according to the build instructions. 

Applying free-1.patch to the IDAPython source requires the -l switch (for ignoring whitespace differences) because the editors I used trashed the tabulation. E.g. if the patches are in the directory that contains idapython-1.0.0/ and swigsdk-versions/:
```   
patch -l -i ../../ida50.patch -d swigsdk-versions/5.0/include
patch -l -i ../free-1.patch -p 1 -d idapython-1.0.0
```
That's for the weird GNU patch from MSYS.

The build script (build.py) links `swigsdk-versions/5.0/libvc.w32/ida.lib`. If you want to build an unfree version of the plugin (with named exports, for testing with commercial versions of IDA) then you need to extract `libvc.w32/ida.lib` from the zip. The build will then make the unfree plugin, and the free plugin will have to be built by repeating the link command with suitable alterations. Otherwise you can simplify things by putting the free implib into `libvc.w32/ida.lib`. E.g.
```
lib /def:IDA50free_VC.def /out:swigsdk-versions/5.0/libvc.w32/ida.lib
```

## TO DO

Elevating the plugin from proof-of-concept status to a reliable tool requires quite a bit of work:
 * code for keeping the free plugin from loading with the wrong ida.wll
 * a comprehensive test suite (.py files) for the plugin functionality
 * catch up on the advances and bug fixes since the release of IDAPython 1.0.0 seven years ago
and optionally:
 * add a decent Python/IDC command prompt to IDA free
 * emulate missing functionality on the SDK/C++ level so that the plugin can deal with more of the code written for recent versions of IDA and IDAPython
 * port/migrate to more recent versions of SWIG and VC++

If someone were to take point on this - setting up a mailing list and getting started on the testing thingy - then I'd lend a hand on the C++/SDK side of things.
