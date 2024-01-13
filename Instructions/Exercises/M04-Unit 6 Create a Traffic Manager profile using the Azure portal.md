---
Exercise:
  title: M04 - 단원 6 Azure Portal을 사용하여 Traffic Manager 프로필 만들기
  module: Module 04 - Load balancing non-HTTP(S) traffic in Azure
---

# M04-Unit 6 Azure Portal을 사용하여 Traffic Manager 프로필 만들기

이 연습에서는 가상의 Contoso Ltd 조직 웹 애플리케이션에 고가용성을 제공하는 Traffic Manager 프로필을 만듭니다. 

**참고:** **[대화형 랩 시뮬레이션](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20a%20Traffic%20Manager%20profile%20using%20the%20Azure%20portal)** 을 사용하여 이 랩을 원하는 속도로 클릭할 수 있습니다. 대화형 시뮬레이션과 호스트된 랩 간에 약간의 차이가 있을 수 있지만 보여주는 핵심 개념과 아이디어는 동일합니다.

#### 예상 시간: 35분

서로 다른 두 지역(미국 동부와 서유럽)에 배포된 두 개의 웹 애플리케이션 인스턴스가 필요합니다. 미국 동부 지역은 Traffic Manager의 주 엔드포인트로, 서유럽 지역은 장애 조치(failover) 엔드포인트로 작동합니다.

엔드포인트 우선 순위를 기준으로 Traffic Manager 프로필을 만듭니다. 이 프로필은 웹 애플리케이션을 실행하는 주 사이트로 사용자 트래픽을 보냅니다. Traffic Manager는 웹 애플리케이션을 지속적으로 모니터링하고, 미국 동부의 주 사이트를 사용할 수 없는 경우 서유럽의 백업 사이트에 자동 장애 조치(failover)를 제공합니다.

아래의 다이어그램에는 이 연습에서 배포할 환경이 대략적으로 나와 있습니다.

    ![Picture 14](../media/exercise-traffic-manager-environment-diagram.png)

 이 연습에서 다음을 수행합니다.

+ 작업 1: 웹앱 만들기
+ 작업 2: Traffic Manager 프로필 만들기
+ 작업 3: Traffic Manager 엔드포인트 추가
+ 작업 4: Traffic Manager 프로필 테스트
+ 작업 5: 리소스 정리


## 작업 1: 웹앱 만들기

이 섹션에서는 서로 다른 두 Azure 지역에 배포된 두 개의 웹 애플리케이션 인스턴스를 만듭니다.

1. Azure Portal 홈페이지에서 **리소스 만들기**를 선택한 다음, **웹앱**을 선택합니다. 이 리소스 종류가 페이지에 나열되지 않은 경우 페이지 맨 위에 있는 검색 상자를 사용하여 검색하고 선택합니다.

1. **웹앱 만들기** 페이지의 **기본** 탭에서 아래 표의 정보를 사용하여 첫 번째 웹 애플리케이션을 만듭니다.

   | **설정**      | **값**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Subscription     | 구독 선택                                     |
   | Resource group   | **새로 만들기** 선택  이름: **Contoso-RG-TM1**             |
   | 속성             | **ContosoWebAppEastUSxx**(여기서 xx는 이름을 고유하기 만들어주는 이니셜임) |
   | 게시          | **코드**                                                     |
   | 런타임 스택    | **ASP.NET V4.8**                                             |
   | 운영 체제 | **Windows**                                                  |
   | 지역           | **미국 동부**                                                  |
   | Windows 플랜     | **새로 만들기** 선택  이름: **ContosoAppServicePlanEastUS** |
   | 요금제     | **표준 S1 총 ACU 100개, 메모리 1.75GB**               |


1. 모니터링 탭을** 선택합니다**.

1. **모니터링** 탭의 **Application Insights 사용**에서 **아니요** 옵션을 선택합니다.

1. **검토 + 만들기**를 선택합니다.

   ![그림 18](../media/create-web-app-1.png)

1. **만들기**를 선택합니다. 웹앱이 성공적으로 배포되면, 그것은 기본 웹 사이트를 만듭니다.

