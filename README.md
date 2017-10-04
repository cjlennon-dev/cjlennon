# cjlennon

## What is cjlennon?

cjlennon is (apart from being my name - yes thats me - Chris Lennon!) is a system.  A system of software development that is designed to help software developers and software development teams produce high quality, high value code - and do this at the pace needed.  The system seeks to maximise developer creativity by maximizing developer freedom.  While also gaining the benifits that consistancy can bring.

The system consists of:

- A set of patterns.  Patterrns are the heart of software development - by following a pettern you are effectively gaining years of experience simply by following in others footsteps.  Because the end goal of the system is to maximise value by maximising developer creativity, through maximizing developoer freedom these are presented simply as patterns. They are not rules. Heck, they are not even guidelines.  Having said that, every pattern presented here represents a _lot_ of hard-won experience.  I hope you will consider some of them :-)

- A sample application.  Named `cognify` this is a full, working piece of open-source software that shows these patterns in use.  This is a responsive web application that allows the management of AWS Cognito users and includes reporting and synchronization with Gsuite (Gmail) contacts.  I am hoping to release this early 2018

Rather than simply presenting patterns in the abstract the sample applications shows one real-world application of these patterns in use.  Enjoy and I hope there will be many more!!

## Pattern: Modularise everyhing (except what is best not to modularise)

A module is a unit of functionality that is designed to achieve a single purpose. Sometimes these are knows a microservices.  A module has the following characteristics

- it is a single unit of functionality, i.e. it has a single purpose
- it is self-contained.  That is it has no dependencies on other software other than what is fully contained in the repo.  The module may of course rely on other services however it should be fully functional simply by downloading the repository
- it is documented
- it includes instructions and / or code that allows the user to host the module.  So the module sees the 'big picture' and enables end considers all aspects of the SDLC including production hosting and support (devops)


### In the sample application

In the sample application A module consists of one or more components.  These are the parts that fit together to create the module.  For example many cognify modules consist of an AWS Lambda function and an API Gateway gateway

In the sample app modules are named as [developer name] - [project name] - [purpose]  For example `cjlennon-cognify-login` or `myname-cms-check-auth` and so on. 

## Patern.  Use semantic versioning

Why wouldn't you?

## Pattern - naming things

Naming things is hard.  The below guidelines are written so I don't have to keep thinking about it

- Folder names, file names and urls
  - name these as lower case with hypen.  So `my-folder-name`, `my-file-name.js`, `/check-user-auth`
- In code use camelCase.  So `let myModule = require('my-module.js')`.  Simply camelCase all words, so `htmlPage` not `HTMLPage` `myApi` not `myAPI` and so on.
- For database tables and field names use MixedCase.  So `Contacts`, `OrderLines` tables, `Id`, `FirstName` field names etc.  Don't be afraid to use plurals for table names, so `Sessions` rather than `Session`, `OrderLines` over `OrderLine` and so on
- name commands eg. npm scripts as lower-case-with-hyphen.   Its one less key-stroke than the underscore
- Name environment variables as UPPERCASE_WITH_UNDERSCORE
- For everything else (and there is a lot else :-) )  use camelCase.  In the 'everything else' bucket would be JSON field names, HTML form fields, Swagger definitions, configuration such sas AWS API Gateway stage names, etc etc

## Pattern - nodejs development

If you are developing in nodejs (the sample application is in nodejs) then:
- always `use strict`
- Validate your formatting with [standard](https://github.com/standard/standard)
