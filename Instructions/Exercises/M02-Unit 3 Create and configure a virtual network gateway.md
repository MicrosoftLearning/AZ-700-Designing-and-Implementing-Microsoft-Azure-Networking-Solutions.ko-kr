---
Exercise:
  title: M02 - 단원 3 가상 네트워크 게이트웨이 만들기 및 구성
  module: Module 02 - Design and implement hybrid networking
---


# M02-단원 3 가상 네트워크 게이트웨이 만들기 및 구성

이 연습에서는 Contoso Core Services VNet 및 Manufacturing VNet을 연결하도록 가상 네트워크 게이트웨이를 구성합니다. 

이 연습에서 다음을 수행합니다.

+ 작업 1: CoreServicesVnet 및 ManufacturingVnet 만들기
+ 작업 2: CoreServicesVM 만들기
+ 작업 3: ManufacturingVM 만들기
+ 작업 4: RDP를 사용하여 테스트 VM에 연결
+ 작업 5: VM 간 연결 테스트
+ 작업 6: CoreServicesVnet 게이트웨이 만들기
+ 작업 7: ManufacturingVnet 게이트웨이 만들기
+ 작업 8: ManufacturingVnet에 CoreServicesVnet 연결 
+ 작업 9: CoreServicesVnet에 ManufacturingVnet 연결
+ 작업 10: 연결 확인 
+ 작업 11: VM 간 연결 테스트

                **참고:** **[대화형 랩 시뮬레이션](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20and%20configure%20a%20virtual%20network%20gateway)** 을 사용하여 이 랩을 원하는 속도로 클릭할 수 있습니다. 대화형 시뮬레이션과 호스트된 랩 간에 약간의 차이가 있을 수 있지만 보여주는 핵심 개념과 아이디어는 동일합니다.

#### 예상 시간: 70분(배포 대기 시간 ~45분 포함)

## 작업 1: CoreServicesVnet 및 ManufacturingVnet 만들기

1. Azure Portal **Cloud Shell** 창 내에서 **PowerShell** 세션을 엽니다.

1. Cloud Shell 창의 도구 모음에서 **파일 업로드/다운로드** 아이콘을 선택하고 드롭다운 메뉴에서 **업로드**를 선택하고 다음 파일 **azuredeploy.json** 및 **azuredeploy.parameters.json**을 원본 폴더 **F:\Allfiles\Exercises\M02**에서 하나씩 Cloud Shell 홈 디렉터리에 업로드합니다.

1. 다음 ARM 템플릿을 배포하여 이 연습에 필요한 가상 네트워크 및 서브넷을 만듭니다.

   ```powershell
   $RGName = "ContosoResourceGroup"
   #create resource group if it doesnt exist
   New-AzResourceGroup -Name $RGName -Location "eastus"
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```

## 작업 2: CoreServicesVM 만들기

1. Azure Portal **Cloud Shell** 창 내에서 **PowerShell** 세션을 엽니다.

1. Cloud Shell 창의 도구 모음에서 **파일 업로드/다운로드** 아이콘을 선택하고 드롭다운 메뉴에서 **업로드**를 선택하고 **CoreServicesVMazuredeploy.json** 및 **CoreServicesVMazuredeploy.parameters.json** 파일을 원본 폴더 **F:\Allfiles\Exercises\M02**에서 하나씩 Cloud Shell 홈 디렉터리에 업로드합니다.

