---
cover: .gitbook/assets/250 deploy to terratowns (1).png
coverY: 126.75647668393782
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

# 2 5 0 Deploying to TerraTowns (HCL)

Andrew test-deploys a house on the test town Missingo using our fully-functional custom plugin.

* [Video 2 5 0](https://www.youtube.com/watch?v=fPYHmiM9r6Y\&list=PLBfufR7vyJJ4q5YCPl4o2XAzGRZUjuD-A\&index=56\&ab\_channel=ExamPro)

## Code Notes

* I trust that we have covered enough knowledge to understand how Go works and how the plugin is written in Go. This section provides the side-by-side comparison of Go in use in HCL (left) and the underlying mechanism of the "Go binary" (right).
* The source code: [main.tf](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.5.0/main.tf) & [main.go](https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.5.0/terraform-provider-terratowns/main.go)&#x20;

<div data-full-width="true">

<figure><img src=".gitbook/assets/250 comparison provider.png" alt=""><figcaption><p>Provider() - the use of Go binary in HCL (left) and the source code of the Go binary (right)</p></figcaption></figure>

</div>

<div data-full-width="true">

<figure><img src=".gitbook/assets/250 comparison resource (1).png" alt=""><figcaption><p>Resource() - the use of Go binary in HCL (left) and the source code of the Go binary (right)</p></figcaption></figure>

</div>

Or see the source code in Github yourself:

<table><thead><tr><th width="154.33333333333331">Element</th><th width="325">HCL (main.tf)</th><th>Go (main.go)</th></tr></thead><tbody><tr><td><code>Provider()</code></td><td><a href="https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.5.0/main.tf">provider "terratowns"</a></td><td><a href="https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.5.0/terraform-provider-terratowns/main.go#L36">*schema.Provider</a></td></tr><tr><td><code>Resource()</code></td><td><a href="https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.5.0/main.tf">resource "terratowns_home" "home"</a></td><td><a href="https://github.com/omenking/terraform-beginner-bootcamp-2023/blob/2.5.0/terraform-provider-terratowns/main.go#L92">*schema.Resource</a></td></tr></tbody></table>

Once deployed, below is what a terratowns house looks like on the Cloud service ([TerraTowns](https://terratowns.cloud/)).&#x20;

<div data-full-width="true">

<figure><img src=".gitbook/assets/250 deploy to terratowns_with_attribs.png" alt=""><figcaption></figcaption></figure>

</div>

### Resources

* Development workflow documentation: [2.5.0 Deploying to Terratowns](https://medium.com/@gwenleigh/terraform-cloud-project-bootcamp-with-andrew-brown-2-5-0-deploying-to-terratowns-b937e75641c9)
