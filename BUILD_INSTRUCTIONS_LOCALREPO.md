# Build Instructions for Local Repository #

This document provides instructions for building the project from a local branch / copy of the source code.

The instructions are intended for developers or system administrators who want to:

* Build the most recent version of the project from source code.
* Create a NuGet package (.nupkg) from the built files, which can be used to install the PowerShell module.
* Publish the NuGet package to a local NuGet repository - without requiring code signing.
* Install the PowerShell module from the local NuGet repository in PowerShell 5.1 (default on Windows 10/11)

## Pre-requisites ##

* Visual Studio 2022 or later (current build was performed on version 17.13.6)
* NuGet package manager executable (nuget.exe) - https://www.nuget.org/downloads
  * Install the NuGet executable to a directory in your PATH environment variable, or specify the full path to the executable in the commands below.

## Step-by-step Instructions ##

1. Clone the repository to your local machine.
1. Open the solution file `VSSetup.sln` in Visual Studio.
1. Update the NuGet package references for each project in the solution:
   1. Right-click on the solution in Solution Explorer and select "Manage NuGet Packages for Solution..."
   1. In the NuGet Package Manager, select the "Updates" tab.
   1. Update all packages to their latest versions.
1. If you have issues with building the project after this, you may need to explicitly restore the NuGet packages:
   1. Right-click on the solution in Solution Explorer and select "Restore NuGet Packages".
1. If building still doesn't work, you can try explicitly installing the listed packages for each project into that specific project using the NuGet package manager in Visual Studio.
1. Open a Visual Studio Powershell prompt using `Tools / Command Line / Developer PowerShell` in Visual Studio.
1. From the PowerShell prompt, issue the following command to build the project, using the Release configuration and public release version (no commit hash in the version number):
   ```powershell
   MSBuild.exe '/p:Configuration=Release;PublicRelease=true'
   ```
1. Update the version number in the `src\VSSetup.PowerShell\VSSetup.PowerShell.nuspec` file to match the version number found in the built files. The version number must be specified in the `<version>` element of the `.nuspec` file.
   1. The version number should be in the format `major.minor.patch`, e.g., `2.2.45`.
1. Create a NuGet package from the built files and the `.nuspec` file above:
   ```powershell
   # Set the environment variable to enable legacy CSPROJ pack
   $env:NUGET_ENABLE_LEGACY_CSPROJ_PACK = 'true'
   # Change to the project directory containing the .nuspec file
   cd .\src\VSSetup.PowerShell
   # Create the NuGet package
   nuget pack .\VSSetup.PowerShell.nuspec -Properties 'Configuration=Release;PublicRelease=true'
   ```
1. The NuGet package will be created in the same directory as the `.nuspec` file, with a filename in the format `VSSetup.PowerShell.<version>.nupkg`.
1. Copy the NuGet package to your local PowerShell repository.
