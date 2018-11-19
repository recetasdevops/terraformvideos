# Primeros pasos provisionando infraestructura en Azure con Terraform

En este repositorio esta incluido un fichero de configuración de TerraForm llamado main.tf que despliega una máquina virtual de Windows en Azure

# Requisitos
- Tener Terraform instalado https://www.terraform.io/downloads.html
- Tener instalado azure cli https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest

# Introducción a Azure Provider de Terraform

En esta página https://www.terraform.io/docs/providers/azurerm/ puedes encontrar toda la información necesaria para trabajar con Azure en Terraform. 

# Descripción fichero main.tf

A continuación detallo cada una de las partes del fichero main.tf

`resource "azurerm_resource_group" "main" {
  name     = "rcdevopsterraform-resources"
  location = "West Europe"
}`



This repository contains a provider to work with Azure devOps. Now I only create projects, but in next weeks I will update with new resources like create repositories, work items ...  
# Resources

- resource_project
   Create a new project in Azure Devops Organization
