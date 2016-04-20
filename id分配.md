见链接：
 
http://stackoverflow.com/questions/6517151/how-does-the-mapping-between-android-resources-and-resources-id-work/6646113#6646113
 
转载：
At build time, the aapt tool collects all of the resources you have defined (though separate files or explicit definitions in files) and assigns resource IDs to them.

A resource ID is a 32 bit number of the form: PPTTNNNN. PP is the package the resource is for; TT is the type of the resource; NNNN is the name of the resource in that type. For applications resources, PP is always 0x7f.

The TT and NNNN values are assigned by aapt arbitrarily -- basically for each new type the next available number is assigned and used (starting with 1); likewise for each new name in a type, the next available number is assigned and used (starting with 1).

So if we have these resource files handled by aapt in this order:

layout/main.xml
drawable/icon.xml
layout/listitem.xml
The first type we see is "layout" so that is given TT == 1. The first name under that type is "main" so that is given NNNN == 1. The final resource ID is 0x7f010001.

Next we see "drawable" so that is given TT == 2. The first name for that type is "icon" so that gets NNNN == 1. The final resource ID is 0x7f020001.

Last we see another "layout" which has TT == 1 as before. This has a new name "listitem" so that gets the next value NNNN == 2. The final resource ID is 0x7f010002.

Note that aapt by default makes no attempt to keep these identifiers the same between builds. Each time the resources change, they can all get new identifiers. Each time they are built, a new R.java is created with the current identifiers so your code gets the correct values. Because of this, you must never persistent resource identifiers anywhere where they can be used across different builds of your app.

Once the resources are compiled and identifiers assigned, aapt generates the R.java file for your source code and a binary file called "resources.arsc" that contains all of the resource names, identifiers, and values (for resources that come from separate file, their value is the path to that file in the .apk), in a format that can easily mmapped and parsed on the device at runtime.

You can get a summary of the resources.arsc file in an apk with the command "aapt dump resources <path-to-apk>".

The format of the binary resource table is documented in the header file for the resource data structures here:

https://github.com/android/platform_frameworks_base/blob/master/include/androidfw/ResourceTypes.h

The full implementation for reading the resource table on the device is here:

https://github.com/android/platform_frameworks_base/blob/master/libs/androidfw/ResourceTypes.cpp