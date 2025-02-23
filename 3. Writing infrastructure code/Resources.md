# Resrources

* Resources are infrastructure elements you can configure using terraform. 

***Examples*** 

* Compute engines instance, VPC, Cloud Storage bucket, Furewall rules
* Terrafom uses the underlying APIs of each google cloud service to deploy your reosurces.

## Meta arguments

* Count - Create a multiple instances according to the value assigned to the count
```sh
resource "google_compute_instance" "dev-vm" {
  count = 3
  name  = "dev_vm${count.index + 1}" --> By default the index value is 0
}
```

* for_each - Create multiple resource instances as per a set of strings

```sh
resource "google_compute_instance" "dev_vm" {
  for_each = toset (["us-central1-a", "asia-east1-b", "eurpoe_west4-a"])
  name = "dev-${each.value}"

  zone = each.value
}
```
* depends_on - specify explicit dependency
* lifecycle - define life cycle of resource. (ex: It destroys the resources before they expire)
* provider - Select a non-default provider configuration

## variables.tf

* Variable name must be unique

```sh
variable "variable_name" {
  type = <variable_type>
  default = "<default value>"
  description = "<variable description>"
  sensitive = true   ------> When set to true, the value is marked sensitive at run time
}
```

## Output values

* Output values expose information about the resource to the user of the terraform configuration
* Output values will be generated only after the resources creation. 

