---
cover: .gitbook/assets/230 gopher in a jar - resource skeleton (long).png
coverY: -9.653333333333332
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

# 2 3 0 Custom Terraform Provider: Resource Skeleton (Go)

In this episode, Andrew defines `validateUUID()` and `providerConfigure()` functions, and sets up the skeleton of `resource()`.&#x20;

* [Video 2 3 0](https://www.youtube.com/watch?v=\_QBTP0SyGtQ\&t=389s\&ab\_channel=ExamPro)

## Code Notes

* **schema.**[**Resource**](https://pkg.go.dev/github.com/hashicorp/terraform/helper/schema#Resource): `Resource()` represents a thing (entity) in Terraform that has a set of configurable attributes and a lifecycle (create, read, update and delete).
* [The source code: main.go](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.3.0/terraform-provider-terratowns/main.go)

<pre class="language-go" data-line-numbers><code class="lang-go"><strong>package main
</strong>
import (
	"context"
	"log"
	"fmt"
<strong>	"github.com/google/uuid"
</strong>	"github.com/hashicorp/terraform-plugin-sdk/v2/diag"
	"github.com/hashicorp/terraform-plugin-sdk/v2/helper/schema"
	"github.com/hashicorp/terraform-plugin-sdk/v2/plugin"
)

func main() {
	plugin.Serve(&#x26;plugin.ServeOpts{
		ProviderFunc: Provider,
	})
	fmt.Println("Hello, world!")
}

type Config struct {
	Endpoint string
	Token string
	UserUuid string
}

func Provider() *schema.Provider {
	var p *schema.Provider
	p = &#x26;schema.Provider{
		ResourcesMap:  map[string]*schema.Resource{
			"terratowns_home": Resource(),
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
				ValidateFunc: validateUUID,
			},
		},
	}
	p.ConfigureContextFunc = providerConfigure(p)
	return p
}

func validateUUID(v interface{}, k string) (ws []string, errors []error) {
	log.Print("validateUUID:start")
	value := v.(string)
	if _, err := uuid.Parse(value); err != nil {
		errors = append(errors, fmt.Errorf("invalid UUID format"))
	}
	log.Print("validateUUID:end")
	return
}

func providerConfigure(p *schema.Provider) schema.ConfigureContextFunc {
	return func(ctx context.Context, d *schema.ResourceData) (interface{}, diag.Diagnostics ) {
		log.Print("providerConfigure:start")
		config := Config{
			Endpoint: d.Get("endpoint").(string),
			Token: d.Get("token").(string),
			UserUuid: d.Get("user_uuid").(string),
		}
		log.Print("providerConfigure:end")
		return &#x26;config, nil
	}
}

func Resource() *schema.Resource {
	log.Print("Resource:start")
	resource := &#x26;schema.Resource{
		CreateContext: resourceHouseCreate,
		ReadContext: resourceHouseRead,
		UpdateContext: resourceHouseUpdate,
		DeleteContext: resourceHouseDelete,
	}
	log.Print("Resource:start")
	return resource
}

func resourceHouseCreate(ctx context.Context, d *schema.ResourceData, m interface{}) diag.Diagnostics {
	var diags diag.Diagnostics
	return diags
}

func resourceHouseRead(ctx context.Context, d *schema.ResourceData, m interface{}) diag.Diagnostics {
	var diags diag.Diagnostics
	return diags
}

func resourceHouseUpdate(ctx context.Context, d *schema.ResourceData, m interface{}) diag.Diagnostics {
	var diags diag.Diagnostics
	return diags
}

func resourceHouseDelete(ctx context.Context, d *schema.ResourceData, m interface{}) diag.Diagnostics {
	var diags diag.Diagnostics
	return diags
}
</code></pre>



### `validateUUID()`

<pre class="language-go" data-line-numbers><code class="lang-go"><strong>func validateUUID(v interface{}, k string) (ws []string, errors []error) {
</strong>	value := v.(string)
	if _, err := uuid.Parse(value); err != nil {
		errors = append(errors, fmt.Errorf("invalid UUID format"))
	}
	return
}
</code></pre>

**Line 1, function signature**

* **Parameters**
  * The parameter `v` is of type `interface{}`, which can accept values of any data type.
  * The parameter `k` should be a string.&#x20;
* **Return value**
  * This function will return two slices - a slice of strings (`ws`) and another of errors (`errors`).&#x20;

**Line 2 `value`**

* The string from the `v` (`interface{}`)'s argument will be assigned to the variable `value`.&#x20;
* `:=` is Go's way of declaring a variable.
* `.(string)` is Go's way of casting a type to a value.
  * a bit deeper: technically speaking, this is called "type assertion" in go and it is a bit different from casting in other languages.&#x20;
* **Lines 3-4 `if _, err`**
  * First, assign the two return values from the function `uuid.Parse(value)` to the variables `_` and `err`. Note that the function uuid is coming from Google's `uuid` package for Go (see the import statement in line 7 in the source code at the top).
  * If the variable `err`'s data type is not `nil` (`err != nil`), it means that there is an error. In that case, append the error values to the `errors` slice which will be later returned.
* **Line 6** `return` statement
  * Note that what will be returned is not declared after the `return` keyword. This is characteristic of Go, where it is not necessary to specify one as the return values are already declared in the function signature (which is the `func` statement, line 1).



### `configureProvider()`

{% code overflow="wrap" lineNumbers="true" %}
```go
func providerConfigure(p *schema.Provider) schema.ConfigureContextFunc {
	return func(ctx context.Context, d *schema.ResourceData) (interface{}, diag.Diagnostics ) {
		config := Config{
			Endpoint: d.Get("endpoint").(string),
			Token: d.Get("token").(string),
			UserUuid: d.Get("user_uuid").(string),
		}
		return &config, nil
	}
}
```
{% endcode %}

**Function signature, line 1**

* **Parameters**
  * `p` is an object of `schema.Provider` type, which is a Terraform provider.
* **Return values**
  * Interestingly, this function returns another function, which is a `schema.ConfigureContextFunc`.&#x20;
    * **Parameters**: this function to be returned takes a context object from the `context` package, and a `ResourceData` object from the `schema` package.&#x20;
      * `context`: is a package used to manage and control the lifecycle of processes and requests in Go applications.&#x20;
    * **Lines 3-7**: The function internally constructs an object called `config` which has three string values. This will be later used for authenticating our http requests and connecting to the cloud service (TerraTowns).
      * **Return values**: this function returns a pointer to the `config` struct and a `nil`. `nil` indicates that the configuration was successful.&#x20;

<figure><img src=".gitbook/assets/230 gopher in a jar - resource skeleton (1).png" alt="" width="563"><figcaption></figcaption></figure>

### Resources

* Development workflow documentation: [2.3.0 Resource Skeleton](https://medium.com/@gwenleigh/terraform-cloud-project-bootcamp-with-andrew-brown-2-3-0-resource-skeleton-5be22f6cfe68)

