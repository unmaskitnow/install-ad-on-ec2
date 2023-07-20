# install-ad-on-ec2
Install Active Directory on Amazon EC2 Windows Server

## Link to YouTube video : [Install Active Directory on Amazon EC2 Windows Server](https://youtu.be/yGWVEyG2sG4)

## Agenda
* Create VPC with two public and two private subnets.
* Launch Bastion Host Instance in Public Subnet.
* Launch DC01 in private subnet and configure as primary domain controller for the domain. In this demo, the domain name is "corp.local".
* Launch DC02 in the other private subnet for high availability and configure as additional (second) domain controller for the domain.
* Run Powershell commands to verify the installation.
* Login to DC01 & DC02 as Domain Administrator.

## [Microsoft documentation for Active Directory Port Requirements](https://learn.microsoft.com/en-us/troubleshoot/windows-server/identity/config-firewall-for-ad-domains-and-trusts)

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

Set the primary and secondary DNS Server IPs on the Ethernet Adapter
  ```powershell
Set-DnsClientServerAddress -InterfaceIndex <InterfaceIndex> -ServerAddresses ("<DC01 IP>", "<DC02 IP>")
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
$domainName = "<domainName>"
$domainUser = "<domainNetBIOSName\domainAdministratorUserName>"

$HashArguments = @{
    Credential = (Get-Credential $domainUser)
    DomainName = $domainName
    InstallDns = $true
}

Install-ADDSDomainController @HashArguments 
  ```

Set the primary and secondary DNS Server IPs on the Ethernet Adapter
  ```powershell
Set-DnsClientServerAddress -InterfaceIndex <InterfaceIndex> -ServerAddresses ("<DC02 IP>", "<DC01 IP>")
  ```






