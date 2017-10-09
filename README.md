# cjlennon

## What is cjlennon?

cjlennon is (apart from being my name - yes thats me - Chris Lennon!) is a system.  A system of software development that is designed to help software developers and software development teams produce high quality, high value code - and do this at the pace needed.  The system seeks to maximise developer creativity by maximizing developer freedom.  While also gaining the benifits that consistency can bring.

The system consists of:

- A set of patterns.  Patterrns are the heart of software development and the current cjlennon patterns are described below

- A sample application.  Named `cognify` this is a full, working piece of open-source software that shows these patterns in use.  This sample application is a responsive web app that allows the management of AWS Cognito users and includes reporting and synchronization with Gsuite (Gmail) contacts.  I am hoping to release this early 2018

Rather than simply presenting patterns in the abstract the sample applications shows (or at least _will_ when it is released) a real-world application of these patterns in use.  Enjoy!!

# A.  Essential patterns

## Pattern: Everything is a module

In the cjlennon system, everthing, from application functionality, deployment pipeline tooling, css styling and so on (i.e. _everything_) is encapsulated within a module.

A module is a unit of functionality that is designed to achieve a single purpose. A module needs to have the following characteristics:

- It is a single unit of functionality, i.e. it has a single purpose
- It accepts a single input (e.g. an event) and produces a single output
- It is self-contained.  That is it has no dependencies on other software other than what is fully contained in the repo.  The module may of course rely on other services, these dependencies need to be services interacted with over http
- It is documented
- It includes instructions and / or code that allows the user to host the module.  So the module sees the 'big picture' and enables end considers all aspects of the SDLC including production hosting and support (devops)

### In the sample application

In the sample application a module consists of one or more components.  These are the parts that fit together to create the module.  For example many cognify modules consist of an AWS Lambda function and an API Gateway endpoint

In the sample app modules are named as [developer name] - [project name] - [purpose]  For example `cjlennon-cognify-login`, `cjlennon-pipeline-update-lambda` and so on. 

## Pattern.  The cloud

By cloud we mean the commercial cloud, e.g. AWS, Google, Azure etc etc.  The cloud has past the 'tipping point' and is now an essential service.

## Pattern.  Serverless

Serverless computing enables the rapid application development and rapid deployment needed for cjlennon modules to add value

## Pattern. Amazon Web Services (AWS)

A common infrastructure is required to enable collaberation between module authors. Of course some of the patterns documented here may help you if you don't use AWS.

#  B.  Recommended Patterns

## nodejs

The primary reason you would use nodejs can be summed up in three letters: [npm](https://www.npmjs.com).  The wealth of functionality found in this vast collection is simply too powerful to ignore

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

When a problem or error is trapped by the module a single json log entry should be made, with the following structure:

**alertType**  (required).  One of `[Application Name]Alert`, `[Application Name]AlertUnexpected`, `[Application Name]AlertSecurity`

(so in the sample application: One of `cognifyAlert`, `cognifyAlertUnexpected`, `cognifyAlertSecurity`)

Where:
-  ‘cognify’ is the application name (and has been used here for the sake of clarity)
 -  `cognifyAlert` represents a specific error or problem case that has been trapped by the module.  
-  `cognifyAlertUnexpected` represents an unexpected error (for example this error type could be logged within the `catch` section of a `try / catch` code block)
-  `cognifyAlertSecuirty` is used for a specific problem case which may have security implications (for example a failed login attempt)

**module** (required):  The name of the module in which the problem occurred

**message** (required):  A summary of the error

**notify**.  Use this field to store information around whether / how to notify people about the error.  For example you could set up a set of values as : NONE | SLACK | EMAIL | SMS

**userMessage**.  If the error was reported back to the caller, this should be the exact text that was passed back to the user

**errorCode**.  A code associated with the error.

**context**.  Information that may be helpful when attempting to troubleshoot the error

**stackTrace**.  A full stack trace of the error

**lambdaEvent**.  For AWS Lambda functions, the source event

**lambdaContext**.  For AWS Lambda functions, the context object

#  C.  Suggested patterns

## Pattern - naming things

It is often said that it is more important to be consistant in the way you name things than to be 'right'.  Largely naming things is a matter of convention.  The following conventions are followed in the sample application:

- Folder names, file names and urls
  - name these as lower case with hypen.  So `my-folder-name`, `my-file-name.js`, `/check-user-auth`
- In code use camelCase.  So `let myModule = require('my-module.js')`.  Simply camelCase all words, so `htmlPage` not `HTMLPage` `myApi` not `myAPI` and so on.
- For database tables (including nosql databases such as AWS DynamoDB) and table field names use MixedCase.  So `Contacts`, `OrderLines` tables, `Id`, `FirstName` field names etc.  Don't be afraid to use plurals for table names, so `Sessions` rather than `Session`, `OrderLines` over `OrderLine` and so on
- Name commands eg. npm scripts as lower-case-with-hyphen.   Its one less key-stroke than the underscore
- Name environment variables as UPPERCASE_WITH_UNDERSCORE
- For everything else (and there is a lot else :-) )  use camelCase.  In the 'everything else' bucket would be JSON field names, HTML form fields, Swagger definitions, configuration such sas AWS API Gateway stage names, etc etc

## Pattern - nodejs code style

If you are developing in nodejs (the sample application is in nodejs) then:
- always `use strict`
- Validate your formatting with [standard](https://github.com/standard/standard)

## Pattern.  Use [semantic versioning](http://semver.org)

Why wouldn't you?
