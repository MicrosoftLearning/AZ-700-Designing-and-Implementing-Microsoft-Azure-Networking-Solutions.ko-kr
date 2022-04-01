---
Exercise:
  title: M06-단원 4 Azure Portal을 사용하여 가상 네트워크에서 DDoS 보호 구성
  module: Module - Design and implement network security
ms.openlocfilehash: e8ad8b1a640391e4393082999d50ed36dcc561ac
ms.sourcegitcommit: 15778a5942c3177246f4fb1077d4233ddeaf95a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2022
ms.locfileid: "140742016"
---
# <a name="m06-unit-4-configure-ddos-protection-on-a-virtual-network-using-the-azure-portal"></a>M06-단원 4 Azure Portal을 사용하여 가상 네트워크에서 DDoS 보호 구성

Contoso의 네트워크 보안 팀 책임자로서 가상 네트워크에서 모의 DDoS 공격을 실행하려고 합니다. 다음 단계는 가상 네트워크를 만들고, DDoS Protection을 구성하고, 원격 분석 및 메트릭의 도움을 통해 관찰하고 모니터링할 수 있는 공격을 만드는 과정을 안내합니다.

이 연습에서 다음을 수행합니다.

+ 작업 1: 리소스 그룹 만들기
+ 작업 2: DDoS 보호 계획 만들기
+ 작업 3: 새 가상 네트워크에서 DDoS Protection을 사용하도록 설정
+ 작업 4: DDoS 원격 분석 구성
+ 작업 5: DDoS 진단 로그 구성
+ 작업 6: DDoS 경고 구성
+ 작업 7: DDoS 서비스 요청을 제출하여 DDoS 공격 실행
+ 작업 8: 리소스 정리



## <a name="task-1-create-a-resource-group"></a>작업 1: 리소스 그룹 만들기

1. Azure 계정에 로그인

2. Azure Portal 홈페이지에서 **리소스 그룹** 을 선택합니다.

3. **만들기** 를 선택합니다. 

4. **기본 사항** 탭의 **리소스 그룹** 에 **MyResourceGroup** 을 입력합니다.

5. **지역** 에 대해 미국 동부를 선택합니다.

6. **검토 + 만들기** 를 선택합니다.

7. **만들기** 를 선택합니다.



## <a name="task-2-create-a-ddos-protection-plan"></a>작업 2: DDoS 보호 계획 만들기

1. Azure Portal 홈페이지에서 검색 상자에 **DDoS** 를 입력한 다음, **DDoS 보호 계획** 이 표시되면 선택합니다.

2. **+ 만들기** 를 선택합니다.

3. **기본 사항** 탭의 **리소스 그룹** 목록에서 방금 만든 리소스 그룹을 선택합니다.

4. **인스턴스 이름** 상자에 **MyDdoSProtectionPlan** 을 입력한 다음, **검토 + 만들기** 를 선택합니다.

5. **만들기** 를 선택합니다.

 

## <a name="task-3-enable-ddos-protection-on-a-new-virtual-network"></a>작업 3: 새 가상 네트워크에서 DDoS Protection을 사용하도록 설정

여기서는 기존 가상 네트워크가 아니라 새 가상 네트워크에서 DDoS를 사용하도록 설정하므로, 먼저 새 가상 네트워크를 만든 다음 이전에 만든 계획을 사용하여 DDoS 보호를 사용하도록 설정해야 합니다.

1. Azure Portal 홈페이지에서 **리소스 만들기** 를 선택하고, 검색 상자에 **가상 네트워크** 를 입력한 다음, **가상 네트워크** 가 표시되면 선택합니다.

2. **Virtual Network** 페이지에서 **생성** 를 선택합니다.

3. **기본 사항** 탭에서 이전에 만든 리소스 그룹을 선택합니다.

4. **이름** 상자에 **MyVirtualNetwork** 를 입력한 다음, **보안** 탭을 클릭합니다. 

5. **보안** 탭의 **DDoS Protection Standard** 옆에서 **사용** 을 선택합니다.

6. **DDoS 보호 계획** 드롭다운 목록에서 **MyDdosProtectionPlan** 을 선택합니다.

   ![가상 네트워크 만들기 - 보안 탭](../media/create-virtual-network-security-for-ddos-protection.png)

7. **검토 + 만들기** 를 클릭합니다.

8. **만들기** 를 클릭합니다.

 

## <a name="task-4-configure-ddos-telemetry"></a>작업 4: DDoS 원격 분석 구성

공용 IP 주소를 만들고, 다음 단계에서 원격 분석을 설정합니다.

1. Azure Portal 홈페이지에서 **리소스 만들기** 를 선택하고, 검색 상자에 **공용 IP** 를 입력한 다음, **공용 IP 주소** 가 표시되면 클릭합니다.

2. **공용 IP 주소** 페이지에서 **만들기** 를 클릭합니다.

3. **공용 IP 주소 만들기** 페이지의 **SKU** 아래에서 **기본** 을 선택합니다.