1. 위의 1~6단계를 반복하여 두 번째 웹앱을 만듭니다. 아래 표의 정보를 제외하고 이전과 동일한 설정을 사용합니다. 

   | **설정**    | **값**                                                    |
   | -------------- | ------------------------------------------------------------ |
   | Resource group | **새로 만들기** 선택  이름: **Contoso-RG-TM2**             |
   | 속성           | **ContosoWebAppWestEuropexx**(여기서 xx는 이름을 고유하기 만들어주는 이니셜임)  |
   | 지역         | **서유럽**                                              |
   | Windows 플랜   | **새로 만들기** 선택  이름: **ContosoAppServicePlanWestEurope** |


1. Azure 홈페이지의 왼쪽 탐색 메뉴에서 **모든 서비스**를 선택하고, **웹**을 선택한 다음, **App Services**를 선택합니다.

1. 두 개의 새로운 웹앱이 나열되어야 합니다.

   ![그림 19](../media/create-web-app-2.png)

 

## 작업 2: Traffic Manager 프로필 만들기

엔드포인트 우선 순위에 따라 사용자 트래픽을 보내는 Traffic Manager 프로필을 만듭니다.

1. Azure Portal 홈페이지에서 **리소스 만들기**를 선택합니다.

1. 페이지 위쪽의 검색 상자에 Traffic Manager 프로필을** 입력**한 다음 팝업 목록에서 선택합니다.

   ![그림 20](../media/create-tmprofile-1.png)

1. **만들기**를 선택합니다.

1. **Traffic Manager 프로필 만들기** 페이지에서 아래 표의 정보를 사용하여 Traffic Manager 프로필을 만듭니다.

   | **설정**             | **값**                |
   | ----------------------- | ------------------------ |
   | Name                    | **Contoso-TMProfilexx**(여기서 xx는 이름을 고유하기 만들어주는 이니셜임) |
   | 라우팅 방법          | **우선 순위**             |
   | Subscription            | 구독 선택 |
   | Resource group          | **Contoso-RG-TM1**       |
   | 리소스 그룹 위치 | **미국 동부**              |


1. **만들기**를 선택합니다.

 

## 작업 3: Traffic Manager 엔드포인트 추가

이 섹션에서는 모든 사용자 트래픽을 라우팅하는 주 엔드포인트로 미국 동부의 웹 사이트를 추가합니다. 그런 다음 장애 조치(failover) 엔드포인트로 유럽 서부의 웹 사이트를 추가합니다. 주 엔드포인트를 사용할 수 없게 되면 트래픽이 장애 조치(failover) 엔드포인트로 자동 라우팅됩니다.

1. Azure Portal 홈페이지에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **Contoso-TMProfile**을 선택합니다.

1. **설정**에서 **엔드포인트**를 선택한 다음, **추가**를 선택합니다.

   ![그림 21](../media/create-tmendpoints-1.png)

1. **엔드포인트 추가** 페이지에 아래 표의 정보를 입력합니다.

   | **설정**          | **값**                         |
   | -------------------- | --------------------------------- |
   | 유형                 | **Azure 엔드포인트**                |
   | 속성                 | **myPrimaryEndpoint**             |
   | 대상 리소스 종류 | **App Service**                   |
   | 대상 리소스      | **ContosoWebAppEastUS(미국 동부)** |
   | 우선 순위             | **1**                             |


1. **추가**를 선택합니다.

1. 위의 2~4단계를 반복하여 장애 조치(failover) 엔드포인트를 만듭니다. 아래 표의 정보를 제외하고 이전과 동일한 설정을 사용합니다. 

   | **설정**     | **값**                                 |
   | --------------- | ----------------------------------------- |
   | 속성            | **myFailoverEndpoint**                    |
   | 대상 리소스 | **ContosoWebAppWestEurope(유럽 서부)** |
   | 우선 순위        | **2**                                     |


1. 우선 순위를 2로 설정하면 구성된 주 엔드포인트가 비정상 상태가 될 경우 해당 트래픽이 이 장애 조치(failover) 엔드포인트로 라우팅됩니다.

