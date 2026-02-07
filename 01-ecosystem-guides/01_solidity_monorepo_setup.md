# Solidity Monorepo Setup
_______________________________________________________________________________

## Monorepo Setup

Create a directory for the monorepo
```sh
mkdir solidity-programming-mastery
```
_______________________________________________________________________________

Enter the monorepo directory
```sh
cd solidity-programming-mastery
```

Initialize git
```sh
git init
```
_______________________________________________________________________________

Use mise to check which versions of Foundry are available
```sh
mise ls-remote foundry
```

or simply run this to check what the latest version is:
```sh
mise latest foundry
```
_______________________________________________________________________________

Use this command to set a specific version of Foundry for your project.
```sh
mise use foundry@1.5.1
```

This will create a `mise.toml` file at the root of the monorepo.

mise.toml
```toml
[tools]
foundry = "1.5.1"
```

Note: The reason why I'm doing it this way is because 
I want all of the sub-directories in my monorepo to use the same version 
of Foundry so that it is easier to maintain my repo.

For a standalone project in its own repo, 
the mise.toml file would be at the root of the project.

_______________________________________________________________________________

To confirm that everything is working:
```sh
anvil --version
cast --version
chisel --version
forge --version
```
_______________________________________________________________________________

## New Solidity Project Setup

Create the directory for the Solidity project
```sh
mkdir new-solidity-project
```

Enter the Solidity project directory
```sh
cd new-solidity-project
```

For the rest of this guide, 
please run all commands from the root of the Solidity project directory.
_______________________________________________________________________________

Initialize the directory
```sh
forge init --use-parent-git --empty
```

forge init --use-parent-git --empty

This will create the structure of a Foundry project.

The `--use-parent-git` flag is needed since this project is created 
within a directory that is already a git repo. 

This tells Foundry to create the `.gitmodules` file 
at the root of the `monorepo` where your `.git` directory is, 
rather than at the root of your project directory.

The `--empty` flag will create the project without the files that contain
examples of basic solidity code.

_______________________________________________________________________________

### `.gitmodules` file explained

The `.gitmodules` file is Git's way of tracking git submodules. 

A submodule is essentially a git repository 
nested inside another git repository.

The reason for this file is because when you create a new Solidity project
with the `forge` command, it installs `forge-std` 
in the the `lib` directory of your project directory. 

`forge-std` is also git repo.

Your `solidity-programming-mastery/.gitmodules` file will contain the following:

```gitconfig
[submodule "new-solidity-project/lib/forge-std"]
    path = new-solidity-project/lib/forge-std    # Where it lives in your repo
    url = https://github.com/foundry-rs/forge-std # Where to fetch it from
```
_______________________________________________________________________________

### The benefit of this `.gitmodules` file

1. When you push your code up to GitHub, 
instead of pushing all of the files inside of `lib/forge-std` to GitHub,
only the `.gitmodules` file and a pointer to the specific
the specific `forge-std` commit will be pushed to GitHub.

2. The `.gitmodules` file makes your Solidity project reproducible,
because it can be re-built with the exact version `lib/forge-std` 
that was used when you created your project with the `forge` command.

This commit hash is saved inside the `lib/forge-std` directory 
of your project.

If you want to view this commit hash. 

Make sure that you are in the directory of your Solidity project
```sh
cd new-solidity-project
```

Then navigate to the forge-std directory
```sh
cd lib/forge-std
```

Then run the this command:
```sh
git rev-parse HEAD
```

The commit hash should look like this:
```
1801b0541f4fda118a10798fd3486bb7051c5dd6
```

You can also see the version of `forge-std` from the `package.json` file:

```json
{
  "name": "forge-std",
  "version": "1.14.0",
  "description": "Forge Standard Library is a collection of helpful contracts and libraries for use with Forge and Foundry.",
  "homepage": "https://book.getfoundry.sh/forge/forge-std",
  "bugs": "https://github.com/foundry-rs/forge-std/issues",
  "license": "(Apache-2.0 OR MIT)",
  "author": "Contributors to Forge Standard Library",
  "files": [
    "src/**/*"
  ],
  "repository": {
    "type": "git",
    "url": "https://github.com/foundry-rs/forge-std.git"
  }
}
```

Note: You do not need to have Node.js installed 
in your monorepo or your project.

The package.json is there because `forge-std` is an npm package, 
that can be installed manually in a TypeScript / JavaScript project,
since Solidity does not have a package manager.

Node.js and npm are not needed in this case because `foundry` includes the 
`forge init` command which can install`forge-std` without needing 
the Node.js runtime or npm.

Don't delete anything in `lib/forge-std`

_______________________________________________________________________________

Clear out the contents of the project `README.md` file

```sh
truncate -s 0 README.md
```
_______________________________________________________________________________

### How to update your project dependencies

From the root of your project run this command to update all dependencies
```sh
forge install
```

Note: Do not run this command if you have just cloned a repo.

This is only used to update project depencies or install a dependency
_______________________________________________________________________________

### How to remove a project from your repo

Use `git rm -rf` because this will handle the removal of sub modules 
from the monorepo
```sh
git rm -rf new-solidity-project
```

Then use this to remove the empty directory
```sh
rm -rf new-solidity-project/
```
_______________________________________________________________________________

### Renaming and Moving Solidity project

Because each project contains its own `lib/forge-std` that has to be tracked
with a filepath in `solidity-programming-mastery/.gitmodules`, 
the simplest thing to do is this:

1. Create a new project with the name you want, at the location you want.
2. Then copy the files to the new project.
3. Delete the old project.

_______________________________________________________________________________

### Setup

Clone this repo
```sh
git clone https://github.com/dezlymacauley/solidity-programming-mastery.git
```

Give mise permission to read the `mise.toml` file
```sh
mist trust
```

Install the version of Foundry listed in `mise.toml`
```sh
mise install
```

Initialize and fetch all of the git submodules of every Solidity project
that is inside the monorepo.
```sh
git submodule update --init --recursive
```

Then enter the project directory of the Solidity project you want:
```sh
cd new-solidity-project
```

Build the project
```sh
forge build
```

If you know you won't be working on a project for a while 
and you want to save disk space, you can run this command:

```sh
forge clean
```
_______________________________________________________________________________