4. **이름** 상자에 **MyPublicIPAddress** 를 입력합니다.

5. **IP 주소 할당** 아래에서 **고정** 을 선택합니다.

6. **DNS 이름 레이블** 에 **mypublicdnsxx** 를 입력합니다(여기서 xx는 이 레이블을 고유하게 만드는 사용자 이니셜임).

7. 목록에서 해당 리소스 그룹을 선택합니다.

   ![공용 IP 주소 만들기](../media/create-public-ip-address-for-ddos-telemetry.png)

8. **만들기** 를 클릭합니다.

9. Azure 홈페이지에서 **모든 리소스** 를 클릭합니다.

10. 리소스 목록에서 **MyDdosProtectionPlan** 을 클릭합니다.

11. **모니터링** 아래에서 **메트릭** 을 선택합니다.

12. **범위** 상자를 선택하고 **MyPublicIPAddress** 옆에 있는 확인란을 선택합니다.

    ![DDoS 원격 분석에 대한 메트릭 범위 만들기](../media/create-metrics-scope-for-ddos-telemetry.png)

13. **적용** 을 클릭합니다.

14. **메트릭** 상자에서 **Inbound packets dropped DDoS** 를 선택합니다.

15. **집계** 상자에서 **최댓값** 을 선택합니다.

    ![DDoS 원격 분석을 위해 만든 메트릭](../media/metrics-created-for-ddos-telemetry.png)

 

## <a name="task-5-configure-ddos-diagnostic-logs"></a>작업 5: DDoS 진단 로그 구성

1. Azure 홈페이지에서 **모든 리소스** 를 클릭합니다.

2. 리소스 목록에서 **MyPublicIPAddress** 를 클릭합니다.

3. **모니터링** 아래에서 **진단 설정** 을 선택합니다.

4. **진단 설정 추가** 를 클릭합니다. 

5. **진단 설정** 페이지의 **진단 설정 이름** 상자에 **MyDiagnosticSetting** 을 입력합니다. 

6. **범주 세부 정보** 에서 3개의 **로그** 확인란 모두와 **AllMetrics** 확인란을 선택합니다.

7. **대상 세부 정보** 에서 **Log Analytics 작업 영역에 보내기** 확인란을 선택합니다. 여기에서 기존 Log Analytics 작업 영역을 선택할 수 있지만 진단 로그에 대한 대상을 아직 설정하지 않았으므로, 설정을 입력하고 이 연습의 다음 단계에서 삭제하겠습니다.

   ![DDoS에 대한 새 진단 설정 구성](../media/configure-ddos-diagnostic-settings-new.png)

8. 일반적으로 **저장** 을 클릭하여 진단 설정을 저장할 수 있습니다. 그러나 아직 설정 구성을 완료할 수 없기 때문에 이 옵션은 회색으로 표시됩니다.

9. **취소** 를 클릭한 다음, **예** 를 클릭합니다.

 

## <a name="task-6-configure-ddos-alerts"></a>작업 6: DDoS 경고 구성

이 단계에서는 가상 머신을 만들고, 공용 IP 주소를 할당하고, DDoS 경고를 구성합니다.

### <a name="create-the-vm"></a>VM 만들기

1. Azure Portal 홈페이지에서 **리소스 만들기** 를 선택하고, 검색 상자에 **가상 머신** 을 입력한 다음, **가상 머신** 이 표시되면 클릭합니다.

2. **가상 머신** 페이지에서 **만들기** 를 클릭합니다.

3. **기본 사항** 탭에서 아래 표의 정보를 사용하여 새 VM을 만듭니다.

   | **설정**           | **값**                                                    |
   | --------------------- | ------------------------------------------------------------ |
   | Subscription          | 구독 선택                                     |
   | Resource group        | **MyResourceGroup**                                          |
   | 가상 머신 이름  | **MyVirtualMachine**                                         |
   | 지역                | 지역                                                  |
   | 가용성 옵션  | **인프라 중복은 필요하지 않음**                   |
   | 이미지                 | **Ubuntu Server 18.04 LTS -  Gen 1**(필요한 경우 VM 세대 구성 링크를 선택) |                     
   | 크기                  | **모든 크기 보기** 를 선택한 다음, 목록에서 **B1ls** 를 선택하고 **선택** **(Standard_B1ls - vcpu 1개, 메모리 0.5GiB**)을 선택합니다. |
   | 인증 유형   | **SSH 퍼블릭 키**                                           |
   | 사용자 이름              | **azureuser**                                                |
   | SSH 공개 키 원본 | **새 키 쌍 생성**                                    |
   | 키 쌍 이름         | **myvirtualmachine-ssh-key**                                 |


4. **검토 + 만들기** 를 클릭합니다.

5. **만들기** 를 클릭합니다.

6. **새 키 쌍 생성** 대화 상자에서 **프라이빗 키 다운로드 및 리소스 만들기** 를 클릭합니다.

