![logo]  
# Cider (CI for Dart. Efficient Release)
[![Actions Status](https://github.com/f3ath/cider/workflows/Dart%20CI/badge.svg)](https://github.com/f3ath/cider/actions)
[![Coverage](https://codecov.io/gh/f3ath/cider/branch/master/graph/badge.svg)](https://codecov.io/gh/f3ath/cider)
[![Pub](https://img.shields.io/pub/v/cider.svg)](https://pub.dev/packages/cider)
[![License: MIT](https://img.shields.io/badge/license-MIT-purple.svg)](https://opensource.org/licenses/MIT)
[![pedantic](https://dart-lang.github.io/linter/lints/style-pedantic.svg)](https://medium.com/dartlang/pedantic-dart-1c7d365510de)  
A command-line utility to automate package maintenance. Manipulates the changelog and pubspec.yaml.

This tool assumes that the changelog:
 - is called `CHANGELOG.md`
 - is sitting in the project root folder
 - strictly follows the [Keep a Changelog v1.0.0](https://keepachangelog.com/en/1.0.0/) format
 - uses basic markdown (no HTML and complex formatting supported) 
 
It also assumes that your project follows [Semantic Versioning v2.0.0](https://semver.org/spec/v2.0.0.html).

## Install
```
pub global activate cider
```
## Configure
The config file name is `.cider.yaml`. It should reside in the root folder of the project. This file is optional. 
So far it consists of just a single entry.
```yaml
changelog:
  diff_link_template: 'https://github.com/org/project/compare/%from%...%to%'
```

The `%from%` and `%to%` placeholders will be replaced with the corresponding version tags.
## Updating the changelog
This command will add a new line to the `Unreleased` section of the changelog
```
cider log <type> <description>
```
 - **type** is one of: `added`, `changed`, `deprecated`, `removed`, `fixed`, `security`
 - **description** is a markdown text line

Examples
```
cider log change 'New turbo engine installed'
cider log add 'Support for rocket fuel and kerosene'
cider log fix 'No more wheels falling off'
```

## Setting the project version
```
cider set <version>
```
- **version** must be in the correct format

Version before | Command | Version after
--- | --- | ---
1.2.3 | `cider set 3.2.1`  | 3.2.1
0.2.1 | `cider set 0.0.1-dev`  | 0.0.1-dev

## Bumping the project version
```
cider bump <version>
```
- **version** can be any of: `breaking`, `major`, `minor`, `patch`, `build`

Use `--keep-build` or `-b` to retain the build part of the version.

Use `--print` or `-p` to print the new version.

Version before | Command | Version after
--- | --- | ---
1.2.3 | `cider bump breaking`  | 2.0.0
0.2.1 | `cider bump breaking`  | 0.3.0
0.2.1 | `cider bump major`     | 1.0.0
0.2.1 | `cider bump minor`     | 0.3.0
0.2.1 | `cider bump patch`     | 0.2.2
0.2.1 | `cider bump build`     | 0.2.1+1
0.2.1+42 | `cider bump build`     | 0.2.1+43
0.2.1+foo | `cider bump build`     | 0.2.1+foo.1
0.2.1+42.foo | `cider bump build`     | 0.2.1+43.foo
0.2.1+foo.bar.1.2 | `cider bump build`     | 0.2.1+foo.bar.2.0

The `cider bump build` command is a bit tricky. It either increments the first numeric part of the build (if there is a 
numeric part) setting other numeric parts to zeroes, or appends `.1` to the build (otherwise).

Retaining the build part:

Version before | Command | Version after
--- | --- | ---
1.2.3+42 | `cider bump breaking`       | 2.0.0
0.2.1+42 | `cider bump breaking -b`    | 0.3.0+42
0.2.1+42 | `cider bump patch`          | 0.2.2
0.2.1+42 | `cider bump patch -b`       | 0.2.2+42

## Releasing the unreleased changes
This command takes all changes from the `Unreleased` section on the changelog and creates a new release with the
version from pubspec.yaml

```
cider release
```

Use `--date` to provide the release date (the default is today).

Cider will automatically generate the diff links in the changelog if the diff link template is found in the config.

## Printing the current project version
```
cider version
```

## Printing the list of changes in the given version
```
cider describe <version>
```
- **version** is an existing version from the changelog

[logo]: https://raw.githubusercontent.com/f3ath/cider/master/cider.png