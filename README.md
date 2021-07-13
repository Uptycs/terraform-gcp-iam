# terraform gcp iam module 

This module allows you to create gcp credential config in Google Cloud Platform projects which will be used get gcp data from aws environment. 

This terraforms module will create below resources:-
 * It creates service account , workpool identity and add cloud provider to it .
 * It will attach below policies to service account 
     * roles/iam.securityReviewer 
     * roles/bigquery.resourceViewer
     * roles/pubsub.subscriber
     * roles/viewer

# Compatibility

This module is meant for use with Terraform version = "~> 3.61.0". If you haven't upgraded do terraform upgrade .


# Usage

## 1. Install terraform (One-time)

## 2. Get authentication
```
Option 1: Set credntial file in main.tf. Edit following line with credential file path
  - "credentials = file("CREDENTIALS_FILE.json")"
Option 2: Login with ADC
  - "gcloud auth application-default login"
```

## 3. Use terraform module steps 
  * Create a <filename>.tf file and paste below code
```
module "create-gcp-cred" {
  source = "git@github.com:Uptycs/terraform-gcp-iam.git"
  gcp_region = "us-east1"
  gcp_project_id = "test-project"
  gcp_project_number = "1234567899"
  is_service_account_exists = false
  service_account_name = "sa-for-cldquery"

  # Host aws account details
  host_aws_account_id = "< aws account id >"
  host_aws_instance_role = "< aws host role >"

  # Modify if required
  gcp_workload_identity = "wip-uptycs"
  gcp_wip_provider_id   = "aws-id-provider-uptycs"
}



output "service-account-email" {
  description = "The deployed Service Account's email-id"
  value       = module.create-gcp-cred.service-account-email

}

output "command-to-generate-gcp-cred-config" {
  value = module.create-gcp-cred.command-to-generate-gcp-cred-config
}

```

  * Points to be remember
```
  1. service account details 
     - Set is_service_account_exists = true if service account is already exists and pass existing service account name in service_account_name .
     - Set is_service_account_exists = false if service account is not exists and give a new name to create service account in service_account_name.
  2. Workload Identity Pool can't be deleted permanently , It is soft-deleted , Soft-deleted providers are permanently deleted after approximately 30 days.
     You can restore a soft-deleted provider using UndeleteWorkloadIdentityPoolProvider. You cannot reuse the ID of a soft-deleted provider until it is permanently deleted.
     After terraform destroy same WIP can't be created again , So modify "gcp_workload_identity" value if required.

  3. credentials.json only create once until there are no changes on variables , for creating again same cred config json data ,please use command return by "command-to-generate-gcp-cred-config"

```


## 4.Execute Terraform Script to get credentials JSON
```
#Init
#  terraform init

#Plan and Verify
#  terraform plan

#Deploy the stack
#  terraform apply

#Note : Once terraform successfully applied then it will create "credentials.json" file on current path.

#Destroy
#  "terraform destroy"

```