7. 프라이빗 키를 저장합니다.

8. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.

### <a name="assign-the-public-ip-address"></a>공용 IP 주소 할당

1. 새 가상 머신의 **개요** 페이지에 있는 **설정** 아래에서 **네트워킹** 을 클릭합니다.

2. **네트워크 인터페이스** 옆에서 **myvirtualmachine-nic** 를 클릭합니다. nic의 이름이 다를 수 있습니다.

3. **설정** 아래에서 **IP 구성** 을 클릭합니다.

4. **ipconfig1** 을 선택합니다.

5. **공용 IP 주소** 목록에서 **MyPublicIPAddress** 를 선택합니다.

6. **저장** 을 클릭합니다.

   ![DDoS VM의 공용 IP 주소 변경](../media/change-public-ip-config-for-ddos-vm-new.png)

### <a name="configure-ddos-alerts"></a>DDoS 경고 구성

1. Azure 홈페이지에서 **모든 리소스** 를 클릭합니다.

2. 리소스 목록에서 **MyDdosProtectionPlan** 을 클릭합니다.

3. **모니터링** 에서 **경고** 를 선택합니다.

4. **새 경고 규칙** 을 클릭합니다.

5. **경고 규칙 만들기** 페이지의 **범위** 아래에서 **리소스 편집** 을 클릭합니다.

6. **리소스 선택** 창의 **리소스 종류별로 필터링** 상자에서 목록 아래로 스크롤하여 **공용 IP 주소** 를 선택합니다.

   ![공용 IP 주소에 대한 새 경고 규칙 변경 범위](../media/new-alert-rule-change-scope-to-public-ip-address-1.png)

7. **리소스** 목록에서 **MyPublicIPAddress** 를 선택한 다음, **완료** 를 클릭합니다.

8. **경고 규칙 만들기** 페이지의 **조건** 아래에서 **조건 추가** 를 클릭합니다.

9. **Under DDoS attack or not**(DDoS 공격 진행 여부)을 선택합니다.

   ![경고 규칙에 조건 추가 - 신호 선택](../media/add-condition-to-alert-rule-1.png)

10. **연산자** 상자에서 **크거나 같음** 을 선택합니다.

11. **임계값** 에 **1** 을 입력합니다(공격이 진행 중임을 의미).

12. **완료** 를 클릭합니다.

    ![경고 규칙에 조건 추가 - 신호 논리 구성](../media/add-condition-to-alert-rule-2.png)

13. **경고 규칙 만들기** 페이지로 돌아가서 **경고 규칙 세부 정보** 섹션까지 아래로 스크롤하고, **경고 규칙 이름** 에 **MyDdosAlert** 를 입력합니다.

    ![새 경고 규칙 만들기의 엔드포인트](../media/new-alert-rule-end.png)

14. **경고 규칙 만들기** 를 클릭합니다.

 

## <a name="task-7-submit-a-ddos-service-request-to-run-a-ddos-attack"></a>작업 7: DDoS 서비스 요청을 제출하여 DDoS 공격 실행

1. [BreakingPoint Cloud](https://breakingpoint.cloud/)를 사용하여 계정 만들기

2. 아래 스크린샷의 설정에 따라 DDoS 테스트를 설정하되(평가판 계정으로 100k pps 테스트 크기를 선택해야 할 수 있음) **대상 IP 주소** 상자에서 고유한 **MyPublicIPAddress** 리소스의 IP 주소(예: **51.140.137.219**)를 지정합니다. ![DDOSAttack](https://user-images.githubusercontent.com/46939028/138599420-58bef33a-2597-4fa2-919f-bf1614037bc3.JPG)

   ![DDoS 테스트 설정](../media/ddos-test-setup.png)

3. Azure Portal 홈페이지에서 **모든 리소스** 를 클릭합니다.

4. 리소스 목록에서 **MyPublicIPAddress** 리소스를 클릭한 다음 **모니터링** 아래에서 **메트릭** 을 클릭합니다. 

5. **메트릭** 상자의 목록에서 **Under DDoS attack or not**(DDoS 공격 진행 여부)을 선택합니다.

6. 여기서는 DDoS 공격이 발생한 것을 볼 수 있습니다. 결과가 표시되는 데 10분이 걸릴 수 있습니다.

   ![DDoS 공격을 받는 리소스를 보여 주는 메트릭](../media/metrics-showing-resource-under-attack.png)

 
## <a name="task-8-clean-up-resources"></a>작업 8: 리소스 정리

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

1. Azure Portal의 **Cloud Shell** 창에서 **PowerShell** 세션을 엽니다.

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

   ```powershell
   Remove-AzResourceGroup -Name 'MyResourceGroup' -Force -AsJob
   ```

    >**참고**: 이 명령은 -AsJob 매개 변수에 의해 결정되어 비동기로 실행되므로, 동일한 PowerShell 세션 내에서 이 명령을 실행한 직후 다른 PowerShell 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지는 몇 분 정도 걸립니다.
