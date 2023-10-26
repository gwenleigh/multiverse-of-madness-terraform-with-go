---
cover: ../.gitbook/assets/240 resource.png
coverY: 25.098666666666663
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

# 2 4 0 Custom Terraform Provider: Resource and CRUD (Go)



* This section walks through the Resource block and the logic flow of the CRUD functions in the `main.go` file in detail. As the code is quite long, each function is covered in its own subpage.
* [Video 2 4 0](https://www.youtube.com/watch?v=aeJCV-VIWiw\&list=PLBfufR7vyJJ4q5YCPl4o2XAzGRZUjuD-A\&index=55\&ab\_channel=ExamPro)

## Code Notes

* Andrew finishes the development of the `main.go` file by flashing out the internal logic of `Resource()` and the CRUD functions. In this episode onwards, we'll be able to actually use the features we developed here in other terraform files.&#x20;
* In this page, we will look into `Resource()` only.
* [The source code: main.go](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.4.0/terraform-provider-terratowns/main.go)

<pre class="language-go" data-line-numbers><code class="lang-go">package main

import (
	"bytes"
	"encoding/json"
	"net/http"
	...
)

func main() {...}
type Config struct {...}
func provider() {...}
func validateUUID(...) {...}
<strong>func providerConfigure(...) {...}
</strong>
func Resource() *schema.Resource {
	resource := &#x26;schema.Resource{
		CreateContext: resourceHouseCreate,
		ReadContext: resourceHouseRead,
		UpdateContext: resourceHouseUpdate,
		DeleteContext: resourceHouseDelete,
		Schema: map[string]*schema.Schema{
			"name": {
				Type: schema.TypeString,
				Required: true,
				Description: "Name of home",
			},
			"description": {
				Type: schema.TypeString,
				Required: true,
				Description: "Description of home",
			},
			"domain_name": {
				Type: schema.TypeString,
				Required: true,
				Description: "Domain name of home eg. *.cloudfront.net",
			},
			"town": {
				Type: schema.TypeString,
				Required: true,
				Description: "The town to which the home will belong to",
			},
			"content_version": {
				Type: schema.TypeInt,
				Required: true,
				Description: "The content version of the home",
			},
		},
	}
	return resource
}
</code></pre>

**Lines 23-50 Schema**

* **Line 16**: The `Resource()` function returns a pointer (\*) to the `schema.Resource` object.
  *   The `*` operator is used to access the value stored at a particular address.&#x20;

      * `pointer argument`: it points to the actual value. So if the value is updated, your functions are working with the updated value.&#x20;
      * `non-pointer argument`: it is a copy of the argument. The copy’s value will stay the same even after the argument’s current value is updated.



      <figure><img src="../.gitbook/assets/ampersand_and_asterisk (2).png" alt="" width="375"><figcaption></figcaption></figure>

      ###
* **Line 22**: inside the function, a variable `resource` is declared, which is `schema.Resource` object that has 4 CRUD functions and a map of Schema with 5 attributes.
  * These CRUD functions will allow us to make http requests to the API endpoints of the chosen cloud service. These requests contain instructions for provisioning cloud resources.
  * A map is a data type in Go which is equivalent to hash table, or a collection of key:value pairs.&#x20;
    * **Line 23-47**: each key comes with 3 attributes (`Type`, `Required`, `Description`) which define the properties of the value that can be assigned to the key. All the attributes are the string type (`schema.TypeString`) except for the key `content_version` (`schema.TypeInt`).

<figure><img src="../.gitbook/assets/240 resource (1).png" alt=""><figcaption></figcaption></figure>

### Resources

* Development workflow documentation: [2.4.0 CRUD Resource](https://medium.com/@gwenleigh/terraform-cloud-project-bootcamp-with-andrew-brown-2-4-0-crud-resource-80890f8c9604)
