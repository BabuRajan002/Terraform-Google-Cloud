## Overview
In this lab, you will create two VMs in the default network. We will use variables to define the VM's attributes at runtime and use output values to print a few resource attributes.

We will then add a static IP address to the first VM to examine how terraform handles implicit dependencies. We will then create a GCS bucket by mentioning explicit dependency to the VM to examine how terraform handles explicit dependency.

## Objectives
In this lab, you learn how to perform the following tasks:

Use variables and output values
Observe implicit dependency
Create explicit resource dependency
Task 1. Sign in to the Cloud Console
For each lab, you get a new Google Cloud project and set of resources for a fixed time at no cost.

```sh
cat instance.tf

# Create a new instance that uses the bucket
resource "google_compute_instance" "another_instance" {

  name         = "terraform-instance-2"
  machine_type = "e2-micro"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
  network_interface {
    network = "default"
    access_config {
    }
  }
  # Tells Terraform that this VM instance must be created only after the
  # storage bucket has been created.
  depends_on = [google_storage_bucket.example_bucket]
}

# New resource for the storage bucket our application will use.
resource "google_storage_bucket" "example_bucket" {
  name     = "qwiklabs-gcp-03-72c934b3a601-2025-02-22"
  location = "US"
  website {
    main_page_suffix = "index.html"
    not_found_page   = "404.html"
  }
}

```

```sh
cat outputs.tf

output "network_IP" {
    value = google_compute_instance.vm_instance.instance_id
    description = "The internal ip address of the instance"
}
output "instance_link" {
  value = google_compute_instance.vm_instance.self_link
  description = "The URI of the created resource."
}
```

```sh
cat provider.tf


  provider "google" {
  project = "qwiklabs-gcp-03-72c934b3a601"
  region  = "us-central1"
  zone    = "us-central1-b"
}

```

```sh
cat variables.tf

variable "instance_name" {
    type     =string
    description = "Name for the Google Compute instance"
}

variable "instance_zone" {
    type      =string
    description = "Zone for the Google Compute instance"
}
variable "instance_type" {
  type        = string
  description = "Disk type of the Google Compute instance"
  default     = "e2-medium"
  }

```

## Explicit dependency
```sh
cat exp.tf
# Create a new instance that uses the bucket
resource "google_compute_instance" "another_instance" {

  name         = "terraform-instance-2"
  machine_type = "e2-micro"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
  network_interface {
    network = "default"
    access_config {
    }
  }
  # Tells Terraform that this VM instance must be created only after the
  # storage bucket has been created.
  depends_on = [google_storage_bucket.example_bucket]
}

# New resource for the storage bucket our application will use.
resource "google_storage_bucket" "example_bucket" {
  name     = "qwiklabs-gcp-03-72c934b3a601-2025-02-22"
  location = "US"
  website {
    main_page_suffix = "index.html"
    not_found_page   = "404.html"
  }
}

```

