# Modules

Package managers have been around for a long time. They were first used in operating systems and later used in programming environments. While there are differences between system-level and language-level package managers, there is also significant overlap in how they work.

In a programming context, a package manager makes it easier to work with first-party and third-party libraries by helping us define and download project dependencies and specific versions and/or version ranges so that dependencies can be upgraded in a logical and methodical manner.

In 2018, the Go team introduced Go modules. As of Go 1.14 modules are enabled by default and in terms of community adoption. We will need to consider the common use cases for modules to help us get things done faster and eliminate wasted time on trial and error.

# The GOPATH
Before Go modules, projects had to be created inside the $GOPATH, which is an environmental variable that points to the directory where your Go workspace exists. This location, or workspace, is where Go manages project files, dependencies, and installed binaries.

The GOPATH is assumed to be the following:
```
# Linux

$HOME/go

# Windows

%USERPROFILE%\go
```

When it comes to setting up a development environment and understanding how the compiler manages dependencies for a project, $GOPATH mechanics can be inflexible will introduce additional learning curve for Go beginners.

As of Go v1.13, $GOPATH mechanics are mostly no longer necessary and Go projects can be placed anywhere in the filesystem.

# Initializing Modules
Getting started with Modules go mod init

The name of the module doubles as the import path; This allows internal imports to be resolved inside the module. It’s also how other consumers import the package. Also, it is the URL of the repository hosting the code.

Example initialization for a new project:
```
go mod init github.com/fostercs/learn-go
```

This will create a go.mod file in the project root. It will contain the import path for the project and the Go version information. We can read the configuration back. cat go.mod

```
cat go.mod

# Returns
module github.com/fostercs/learn-go

go 1.19
```

Installing dependencies:
```
# Add dependency
go get github.com/fostercs/learn-go

# Target specific branch
go get github.com/fostercs/learn-go@main

# Target specific version
go get github.com/fostercs/learn-go@v0.0.1

# Target specific commit
go get github.com/fostercs/learn-go@ab912ef
```

# Indirect Comment
```
require github.com/fostercs/learn-go v0.0.1-0.20200301204615-d6ee6871f21d // indirect
```
The // indirect comment indicates that this package is not currently being used in the project. You may also see this comment when a package is an indirect dependency (that is a dependency of another dependency).

# Importing a Go package
We are going to import the Kubernetes client-go package by specifying its import path and using one of its exported methods.

```
// main.go
package main

import (
"https://github.com/kubernetes/client-go"
)

func main() {
client-go.Load()
}
```

# Go Tidy
Running the go mod tidy command in your terminal will update the go.mod file. Go tidy will remove unused dependencies in the project and add missing ones (eg. Any third-party packages that were added but not fetched first.)

Ideally go tidy is run before each new version of the project, and before each commit; This ensures the module file is clean and accurate, especially in the case of a reproducible build.