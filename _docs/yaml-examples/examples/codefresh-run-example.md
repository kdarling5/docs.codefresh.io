---
title: "Calling a CD pipeline from a CI pipeline"
description: "Learn how to call children pipelines from a parent pipeline"
group: yaml-examples
sub_group: examples
toc: true
---

sdfsdf

 
{% include image.html 
lightbox="true" 
file="/images/examples/terraform/terraform-pipeline.png" 
url="/images/examples/terraform/terraform-pipeline.png" 
alt="Running Terraform inside Codefresh"
caption="Running Terraform inside Codefresh"
max-width="80%" 
%}

## The example Project

You can see the example project at [https://github.com/codefresh-contrib/terraform-sample-app](https://github.com/codefresh-contrib/terraform-sample-app). The repository contains a simple Terraform definition that creates a VM on Google cloud.

You can play with it locally after installing the `terraform` executable. 



## Create a CI/CD pipeline for Terraform

Here is the whole pipeline:

 `codefresh.yml`
{% highlight yaml %}
{% raw %}
version: '1.0'
stages:
  - checkout
  - prepare   
  - deploy
steps:
  main_clone:
    title: Cloning main repository...
    stage: checkout
    type: git-clone
    repo: 'codefresh-contrib/terraform-sample-app'
    revision: master
    git: github      
  SetupAuth:
    image: alpine:3.9
    title: Setting up Google cloud auth
    stage: prepare
    commands:
      - echo $ACCOUNT_JSON_CONTENT > /codefresh/volume/account.json
      - cf_export GOOGLE_CLOUD_KEYFILE_JSON=/codefresh/volume/account.json
  DeployWithTerraform:
    image: hashicorp/terraform:0.12.0
    title: Deploying Terraform plan
    stage: deploy
    commands:
      - terraform init
      - terraform apply -auto-approve 

{% endraw %}
{% endhighlight %}

This pipeline does the following:

1. Clones the source code with a [Git clone step]({{site.baseurl}}/docs/codefresh-yaml/steps/git-clone/)
1. Runs [cf_export]({{site.baseurl}}/docs/codefresh-yaml/variables/#exporting-environment-variables-from-a-freestyle-step) to create a pipeline variable with the path of the google service account
1. Runs `terraform init/apply` to create the VM on Google cloud.




## What to read next


* [Codefresh YAML]({{site.baseurl}}/docs/codefresh-yaml/what-is-the-codefresh-yaml/)
* [Pipeline steps]({{site.baseurl}}/docs/codefresh-yaml/steps/)
* [Creating pipelines]({{site.baseurl}}/docs/configure-ci-cd-pipeline/pipelines/)
* [Pipeline plugins](https://codefresh.io/steps/)