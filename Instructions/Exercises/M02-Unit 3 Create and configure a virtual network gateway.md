---
Exercise:
    title: 'M02-단원 3 가상 네트워크 게이트웨이 만들기 및 구성'
    module: '모듈 - 하이브리드 네트워킹 설계 및 구현'
---


# M02-단원 3 가상 네트워크 게이트웨이 만들기 및 구성

이 연습에서는 Contoso Core Services VNet과 Manufacturing VNet 연결을 위한 가상 네트워크 게이트웨이를 구성합니다. 

이 연습에서는 다음을 수행합니다.

+ 작업 1: CoreServicesVnet 및 ManufacturingVnet 만들기
+ 작업 2: CoreServicesTestVM 만들기
+ 작업 3: ManufacturingTestVM 만들기
+ 작업 4: RDP를 사용하여 테스트 VM에 연결
+ 작업 5: VM 간의 연결 테스트
+ 작업 6: CoreServicesVnet 게이트웨이 만들기
+ 작업 7: ManufacturingVnet 게이트웨이 만들기
+ 작업 8: CoreServicesVnet을 ManufacturingVnet에 연결 
+ 작업 9: ManufacturingVnet을 CoreServicesVnet에 연결
+ 작업 10: 연결이 정상적으로 설정되는지 확인 
+ 작업 11: VM 간의 연결 테스트

## 작업 1: CoreServicesVnet 및 ManufacturingVnet 만들기

1. Azure Portal에서 **Cloud Shell** 창 내의 **PowerShell** 세션을 엽니다.

2. Cloud Shell 창 도구 모음에서 파일 업로드/다운로드 아이콘을 선택하고 드롭다운 메뉴에서 업로드를 클릭합니다. 그런 다음 Cloud Shell 홈 디렉터리에 **azuredeploy.json** 및 **azuredeploy.parameters.json** 파일을 업로드합니다.

3. 다음 ARM 템플릿을 배포하여 이 연습에 필요한 가상 네트워크 및 서브넷을 만듭니다.

   ```powershell
   $RGName = "ContosoResourceGroup"
   #create resource group if it doesnt exist
   New-AzResourceGroup -Name $RGName -Location East US
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```

## 작업 2: CoreServicesTestVM 만들기

1. Azure 홈 페이지에서 전역 검색을 사용하여 **가상 네트워크**를 입력하고 서비스에서 가상 머신을 선택합니다.

2. 가상 머신에서 **+ 만들기, + 가상 머신**을 선택합니다.

3. 다음 표의 정보를 사용하여 VM을 만듭니다.

   | **탭**         | **옵션**                                                   | **값**                             |
   | --------------- | ------------------------------------------------------------ | ------------------------------------- |
   | 기본 사항          | 리소스 그룹                                               | ContosoResourceGroup                  |
   |                 | 가상 머신 이름                                         | CoreServicesTestVM                    |
   |                 | 지역                                                       | 미국 동부                               |
   |                 | 가용성 옵션                                         | 인프라 중복이 필요하지 않습니다. |
   |                 | 이미지                                                        | Windows Server 2022 Datacenter - Gen1  |
   |                 | Azure Spot 인스턴스                                          | 선택되지 않음                          |
   |                 | 크기                                                         | Standard_D2s_v3 - vCPU 2개, 8GiB 메모리 |
   |                 | 사용자 이름                                                     | TestUser                              |
   |                 | 암호                                                     | TestPa$$w0rd!                         |
   |                 | 공용 인바운드 포트                                         | 선택한 포트 허용                  |
   |                 | 인바운드 포트 선택                                         | RDP(3389)                            |
   | 디스크           | 변경 불필요                                          |                                       |
   | 네트워킹      | 가상 네트워크                                              | CoreServicesVnet                      |
   |                 | 서브넷                                                       | DatabaseSubnet(10.20.20.0/24)        |
   |                 | 공용 IP                                                    | (신규) CoreServicesTestVM-ip           |
   |                 | NIC 네트워크 보안 그룹                                   | 기본                                 |
   |                 | 공용 인바운드 포트                                         | 선택한 포트 허용                  |
   |                 | 인바운드 포트 선택                                         | RDP(3389)                            |
   |                 | 부하 분산                                               | 선택되지 않음                          |
   | 관리      | 변경 불필요                                          |                                       |
   | 고급        | 변경 불필요                                          |                                       |
   | 태그            | 변경 불필요                                          |                                       |
   | 검토 + 만들기 | 설정을 검토하고 만들기를 선택합니다.                       |                                       |

