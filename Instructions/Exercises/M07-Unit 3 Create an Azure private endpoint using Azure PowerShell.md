---
Exercise:
    title: 'M07-단원 3 Azure PowerShell을 사용하여 Azure 프라이빗 엔드포인트 만들기'
    module: '모듈 - Azure 서비스에 대한 프라이빗 액세스 설계 및 구현'
---

# M07-단원 3 Azure PowerShell을 사용하여 Azure 프라이빗 엔드포인트 만들기

Private Endpoint를 통해 Azure 웹앱에 안전하게 연결하여 Azure Private Link를 시작합니다. Portal, CLI, PowerShell 등의 다양한 방법을 사용하여 엔드포인트를 만들 수 있습니다. 

이 연습에서는 Azure 웹앱용 프라이빗 엔드포인트를 만들고 가상 머신을 배포하여 프라이빗 연결을 테스트합니다.

Azure SQL 및 Azure Storage와 같은 다양한 종류의 Azure 서비스용으로 프라이빗 엔드포인트를 만들 수 있습니다.

**필수 구성 요소**

- 활성 구독이 있는 Azure 계정. 체험 계정을 만듭니다.

- PremiumV2 계층 이상의 App Service 요금제가 Azure 구독에 배포된 Azure 웹앱

1. M07 폴더에서 parameters.json을 찾아 엽니다. 메모장에서 이 파일을 열고 "value" 줄을 찾습니다. "GEN-UNIQUE". GEN-UNIQUE 자리 표시자 문자열을 웹앱 이름으로 사용할 고유한 값으로 바꿉니다. 이 변경 내용을 저장합니다.

2. Azure Portal에서 **Cloud Shell** 창의 **PowerShell** 세션을 엽니다.

3. Cloud Shell 창 도구 모음에서 파일 업로드/다운로드 아이콘을 선택하고 드롭다운 메뉴에서 업로드를 클릭합니다. 그런 다음 Cloud Shell 홈 디렉터리에 template.json 및 parameters.json 파일을 업로드합니다.



PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 예제의 작업을 실행하려면 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. ```Get-Module -ListAvailable Az```를 실행하여 설치된 버전을 확인합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/ko-kr/azure/app-service/quickstart-dotnetcore)를 참조하세요. PowerShell을 로컬로 실행하는 경우에는 ```Connect-AzAccount```를 실행하여 Azure와 연결해야 합니다.

이 연습에서는 다음을 수행합니다.

+ 작업 1: 리소스 그룹 만들기
+ 작업 2: 가상 네트워크 및 베스천 호스트 만들기
+ 작업 3: 테스트 가상 머신 만들기
+ 작업 4: Private Endpoint 만들기
+ 작업 5: 프라이빗 DNS 영역 구성
+ 작업 6: 프라이빗 엔드포인트로의 연결 테스트
+ 작업 7: 리소스 정리

## 작업 1: 리소스 그룹을 만들고 필수 웹앱 배포

Azure 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리적 컨테이너입니다.

