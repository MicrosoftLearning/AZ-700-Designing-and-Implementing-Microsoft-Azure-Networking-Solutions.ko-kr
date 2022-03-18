---
Exercise:
  title: M01-단원 8 전역 가상 네트워크 피어링을 사용해 Azure Virtual Network 2개 연결
  module: Module - Introduction to Azure Virtual Networks
ms.openlocfilehash: 934ebb601aa0fb8a66b9493d1cb4b5d913005482
ms.sourcegitcommit: cc6b12857d97b72310f349592f5d4adbd371cc50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2022
ms.locfileid: "138422792"
---
# <a name="m01-unit-8-connect-two-azure-virtual-networks-using-global-virtual-network-peering"></a>M01-단원 8 전역 가상 네트워크 피어링을 사용해 Azure Virtual Network 2개 연결

## <a name="exercise-scenario"></a>연습 시나리오 
이 단원에서는 트래픽 흐름을 허용하는 피어링을 추가하여 CoreServicesVnet과 ManufacturingVnet 간의 연결을 구성합니다. 

이 단원에서 학습할 내용은 다음과 같습니다.

+ 작업 1: 구성을 테스트할 가상 머신 만들기
+ 작업 2: RDP를 사용하여 테스트 VM에 연결
+ 작업 3: VM 사이의 연결 테스트
+ 작업 4: CoreServicesVnet과 ManufacturingVnet 사이에 VNet 피어링 만들기
+ 작업 5: VM 간 연결 테스트
+ 작업 6: 리소스 정리

## <a name="task-1-create-a-virtual-machine-to-test-the-configuration"></a>작업 1: 구성을 테스트할 가상 머신 만들기

이 섹션에서는 ManufacturingVnet에서 다른 Azure 가상 네트워크 내의 리소스에 액세스할 수 있는지 테스트할 테스트 VM을 Manufacturing VNet에 만듭니다.

### <a name="create-manufacturingvm"></a>ManufacturingVM 만들기

1. Azure Portal의 **Cloud Shell** 창에서 **PowerShell** 세션을 엽니다.

2. Cloud Shell 창의 도구 모음에서 파일 업로드/다운로드 아이콘을 선택하고 드롭다운 메뉴에서 업로드를 선택한 다음 **ManufacturingVMazuredeploy.json** 및 **ManufacturingVMazuredeploy.parameters.json** 파일을 소스 폴더 **F:\Allfiles\Exercises\M01** 의 Cloud Shell 홈 디렉터리에 업로드합니다.

3. 다음 ARM 템플릿을 배포하여 이 연습에 필요한 VM을 만듭니다.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
4. 배포가 완료되면 Azure Portal 홈페이지로 이동한 다음 **가상 머신** 을 선택합니다.

5. 가상 머신이 만들어졌는지 확인합니다.

## <a name="task-2-connect-to-the-test-vms-using-rdp"></a>작업 2: RDP를 사용하여 테스트 VM에 연결

1. Azure Portal 홈 페이지에서 **가상 머신** 을 선택합니다.

2. **ManufacturingVM** 을 선택합니다.

3. ManufacturingVM에서 **연결 &gt; RDP** 를 선택합니다.

4. ManufacturingVM | 연결에서 **RDP 파일 다운로드** 를 선택합니다.

5. RDP 파일을 바탕화면에 저장합니다.

6. RDP 파일과 사용자 이름 **TestUser** 및 암호 **TestPa$$w0rd!** 를 사용하여 ManufacturingVM에 연결합니다.

7. Azure Portal 홈 페이지에서 **가상 머신** 을 선택합니다.

8. **TestVM1** 을 선택합니다.

9. TestVM1에서 **연결 &gt; RDP** 를 선택합니다.

10. TestVM1 | 연결에서 **RDP 파일 다운로드** 를 선택합니다.

11. RDP 파일을 바탕화면에 저장합니다.

12. RDP 파일과 사용자 이름 **TestUser** 및 암호 **TestPa$$w0rd!** 를 사용하여 TestVM1에 연결합니다.

