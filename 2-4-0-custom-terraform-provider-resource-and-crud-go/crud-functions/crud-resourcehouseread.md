---
description: >-
  In addition to the workflow of the Read operation, we will look into an
  interesting aspect of Terraform which is easy to miss out as it is not written
  anywhere in the code.
cover: ../../.gitbook/assets/cRud read01.png
coverY: 61.78133333333333
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

# cRud resourceHouseRead()

* **TL;DR**
  * To read data, we use `.get()` function. To update data, we use `.set()`.
    * When performing a read operation, Terraform runs a sanity check and compares the 'state that is applied via Terraform' against the 'actual state of the resources on the Cloud' (lines 28-38).&#x20;
      * So this read function includes `.set()`  even though it is a "read" operation.
  * The argument `d` is the Terraform's knowledge of the latest state that was executed via `terraform apply`. The variable `responseData` is the state data fetched from the Cloud service via API call (the http request). Terraform then compares `d` against `responseData` and updates `d` to inform itself of the most up-to-date actual cloud state.
* From this function on, there will be repeating lines of logic in the HTTP request flow. The repeat lines will be skipped.
* [The source code: main.go](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.4.0/terraform-provider-terratowns/main.go#L190)

<pre class="language-go" data-title="main.go" data-overflow="wrap" data-line-numbers data-full-width="true"><code class="lang-go"><strong>func resourceHouseRead(ctx context.Context, d *schema.ResourceData, m interface{}) diag.Diagnostics {
</strong>	var diags diag.Diagnostics

	config := m.(*Config)

	homeUUID := d.Id()

	// Construct the HTTP Request
	url := config.Endpoint+"/u/"+config.UserUuid+"/homes/"+homeUUID
	req, err := http.NewRequest("GET", url, nil)
	if err != nil {
		return diag.FromErr(err)
	}

	// Set Headers
	req.Header.Set("Authorization", "Bearer "+config.Token)
	req.Header.Set("Content-Type", "application/json")
	req.Header.Set("Accept", "application/json")
	
	// Send HTTP request
	client := http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		return diag.FromErr(err)
	}
	defer resp.Body.Close()

	var responseData map[string]interface{}

	if resp.StatusCode == http.StatusOK {
		// parse response JSON
		if err := json.NewDecoder(resp.Body).Decode(&#x26;responseData);  err != nil {
			return diag.FromErr(err)
		}
		d.Set("name",responseData["name"].(string))
		d.Set("description",responseData["description"].(string))
		d.Set("domain_name",responseData["domain_name"].(string))
		d.Set("content_version",responseData["content_version"].(float64))
	} else if resp.StatusCode != http.StatusNotFound {
		d.SetId("")
	} else if resp.StatusCode != http.StatusOK {
		return diag.FromErr(fmt.Errorf("failed to read home resource, status_code: %d, status: %s, body %s", resp.StatusCode, resp.Status, responseData))
	}

	return diags
}
</code></pre>

* **Line 6**: the ID from the `schema.ResourceData` object `d` is stored in the variable `homeUUID`.&#x20;
* **Line 10**:  Construct the HTTP request.
  * For this operation, we use the GET method as we want to "read" the existing data. The object `d` contains the local state information on the resources we created with the `resourceHouseCreate()` function.