4. 배포가 완료되면 **리소스로 이동**을 선택합니다.

## 작업 3: ManufacturingTestVM 만들기

1. Azure 홈 페이지에서 전역 검색을 사용하여 **가상 네트워크**를 입력하고 서비스에서 가상 머신을 선택합니다.

2. 가상 머신에서 **+ 만들기, + 가상 머신**을 선택합니다.

3. 다음 표의 정보를 사용하여 VM을 만듭니다.

   | **탭**         | **옵션**                                                   | **값**                                 |
   | --------------- | ------------------------------------------------------------ | ----------------------------------------- |
   | 기본 사항          | 리소스 그룹                                               | ContosoResourceGroup                      |
   |                 | 가상 머신 이름                                         | ManufacturingTestVM                       |
   |                 | 지역                                                       | 서유럽                               |
   |                 | 가용성 옵션                                         | 인프라 중복이 필요하지 않습니다.     |
   |                 | 이미지                                                        | Windows Server 2022 Datacenter - Gen1      |
   |                 | Azure Spot 인스턴스                                          | 선택되지 않음                              |
   |                 | 크기                                                         | Standard_D2s_v3 - vCPU 2개, 8GiB 메모리     |
   |                 | 사용자 이름                                                     | TestUser                                  |
   |                 | 암호                                                     | TestPa$$w0rd!                             |
   |                 | 공용 인바운드 포트                                         | 선택한 포트 허용                      |
   |                 | 인바운드 포트 선택                                         | RDP(3389)                                |
   | 디스크           | 변경 불필요                                          |                                           |
   | 네트워킹      | 가상 네트워크                                              | ManufacturingVnet                         |
   |                 | 서브넷                                                       | ManufacturingSystemSubnet(10.40.40.0/24) |
   |                 | 공용 IP                                                    | (신규) ManufacturingTestVM-ip              |
   |                 | NIC 네트워크 보안 그룹                                   | 기본                                     |
   |                 | 공용 인바운드 포트                                         | 선택한 포트 허용                      |
   |                 | 인바운드 포트 선택                                         | RDP(3389)                                |
   |                 | 부하 분산                                               | 선택되지 않음                              |
   | 관리      | 변경 불필요                                          |                                           |
   | 고급        | 변경 불필요                                          |                                           |
   | 태그            | 변경 불필요                                          |                                           |
   | 검토 + 만들기 | 설정을 검토하고 **만들기**를 선택합니다.                   |                                           |

4. 배포가 완료되면 **리소스로 이동**을 선택합니다.

## 작업 4: RDP를 사용하여 테스트 VM에 연결

1. Azure Portal 홈 페이지에서 **가상 머신**을 선택합니다.
2. **ManufacturingTestVM**을 선택합니다.
3. **ManufacturingTestVM**에서 **연결 &gt; RDP**를 선택합니다.
4. **ManufacturingTestVM | 연결**에서 **RDP 파일 다운로드**를 선택합니다.
5. 바탕 화면에 RDP 파일을 저장합니다.
6. RDP 파일, 사용자 이름 **TestUser** 및 암호 **TestPa$w0rd!** 를 사용하여 ManufacturingTestVM에 연결합니다.
7. Azure Portal 홈 페이지에서 **가상 머신**을 선택합니다.
8. **CoreServicesTestVM**을 선택합니다.
9. **CoreServicesTestVM**에서 **연결 &gt; RDP**를 선택합니다.
10. **CoreServicesTestVM | 연결**에서 **RDP 파일 다운로드**를 선택합니다.
11. 바탕 화면에 RDP 파일을 저장합니다.
12. RDP 파일, 사용자 이름 **TestUser** 및 암호 **TestPa$w0rd!** 를 사용하여 CoreServicesTestVM에 연결합니다.
13. 두 VM의 **디바이스의 개인 정보 설정 선택**에서 **수락**을 선택합니다.
14. 두 VM의 **네트워크**에서 **예**를 선택합니다.
15. CoreServicesTestVM에서 PowerShell을 열고 ipconfig 명령을 실행합니다.
16. IPv4 주소를 적어 둡니다. 

 