13. 두 VM의 **디바이스에 대한 개인 정보 설정 선택** 에서 **동의** 를 선택합니다.

14. 두 VM의 **네트워크** 에서 **예** 를 선택합니다.

15. TestVM1에서 PowerShell 프롬프트를 열고 다음 명령, ipconfig를 실행합니다.

16. IPv4 주소를 기록해 두세요. 

 

## <a name="task-3-test-the-connection-between-the-vms"></a>작업 3: VM 사이의 연결 테스트

1. ManufacturingVM에서 PowerShell 프롬프트를 엽니다.

2. 다음 명령을 사용하여 CoreServicesVnet에 TestVM1에 대한 연결이 없는지 확인합니다. TestVM1용 IPv4 주소를 사용해야 합니다.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```


3. 테스트 연결이 실패하면 다음과 유사한 결과가 표시됩니다. ![Test-NetConnection 10.20.20.4 -port 3389의 실행 결과로 failed가 표시된 PowerShell 창 ](../media/test-netconnection-fail.png)

 

## <a name="task-4-create-vnet-peerings-between-coreservicesvnet-and-manufacturingvnet"></a>작업 4: CoreServicesVnet과 ManufacturingVnet 사이에 VNet 피어링 만들기

1. Azure 홈페이지에서 **가상 네트워크** 를 선택한 다음 **CoreServicesVnet** 을 선택합니다.

2. CoreServicesVnet의 **설정** 에서 **피어링** 을 선택합니다.
   ![핵심 서비스 VNet 피어링 설정 스크린샷 ](../media/create-peering-on-coreservicesvnet.png)

3. CoreServicesVnet | 피어링에서 **+ 추가** 를 선택합니다.

4. 다음 표의 정보를 사용하여 피어링을 만듭니다.

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
|                                      | 리소스 ID를 알고 있음                         | 선택되지 않음                          |
|                                      | 구독                                  | MOC Subscription-lodxxxxxxxx          |
|                                      | 가상 네트워크                               | ManufacturingVnet                     |
|                                      | 원격 가상 네트워크로의 트래픽             | 허용(기본값)                       |
|                                      | 원격 가상 네트워크에서 전달된 트래픽 | 허용(기본값)                       |
|                                      | 가상 네트워크 게이트웨이 또는 Route Server       | 없음(기본값)                        |
| 설정을 검토하고 추가를 선택합니다. |                                               |                                       |
|                                      |                                               |                                       |


5. CoreServicesVnet | 피어링에서 **CoreServicesVnet-to-ManufacturingVnet** 피어링이 표시되는지 확인합니다.

6. 가상 네트워크에서 **ManufacturingVnet** 을 선택하고, **ManufacturingVnet-to-CoreServicesVnet** 피어링이 표시되는지 확인합니다.

 

## <a name="task-5-test-the-connection-between-the-vms"></a>작업 5: VM 간 연결 테스트

1. ManufacturingVM에서 PowerShell 프롬프트를 엽니다.

2. 다음 명령을 사용하여 이제 CoreServicesVnet에 TestVM1에 대한 연결이 있는지 확인합니다. 

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```


3. 테스트 연결이 성공하면 다음과 유사한 결과가 표시됩니다. ![Test-NetConnection 10.20.20.4 -port 3389의 실행 결과로 TCP test succeeded: true가 표시된 PowerShell 창](../media/test-connection-succeeded.png)

 

축하합니다! 피어링을 추가하여 VNet 간의 연결을 성공적으로 구성했습니다. 

## <a name="task-6-clean-up-resources"></a>작업 6: 리소스 정리

   >**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

1. Azure Portal의 **Cloud Shell** 창에서 **PowerShell** 세션을 엽니다. 필요한 경우 기본 설정을 사용하여 Cloud Shell 스토리지를 만듭니다.

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```

    >**참고**: 이 명령은 -AsJob 매개 변수에 의해 결정되어 비동기로 실행되므로, 동일한 PowerShell 세션 내에서 이 명령을 실행한 직후 다른 PowerShell 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지는 몇 분 정도 걸립니다.
