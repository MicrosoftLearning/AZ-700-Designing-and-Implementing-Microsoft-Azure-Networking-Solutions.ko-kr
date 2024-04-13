---
Exercise:
  title: M01 - 단원 8 전역 가상 네트워크 피어링을 사용해 Azure Virtual Network 2개 연결
  module: Module 01 - Introduction to Azure Virtual Networks
---
# M01-단원 8 전역 가상 네트워크 피어링을 사용해 Azure Virtual Network 2개 연결

## 연습 시나리오

이 단원에서는 트래픽 흐름을 허용하는 피어링을 추가하여 CoreServicesVnet과 ManufacturingVnet 간의 연결을 구성합니다.

![가상 네트워크 피어링의 다이어그램](../media/8-exercise-connect-two-azure-virtual-networks-global.png)

이 단원에서 학습할 내용은 다음과 같습니다.

+ 작업 1: 구성을 테스트할 가상 머신 만들기
+ 작업 2: RDP를 사용하여 테스트 VM에 연결
+ 작업 3: VM 간 연결 테스트
+ 작업 4: CoreServicesVnet과 ManufacturingVnet 사이에 VNet 피어링 만들기
+ 작업 5: VM 간 연결 테스트
+ 작업 6: 리소스 정리

**참고:** **[대화형 랩 시뮬레이션](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Connect%20two%20Azure%20virtual%20networks%20using%20global%20virtual%20network%20peering)** 을 사용하여 이 랩을 원하는 속도로 클릭할 수 있습니다. 대화형 시뮬레이션과 호스트된 랩 간에 약간의 차이가 있을 수 있지만 보여주는 핵심 개념과 아이디어는 동일합니다.

### 예상 소요 시간: 20분

## 작업 1: 구성을 테스트할 가상 머신 만들기

이 섹션에서는 ManufacturingVnet에서 다른 Azure 가상 네트워크 내의 리소스에 액세스할 수 있는지 테스트할 테스트 VM을 Manufacturing VNet에 만듭니다.

### ManufacturingVM 만들기

1. Azure Portal의 **Cloud Shell** 창에서 **PowerShell** 세션을 엽니다.
  > **참고:** Cloud Shell을 처음 연 경우에는 스토리지 계정을 만들라는 메시지가 표시될 수 있습니다. **스토리지 만들기**를 선택합니다.

1. Cloud Shell 창의 도구 모음에서 **파일 업로드/다운로드** 아이콘을 선택하고 드롭다운 메뉴에서 **업로드**를 선택한 다음 **ManufacturingVMazuredeploy.json** 및 **ManufacturingVMazuredeploy.parameters.json** 파일을 소스 폴더 **F:\Allfiles\Exercises\M01**의 Cloud Shell 홈 디렉터리에 차례대로 업로드합니다.

1. 다음 ARM 템플릿을 배포하여 이 연습에 필요한 VM을 만듭니다.

   >**참고**: 관리 암호를 입력하라는 메시지가 표시됩니다.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
1. 배포가 완료되면 Azure Portal 홈페이지로 이동한 다음 **가상 머신**을 선택합니다.

1. 가상 머신이 만들어졌는지 확인합니다.

## 작업 2: RDP를 사용하여 테스트 VM에 연결

1. Azure Portal 홈 페이지에서 **Virtual Machines**를 선택합니다.

1. **ManufacturingVM**을 선택합니다.

1. ManufacturingVM에서 **연결 &gt; RDP**를 선택합니다.

1. ManufacturingVM | 연결에서 **RDP 파일 다운로드**를 선택합니다.

1. RDP 파일을 데스크톱에 저장합니다.

1. RDP 파일, 사용자 이름 **TestUser** 및 배포 중에 제공한 암호를 사용하여 ManufacturingVM에 연결합니다.

1. Azure Portal 홈 페이지에서 **Virtual Machines**를 선택합니다.

1. **TestVM1**을 선택합니다.

1. TestVM1에서 **연결 &gt; RDP**를 선택합니다.

1. TestVM1 | 연결에서 **RDP 파일 다운로드**를 선택합니다.

1. RDP 파일을 데스크톱에 저장합니다.

1. RDP 파일, 사용자 이름 **TestUser** 및 배포 중에 제공한 암호를 사용하여 TestVM1에 연결합니다.

1. 두 VM의 **디바이스에 대한 개인 정보 설정 선택**에서 **동의**를 선택합니다.

1. 두 VM의 **네트워크**에서 **예**를 선택합니다.

