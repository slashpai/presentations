+++
title = "Jenkins in Action"
outputs = ["Reveal"]
[reveal_hugo]
theme = "league"
margin = 0.2
+++

## Jenkins in Action

Let's learn Jenkins with handson

---

## Agenda

- Why Jenkins?

- Jenkins Plugins

- Jenkins Pipelines

- Managing Jenkins

---

## What is Jenkins?

- {{% fragment %}} Self-contained, open source automation server which can be used to automate all sorts of tasks related to building, testing, and delivering or deploying software {{% /fragment %}}

---

## Install and Configure Jenkins

- {{% fragment %}} Jenkins can be installed through native system packages, Docker, or even run standalone by any machine with a Java Runtime Environment (JRE) installed {{% /fragment %}}

---

## Using docker

```go
 docker run -u 0 --privileged -d  -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home  -v $(which docker):/usr/bin/docker -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins:lts
```

- Note extra volumes added for docker to use host docker daemon this is done for the sake of demo, in production it will run on agents on which docker is installed

---

Go to http://localhost:8080

---

## Let's explore Jenkins environment

---

## Plugins

---

- Pipeline Plugins
- Docker, Docker Pipeline
- BlueOcean
- lot more

---

## Jenkins Agents

---

- {{% fragment %}} Jenkins architecture is designed for distributed build environments {{% /fragment %}}
{{% note %}} Allows to use different environments for each build project balancing the workload among multiple agents running jobs in parallel {{% /note %}}

---

- {{% fragment %}} Jenkins controller administers the Jenkins agents and orchestrates their work, including scheduling jobs on agents and monitoring agents {{% /fragment %}}

- {{% fragment %}} Agents may be connected to the Jenkins controller using either local or cloud computers {{% /fragment %}}

- {{% fragment %}} Jenkins agents can be launched in physical machines, virtual machines, Kubernetes clusters, and with Docker images {{% /fragment %}}

---

## Sample Job Run

---

## Jenkins Pipeline

---

- {{% fragment %}} Jenkins Pipeline is a suite of plugins which supports implementing and integrating continuous delivery pipelines into Jenkins {{% /fragment %}}
- {{% fragment %}} Pipeline provides an extensible set of tools for modeling simple-to-complex delivery pipelines "as code" via the Pipeline DSL syntax {{% /fragment %}}

---

- {{% fragment %}} Jenkins Pipeline definition is written into a text file (called a Jenkinsfile) which in turn can be committed to a project’s source control repository{{% /fragment %}}

- {{% fragment %}} Foundation of "Pipeline-as-code"; treating the CD pipeline a part of the application to be versioned and reviewed like any other code {{% /fragment %}}

---

## Jenkinsfile Benefits

---

- {{% fragment %}} Automatically creates a Pipeline build process for all branches and pull requests {{% /fragment %}}

- {{% fragment %}} Code review/iteration on the Pipeline (along with the remaining source code) {{% /fragment %}}

- {{% fragment %}} Audit trail for the Pipeline {{% /fragment %}}

- {{% fragment %}} Single source of truth for the Pipeline, which can be viewed and edited by multiple members of the project {{% /fragment %}}

---

- {{% fragment %}} While the syntax for defining a Pipeline, either in the web UI or with a Jenkinsfile is the same, it is generally considered best practice to define the Pipeline in a Jenkinsfile and check that in to source control {{% /fragment %}}

---

## Declarative versus Scripted Pipeline

---

- {{% fragment %}} A Jenkinsfile can be written using two types of syntax - Declarative and Scripted {{% /fragment %}}

- {{% fragment %}} Declarative and Scripted Pipelines are constructed fundamentally differently {{% /fragment %}}

---

- Declarative Pipeline

- {{% fragment %}} Declarative Pipeline is a more recent feature of Jenkins Pipeline which: {{% /fragment %}}

  - {{% fragment %}} provides richer syntactical features over Scripted Pipeline syntax, and {{% /fragment %}}

  - {{% fragment %}} is designed to make writing and reading Pipeline code easier {{% /fragment %}}

