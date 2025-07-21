---
demo:
  module: Module 04 - Load balancing non-HTTPS traffic
  title: Load Balancer(모듈 04)
---
## Azure Load Balancer 구성

이 데모에서는 공용 부하 분산 장치를 만드는 방법을 알아봅니다. 

**참조**: [빠른 시작: Azure Portal을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](https://learn.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)

**참고:**  이 데모를 진행하려면 하나 이상의 서브넷이 포함된 가상 네트워크가 필요합니다. 

**포털의 선택 도움말 기능 표시**

1. Azure Portal 액세스:

1. **부한 분산 - 선택 도움말**을 검색하고 선택합니다.

1. 마법사를 사용하여 다양한 시나리오를 연습합니다.
   
**부하 분산 장치 만들기**

1. Azure Portal에서 계속 진행합니다.

1.  **부하 분산 장치**를 검색하고 선택합니다. 부하 분산 장치를 **만듭니다**. 

1. **기본 사항** 탭에서는 **SKU**, **유형** 및 **계층**에 대해 설명합니다.

1. **프런트 엔드 IP 구성** 탭에서는 공용 IP 주소 사용에 대해 설명합니다.

1. **백 엔드 풀** 탭에서는 IP 주소 범위가 있는 가상 네트워크를 선택합니다.

1. **인바운드 규칙** 탭에서는 부하 분산 규칙을 만듭니다. **프로토콜**, **포트**, **상태 프로브**, **세션 지속성** 등의 매개 변수에 대해 논의합니다. 


