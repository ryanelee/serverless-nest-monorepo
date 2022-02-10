# Serverless Nest Monorepo Plugin

Plugin that adds support to run NestJS Apps from Nest Monorepo, which is set up using Nest CLI with several microservices.

**This plugin allows you to use Serverless framework with each microservice from monorepo. Each microservice gets separate configuration for Serverless framework.**

Nest monorepo usually looks like this:

- apps/
  - service1
  - service2
  - service3
- lib/
  - library1
  - library2
- node_modules/
- dist/
  - apps/
    - service1
    - service2
    - service3

It is build using `nest build <service>` command and generates webpacked JavaScript file from TypeScript in `dist/apps/service1` directory. Node modules are in root directory as are `nest-cli.json` or other configuration files.

This structure is fairly incompatible with Serverless framework `serverless`, since it does not work very good with multiple services.



## Installation

1. Install with npm in monorepo root:

`npm install --save-dev serverless-nest-monorepo`

2. Move existing serverless.yml file from monorepo root to correct NestJS app. Place it in `apps/service1` directory.

`mv serverless.yml apps/service1/serverless.yml`

3. Create a new serverless.yml file in monorepo root then add the plugin to your root serverless.yml file:

```
service: '<your-monorepo-name-same-as-in-package.json>'

useDotenv: true # Remove if not in use
plugins:
  - serverless-nest-monorepo

provider:
  name: aws
  runtime: nodejs14.x

frameworkVersion: '3'

plugins:
  - serverless-nest-monorepo
```
Both `provider` and `service` settings are ignored, but are there for main serverless config validation.

## Usage

Once root `serverless.yml` is set up and each microservice you wish to run has `serverless.yml` in it's app directory, run `serverless mono` from the root.

```
serverless mono --nestApp service1
```


### Caveats

- If you are using `useDotenv: true`, ensure you set it to true in root `serverless.yml` and a symlink will be created for correct NestJS microservice.
- It was only tested using Serverless Framework **version 3**.
- Config in root must pass serverless's configuration check, so a provider must be picked. Both name and runtime are ignored in the root file.
- It is suggested to add `.tml.yml` to .gitignore, as the plugin creates symbolic links to prevent commiting them to repository.

### How it works?

Once `serverless mono` command is executed, a temporary symbolic link (`ln -s`) is created to the serverless file for the correct app. The original file remains
