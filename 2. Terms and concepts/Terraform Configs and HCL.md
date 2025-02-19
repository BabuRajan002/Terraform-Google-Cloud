# Terraform configurations and Hashicorp Languages

![terraform workflow](images/TerraformWorkflow.png)

## Hashicorp Configuration Language 

***Syntax***

```sh
<BLOCK TYPE> "<BLOCK Label>" "<BLOCK LABEL>" {
    # Block body
    <IDENTIFIER> = <EXPRESSION> #Argument
}

resource "resource_type" "resource_name" {
#Resource sepcific arguments
}
```

* `providers.tf` - Terraform downloads the provider plugin in the root configuration when the provider is decalred! 