# Wendy

## Bob's SO and the reference CLI.

[![License: GPL v3](https://img.shields.io/badge/license-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
[![CircleCI](https://circleci.com/gh/bob-cd/wendy/tree/master.svg?style=svg)](https://circleci.com/gh/bob-cd/wendy/tree/master)
[![Dependencies Status](https://versions.deps.co/bob-cd/wendy/status.svg)](https://versions.deps.co/bob-cd/wendy)

[![Built with Spacemacs](https://cdn.rawgit.com/syl20bnr/spacemacs/442d025779da2f62fc86c2082703697714db6514/assets/spacemacs-badge.svg)](http://spacemacs.org)

She is an `opinionated client` for Bob and takes care of the following tasks:

- Implement a full CLI to all of Bob's REST API with corresponding and semantic commands
- Read a `build.toml` file and translate to REST API calls to Bob
- Plan a build:
    - Bob doesnt know of the order of execution of pipelines
    - Wendy reads the dependencies of a pipeline on other pipelines and schedules them accordingly
    - Trigger all of the upstream pipelines of a pipeline such that Bob can proceed with the builds

Wendy is a reference client for Bob implementing a build with certain opinions but others are
encouraged to build their own CLIs using Bob as an engine in ways they see fit.

**This is a work in progress in conjunction with Bob and will be evolving.**

## Installation

You can run the bootstrap-script to generate a wendy binary. Then you can add it to your PATH if you want to.

### Dependencies
bash, curl, tar, jq

### Premises
- Running a Bob instance on localhost:7777
- Running an artifact store on localhost:8001
- Running an external resource on localhost:8000

### Manual:
- Start Docker daemon e.g. with `systemctl start docker`
- Start Bob's database e.g. with `docker run --detach --name bob-db -p 5432:5432 -e POSTGRES_DB=bob -e POSTGRES_USER=bob postgres:alpine`
- Start Bob's artifact store e.g. with `docker run --detach --name bob-artifact -p 8001:8001 bobcd/artifact-local`
- Start Bob's external resource e.g. with `docker run --detach --name bob-resource -p 8000:8000 bobcd/resource-git`
- Start Bob e.g. with `docker run --detach --privileged --name bob -p 7777:7777 bobcd/bob`
- Run `bootstrap_wendy.sh`

## Building and running from source

### Requirements:
- [Graal VM](https://www.graalvm.org/downloads/)
- [Clojure CLI](https://clojure.org/guides/getting_started)(faster) or [Leiningen](https://leiningen.org/)(better Windows support).
- A running instance of [Bob](https://github.com/bob-cd/bob)

### Running

Installing GraalVM:
- Download and extract GraalVM CE. Go to the extracted location and navigate to
  the directory where you can find bin, lib, jre and other directories.
- Run `export GRAALVM_HOME=$PWD`.

Clone the repo and from the repo directory:
- Run `clojure -A:test -m kaocha.runner` if using Clojure CLI or `lein kaocha` with leiningen.
- Run `$GRAALVM_HOME/bin/gu install native-image` to get the Graal native compiler.
- Run `clojure -A:native-image` if using Clojure CLI or `lein native-image` with leiningen to compile it to a native executable.
  (Warning: Quite a resource heavy step)
- The executable is found in `target/` if compiled via Clojure CLI or in `/target/default+uberjar/` with leiningen.
- Running `./wendy can-we-build-it` should output `"Yes we can! 🔨 🔨"`

#### The full command reference can be found [here](https://github.com/bob-cd/wendy/blob/master/docs/commands.md)

#### Configuration
You can configure Wendy in `~/.wendy.edn`. When you create a `~/.wendy.edn`-file then it currently has to hold all the configuration. That means it needs to have following map:
```
{:connection {:host "127.0.0.1"
              :port 7777}}
```
Here you can configure where your bob runs. Only Host and Port are possible for now.
