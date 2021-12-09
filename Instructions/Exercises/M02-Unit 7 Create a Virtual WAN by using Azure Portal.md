---
Exercise:
    title: 'M02-단원 7 Azure Portal을 사용하여 Virtual WAN 만들기'
    module: '모듈 - 하이브리드 네트워킹 설계 및 구현'
---

# M02-단원 7 Azure Portal을 사용하여 Virtual WAN 만들기


이 연습에서는 Contoso용 Virtual WAN을 만듭니다.

이 연습에서는 다음을 수행합니다.

+ 작업 1: Virtual WAN 만들기
+ 작업 2: Azure Portal을 사용하여 허브 만들기
+ 작업 3: 가상 허브에 VNet 연결
+ 작업 4: 리소스 정리



## 작업 1: Virtual WAN 만들기

1. 브라우저에서 Azure Portal로 이동하고 Azure 계정으로 로그인합니다.

2. 포털의 검색 상자에 Virtual WAN을 입력한 후 결과 목록에서 **Virtual WAN**을 선택합니다.

   ![Azure Portal에서 Virtual WAN 검색](../media/search-for-virtual-wan.png)

 

3. Virtual WAN 페이지에서 + **만들기**를 선택합니다. 

4. WAN 만들기 페이지의 **기본 사항** 탭에서 다음 필드에 정보를 입력합니다.

   - **구독:** 기존 구독 사용

   - **리소스 그룹:** ContosoResourceGroup

   - **리소스 그룹 위치:** 드롭다운에서 리소스 위치를 선택합니다. WAN은 전역 리소스이며 특정 지역에 상주하지 않습니다. 그러나 사용자가 만든 WAN 리소스를 관리하고 찾으려면 지역을 선택해야 합니다.

   - **이름:** ContosoVirtualWAN

   - **유형:** 표준 

5. 필드 작성을 완료하고 **검토 + 만들기**를 선택합니다.

6. 유효성 검사를 통과하면 **만들기**를 선택하여 Virtual WAN을 만듭니다.

## 작업 2: Azure Portal을 사용하여 허브 만들기

허브에는 사이트 간, ExpressRoute 또는 지점 및 사이트 간 기능 사용을 위한 게이트웨이가 포함됩니다. 가상 허브에서 사이트 간 VPN Gateway를 만드는 데 30분이 소요됩니다. 먼저 Virtual WAN을 만들어야 허브를 만들 수 있습니다.

1. 만든 Virtual WAN을 찾습니다. 
2. Virtual WAN 페이지의 **연결** 아래에서 **허브**를 선택합니다.
3. 허브 페이지에서 **+새 허브**를 선택하여 가상 허브 만들기 페이지를 엽니다.
   ![가상 허브 만들기 기본 사항 탭](../media/create-vwan-hub.png)
4. 가상 허브 만들기 페이지의 **기본 사항** 탭에서 다음 필드를 작성합니다.
   - **지역:** 미국 서부
   - **이름:** ContosoVirtualWANHub-WestUS
   - **허브 프라이빗 주소 공간:** 10.60.0.0/24
5. **다음: 사이트 간**을 선택합니다.
6. **사이트 간** 탭에서 다음 필드를 작성합니다.
   - **사이트 대 사이트(VPN Gateway)를 만드시겠습니까?:** 예
   - **AS 번호** 필드는 편집할 수 없습니다.
   - **게이트웨이 배율 단위** 1 배율 단위 = 500Mbps
7. **검토 + 만들기**를 선택하여 유효성을 검사합니다.
8. **만들기**를 선택하여 허브를 만듭니다. 
9. 30분 후에 **새로 고침**을 선택하여 허브 페이지에서 허브를 확인합니다. 

## 작업 3: 가상 허브에 VNet 연결

1. 만든 Virtual WAN을 찾습니다. 

2. ContosoVirtualWAN의 **연결** 아래에서 **가상 네트워크 연결**을 선택합니다.

   ![가상 네트워크 연결이 강조 표시된 Virtual WAN 구성 페이지](../media/connect-vnet-to-virtual-hub.png)

3. ContosoVirtualWAN | 가상 네트워크 연결에서 **+ 연결 추가**를 선택합니다.

4. 연결 추가에서 다음 정보를 사용하여 연결을 만듭니다.

   - **연결 이름:** ContosoVirtualWAN-to-ResearchVNet

   - **허브:** ContosoVirtualWANHub-WestUS

   - **구독:** 변경 안 함

   - **리소스 그룹:** ContosoResourceGroup

   - **가상 네트워크:** ResearchVNet

   - **없음으로 전파:** 예

   - **경로 테이블 연결:** 기본값

5. **만들기**를 선택합니다.

 

축하합니다! Virtual WAN과 Virtual WAN 허브를 만들고 허브에 ResearchVNet을 연결했습니다.

## 작업 4: 리소스 정리

   >**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스를 제거해야 합니다. 사용하지 않는 리소스를 제거하면 예기치 않은 비용이 발생하지 않습니다.

1. Azure Portal에서 **Cloud Shell** 창 내의 **PowerShell** 세션을 엽니다.

1. 다음 명령을 실행하여 이 모듈의 전체 랩에서 만든 모든 리소스 그룹을 삭제합니다.

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```

    >**참고**: 명령은 비동기적으로 실행되므로(-AsJob 매개 변수에 의해 결정됨) 동일한 PowerShell 세션 내에서 즉시 다른 PowerShell 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸릴 것입니다.
