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

A module is a unit of functionality that is designed to achieve a single purpose. A module:

- is a single unit of functionality, i.e. it has a single purpose
- accepts a single input (e.g. an event) and results in a single outcome (e.g. a component is rendered, a web page is rendered, a Lambda function is updated, an email is sent and so on)
- is self-contained.  That is the module has no dependencies on software other than what is fully contained in the repo (after install).  Because 'no module is an island' modules will generally rely on other services, these dependencies need to be services interacted with over http(s)
- is documented
- resides in a single code repository (e.g. a single github repo)
- includes instructions and / or code that allows the user to host the module.  So the module sees the 'big picture' and enables end considers all aspects of the SDLC including production hosting and support (devops)
- is responsible for handling  / logging problems encountered (see the 'responding to problems' section later)
- is responsible for handling its own state.  Persisting to the database as needed, the module must support a stateless architecture

### In the sample application

In the sample application a module consists of one or more components.  These are the parts that fit together to create the module.  For example many cognify modules consist of an AWS Lambda function and an API Gateway endpoint

In the sample app modules are named as [developer name] - [project name] - [purpose]  For example `cjlennon-cognify-login`, `cjlennon-pipeline-update-lambda` and so on. 

## Pattern. Amazon Web Services (AWS)

A common infrastructure is required to enable sufficient collaberation between module authors. Of course some of the patterns documented here may help you if you don't use AWS.

## Pattern.  Global (per project) css styling

For a given project (project in the sense of a collaberative endevour) use a single stylesheet, or set of stylesheets that control the styling for all pages and components within the project.

This does not break the 'everything is a module' pattern because the styling project (which could for example contain LESS files, compiled css and images) is itself a module.

This does mean that some modules, namely those that display content, rely on this styling module to render correctly.  The alternative would be to have each module be responsible for its own styling.  This is a perfectly acceptable pattern (indeed to some extent a better pattern from a purist point of view); however having a global styling module was chosen as the pattern for a few reasons:

-  Most projects require a consistant styling.  We don't want the user confused by having to come to terms with different look and feel for different pages within the same application.  A global css file or files will help ensure consistancy
-  The color palette is almost always global to an application.  So even if the style is contained within the module, there will need to be at least a dependency on a set of global colors.  The point being that some sharing of style is very hard to avoid.
- Globalising a style sheet enables the styling to be changed and improved, without the need to change the modules themselves
-  A global stylesheet enables component re-use.  Components such as dynamic tables, multi-select boxes etc can be rendered in a simple and consistant way
-  In practical terms graphic design is a skillset that may well not reside within the developer / team authoring the module.  Separating layout from functionality in this way enables the styling work to be done by a team with the graphic design skills required

### In the sample application

The styling for Cognify is powered by the `cjlennon-cognify-global-styles` project, which itself sits on top of a slightly modified AdminLTE stylesheet, which sits on top of bootstrap.  You can find the Cognify styles project and the modified AdminLTE project in this cjlennon github project

## Pattern: responding to problems

As we have said, a module is a unit of functionality, designed to accomplish a specific task.  Because of this we can say that there are only two outcomes for a module once it is triggered by an event:

1.	 The module completes successfully
2.	The module does not complete successfully

Of course this is very much a simplification, however it is a helpful concept when it comes to error handling, as we can establish a rule as follows:

#### Rule:  Whenever your module fails to complete successfully, an alert must be logged by the module.  That is, the problem preventing the module from completing successfully should be trapped by the module, and logged.

‘Completing successfully’ can be defined as ‘the user gets the result they would expect’.  This is analogous to the ‘happy path’.  Another way of explaining this concept:  the case where a module fails to complete, and no error message is logged by the module itself, should never happen.

As an example, let's take an application with a front end that validates user input, and an associated API which also validates user input.  This is good practice and ensures there is an extra layer of validation and security beyond the front end.  If the API receives user input which is not valid it will correctly reject the request.  All is working as designed, however from a user point of view, the module has not completed successfully.  The fact that invalid input was supplied to the API is therefore a problem which must be handled and logged by the API component.

Of course not all problems are of equal severity and it is helpful to have a way of categorising the severity of problems.  It is also helpful to have problems logged in a standard way as this enables you to set up consistent monitoring and alerting rules.  For these two reasons this guide includes an error logging specification -  see the 'cjlennon error logging pattern' later in this document for an error logging specification you may want to consider.

