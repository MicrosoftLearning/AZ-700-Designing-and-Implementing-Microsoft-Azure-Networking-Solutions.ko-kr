---
demo:
  title: 모듈 05 - Application Gateway
  module: Module 05 - Load balancing HTTPS traffic
---
## Azure Application Gateway 구성

이 데모에서는 Azure Application Gateway를 만드는 방법을 알아봅니다. 

**참조**: [빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure Portal](https://learn.microsoft.com/azure/application-gateway/quick-create-portal)

**참조**: [Azure Application Gateway를 사용하여 네트워크 트래픽 엔드투엔드 암호화](https://github.com/MicrosoftDocs/mslearn-end-to-end-encryption-with-app-gateway)

**참고**: 작업을 간단하게 유지하려면 구성을 진행하면서 새 가상 네트워크 및 서브넷을 만듭니다. 

**Azure Application Gateway 만들기**

1. Azure Portal에 액세스합니다.

1. **Application Gateway**를 검색하여 선택합니다.

1. 새 게이트웨이를 **만듭니다**.

1. **기본 사항** 탭에서 **계층**, **자동 크기 조정** 및 **인스턴스 수**에 대해 설명합니다.

1. **프런트 엔드** 탭에서 IP 주소 유형에 대해 설명합니다.

1. **백 엔드 탭**에서 빈 백 엔드 풀을 사용하는 시기를 설명합니다.

1. **구성 탭**에서 라우팅 규칙을 설명합니다. 부하 분산 장치 규칙과 비교합니다.

1. 게이트웨이를 만든 후 백 엔드 대상을 추가하고 테스트한다고 설명합니다. 
