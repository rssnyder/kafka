# Build and Test Apache Kafka on Harness CI

This is a fork of Apache Kafka project. This project was used to demo the new capabilities of Harness CI at Unscripted conference.
This file contains instructions on how to run the Apache Kafka pipeline shown during the unscripted conference demo on Harness CI.


# Setting up this pipeline on Harness CI
 1. Create a [GitHub Account](https://github.com) or use an existing one
 2. Fork [this repository](https://github.com/harness-community/kafka/fork) into your GitHub account. 
 3. If you are new to Harness, signup for [Harness CI](https://app.harness.io/auth/#/signup)
 4. Select the `Continuous Integration` module and choose the `Starter pipeline` wizard to create your first pipeline using the forked repo from #2.
 5. Go to the newly created pipeline and hit the `Triggers`tab. If everything went well, you should see two triggers auto-created. A `Pull Request`trigger and a `Push`trigger. For this exercise, we only need `Pull Request`trigger to be enabled. So, please disable or delete the `Push`trigger.
 6. Update the starter pipeline YAML's step as follows:
```
                  - step:
                    type: RunTests
                    name: Run Tests with Intelligence
                    identifier: Run_Tests_with_Intelligence
                    spec:
                      language: Java
                      buildTool: Gradle
                      args: "--build-cache unitTest -PmaxParallelForks=32 -PignoreFailures=true"
                      packages: org.apache.kafka,kafka.test,kafka.testkit,kafka.server,kafka.tools,kafka.examples,test.plugins
                      runOnlySelectedTests: true
                      preCommand: ls
                      reports:
                        type: JUnit
                        spec:
                          paths:
                            - "**/*.xml"
```
7. Create a Pull Request in a new branch by updating the `build.gradle`file. (e.g. add a comment or new line)
8. The Harness pipeline kicks off a webhook execution due to the enabled `Pull Request` trigger. This `build.gradle` file is special and so Harness Test Intelligence selects all the available tests in the repository to run and stores their relationships with source files.
9. Merge the PR after the pipeline runs to completion.
10. The repository forked in Step 2 already has a `GitHub Actions` workflow file added. You can choose to enable this workflow from the `Actions` tab on GitHub.
11. Create any other `Pull Request` with a few source or test file changes. You can consider cherry-picking any of the commits from the `Apache Kafka` repo such as [this one](https://github.com/harness-community/kafka/pull/2)
12. Step-11 will trigger the Harness pipeline (as well as GitHub Actions workflow if enabled in Step-10). Depending on what files got changed in the PR, only those tests related to the changed source/test code will be selected to run in the Harness pipeline. GitHub Actions workflow, however, will run all the unit tests for every PR. For the most real-world `Pull Requests` the Harness pipeline execution is faster than GitHub Actions.
