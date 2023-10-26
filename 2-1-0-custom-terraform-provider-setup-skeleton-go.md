---
description: Andrew defines the Provider object in Go language.
cover: .gitbook/assets/210 provider skeleton.png
coverY: -94.60266666666666
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 2 1 0 Custom Terraform Provider: Setup Skeleton (Go)

Over the course of Week 2, Andrew develops the custom provider plugin in Golang. In this episode, we are setting up the skeleton to give shape to the provider. The Provider is instantiated with three attributes which are required to interact with the TerraTowns service.

* [Video 2 1 0 ](https://youtu.be/pU8-AOhrIV8)



## Code Notes

* A **provider** is a Terraform entity that interacts with a particular cloud infrastructure or service. In [Andrews' Bootcamp](https://www.exampro.co/ter-cpb-001), the provider will interact with the [TerraTowns](https://terratowns.cloud/) website using the AWS cloud services (`S3 bucket` and `CloudFront`).
* A **package** in go is [_a collection of source files in the same directory that are compiled together_](https://go.dev/doc/code).
* [The source code: main.go](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.1.0/terraform-provider-terratowns/main.go)

<pre class="language-go" data-line-numbers><code class="lang-go">package main

import (
	"log"
	"fmt"
	"github.com/hashicorp/terraform-plugin-sdk/v2/helper/schema"
	"github.com/hashicorp/terraform-plugin-sdk/v2/plugin"
)
 
func main() {
	plugin.Serve(&#x26;plugin.ServeOpts{
		ProviderFunc: Provider,
	})
}

<strong>func Provider() *schema.Provider {
</strong>	var p *schema.Provider
	p = &#x26;schema.Provider{
		ResourcesMap:  map[string]*schema.Resource{

		},
		DataSourcesMap:  map[string]*schema.Resource{

		},
		Schema: map[string]*schema.Schema{
			"endpoint": {
				Type: schema.TypeString,
				Required: true,
				Description: "The endpoint for hte external service",
			},
			"token": {
				Type: schema.TypeString,
				Sensitive: true, // make the token as sensitive to hide it the logs
				Required: true,
				Description: "Bearer token for authorization",
			},
			"user_uuid": {
				Type: schema.TypeString,
				Required: true,
				Description: "UUID for configuration",
				//ValidateFunc: validateUUID,
			},
		},
	}
	return p
}
</code></pre>

* `main.go`: a conventional filename used as the entry point of a Go application. The `main.go` file typically contains the main function, which is the starting point for the execution of the program. If you are familiar with Java, this is pretty much the Go equivalent of Java's `public static void main(String[] args)`.
* **Lines 13-15** inside `main()`: are the boilerplate code provided by Terraform for [creating a Terraform custom provider](https://www.hashicorp.com/blog/writing-custom-terraform-providers) plugin in Go. If it doesn't make sense to you, just know that it just has to be there, otherwise the Provider won't be created :P

{% code lineNumbers="true" %}
```go
func Provider() *schema.Provider {
    return &schema.Provider{
        ResourcesMap: map[string]*schema.Resource{},
    }
}
```
{% endcode %}

* **`ResourcesMap`**, **line 3**: the `ResourcesMap` block inside `&schema.Provider{}` will have to contain the blueprint of the resources the `Provider` can provision when instantiated. More on that later in [2.3.0](2-3-0-custom-terraform-provider-resource-skeleton-go.md).
* **`Schema`, lines 25-43**: is the blueprint of the `Provider()` entity. When declaring a provider in a `.tf` file, you will have to provide the values for the Provider's  attributes (configuration options). In our plugin, the Provider entity comes with three attributes: `endpoint`, `token`, and `user_uuid`. They are required when instantiating a provider in order for it to interact with [TerraTowns](https://terratowns.cloud/) (the cloud service).&#x20;
  * `endpoint`: terraform should know where the service is (the website address, or API in other words). The value for this will eventually be [TerraTowns](https://terratowns.cloud/)' domain name.&#x20;
  * Your credentials information
    * `token`: the token generated from ExamPro's vending machine :slot\_machine:. This token is unique to your account, so [TerraTowns](https://terratowns.cloud/) can authenticate your http request.
    * `user_uuid`: also unique to your account. In combination with the `token`,  [TerraTowns](https://terratowns.cloud/) authenticates you then processes your http request.
  *   `schema.Schema.attribute.Type`: defines the data type of the value you will provide for the attribute. They are categorised into two groups:&#x20;

      <table data-full-width="true"><thead><tr><th>Primitive types</th><th>Aggregate types</th></tr></thead><tbody><tr><td><p><code>TypeBool</code></p><p><code>TypeInt</code> </p><p><code>TypeFloat</code></p><p><code>TypeString</code></p><p><code>Date &#x26; Time data</code></p></td><td><p><code>TypeMap</code></p><p><code>TypeType</code></p><p><code>TypeSet</code></p></td></tr></tbody></table>

This marks the end of the tutorial, and also the completion of the Provider skeleton. It now does have a shape - an object with three attributes. As there is no actions (functions) defined, however, it can't really do anything. If we were to compare this to web development, it's like we just finished writing an html file without any backend functions. So we can view the page, but cannot interact with it.&#x20;

<div data-full-width="true">

<figure><img src=".gitbook/assets/210 provider skeleton (4).png" alt=""><figcaption></figcaption></figure>

</div>

### Resources

* Development workflow documentation: [2.1.0 Setup Skeleton for Custom Terraform Provider](https://medium.com/@gwenleigh/terraform-cloud-project-bootcamp-with-andrew-brown-2-1-0-cb7938b0d3b5)

#### Terraform

* [Writing Custom Terraform Providers](https://www.hashicorp.com/blog/writing-custom-terraform-providers)&#x20;
* [Schema Attributes and Types](https://developer.hashicorp.com/terraform/plugin/sdkv2/schemas/schema-types)

#### **Go**&#x20;

* [How to Write Go Code](https://go.dev/doc/code)
* [Schema package](https://pkg.go.dev/github.com/hashicorp/terraform/helper/schema#section-readme)
