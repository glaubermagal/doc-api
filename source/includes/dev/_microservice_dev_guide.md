# Microservice development guide

In this chapter, we'll cover additional details that you, as a RW API developer, should keep in mind when developing
your microservice. We'll focus not only on the technical requirements you need to meet for your microservice to
communicate with the remaining RW API internal components, but also discuss the policy surrounding development for the
RW API, as a way to achieve a certain degree of consistency across a naturally heterogeneous microservice-based system.

## Microservice overview

As described in the [API Architecture](/developer.html#api-architecture) section, microservices are small web
applications that expose a REST API through a web server. This means that microservices can be built using any
programming language, just as long as it supports HTTP communication. In practical terms, most of this API's core
microservices are built using [nodejs](https://nodejs.org), with [Python](https://www.python.org/)
and [Rails](https://rubyonrails.org/) being distant 2nd and 3rd respectively. New microservices being developed should
respect this hierarchy when it comes to choosing a development language, and the adoption of a different stack should be
validated with the remaining development team members beforehand.

In this whole section, we will use code examples from
the [Dataset microservice](https://github.com/resource-watch/dataset/), which is built using nodejs. We will discuss the
general principles, which should apply to all implementations, as well as implementation details, which may apply to
your scenario if you are also using nodejs, or that may not apply if you are using something else.

## Development lifecycle

As a developer of the RW API, your ultimate goal is to make an improvement to the API source code and push it to the
production environment. Of course, this is an overly simplistic description of a complex process, and the goal of the
next section is to dive deeper into the steps you need to take to achieve that. Breaking this down into a more detailed
list, these are the high-level steps you'll need to take in order to contribute to the RW API:

- Checkout the code
- Run it locally
- Make a feature branch
- Write your code
- Test your feature branch locally
- Push your code to Github
- Make a PR from your feature branch to `dev` and request review from peers
- Wait for Travis' and reviewer approval, then merge your code
- [Optional] Deploy your changes to the `dev` cluster for testing in a real-world infrastructure.
  Check with team if changes require this level of testing and spin up
  cluster before deploying if it is in [hibernation](#rw-api-hibernation).
- Make a PR from `dev` to `staging`, wait for Travis' approval, then merge your code.
- Make an announcement about an upcoming changes, deploy to `staging` and test your code with real-world data.
- Make a PR from `staging` to `production`, wait for Travis' approval, then merge your code.
- Make an announcement about an upcoming changes, deploy to `production` and test your code.

In the next sections we'll dive deeper into the details of each step.

## Setting up a development environment

In this section, we'll cover the details of how you can configure your operating system to be used as a development
environment for the [Dataset microservice](https://github.com/resource-watch/dataset/), which is built using nodejs.
These instructions will apply, without major changes, to all other nodejs-based microservices. For microservices based
on Python or Rails, and when using [Docker](https://www.docker.com/), you should also be able to use these instructions.
Native execution for Python and Rails microservices is done using equivalent commands, which we'll outline as we go.

Note that these instructions aim at giving you the details about **what's specific to the RW API**, and it's not a
step-by-step list of commands you can copy-paste. For example, we will not cover the details of how to install
dependencies - that's something best answered by that particular piece of software's documentation page for your
operating system, which you can easily find with your favourite search engine.

Also, when applying these instruction to different microservices, be sure to review their respective `README.md` file
for a comprehensive list of dependencies you'll need, or other specific details about its setup process.

### Execution - native vs Docker

All microservices can be executed in two ways: natively or using [Docker](https://www.docker.com/). If you are not
familiar with Docker, we suggest briefly learning about it does before proceeding. In a nutshell, it simplifies setup
and execution, at the expense of varying performance hit, depending on your operating system. Here are a few key points
you should consider when making a decision between executing natively or using Docker:

- When using Docker, you typically do not need to set up any other dependency for any microservice - Docker will take
  care of that for you.
- On Windows and Mac, Docker will run a small Linux virtual machine behind the scenes, which will mean a noticeable
  increase in resource consumption and a reduction in runtime performance, when compared to native execution. When using
  linux, that does not happen, and runtime performance and resource usage is roughly equivalent to native execution.
- Docker does have its quirks, and it does come with a bootstrap time penalty when running your code, so if you are not
  very familiar with it, or are used to native execution of nodejs, Python or Rails code, it may pay off to use that
  approach.
- The final service will run inside a Docker container. When using Docker containers during development, you reduce the
  risks of "it worked on my machine" type of problems.

### Using native execution

#### Getting the code

The first step will be getting the source code from [Github](https://github.com/resource-watch/dataset/) to your
computer using the [Git](https://git-scm.com/) CLI (or equivalent).

```shell
git clone https://github.com/resource-watch/dataset.git 
```

Or, if you prefer, you can use:

```shell
git clone git@github.com:resource-watch/dataset.git
```

#### Installing dependencies

In the source code you just downloaded, you'll find a `README.md` file with detailed instruction for the microservice,
including dependencies you'll need to install.

For all Node.js microservices, you'll need to install [Node.js](https://nodejs.org/en/) and [Yarn](https://yarnpkg.com).
Rather than installing Node.js from the official website, we recommend using [nvm](https://github.com/nvm-sh/nvm), which
allows you to easily install and manage different Node.js versions on your computer, since different microservices may
require different versions of Node.js to run.

<aside class="notice">
For Python, you can use something like <a href="https://github.com/pyenv/pyenv">pyenv</a>, and <a href="https://rvm.io/">rvm</a> for Rails-based microservices.
</aside>

```shell
# Install Node.js v20.4 for the dataset microservice
nvm install 20.4

# Switch to the v0.4 installation
nvm use 20.4
```

Once you've installed a version manager like `nvm`, you need to check which version of the language to install. For
Node.js microservices, the `package.json` file typically has a `engine` value which will tell you which version(s) of
Node.js are supported. Another place where you'll find this info (which also works for other languages) is the content
of the `Dockerfile` (typically in the first line) - in the dataset microservice, for
example, `FROM node:20.4-alpine3.18` means this microservice runs on Node.js v20.4.

```shell
# To install dependencies, navigate to the directory where you cloned the microservice and run:
yarn
```

[Yarn](https://yarnpkg.com) is a package manager for Node.js applications (a spiritual equivalent
to [pip](https://pypi.org/project/pip/) for Python or [Bundler](https://bundler.io/) for Ruby). Once it's installed, be
sure to use it to install the necessary libraries (see right).

<aside class="notice">
Windows users: If after running <code>yarn</code> you get an error where <code>gyp</code> cannot find Visual Studio, the solution <i>should</i> be as easy as running <code>yarn global add windows-build-tools</code> from an admin command prompt.
</aside>

The microservice's `README` may specify additional dependencies you need to
install. [MongoDB](https://www.mongodb.com/), for example, is a common dependency of many RW API microservices, with
applications
like [Postgres](https://www.postgresql.org/), [Redis](https://redis.io/), [RabbitMQ](https://www.rabbitmq.com/)
or [Open Distro for Elasticsearch](https://opendistro.github.io/for-elasticsearch/) also being required on certain
microservices. If a version number is not identified on the `README.md` file, the `docker-compose-test.yml` file may
help. `image: mongo:3.6` means this microservice depends on MongoDB v3.6.

Besides these dependencies, microservices may also depend on other microservices, for complex functionality or user
management. We'll dive deeper into that on the [Local gateway](#local-gateway) section. If your endpoint does not rely
on other microservices or use user data, you can ignore that section.

**A note on dependencies**

Due to a recent infrastructure migration, some `README` files may mention old dependencies that have since been replaced
with newer equivalents. Here are the old dependencies you may find, and their newer equivalent:

- Elasticsearch 5: it has been replaced by AWS Elasticsearch Service (based on Elasticsearch 7), which is based
  on [Open Distro for Elasticsearch](https://opendistro.github.io/for-elasticsearch/).

#### Configuration

With the dependencies set up, it's time to configure the microservice. This is done
using [environment variables](https://en.wikipedia.org/wiki/Environment_variable) (env vars) which you can define in
multiple ways, depending on your OS, way of executing the code (e.g. many IDEs have a "Run" feature that allow
configuring environment variables using a GUI) and personal preference. For this tutorial, and going forward, we'll
assume you'll run the code from a terminal and specify the environment variables inline.

```shell
NODE_ENV=production SOME_VARIABLE=some-value <more variables> <your command>
```

To find out more about which env vars you can/need to specify, refer to the microservice's `README.md` file, as it
typically documents the main variables available to you. Nodejs-base microservices will often have a full list in
the `config/custom-environment-variables.json` file. The `docker-compose-test.yml` and `docker-compose-develop.yml`
files contain usages of said variables, and may be helpful if you are looking for an example or an undocumented
variable.

As a rule of thumb, env vars configure things like databases address and credentials, 3rd party services (for example,
an AWS S3 bucket URL or AWS access credentials), or gateway URL.

#### Starting the microservice

```shell
# Starting a Node.js microservice:
yarn start

# Node.js using inline environment variables:
NODE_ENV=production <your other environment variables> yarn start

# Starting a Python microservice may look something like this:
python main.py

# Rails-based microservices can rely on the traditional Rails CLI:
rails server
```

Once you have determined the values you'll need to run your microservice with the desired configuration, you should have
everything ready to run it. For nodejs based microservice like Dataset, you can do this by running `yarn start`. For
other languages, the startup command will be different (see right).

You can also review the `entrypoint.sh` file content, under the `start` or `develop` sections, as it will contain the
command you need to execute to run the code natively.

The application should output useful information, like database connection status and HTTP port. Overall, if no error
message is produced, the microservice should be up and running, and available at the port specified by its output.

#### Running the tests

```shell
# Running tests for a Node.js microservice:
yarn test

# Node.js with environment variables:
NODE_ENV=test <your other environment variables> yarn test

# Python:
exec pytest <test folder>

# Ruby:
bundle exec rspec spec
```

Most microservices (hopefully all in the future) come with tests included. Running these tests can help you identify
issues with your code changes, and are required for any new modifications merged into the RW API. It's recommended that
you run tests locally before pushing changes to Github.

Tests sometimes mock certain dependencies, like external 3rd party service, but often require an actually running
database, as a native execution would (think MongoDB or Postgres). Check the `docker-compose-test.yml` for whatever
services it runs besides the microservice - those are the dependencies you'll need to have up and running to run the
tests natively. The gateway is not required to run the tests.

Test execution requires roughly the same env vars as running the actual microservice. For microservices that rely on a
database, make sure you are not using the same database as you do for development purposes - tests assume database
isolation, and will delete preexisting data.

See right for how to run tests for microservices in different languages. You can also review the `entrypoint.sh` file
content, under the `test` section, which will contain the exact command you need to execute.

#### Common errors and pitfalls

- **Your microservice cannot connect to MongoDB/other database**: ensure that the corresponding service is running and
  listening on the configured address and port - be mindful that `localhost`, `127.0.0.1` and your local IP are not
  always interchangeable. Also confirm user and password data.
- **Your microservice crashes when handling an API call, trying to reach a network address**: this may be your
  microservice trying to reach another microservice through the gateway. Make sure that both the gateway and the
  necessary dependent microservices are up and running, and that all microservices involved are registered in the
  gateway.
- **Your microservice has user-related issues, even though you are providing a `Bearer` token**: Bearer tokens are
  processed by the `authorization` microservice, which is reached through the gateway. Ensure both are up and running,
  and check their respective logs for more clues to what may be going wrong.
- **Your tests keep failing**: This can be due to multiple reasons. Check the microservice's travis status (link in the
  README.md) to see if it's just you, or if there's an issue with the preexisting code base. Run your tests a few more
  times and see if the output is consistent - some tests are not deterministic, and have varying results. Ensure your
  env vars are correct - check `docker-compose-test.yml` or `.travis.yml` for examples on values.

### Using Docker

#### Getting the code

The first step will be getting the source code from [Github](https://github.com/resource-watch/dataset/) to your
computer using the [Git](https://git-scm.com/) CLI (or equivalent).

```shell
git clone https://github.com/resource-watch/dataset.git 
```

Or, if you prefer, you can use:

```shell
git clone git@github.com:resource-watch/dataset.git
```

<aside class="notice">
Make sure to checkout with `--config core.autocrlf=input`. This avoids an issue between Docker and Windows when handling line endings.
</aside>

#### Installing dependencies

As we mentioned before, if you decide to use Docker, your only dependency will be Docker itself (and docker-compose,
which comes included). Depending on your OS, Docker installation instructions will differ, but your favourite web search
engine will hopefully point you in the right direction.

When you run Docker, it will automatically fetch the necessary dependencies and run them for you. However, if you are
not using Linux, you may have to fine-tune some settings so that dependencies like MongoDB can communicate with your
microservice - we'll review this in detail in a bit.

Note that Docker will not fetch nor run the gateway for you - if you want to execute your microservice in integration
with other microservices, you'll have to set it up manually. We'll dive deeper into that on
the [Local gateway](#local-gateway) section. If your endpoint does not rely on other microservices or use user data, you
can ignore that section.

#### Configuration

Configuration for Docker based execution is done
using [environment variables](https://en.wikipedia.org/wiki/Environment_variable) (env vars) passed to the Docker
runtime using a special `dev.env` file. Some microservices will include a `dev.env.sample` or equivalent that you can
copy-paste and use as a starting point when configuring your environment.

To find out more about which env vars you can/need to specify, refer to the microservice's `README.md` file, as it
typically documents the main variables available to you. Nodejs-base microservices will often have a full list in
the `config/custom-environment-variables.json` file. The `docker-compose-test.yml` and `docker-compose-develop.yml`
files contain usages of said variables, and may be helpful if you are looking for an example or an undocumented
variable.

As a rule of thumb, env vars configure things like databases address and credentials, 3rd party services (for example,
an AWS S3 bucket URL or AWS access credentials), or gateway URL. Your docker-compose file may already have predefined
values for some of these, in which case do not overwrite them unless you
are certain of what you're doing.

Docker networking works differently on Linux vs other operating systems, and you need to keep this in mind when
specifying values for things like MongoDB or gateway addresses. Under Linux, Docker containers and the host
operating system run in the same network host, so you can use `localhost`, for example, when telling a dockerized
Dataset microservice where it can reach the gateway (running natively or in a Docker container). Under other operating
systems, however, Docker containers run on a different network host, so you should instead use your local network IP -
using `localhost` will not reach your expected target.

#### Starting the microservice

For convenience, most microservices include a unix-based script that will run the Docker command that will start your
microservice, along with the dependencies covered by Docker. The file name will vary from microservice to microservice,
and the argument may also vary, but it's usually something along the lines of:

```shell
./dataset.sh develop
```

Mac users' mileage may vary with these scripts, and Windows users will need to manually open these file and reproduce
the included logic in Windows-compatible syntax - don't worry, they are pretty simple and easy to understand.

Docker will take a few minutes to run, especially during the first execution, but once it's up and running, you should
see the HTTP address where your microservice is available in the output printed to the console.

#### Running the tests

Running tests under Docker is similar to running the actual microservice. The easiest way to do so, for unix-based OSs
is using the included `.sh` helper file:

```shell
./dataset.sh test
```

#### Common errors and pitfalls

- **Your microservice cannot connect to MongoDB/other database**: this can happen with Docker setups if the database
  container takes longer to start than the microservice container - which is common on first time executions. Re-run the
  docker-compose command fixes it most times. Check if the address, port, username and password values on the `dev.env`
  file are correct - most of the time, the default values will work, and your `dev.env` file should not override them.
- **Your microservice crashes when handling an API call, trying to reach a network address**: this may be your
  microservice trying to reach another microservice through the gateway. Make sure that both the gateway and the
  necessary dependent microservices are up and running, and that all microservices involved are registered in the
  gateway.
- **Your microservice has user-related issues, even though you are providing a `Bearer` token**: Bearer tokens are
  processed by the `authorization` microservice, which is reached through the gateway. Ensure both are up and running,
  and check their respective logs for more clues to what may be going wrong.
- **Your tests keep failing**: This can be due to multiple reasons. Check the microservice's travis status (link in the
  README.md) to see if it's just you, or if there's an issue with the preexisting code base. Run your tests a few more
  times and see if the output is consistent - some tests are not deterministic, and have varying results.

### Local gateway

As previously mentioned, microservices rely on each other to build complex responses or provide advanced functionality.
They also rely on a dedicated microservice to validate user data, a scenario you'll probably run into often as you
develop your own endpoints. When deployed on the cloud, the RW API uses AWS API Gateway to achieve this. However, when
running microservices locally, using API Gateway is not feasible for multiple reasons, so instead you need to use an
alternative.

[Localstack](https://localstack.cloud/) aims to be a AWS drop-in replacement meant to be used in scenarios like the one
above, where you want to have AWS equivalent functionality, but locally/offline.

There are multiple ways to run Localstack, covered in the respective documentation page. For simplicity, this guide will
assume you are using the standard `docker` setup which, at the time of writing, can be executed using the following:

```shell
docker run --rm -it -p 4566:4566 -p 4510-4559:4510-4559 localstack/localstack
```

This will run Localstack locally, and make it available through port 4566.

Once it's up, you now need to configure it to act as the "real" AWS API Gateway configuration for RW API would. To do
this, you can
use [this Terraform project](https://github.com/resource-watch/api-infrastructure/blob/21aec8ae26cedf8423e353c27112d5d021974065/terraform-localstack),
which will effectively apply the same configuration that's present on the "real" AWS, but tweaked to work with
Localstack.

It's important to note a few details about this:

- This configuration will try to reach all microservices on the same host (provided by you as a Terraform variable).
- Each microservice is expected to be running on a specific port. You will find a folder per
  microservice [in this folder](https://github.com/resource-watch/api-infrastructure/tree/21aec8ae26cedf8423e353c27112d5d021974065/terraform-k8s-infrastructure/modules/k8s_microservice_routing).
  Each folder will contain a `main.tf` file, and in the first few lines of it, you will
  find the port in which you must expose that microservice. All ports are in the 30500+ range.
- You only need to start the microservices that you plan on using - no need to start all of them.

## CI/CD

The RW API uses multiple tools in it's [CI](https://en.wikipedia.org/wiki/Continuous_integration)
and [CD](https://en.wikipedia.org/wiki/Continuous_delivery) pipelines. All microservices that compose the RW API use a
common set of tools:

- [Github](https://github.com) for version control and code repository.
- [Travis CI](https://travis-ci.org) for automatic test execution.
- [Code Climate](https://codeclimate.com) for code coverage monitoring.
- [Jenkins](https://www.jenkins.io/) for deployment.

We assume, at this point, that you're already familiar with Github and its core functionality, like branches and pull
requests (PRs). If that's not the case, use your favourite search engine to learn more about those concepts.

Each microservice lives in a separate Github repository, most of which have Travis and Code Climate integrations
configured. Whenever a pull request is created, both tools will be triggered automatically - Travis will run the tests
included in the code, and notify the PR author of the result. Code Climate builds on top of that, and monitors and
reports [code coverage](https://en.wikipedia.org/wiki/Code_coverage). The behavior of both tools is controlled by a
single `.travis.yml` file you'll find in the root of each microservice's code base, and you can learn about it on each
of the tool's documentation page. You can see the result directly on the PR page.

When you want to submit a change to the code of one of the microservices, you should:

- Do your changes in a separate git branch, named after the change you're making.
- Target the `dev` branch (or `develop`, if `dev` does not exist yet - we're in the process of migrating to
  a `dev`+`staging`+`production` branch structure, but haven't done so for all repos yet).
- Include tests to cover the change you're making.
- Ensure your PR tests pass when executed by Travis.
- Maintain/increase the code coverage value reported by Code Climate.
- Briefly describe the changes you're doing in a `CHANGELOG.md` entry and, if these are public-facing, do a PR to
  the [RW API documentation repository](github.com/resource-watch/doc-api).

At this stage, and even if your tests pass locally, they may fail when executed in Travis. We recommend running them
again if this happens, to see if any [hiccup](https://whatis.techtarget.com/definition/hiccup) occurred. If that's not
the case, look into the Travis logs to learn more. Unfortunately, the reasons for these are diverse. It can be related
to env vars defined inside the `.travis.yml` file, missing or incorrectly configured dependencies, differences in
packages between your local environment and Travis', etc. At the time of writing, and by default which can be
overridden, Travis uses Ubuntu and is configured to [use native execution](/developer.html#using-native-execution) when
running tests, so using that very same approach locally may get you closer to the source of the problem you're
experiencing. Travis' output log will usually help you identify what's happening, and get you closer to a solution.

Once reviewed by a peer, your changes will be merged and will be ready for deployment to one of the live environments.

Currently, the RW API has 3 different environments:

- `dev` at [https://aws-dev.resourcewatch.org](https://aws-dev.resourcewatch.org) for internal testing and development
  of new features. There are no guarantees of stability or data persistence. While it's not barred from public access,
  it's meant to be used only by developers working on the RW API code, for testing, debugging and experimentation.
- `staging` at [https://staging-api.resourcewatch.org/](https://staging-api.resourcewatch.org/) is a more stable
  environment, meant to be used by both the RW API developers as well as other developers working on applications built
  using the RW API. It aims to be functionally stable, but occasional interruptions may occur if needed as part of a
  process, and code is sometimes in "release candidate" status, meaning it can have some issues. Data is often relied on
  by users of this API, so be mindful when performing destructive actions.
- `production` at [https://api.resourcewatch.org/](https://api.resourcewatch.org/) is meant to be as stable as possible,
  as it's used by real users.

Each microservice repository has a branch matching the name of each of these 3 environments, and changes will always go
from a feature branch to `dev`, then to `staging`, and finally to `production`. To push your changes across the
different environments, you should:

- Create a PR from the source branch to the target branch (from `dev` to `staging`, or from `staging` to `production`)
- Deploy the code to the respective environment (we'll see how in a moment)
- Test it with actual calls to the API, to validate that no side effects were introduced.

Depending on the scale of the changes you're doing, it's recommended to
use [git tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging) with [semantic versioning](https://semver.org/). Also
be sure to update the `CHANGELOG.md` accordingly, and the `package.json` or equivalent files if they refer a version
number.

Changes being pushed to either `production` or `staging` should be announced in advance in the `general` channel in the
WRI API Slack (and to contact Ethan Roday if you're not in that Slack workspace). Specifically, for changes going
to `production`, that notice period should be of at least 14 days, during which said changes should be available
in `staging` for testing by everyone. In rare cases, if a hotfix is needed to fix a breaking change in `production`, the
14-day lead time can be circumvented, but an announcement still must be made.

It's also best practice to announce the changes you're about to deploy before doing so, so that other developers of RW
API applications can be on the lookout for regressions, and can quickly get in touch with you should any undesired
behavior change be detected.

Each of the referred environments lives on a separate [Kubernetes](https://kubernetes.io/) cluster (hosted
with [AWS EKS](https://aws.amazon.com/eks/)), and deployment is done using individual Jenkins instances:

- [Jenkins for the dev environment](https://jenkins.aws-dev.resourcewatch.org)
- [Jenkins for the staging environment](https://jenkins.aws-staging.resourcewatch.org)
- [Jenkins for the production environment](https://jenkins.aws-prod.resourcewatch.org)

All 3 instances have similar overall configuration, but different microservices may deploy differently depending on the
behavior coded into the `Jenkinsfile` that's part of their source code - for example, some WRI sites are also deployed
using this approach, but opt to deploy both staging and production versions to the `production` cluster, and may not be
in the `staging` or `dev` Jenkins. However, the majority of services follow the convention of a single branch per
Jenkins instance, with the branch name matching the name of the respective environment.

The list of jobs you find on each Jenkins instance will match the list of services deployed on that environment. In the
details of each job, you should find a branch named after the environment, which corresponds to the Github branch with
the same name (some services may still have the old approach, with `develop` for `dev` and `staging`, and `master`
for `production`). You may also find other branches, or a different branch structure, depending on the service itself -
again, the `Jenkinsfile` configuration is king here, and you should refer to it to better understand what is the desired
behavior per branch. In some cases, old branches will be listed on Jenkins but should be ignored.

Deployments need to be triggered manually, on a per-microservice and per-branch basis. Once a deployment starts, Jenkins
will run the `Jenkinsfile` content - it is, after all, a script - and perform the actions contained in it. While it's up
to the maintainer of each microservice to modify this script, more often than not it will run the tests included in the
microservice, using Docker, and if these pass, push the newly generated Docker image
to [Docker Hub](https://hub.docker.com/). It will then update the respective Kubernetes cluster with content of the
matching subfolder inside the `k8s` folder of the microservice, plus the `k8s/service` folder if one exists. The last
step is to deploy the recently pushed Docker image from Docker Hub to the cluster, which will cause Kubernetes to
progressively replace running old instances of the service with ones based on the new version.

**A couple of important notes here:**

- All code deployed this way is made public through Docker Hub. If you have sensitive information in your codebase, and
  are using a Github private repository but are deploying using this approach, your information is NOT kept private.
- When deploying to production, most microservices will have an additional step at the end of the `Jenkinsfile`
  execution, which will require a human to explicitly click a link at the end of the Jenkins build log to trigger a
  deployment to the cluster. This is made intentionally so that deployment to the production environment are explicit
  and intentional, and are not triggered by accident.

While it's rare, tests ran by Jenkins at this stage may also fail, preventing your deployment. In these cases, refer to
the Jenkins build log for details, which most of the times can be reproduced
locally [running your tests using Docker](/developer.html#running-the-tests69). If your Jenkins log mentions issues
related with disk capacity or network address assignment problems, please reach out to someone with access to the
Jenkins VMs and ask for a [docker system prune](https://docs.docker.com/engine/reference/commandline/system_prune/).

## Infrastructure configuration

While the workflow above will cover most of the changes you'll do as an RW API developer - changes to the code that
powers the API - from time to time you'll need to adjust the actual infrastructure on which the API runs. This section
covers what you need to know to be able to manage the infrastructure.

### Infrastructure as code using Terraform

Each of the 3 RW API environments lives on a separate AWS account. To ease maintenance, the infrastructure configuration
is shared by all 3 environments, and is maintained using a common [Terraform](https://www.terraform.io/) project,
an [infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_code) tool. If you are not familiar with
Terraform, we recommend learning about it before proceeding.

Structure-wise, the 3 RW API environments are mostly equal, with the differences between them being the following:

- Scale and redundancy: the production environment has more and more capable hardware, to account for higher user load
  and also to provide redundancy on key services
- Sites: due to its stability-oriented purpose, the production environment also hosts the sites for some WRI-related
  projects, which run in
  dedicated [EKS node groups](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html), and that do
  not exist on the dev or staging clusters.
- Availability: being a development-only resource, the dev environment does not necessarily need to be available 24/7,
  and it may be intentionally unavailable as a cost-saving measure - we call this [hibernation](#rw-api-hibernation).

Due to the structure of the RW API infrastructure, the final architecture is defined by 2 Terraform projects:

- [The AWS Terraform project](https://github.com/resource-watch/api-infrastructure/tree/production/terraform) contains
  lower level elements, like networking, a [bastion host](https://en.wikipedia.org/wiki/Bastion_host), Jenkins and an
  AWS EKS Kubernetes cluster. This configuration is automatically applied to each AWS account
  using [Github Actions](https://github.com/features/actions) when merged to the respective branch. Github actions are
  also used to run a `terraform plan` preview of changes for each Pull Request.
- [The Kubernetes Terraform project](https://github.com/resource-watch/api-infrastructure/tree/production/terraform-k8s-infrastructure)
  mostly contains the configuration for Kubernetes services, as well as some database-level services. Unlike the
  previous, this Terraform project needs to be applied manually, using the `terraform apply` command.

The Kubernetes Terraform project relies on the resources provisioned by the AWS Terraform project (which is why
they [can't be merged into a single one](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs#stacking-with-managed-kubernetes-cluster-resources)),
so be sure that they are applied in that order.

While the Kubernetes Terraform project contains an increasingly large portion of the overall Kubernetes configuration,
there are some additional Kubernetes elements provisioned outside of it.

- [Some resources](https://github.com/resource-watch/api-infrastructure/tree/production/k8s-aws) are provisioned using
  traditional YAML files, that need to be manually applied using `kubectl apply` once the Kubernetes cluster is up and
  running. The link above contains not only said YAML files, but also associated documentation.
- [Kubernetes secrets](https://kubernetes.io/docs/concepts/configuration/secret/) are kept in a separate, private
  repository. Said repository has multiple YAML files, and organized by cluster, and then by Kubernetes namespace. Each
  of these YAML files needs to be manually applied whenever needed.

#### RW API hibernation

As mentioned above, to save costs on the dev environment, its functionality may be turned off at times when it's not
needed - we called this "hibernation". The goal is to have a way to dial down resources in times when they are not
needed (which we anticipate will be most of the time), while also giving RW API developers a simple and easy way to
restore full functionality in times when it's needed.

This can be achieved by modifying the `hibernate` boolean variable in
the [Terraform dev variables file](https://github.com/resource-watch/api-infrastructure/blob/e0c60966e82f98b2f05939169389d9170a4f4985/terraform/vars/terraform-dev.tfvars#L24)
and applying these changes (Github Actions will do this automatically on push/merge to the `dev` branch). Setting this
value to `true` will cause the dev RW API to go into hibernation and become unavailable, while `false` restore its
functionality. Keep in mind that both hibernation and restoration processes will take a few minutes, so we recommend the
company of your favourite beverage while you carry out these steps.

One important note here: while it's meant to be used with the dev environment only, there is no failsafe mechanism in
place preventing the staging or production environments from being set into hibernation as well. When modifying the
Terraform variables file, be sure you are on the correct file, otherwise you may accidentally cause staging or
production unavailability.

### Access to infrastructure resources

For management or debug purposes, you may need to access the infrastructure resources. Depending on what you want to
achieve, there are multiple paths forward. However, for all of them, a common required element is an AWS account with
the adequate permissions. These permissions will depend on what you're trying to achieve in particular. The AWS IAM
permission mechanism is too powerful and complex to cover here, so be prepared to see a few "permission denied" from
time to time, and to discuss with your fellow RW API developers what permissions you are missing that will allow you to
access a give resource.

#### Infrastructure details

Infrastructure details are accessible in multiple ways, depending on exactly what you're looking for.

If you are looking for a high-level piece of information (like "how many CPUs are we running?"), you may use the AWS
Console directly, as it provides a simple UI for a lot of information. Alternatively, investigating the Terraform files
are a good way to learn about what services are configured overall, without having to browse every page of the AWS
Console, or worry that you may be looking in the wrong AWS Region.

Finally, for very low level details, AWS has a CLI tool that may expose information not available through the channels
mentioned above.

In all scenarios, if you are looking to permanently modify the infrastructure, keep in mind that the Terraform projects
are kings here, and any change made using either the AWS Console or AWS CLI that is not persisted to Terraform should be
considered ephemeral, as it may be overwritten at any time without prior warning. You may, however, modify the
infrastructure using the AWS Console or AWS CLI as a means of experimentation, before projecting your final changes on
Terraform.

#### Infrastructure access

Infrastructure access is often need as a way to access things like Kubernetes, database dumps, system status, etc. It's
not an end in itself, but rather a necessary step to achieve other goals. To configure your infrastructure access,
you'll need two elements.

The first of which is a running and configured [AWS CLI](https://aws.amazon.com/cli/) tool installation. The AWS CLI
tool has comprehensive documentation, which should also cover the install steps for your particular operating system. To
configure it you'll also need the AWS account covered in the previous section.

The second element you'll need is access to the [bastion host](https://en.wikipedia.org/wiki/Bastion_host). If you are
not familiar with bastion hosts, we recommend reading about it before proceeding but, in a nutshell, a bastion host
works as a single point of entry into key parts of the infrastructure, which are otherwise inaccessible from the public
internet. A way to contact a service running in the infrastructure from the outside world is creating an SSH tunnel that
proxies traffic to that service through the bastion host, thus bypassing this restriction. For this to work, you need
SSH access to the bastion host, which a fellow RW API developer may grant you.

```shell script
ssh -N -L <local port>:<target service address>:<target service port> <bastion host user>@<bastion host address>
```

To create an SSH tunnel under a unix-based system, you'll need to run a command like the example here.

#### Database access

Access to databases (to extract a dump for testing, for example) depends on how said database service is configured. At
the time of writing, some database services run as AWS managed services, while other live inside the Kubernetes cluster,
as Kubernetes services.

For database services provided by AWS managed services, the only necessary steps are the ones covered previously on
the [Infrastructure access](/developer.html#infrastructure-access) section. After that, you should be able to reach the
host of the database service, per details provided by the service itself. You may also need authentication details for a
specific service, which you may find either on the Terraform configuration, the Kubernetes secrets files or AWS secret
storage.

For access to database services running as a Kubernetes service, you'll need Kubernetes access (which we will cover
next). Once you have that configured, you should configure
a [Kubernetes port forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)
to map said service to a port of your local host. Access credentials are typically available on the Kubernetes secrets
files.

#### Kubernetes access

The RW API runs in a AWS EKS Kubernetes cluster, which can be accessed using
the [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) command line tool, which you should install on
your computer. You also need the elements previously covered in
the [Infrastructure access](/developer.html#infrastructure-access) section, so be sure that your AWS CLI is installed
and configured, and that you have a way to communicate with the infrastructure's inner elements.

To configure `kubectl`, you will need some details that are specific to the kubernetes cluster you're trying to access.
Said details are available as the output of the `terraform apply` command that's executed by Github Actions for
the [AWS Terraform project](https://github.com/resource-watch/api-infrastructure/tree/production/terraform). Be mindful
that, amongst those details, is the URL through which `kubectl` should contact the Kubernetes control plane. Given that
you are using an SSH tunnel, you should:

- Modify the Kubernetes URL in the `kubectl` by adding a custom port value to it (say 4433, for the sake of example)
- Modify the you local hosts file so that the Kubernetes URL is resolved to your 127.0.0.1 IP (or equivalent)
- Create a SSH tunnel that maps your local port (the 4433 from the example above) to the actual EKS URL and port,
  proxied through the bastion host.

```shell
ssh -N -L 4433:<EKS URL>:443 <bastion host user>@<bastion host URL>
```

Here's an example of how you could create said SSH tunnel:

#### Log access

Logs for the whole infrastructure are centralized in AWS Cloudwatch. Optionally, if you find it more convenient, you can
opt to use `kubectl` to access logs for a particular pod or container, but you'll also find that output on AWS
Cloudwatch.

Certain AWS managed services' logs will only be available on Cloudwatch, so we encourage you to learn how to navigate
it.

## Testing your changes

With your code live on one of the clusters, you should now proceed to testing it. The type of tests you should run vary
greatly with the nature of the changes you did, so common sense and industry best practices apply here:

- The bigger the change, the broader the testing should be.
- Test your changes with different types of users, `applications`, payloads, etc.
- Try to break your code - send unexpected input, try to access resources you should not have access to, etc. More
  important than doing what it should is not doing what it shouldn't .
- If you can, ask for help - testing can be seen as an exercise in creativity, and having someone's assistance will help
  think outside the box.
- If you find a bug, fix it, and test everything again, not only what you just fixed.
- If a test is "simple", write it as a code test, which is reproducible. Save manual testing for the complex scenarios.
- Test the assumptions you used for behavior of other microservices - E2E testing mocks other microservices, so this may
  be the first time your code is running alongside real instances of other microservices.
- Clean up after your tests - if you created a bunch of test data, do your best to delete it once you're done. This is
  particularly important if you are testing something in the production environment, as that test data may be visible to
  real world users. Cleaning up in staging is also highly recommended.

If you are implementing a new endpoint and it's mission critical to the RW API or one of the applications it powers, you
may want to add a [API smoke test](/developer.html#api-smoke-tests) to ensure that any issue affecting its availability
is detected and reported. Refer to that section of the docs for more details.

## Microservice internal architecture - nodejs

<aside class="notice">
This section is completely implementation specific and opinionated, and does not reflect any technical requirement of the API. However, as all nodejs microservices use this architecture, we will cover it here as it's useful for new developers. Other microservice implementations in other languages will have different architectures, and you can also implement your own microservice using nodejs using a totally different architecture. Take this whole section as information/suggestion rather than as a ruleset.
</aside>

Nodejs microservices are based on the [Koa](https://koajs.com/) framework for nodejs. To understand the following code
snippets, we assume you are familiar with the basics of the framework, like how routes are declared and handled, what
middleware is, and how it works. You should also be somewhat familiar with tools
like [npm](https://www.npmjs.com/), [mongo](https://www.mongodb.com/)
and [mongoose](https://mongoosejs.com/), [Jenkins CI](https://jenkins.io/), [docker](https://www.docker.com/)
and [docker-compose](https://docs.docker.com/compose/)

### Anatomy of a (nodejs) microservice

In this section, we'll use [the dataset microservice](https://github.com/resource-watch/dataset) as an example, but
these concepts should apply to most if not all nodejs microservices:

- app: source code for the microservice functionality.
- config: configuration for different environments in which the microservice will be executed.
- k8s: [kubernetes](https://kubernetes.io/) configuration.
- Jenkinsfile: deployment configuration for [Jenkins CI](https://jenkins.io/).
- dataset.sh: convenience executable file (the name will always match the microservice).
- docker-compose-develop.yml: [docker-compose](https://docs.docker.com/compose/) configuration for develop environment.
- docker-compose-test.yml: [docker-compose](https://docs.docker.com/compose/) configuration for test environment.
- docker-compose.yml: [docker-compose](https://docs.docker.com/compose/) configuration for production environment.
- entrypoint.sh: [docker](https://www.docker.com/) entry point.

Since we are interested in the microservice's functional bits, we'll analyse the `app` folder content in more detail.
It's worth mentioning that, depending on how you run the microservice, the respective `docker compose` files may contain
relevant information and configuration, as do the files inside the `config` folder.

The `app` folder contains the following structure:

- src: source code for the microservice.
- test: test source code.
- Gruntfile.js: [grunt](https://gruntjs.com/) task definition file.
- index.js: nodejs entry point.

The grunt file includes several task definition that may be useful during day-to-day development. However, grunt is
semi-deprecated (it's still needed, don't remove it) in the sense that it's recommended to define useful tasks in
the `package.json` file instead - those tasks will, in turn, call grunt tasks.

Inside the `app/src` folder you'll find the following structure. The folders below will be commonly found on all
microservice, unless stated otherwise:

- data: data files. This folder is specific to the dataset microservice.
- errors: error classes for specific scenarios, which then in turn translate into specific HTTP codes and responses.
- models: `mongose` models to ease integration with `mongo`.
- routes: `koa` route and request handling definition, as well as middleware.
- serializers: `mongoogse` model to JSON response serializers.
- services: application business logic.
- validators: input validators.
- app.constants.js: microservice application constants.
- app.js: `koa` bootstrap, as well as basic error handling and gateway registration.
- loader.js: convenience file that iterates over the nested content of the `routes` folder and loads files.
- logger.js: convenience file that configures the logger for ease of use.

### Adding a new endpoint

In this section we'll cover how you can add a new endpoint with new functionality to an existing microservice. The aim
is not to be a comprehensive guide to cover all cases, but more of a quick entry point into day-to-day actions you may
want to perform, which should be complemented by your own learning of how a microservice works - remember that all
microservices, despite being structurally similar, have their own custom code and functionality.

To add a new endpoint, here's the short tasklist you have to tackle:

- Register your route in koa.
- Add a handler for that route.
- Add middleware for validation, if applicable.
- Implement new services, models or serializers to handle your application logic, if applicable.
- Add tests for your functionality (you may want to start with this, if TDD is your thing).
- Update the [Infrastructure as code](#infrastructure-as-code) repository and deploy the updated configuration.

### Register your route in koa

Route registration is done using the [koa-router](https://github.com/ZijianHe/koa-router) library, and can be done in
the `app/src/routes/api/v1/dataset.router.js` file, usually at the bottom of if:

```javascript

// router object declaration, usually at the top of the file
const router = new Router({
  prefix: '/dataset',
});

// routes declaration, usually at the bottom of the file
router.get('/', DatasetRouter.getAll);
router.post('/find-by-ids', DatasetRouter.findByIds);
router.post('/', validationMiddleware, authorizationMiddleware, authorizationBigQuery, DatasetRouter.create);
// router.post('/', validationMiddleware, authorizationMiddleware, authorizationBigQuery, authorizationSubscribable, DatasetRouter.create);
router.post('/upload', validationMiddleware, authorizationMiddleware, DatasetRouter.upload);
router.post('/:dataset/flush', authorizationMiddleware, DatasetRouter.flushDataset);
router.post('/:dataset/recover', authorizationRecover, DatasetRouter.recover);

router.get('/:dataset', DatasetRouter.get);
router.get('/:dataset/verification', DatasetRouter.verification);
router.patch('/:dataset', validationMiddleware, authorizationMiddleware, DatasetRouter.update);
router.delete('/:dataset', authorizationMiddleware, DatasetRouter.delete);
router.post('/:dataset/clone', validationMiddleware, authorizationMiddleware, DatasetRouter.clone);
```

In here you'll find the already existing routes. As you can see from the rather explicit syntax, you need to call the
method that matches the desired HTTP verb on the `router` object, and pass it a variable number of arguments - more on
this in the next section. One thing to keep in mind is that all the routes in a file are typically prefixed, as defined
in the `router` object declaration.

## RW API integration

While they could technically work as standalone applications, microservices are built from the ground up to work as part
of a microservice-based application - the RW API. As such, the ecosystem provides each microservice with a few
convenience
feature that [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) certain aspects, such as:

- Requests to other RW API microservices.
- [JWT tokens](reference.html#jwt-tokens) and [API key](reference.html#api-keys) parsing and handling.
- [Fastly](https://www.fastly.com) integration.

These 3 aspects can be handled by RW API integration libraries, that can be used as-is as part of the microservice
development
lifecycle. Currently, there are libraries for 3 development environments that support the latest RW API features:

- [nodejs package for Koa](https://github.com/resource-watch/rw-api-microservice-node) v5.1.3 and later
- [Python module for Flask](https://github.com/resource-watch/rw-api-microservice-python) v3.0.0 and later
- [Rails engine](https://github.com/resource-watch/rw-api-microservice-rails)

Due to the way in which the different development environments are structured, not all 3 libraries implement the 3
integration
aspects previously identified. As this guide uses the nodejs environment, we'll cover the corresponding library, which
does implement all 3, but you may need to manually add said functionality to your microservice if needed and not
provided.

### Integrating the library in your microservice

As with all libraries you add to your source code, be sure to read the README and follow the setup instructions in it.

### Requests to other RW API microservices

Often, microservices need to communicate with each other to build complex responses or provide advanced functionality.
As highlighted above, microservices don't need (nor should) communicate directly with each other, and instead rely on
two key pieces of information to achieve said communication:

- The gateway URL (provided as a configuration value)
- The endpoint path (typically hardcoded in the microservice itself)

Your code can then call a known RW API endpoint using the following approach:

```javascript
// Loading the details of a user by its id
// The API Key should be retrieved from the client application's request headers
const { RWAPIMicroservice } = require('rw-api-microservice-node');

const getUserById = async (userId, apiKey) => {
  const body = await RWAPIMicroservice.requestToMicroservice({
    uri: `/auth/user/${userId}`,
    method: 'GET',
    headers: {
      'x-api-key': apiKey,
    }
  });
  logger.debug('User by id', body.data);
  return body.data;
}
```

Refer to the integration library documentation for details on all arguments supported, including TypeScript type
definitions.

### JWT Token handling

RW API manages request authentication by using [JWT tokens](https://jwt.io/). When provided, these tokens are included
in the headers of the request sent by the client application. Many RW API endpoints will provide different data or
functionality depending on which user issued a request, so it's crucial that these tokens are validated and the
corresponding user data loaded prior to the request being processed by the microservice.

To ease this process, the RW API integration library for nodejs includes a middleware that will detect said JWT token
and, if present, will validate it and load the associated user details. While this is fully transparent to you as a dev,
it's worth knowing that this is done internally by the library, by issuing a request to the `authorization`
microservice, which is responsible for managing user accounts. The actual implementation is very similar to
the [Requests to other RW API microservices](#requests-to-other-rw-api-microservices) example in the previous section.

If the JWT token is valid, the user data will be available to you as part of the koa context object, without you having
to look for it or explicitly load it. Depending on the HTTP request verb, it may be available as a query parameter, or
as part of the request body. The following snippet illustrates a function that would handle both scenarios, and return
the user object:

```javascript
const getUser = (ctx) => {
  const { query, body } = ctx.request;
  let user = { ...(query.loggedUser ? JSON.parse(query.loggedUser) : {}), ...ctx.request.body.loggedUser };
  return user;
}
```

### API key handling

The RW API uses API keys to identify client applications, and to provide them with access to the API. These API keys are
required on most endpoints, and are provided by client applications as part of the request headers. The integration
library will automatically intercept these headers and, if present, validate them. It will also use that data to
generate analytical records of the incoming request, and the user and application associated with them. This is done
automatically and transparently to you as a developer, so you don't have to worry about it, but it may be useful to
learn about it, as it may help you debug issues.

You can optionally disable API key validation or analytics collection - see the integration library documentation for
more details.

### Fastly integration

Fastly is an HTTP cache that sites between the client applications and the actual infrastructure that hosts the RW API.
It mostly works transparently to both API users and developers but, as a developer, you may want to tweak its behavior
so that, for example, one of your endpoint's responses is cached more or less often.

This functionality builds on top of the existing [HTTP caching](#http-caching) functionality, so be sure to learn about
that before proceeding.

It's worth always keeping in mind that, like with all HTTP caches, only non-authenticated, GET requests with 2xx or 3xx
response codes are cached - everything else is ignored as passed as-is. By default, even the requests that match the
above criteria are not cached, as caching _must_ be explicitly enabled on a per-response basis.

You can control the caching policy applied to your responses using its headers. Specifically, you have headers to do so:

- `cache`: Setting the `cache` header will tell the HTTP cache to cache your response. The value provided (space
  separated list of strings) will be the different keys associated with your cache entry
- `uncache`: You can set the `uncache` on any non-GET, successful response. Like before, it accepts a space separated
  list of string values. When set, this will cause all cache keys associated with any of the provided values to be
  purged. This is useful, for example, as part of a DELETE request, when you want to invalidate any cached response that
  may have included the deleted element.

Using these headers, the RW API integration library will ensure Fastly behaves as you would expect.

### Other considerations

In some scenarios, while developing, it's not practical to run all the microservices your logic depend on on your
development computer. The [Writing end-to-end tests](/developer.html#writing-end-to-end-tests) section has some details
about writing tests for your code, including how you can mock such calls, so you don't have to run the actual
dependencies.

## Docker

When deployed in a production environment, microservices will run in a [Docker](https://www.docker.com/) container. As a
microservice developer, you should include in your microservice the necessary configuration to run your application
inside a container. This is done using a Dockerfile, and you can use
the [Dataset microservice's Dockerfile](https://github.com/resource-watch/dataset/blob/develop/Dockerfile) as an example
of how one of these files looks like for a nodejs based microservice.

Its worth noting that these container are set up in a way that allows using them to both run the microservice itself, or
their tests. This will be useful further ahead when we review the testing approach you should use when writing
microservices.

## Data layer

Many microservices require the ability to store data to perform their function. The RW API has several data storage
tools available to you, in case you need to store information to run your service.

**Warning**: microservices run on ephemeral containers managed by Kubernetes, and often in multiple parallel instances,
so do not rely on storing data on the filesystem, unless you know there's something like
a [Kubernetes' persistent volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) to back it up.

When accessing these tools, there are a few things you should keep in mind:

- Isolation is not guaranteed, meaning your microservice will have theoretical access to other microservice's data, and
  other microservices may access your data.
- Despite having access to it, you should not manipulate other microservice's data directly at the data layer, unless
  there's a clear agreement between the involved microservices.
- It's up to you to ensure logic level isolation of your data - for example, if you rely on an relational database, be
  sure to use a unique database name.
- Access to the data layer is only available within the RW API cluster, which is why not all data storage tools have
  authentication enabled.

Currently, the following data storage tools are available on the RW API cluster:

### MongoDB v3.6

[MongoDB](https://www.mongodb.com/) is the most frequently used data storage tool, as it supports schema-less document
storage, thus making it easy to setup and run. When using MongoDB, be sure to give your collection an unique name, to
avoid conflicts

To see an example of how to use MongoDB on a real-world microservice, check out
the [Dataset microservice](https://github.com/resource-watch/dataset/).

### Postgres v9.6

Use [Postgres](https://www.postgresql.org/) if your application needs a relational database. Unlike other data storage
tools, Postgres access to individual microservices is granted on a per-database basis.

To see an example of how to use Postgres on a real-world microservice, check out
the [Resource watch manager microservice](https://github.com/resource-watch/resource-watch-manager/) (written in Ruby on
Rails).

### AWS Elasticsearch Service v7.7

Use [AWS Elasticsearch Service](https://aws.amazon.com/elasticsearch-service/) (powered
by [Open Distro for Elasticsearch](https://opendistro.github.io/for-elasticsearch/)) for search optimization or
heterogeneous data storage with quick access.

To see an example of how to use Elasticsearch on a real-world microservice, check out
the [Document dataset adapter microservice](https://github.com/resource-watch/document-adapter/).

### Redis v5.0

[Redis](https://redis.io/) is an in-memory data storage tool, and can also be used as
a [pub-sub](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) messaging tool.

You can learn how to use Redis in your applications by looking at the code of
the [Subscriptions microservice](https://github.com/gfw-api/gfw-subscription-api).

### Neo4J v2.0

[Neo4J](https://neo4j.com/) is a graph database used
by [Graph microservice](https://github.com/resource-watch/graph-client/) to build complex associations between different
RW API resources.

### RabbitMQ v3.7

[RabbitMQ](https://www.rabbitmq.com/) is a message broker service, which is particularly useful when handling long,
asynchronous operations. You can see an example of its usage on
the [Document microservice - Executor submodule](https://github.com/resource-watch/doc-executor) code base.

### Cloud services

Some microservices have data storage needs that are not covered by the applications described here (for example, file
storage). In those scenarios, it's common to use cloud services (like AWS S3, for example), but do reach out to the
broader RW API development team before implementing your solution.

## HTTP caching

The RW API has a system-wide HTTP cache that you may use to cache your requests, improving scalability and response
times. This cache is based on [Fastly](https://www.fastly.com/), and you can browse its documentation if you are looking
for a specific detail on its behavior. For most common use cases, you just need to keep in mind the following:

- Verbs other than GET are not cached.
- Only responses with codes 200, 203, 300 and 410 are cached, and the default cache TTL is 3 days.
- GET responses for `/auth` endpoints are never cached.
- GET responses for `/query` or `/fields` endpoints are cached for 2 days.

If you are using the nodejs RW API integration library, you can further tweak this behavior by using
the [Fastly integration](#fastly-integration).

## Logging

An important part of microservice operation is logging events as it processes requests. Many errors are only triggered
during staging and production server execution, and without proper logging, there isn't a way to identify how it can be
reproduced, so it can then be fixed.

Common development languages often come with either built-in or 3rd party logging libraries than make logging easier to
handle. Current nodejs microservices use [Bunyan](https://github.com/trentm/node-bunyan) to manage logs, which eases
managing log destinations (stdout, file, etc) and log levels. Other libraries, for nodejs and other languages, offer
similar functionality.

For microservice staging and production logs, the output channels should be `stdout` and `stderr`, the standard output
streams you'll find on most OSs. When live, these, will seamlessly integrate with the infrastructure to which
microservices will be deployed, and will allow for cluster-wide logging.

```javascript 
const logger = require('logger');

logger.info('Validating Dataset Update');
```

The example above logs that the validation process for input data associated with a dataset updated has been started.
You'll notice that the `info()` function is called - this sets the logging level for this message. While different
logging tools implement different strategies to differentiate logs, most microservices uses these 4 levels:

- `debug`: use this whenever anything happens, that may or may not be relevant to have logged on a daily basis, but
  rather as a opt-in development and debug tool.
- `info`: use this for high-level expected actions and output that you'd need to have available to you in case you need
  to investigate a production issue.
- `warn`: use this for situations where something unexpected happened, but that may not necessarily be irregular flows -
  for example, user originated errors.
- `error`: use this when the application failed and is no longer able to recover, or when an server-side error occurs.

A common issue some developers have concerns logging errors. It's not uncommon to find microservices where all types of
errors generate a `error` log entry. However, this actually produces a lot of noise, and make it hard to debug. Consider
the following two scenarios when attempting to load a dataset by id:

- The dataset microservice queries the database, and the database cannot find a dataset matching that id, and the
  microservice returns a 404 HTTP response.
- The dataset microservice queries the database, but the database is offline for whatever reason, and the microservice
  returns a 500 HTTP response.

Both cases are, indeed, errors. However, the first one is not an application error - the microservice behaved as it
should. In this scenario, logging this event should not involve an `error` level event, as nothing unexpected, from the
application's point of view, happened: a user asked for something that does not exist, and the microservice handled that
as it should.

On the second case, however, something really unexpected did happen - the microservice could not contact the database.
This is an application level error, as we assume that our databases are always available for to microservices. This is
an example scenario where a `error` logging line should be generated. Or, putting it in another way, only use `errors`
logging for situations where a RW API developer should look into it.

Another best practice we recommend for log management is using an application-wide configuration value to define the
logging level. This is prove extremely useful when you switch from your local development environment (where you may
prefer the `debug` logging level for maximum detail) to production (where `warn` or `error` may be more reasonable).

When using Bunyan, logging levels are
set [per stream](https://github.com/resource-watch/dataset/blob/5d4b6d1a3b243f5ba985b444633c0f6acf78b35d/app/src/logger.js#L7).
Many microservices integrate the [Config](https://www.npmjs.com/package/config) library at this stage, allowing you to
have different values
for [production](https://github.com/resource-watch/dataset/blob/25ab6b6ac7b1c4618b3d4ae1690957b256bafca8/config/prod.json#L4), [staging](https://github.com/resource-watch/dataset/blob/685a799f79f2441a129e4cf5cfaf3ed06ace5546/config/staging.json#L4)
or other environments. Config also allows you
to [override selected values with a environment variable](https://github.com/resource-watch/dataset/blob/34d4b00fe06bd6d7c9b1dd25e043da4e820db653/config/custom-environment-variables.json#L12),
typically `LOGGER_LEVEL`, which you may use, for example, to temporarily override the logging level on a particular
environment without changing the predefined default values.

If you want to access your logging output for a microservice that's already deployed on either staging or production,
you'll need access to `kubernetes` logging UI or CLI.

## Testing

Testing code is important. And, as the developer of a RW API microservice, it's your responsibility to ensure that your
code is bug free and easily extendable in the future. That means it should ship with a set of tests that can ensure, now
and in the future, that it does what it's supposed to do. And the best way to do that is through testing.

If you are developing a new microservice or endpoint, it's expected that you provide a complete test suit for your code.
In many cases, existing microservices will be a valuable source of examples you can copy and adapt to your needs. On
occasion, you'll need to changes to endpoints that are not yet covered by tests. In those scenarios, we ask that add at
least the tests to cover your modification. If you are feeling generous, and want to add tests that cover the endpoint's
full functionality, you'll have our gratitude - test coverage for the RW API's endpoints is a work in progress, and not
all endpoints have been reached just yet.

### Writing end-to-end tests

Most microservices rely, to varying degrees, on end-to-end tests. In the context of an HTTP based microservice, this
means that tests are responsible for issuing an HTTP request to a running instance of your microservice, getting the
response and validating its content. Tests should also handle things like mocking resources and isolation from outside
world - we'll get to these in a moment.

> Example of a test from the dataset microservice

```javascript
it('Create a JSON dataset with data in the body should be successful', async () => {
  const timestamp = new Date();
  const dataset = {
    name: `JSON Dataset - ${timestamp.getTime()}`,
    application: ['forest-atlas', 'rw'],
    applicationConfig: {
      'forest-atlas': {
        foo: 'bar',
      },
      rw: {
        foo: 'bar',
      }
    },
    connectorType: 'document',
    env: 'production',
    provider: 'json',
    dataPath: 'data',
    dataLastUpdated: timestamp.toISOString(),
    data: {
      data: [
        {
          a: 1,
          b: 2
        },
        {
          a: 2,
          b: 1
        },
      ]
    }
  };

  nock(process.env.GATEWAY_URL)
    .post('/v1/doc-datasets/json', (request) => {
      request.should.have.property('connector').and.be.an('object');
      const requestDataset = request.connector;

      requestDataset.should.have.property('name').and.equal(dataset.name);
      requestDataset.should.have.property('connectorType').and.equal(dataset.connectorType);
      requestDataset.should.have.property('application').and.eql(dataset.application);
      requestDataset.should.have.property('data').and.deep.equal(dataset.data);
      requestDataset.should.have.property('sources').and.eql([]);
      requestDataset.should.not.have.property('connectorUrl');

      return true;
    })
    .reply(200, {
      status: 200,
      detail: 'Ok'
    });

  const response = await requester.post(`/api/v1/dataset`).send({
    dataset,
    loggedUser: USERS.ADMIN
  });
  const createdDataset = deserializeDataset(response);

  response.status.should.equal(200);
  response.body.should.have.property('data').and.be.an('object');
  createdDataset.should.have.property('name').and.equal(`JSON Dataset - ${timestamp.getTime()}`);
  createdDataset.should.have.property('connectorType').and.equal('document');
  createdDataset.should.have.property('provider').and.equal('json');
  createdDataset.should.have.property('connectorUrl').and.equal(null);
  createdDataset.should.have.property('tableName');
  createdDataset.should.have.property('userId').and.equal(USERS.ADMIN.id);
  createdDataset.should.have.property('status').and.equal('pending');
  createdDataset.should.have.property('overwrite').and.equal(false);
  createdDataset.should.have.property('applicationConfig').and.deep.equal(dataset.applicationConfig);
  createdDataset.should.have.property('dataLastUpdated').and.equal(timestamp.toISOString());
  createdDataset.legend.should.be.an.instanceOf(Object);
  createdDataset.clonedHost.should.be.an.instanceOf(Object);
});
```

Current nodejs based microservices rely on [Chai](https://www.chaijs.com/) and [Mocha](https://mochajs.org/) as testing
libraries, and this code example shows one of the tests that validate the dataset creation process. The code block is
relatively large, but the logic is simple:

- We craft a JSON object with the content of the HTTP POST body
- As this endpoint needs to make a call to another microservice (through the gateway), we
  use [Nock](https://github.com/nock/nock) to mock that POST request to the `/v1/doc-datasets/json` endpoint. This way,
  your tests won't require actual running instances of the gateway or other microservices to run.
- We send the our previously crafted POST request to a running instance of our dataset microservice, along with
  a `loggedUser` spoof data.
- We get the HTTP response, process it for easier handling, and proceed to validate that it's content is as expected.

Different microservices and endpoint will have different requirements when it comes to testing, but the great majority
of endpoints can be tested using simple variations of these steps. There are some additional considerations you should
take into account when testing:

- The example above creates an actual dataset, meaning a MongoDB (or equivalent mocks) need to exist. For MongoDB
  specifically, our approach so far has been to use a real MongoDB instance, and running the tests on a separate
  database (´dataset-tests´ for example), aiming for isolation. Other microservices (for example, those relying on
  Elasticsearch) use mocks instead. Mocking usually leads to faster execution times, but can be troublesome to properly
  code. Use whichever alternative is best for you, and refer to the [Data layer](/developer.html#data-layer) section for
  examples of microservices that use (and test with) different tools.
- Nock has a [feature that blocks all HTTP requests](https://github.com/nock/nock#enabledisable-real-http-requests),
  which is useful to ensure your code or tests are not relying on an external service without you being aware - just be
  sure to whitelist your own IP, otherwise the HTTP call your test makes to your microservice will fail too.
- Tests must be idempotent, and execute without assuming order. For example, running a test that first tests an insert,
  and then using the inserted element to test a delete would be a bad practice. Instead, your insert test should clean
  up its data once it's done, and the delete test should prepopulate the database before actually trying to delete it. A
  corollary of this is that you should be able to run your tests multiple times, back-to-back, without that affecting
  the results.

### Test coverage metrics

While not required, most microservices use [code coverage](https://en.wikipedia.org/wiki/Code_coverage) tools to
evaluate how much of your code base is actually being checked when the test suite is executed. Nodejs based
microservices frequently use [NYC](https://www.npmjs.com/package/nyc) and [Istanbul](https://istanbul.js.org/) for this
purpose, in case you are looking for a recommendation.

### Running your tests using docker compose

The previous section covers an example of how a test looks like. Depending on your microservice technology stack, you
have different ways of running your tests - in the case of the Dataset microservice, tests are executed
using [yarn](https://yarnpkg.com/).

However, to standardise test execution, you should also create a [docker compose](https://docs.docker.com/compose/) file
that runs your tests (and their dependencies). This docker compose configuration should use
the [existing docker file](/developer.html#docker) set up previously, unless that's not possible.

Here's an example
of [one of these files](https://github.com/resource-watch/dataset/blob/ab23e379362680e9899ac8f191589988f0b7c1cd/docker-compose-test.yml).
These will be particularly useful down the line, but also convenient for running tests locally.

For convenience, microservices commonly have
a [one line CLI command](https://github.com/resource-watch/dataset/blob/ab23e37936/dataset.sh#L8) that allows running
tests using the docker compose configuration you provide. These are particularly useful for other developers to run your
tests without having to manually set up the associated dependencies.

### CI/CD, Travis and Code Climate

Assuming you are hosting your microservice code on a service like Github, then you may benefit from its integration
with [CI/CD](https://en.wikipedia.org/wiki/CI/CD) tools. There are multiple options in this space, and they mostly offer
the same core functionality, but our preference so far has been to use [Travis](https://travis-ci.org/). In a nutshell,
you can configure Travis to run your tests every time you push a new commit to a Github pull request. Tests will run on
Travis' servers, and if they fail, you will get a message on your pull request warning you about this.

For full details on Travis and its features, how to configure it, what alternatives are there, and their pros and cons,
please refer to your favourite search engine. If you are just want the basic, "it just works"
configuration, [this file from the Dataset microservice](https://github.com/resource-watch/dataset/blob/cd55ff83695266ffae58471569fc3c16b2f1adf2/.travis.yml#L10)
will have most of what you'll need.

Apart from running your tests, Travis also integrates with a service called [Code Climate](https://codeclimate.com/)
which analyses your code and looks for potentially problematic bits and suggests you fix them. More often than not, we
just rely on another functionality offered by Code
Climate - [code coverage](https://en.wikipedia.org/wiki/Code_coverage). This allows you to easily monitor how your pull
request influences code coverage - for example, you can set up an alarm that warns you in case your pull request
decreases your code coverage, which may indicate that you added more code than you tested.

Most microservices will display a test status and code coverage badges on their README, as a way to display if the tests
are passing, and a rough estimation of how broad the test coverage is.

### Smoke testing

Besides the test tools covered above, which are used to validate that your code changes work as designed, there is also
a smoke test tool in place, that periodically issues a request to selected RW API endpoints and validates that the
response match an expected preconfigured value. These tests are not used to validate functionality, but rather
availability - if something goes wrong, a human is notified that the RW API is not working as it should, and that this
is potentially affecting users.

If you believe your microservice implements a mission-critical endpoint that would merit one of these tests, please
reach out to the RW API team.

## Deploying your microservice

### Jenkins

Microservice deployment to the Kubernetes clusters is done using [Jenkins](https://www.jenkins.io/). The actual
deployment process is configurable using
a [Jenkinsfile](https://github.com/resource-watch/dataset/blob/develop/Jenkinsfile) script written
in [Groovy](https://groovy-lang.org/). Most microservices use the same file, as the logic in it is flexible enough to
accommodate most scenarios.

In a nutshell, this Jenkinsfile will:

- Build a docker image using the Docker file contained in your microservice.
- Uses the included docker compose configuration to run your tests. If the tests fail, the process is aborted at this
  stage
- Push the generated docker image to [dockerhub](https://hub.docker.com/)
- Depending on the git branch and the Jenkinsfile content, some of the following actions may take place:
  - If deploying from the `dev` branch, it will push the docker image to the dev kubernetes cluster
  - If deploying from the `develop` or `staging` branches, it will push the docker image to the staging kubernetes
    cluster
  - If deploying from the `master` or `production` branches branch, you will get a confirmation input. If you confirm,
    it will push the docker image to the production kubernetes cluster
  - Any other branches are ignored.

**A note on branches**: an old branching scheme you may still find on some microservices relied on `master` + `develop`
branches, but it's gradually being replaced by a scheme that uses `dev`, `staging` and `production`. All repos use one
scheme or the other, but not both simultaneously, and the Jenkinsfile will reflect that.

At the beginning of each deploy process, you may also see an confirmation input that, if accepted, will redeploy the
kubernetes configuration contained in the microservice code repository to the respective kubernetes cluster: `develop`
branch to the staging cluster, `master` branch to the production cluster.

One thing worth noting is that the docker images generated using this method are publicly available on dockerhub. Be
careful not to store any sensitive data in them, as it will be available to anyone.

#### Getting access to Jenkins

Each environment (`dev`, `staging`, `production`) has its own Jenkins server:

- [Jenkins for the dev environment](https://jenkins.aws-dev.resourcewatch.org)
- [Jenkins for the staging environment](https://jenkins.aws-staging.resourcewatch.org)
- [Jenkins for the production environment](https://jenkins.aws-prod.resourcewatch.org)

If you need an account in one of these environments (for example to approve a deployment in production), contact
ethan.roday@wri.org.

### Kubernetes configuration

Most microservice have a [Kubernetes configuration folder](https://github.com/resource-watch/dataset/tree/develop/k8s),
typically containing 3 folders:

- `production` contains files that will be applied when deploying the `master` branch to the production cluster
- `staging` contains files that will be applied when deploying the `develop` branch to the production cluster
- `services` contains files that will be applied when deploying either branches to their respective cluster.

Note that these settings are only applied if you opt in to it, by interacting with the input request that is displayed
on Jenkins at the very beginning of the deployment process.

## Documentation

### README

Here are some 'do' and 'do not' you should take into account when writing the README.md for your microservice.

Do:

- Add the name of your microservice, along with a high level, short description of what it does.
- Identify the technical dependencies. This should include:
  - Programming language
  - Main framework or language-specific tools used
  - Data layer dependencies or other applications, including version numbers
  - Dependencies on other microservices
- Describe how to get your microservice up and running for development purposes (at least for the operating system you
  are currently using, but you get extra "thank you"s if you add details for other OSs)
- Describe how to run your tests
- Describe if and which configuration variables exist, and their behavior.
- Document implementation details, software development architectural decisions, etc
- Use English.

Do not:

- Document in detail how to set up dependencies on 3rd party applications (for example, don't provide installation
  instruction for a database server, just mention it's a dependency, and assume your fellow developer will figure out
  how to set it up on their system).
- Include a license text - you may mention it, but add the actual text on a separate file, to keep the README file
  concise.
- Assume the reader has vast experience in developing for the RW API, in the language your microservice is coded on, or
  using its dependencies or libraries
- Document endpoint behavior - that goes elsewhere.

Overall, the README should be targeted at developers that may need to run, test and debug your code.

### Functional documentation

Documentation describing the business logic implemented by your microservice should go in
the [RW API reference documentation](reference.html) page. The documentation is
available [on this Github repository](https://github.com/resource-watch/doc-api/) and its README includes instructions
on how to use it and contribute.

Documentation is a key component of a successful API, so when altering public-facing behavior on the RW API, you must
update the documentation accordingly, so that RW API users out there can be aware of the changes you made.

## Code styling

As a way to help RW API developers collaborate, most microservices include
a [linter](https://en.wikipedia.org/wiki/Lint_(software)) tool and ruleset to promote, as much as possible, a common set
of rules for the way code is structured.

For microservices written in nodejs, this is achieved using [Eslint](https://eslint.org/)
with [this configuration file](https://github.com/resource-watch/dataset/blob/develop/.eslintrc.yml).

For ruby-based microservices, you can use [Rubocop](https://github.com/rubocop-hq/rubocop) along
with [this configuration file](https://github.com/resource-watch/resource-watch-manager/blob/develop/.rubocop.yml).

Most microservices will also include
a [.editorconfig](https://github.com/resource-watch/dataset/blob/develop/.editorconfig) file - you can learn more about
there [here](https://editorconfig.org/).
