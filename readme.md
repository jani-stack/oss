![logo](https://github.com/devlooped/devlooped.github.io/blob/main/img/tiny.png) oss template
===

For my new open source projects, this is the basic repository structure and build approach I'm going to use. 

## Goals

1. Trivial to apply and update via [dotnet-file](https://github.com/kzu/dotnet-file) with just two simple `dotnet` commands.
2. Repo instructions should just be: `dotnet restore & dotnet build & dotnet test` right on the repo root.

## Installing

After creating an empty repo (maybe with just a `readme.md`), just run:

```
dotnet file init https://github.com/devlooped/oss/blob/main/.netconfig
```
 
This will fetch the given [dotnetconfig](https://dotnetconfig.org] and
synchronize the configured files and create a [.netconfig](.netconfig) 
in the repo containing all the downloaded entries for future sync.

At this point, you should add a `skip` value to the `.netconfig` file for the entries 
you don't want to keep up-to-date afterwards. The default skips would likely match 
the provided [.netconfig](.netconfig), plus any extra files you want to modify, for 
example:

```gitconfig
[file]
	url = https://github.com/devlooped/oss

# don't sync the .netconfig itself, to avoid a loop
[file ".netconfig"]
	url = https://github.com/devlooped/oss/blob/main/.netconfig
	skip

# readme is always customized for the project
[file "readme.md"]
	url = https://github.com/devlooped/oss/blob/main/readme.md
	skip

# we'll be tweaking the build, say
[file ".github/workflows/build.yml"]
	url = https://github.com/devlooped/oss/blob/main/.github/workflows/build.yml
	skip
 ```

> NOTE: you can also download the raw [.netconfig](.netconfig) from this repository 
> and run `dotnet file update` instead. It already contains skips for the readme.

## Updating

From this point on, applying template changes is as easy as running:

```
dotnet file update
```

You can also just list detected changes with:

```
dotnet file changes
```

Automation is provided via the [dotnet-file.yml](.github/workflows/dotnet-file.yml) 
workflow, which runs daily and does a `dotnet file sync` and creates a PR in your 
repository as needed, with a populated changelog to inspect the incoming changes.


## Design Choices

In no particular order:

1. `src` folder contains `Directory.Build.props` and `Directory.Build.targets` 
   and those contain all the customizations for the build, packaging and versioning. 
   In the past I went crazy factoring the targets into multiple files with single 
   purpose groupings and it bcomes [quite hard to follow](https://github.com/devlooped/moq/tree/a76c3cea6/src/build) 
   even for me, having written it all. So it's better to Keep Things Simple™.
   Logically related properties and items have a `Label` attribute as documentation.
   You can customize both by adding a `Directory.props` or `Directory.targets`, 
   which are imported at the end of both files.

2. If a `src/Directory.Packages.props` is found, I turn on 
   [centrally managed package versions](https://github.com/NuGet/Home/wiki/Centrally-managing-NuGet-package-versions), but it's not required.
    
3. GitHub Actions are provided for the CI/CD process as follows:
   - [Build](.github/workflows/build.yml): regular branch builds and PRs build. By default, build and test 
     jobs will run on `ubuntu-latest`. To customize this, create a `./.github/workflows/os-matrix.json` file in the 
     repository, with the matrix to use for the build, such as `["windows-latest", "ubuntu-latest", "macOS-latest"]`. 
   - [Changelog](.github/workflows/changelog.yml): when a release is released (not created, but actually released), 
     a changelog is calculated and pushed to main. The [changelog.config](.github/workflows/changelog.config) file 
     defines [changelog generation options](https://github.com/github-changelog-generator/github-changelog-generator/wiki/Advanced-change-log-generation-examples).
   - [Release notes](.github/workflows/release-notes.yml): when a release (either draft or final) is published, 
     the notes are generated using the same configuration above.
   - [Includes](.github/workflows/includes.yml): allows using HTML includes in markdown files for 
     easier content reuse. Readmes should include the 
     [standard footer](https://github.com/devlooped/sponsors/raw/main/footer.md) with:

     ```
     <!-- include https://github.com/devlooped/sponsors/raw/main/footer.md -->
# Sponsors 

<!-- sponsors.md -->
[![Clarius Org](https://avatars.githubusercontent.com/u/71888636?v=4&s=39 "Clarius Org")](https://github.com/clarius)
[![MFB Technologies, Inc.](https://avatars.githubusercontent.com/u/87181630?v=4&s=39 "MFB Technologies, Inc.")](https://github.com/MFB-Technologies-Inc)
[![SandRock](https://avatars.githubusercontent.com/u/321868?u=99e50a714276c43ae820632f1da88cb71632ec97&v=4&s=39 "SandRock")](https://github.com/sandrock)
[![DRIVE.NET, Inc.](https://avatars.githubusercontent.com/u/15047123?v=4&s=39 "DRIVE.NET, Inc.")](https://github.com/drivenet)
[![Keith Pickford](https://avatars.githubusercontent.com/u/16598898?u=64416b80caf7092a885f60bb31612270bffc9598&v=4&s=39 "Keith Pickford")](https://github.com/Keflon)
[![Thomas Bolon](https://avatars.githubusercontent.com/u/127185?u=7f50babfc888675e37feb80851a4e9708f573386&v=4&s=39 "Thomas Bolon")](https://github.com/tbolon)
[![Kori Francis](https://avatars.githubusercontent.com/u/67574?u=3991fb983e1c399edf39aebc00a9f9cd425703bd&v=4&s=39 "Kori Francis")](https://github.com/kfrancis)
[![Reuben Swartz](https://avatars.githubusercontent.com/u/724704?u=2076fe336f9f6ad678009f1595cbea434b0c5a41&v=4&s=39 "Reuben Swartz")](https://github.com/rbnswartz)
[![Jacob Foshee](https://avatars.githubusercontent.com/u/480334?v=4&s=39 "Jacob Foshee")](https://github.com/jfoshee)
[![](https://avatars.githubusercontent.com/u/33566379?u=bf62e2b46435a267fa246a64537870fd2449410f&v=4&s=39 "")](https://github.com/Mrxx99)
[![Eric Johnson](https://avatars.githubusercontent.com/u/26369281?u=41b560c2bc493149b32d384b960e0948c78767ab&v=4&s=39 "Eric Johnson")](https://github.com/eajhnsn1)
[![Jonathan ](https://avatars.githubusercontent.com/u/5510103?u=98dcfbef3f32de629d30f1f418a095bf09e14891&v=4&s=39 "Jonathan ")](https://github.com/Jonathan-Hickey)
[![Ken Bonny](https://avatars.githubusercontent.com/u/6417376?u=569af445b6f387917029ffb5129e9cf9f6f68421&v=4&s=39 "Ken Bonny")](https://github.com/KenBonny)
[![Simon Cropp](https://avatars.githubusercontent.com/u/122666?v=4&s=39 "Simon Cropp")](https://github.com/SimonCropp)
[![agileworks-eu](https://avatars.githubusercontent.com/u/5989304?v=4&s=39 "agileworks-eu")](https://github.com/agileworks-eu)
[![Zheyu Shen](https://avatars.githubusercontent.com/u/4067473?v=4&s=39 "Zheyu Shen")](https://github.com/arsdragonfly)
[![Vezel](https://avatars.githubusercontent.com/u/87844133?v=4&s=39 "Vezel")](https://github.com/vezel-dev)
[![ChilliCream](https://avatars.githubusercontent.com/u/16239022?v=4&s=39 "ChilliCream")](https://github.com/ChilliCream)
[![4OTC](https://avatars.githubusercontent.com/u/68428092?v=4&s=39 "4OTC")](https://github.com/4OTC)
[![domischell](https://avatars.githubusercontent.com/u/66068846?u=0a5c5e2e7d90f15ea657bc660f175605935c5bea&v=4&s=39 "domischell")](https://github.com/DominicSchell)
[![Adrian Alonso](https://avatars.githubusercontent.com/u/2027083?u=129cf516d99f5cb2fd0f4a0787a069f3446b7522&v=4&s=39 "Adrian Alonso")](https://github.com/adalon)
[![torutek](https://avatars.githubusercontent.com/u/33917059?v=4&s=39 "torutek")](https://github.com/torutek)
[![Ryan McCaffery](https://avatars.githubusercontent.com/u/16667079?u=c0daa64bb5c1b572130e05ae2b6f609ecc912d4d&v=4&s=39 "Ryan McCaffery")](https://github.com/mccaffers)
[![Seika Logiciel](https://avatars.githubusercontent.com/u/2564602?v=4&s=39 "Seika Logiciel")](https://github.com/SeikaLogiciel)
[![Andrew Grant](https://avatars.githubusercontent.com/devlooped-user?s=39 "Andrew Grant")](https://github.com/wizardness)
[![eska-gmbh](https://avatars.githubusercontent.com/devlooped-team?s=39 "eska-gmbh")](https://github.com/eska-gmbh)
[![Geodata AS](https://avatars.githubusercontent.com/u/5946299?v=4&s=39 "Geodata AS")](https://github.com/geodata-no)


<!-- sponsors.md -->
[![Sponsor this project](https://avatars.githubusercontent.com/devlooped-sponsor?s=118 "Sponsor this project")](https://github.com/sponsors/devlooped)

[Learn more about GitHub Sponsors](https://github.com/sponsors)

<!-- https://github.com/devlooped/sponsors/raw/main/footer.md -->
     ```

4. `dotnet format` is enforced on builds to keep consistency with `.editorconfig`.

5. [dependabot](.github/dependabot.yml) is configured to check for updated nuget packages daily.

6. A default [strong-name key](src/kzu.snk) is provided by default too. If the project does not desire to 
   strong-name the assemblies, it can be skipped as well in the `.netconfig` file. If present, the mentioned 
   `Directory.Build.*` targets will automatically pick the file and strong name assemblies.

7. [Bug](.github/ISSUE_TEMPLATE/bug.md) template provided. No addiitonal config provided since the 
   discussions URLs cannot be relative :(.