---

- Scripted Pipeline

- {{% fragment %}} Like Declarative Pipeline, is built on top of the underlying Pipeline sub-system {{% /fragment %}}

- {{% fragment %}} Unlike Declarative, Scripted Pipeline is effectively a general-purpose DSL built with Groovy {{% /fragment %}}

- {{% fragment %}} Most functionality provided by the Groovy language is made available to users of Scripted Pipeline, which means it can be a very expressive and flexible tool {{% /fragment %}}

---

## Explore Jenkins Pipeline

---

- Simple Pipeline

```groovy
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

---

- Docker as Agent

```groovy
pipeline {
    agent { docker { image 'ruby:2.6.6-alpine' }}
    stages {
        stage('Version check') {
            steps {
                sh 'ruby --version'
            }
        }
    }
}
```

---

## Using a image built for specific purpose

---

Use contents from this folder for building image

cd to calculator_as_interpreter in
https://github.com/slashpai/docker_k8s_training/tree/main/docker_apps

---

```groovy
pipeline {
    agent { docker { image 'calculator_interpreter:v1' }}
    stages {
        stage('Sum 2 + 3') {
            steps {
                sh 'ruby /app/calculator.rb 2 + 3'
            }
        }

        stage('Diff 2 - 3') {
            steps {
                sh 'ruby /app/calculator.rb 2 - 3'
            }
        }
    }
}
```

---

- Parameters in Pipeline

```groovy
pipeline {
    agent any
    parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')

        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }
    stages {
        stage('Example') {
            steps {
                echo "Hello ${params.PERSON}"

                echo "Biography: ${params.BIOGRAPHY}"

                echo "Toggle: ${params.TOGGLE}"

                echo "Choice: ${params.CHOICE}"

                echo "Password: ${params.PASSWORD}"
            }
        }
    }
}
```

---

## Parallel Stages

---

```groovy
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Parallel Stages') {
            parallel {
                stage('parallel 1') {
                    steps {
                        echo 'Hello from Parallel Stage 1'
                    }
                }
                stage('parallel 2') {
                    steps {
                        echo 'Hello from Parallel Stage 2'
                    }
                }
            }
        }
    }
}
```

---

More options: https://www.jenkins.io/doc/book/pipeline/syntax/

---

## Pipleline Examples

https://github.com/jenkinsci/pipeline-examples

---

## BlueOcean Dashboard

---

## Credentials

- {{% fragment %}} There are numerous 3rd-party sites and applications that can interact with Jenkins, for example, artifact repositories, cloud-based storage systems and services, and so on {{% /fragment %}}

---

- {{% fragment %}} A systems administrator of such an application can configure credentials in the application for dedicated use by Jenkins {{% /fragment %}}
- {{% fragment %}} Once a Jenkins manager (i.e. a Jenkins user who administers a Jenkins site) adds/configures these credentials in Jenkins, the credentials can be used by Pipeline projects to interact with these 3rd party applications {{% /fragment %}}

---

## Helpful Plugins for System admin

---

https://github.com/jenkinsci/ssh-steps-plugin

---

## Managing Jenkins

---

## Script Console

---
Usecase: https://support.cloudbees.com/hc/en-us/articles/360051376772-How-can-I-purge-clean-the-build-queue-?page=3

```groovy
Jenkins.instance.queue.clear()
```

---

## Helpful scripts

https://github.com/cloudbees/jenkins-scripts

---

## References

- https://www.jenkins.io/doc
- https://www.jenkins.io/doc/book/pipeline
- https://www.jenkins.io/doc/book/blueocean

---

## Training Summary

- Why Jenkins?

- Jenkins Pipelines

- Helpful plugins

- Managing Jenkins

---

{{< slide content="common.thankyou" >}}
