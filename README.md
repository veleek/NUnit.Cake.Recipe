# NUnit.Cake.Recipe

NUnit.Cake.Recipe is a standard cake recipe used for NUnit projects. It is inspired by Cake.Recipe,
but is somewhat simpler in implementation, since it isn't intended for general use.

## Structure of the `build.cake` File

The following is an example of a simple `build.cake` file for a project which uses the recipe
to create a single package.

```
// Load the recipe
#load nuget:?package=NUnit.Cake.Recipe&version=1.0.0 // Note 1

// Initialize Build Settings
BuildSettings.Initialize(                            // Note 2
	context: Context,
	title: "Sample Application",
	githubRepository: "NUnit.Sample.Application");

// Define the package
BuildSettings.Packages.Add(new NuGetPackage(         // Note 3
	id: "NUnit.Sample.Application",
	source: "nuget/NUnit.Sample.Application.nuspec",
	checks: new PackageCheck[] {
		HasFiles(
			"LICENSE.txt", "README.md", "nunit.png",
			"lib/net8.0/nunit.sample.application.dll") }));

// Run the task selected by user or default task
Build.Run();                                         // Note 4
```

**NOTES:**

1. The `#load` statement loads `NUnit.Cake.Recipe`, specifying the version to use, 
   in this case version 1.0.0. It is recommended that you specify the recipe 
   version to avoid unpleasant surprises when the recipe is updated.

2. `BuildSettings.Initialize` sets the parameters which drive the build process. The example
   specifies only the three required paraameters.

3. Define a single `nuget` package.

4. Run the selected target. This must be the last command in the file.

## Supported Arguments

The recipe supports a number of standard command-line arguments. Additional arguments may be
supported directly by the user's `build.cake` file, but must not conflict with the built-in arguments.

Arguments taking a value may use  `=` or space to separate the name from the value and
may be specified in abbreviated form, down to the minimum length shown in square braces.
Single character abbreviations use a single dash.

#### --target=TARGET              [-t]
The name of the TARGET task to be run, e.g. Test. Default is "Build." For a list
of supported targets, use the Cake `--description` option.

#### --configuration=CONFIG       [-c]
The name of the configuration to build, test and/or package, e.g. Debug.
Defaults to Release.

#### --packageVersion=VERSION     [--pack]
Specifies the full package version, including any pre-release
suffix. This version is used directly instead of the default
version from the script or that calculated by GitVersion.
Note that all other versions (AssemblyVersion, etc.) are
derived from the package version.

NOTE: We can't use "version" since that's an argument to Cake itself.

#### --where=SELECTION            [-w]
Specifies a selction expression used to choose which packages
to build and test, for use in debugging. Consists of one or
more specifications, separated by '|' and '&'. Each specification
is of the form "prop=value", where prop may be either id or type.
Examples:
* --where type=nuget
* --where id=NUnit.Engine.Api
* --where "type=nuget|type=choco"

#### --level=LEVEL                [--lev]
Specifies the level of package testing, which is normally set
automatically for different types of builds like CI, PR, etc.
Used by developers to test packages locally without creating
a PR or publishing the package. Defined levels are
  1. Normal CI tests run every time you build a package
  2. Adds more tests for PRs and Dev builds uploaded to MyGet
  3. Adds even more tests prior to publishing a release

#### --trace=LEVEL                [--tr]
Specifies the default trace level for this run. Values are Off,
Error, Warning, Info or Debug. Default is Off.

#### --nobuild                    [--nob]
Indicates that the Build task should not be run even if other
tasks depend on it. The existing build is used instead.

#### --nopush                     [--nop]
Indicates that no publishing or releasing should be done. If
publish or release targets are run, a message is displayed.

#### --usage                 [--us]
Displays a general help message. No targets are run.
