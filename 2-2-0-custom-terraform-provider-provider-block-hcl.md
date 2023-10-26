---
cover: .gitbook/assets/220 required_providers provider (long).png
coverY: -7.722666666666666
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 2 2 0 Custom Terraform Provider: Provider Block (HCL)

As the Provider does not have any CRUD functions defined, it doesn't do anything. Andrew tests the mechanism of the skeleton plugin by building the Go binary and running `terraform init` and `terraform apply` - as terraform executes the Go binary, the `init` and `apply` commands should run successfully and it means the plugin is working.&#x20;

* [Video 2 2 0](https://www.youtube.com/watch?v=PivvxGseOwk\&list=PLBfufR7vyJJ4q5YCPl4o2XAzGRZUjuD-A\&index=53\&ab\_channel=ExamPro)



## Code Notes

* As we defined a custom provider, we can now declare it in a terraform file.&#x20;
* [The source code: main.tf](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.2.0/main.tf)

{% code title="main.tf" %}
```hcl
terraform {
  required_providers {
    terratowns = {
      source = "local.providers/local/terratowns"
      version = "1.0.0"
    }
  }

provider "terratowns" {
  endpoint = "http://localhost:4567"
  user_uuid="e328f4ab-b99f-421c-84c9-4ccea042c7d1" 
  token="9b49b3fb-b8e9-483c-b703-97ba88eef8e0"
}
```
{% endcode %}

### Difference between `required_providers` & `provider`

1. #### `required_providers`: for the entire **configuration**

* It is a top-level block that sits inside the `terraform{}` block in the Terraform configuration.
* It is used to specify the providers that are required for the **entire configuration**.
* If you are familiar with Node.js/React, you can think of `package.json`. They are not the exact equivalents of each other, but share a common point - both of them list out the dependencies required for a project.&#x20;
* Example: let's say that we are going to use all the three major cloud services (AWS, Azure, and GCP) to deploy our terratowns houses to [TerraTowns](https://terratowns.cloud/). Then the required providers will look like:&#x20;

{% code title="imaginary_main.tf" %}
```hcl
terraform {
  required_providers {
    terratowns = {                                   # our custom plugin 
      source = "local.providers/local/terratowns"
      version = "1.0.0"
    }
    aws = {
      source  = "hashicorp/aws"
      version = ">= 2.0"
    }
    azure = {
      source  = "hashicorp/azurerm"
      version = "= 2.0"
    }
    google = {
      source  = "hashicorp/google"
      version = ">= 3.0"
    }
  }
}
```
{% endcode %}

2. `provider`: **instantiation** of the `required_provider`

* The `provider` block is typically used within `resource` or `data` blocks to create an instance of that `provider`.&#x20;
* A `provider` specifies the configuration for managing particular resource(s) or data source.
* Below example is an instantiation of the `required_provider` `terratowns` listed above.

{% code title="imaginary_homes.tf" %}
```hcl
provider "terratowns" {
  endpoint = var.terratowns_endpoint
  user_uuid = var.teacherseat_user_uuid
  token = var.terratowns_access_token
}

// See how these resources are using the required_providers.
resource "terratowns_home" "home_arcanum" {        //   Deploying to TerraTowns
  name = "How to play Arcanum in 2023!"
  town = "missingo"
  content_version = var.arcanum.content_version
}

resource "aws_home" "home_amazon" {                //   Deploying to aws
  name = "How to play Amazon in 2023!"
  town = "missingo"
  content_version = var.arcanum.content_version
}

resource "azure_home" "home_microsoft" {           //   Deploying to azure
  name = "How to play Microsoft in 2023!"
  town = "missingo"
  content_version = var.arcanum.content_version
}
```
{% endcode %}





<figure><img src=".gitbook/assets/220 required_providers provider.png" alt=""><figcaption><p>Does the image make sense to you now?</p></figcaption></figure>

### Resources

* Development workflow documentation: [2.2.0 Provider Block for Custom Terraform Provider](https://medium.com/@gwenleigh/terraform-cloud-project-bootcamp-with-andrew-brown-2-2-0-a104a17eca9e)
