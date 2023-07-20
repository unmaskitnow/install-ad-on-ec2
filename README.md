# install-ad-on-ec2
Install Active Directory on Amazon EC2 Windows Server

## Link : [Install Active Directory on Amazon EC2 Windows Server](TBD)

## Powershell Commands

Rename Computer
  ```powershell
Rename-Computer -NewName "<newName>" -Restart
  ```
### DC01
Install Active Directory Domain Services Windows Feature along with its Management tools
  ```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
  ```

Verify Active Directory Domain Service Windows Feature installation
  ```powershell
Get-WindowsFeature -Name AD-Domain-Services
  ```

Configure an Active Directory Forest & Install Domain Name Service(DNS) Server Service. 
  ```powershell
$domainName = "<domainName>"              
Install-ADDSForest -DomainName $domainName -InstallDNS 
  ```

### DC02
Install Active Directory Domain Services Windows Feature along with its Management tools
  ```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
  ```

Verify Active Directory Domain Service Windows Feature installation
  ```powershell
Get-WindowsFeature -Name AD-Domain-Services
  ```

Get Interface Index for Ethernet adapter (Elastic Network Adapter)
  ```powershell
Get-NetIPInterface 
  ```

Set the primary DNS Server IP on the Ethernet Adapter
  ```powershell
Set-DnsClientServerAddress -InterfaceIndex <InterfaceIndex> -ServerAddresses ("<DC01 IP>")
  ```

Add an additional Domain Controller for the domain
  ```powershell
#Add as second domain controller
$domainName = "<domainName>"
$domainUser = "<domainNetBIOSName\domainAdministratorUserName>"

$HashArguments = @{
    Credential = (Get-Credential $domainUser)
    DomainName = $domainName
    InstallDns = $true
}

Install-ADDSDomainController @HashArguments 
  ```