1. 다음 ARM 템플릿을 배포하여 이 연습에 필요한 VM을 만듭니다.

   >**참고**: 관리 암호를 입력하라는 메시지가 표시됩니다.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile CoreServicesVMazuredeploy.json -TemplateParameterFile CoreServicesVMazuredeploy.parameters.json
   ```
  
1. 배포가 완료되면 Azure Portal 홈페이지로 이동한 다음 **가상 머신**을 선택합니다.

1. 가상 머신이 만들어졌는지 확인합니다.

## 작업 3: ManufacturingVM 만들기

1. Azure Portal **Cloud Shell** 창 내에서 **PowerShell** 세션을 엽니다.

1. Cloud Shell 창의 도구 모음에서 **파일 업로드/다운로드** 아이콘을 선택하고 드롭다운 메뉴에서 **업로드****를 선택하고 다음 파일 ManufacturingVMazuredeploy.json** 및 **ManufacturingVMazuredeploy.parameters.json**을 원본 폴더 **F:\Allfiles\Exercises\M02**에서 하나씩 Cloud Shell 홈 디렉터리에 업로드합니다.

1. 다음 ARM 템플릿을 배포하여 이 연습에 필요한 VM을 만듭니다.

   >**참고**: 관리 암호를 입력하라는 메시지가 표시됩니다.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
1. 배포가 완료되면 Azure Portal 홈페이지로 이동한 다음 **가상 머신**을 선택합니다.

1. 가상 머신이 만들어졌는지 확인합니다.


## 작업 4: RDP를 사용하여 테스트 VM에 연결

1. Azure Portal 홈 페이지에서 **가상 머신**을 선택합니다.
1. **ManufacturingVM**을 선택합니다.
1. **ManufacturingVM**에서 **RDP 연결을 &gt;** 선택합니다.
1. **ManufacturingVM | 연결**하고 **RDP 파일 다운로드를** 선택합니다.
1. RDP 파일을 바탕화면에 저장합니다.
1. RDP 파일 및 사용자 이름 **TestUser** 및 배포 중에 제공한 암호를 사용하여 ManufacturingTestVM에 연결합니다. 연결한 후 RDP 세션을 최소화합니다.
1. Azure Portal 홈 페이지에서 **가상 머신**을 선택합니다.
1. **CoreServicesVM**을 선택합니다.
1. **CoreServicesVM**에서 **RDP 연결을 &gt;** 선택합니다.
1. **CoreServicesVM | 연결**하고 **RDP 파일 다운로드를** 선택합니다.
1. RDP 파일을 바탕화면에 저장합니다.
1. RDP 파일 및 사용자 이름 **TestUser** 및 배포 중에 제공한 암호를 사용하여 CoreServicesTestVM에 연결합니다.
1. 두 VM의 **디바이스에 대한 개인 정보 설정 선택**에서 **동의**를 선택합니다.
1. 두 VM의 **네트워크**에서 **예**를 선택합니다.
1. CoreServicesTestVM에서 PowerShell을 열고 ipconfig 명령을 실행합니다.
1. IPv4 주소를 기록해 두세요. 

 

## 작업 5: VM 간 연결 테스트

1. **ManufacturingVM**에서 PowerShell을 엽니다.

1. 다음 명령을 사용하여 CoreServicesVnet의 CoreServicesVM에 대한 연결이 없는지 확인합니다. CoreServicesVM에 대한 IPv4 주소를 사용해야 합니다.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

1. 테스트 연결이 실패하면 다음과 유사한 결과가 표시됩니다.

   ![Test-NetConnection이 실패했습니다.](../media/test-netconnection-fail.png)

 

##  작업 6: CoreServicesVnet 게이트웨이 만들기

1. **리소스, 서비스, 문서 검색(G+/)** 에서 **가상 네트워크 게이트웨이**를 입력한 다음, 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.
   ![Azure Portal에서 가상 네트워크 게이트웨이를 검색합니다.](../media/virtual-network-gateway-search.png)

1. 가상 네트워크 게이트웨이에서 **+ 만들기**를 선택합니다.

1. 다음 표의 정보를 사용하여 가상 네트워크 게이트웨이를 만듭니다.

   | **Tab**         | **섹션**       | **옵션**                                  | **값**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | 기본 사항          | 프로젝트 세부 정보   | Subscription                                | 변경 필요 없음          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | 인스턴스 정보  | 속성                                        | CoreServicesVnetGateway      |
   |                 |                   | 지역                                      | 미국 동부                      |
   |                 |                   | 게이트웨이 유형                                | VPN                          |
   |                 |                   | VPN 유형                                    | 경로 기반                  |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | Generation                                  | 생성 1                  |
   |                 |                   | 가상 네트워크                             | CoreServicesVnet             |
   |                 |                   | 서브넷                                      | GatewaySubnet(10.20.0.0/27) |
   |                 |                   | 공용 IP 주소 형식                      | 표준                     |
   |                 | 공용 IP 주소 | 공용 IP 주소                           | 새로 만들기                   |
   |                 |                   | 공용 IP 주소 이름                      | CoreServicesVnetGateway-ip   |
   |                 |                   | 활성-활성 모드 사용                   | 사용 안 함                     |
   |                 |                   | BGP 구성                               | 사용 안 함                     |
   | 검토 + 만들기 |                   | 설정을 검토하고 **만들기**를 선택합니다. |                              |

   > [!NOTE] 
   >
   > 가상 네트워크 게이트웨이 하나를 만드는 데 최대 45분이 걸릴 수 있습니다. 

## 작업 7: ManufacturingVnet 게이트웨이 만들기

1. **리소스, 서비스, 문서 검색(G+/)** 에서 **가상 네트워크 게이트웨이**를 입력한 다음, 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.

1. 가상 네트워크 게이트웨이에서 **+ 만들기**를 선택합니다.

1. 다음 표의 정보를 사용하여 가상 네트워크 게이트웨이를 만듭니다.

   | **Tab**         | **섹션**       | **옵션**                                  | **값**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | 기본 사항          | 프로젝트 세부 정보   | Subscription                                | 변경 필요 없음          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | 인스턴스 정보  | 속성                                        | ManufacturingVnetGateway     |
   |                 |                   | 지역                                      | 서유럽                  |
   |                 |                   | 게이트웨이 유형                                | VPN                          |
   |                 |                   | VPN 유형                                    | 경로 기반                  |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | Generation                                  | 생성 1                  |
   |                 |                   | 가상 네트워크                             | ManufacturingVnet            |
   |                 |                   | 서브넷                                      | GatewaySubnet(10.30.0.0/27) |
   |                 |                   | 공용 IP 주소 형식                      | 표준                     |
   |                 | 공용 IP 주소 | 공용 IP 주소                           | 새로 만들기                   |
   |                 |                   | 공용 IP 주소 이름                      | ManufacturingVnetGateway-ip  |
   |                 |                   | 활성-활성 모드 사용                   | 사용 안 함                     |
   |                 |                   | BGP 구성                               | 사용 안 함                     |
   | 검토 + 만들기 |                   | 설정을 검토하고 **만들기**를 선택합니다. |                              |
   
   > [!NOTE]
   >
   > 가상 네트워크 게이트웨이 하나를 만드는 데 최대 45분이 걸릴 수 있습니다. 

 

## 작업 8: ManufacturingVnet에 CoreServicesVnet 연결 

1. **리소스, 서비스, 문서 검색(G+/)** 에서 **가상 네트워크 게이트웨이**를 입력한 다음, 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.

1. 가상 네트워크 게이트웨이에서 **CoreServicesVnetGateway**를 선택합니다.

1. CoreServicesGateway에서 **연결**을 선택한 다음 **+ 추가**를 선택합니다.

   > [!NOTE]
   >
   >  가상 네트워크 게이트웨이가 완전히 배포될 때까지 이 구성을 완료할 수 없습니다.

1. 다음 표의 정보를 사용하여 연결을 만듭니다.

   | **옵션**                     | **값**                         |
   | ------------------------------ | --------------------------------- |
   | 속성                           | CoreServicesGW-to-ManufacturingGW |
   | 연결 형식                | VNet 간                      |
   | 첫 번째 가상 네트워크 게이트웨이  | CoreServicesVnetGateway           |
   | 두 번째 가상 네트워크 게이트웨이 | ManufacturingVnetGateway          |
   | 공유 키(PSK)               | abc123                            |
   | Azure 개인 IP 주소 사용   | 선택되지 않음                      |
   | BGP 사용                     | 선택되지 않음                      |
   | IKE 프로토콜                   | IKEv2                             |
   | Subscription                   | 변경 필요 없음               |
   | Resource group                 | 변경 필요 없음               |
   | 위치                       | 미국 동부                           |

1. 연결을 만들려면 **확인**을 선택합니다.
   

## 작업 9: CoreServicesVnet에 ManufacturingVnet 연결

1. **리소스, 서비스, 문서 검색(G+/)** 에서 **가상 네트워크 게이트웨이**를 입력한 다음, 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.

1. 가상 네트워크 게이트웨이에서 **ManufacturingVnetGateway**를 선택합니다.

1. CoreServicesGateway에서 **연결**을 선택한 다음 **+ 추가**를 선택합니다.

1. 다음 표의 정보를 사용하여 연결을 만듭니다.

   | **옵션**                     | **값**                         |
   | ------------------------------ | --------------------------------- |
   | 속성                           | ManufacturingGW-to-CoreServicesGW |
   | 연결 형식                | VNet 간                      |
   | 첫 번째 가상 네트워크 게이트웨이  | ManufacturingVnetGateway          |
   | 두 번째 가상 네트워크 게이트웨이 | CoreServicesVnetGateway           |
   | 공유 키(PSK)               | abc123                            |
   | Azure 개인 IP 주소 사용   | 선택되지 않음                      |
   | BGP 사용                     | 선택되지 않음                      |
   | IKE 프로토콜                   | IKEv2                             |
   | Subscription                   | 변경 필요 없음               |
   | Resource group                 | 변경 필요 없음               |
   | 위치                       | 서유럽                       |

1. 연결을 만들려면 **확인**을 선택합니다.

## 작업 10: 연결 확인 

1. **리소스, 서비스, 문서 검색(G+/)** 에서 **연결**을 입력한 다음 결과에서 **연결**을 선택합니다.

1. 두 연결의 상태가 **연결됨**이 될 때까지 기다립니다. 화면을 새로 고쳐야 할 수도 있습니다. 

   ![VPN Gateway 연결을 만들었습니다.](../media/connections-status-connected.png)

 

## 작업 11: VM 간 연결 테스트

1. **ManufacturingVM**에서 PowerShell을 엽니다.

1. 다음 명령을 사용하여 CoreServicesVnet에서 CoreServicesVM에 대한 연결이 있는지 확인합니다. CoreServicesVM에 대한 IPv4 주소를 사용해야 합니다.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

1. 테스트 연결이 성공하면 다음과 유사한 결과가 표시됩니다.

   ![Test-NetConnection이 성공했습니다.](../media/test-connection-succeeded.png)

1. 원격 데스크톱 연결 창을 닫습니다.

축하합니다! 가상 네트워크 게이트웨이를 사용하여 VNet 간 연결을 구성했습니다.