#  B.  Suggested Patterns

## nodejs

The primary reason you would use nodejs can be summed up in three letters: [npm](https://www.npmjs.com).  There is a wealth of functionality found in this vast collection and when rapid application development is the goal, nodejs is hard to ignore

## Pattern: Use a common error logging specification

As stated, not all problems are of equal severity and it is helpful to have a consistant way of categorising the severity of problems.  It is also helpful to have problems logged in a standard way as this enables you to set up consistent monitoring and alerting rules.  Below a suggested specification for logging errors:

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

**notify**.  Use this field to store information around whether / how to notify people who would want to be informed of the error.  For example you could set up a set of values for this field as : NONE | SLACK | EMAIL | SMS

**userMessage**.  If the error was reported back to the caller, this should be the exact text that was passed back to the user

**errorCode**.  A code associated with the error.

**context**.  Information that may be helpful when attempting to troubleshoot the error

**stackTrace**.  A full stack trace of the error

**lambdaEvent**.  For AWS Lambda functions, the source event

**lambdaContext**.  For AWS Lambda functions, the context object


## Pattern - naming things

It is often said that it is more important to be consistant in the way you name things than to be 'right'.  Naming things is largely a matter of convention.  The following conventions are followed in the sample application:

- Folder names, file names and urls are named as lowercase-with-hypen.  So `my-folder-name`, `my-file-name.js`, `/check-user-auth`
- In code use camelCase.  So `let myModule = require('my-module.js')`.  Simply camelCase all words, so `htmlPage` not `HTMLPage` `myApi` not `myAPI` and so on.
- For database tables (including Nosql databases such as AWS DynamoDB) and table field names use MixedCase.  So `Contacts`, `OrderLines` tables, `Id`, `FirstName` field names etc.  Use plurals for table names, so `Sessions` rather than `Session`, `OrderLines` over `OrderLine` and so on
- Name commands e.g. npm scripts as lower-case-with-hyphen.   Its one less key-stroke than the underscore
- Name environment variables as UPPERCASE_WITH_UNDERSCORE
- For everything else (and there is a lot else :-) )  use camelCase.  In the 'everything else' bucket would be JSON field names, HTML form fields, Swagger definitions, configuration such sas AWS API Gateway stage names, etc etc

## Pattern - nodejs code style

If you are developing in nodejs (the sample application is in nodejs) then:
- always `use strict`
- Validate your formatting with [standard](https://github.com/standard/standard)

## Pattern.  Semantic versioning

Begin your component at version 0.1.0 and use versions less than 1.0.0 to represent a version that may release breaking changes at any time.

See [semantic versioning](http://semver.org) for more information

#  C.  Possible patterns

The below patterns are under consideration:

## Pattern.  Don't specify a front end framework

In a modularised architecture, the developer(s) of a module is free to architect the module as they wish - provided of course that the patterns the team have agreed upon are used.  For this reason it is possible to have modules use differing front end frameworks - for example within a single application the login component could use jquery, the dashboard page use angular, a reporting page use react, and so on.  So long as the user receives a consistent user experience, then nothing prevents this. 

Of course for reasons of knowledge sharing, performance (you may not wish to have the browser load jquery _and_ angular _and_ react) or a number of other reasons, this approach may not be desired, and you may wish to use a common front-end framework.  No particular front-end framework is suggested in this document.

### In the sample application

No front end framework or templating engine is used in the sample application.  Or to put this another way, the Cognify uses its own rendering system.  Pages are created by using nodejs code and the values needed at run time are injected when the page or component is rendered (using `string.replace()`)

The html pages and components use a number of open-source components many of which are built on top of jquery

## Pattern.  Interacting with the database

On some occasions a module may be able to use its own self-contained database, which is ideal from a modularisation point of view.  However this will be the exception rather than the rule.  Consider a 'Sessions' table, an 'Orders' table, 'Contacts' and so on.  These data tables will need to be shared accross a number of components.

A common database is also desirable from a reporting and business intelligence point of view.

How then to keep access to a common database isolated within module boundaries?

The answer is possibly that database access should be via API modules?  Reset with Swagger?  GraphQL?



