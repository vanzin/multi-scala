# One Project, Multiple Scala Versions

This p.o.c. is an attempt to support multiple Scala versions in the same project without
requiring hacks that pollute the working directory.

The goals are to support both modules that are not tied to any Scala version, and modules that
need to be published for specific Scala versions.

So, in the same project, you could have "java-lib" that publishes a single jar, and
"scala-lib" that publishes "scala-lib_2.11", "scala-lib_2.12" and whatever other Scala
versions are desired.

## How to navigate the project

Everything starts at the root pom. It has a separate profile for each scala-based project.
Each sub-module that is built for a specific Scala version needs to be added to each profile.
That's source #1 of duplication.

Each Scala-based module needs one subdirectory for each Scala version. In the repo, that's
`thelib/2.10` and `thelib/2.11`. Each one has a small pom file that mostly delegates to other
pom files in the build. The "non-default" Scala version needs to be explicitly set in these
pom files for every module; that's source #2 of duplication. These are the modules that are
added to each Scala profile in the root pom.

These pom files should have the module's main pom file as the parent (in the repo,
`thelib/pom.xml`). The module's main pom should inherit from the specially crafted
"Scala root module" (`scala/pom.xml` in the repo).

The Scala root module is where most of the magic happens; that's where the properties that
define the Scala version are used, and plugins are used to add certain default paths to the
build.

## Why all this is needed

Mostly because of how maven inheritance works. Because you can't set two different Scala
versions in the same module, you need the level of indirection where the child module's
pom will set a more specific Scala version than the default. To avoid duplicating too much
code, each Scala module's pom ends up broken into three.

## What about sbt?

No idea how this would interact with sbt builds that parse maven poms (e.g. Spark).
