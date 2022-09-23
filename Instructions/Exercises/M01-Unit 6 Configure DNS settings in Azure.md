---
Exercise:
  title: M01 - 단원 6 Azure에서 DNS 설정 구성
  module: Module - Introduction to Azure Virtual Networks
ms.openlocfilehash: e6a21f21c87ff92e0e8a1a245d5b1fab477ce271
ms.sourcegitcommit: e98d709ed0f96f3c8e8c4e74c3aea821dff153ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2022
ms.locfileid: "147922325"
---
# <a name="m01---unit-6-configure-dns-settings-in-azure"></a>M01 - 단원 6 Azure에서 DNS 설정 구성

## <a name="exercise-scenario"></a>연습 시나리오 
이 단원에서는 Contoso Ltd.의 DNS 이름 확인을 구성합니다. contoso.com이라는 프라이빗 DNS 영역을 만들고, 등록 및 확인용 VNnet을 연결한 다음, 두 개의 가상 머신을 만들어 해당 구성을 테스트합니다.

이 연습에서 다음을 수행합니다.

+ 작업 1: 프라이빗 DNS 영역 만들기
+ 작업 2: 자동 등록을 위한 서브넷 연결
+ 작업 3: 구성을 테스트할 가상 머신 만들기
+ 작업 4: 레코드가 DNS 영역에 있는지 확인

#### <a name="estimated-time-25-minutes"></a>예상 소요 시간: 25분

## <a name="task-1-create-a-private-dns-zone"></a>작업 1: 프라이빗 DNS 영역 만들기

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. Azure 홈페이지의 검색 창에 dns를 입력한 다음 **프라이빗 DNS 영역** 을 선택합니다.  
   ‎![DNS를 검색한 Azure Portal 홈 페이지](../media/create-private-dns-zone.png)

3. 프라이빗 DNS 영역에서 **만들기** 를 선택합니다.

4. 다음 표의 정보를 사용하여 프라이빗 DNS 영역을 만듭니다.

| **Tab**         | **옵션**                             | **값**            |
| --------------- | -------------------------------------- | -------------------- |
| 기본 사항          | Resource group                         | ContosoResourceGroup |
|                 | Name                                   | Contoso.com          |
| 태그            | 변경 필요 없음                    |                      |
| 검토 + 만들기 | 설정 검토 및 만들기 선택 |                      |


5. 배포가 완료될 때까지 기다렸다가 **리소스로 이동** 을 선택합니다.

6. 영역이 만들어졌는지 확인합니다.

## <a name="task-2-link-subnet-for-auto-registration"></a>작업 2: 자동 등록을 위한 서브넷 연결

1. Contoso.com의 **설정** 에서 **가상 네트워크 링크** 를 선택합니다.

2. Contoso.com | 가상 네트워크 링크에서 **+ 추가** 를 선택합니다.

![+ 추가가 강조 표시된 contoso.com | 가상 링크.](../media/add-network-link-dns.png)

3. 다음 표의 정보를 사용하여 가상 네트워크 링크를 추가합니다.

| **옵션**                          | **값**                               |
| ----------------------------------- | --------------------------------------- |
| 링크 이름                           | CoreServicesVnetLink                    |
| 구독                        | 변경 필요 없음                     |
| Virtual Network                     | CoreServicesVnet(ContosoResourceGroup) |
| 자동 등록 사용            | 선택됨                                |
| 설정을 검토하고 확인을 선택합니다. |                                         |


4. **새로 고침** 을 선택합니다.

5. CoreServicesVnetLink가 생성되었는지와 자동 등록이 설정되는지 확인합니다.

6. 다음 표의 정보를 사용하여 ManufacturingVnet에 2~5단계를 반복합니다. 

| **옵션**                          | **값**                                |
| ----------------------------------- | ---------------------------------------- |
| 링크 이름                           | ManufacturingVnetLink                    |
| 구독                        | 변경 필요 없음                      |
| Virtual Network                     | ManufacturingVnet(ContosoResourceGroup) |
| 자동 등록 사용            | 선택됨                                 |
| 설정을 검토하고 확인을 선택합니다. |                                          |


7. **새로 고침** 을 선택합니다.

8. ManufacturingVnetLink가 생성되었는지와 자동 등록이 설정되는지 확인합니다.

9. 다음 표의 정보를 사용하여 ResearchVnet에 2~5단계를 반복합니다. 

| **옵션**                          | **값**                           |
| ----------------------------------- | ----------------------------------- |
| 링크 이름                           | ResearchVnetLink                    |
| 구독                        | 변경 필요 없음                 |
| Virtual Network                     | ResearchVnet(ContosoResourceGroup) |
| 자동 등록 사용            | 선택됨                            |
| 설정을 검토하고 확인을 선택합니다. |                                     |


