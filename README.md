# Testumgebungen mit Microsoft Azure
## SQL Server und SQL Datenbanken

### Skripte für das Setup (ASM / classic)

#### Variante A: Installation via Windows Management Framework (WMF) 5.0 (Production Preview)

WMF 5.0 ist auf Windows 10 vorinstalliert; auf dem Windows Server 2012 R2 noch nicht. 
Daher müssen wir das Framework vorher installieren. Nach der Installation ist ein Neustart notwendig.

Download: http://www.microsoft.com/en-us/download/details.aspx?id=48729

<pre>
Set-ExecutionPolicy RemoteSigned  
Install-Module Azure  
Install-Azure 
Add-AzureAccount
</pre>

#### Variante B: Installation via Web Platform Installer

Download über: https://azure.microsoft.com/de-de/downloads/

### Skripte für die VM-Erstellung

<pre>
# Settings
$subscriptionName = "MSFT MVA Live" # Get-AzureSubscription
$location = "West Europe" # Get-AzureLocation
$serviceName = "testservice0815"
$storageAccountName = "teststorage0815"
$storageContainerName = "vhds"
$adminUsername = "adm_test"
$adminPassword = "Azure ist toll!"
$imageName = "fb83b3509582419d99629ce476bcb5c8__SQL-Server-2014-RTM-12.0.2048.0-Ent-ENU-Win2012R2-cy15su04" # Get-AzureVMImage
$vmName = "host1"
$instanceSize = "Standard_DS3"

# In case you have more than one Azure subscription, select one.
Select-AzureSubscription -SubscriptionName $subscriptionName

# 1st: create cloud service
New-AzureService -Location $location -ServiceName $serviceName

# 2nd: create storage account.
New-AzureStorageAccount -Location $location -StorageAccountName $storageAccountName

# 3rd: create container for VHDs.
$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageContext = New-AzureStorageContext -StorageAccountKey $storageAccountKey.Primary -StorageAccountName $storageAccountKey.StorageAccountName
$result = New-AzureStorageContainer -Name $storageContainerName -Permission Off -Context $storageContext

# 4th: New-AzureVM needs the CurrentStorageAccountName to be set.
Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccountName

# 5th: create new VM configuration.
$vmConfig = New-AzureVMConfig -ImageName $imageName -InstanceSize $instanceSize -Name $vmName  `
                -MediaLocation "https://$storageAccountName.blob.core.windows.net/$storageContainerName/$vmName.vhd"

# 6th: add provisioning data to VM configuration.
Add-AzureProvisioningConfig -Windows -AdminUsername $adminUsername -Password $adminPassword -VM $vmConfig

# 7th: create new VM and let it start.
New-AzureVM -ServiceName $serviceName -Location $location -VMs $vmConfig
</pre>

### Skripte für VM-Vorlagen

<pre>
$images = Get-AzureVMImage
$images.Count

$images | Group-Object -Property OS

$images | Group-Object -Property PublisherName | Sort-Object -Property Name | Format-Table 

$images | Group-Object -Property ImageFamily | Sort-Object -Property Name | Format-Table -Property Count, Name -AutoSize

$images | Where-Object -Property ImageFamily -eq "SQL Server 2014 RTM Enterprise on Windows Server 2012 R2" | Format-List -Property ImageName, Label, PublishedDate

$imageName = Get-AzureVMImage | Where-Object -Property ImageFamily -eq "SQL Server 2014 RTM Enterprise on Windows Server 2012 R2" | Sort-Object -Property PublishedDate -Descending | Select-Object -ExpandProperty ImageName -First 1

$imageName

</pre>

### Skripte für Instanzgrößen

<pre>

Get-AzureRoleSize

Get-AzureRoleSize | Format-Table -Property InstanceSize, Cores, MemoryInMb, SupportedByVirtualMachines -AutoSize

</pre>

## Weiterführende Informationen

[Blog Peter Kirchner mit vielen PowerShell-Tipps](http://aka.ms/PeterKirchner)

[Installieren und Konfigurieren von Azure PowerShell](https://azure.microsoft.com/de-de/documentation/articles/powershell-install-configure/)