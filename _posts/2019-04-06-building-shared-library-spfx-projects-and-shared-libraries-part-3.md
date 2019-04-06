---
layout: post
title: Building a shared Library - SPFx projects and Shared Libraries - iii
date: 2019-04-06 +13:00
published: true
category: Dev
tags: [spfx, sharepoint, sharedlibraries, spfxextensions, spfxwebparts, typescript, npm]
---

When we left off [last time](https://nateforsyth.github.io/2019-02-21-building-shared-library-spfx-projects-and-shared-libraries-part-2/), I'd highlighted an issue I had faced whereby there was no supported way to build a shared code package _within_ the SharePoint Framework (SPFx). I rambled on about what had happened, why this approach was chosen, etc. I also realised that the post was getting too long, and decided to continue with a new post detailing the build and deployment process.

It's now that time, but first, there are assumptions being made here.

- First, you have an Azure DevOps environment that you are using for Source Control.
- Second, you are going to use the Azure DevOps environment as your build platform.

If you're not using those, fear not, simply modify the instructions to suit your Source Control system and choice of packaging environment, e.g. npm.

Here are links to all of the posts on this topic:
- [SPFx projects and Shared Libraries - numero uno](https://dreamsof.dev/2019-02-15-spfx-projects-and-shared-libraries-part-1/)
- [Building a shared Library - SPFx projects and Shared Libraries - part deux](https://dreamsof.dev/2019-02-21-building-shared-library-spfx-projects-and-shared-libraries-part-2/)
- Building a shared Library - SPFx projects and Shared Libraries - iii


## What this post will cover.

- Scaffolding and configuration of the project.
- Early issues that were encountered.
~~- building the project.~~
~~- bundling the project.~~
~~- using the project.~~


## Scaffolding and configuration of the project.

I am assuming, since you're reading my post about SPFx, that you already have a development environment setup using [Visual Studio Code](https://code.visualstudio.com/), but if not, follow these tutorials as I won't rehash the basics here, that's not what this post is for.

- [Set up your SPFx Development Environment](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/set-up-your-development-environment)
- [Build a Hello World Web Part](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/web-parts/get-started/build-a-hello-world-web-part)

Sooo... you have a development environment set up and are ready to get started.


### Create your Library project

First, navigate to your favourite code repo folder, open a terminal (I prefer PowerShell).

Next, check that you have the latest versions of [Node.js](https://nodejs.org/en/) and [npm](https://www.npmjs.com/get-npm).

These are versions in our currently supported environment, [ymmv](https://dictionary.cambridge.org/dictionary/english/ymmv) though.

~~~powershell
node --version # 10.15.2
npm --version # 6.8.0
~~~

Now we will create a folder for the project, and initialise the project using npm.

~~~powershell
mkdir [tld.domain].MyCoolSPFxUtilityLibrary
cd [tld.domain].MyCoolSPFxUtilityLibrary
npm init
~~~

Fill in the details you want to. You'll now have a **package.json** file within your project folder.

It's now time to install TypeScript and tslint.

~~~powershell
npm install -d typescript
npm install -d tslint
~~~

You'll probably also want to install other dependencies  from npm (or yarn) at this point, including some SharePoint specific functionality you'll no doubt need. That's outside of the scope of this post though - the import process is no different than importing adding them to an SPFx project.

If you haven't already done so, it's time to install your favourite unit testing framework. I chose [Mocha](https://www.npmjs.com/package/mocha) and [Chai](https://www.npmjs.com/package/chai), mainly due to them being included out of the box within SPFx projects scaffolded by the [Yeoman SharePoint Generator](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/toolchain/scaffolding-projects-using-yeoman-sharepoint-generator).


### Let's complete our project structure.

- add a *src* folder to the root, and then two files: **index.ts** and **Utilities.ts** - we'll leave these empty for now.
- now, add a *_tests* folder under **src**, and then another file: **Currency.test.ts** - we'll also leave this empty (the x.test.ts naming scheme ensures that the testing framework can find all related unit tests you prepare).

Now, initialise your Git repo and commit the initial files.

~~~powershell
git init
~~~


### Confirm and lock down our configuration

Here's an example package.json file.


#### package.json

~~~json
{
  "name": "tld-domain-mycoolspfxutilitylibrary",
  "version": "1.0.0",
  "description": "This package is intended to be used to share common SharePoint functionality between various SPFx components.",
  "main": "index.js",
  "scripts": {
    "test": "npm run build && mocha -r esm ./src/_tests/**/*.test.ts",
    "build": "tsc",
    "lint": "tslint -p tsconfig.json"
  },
  "babel": {
    "presets": [
      "es2015"
    ]
  },
  "engines": {
    "node": ">=0.10.0"
  },
  "repository": {},
  "files": [
    "lib/**/*"
  ],
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {},
  "devDependencies": {
    "@types/chai": ">=3.4.34 <3.6.0",
    "@types/mocha": ">=2.2.33 <2.6.0",
    "ajv": "~5.2.2",
    "esm": "^3.2.4",
    "tslint": "^5.12.1",
    "typescript": "^3.3.1"
  }
}
~~~


Add a TypeScript config file (tsconfig.json) to the root of the project. Use this configuration as your base.

#### tsconfig.json

~~~json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "declaration": true,
    "skipLibCheck": true,
    "outDir": "./lib",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src"],
  "exclude": ["node_modules"],
  "allowJs": true
}
~~~


Now, add a TSLint config file (tslint.json). This is a pretty standard example, though there are some overrides I have taken the liberty of disabling; you don't need me to go through this.

#### tslint.json

~~~json
{
    "defaultSeverity": "error",
    "extends": [
        "tslint:recommended"
    ],
    "jsRules": {},
    "rules": {
    "class-name": false,
      "export-name": false,
      "forin": false,
      "label-position": false,
      "member-access": true,
      "no-arg": false,
      "no-console": false,
      "no-construct": false,
      "no-duplicate-case": false,
      "no-duplicate-variable": true,
      "no-eval": false,
      "no-internal-module": true,
      "no-shadowed-variable": true,
      "no-switch-case-fall-through": true,
      "no-unused-expression": true,
      "no-use-before-declare": true,
      "semicolon": true,
      "trailing-comma": false,
      "typedef": false,
      "typedef-whitespace": false,
      "valid-typeof": false,
      "variable-name": false,
      "whitespace": false,
      "ordered-imports": false,
      "max-line-length": false,
      "no-string-literal": false,
      "object-literal-sort-keys": false,
      "object-literal-shorthand": false,
      "prefer-for-of": false,
      "object-literal-key-quotes": false,
      "max-classes-per-file": false
    },
    "rulesDirectory": []
}
~~~


Got something similar to all of the above? Right on!!! Let's shrinkwrap the dependencies.

~~~npm
npm shrinkwrap
~~~


### It's now time to get into the meat of the post - creating your library functionality.


Open up your Utilities.ts file, and enter the following code (this is an admittedly very basic example, but suitably illustrates the concepts being discussed here).

#### ./src/Utilities.ts

~~~ts
export class StringHelper {
    public truncateStringToDefinedLength(stringToTruncate: string, desiredLength: number): string {
    return stringToTruncate.length === desiredLength ? stringToTruncate : stringToTruncate.length + 2 > desiredLength ? `${stringToTruncate.substr(0, desiredLength)}...` : stringToTruncate;
  }
}
~~~


Open up your index.ts file and add the following.

#### ./src/index.ts

~~~ts
import { StringHelper } from "./BDUtilities";
export const StringHelperUtils = () => new StringHelper();
~~~


*Unfamiliar with unit testing? [check out this tutorial](https://semaphoreci.com/community/tutorials/getting-started-with-node-js-and-mocha) to get started.*

Now, open up your Currency.test.ts file, and add the following:

#### Currency.test.ts

~~~ts
import { StringHelper } from "./../../lib/BDUtilities";
import { expect } from "chai";

const stringHelper = new StringHelper();

describe("Equal length string truncation function", () => {
    const lengthToTruncate = 12;
    const stringToTruncate = "Nate Forsyth";
    const willTruncate = stringToTruncate.length > lengthToTruncate;
    it("should equal 12", () => {
        expect(stringHelper.truncateStringToDefinedLength(stringToTruncate, lengthToTruncate)).to.length(willTruncate ? lengthToTruncate + 3 : stringToTruncate.length);
    });
});

describe("Long string truncation function", () => {
    const lengthToTruncate = 4;
    const stringToTruncate = "Nate Forsyth";
    const willTruncate = stringToTruncate.length > lengthToTruncate;
    it("should equal 7", () => {
        expect(stringHelper.truncateStringToDefinedLength(stringToTruncate, lengthToTruncate)).to.length(willTruncate ? lengthToTruncate + 3 : stringToTruncate.length);
    });
});

describe("Short string truncation function", () => {
    const lengthToTruncate = 25;
    const stringToTruncate = "Nate Forsyth";
    const willTruncate = stringToTruncate.length > lengthToTruncate;
    it("should equal 3", () => {
        expect(stringHelper.truncateStringToDefinedLength(stringToTruncate, lengthToTruncate)).to.length(willTruncate ? lengthToTruncate + 3 : stringToTruncate.length);
    });
});
~~~


### It's now time to compile your code and run your unit tests

Compile your code and run your code by running the following npm command in your terminal (configured in the **package.json** file).


~~~powershell
npm install -g mocha
~~

#### Compile and run the tests

~~~powershell
npm run test
~~~

Here are the expected results

~~~powershell
# results

  Equal length string truncation function
    √ should equal 12

  Long string truncation function
    √ should equal 7

  Short string truncation function
    √ should equal 3


  3 passing (10ms)
~~~


## Early issues that were encountered.


### missing modules

~~~powershell
"Cannot find [xyz] module"
~~~

or

~~~powershell
"The term 'xyz' is not recognized as the name of a cmdlet, function, script file, or operable program."
~~~

These can usually be resolved by reinstalling the package from npm, although sometimes you'll need to install a package globally for the intended functionality to work as intended. I encountered this with:
- mocha
- esm


### framework incompatibilities

I'd originally chosen [JestJs](https://jestjs.io/) as my testing framework of choice as I've used it many times before, however I hadn't expected it to have issues with SPFx related projects.

The issues I faced wouldn't be an issue in a project such as the one detailed in this post, however as soon as you add Microsoft libraries to the project you will come to a screeching halt. This is due to Microsoft having not packaged .js files with their libraries, meaning that Jest will not be able to invoke unit tests even though your project compiles just fine.

My work-around for this was to simply fall back to Mocha and Chai, as in addition to being the ootb supported unit testing solution for Yeoman scaffolded SPFx projects, it's also still rather easy to use.

Of course4, Mocha and Chai still had the same issue, but that was mitigated by installing [esm](https://www.npmjs.com/package/esm) as a module loader, which assited in transpiling the required files (which Jest couldn't handle).


## phew, this has been another long post.

I'll save the build, bundling and usage for another post. Check back later :)
