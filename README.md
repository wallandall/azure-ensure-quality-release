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
- ** To destroy resources created by Terraform run ``` terraform destroy ```

## Implement Azure DevOps Pipeline
1. Create a new Pipeline 
   1. Select GitHub
   2. Under "Configure your pipeline", select "Exisitng Azure Pipelines YAML File" and select the azure-pipeline.yaml file
   3. Update the Storage account with the details created in Terraform Configuration
      1. ``` backendAzureRmStorageAccountName: "tstate8176" ```
   4. Add a new variable called "ARM_ACCESS_KEY" and add the generated key
   5. Save Pipeline
   6. Update the Test Environment
      1. Select TEST under Environments
      2. Select Add resource
      3. Select Generic and Linux from the drop down menue
      4. Copy the registration script and run it. 
      5. After the registration script has successfuly run, click on close
   7. Update Service Connection
      1. Click on Project Settings
      2. Select Sevice connection
      3. Click New Service Connection
      4. Select Service principle (automatic)
      5. SelectSubscription
         1. Subscription ID
         2. The created resourtce group
         3. Enter the Service Principal Account created in the Terraform Configuration
      6. Save the service connection
   8. Uplaod terraform.vars file to Secure files in Azure Devops

   9. Create tasks to run Terraform
   10. Run Tests for
      -  Postman
      -  Selenium
      -  JMeter

ToDo
screenshot of the log output of Terraform when executed by the CI/CD pipeline
screenshot of the successful execution of the pipeline build results page
screenshot of the log output of JMeter when executed by the CI/CD pipeline 
screenshot of the execution of the test suite by the CI/CD pipeline.
Three screenshots of the Test Run Results from Postman shown in Azure DevOps.
 One should be the Run Summary page (which contains 4 graphs), 
 one should be of the Test Results page (which contains the test case titles from each test)
 one should be of the output of the Publish Test Results step.
 screenshots of the email received when the alert is triggered
 screenshots of log analytics queries and result sets which will show specific output of the Azure resource
