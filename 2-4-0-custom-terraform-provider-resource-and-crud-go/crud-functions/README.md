---
cover: ../../.gitbook/assets/CRUD - all four.png
coverY: 0
layout:
  cover:
    visible: true
    size: full
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

# CRUD Functions

As we move on to 2 5 0 and 2 6 0, we will see the CRUD functions in action. One interesting thing to note is, though, that **the CRUDs are **<mark style="color:red;">**NOT**</mark>** explicitly called anywhere in our project code** (no function calls in `main.tf`). This is because these CRUDs are defined for the <mark style="color:purple;">**Terraform SDK**</mark>. They are invoked automatically by Terraform based on the configurations specified in our terraform configuration files (`main.tf`).

* <mark style="color:purple;">**SDK**</mark>...? 🤨 : Terraform SDK (Software Development Kit) is the set of libraries and tools provided by HashiCorp for developing custom Terraform providers.
* This <mark style="color:purple;">**SDK**</mark> provides a plugin framework which allows us to build custom plugins ([providers](../../2-3-0-custom-terraform-provider-resource-skeleton-go.md) and provisioners) which is loaded by Terraform. Using the <mark style="color:purple;">**SDK**</mark>, we can create **custom resource types** and execute **custom actions** during Terraform's lifecycle.
  * **custom resource type**: `terrahouse`
  * **custom actions**: the CRUD functions

<div data-full-width="true">

<figure><img src="../../.gitbook/assets/CRUD - all four (1).png" alt=""><figcaption></figcaption></figure>

</div>

### Resources

* [Wikipedia - CRUD](https://en.wikipedia.org/wiki/Create,\_read,\_update\_and\_delete)