## 작업 5: VM 간의 연결 테스트

1. **ManufacturingTestVM**에서 PowerShell을 엽니다.

2. 다음 명령을 사용하여 CoreServicesVnet에서 CoreServicesTestVM으로의 연결이 설정되어 있지 않음을 확인합니다. CoreServicesTestVM의 IPv4 주소를 사용해야 합니다.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

3. 테스트 연결이 실패하고 다음과 같은 결과가 표시됩니다.

   ![Test-NetConnection failed.](../media/test-netconnection-fail.png)

 

##  작업 6: CoreServicesVnet 게이트웨이 만들기

1. **리소스, 서비스 및 문서 검색(G+/)** 에 **가상 네트워크 게이트웨이**를 입력하고 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.
   ![Azure Portal에서 가상 네트워크 게이트웨이 검색](../media/virtual-network-gateway-search.png)

2. 가상 네트워크 게이트웨이에서 **+ 만들기**를 선택합니다.

3. 다음 표의 정보를 사용하여 가상 네트워크 게이트웨이를 만듭니다.

   | **탭**         | **구역**       | **옵션**                                  | **값**                    |
   | --------------- | ------------------ | ------------------------------------------- | ---------------------------- |
   | 기본 사항          | 프로젝트 세부 정보   | 구독                                | 변경 불필요          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | 인스턴스 세부 정보  | 이름                                        | CoreServicesVnetGateway      |
   |                 |                   | 지역                                      | 미국 동부                      |
   |                 |                   | 게이트웨이 유형                                | VPN                          |
   |                 |                   | VPN 유형                                    | 경로 기반                  |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | 세대                                  | Generation1                  |
   |                 |                   | 가상 네트워크                             | CoreServicesVnet             |
   |                 |                   | 서브넷                                      | GatewaySubnet(10.20.0.0/27) |
   |                 | 공용 IP 주소 | 공용 IP 주소                           | 새로 만들기                   |
   |                 |                   | 공용 IP 주소 이름                      | CoreServicesVnetGateway-ip   |
   |                 |                   | 공용 IP 주소 SKU                       | 기본                        |
   |                 |                   | active-active 모드 사용                   | 사용 안 함                     |
   |                 |                   | BGP 구성                               | 사용 안 함                     |
   | 검토 + 만들기 |                   | 설정을 검토하고 **만들기**를 선택합니다. |                              |

   > [!참고] 
   >
   > 가상 네트워크 게이트웨이 하나를 만드는 데 최대 45분이 걸릴 수 있습니다. 

## 작업 7: ManufacturingVnet 게이트웨이 만들기

1. **리소스, 서비스 및 문서 검색(G+/)** 에 **가상 네트워크 게이트웨이**를 입력하고 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.

2. 가상 네트워크 게이트웨이에서 **+ 만들기**를 선택합니다.

3. 다음 표의 정보를 사용하여 가상 네트워크 게이트웨이를 만듭니다.

   | **탭**         | **구역**       | **옵션**                                  | **값**                    |
   | --------------- | ------------------ | ------------------------------------------- | ---------------------------- |
   | 기본 사항          | 프로젝트 세부 정보   | 구독                                | 변경 불필요          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | 인스턴스 세부 정보  | 이름                                        | ManufacturingVnetGateway     |
   |                 |                   | 지역                                      | 서유럽                  |
   |                 |                   | 게이트웨이 유형                                | VPN                          |
   |                 |                   | VPN 유형                                    | 경로 기반                  |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | 세대                                  | Generation1                  |
   |                 |                   | 가상 네트워크                             | ManufacturingVnet            |
   |                 |                   | 서브넷                                      | GatewaySubnet(10.30.0.0/27) |
   |                 | 공용 IP 주소 | 공용 IP 주소                           | 새로 만들기                   |
   |                 |                   | 공용 IP 주소 이름                      | ManufacturingVnetGateway-ip  |
   |                 |                   | 공용 IP 주소 SKU                       | 기본                        |
   |                 |                   | active-active 모드 사용                   | 사용 안 함                     |
   |                 |                   | BGP 구성                               | 사용 안 함                     |
   | 검토 + 만들기 |                   | 설정을 검토하고 **만들기**를 선택합니다. |                              |
   
   > [!참고]
   >
   > 가상 네트워크 게이트웨이 하나를 만드는 데 최대 45분이 걸릴 수 있습니다. 

 

