# Demo Project: Create a Jenkins Shared Library

## Overview
This project demonstrates how to create a Jenkins Shared Library (JSL) to extract and reuse common build logic across multiple pipelines. Jenkins Shared Libraries promote modularity, code reuse, and maintainability in CI/CD pipelines.

## Technologies Used
- **Jenkins**: To manage and integrate the shared library.
- **Groovy**: For scripting reusable logic in the shared library.
- **Docker**: To run Jenkins in a containerized environment.
- **Git**: For storing the shared library.
- **Java & Maven**: For the demo application used with the shared library.

---

## What is a Jenkins Shared Library?
A Jenkins Shared Library is a collection of reusable Groovy scripts that can be shared across multiple Jenkins pipelines. It allows you to:
1. Centralize commonly used pipeline logic.
2. Standardize workflows and reduce code duplication.
3. Make pipelines easier to maintain and update.

Shared libraries can be integrated into Jenkins globally or for specific projects, enabling flexibility in pipeline development.

---

## Project Description
### Goals
1. Create a separate Git repository for the Jenkins Shared Library.
2. Develop reusable functions in the shared library.
3. Integrate the shared library into Jenkins globally and in a specific project pipeline.

### Steps

### 1. Create a Git Repository for the Shared Library
1. Create a new Git repository (e.g., `jenkins-shared-library`).
2. Set up the required directory structure:
   ```
   (root)
   ├── vars
   │   └── exampleLib.groovy
   └── src
       └── org
           └── example
               └── utilities
                   └── Helper.groovy
   ```
3. Add an example script in `vars/exampleLib.groovy`:
   ```groovy
   def call(String name) {
       echo "Hello, ${name}! This is a shared library."
   }
   ```
4. Add a helper class in `src/org/example/utilities/Helper.groovy`:
   ```groovy
   package org.example.utilities

   class Helper {
       static String sayHello(String name) {
           return "Hello, ${name}! From Helper class."
       }
   }
   ```
5. Commit and push the changes:
   ```bash
   git init
   git add .
   git commit -m "Initial shared library structure"
   git remote add origin <your-repo-url>
   git push -u origin main
   ```

### 2. Configure the Shared Library in Jenkins
1. Go to **Manage Jenkins** > **Configure System**.
2. Scroll to **Global Pipeline Libraries** and click **Add**.
3. Provide the following details:
   - **Name**: `shared-library`
   - **Default Version**: `main` (or the default branch name).
   - **Retrieval Method**: Modern SCM.
   - **Source Code Management**: Git.
   - **Repository URL**: `<your-repo-url>`.
   - **Credentials**: Add if needed for private repositories.
4. Save the configuration.

### 3. Use the Shared Library in a Pipeline
#### Global Integration
1. Create a Jenkins pipeline and add the following in your `Jenkinsfile`:
   ```groovy
   @Library('shared-library') _

   pipeline {
       agent any
       stages {
           stage('Greet') {
               steps {
                   exampleLib('World')
               }
           }
           stage('Helper Example') {
               steps {
                   script {
                       def greeting = org.example.utilities.Helper.sayHello('World')
                       echo greeting
                   }
               }
           }
       }
   }
   ```

#### Project-Specific Integration
1. Clone the shared library into a subdirectory in the project repository (optional).
2. Reference the local path in the `Jenkinsfile`:
   ```groovy
   library identifier: 'shared-library@main', retriever: legacySCM(scm: scm)

   pipeline {
       agent any
       stages {
           stage('Greet') {
               steps {
                   exampleLib('Project')
               }
           }
       }
   }
   ```

### 4. Test the Shared Library
1. Trigger the pipeline in Jenkins.
2. Verify that:
   - The `exampleLib` function prints the greeting message.
   - The `Helper` class method works as expected.

---

## Best Practices
1. Use meaningful names for shared library functions and classes.
2. Version-control the library carefully to avoid breaking changes in pipelines.
3. Document the library functions for easier usage by pipeline developers.
4. Write unit tests for shared library code using tools like the [Pipeline Unit Testing Framework](https://github.com/jenkinsci/JenkinsPipelineUnit).

---

## Conclusion
This project demonstrates how to create and use Jenkins Shared Libraries for efficient CI/CD pipeline management. By centralizing reusable code, you can streamline your workflows, reduce redundancy, and improve maintainability.