[New-AzResourceGroup](https://docs.microsoft.com/ko-kr/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```Azure PowerShell
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```
다음 ARM 템플릿을 배포하여 이 연습에 필요한 PremiumV2-tier Azure 웹앱을 만듭니다.

   ```powershell
   $RGName = "CreatePrivateEndpointQS-rg"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile template.json -TemplateParameterFile parameters.json
   ```

## 작업 2: 가상 네트워크 및 베스천 호스트 만들기

가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다.

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

가상 네트워크 및 베스천 호스트 만들기

- New-AzVirtualNetwork

- New-AzPublicIpAddress

- New-AzBastion

 

```Azure PowerShell
## Create backend subnet config. ##

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24```

## Create Azure Bastion subnet. ##

$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##

$parameters1 = @{

 Name = 'MyVNet'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 AddressPrefix = '10.0.0.0/16'

 Subnet = $subnetConfig, $bastsubnetConfig

}

$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##

$parameters2 = @{

 Name = 'myBastionIP'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 Sku = 'Standard'

 AllocationMethod = 'Static'

}

$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##

$parameters3 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'myBastion'

 PublicIpAddress = $publicip

 VirtualNetwork = $vnet

}

New-AzBastion @parameters3
```




## 작업 3: 테스트 가상 머신 만들기

이 섹션에서는 프라이빗 엔드포인트를 테스트하는 데 사용할 가상 머신을 만듭니다.

- 다음을 사용하여 가상 머신을 만듭니다.

- Get-Credential(참고: VM의 로컬 관리자 계정 자격 증명(예: Student와 Pa55w.rd1234)을 입력하라는 메시지가 표시되면 입력하세요.)

- New-AzNetworkInterface

- New-AzVM

- New-AzVMConfig

- Set-AzVMOperatingSystem

- Set-AzVMSourceImage

- Add-AzVMNetworkInterface

``` Azure PowerShell
## Set credentials for server admin and password. ##

$cred = Get-Credential

## Command to get virtual network configuration. ##

$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##

$parameters1 = @{

 Name = 'myNicVM'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 Subnet = $vnet.Subnets[0]

}

$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##

$parameters2 = @{

 VMName = 'myVM'

 VMSize = 'Standard_DS1_v2'

}

$parameters3 = @{

 ComputerName = 'myVM'

 Credential = $cred

}

$parameters4 = @{

 PublisherName = 'MicrosoftWindowsServer'

 Offer = 'WindowsServer'

 Skus = '2019-Datacenter'

 Version = 'latest'

}

$vmConfig = New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##

New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig 


```




Azure에서는 공용 IP 주소가 할당되지 않았거나 내부 기본 Azure Load Balancer의 백 엔드 풀에 있는 Azure Virtual Machines용으로 사용 후 삭제 IP를 제공합니다. 사용 후 삭제 IP 메커니즘에서는 구성 불가능한 아웃바운드 IP 주소가 제공됩니다.

가상 머신에 공용 IP 주소를 할당하거나, 표준 Load Balancer(아웃바운드 규칙 포함 여부는 관계없음)의 백 엔드 풀에 가상 머신을 추가하면 사용 후 삭제 IP는 사용하지 않도록 설정됩니다. 가상 머신의 서브넷에 Azure Virtual Network NAT 게이트웨이 리소스를 할당하면 사용 후 삭제 IP는 사용하지 않도록 설정됩니다.

Azure의 아웃바운드 연결에 대한 자세한 내용은 아웃바운드 연결에 SNAT(Source Network Address Translation) 사용을 참조하세요.

## 작업 4: Private Endpoint 만들기

이 섹션에서는 다음을 사용하여 프라이빗 엔드포인트 및 연결을 만듭니다.

- New-AzPrivateLinkServiceConnection

- New-AzPrivateEndpoint

 

```Azure PowerShell
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##

## Replace <your-webapp-name> with your webapp name ##

$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create Private Endpoint connection. ##

$parameters1 = @{

 Name = 'myConnection'

 PrivateLinkServiceId = $webapp.ID

 GroupID = 'sites'

}

$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##

$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##

$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork

## Create private endpoint

$parameters2 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'myPrivateEndpoint'

 Location = 'eastus'

 Subnet = $vnet.Subnets[0]

 PrivateLinkServiceConnection = $privateEndpointConnection

}

New-AzPrivateEndpoint @parameters2 
```




## 작업 5: 프라이빗 DNS 영역 구성

이 섹션에서는 다음을 사용하여 프라이빗 DNS 영역을 만들어 구성합니다.

- New-AzPrivateDnsZone

- New-AzPrivateDnsVirtualNetworkLink

- New-AzPrivateDnsZoneConfig

- New-AzPrivateDnsZoneGroup

```Azure PowerShell
## Place virtual network into variable. ##

$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##

$parameters1 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'privatelink.azurewebsites.net'

}

$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##

$parameters2 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 ZoneName = 'privatelink.azurewebsites.net'

 Name = 'myLink'

 VirtualNetworkId = $vnet.Id

}

$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##

$parameters3 = @{

 Name = 'privatelink.azurewebsites.net'

 PrivateDnsZoneId = $zone.ResourceId

}

$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##

$parameters4 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 PrivateEndpointName = 'myPrivateEndpoint'

 Name = 'myZoneGroup'

 PrivateDnsZoneConfig = $config

}

New-AzPrivateDnsZoneGroup @parameters4 
```


## 작업 6: 프라이빗 엔드포인트로의 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 연결합니다.

- [Azure Portal](https://portal.azure.com/)에 로그인합니다.

- 왼쪽 탐색 창에서 **리소스 그룹**을 선택합니다.

- **CreatePrivateEndpointQS-rg**를 선택합니다.

- **myVM**을 선택합니다.

- **myVM**의 개요 페이지에서 **연결**을 선택한 다음 **Bastion**을 선택합니다.

- 파란색 **Bastion 사용** 단추를 선택합니다.

- 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

- 연결한 후 서버에서 Windows PowerShell을 엽니다.

- nslookup &lt;your- webapp-name&gt;.azurewebsites.net을 입력합니다. &lt;your-webapp-name&gt;은 이전 단계에서 만든 웹앱의 이름으로 바꿉니다. 아래 표시된 것과 유사한 메시지가 표시됩니다.

  ```
  Server: UnKnown
  
  Address: 168.63.129.16
  
  Non-authoritative answer:
  
  Name: mywebapp8675.privatelink.azurewebsites.net
  
  Address: 10.0.0.5
  
  Aliases: mywebapp8675.azurewebsites.net 
  ```  


웹앱 이름에 대해 개인 IP 주소 **10.0.0.5**가 반환됩니다. 이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.

- **myVM**에 대한 베스천 연결에서 Internet Explorer를 엽니다.
- 웹앱의 URL인 **https://&lt;your-webapp-name&gt;.azurewebsites.net**을 입력합니다.
- 애플리케이션이 배포되지 않은 경우 기본 웹앱 페이지가 표시됩니다.
  ![App Service가 실행 중임이 표시된 Azure의 페이지 스크린샷](../media/web-app-default-page.png)
- **myVM**에 대한 연결을 닫습니다.

## 작업 7: 리소스 정리

프라이빗 엔드포인트 및 VM 사용을 마쳤으면 [Remove-AzResourceGroup](https://docs.microsoft.com/ko-kr/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 제거합니다.

```Azure PowerShell
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force -AsJob
```





