# Commands Manual
The goal of this doc is to present the commands needed when you will develop your own operator.

As for now I am pasting commands executed during cronjob-tutorial.

## Commands

### Initialize a project
```sh
kubebuilder init --domain tutorial.kubebuilder.io --repo tutorial.kubebuilder.io/project
```

**domain** - is used as the base to generate FQNDs fot the CRDs that your project will Create. For instance if you use domain `pdmgr.0x41gawor.com` and later create a CRD named `Loop`. The generated CRD will habe an API group of `loop.pdmgr.0x41gawor.com`. This is useful in avoiding naming colisions with CRD defined by other projects. Note that: The structure of the domain follows the reverse domain name notation, which is a common practice in software development to create unique identifiers based on domain names that you control. 

**repo** -  specifies the name of the repository for your project. This is used internally by Kubebuilder for several purposes, including the generation of Go module paths and as a reference for where the project's source code is or will be located. Essentially, it sets the base path for the Go module of your project, aligning it with the expected location in a version control system like GitHub, GitLab, or others.

It's important to structure your --repo argument to reflect your actual or intended repository URL or path, especially if you plan to push your project to a version control system. This consistency helps in avoiding issues with Go modules and package imports, especially when other developers are working with your code or when deploying your project across different environments

## Create API GVK

```sh
kubebuilder create api --group batch --version v1 --kind CronJob
```