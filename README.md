# azure-ensure-quality-release
In this project we will define the steps to create desposible test environments, run automated tests, monitor and deploy a Python Application to Azure using Terraform and Azure App Services. 
High level architecture of the solution is as per the below image:

## Architecture
![alt text](/images/architecture.png "Architecture Diagram")

## Terraform Configuration
1. Create a Service Principle by running ```az ad sp create-for-rbac --role="Contributor" --name="TerraformSP" ```
2. The above step will produce the following variables:
   - appId
   - displayName
   - name
   - password
   - tenant    
3. Rename terraform.tvars.example to terraform.tvars and add the required variables. To ensure secuirty make sure this file is added to gitignore and not pushed to GitHub.
4. To get the Subscription ID run ``` az account list ``` 
5. Create a storage account by running ``` create-storage-account.sh ```. Replace the values in terraform/Environments/test/main.tf with the output as per the below example:

 ```
terraform {
    backend "azurerm" {
        resource_group_name  = "tstate"
        storage_account_name = "tstate8176"
        container_name       = "tstate"
        key                  = "terraform.tfstate"
    }
}
```
## Running Terraform
Terraform creates the below resources based on the defined variables provided in the above section:
   1. Azure AppService
   2. Network
   3. Network Security Group
   4. Public IP
   5. Resource Group
   6. Linux VM
   
To create the above resources run the below commands from ```terraform/environments/test``` directory:
- ``` terraform init ``` Initializes the the directory containing the Terraform configuration files
- ```terraform plan -out solution.plan ``` Creates an execution plan
- ```terraform apply solution.plan ``` Executes the actions proposed in the Terraform plan
- To destroy resources created by Terraform: terraform destroy

## Implement Azure DevOps Pipeline
   1. Create tasks to run Terraform
   2. Run Tests for
      -  Postman
      -  Selenium
      -  JMeter



