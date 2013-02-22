#A Fork of Jacoco4sbt plugin.

This project is a fork of [Joachim Hofer's jacoco4sbt plugin](https://bitbucket.org/jmhofer/jacoco4sbt).
I created a fork because I wanted to add functionality to support code coverage for integration-testing and merging of unit-test and integration-test reports.

Read the [main site Wiki](https://bitbucket.org/jmhofer/jacoco4sbt/wiki/Home) on how to add the plugin to your project.
I hope Joachim wants to include these changes in his project. Otherwise just checkout and build this project and use this plugin in your project. See details below.

##Added the following functionality:
+ Supports coverage for integration testing. A new task has been added 'it-jacoco' which support al the same keys as 'jacoco'.
+ Supports merging of unittest coverage and integration-test coverage into a single coverage report. If 'it-jacoco' is run after 'jacoco' (and the execution data was saved) then it-jacoco will by default merge all data and create a single report.
+ Added 'clean' key to clean to JaCoCo output-directory.
+ Option to turn merging of reports off (is default enabled). Running 'merge' will always merge also when merge is disabled!

## Enabling integration-testing in SBT

Put this code in a Scala class file in your <project>/project directory to be able to run integration tests in SBT.

    import sbt._
    import Keys._

    object IntegrationTesting extends Build {

      lazy val root =
        Project("root", file("."))
          .configs( IntegrationTest )
          .settings( Defaults.itSettings : _* )

    }

You can run integration tests via:

    it:test

To disable running the integration tests parallel (which is default for running tests in SBT), add this line anywhere in your 'build.sbt'

    parallelExecution in IntegrationTest := false


## Usage of Jacoco Plugin

To run code coverage on your unittests, still run the plugin using 'jacoco:<key>' as before.
To run code coverage on your integration tests, use 'it-jacoco:<key>'

###To run all tests and save the execution data

    [it-]jacoco:check

###To create a report

    [it-]jacoco:report

###To run all tests and create a report

    [it-]jacoco:cover

>Note: You can stil run [it-]jacoco:test, but running [it-]jacoco:report after this will give an error because the execution data was not saved.
Basically, don't use 'test' but only the goals 'check' or 'cover' depending on whether you want to create a report directly or not.

###To run both unittest and integration-tests and create a combined report:

    jacoco:[check|cover]
    it-jacoco:cover
The report is generated in <project>/target/scala-<version>/it-jacoco/html.

###To *Not* merge the unittest and integration-test data, disable the merging of reports in the 'build.sbt'

    itJacoco.mergeReports in itJacoco.Config := false
With mergeReports set to 'false', it-jacoco will only include the integration-test data in the report.
>To include the unittest coverage again use 'it-jacoco:merge' before creating the report. This will always merge even when mergeReports is set to 'false'.

###To disable parallel execution for code coverage

Add this line to your 'build.sbt'

    parallelExecution in itJacoco.Config := false

###To clean the JaCoCo output-directory

    [it-]jacoco:clean

## Clone and build the project
    $ hg clone ssh://hg@bitbucket.org/diversit/jacoco4sbt
    $ cd jacoco4sbt
    $ sbt
    > + publish-local

In your project, update the plugin dependency to version '1.2.5a'
