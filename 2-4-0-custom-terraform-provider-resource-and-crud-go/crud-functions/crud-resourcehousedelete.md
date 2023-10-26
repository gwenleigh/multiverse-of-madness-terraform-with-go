---
cover: ../../.gitbook/assets/CRUD - delete.png
coverY: -69.50399999999999
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

# cruD resourceHouseDelete()

* [The source code: main.go](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.4.0/terraform-provider-terratowns/main.go#L291)

{% code overflow="wrap" lineNumbers="true" fullWidth="true" %}
```go
func resourceHouseDelete(ctx context.Context, d *schema.ResourceData, m interface{}) diag.Diagnostics {
	var diags diag.Diagnostics

	config := m.(*Config)

	homeUUID := d.Id()

	// Construct the HTTP Request
	url :=  config.Endpoint+"/u/"+config.UserUuid+"/homes/"+homeUUID
	req, err := http.NewRequest("DELETE", url , nil)
	if err != nil {
		return diag.FromErr(err)
	}

	// Set Headers
	req.Header.Set("Authorization", "Bearer "+config.Token)
	req.Header.Set("Content-Type", "application/json")
	req.Header.Set("Accept", "application/json")

	client := http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		return diag.FromErr(err)
	}
	defer resp.Body.Close()

	// StatusOK = 200 HTTP Response Code
	if resp.StatusCode != http.StatusOK {
		return diag.FromErr(fmt.Errorf("failed to delete home resource, status_code: %d, status: %s", resp.StatusCode, resp.Status))
	}

	d.SetId("")

	log.Print("resourceHouseDelete:end")
	return diags
}
```
{% endcode %}

* In terms of the go code, there isn't much you need more as we have gone through everything.&#x20;
* The question still remains, though, about how actually the data is updated on the Cloud side.&#x20;

## Multiverse of Madness: who's pushing to the database?

* We have learnt that it is the Terraform engine who executes the resource update operations from the local to the Cloud service. However, what Terraform actually can do is talking to the API endpoints and send the requests, and it is the server side (of the Cloud service) that is actually making the changes.&#x20;
  * Being able to interact with (or update) a database through Terraform is great for us because it means that we don't have to care if the DB is Oracle, MariaDB, PostgreSQL, if the syntax is different, if there is a spelling mistake in the database connection url, etc.&#x20;
  * The Cloud service has internal functions that handle the operations requested by the `http.requests`. If you wonder how this works on the server side, I recommend you to try [Andrews](https://exampro.co/)' [Free AWS Cloud Bootcamp](https://www.youtube.com/watch?v=8b8SvQHc4Pk\&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv\&ab\_channel=ExamPro). &#x20;
    * Come to think about it, then, it is a pretty risky operation that the Cloud service does exactly what Terraform tells it to do. Imagine, what if Terraform sends a request that asks the Cloud service to delete all the data in their database?&#x20;
      * Of course, this is not going to happen because:&#x20;
        * For the http request, all the CRUD functions are given a specific endpoint which points to the resources in your account only (the endpoint url ends with your `homeUUID`).
          * The variables `config` and `homeUUID` work as credentials for authenticating the http request. So they know it's you and the database that will be updated concerns your data only.&#x20;

<figure><img src="../../.gitbook/assets/CRUD - delete (2).png" alt="" width="563"><figcaption></figcaption></figure>

### Resources

* [Andrew ](https://www.linkedin.com/in/andrew-wc-brown/)and [Andrew](https://www.linkedin.com/search/results/all/?fetchDeterministicClustersOnly=true\&heroEntityKey=urn%3Ali%3Afsd\_profile%3AACoAACpgExEBDe45kds7laCsoy-jRoR58KujJp4\&keywords=andrew%20bayko\&origin=RICH\_QUERY\_SUGGESTION\&position=0\&searchId=ecc185ba-8cc3-45f4-929d-a719626ccc1d\&sid=jBQ\&spellCorrectionEnabled=false)'s [AWS Free Cloud Project Bootcamp](https://www.youtube.com/watch?v=8b8SvQHc4Pk\&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv\&ab\_channel=ExamPro)
