---
cover: .gitbook/assets/260 terratowns.png
coverY: -26
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

# 2 6 0 Terraform Cloud and Multi Homes (HCL)

Andrew deploys multiple homes. This is the end of the project. As the code repeats, Andrew suggests some challenges for us that we can work on. &#x20;

* [Video 2 6 0](https://www.youtube.com/watch?v=PswO3Tf8HDs\&list=PLBfufR7vyJJ4q5YCPl4o2XAzGRZUjuD-A\&index=57\&ab\_channel=ExamPro)

## Code Notes

* Below is the pattern of houses if we deploy multiple homes.&#x20;

```
public
├── home1
│   ├── assets
│   │   ├── ...
│   │   └── image.jpg
│   ├── index.html
│   └── error.html
├── home2
│   ├── assets
│   │   ├── ...
│   │   └── image.jpg
│   ├── index.html
└── └── error.html 
```

Below is how we create them. We repeat the entire `resource` and `module` blocks as many times as we would like to create homes.&#x20;

```hcl
resource "terratowns_home" "home" {
  name = "How to play Arcanum in 2023!"
  description = <<DESCRIPTION ...
DESCRIPTION
  domain_name = module.home_arcanum_hosting.domain_name
  town = "missingo"
  content_version = var.arcanum.content_version
}

module "home_arcanum_hosting" {
  source = "./modules/terrahome_aws"
  user_uuid = var.teacherseat_user_uuid
  public_path = var.arcanum.public_path
  content_version = var.arcanum.content_version
}
```

Explore and try to optimise the code so we can still create the same number of homes with less line of code with increased simplicity.&#x20;

<div data-full-width="true">

<figure><img src=".gitbook/assets/260 terratowns (1).png" alt=""><figcaption></figcaption></figure>

</div>

### Resources

* Development workflow documentation: [2.6.0 Terraform Cloud and Multi Homes](https://medium.com/@gwenleigh/terraform-cloud-project-bootcamp-with-andrew-brown-2-6-0-terraform-cloud-and-multi-homes-3d107a2b5e35)

