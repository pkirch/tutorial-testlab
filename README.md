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

### Skripte für Vorlagen
