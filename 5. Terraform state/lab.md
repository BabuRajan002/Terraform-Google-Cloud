# Lab

## Overview

In this lab, you will create a local backend and then create a Cloud Storage bucket to migrate the state to a remote backend

## Objecttives

In this lab, you will learn how to perform the following tasks:

* Create a local backend.
* Create a Cloud Storage backend.
* Refresh your Terraform state.

## Configure the terraform state to store in local

```sh
provider "google" {
  project     = "qwiklabs-gcp-03-34c6ecd82e32"
  region      = "europe-west4"
}
resource "google_storage_bucket" "test-bucket-for-state" {
  name        = "qwiklabs-gcp-03-34c6ecd82e32"
  location    = "US" # Replace with EU for Europe region
  uniform_bucket_level_access = true
}
terraform {
  backend "local" {
    path = "terraform/state/terraform.tfstate"
  }
}
```

## Add cloud storage as a backend

***Step1*** Adding the cloud storage as a backend
```sh
provider "google" {
  project     = "qwiklabs-gcp-03-34c6ecd82e32"
  region      = "europe-west4"
}
resource "google_storage_bucket" "test-bucket-for-state" {
  name        = "qwiklabs-gcp-03-34c6ecd82e32"
  location    = "US" # Replace with EU for Europe region
  uniform_bucket_level_access = true
}
terraform {
  backend "gcs" {
    bucket  = "qwiklabs-gcp-03-34c6ecd82e32"
    prefix  = "terraform/state"
  }
}
```
***step2*** Once you done execute the below command to migrate 

```sh
terraform init -migrate-state
```
***Step3*** Refresh the state

The terraform refresh command is used to reconcile the state Terraform knows about (via its state file) with the real-world infrastructure. This can be used to detect any drift from the last-known state and to update the state file. This does not modify infrastructure, but does modify the state file. If the state is changed, this may cause changes to occur during the next plan or apply.

1. Return to your storage bucket in the Cloud Console. Select the check box next to the name, and click the Labels button on the top. The info panel with Labels tabs will open up.
2. Click +ADD LABEL. Set the Key1 = key and Value1 = value. Click Save.
3. Return to Cloud Shell and use the following command to update the state file:

```sh
terraform refresh
```

***Note*** In case of we want to change the backend we need to update the main.tf file and execute the `terraform init -migrate-plan`
