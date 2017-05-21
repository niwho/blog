title: py脚本打包 
date: 2014-06-25 10:35:12
updated: 2014-06-25 10:35:27
tags: 
- python
- py2exe
categories: 
- py脚本打包 
---

使用[py2exe](http://http://www.py2exe.org/)，可打包为单个文件，打包后程序启动快，比pyinstaller快太多了。  
    setup 文件示例参考
<!--more-->
    
    from distutils.core import setup
    import py2exe
    #setup(console=['hello.py'])

    options = {"py2exe": 
    {   "compressed":1, 
        "optimize": 2, 
        #"includes": includes, 
        "bundle_files":1,
        "dll_excludes": [ "MSVCP90.dll", "mswsock.dll", "powrprof.dll","w9xpopen.exe"
                  'API-MS-Win-Core-ErrorHandling-L1-1-0.dll',
                  'API-MS-Win-Core-File-L1-1-0.dll',
                  'API-MS-Win-Core-IO-L1-1-0.dll',
                  'API-MS-Win-Core-LibraryLoader-L1-1-0.dll',
                  'API-MS-Win-Core-Handle-L1-1-0.dll',
                  'API-MS-Win-Core-Heap-L1-1-0.dll',
                  'API-MS-Win-Core-Interlocked-L1-1-0.dll',
                  'API-MS-Win-Core-Localization-L1-1-0.dll',
                  'API-MS-Win-Core-Memory-L1-1-0.dll',
                  'API-MS-Win-Core-Misc-L1-1-0.dll',
                  'API-MS-Win-Core-ProcessThreads-L1-1-0.dll',
                  'API-MS-Win-Core-Profile-L1-1-0.dll',
                  'API-MS-Win-Core-String-L1-1-0.dll',
                  'API-MS-Win-Core-Synch-L1-1-0.dll',
                  'API-MS-Win-Core-SysInfo-L1-1-0.dll',
                  'API-MS-Win-Core-ErrorHandling-L1-1-0.dll'
                  ]
    } 
    } 

    setup(version = "1.0.0", 
        description = "网格作业提交", 
        name = "netposaJobCommit", 
        options = options, 
        zipfile = None 
      ,windows=[{'script':'pcc_gui.py'
                ,'icon_resources': [(1, '13_e.ico')]
                #,'dll_excludes': [ "mswsock.dll", "powrprof.dll" ]
                }])

###错误诊断：  
>File "zipextimporter.pyo", line 98, in load_module
>ImportError: MemoryLoadLibrary failed loading PCCUserppyy.pyd

    :::python 
    r"""zipextimporter - an importer which can import extension modules from zipfiles

    This file and also _memimporter.pyd is part of the py2exe package.

    Overview
    ========

    zipextimporter.py contains the ZipExtImporter class which allows to
    load Python binary extension modules contained in a zip.archive,
    without unpacking them to the file system.

    Call the zipextimporter.install() function to install the import hook,
    add a zip-file containing .pyd or .dll extension modules to sys.path,
    and import them.

    It uses the _memimporter extension which uses code from Joachim
    Bauch's MemoryModule library.  This library emulates the win32 api
    function LoadLibrary.

    Sample usage
    ============

    You have to prepare a zip-archive 'lib.zip' containing
    your Python's _socket.pyd for this example to work.

    >>> import zipextimporter
    >>> zipextimporter.install()
    >>> import sys
    >>> sys.path.insert(0, "lib.zip")
    >>> import _socket
    >>> print _socket
    <module '_socket' from 'lib.zip\_socket.pyd'>
    >>> _socket.__file__
    'lib.zip\\_socket.pyd'
    >>> _socket.__loader__
    <ZipExtensionImporter object 'lib.zip'>
    >>> # Reloading also works correctly:
    >>> _socket is reload(_socket)
    True
    >>>

    """
    import imp, sys
    import zipimport
    import _memimporter
    _memimporter.get_verbose_flag = lambda: 3

    class ZipExtensionImporter(zipimport.zipimporter):
        _suffixes = [s[0] for s in imp.get_suffixes() if s[2] == imp.C_EXTENSION]

        def find_module(self, fullname, path=None):
            result = zipimport.zipimporter.find_module(self, fullname, path)
            if result:
                if _memimporter.get_verbose_flag() > 2:
                    sys.stderr.write("find_module: found %s by standard zipimport\n" % (fullname))
                return result
            if fullname in ("pywintypes", "pythoncom"):
                fullname = fullname + "%d%d" % sys.version_info[:2]
                fullname = fullname.replace(".", "\\") + ".dll"
                if fullname in self._files:
                    if _memimporter.get_verbose_flag() > 2:
                        sys.stderr.write("find_module: found %s by py2exe hack\n" % (fullname))
                    return self
            else:
                fullname = fullname.replace(".", "\\")
                for s in self._suffixes:
                    if (fullname + s) in self._files:
                        if _memimporter.get_verbose_flag() > 2:
                            sys.stderr.write("find_module: found %s by py2exe with %s extension\n" % (fullname, s))
                        return self
            if _memimporter.get_verbose_flag() > 2:
                sys.stderr.write("find_module: not found %s by py2exe\n" % (fullname))
            return None

        def locate_dll_image(self, name):
            # A callback function for_memimporter.import_module.  Tries to
            # locate additional dlls.  Returns the image as Python string,
            # or None if not found.
            if name in self._files:
                if _memimporter.get_verbose_flag() > 2:
                    sys.stderr.write("locate_dll_image: %s - yes\n" % (name))
                return self.get_data(name)
            if _memimporter.get_verbose_flag() > 2:
                sys.stderr.write("locate_dll_image: %s - no\n" % (name))
            return None

        def load_module(self, fullname):
            if sys.modules.has_key(fullname):
                mod = sys.modules[fullname]
                if _memimporter.get_verbose_flag():
                    sys.stderr.write("load_module: import %s # previously loaded from zipfile %s\n" % (fullname, self.archive))
                return mod
            _memimporter.set_find_proc(self.locate_dll_image)
            try:
                return zipimport.zipimporter.load_module(self, fullname)
            except zipimport.ZipImportError:
                if _memimporter.get_verbose_flag() > 2:
                    sys.stderr.write("load_module: standard zipimport failed\n")
                pass
            if _memimporter.get_verbose_flag() > 2:
                sys.stderr.write("load_module: %s is not loaded by standard zipimport\n" % (fullname))
            initname = "init" + fullname.split(".")[-1] # name of initfunction
            filename = fullname.replace(".", "\\")
            if filename in ("pywintypes", "pythoncom"):
                filename = filename + "%d%d" % sys.version_info[:2]
                suffixes = ('.dll',)
            else:
                suffixes = self._suffixes
            for s in suffixes:
                path = filename + s
                if path in self._files:
                    if _memimporter.get_verbose_flag():
                        sys.stderr.write("load_module: found %s in zipfile %s\n" % (path, self.archive))
                    code = self.get_data(path)
                    mod = _memimporter.import_module(code, initname, fullname, path)
                    mod.__file__ = "%s\\%s" % (self.archive, path)
                    mod.__loader__ = self
                    if _memimporter.get_verbose_flag():
                        sys.stderr.write("load_module: %s is loaded from zipfile %s\n" % (fullname, mod.__file__))
                    return mod
            raise zipimport.ZipImportError, "can't find module %s" % fullname

        def __repr__(self):
            return "<%s object %r>" % (self.__class__.__name__, self.archive)

    def install():
        "Install the zipextimporter"
        sys.path_hooks.insert(0, ZipExtensionImporter)
        sys.path_importer_cache.clear()

    if __name__ == "__main__":
    ##    import doctest
    ##    doctest.testmod()
        #import testpyd
        print 'aaaaa'
        install()
        import sys
        sys.path.insert(0,r"D:\bi4Al31dN8\workspace\Pyproj\tmp3\gui_exe.exe")
        import testpyd
        import PCCUserppyy
        #import PCCUserppyy
        print 'sssss'
    
###问题所在
>API-MS-Win-Core-*.dll的动态库包含应当去掉

###py2exe打包时，如果py有依赖egg格式的库，会有ImportError: No module named xxx的错误
>
>
