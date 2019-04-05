---
layout: post
title: Building a shared Library - SPFx projects and Shared Libraries - iii
date: 2019-02-21 +13:00
published: false
category: Dev
tags: [spfx, sharepoint, sharedlibraries, spfxextensions, spfxwebparts, typescript, npm]
---

When we left off [last time](https://nateforsyth.github.io/2019-02-21-building-shared-library-spfx-projects-and-shared-libraries-part-2/), I'd highlighted an issue I had faced whereby there was no supported way to build a shared code package _within_ the SharePoint Framework (SPFx). I rambled on about what had happened, why this approach was chosen, etc. I also realised that the post was getting too long, and decided to continue with a new post detailing the build and deployment process.

It's now that time...

## What this post will cover.

- Scaffolding and configuration of the project.
- Early issues that were encountered.
- building the project.


## Scaffolding and configuration of the project.

### tsconfig.json

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
  "exclude": ["node_modules", "**/__tests__/*"],
  "allowJs": true
}
~~~

### package.json

~~~json
{
  "name": "bd-solar-spfx-common-services",
  "version": "2.2.2",
  "description": "This package is intended to be used to share SharePoint API wrappers with multiple SPFx solutions prepared by Brighter Days Ltd.",
  "main": "index.js",
  "scripts": {
    "test": "npm run build && mocha -r esm ./src/__tests__/**/*.test.ts",
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
  "dependencies": {
    "@microsoft/decorators": "^1.4.1",
    "@microsoft/sp-core-library": "^1.4.1",
    "@microsoft/sp-http": "^1.4.1",
    "@microsoft/sp-lodash-subset": "^1.4.1",
    "@microsoft/sp-page-context": "^1.4.1",
    "@types/jquery": "^3.3.29",
    "@types/underscore": "^1.8.9",
    "jquery": "^3.3.1",
    "moment": "^2.22.2",
    "sp-pnp-js": "^3.0.8",
    "underscore": "^1.9.1"
  },
  "devDependencies": {
    "@microsoft/sp-build-web": "^1.4.1",
    "@microsoft/sp-module-interfaces": "^1.4.1",
    "@types/chai": ">=3.4.34 <3.6.0",
    "@types/mocha": ">=2.2.33 <2.6.0",
    "ajv": "~5.2.2",
    "esm": "^3.2.4",
    "tslint": "^5.12.1",
    "typescript": "^3.3.1"
  }
}
~~~

### tslint.json

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

### index.ts

~~~ts
import { SPHttpClient } from "@microsoft/sp-http";

import { BDTermStoreService } from "./SpoServiceWrappers/BDTermStoreService";
import { BDPagesService } from "./SpoServiceWrappers/BDPagesService";
import { BDFilesService } from "./SpoServiceWrappers/BDFilesService";
import { BDListsService } from "./SpoServiceWrappers/BDListsService";
import { BDMegaMenuService } from "./BDServiceWrappers/BDMegaMenuService";

// expose SPO Service Wrapper classes
export const FileService = (validatedSiteColUrl: string, httpClient: any) => new BDFilesService(validatedSiteColUrl, httpClient as SPHttpClient);
export const ListService = (validatedSiteColUrl: string, httpClient: any) => new BDListsService(validatedSiteColUrl, httpClient as SPHttpClient);
export const PageService = (validatedSiteColUrl: string, httpClient: any) => new BDPagesService(validatedSiteColUrl, httpClient as SPHttpClient);
export const TermStoreService = (validatedSiteColUrl: string, httpClient: any, serverRelativeUrl: string) => new BDTermStoreService(validatedSiteColUrl, httpClient as SPHttpClient, serverRelativeUrl);
export const MegaMenuService = () => new BDMegaMenuService();

import { StringHelper, UrlHelper, DomHelper, ValidationHelper } from "./BDUtilities";
import { BDServiceUtilities } from "./SpoServiceWrappers/BDServiceUtilities";
// expose Utility classes
export const TenantHelperUtils = (httpClient: any) => new BDServiceUtilities(httpClient as SPHttpClient);
export const StringHelperUtils = () => new StringHelper();
export const UrlHelperUtils = () => new UrlHelper();
export const DomHelperUtils = () => new DomHelper();
export const ValidationHelperUtils = () => new ValidationHelper();
~~~


## Early issues that were encountered.




## building the project.




