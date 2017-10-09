# cjlennon

## What is cjlennon?

cjlennon is (apart from being my name - yes thats me - Chris Lennon!) is a system.  A system of software development that is designed to help software developers and software development teams produce high quality, high value code - and do this at the pace needed.  The system seeks to maximise developer creativity by maximizing developer freedom.  While also gaining the benifits that consistancy can bring.

The system consists of:

- A set of patterns.  Patterrns are the heart of software development - by following a pettern you are effectively gaining years of experience simply by following in others footsteps.  Because the end goal of the system is to maximise value by maximising developer creativity, through maximizing developoer freedom these are presented simply as patterns. They are not rules. Heck, they are not even guidelines.  Having said that, every pattern presented here represents a _lot_ of hard-won experience.  I hope you will consider some of them :-)

- A sample application.  Named `cognify` this is a full, working piece of open-source software that shows these patterns in use.  This is a responsive web application that allows the management of AWS Cognito users and includes reporting and synchronization with Gsuite (Gmail) contacts.  I am hoping to release this early 2018

Rather than simply presenting patterns in the abstract the sample applications shows one real-world application of these patterns in use.  Enjoy and I hope there will be many more!!

## Pattern: Everything is a module

In the cjlennon system, everthing, from application functionality, deployment pipeline tooling, css styling and so on (i.e. _everything_) is encapsulated within a module.

A module is a unit of functionality that is designed to achieve a single purpose. Sometimes these are knows a microservices.  A module needs to have the following characteristics:

- it is a single unit of functionality, i.e. it has a single purpose
- it accepts a single input (e.g. an event) and produces a single output
- it is self-contained.  That is it has no dependencies on other software other than what is fully contained in the repo.  The module may of course rely on other services, these dependencies need to be services interacted with over http
- it is documented
- it includes instructions and / or code that allows the user to host the module.  So the module sees the 'big picture' and enables end considers all aspects of the SDLC including production hosting and support (devops)

### In the sample application

In the sample application A module consists of one or more components.  These are the parts that fit together to create the module.  For example many cognify modules consist of an AWS Lambda function and an API Gateway gateway

In the sample app modules are named as [developer name] - [project name] - [purpose]  For example `cjlennon-cognify-login` or `myname-cms-check-auth` and so on. 

## Pattern.  The cloud

By cloud we mean the commercial cloud, e.g. AWS, Google, Azure etc etc.  The cloud has past the 'tipping point' and is now an essential service.  It offers many advantages and its not going anywhere

## Pattern.  Serverless

Serverless computing enables the rapid application development and deployment needed for cjlennon modules to add value

## Pattern. Amazon Web Services

A common infrastructure is required to enable collaberation between module authors. Of course some of the patterns documented here may help you if you don't use AWS.

## Pattern - naming things

Naming things is hard.  The below guidelines are written so I don't have to keep thinking about it

- Folder names, file names and urls
  - name these as lower case with hypen.  So `my-folder-name`, `my-file-name.js`, `/check-user-auth`
- In code use camelCase.  So `let myModule = require('my-module.js')`.  Simply camelCase all words, so `htmlPage` not `HTMLPage` `myApi` not `myAPI` and so on.
- For database tables and field names use MixedCase.  So `Contacts`, `OrderLines` tables, `Id`, `FirstName` field names etc.  Don't be afraid to use plurals for table names, so `Sessions` rather than `Session`, `OrderLines` over `OrderLine` and so on
- name commands eg. npm scripts as lower-case-with-hyphen.   Its one less key-stroke than the underscore
- Name environment variables as UPPERCASE_WITH_UNDERSCORE
- For everything else (and there is a lot else :-) )  use camelCase.  In the 'everything else' bucket would be JSON field names, HTML form fields, Swagger definitions, configuration such sas AWS API Gateway stage names, etc etc

## Pattern - styling nodejs development

If you are developing in nodejs (the sample application is in nodejs) then:
- always `use strict`
- Validate your formatting with [standard](https://github.com/standard/standard)

## Pattern: responding to problems

As we have said, a module is a unit of functionality, designed to accomplish a specific task.  Because of this we can say that there are only two outcomes for a module once it is triggered by an event:

1.	 The module completes successfully
2.	The module does not complete successfully

Of course this is very much a simplification, however it is a helpful concept when it comes to error handling, as we can establish a rule as follows:

#### Rule:  Whenever your module fails to complete successfully, an alert must be logged by the module.  That is, the problem should be trapped by the module, and logged.

‘Completing successfully’ can be defined as ‘the user gets the result they would expect’.  This is analogous to the ‘happy path’.  Another way of explaining this concept:  the case where a module fails to complete, and no error message is logged by the module itself, should never happen.

As an example, let's take an application with a front end that validates user input, and an associated API which also validates user input.  This is good practice and ensures there is an extra layer of validation and security beyond the front end.  If the API receives user input which is not valid it will correctly reject the request.  All is working as designed, however from a user point of view, the module has not completed successfully.  The fact that invalid input was supplied to the API is therefore a problem which must be handled and logged by the API module.

Of course not all problems are of equal severity and it is helpful to have a way of categorising the severity of problems.  It is also helpful to have problems logged in a standard way, this enables you to set up consistent monitoring and alerting rules.  For these two reasons the following is recommended as a specification for logging errors:

### cjlennon error logging specification

When a problem or error is trapped by the module a single json log entry should be made, with the following structure.
**alertType**  (required).  One of `cognifyAlert`, `cognifyAlertUnexpected`, `cognifyAlertSecurity`,
Where:
-  ‘cognify’ is the application name (and has been used here for the sake of clarity)
 -  `cognifyAlert` represents a specific error or problem case that has been trapped by the module.  
-  `cognifyAlertUnexpected` represents an unexpected error (for example this error type could be logged within the `catch` section of a `try / catch` code block)
-  `cognifyAlertSecuirty` is used for a specific problem case which may have security implications (for example a failed login attempt)

**Module** (required):  The name of the module in which the problem occurred

**Message** (required):  A summary of the error

**Notify**.  Use this field to store information around whether / how to notify people about the error.  For example you could set up a set of values as : NONE | SLACK | EMAIL | SMS

**User Message**.  If the error was reported back to the caller, this should be the exact text that was passed back to the user

**ErrorCode**.  A code associated with the error.  This should be generated by the application.  If an error with an error code is trapped, then this should be reported in the ‘context’ section

**Context**.  Information that may be helpful when attempting to troubleshoot the error

**StackTrace**.  A full stack trace of the error

**Event**.  For AWS Lambda functions, the source event

**Context**.  For AWS Lambda functions, the context object

## Pattern.  Use semantic versioning

Why wouldn't you?
