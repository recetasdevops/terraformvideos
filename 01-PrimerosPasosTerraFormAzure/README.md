# Primeros pasos provisionando infraestructura en Azure con Terraform

En este repositorio esta incluido un fichero de configuración de TerraForm llamado main.tf que despliega una máquina virtual de Windows en Azure

# Requisitos
- Tener Terraform instalado https://www.terraform.io/downloads.html
- Tener instalado azure cli https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest

# Introducción a Azure Provider de Terraform

En esta página https://www.terraform.io/docs/providers/azurerm/ puedes encontrar toda la información necesaria para trabajar con Azure en Terraform. 

# Descripción fichero main.tf

A continuación detallo cada una de las partes del fichero main.tf

Definimos un resource group llamado rcdevopsterraform-resources y con localización en West Europe:

`resource "azurerm_resource_group" "main" {
  name     = "rcdevopsterraform-resources"
  location = "West Europe"
}`

Definimos una virtual network necesario para crear la máquina virtual. Definimos nuestro rango de IPs. También hacemos referencia a valores de otros resources, en este caso al resource group. En terraform podemos acceder a esta información de la siguiente manera ${<tiporesource>.<nombre_del_resource>.<propiedad_de_la_que_queremos_el_valor>}

`resource "azurerm_virtual_network" "main" {
  name                = "rc-network"
  address_space       = ["10.0.0.0/16"]
  location            = "${azurerm_resource_group.main.location}"
  resource_group_name = "${azurerm_resource_group.main.name}"
}`

Definimos una subnet 

`resource "azurerm_subnet" "internal" {
  name                 = "internal"
  resource_group_name  = "${azurerm_resource_group.main.name}"
  virtual_network_name = "${azurerm_virtual_network.main.name}"
  address_prefix       = "10.0.2.0/24"
}`

Definimos la interface de red de nuestra máquina virtual

`resource "azurerm_network_interface" "main" {
  name                = "rc-nic"
  location            = "${azurerm_resource_group.main.location}"
  resource_group_name = "${azurerm_resource_group.main.name}"

  ip_configuration {
    name                          = "testconfiguration1"
    subnet_id                     = "${azurerm_subnet.internal.id}"
    private_ip_address_allocation = "dynamic"
  }
}`

Por último definimos nuestra máquina virtual de Windows. Decimos como se va a llamar, cuál es la imagen (del marketplace de azure) en nuestro caso un Windows Server 2016 DataCenter

También definimos el tipo de disco de sistema a usar, el nombre y password del usuario o si vamos a activar Windows Update por defecto.

`resource "azurerm_virtual_machine" "main" {
  name                  = "rcdevopsvm"
  location              = "${azurerm_resource_group.main.location}"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  network_interface_ids = ["${azurerm_network_interface.main.id}"]
  vm_size               = "Standard_DS1_v2"

   delete_os_disk_on_termination = true

   delete_data_disks_on_termination = true

  storage_image_reference {
    publisher = "microsoftwindowsserver"
    offer     = "WindowsServer"
    sku       = "2016-Datacenter"
    version   = "latest"
  }

  storage_os_disk {
    name              = "rcwinserver"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }
  os_profile {
    computer_name  = "rcwinservervm"
    admin_username = "testadmin"
    admin_password = "Password1234!"
  }
  os_profile_windows_config {
    enable_automatic_upgrades = true
    provision_vm_agent = true
  }

  tags {
    environment = "dev"
  }
}`

# Más información y ayuda

Cualquier sugerencia o ayuda o lo que sea contactarme vía twitter @RecetasDevOps
