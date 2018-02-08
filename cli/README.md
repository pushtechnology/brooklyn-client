
# [![**Brooklyn**](https://brooklyn.apache.org/style/img/apache-brooklyn-logo-244px-wide.png)](http://brooklyn.apache.org/)

### Apache Brooklyn Client CLI

A command line client for [Apache Brooklyn](https://brooklyn.apache.org).

## Toolchain

The CLI tool is written in Go and should be obtained and built as a standard Go project. 
You will need the following tools to build it:

- Go (version 1.6.1 or higher), with full cross-compiler support: 
the standard [binary packages](https://golang.org/dl) include this,
or you can use your favorite package managers (e.g. `brew install go --with-cc-all`).

Optional:
- Maven (used by the Brooklyn build process)

- Maven (see note below on the Brooklyn build process)


## Workspace Setup

Go is very particular about the layout of a source tree and the source repository, 
as it relies on this in the naming of packages.  

If you're familiar with Go and just want to develop the `br` tool itself you may simply work in your usual `GOPATH`, 
using `go get github.com/apache/brooklyn-client/cli/br` and adding your own fork as a remote. 
If you know `glide` then you probably know what to do and can ignore these instructions;
`br` is built just like any other Go project. 

If you're new to Go and you want to work on the CLI alongside non-Go components in Apache Brooklyn,
then the common Go setup -- where code lives under the `GOPATH` -- may be tedious to work with.
A good pattern is to have the requisite `GOPATH` entry linking to the `brooklyn-client` project
elsewhere on disk, so you have just one copy in the usual space and there is no need to touch the `GOPATH` thereafter.
This is the recommended default described by the instructions below.

First ensure that a `GOPATH` is set; this is where Go will store its files. 
`~/go` is the default, and `~/.go` is acceptable also. For example:

```bash
export GOPATH=$HOME/go
```

These instructions now assume that you have `brooklyn-client` checked out and are
in the `cli` subdirectory, where this file resides. 
Tell Go to use this checked-out project by linking to it under `GOPATH`:

```bash
rm -rf $GOPATH/src/github.com/apache/brooklyn-client
cd ..
ln -s `pwd` $GOPATH/src/github.com/apache/brooklyn-client
cd cli
```


## Installing Dependencies

The CLI has a small number of dependencies, including the popular `urfave/cli`.
To manage the version of dependencies, the CLI
code currently uses [Glide](https://github.com/Masterminds/glide) to fetch
and maintain these:

```bash
go get github.com/Masterminds/glide
$GOPATH/bin/glide install
```


## Compiling the code with Go for development purposes

Just use the regular Go build commands:

```bash
go build -o target/br ./br
```

The binary is now ready to use in `target/br`. 


## Testing 

The code includes a test script in the [test](test) directory. This deploys a Tomcat server on a location of your choice
and runs a number of tests against it, to verify that the br commands perform as expected.  To use this you must edit
the file "test_app.yaml" to change the location to your own value, and then invoke the test script like the following,
where the username and password need only be supplied if Brooklyn requires them:

```bash
sh test.sh  http://your-brooklyn-host:8081 myuser mypassword
```

Note, the tests are not yet comprehensive, and contributions are welcome.


## Building the code as part of the Brooklyn build process

For consistency with the other sub-projects of the overall [Brooklyn](https://github.com/apache/brooklyn) build, Maven
is used to perform the build when brooklyn-client is built as one of the sub-modules of Brooklyn, cross-compiling the code for a number of platform-architecture combinations.

Invoke the build script via Maven with one of 

  - ```mvn clean install```                                     build for all supported platforms
  - ```mvn -Dtarget=native clean install```                     build for the current platform
  - ```mvn -Dtarget=cross -Dos=OS -Darch=ARCH clean install```  build for platform with operating system OS and architecture ARCH

*NOTE* This does *not* build the code into your usual GOPATH. To allow the project to be checked out along with the 
other Brooklyn submodules and built using Maven, without any special treatment to install it into a separate GOPATH
location, the Maven build makes no assumption about the location of the project root directory. Instead, the Maven
`target` directory is used as the GOPATH, and a soft link is created as `target/src/github.com/apache/brooklyn-cli` to 
the code in the root directory. 

This builds the requested binaries into the `target/` directory, each in its own subdirectory with a name that includes 
the platform/architecture details, e.g. `bin/linux.386/br`.  The build installs a maven artifact to the maven repository,
consisting of a zip file containing all the binaries.  This artifact can be referenced in a POM as

```xml
<groupId>org.apache.brooklyn</groupId>
<artifactId>brooklyn-client-cli</artifactId>
<classifier>bin</classifier>
<type>zip</type>
<version>0.12.0-REAPPT-1.4.4-SNAPSHOT</version>  <!-- BROOKLYN_VERSION -->
```

Most of the work is delegated to the `release/build.sh` script;
it is not normally necessary to use this, but if you need to know more,
try `release/build.sh -h` for more information.


## Usage

See instructions in the included [Runtime README](release/files/README) file.


----
Licensed to the Apache Software Foundation (ASF) under one 
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

 http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY 
KIND, either express or implied.  See the License for the 
specific language governing permissions and limitations
under the License.
