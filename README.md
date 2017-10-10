# cjlennon

## What is cjlennon?

cjlennon is (apart from being my name - yes thats me - Chris Lennon!) is a system.  A system of software development that is designed to help software developers and software development teams produce high quality, high value code - and do this at the pace needed.  The system seeks to maximise value to the customer by maximizing developer creativity by maximizing developer freedom.  While also gaining the benifits that consistency can bring.

The system consists of:

- A set of patterns.  Patterrns are the heart of software development and the current cjlennon patterns are described below

- A sample application.  Named `cognify` this is a full, working piece of open-source software that shows these patterns in use.  This sample application is a responsive web app that allows the management of AWS Cognito users and includes reporting and synchronization with Gsuite (Gmail) contacts.  I am hoping to release this early 2018

Rather than simply presenting patterns in the abstract the sample applications shows (or at least _will_ when it is released) a real-world application of these patterns in use.  Enjoy!!

# A.  Foundation patterns

## Pattern: Everything is a module

In the cjlennon system, everthing, from application functionality, deployment pipeline tooling, css styling and so on (i.e. _everything_) is encapsulated within a module.

A module is a unit of functionality that is designed to achieve a single purpose. A module needs to have the following characteristics:

- It is a single unit of functionality, i.e. it has a single purpose
- It accepts a single input (e.g. an event) and results in a single outcome (e.g. a component is rendered, a web page is rendered, a Lambda function is updated, an email is sent and so on)
- It is self-contained.  That is the module has no dependencies on software other than what is fully contained in the repo (after install).  The module may of course rely on other services, these dependencies need to be services interacted with over http(s)
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

## Pattern.  Global (per project) css styling

For a given project (project in the sense of a collaberative endevour) use a single stylesheet, or set of stylesheets that give the styling for all pages / components within the project.

This does not break the 'everything is a module' pattern because the styling project (which could for example contain LESS files, compiled css and images) is itself a module.

It does mean that some modules, namely those that display content, rely on this styling module to render correctly.  The alternative would be to have each module be responsible for its own styling.  This is a perfectly acceptable pattern (indeed to some extent a better pattern from a purist point of view); however having a global styling module was chosen for a few reasons:

-  Most projects require a consistant styling.  We don't want the user confused by having to come to terms with different look and feel for different pages within the same application.  A global css file or files will help ensure consistancy
-  Even if you want components responsible for their own styling in terms of layout, the color palette is almost always global to an application.  So even if the style is contained within the module, there will need to be at least a dependency on a set of global color variables
-  In practical terms graphic design is a skillset that may well not reside within the developer / team authoring the module.  Separating layout from functionality in this way enables the styling work to be done by a team with the graphic design skills required
-  A global stylesheet enables component re-use.  Components such as dynamic tables, multi-select boxes etc can be rendered in a simple and consistant way

### In the sample application

The styling for Cognify is powered by the `cjlennon-cognify-global-styles` project, which itself sits on top of a slightly modified AdminLTE stylesheet, which sits on top of bootstrap.  You can find the Cognify styles project and the modified AdminLTE project in this cjlennon github project

## Pattern: responding to problems

As we have said, a module is a unit of functionality, designed to accomplish a specific task.  Because of this we can say that there are only two outcomes for a module once it is triggered by an event:

1.	 The module completes successfully
2.	The module does not complete successfully

Of course this is very much a simplification, however it is a helpful concept when it comes to error handling, as we can establish a rule as follows:

#### Rule:  Whenever your module fails to complete successfully, an alert must be logged by the module.  That is, the problem preventing the module from completing successfully should be trapped by the module, and logged.

‘Completing successfully’ can be defined as ‘the user gets the result they would expect’.  This is analogous to the ‘happy path’.  Another way of explaining this concept:  the case where a module fails to complete, and no error message is logged by the module itself, should never happen.

As an example, let's take an application with a front end that validates user input, and an associated API which also validates user input.  This is good practice and ensures there is an extra layer of validation and security beyond the front end.  If the API receives user input which is not valid it will correctly reject the request.  All is working as designed, however from a user point of view, the module has not completed successfully.  The fact that invalid input was supplied to the API is therefore a problem which must be handled and logged by the API module.

Of course not all problems are of equal severity and it is helpful to have a way of categorising the severity of problems.  It is also helpful to have problems logged in a standard way, this enables you to set up consistent monitoring and alerting rules.  For these two reasons the following is recommended as a specification for logging errors, see the 'cjlennon error logging pattern' later in this document for a possible error logging specification you may want to consider.

#  B.  Suggested Patterns

## nodejs

The primary reason you would use nodejs can be summed up in three letters: [npm](https://www.npmjs.com).  The wealth of functionality found in this vast collection is simply too powerful to ignore

## Pattern: Use a common error logging specification

Not all problems are of equal severity and it is helpful to have a consistant way of categorising the severity of problems.  It is also helpful to have problems logged in a standard way as this enables you to set up consistent monitoring and alerting rules.  Below a suggested specification for logging errors:

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

#  C.  Possible patterns

The below patterns are under consideration

## Pattern.  Don't use a front end framework

## Pattern.  Rest APIs

## Pattern.  Common database

NoSQL??  DynamoDB