## 작업 8: CoreServicesVnet을 ManufacturingVnet에 연결 

1. **리소스, 서비스 및 문서 검색(G+/)** 에 **가상 네트워크 게이트웨이**를 입력하고 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.

2. 가상 네트워크 게이트웨이에서 **CoreServicesVnetGateway**를 선택합니다.

3. CoreServicesGateway에서 **연결**, **+ 추가**를 차례로 선택합니다.

   > [!참고]
   >
   >  가상 네트워크 게이트웨이가 완전히 배포될 때까지는 이 구성을 완료할 수 없습니다.

4. 다음 표의 정보를 사용하여 연결을 만듭니다.

   | **옵션**                     | **값**                         |
   | ------------------------------ | --------------------------------- |
   | 이름                           | CoreServicesGW-to-ManufacturingGW |
   | 연결 형식                | VNet 간                      |
   | 첫 번째 가상 네트워크 게이트웨이  | CoreServicesVnetGateway           |
   | 두 번째 가상 네트워크 게이트웨이 | ManufacturingVnetGateway          |
   | 공유 키(PSK)               | abc123                            |
   | Azure 개인 IP 주소 사용   | 선택되지 않음                      |
   | BGP 사용                     | 선택되지 않음                      |
   | IKE 프로토콜                   | IKEv2                             |
   | 구독                   | 변경 불필요               |
   | 리소스 그룹                 | 변경 불필요               |
   | 위치                       | 미국 동부                           |

5. 연결을 만들려면 **확인**을 선택합니다.
   

## 작업 9: ManufacturingVnet을 CoreServicesVnet에 연결

1. **리소스, 서비스 및 문서 검색(G+/)** 에 **가상 네트워크 게이트웨이**를 입력하고 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.

2. 가상 네트워크 게이트웨이에서 **ManufacturingVnetGateway**를 선택합니다.

3. CoreServicesGateway에서 **연결**, **+ 추가**를 차례로 선택합니다.

4. 다음 표의 정보를 사용하여 연결을 만듭니다.

   | **옵션**                     | **값**                         |
   | ------------------------------ | --------------------------------- |
   | 이름                           | ManufacturingGW-to-CoreServicesGW |
   | 연결 형식                | VNet 간                      |
   | 첫 번째 가상 네트워크 게이트웨이  | ManufacturingVnetGateway          |
   | 두 번째 가상 네트워크 게이트웨이 | CoreServicesVnetGateway           |
   | 공유 키(PSK)               | abc123                            |
   | Azure 개인 IP 주소 사용   | 선택되지 않음                      |
   | BGP 사용                     | 선택되지 않음                      |
   | IKE 프로토콜                   | IKEv2                             |
   | 구독                   | 변경 불필요               |
   | 리소스 그룹                 | 변경 불필요               |
   | 위치                       | 서유럽                       |

5. 연결을 만들려면 **확인**을 선택합니다.

## 작업 10: 연결이 정상적으로 설정되는지 확인 

1. **리소스, 서비스 및 문서 검색(G+/)** 에 **연결**을 입력하고 결과에서 **연결**을 선택합니다.

2. 두 연결의 상태가 모두 **연결됨**으로 표시될 때까지 기다립니다. 화면을 새로 고쳐야 할 수도 있습니다. 

   ![VPN Gateway 연결이 정상적으로 만들어졌음이 표시된 화면](../media/connections-status-connected.png)

 

## 작업 11: VM 간의 연결 테스트

1. **ManufacturingTestVM**에서 PowerShell을 엽니다.

2. 다음 명령을 사용하여 이제 CoreServicesVnet에서 CoreServicesTestVM으로의 연결이 설정되어 있음을 확인합니다. CoreServicesTestVM의 IPv4 주소를 사용해야 합니다.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

3. 테스트 연결이 성공하고 다음과 같은 결과가 표시됩니다.

   ![Test-NetConnection suceeded.](../media/test-connection-succeeded.png)

 

축하합니다! 가상 네트워크 게이트웨이를 사용하여 VNet 간 연결을 구성했습니다.