```sh
cat terraform.tfstate

{
  "version": 4,
  "terraform_version": "1.5.7",
  "serial": 6,
  "lineage": "56194bab-a4fe-0a01-0e20-0c1185ccc865",
  "outputs": {
    "instance_link": {
      "value": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/instances/myinstance",
      "type": "string"
    },
    "network_IP": {
      "value": "5682915491527792270",
      "type": "string"
    }
  },
  "resources": [
    {
      "mode": "managed",
      "type": "google_compute_address",
      "name": "vm_static_ip",
      "provider": "provider[\"registry.terraform.io/hashicorp/google\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "address": "34.59.69.72",
            "address_type": "EXTERNAL",
            "creation_timestamp": "2025-02-22T02:35:05.421-08:00",
            "description": "",
            "effective_labels": {
              "goog-terraform-provisioned": "true"
            },
            "id": "projects/qwiklabs-gcp-03-72c934b3a601/regions/us-central1/addresses/terraform-static-ip",
            "ip_version": "",
            "ipv6_endpoint_type": "",
            "label_fingerprint": "vezUS-42LLM=",
            "labels": {},
            "name": "terraform-static-ip",
            "network": "",
            "network_tier": "PREMIUM",
            "prefix_length": 0,
            "project": "qwiklabs-gcp-03-72c934b3a601",
            "purpose": "",
            "region": "us-central1",
            "self_link": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/regions/us-central1/addresses/terraform-static-ip",
            "subnetwork": "",
            "terraform_labels": {
              "goog-terraform-provisioned": "true"
            },
            "timeouts": null,
            "users": [
              "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/instances/myinstance"
            ]
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjoxMjAwMDAwMDAwMDAwLCJkZWxldGUiOjEyMDAwMDAwMDAwMDAsInVwZGF0ZSI6MTIwMDAwMDAwMDAwMH19"
        }
      ]
    },
    {
      "mode": "managed",
      "type": "google_compute_instance",
      "name": "another_instance",
      "provider": "provider[\"registry.terraform.io/hashicorp/google\"]",
      "instances": [
        {
          "schema_version": 6,
          "attributes": {
            "advanced_machine_features": [],
            "allow_stopping_for_update": null,
            "attached_disk": [],
            "boot_disk": [
              {
                "auto_delete": true,
                "device_name": "persistent-disk-0",
                "disk_encryption_key_raw": "",
                "disk_encryption_key_sha256": "",
                "initialize_params": [
                  {
                    "enable_confidential_compute": false,
                    "image": "https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/debian-11-bullseye-v20250212",
                    "labels": {},
                    "provisioned_iops": 0,
                    "provisioned_throughput": 0,
                    "resource_manager_tags": null,
                    "resource_policies": [],
                    "size": 10,
                    "storage_pool": "",
                    "type": "pd-standard"
                  }
                ],
                "interface": "",
                "kms_key_self_link": "",
                "mode": "READ_WRITE",
                "source": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/disks/terraform-instance-2"
              }
            ],
            "can_ip_forward": false,
            "confidential_instance_config": [],
            "cpu_platform": "Intel Broadwell",
            "creation_timestamp": "2025-02-22T02:45:14.464-08:00",
            "current_status": "RUNNING",
            "deletion_protection": false,
            "description": "",
            "desired_status": null,
            "effective_labels": {
              "goog-terraform-provisioned": "true"
            },
            "enable_display": false,
            "guest_accelerator": [],
            "hostname": "",
            "id": "projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/instances/terraform-instance-2",
            "instance_id": "8502773443628110933",
            "key_revocation_action_type": "",
            "label_fingerprint": "vezUS-42LLM=",
            "labels": null,
            "machine_type": "e2-micro",
            "metadata": null,
            "metadata_fingerprint": "RRAcc9G9f1g=",
            "metadata_startup_script": null,
            "min_cpu_platform": "",
            "name": "terraform-instance-2",
            "network_interface": [
              {
                "access_config": [
                  {
                    "nat_ip": "35.193.200.117",
                    "network_tier": "PREMIUM",
                    "public_ptr_domain_name": ""
                  }
                ],
                "alias_ip_range": [],
                "internal_ipv6_prefix_length": 0,
                "ipv6_access_config": [],
                "ipv6_access_type": "",
                "ipv6_address": "",
                "name": "nic0",
                "network": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/global/networks/default",
                "network_ip": "10.128.0.3",
                "nic_type": "",
                "queue_count": 0,
                "stack_type": "IPV4_ONLY",
                "subnetwork": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/regions/us-central1/subnetworks/default",
                "subnetwork_project": "qwiklabs-gcp-03-72c934b3a601"
              }
            ],
            "network_performance_config": [],
            "params": [],
            "project": "qwiklabs-gcp-03-72c934b3a601",
            "reservation_affinity": [],
            "resource_policies": null,
            "scheduling": [
              {
                "automatic_restart": true,
                "availability_domain": 0,
                "instance_termination_action": "",
                "local_ssd_recovery_timeout": [],
                "max_run_duration": [],
                "min_node_cpus": 0,
                "node_affinities": [],
                "on_host_maintenance": "MIGRATE",
                "on_instance_stop_action": [],
                "preemptible": false,
                "provisioning_model": "STANDARD"
              }
            ],
            "scratch_disk": [],
            "self_link": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/instances/terraform-instance-2",
            "service_account": [],
            "shielded_instance_config": [
              {
                "enable_integrity_monitoring": true,
                "enable_secure_boot": false,
                "enable_vtpm": true
              }
            ],
            "tags": null,
            "tags_fingerprint": "42WmSpB8rSM=",
            "terraform_labels": {
              "goog-terraform-provisioned": "true"
            },
            "timeouts": null,
            "zone": "us-central1-b"
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjoxMjAwMDAwMDAwMDAwLCJkZWxldGUiOjEyMDAwMDAwMDAwMDAsInVwZGF0ZSI6MTIwMDAwMDAwMDAwMH0sInNjaGVtYV92ZXJzaW9uIjoiNiJ9",
          "dependencies": [
            "google_storage_bucket.example_bucket"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "google_compute_instance",
      "name": "vm_instance",
      "provider": "provider[\"registry.terraform.io/hashicorp/google\"]",
      "instances": [
        {
          "schema_version": 6,
          "attributes": {
            "advanced_machine_features": [],
            "allow_stopping_for_update": null,
            "attached_disk": [],
            "boot_disk": [
              {
                "auto_delete": true,
                "device_name": "persistent-disk-0",
                "disk_encryption_key_raw": "",
                "disk_encryption_key_sha256": "",
                "initialize_params": [
                  {
                    "enable_confidential_compute": false,
                    "image": "https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/debian-11-bullseye-v20250212",
                    "labels": {},
                    "provisioned_iops": 0,
                    "provisioned_throughput": 0,
                    "resource_manager_tags": {},
                    "resource_policies": [],
                    "size": 10,
                    "storage_pool": "",
                    "type": "pd-standard"
                  }
                ],
                "interface": "",
                "kms_key_self_link": "",
                "mode": "READ_WRITE",
                "source": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/disks/myinstance"
              }
            ],
            "can_ip_forward": false,
            "confidential_instance_config": [],
            "cpu_platform": "Intel Broadwell",
            "creation_timestamp": "2025-02-22T02:35:14.814-08:00",
            "current_status": "RUNNING",
            "deletion_protection": false,
            "description": "",
            "desired_status": null,
            "effective_labels": {
              "goog-terraform-provisioned": "true"
            },
            "enable_display": false,
            "guest_accelerator": [],
            "hostname": "",
            "id": "projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/instances/myinstance",
            "instance_id": "5682915491527792270",
            "key_revocation_action_type": "",
            "label_fingerprint": "vezUS-42LLM=",
            "labels": {},
            "machine_type": "e2-medium",
            "metadata": {},
            "metadata_fingerprint": "RRAcc9G9f1g=",
            "metadata_startup_script": null,
            "min_cpu_platform": "",
            "name": "myinstance",
            "network_interface": [
              {
                "access_config": [
                  {
                    "nat_ip": "34.59.69.72",
                    "network_tier": "PREMIUM",
                    "public_ptr_domain_name": ""
                  }
                ],
                "alias_ip_range": [],
                "internal_ipv6_prefix_length": 0,
                "ipv6_access_config": [],
                "ipv6_access_type": "",
                "ipv6_address": "",
                "name": "nic0",
                "network": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/global/networks/default",
                "network_ip": "10.128.0.2",
                "nic_type": "",
                "queue_count": 0,
                "stack_type": "IPV4_ONLY",
                "subnetwork": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/regions/us-central1/subnetworks/default",
                "subnetwork_project": "qwiklabs-gcp-03-72c934b3a601"
              }
            ],
            "network_performance_config": [],
            "params": [],
            "project": "qwiklabs-gcp-03-72c934b3a601",
            "reservation_affinity": [],
            "resource_policies": [],
            "scheduling": [
              {
                "automatic_restart": true,
                "availability_domain": 0,
                "instance_termination_action": "",
                "local_ssd_recovery_timeout": [],
                "max_run_duration": [],
                "min_node_cpus": 0,
                "node_affinities": [],
                "on_host_maintenance": "MIGRATE",
                "on_instance_stop_action": [],
                "preemptible": false,
                "provisioning_model": "STANDARD"
              }
            ],
            "scratch_disk": [],
            "self_link": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-72c934b3a601/zones/us-central1-b/instances/myinstance",
            "service_account": [],
            "shielded_instance_config": [
              {
                "enable_integrity_monitoring": true,
                "enable_secure_boot": false,
                "enable_vtpm": true
              }
            ],
            "tags": [],
            "tags_fingerprint": "42WmSpB8rSM=",
            "terraform_labels": {
              "goog-terraform-provisioned": "true"
            },
            "timeouts": null,
            "zone": "us-central1-b"
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjoxMjAwMDAwMDAwMDAwLCJkZWxldGUiOjEyMDAwMDAwMDAwMDAsInVwZGF0ZSI6MTIwMDAwMDAwMDAwMH0sInNjaGVtYV92ZXJzaW9uIjoiNiJ9",
          "dependencies": [
            "google_compute_address.vm_static_ip"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "google_storage_bucket",
      "name": "example_bucket",
      "provider": "provider[\"registry.terraform.io/hashicorp/google\"]",
      "instances": [
        {
          "schema_version": 3,
          "attributes": {
            "autoclass": [],
            "cors": [],
            "custom_placement_config": [],
            "default_event_based_hold": false,
            "effective_labels": {
              "goog-terraform-provisioned": "true"
            },
            "enable_object_retention": false,
            "encryption": [],
            "force_destroy": false,
            "hierarchical_namespace": [
              {
                "enabled": false
              }
            ],
            "id": "qwiklabs-gcp-03-72c934b3a601-2025-02-22",
            "labels": null,
            "lifecycle_rule": [],
            "location": "US",
            "logging": [],
            "name": "qwiklabs-gcp-03-72c934b3a601-2025-02-22",
            "project": "qwiklabs-gcp-03-72c934b3a601",
            "project_number": 1047763276511,
            "public_access_prevention": "inherited",
            "requester_pays": false,
            "retention_policy": [],
            "rpo": "DEFAULT",
            "self_link": "https://www.googleapis.com/storage/v1/b/qwiklabs-gcp-03-72c934b3a601-2025-02-22",
            "soft_delete_policy": [
              {
                "effective_time": "2025-02-22T10:45:08.380Z",
                "retention_duration_seconds": 604800
              }
            ],
            "storage_class": "STANDARD",
            "terraform_labels": {
              "goog-terraform-provisioned": "true"
            },
            "timeouts": null,
            "uniform_bucket_level_access": false,
            "url": "gs://qwiklabs-gcp-03-72c934b3a601-2025-02-22",
            "versioning": [],
            "website": [
              {
                "main_page_suffix": "index.html",
                "not_found_page": "404.html"
              }
            ]
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjo2MDAwMDAwMDAwMDAsInJlYWQiOjI0MDAwMDAwMDAwMCwidXBkYXRlIjoyNDAwMDAwMDAwMDB9LCJzY2hlbWFfdmVyc2lvbiI6IjMifQ=="
        }
      ]
    }
  ],
  "check_results": null
}

```