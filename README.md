# LZMA for Windows for Intel and ARM Processors ReadMe

The purpose of this repository is to publish a port of the famous LZMA library
for creating 7-Zip archives and extracting files from them to Windows 10 running
on ARM processors such as the Qualcomm Snapdragon series, such as the one inside
the Asus ASUS NovaGo TP370QL, about which you can learn more at
<https://www.asus.com/us/2-in-1-PCs/ASUS-NovaGo-TP370QL/>.

This code builds on the latest version of the LZMA SDK published on the official
site, at <https://www.7-zip.org/sdk.html>, adding configurations for ARM, ARM64,
and, for good measure, x64.

For completeness, this repository includes the __Win32__ (32 bit Windows on
Intel/AMD) binaries, and the included Visual Studio solution and project files
are upgraded from the original Visual Studio 6 format to the latest Visual
Studio 2017 format, a requirement for building for the ARM and ARM64 platforms.

## Using These Libraries

If you have any edition of Visual Studio 2017 installed on a 64-bit copy of
Microsoft Windows, and you intend to use only the 64-bit Intel libraries, you
may skip the rest of this section, because the required runtime libraries are
already installed.

Otherwise, please keep reading, especially if your target is an ARM processor,
since the Microsoft C runtime for ARM is tucked away, and finding them took
many searches, spread across about a week, to find a proper package. Moreover,
extracting them from that package requires some specialized knowledge about the
internal format of a NuGet package.

To use these libraries on any supported platform, you must acquire and install
the Microsoft« C Runtime Library, version 14.00.24234.1 or later, called
`vcruntime140.dll` on all platforms. Microsoft distributes it as part of the
"Microsoft Visual C++ 2015 Redistributable Update 3 RC" package, available from
<https://www.microsoft.com/en-us/download/details.aspx?id=52685> for Intel and
AMD CPUs, and from <https://dotnet.myget.org/F/dotnet-core/api/v2/package/vc-runtime/2.0.0/>
for ARM processors.

- The __Intel/AMD__ packages come as self-extracting `EXE` files, one each for
__x86__ (32 bits) and __x64__ (64 bits). Though they are packaged separately, the
download page offers the option of fetching both in one pass. If you take that
option, you should elect to dwonload both, then view the packages in your
`Downloads` folder. This suggestion applies to Edge and Chrome for certain, and
is sound advice for other Web browsers. Though technically self-extracting
archives, manually extracting them makes clear that they are intended to be
executed, so that the executable code embedded therein can reassemble the
numerous bits into a handful of program files before it installs them.

- The __ARM__ package comes as a `.nupkg` that can be renamed to `.zip`, and
treated as an ordinary ZIP archive. Both __ARM__ and __ARM64__ (32 and 64 bit)
runtimes come in one package. To get the goods, extract the archive into a new
directory, and use the file explorer to drill down into it. Inside directory
`content\VC\Redist\MSVC\14.14.26405\onecore` are two subdirectories, helpfully
named `arm` and `arm64`. Unlike the Intel/AMD packages, the __ARM__ package is
a straightforward, if opaque, ZIP archive, and the contents of the two leaf
directories need only to be copied into a directory that is in your __PATH__
list, and they are ready for use.

For those who are interested, the directory structure in the NuGet package
reflects the location into which the files are installed into Visual Studio
when you install the ARM compilers and tools.

To get the tools for Microsoft Visual Studio, start the __Visual Studio Installer__,
which should be on the main list of __Programs__ (not APPs), answer the UAC
prompt, and select the __Modify__ button. Once the form draws itself, select the
__Individual Components__ tab, and scroll down almost to the bottom, where you will
eventually find __Visual C++ compilers and libraries for ARM and ARM64__, listed as
two items. Check either or both, and click __Modify__. When the installer is done,
you are ready to go.

The output directories are organized by platform, and are located directly off
the repository root. Reflecting the directory organization laid down by author
Igor Pavlov, the Visual Studio project and solution files are nested two levels
deeper, in `Util\LzmaLib`. As a convenience, the Visual C runtime libraries for
all four supported platforms are organized by platform under directory
`vcruntime140`, and the project build script installs the correct CRT library
into the output directory.

Rather than using a __Post-Build__ step, these file copying operations use a `Copy`
task in an `AfterBuild` target. This choice arose from research that led to the
development of another custom target, `BeforeBuildGenerateSources`, that lists
the resolved values of the major MSBuild macros. Its aoutput appears very close
to the top of the build log, and the need for it arose from some early trouble
shooting on the __zlib__ project, of which this project is one of the three
dependencies that had to be found and built first. Since it earned its keep on
that project, all four of these projects, in addition to two unrelated projects
developed at the same time, incorporate this target.

Though neither target is visible in the Visual Studio property pages, the VS
property page editor preserves them when you edit the project properties in the
IDE, though they won't retain the beautified form that you see here, since it
removes all unnecessary white space.