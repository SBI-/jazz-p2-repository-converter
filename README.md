# SDK to p2 repository converter
Command line scripts for converting a collection of java artifacts into a p2 repository.

## Abstract
In order to use the sdk files provided by IBM for development / debugging etc. with eclipse plugins, they have to be bundled as a p2 repository. These scripts provide the required functionality for creating a p2 repository on windows and linux.

To use the converted p2 repository as a maven dependency, which is required for compiling many of our projects, a local maven p2 repository has to be created. This is is explained in this readme.

## Prerequisites
1. Download and install [eclipse](https://www.eclipse.org/downloads/).
2. Download and extract the [IBM RTC SDK ](https://jazz.net/downloads/rational-team-concert). The download for the SDK is usually in the 'All Downloads' tab. Note the extraction location, you will need it later.

## Usage
The provided scripts are simple, they just call the FeaturesAndBundlesPublisher application with the required parameters. You can read more about the equinox publisher features in the [eclipse wiki](https://wiki.eclipse.org/Equinox/p2/Publisher).

**Please note that the destination path must be an absolute path from the root of your file system**

If you get any errors regarding the logging configuration, you can safely ignore them. The equinox launcher will run correctly and log to console.

### Windows
Find out the path to the equinox launcher jar which was installed with eclipse. Browse to the eclipse installation folder and go to the plugins folder. Inside it, there is a file that starts with `org.eclipse.equinox.launcher_`. Note the path to this file.

Run the `converter.ps1` script. The correct parameters are:

```
-launcher "path\to\equinox_launcher" (this is the path you noted above)
-dest "drive:\destination\path" (full path to where you want the p2 repository created)
-sdk "path\to\sdk" (location where you unpacked the sdk)
```

#### Example (paths will differ)
`.\converter.ps1 D:\Software\eclipse\plugins\org.eclipse.equinox.launcher_1.3.0.v20140415-2008.jar D:\workspaces\jazz-p2-repository-converter\p2dest .\sdk`

### Linux
Find out the path to the equinox launcher jar which was installed with eclipse. The easiest way is to use `find` from the eclipse installation directory: `find . -name "org.eclipse.equinox.launcher_*"`.

Run the `converter.sh` script. The parameters are positional:

```
$1: equinox/launcher/path (the path you found when using find above)
$2: /path/to/destination (absolute path where you want the p2 repository created)
$3: /path/to/sdk
```

#### Example
`./converter.sh /home/usr/eclipse/plugins/org.eclipse.equinox.launcher_1.3.200.v20160318-1642.jar /home/usr/workspaces/jazz-p2-repository-converter/p2dest /home/usr/workspaces/jazz-sdk`

## Maven repository
In order to use the p2 repository you created above with maven, you have to add the location of your p2 repository as a local repository to your maven settings. The easiest way to do this for all projects is to add the location to your global maven settings file. In Windows, the default location of this file is in your `C:\Users\username\.m2\settings.xml` directory, and in linux, the default location is in `/home/usr/.m2/settings.xml`

The easiest way of achieve this is to create a profile for your p2 repository and set it as the active profile when compiling jazz related java sources.

#### Example .m2/settings.xml content
```
<settings>
...
    <profiles>
        <profile>
            <id>p2 repositories</id>
            <repositories>
                <repository>
                    <id>ibm sdk p2 repository</id>
                    <url>file:/home/usr/workspaces/jazz/jazz-p2-repository-converter/p2dest</url>
                </repository>
            </repositories>
        </profile>
    </profiles>

    <activeProfiles>
        <activeProfile>p2 repositories</activeProfile>
    </activeProfiles>
...
</settings>

```

Alternatively, you can configure the p2 repository on a per-project basis. In order to do so, you have to add the repository information to every `pom.xml` that needs to have access to it.

#### Example pom.xml content
```
<project>
...
    <repositories>
        <repository>
            <id>rtc-sdk</id>
            <layout>p2</layout>
            <url>file:/home/sbi/Documents/workspaces/jazz/jazz-p2-repository-converter/p2dest</url>
        </repository>
    </repositories>
...
</project>
```

## Contributing
Please use the [Issue Tracker](https://github.com/jazz-community/__________/issues) of this repository to report issues or suggest enhancements.<br>
Pull requests are very welcome.

## Licensing
Copyright (c) Siemens AG. All rights reserved.<br>
Licensed under the [MIT](https://github.com/jazz-community/_______/blob/master/LICENSE) License.