1. TestVM1에서 PowerShell 프롬프트를 열고 다음 명령, ipconfig를 실행합니다.

1. IPv4 주소를 기록해 두세요.

## 작업 3: VM 사이의 연결 테스트

1. ManufacturingVM에서 PowerShell 프롬프트를 엽니다.

1. 다음 명령을 사용하여 CoreServicesVnet에 TestVM1에 대한 연결이 없는지 확인합니다. TestVM1용 IPv4 주소를 사용해야 합니다.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```

1. 테스트 연결에 실패하면, 다음과 유사한 결과가 표시됩니다.  ![Test-NetConnection 10.20.20.4 -port 3389가 실패한 것으로 표시된 PowerShell 창 ](../media/test-netconnection-fail.png)

## 작업 4: CoreServicesVnet과 ManufacturingVnet 사이에 VNet 피어링 만들기

1. Azure 홈페이지에서 **가상 네트워크**를 선택한 다음 **CoreServicesVnet**을 선택합니다.

1. CoreServicesVnet의 **설정**에서 **피어링**을 선택합니다.
   ![핵심 서비스 VNet 피어링 설정 스크린샷 ](../media/create-peering-on-coreservicesvnet.png)

1. CoreServicesVnet | 피어링에서 **+ 추가**를 선택합니다.

1. 다음 표의 정보를 사용하여 피어링을 만듭니다.

| **섹션**                          | **옵션**                                    | **값**                             |
| ------------------------------------ | --------------------------------------------- | ------------------------------------- |
| 이 가상 네트워크                 |                                               |                                       |
|                                      | 피어링 링크 이름                             | CoreServicesVnet-to-ManufacturingVnet |
|                                      | 원격 가상 네트워크로의 트래픽             | 허용(기본값)                       |
|                                      | 원격 가상 네트워크에서 전달된 트래픽 | 허용(기본값)                       |
|                                      | 가상 네트워크 게이트웨이 또는 Route Server       | 없음(기본값)                        |
| 원격 가상 네트워크               |                                               |                                       |
|                                      | 피어링 링크 이름                             | ManufacturingVnet-to-CoreServicesVnet |
|                                      | 가상 네트워크 배포 모델              | 리소스 관리자                      |
|                                      | 리소스 ID를 알고 있음                         | 선택 안 됨                          |
|                                      | 구독                                  | 제공된 구독 선택      |
|                                      | 가상 네트워크                               | ManufacturingVnet                     |
|                                      | 원격 가상 네트워크로의 트래픽             | 허용(기본값)                       |
|                                      | 원격 가상 네트워크에서 전달된 트래픽 | 허용(기본값)                       |
|                                      | 가상 네트워크 게이트웨이 또는 Route Server       | 없음(기본값)                        |
| 설정을 검토하고 추가를 선택합니다. |                                               |                                       |
|                                      |                                               |                                       |

 >**참고**: “MOC 구독”이 없는 경우 이전에 사용한 구독을 사용합니다. 단지 이름일 뿐입니다.

1. CoreServicesVnet | 피어링에서 **CoreServicesVnet-to-ManufacturingVnet** 피어링이 표시되는지 확인합니다.

1. 가상 네트워크에서 **ManufacturingVnet**을 선택하고, **ManufacturingVnet-to-CoreServicesVnet** 피어링이 표시되는지 확인합니다.

## 작업 5: VM 간 연결 테스트

1. ManufacturingVM에서 PowerShell 프롬프트를 엽니다.

1. 다음 명령을 사용하여 이제 CoreServicesVnet에 TestVM1에 대한 연결이 있는지 확인합니다.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```

1. 테스트 연결에 성공하면 다음과 유사한 결과가 표시됩니다. ![Test-NetConnection 10.20.20.4 -port 3389 showing TCP test succeeded: true가 표시된 Powershell 창](../media/test-connection-succeeded.png)

축하합니다! 피어링을 추가하여 VNet 간의 연결을 성공적으로 구성했습니다.

## 작업 6: 리소스 정리

   >**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

1. Azure Portal의 **Cloud Shell** 창에서 **PowerShell** 세션을 엽니다. 필요한 경우 기본 설정을 사용하여 Cloud Shell 스토리지를 만듭니다.

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```

    >**참고**: 이 명령은 -AsJob 매개 변수에 의해 결정되어 비동기로 실행되므로, 동일한 PowerShell 세션 내에서 이 명령을 실행한 직후 다른 PowerShell 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지는 몇 분 정도 걸립니다.
