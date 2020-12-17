#  _____           _ _____           
| __  |___ ___ _| |     |___ _____ 
|    -| -_| .'| . | | | | -_|     |
|__|__|___|__,|___|_|_|_|___|_|_|_|

A small userland util to dump processes memory
Useful to dump stuff or verify stuff without gdb or running under gdb
(c) fG! - 2012, 2013 - reverser@put.as - http://reverse.put.as
                                   
This is a small and simple userland util to dump processes memory on the screen or to a binary file.
Useful to dump stuff or verify something without gdb or running under gdb.

A new option as been added as of version 0.3 that will dump the mach-o app or lib that is located
at the given address. This makes process dumping easier (dumped binaries will not work because objective-c
related stuff!), especially in iOS where there is no vmmap utility by default.
With this, you can dump the main binary or any of its loaded libraries. You need to find the start address,
which usually can be done using "info shared" command inside gdb. 
To use this feature, pass the -f option to the util (no need to specify the size in this case).
Usage:
readmem -p XXX -a ADDRESS -o WHATEVERNAMEYOUWANT -f

It is compatible with Mac OS X and iOS. If you want to compile to iOS you need to modify the project target
and add the below information to Xcode configurations. 
They will allow to build command-line executables for iOS.

Don't forget that you need to give procmod permissions to the OS X version and the correct entitlements
to the iOS version.
For a good reference regarding entitlements go to http://246tnt.com/iPhone/.

Nothing fancy to be seen here :-)

Version 0.5 introduces code to locate and dump main binary of a given PID. No need anymore to input start address.

fG!
reverser@put.as

Changes to iPhoneOSPackageTypes.xcspec:

    // Mach-O executable
    {   Type = PackageType;
        Identifier = com.apple.package-type.mach-o-executable;
        Name = "Mach-O Executable";
        Description = "Mach-O executable";
        DefaultBuildSettings = {
            EXECUTABLE_PREFIX = "";
            EXECUTABLE_SUFFIX = "";
            EXECUTABLE_NAME = "$(EXECUTABLE_PREFIX)$(PRODUCT_NAME)$(EXECUTABLE_VARIANT_SUFFIX)$(EXECUTABLE_SUFFIX)";
            EXECUTABLE_PATH = "$(EXECUTABLE_NAME)";
        };
        ProductReference = {
            FileType = compiled.mach-o.executable;
            Name = "$(EXECUTABLE_NAME)";
            IsLaunchable = YES;
        };
    },

Changes to iPhoneOSProductTypes.xcspec:

    // Tool (normal Unix command-line executable)
    {   Type = ProductType;
        Identifier = com.apple.product-type.tool;
        Class = PBXToolProductType;
        Name = "Command-line Tool";
        Description = "Standalone command-line tool";
        IconNamePrefix = "TargetExecutable";
        DefaultTargetName = "Command-line Tool";
        DefaultBuildProperties = {
            FULL_PRODUCT_NAME = "$(EXECUTABLE_NAME)";
            EXECUTABLE_PREFIX = "";
            EXECUTABLE_SUFFIX = "";
            REZ_EXECUTABLE = YES;
            INSTALL_PATH = "/usr/local/bin";
            FRAMEWORK_FLAG_PREFIX = "-framework";
            LIBRARY_FLAG_PREFIX = "-l";
            LIBRARY_FLAG_NOSPACE = YES;
            GCC_DYNAMIC_NO_PIC = NO;
            GCC_SYMBOLS_PRIVATE_EXTERN = YES;
            GCC_INLINES_ARE_PRIVATE_EXTERN = YES;
            STRIP_STYLE = "all";
            CODE_SIGNING_ALLOWED = YES;
        };
        PackageTypes = (
            com.apple.package-type.mach-o-executable   // default
        );
    },