1. 설정 구성**을 선택한 **다음 엔드포인트 모니터 설정 **프로토콜**을 HTTPS 및 포트**로 업데이트하고 **저장**을 선택합니다**.****

1. 두 개의 새 엔드포인트가 Traffic Manager 프로필에 표시됩니다. 몇 분 뒤 **모니터링 상태**가 **온라인**으로 변경되어야 합니다.

   ![그림 22](../media/create-tmendpoints-2.png)

 

## 작업 4: Traffic Manager 프로필 테스트

이 섹션에서는 Traffic Manager 프로필의 DNS 이름을 확인하고 나서 주 엔드포인트를 사용할 수 없도록 구성합니다. 그런 다음 웹앱을 계속 사용할 수 있는지 확인하여 Traffic Manager 프로필이 장애 조치(failover) 엔드포인트로 트래픽을 전송하는지 테스트합니다.

1. **Contoso-TMProfile** 페이지에서 **개요**를 선택합니다.

1. **개요** 화면에서 **DNS 이름** 항목을 클립보드에 복사하거나 다른 곳에 메모해 둡니다.

   ![그림 23](../media/check-dnsname-1.png)

1. 웹 브라우저 탭을 열고 **DNS 이름** 항목(contoso-tmprofile.trafficmanager.net)을 주소 표시줄에 붙여넣거나 입력한 다음 Enter 키를 누릅니다.

1. 웹앱의 기본 웹 사이트가 표시됩니다. **404 웹 사이트를 찾을 수 없음** 메시지가 표시되면 **Contoso-TMProfilexx** Traffic Manager 프로필 개요 페이지에서 **프로필 비활성화**를 설정하고 **프로필 활성화**를 설정합니다. 그런 다음 웹 페이지를 새로 고침합니다.

   ![그림 24](../media/tm-webapp-test-1a.png)

1. 현재 **우선 순위**를 **1**로 설정하면 모든 트래픽이 주 엔드포인트로 전송됩니다.

1. 장애 조치(failover) 엔드포인트가 제대로 작동하는지 테스트하려면 주 사이트를 사용하지 않도록 설정해야 합니다.

1. **Contoso-TMProfile** 페이지의 개요 화면에서 **myPrimaryEndpoint**를 선택합니다.

1. **MyPrimaryEndpoint** 페이지의 **상태**에서 **사용 안 함**을 선택한 다음, **저장**을 선택합니다.

   ![그림 25](../media/disable-primary-endpoint-1.png)

1. **myPrimaryEndpoint** 페이지를 닫습니다(페이지 오른쪽 위에 있는 **X** 선택).

1. 이제 **Contoso-TMProfile** 페이지에 있는 **myPrimaryEndpoint**의 **모니터 상태**가 **사용 안 함**이 됩니다.

1. 새 웹 브라우저 세션을 열고 주소 표시줄에 **DNS 이름** 항목(contoso-tmprofile.trafficmanager.net)을 붙여넣거나 입력한 후 Enter 키를 누릅니다.

1. 웹앱이 계속 응답하는지 확인합니다. 주 엔드포인트를 사용할 수 없기 때문에 웹 사이트가 계속 작동할 수 있도록 트래픽이 대신 장애 조치(failover) 엔드포인트로 라우팅되었습니다.

 
 ## 작업 5: 리소스 정리

   >**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

1. Azure Portal에서 Cloud Shell 창 내에서 **PowerShell** 세션을 엽니다**.**

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

   ```powershell

   Remove-AzResourceGroup -Name 'Contoso-RG-TM1' -Force -AsJob
   Remove-AzResourceGroup -Name 'Contoso-RG-TM2' -Force -AsJob

   ```

    >**참고**: 이 명령은 -AsJob 매개 변수에 의해 결정되어 비동기로 실행되므로, 동일한 PowerShell 세션 내에서 이 명령을 실행한 직후 다른 PowerShell 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지는 몇 분 정도 걸립니다.
 

