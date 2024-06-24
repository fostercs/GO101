# Why should we use GVM to install GO?
GMV is a smart approach to manage the version of Go that we will be using. This is especially useful when working with multiple projects that run various versions of Go.

If your environment has Go previously installed, you can do a bit of inspection to determine how it was installed.

```
which go

Returns
/opt/homebrew/bin/go

#Uninstall GO managed by homebrew
brew uninstall go
```

# Setting GO Path
```
Go

export GOPATH=$HOME/go
export PATH="$PATH:${GOPATH}/bin:$HOME/bin:/usr/local/bin"
```

Read more about GOPATH [here](https://go.dev/doc/code).

# Install GVM
bash
```
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```
zsh
```
zsh < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```

# Source GVM
   After installation, add GVM to your shell profile (e.g., `.bashrc`, `.zshrc`):
   ```sh
   source ~/.gvm/scripts/gvm
   ```

# List all available versions
List available versions: `gvm listall`

# Install Go with GVM
Installing a specific go version : `gvm install go1.19.3`
List installed versions : `gvm list`
Using specified go version : `gvm use go1.19.3`

# Go: Command not found
Initially, you might see something like this. Post version 1.4 Go is written in go so you need to install something that can understand and compile go natively, often called the go toolchain.

Manually download and install Go 1.14 and set the PATH

```
export GOPATH=$HOME/golang
export GOROOT=/usr/local/opt/go/libexec
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOPATH
export PATH=$PATH:$GOROOT/bin
```

go env to view the configuration.
```
/Users/~/.gvm/scripts/install: go: command not found
```

# Setting up packages
Set up packages for specific go versions:
```
gvm use go1.18.1
gvm pkgset go18
```

Go version is 1.18.1 and is using go18 package directory.
activate: `gvm pkgset use go18`
delete the pkgset `gvm pkgset delete go18`

# Uninstall GVM
Uninstall gvm `gvm implode`