* **Lines 30-43**: Parse response JSON & update `resourceData` `d`
  * One interesting thing to note here is that, in Terraform, the CRUD (Create, Read, Update, Delete) functions in a custom provider are not explicitly called anywhere in our Terraform codes (.tf files) or the Terraform configuration. Instead, Terraform's engine calls them under the hood based on the blue prints we declare in terraform files (`.tf`, `.tfvars` and such).
    * **Lines 32-33**: if the status code is healthy (`http.StatusOK`, line 30), the function will first run the error check and store it in the `diag` object.
    * **Lines 35-38**: We are talking about the "Read" operation in CRUD, but there are `.set()` functions in it. Did you ever question why this is necessary when what we only need to do is to read? It may make more sense for you to read the [Multiverse of Madness](crud-resourcehouseread.md#multiverse-of-madness-many-states) section below first.
      * These lines update the object `d` which is an argument (`*schema.ResourceData`) passed into the function signature (line 1). Let's clarify which is which:&#x20;
        * `d *schema.ResourceData` : this is the in-memory state within Terraform. The local state Terraform manages, and it's state 2 in the below classification.
        * `responseData` (line 28): this is the state 3, the actual state over there on the cloud ([TerraTowns](https://terratowns.cloud/)). If you think about it, it's also logical because we are creating an HTTP request and store the response from the cloud service to this variable. What else would we interact through the API with, if not with the remote "Cloud"? (Any idea :eyes: ??)
      * In summary, Terraform is updating the object `d`, its most-up-to-date knowledge about the resources it manages, and running the sanity check to make sure its knowledge is aligned with the actual situation on the cloud service.
    * **Lines 39-42**: Error handling for status 404 and if the status is sick (not 200).
* The read operation is now complete and Terraform is aware of what the state is (the state 2 updated based on state 3).

## Multiverse of Madness: multiple States

In Terraform, the concept of the "state" is extremely important. It is everything, everywhere all at once. So let's dive in.&#x20;

* State: this is the state of the resources in your cloud infrastructure.&#x20;
  * Examples: In the context of [Andrews](https://exampro.co/)' [Terraform Bootcamp](https://www.exampro.co/ter-cpb-001), the state would be the state of our S3 buckets on AWS where we store all the data necessary to spin up our terra houses.
    * :question: So the question is, <mark style="background-color:yellow;">will you provision a VPC</mark>? <mark style="background-color:purple;">How many buckets have you provisioned</mark>? <mark style="background-color:green;">How many buckets are currently running on AWS</mark>? <mark style="background-color:green;">How many RDS database instances are currently running on AWS</mark>?
    * These are all states.&#x20;

<figure><img src="../../.gitbook/assets/240 multiple states (1).png" alt=""><figcaption></figcaption></figure>

Speaking of the state of resources, we can categorise varying levels of states into specific groups because states have different characteristics depending on where they are in the process of provisioning. Generally speaking, there are three types of states:

* <mark style="background-color:yellow;">1) the state you desire</mark>&#x20;
* <mark style="background-color:purple;">2) the state that is actually provisioned (via Terraform)</mark>
* <mark style="background-color:green;">3) the actual state in place (on cloud service)</mark>

I'll elaborate the above three states:

* <mark style="background-color:yellow;">1)</mark> This is the design of the infrastructure you wish to apply and provision. It might be only in your head, or maybe you declared them in your `.tf` files. You can generally think of it as the idea of a state before `terraform apply`.
* <mark style="background-color:purple;">2)</mark> The state of the infrastructure that has been executed (provisioned) on the cloud via Terraform upon `terraform apply`. Given that you are running Terraform locally on your local machine, the data of the running state reside in your RAM (this is also called in-memory).&#x20;
* <mark style="background-color:green;">3)</mark> The state of the resources provisioned on the cloud service of your choice. This includes all kinds of state -  not just running resources, but also resources that were stopped or terminated.&#x20;
* :rotating\_light: **IMPORTANT**!: Did you knotice?
  * Generally speaking, the <mark style="background-color:green;">state 3</mark> is often the result of the <mark style="background-color:purple;">state 2</mark>. However, it is possible that discrepancies occur between the <mark style="background-color:purple;">state 2</mark> and <mark style="background-color:green;">state 3</mark>. The typical reasons include:
    * You manually deleted some resources on AWS console after running `terraform apply`.
    * There were some errors between the terraform code and the AWS resource provisioning rules so some resources failed to be created or destroyed.&#x20;
      * S3 buckets are good examples. A bucket should be empty before destroying. If it contains files, terraform will fail to destroy it. If you run `terraform destroy`, though, it still can destroy everything else in the infrastructure. So it successfully completes the destroy command AND reports you that the destroy was successful. But if you go to the S3 console and check, the bucket is still there (~~hey!~~ :eyes:).&#x20;

<figure><img src="../../.gitbook/assets/Golang-Terraform world - Page 1.png" alt="" width="375"><figcaption></figcaption></figure>