10. **새로 고침** 을 선택합니다.

11. ResearchVnetLink가 생성되었는지와 자동 등록이 설정되는지 확인합니다.

 

##  <a name="task-3-create-virtual-machines-to-test-the-configuration"></a>작업 3: 구성을 테스트할 가상 머신 만들기

이 섹션에서는 프라이빗 DNS 영역 구성을 테스트할 두 개의 테스트 VM을 만듭니다.

1. Azure Portal의 **Cloud Shell** 창에서 **PowerShell** 세션을 엽니다.
    
    > **참고:** Cloud Shell을 처음 연 경우에는 스토리지 계정을 만들라는 메시지가 표시될 수 있습니다. **스토리지 만들기** 를 선택합니다.

2. Cloud Shell 창의 도구 모음에서 파일 업로드/다운로드 아이콘을 선택하고 드롭다운 메뉴에서 업로드를 선택한 다음 **azuredeploy.json** 및 **azuredeploy.parameters.json** 파일을 소스 폴더 **F:\Allfiles\Exercises\M01** 의 Cloud Shell 홈 디렉터리에 차례대로 업로드합니다.

3. 다음 ARM 템플릿을 배포하여 이 연습에 필요한 VM을 만듭니다.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
4. 배포가 완료되면 Azure Portal 홈페이지로 이동한 다음 **가상 머신** 을 선택합니다.

5. 두 가상 머신이 모두 만들어졌는지 확인합니다.

 

## <a name="task-4-verify-records-are-present-in-the-dns-zone"></a>작업 4: 레코드가 DNS 영역에 있는지 확인

1. Azure Portal 홈 페이지에서 **프라이빗 DNS 영역** 을 선택합니다.

2. 프라이빗 DNS 영역에서 **contoso.com** 을 선택합니다.

3. 다음과 같이 두 VM 모두 호스트 (A) 레코드가 표시되는지 확인합니다.

![자동 등록된 호스트 A 레코드가 표시된 Contoso.com DNS 영역.](../media/contoso_com-dns-zone.png)

 

4. VM의 이름 및 IP 주소를 기록해 둡니다.

 

### <a name="connect-to-the-test-vms-using-rdp"></a>RDP를 사용하여 테스트 VM에 연결

1. Azure Portal 홈 페이지에서 **가상 머신** 을 선택합니다.

2. **TestVM1** 을 선택합니다.

3. TestVM1에서 **연결 &gt; RDP** 를 선택합니다.

![연결과 RDP가 강조 표시된 TestVM1.](../media/connect-to-am.png)

4. TestVM1 | 연결에서 **RDP 파일 다운로드** 를 선택합니다.

5. RDP 파일을 바탕화면에 저장합니다.

6. Azure Portal 홈 페이지에서 **가상 머신** 을 선택합니다.

7. **TestVM2** 를 선택합니다.

8. TestVM2에서 **연결 &gt; RDP** 를 선택합니다.

9. TestVM2 | 연결에서 **RDP 파일 다운로드** 를 선택합니다.

10. RDP 파일을 바탕화면에 저장합니다.

11. RDP 파일과 사용자 이름 **TestUser** 및 암호 **TestPa$$w0rd!** 를 사용하여 TestVM1에 연결합니다.

12. RDP 파일과 사용자 이름 **TestUser** 및 암호 **TestPa$$w0rd!** 를 사용하여 TestVM2에 연결합니다.

13. 두 VM의 **디바이스에 대한 개인 정보 설정 선택** 에서 **동의** 를 선택합니다.

14. 메시지가 표시되면 두 VM의 **네트워크** 에서 **예** 를 선택합니다.

15. TestVM1에서 명령 프롬프트를 열고 ipconfig /all 명령을 입력합니다.

16. IP 주소가 DNS 영역에서 적어둔 것과 동일한지 확인합니다.

17. TestVM2.contoso.com 명령 ping을 입력합니다.

18. FQDN이 프라이빗 DNS 영역에 기록된 IP 주소로 확인되는지 확인합니다. ping 자체는 VM에서 사용하도록 설정된 Windows 방화벽으로 인해 시간 초과됩니다.

19. 또는 nslookup TestVM2.contoso.com 명령을 입력하고 VM2의 이름 확인 레코드를 수신했는지 확인할 수 있습니다.
 

축하합니다! 프라이빗 DNS 영역을 만들고, 이름 확인 및 자동 등록 링크를 추가하고, 구성에서 이름 확인을 테스트했습니다. 
