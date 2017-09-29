# cjlennon

## What is cjlennon?

cjlennon is (apart from being my name - yes thats me - Chris Lennon!) is a concept.  A set of modules that connect together, allowing developers to create applications by combining these modules.  A set of 'lego blocks' for the web.

## What is a module in the world of cjlennon?

A module is a unit of functionality that is designed to achieve a single purpose. Sometimes these are knows a microservices.  A cjlennon module has the following characteristics

- it is a single unit of functionality, i.e. it has a single pupose
- it is self-contained.  That is it has no dependencies on other software other than what is fully contained in the repo.  The module may of course rely on other services however it should be fully functional simply by downloading the repository
- it is open source, and any dependencies can only be on open source software
- it is documented
- it includes the ability to be hosted on AWS, perferably with little or no cost for usage that is not large
- it conforms to certain guidelines (these will be written later ;-) )
- it may optionally make use of certain patterns.  See for example the 'naming things' section below

A module consists of one or more components.  These are the parts that fit together to create the module.  For example a module could consist of an AWS Lambda function and an API Gateway gateway

## Rules and guidelines for cjlennon modules

- name cjlennon modules as [developer name] - [project name] - [purpose]  For example `cjlennon-cognify-login` or `myname-cms-check-auth` and so on.  Have a maximum of four words  - so `myname-myproject-check-auth` not `myname-myproject-check-user-is-authorised`
- use semantic versionning

## Pattern - naming things

Naming things is hard.  The below guidelines are written so I don't have to keep thinking about it

- Folder names, file names and urls
  - name these as lower case with hypen.  So `my-folder-name`, `my-file-name.js`, `/check-user-auth`
- In code use camelCase.  So `let myModule = require('my-module.js')`.  Simply camelCase all words, so `htmlPage` not `HTMLPage` `myApi` not `myAPI` and so on.
- For database tables and field names use MixedCase.  So `Contacts`, `OrderLines` tables, `Id`, `FirstName` field names etc.  
- name commands eg. npm scripts as lower-case-with-hyphen.
- Name environment variables as UPPERCASE_WITH_UNDERSCORE
- For everything else (and there is a lot else :-) )  use camelCase.  In the 'everything else' banner would be JSON field names, HTML form fields, Swagger definitions, AWS API Gateway stage names, etc etc

## Pattern - nodejs development

- always `use strict`
- for JavaScript code e.g. node.js code validate your formatting with [standard](https://github.com/standard/standard)
