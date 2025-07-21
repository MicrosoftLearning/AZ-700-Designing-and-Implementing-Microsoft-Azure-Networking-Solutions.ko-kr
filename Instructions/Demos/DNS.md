---
demo:
  module: Module 01 - Introduction to Azure Virtual Networks
  title: DNS 이름 확인(모듈 01)
---
## Azure DNS 구성

본 데모에서는 Azure DNS에 대해 살펴봅니다.

**참조**: [자습서: 도메인 및 하위 도메인 호스트 - Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

**DNS 영역 만들기**

1. Azure Portal에 액세스합니다.

1.  **DNS 영역**  서비스를 검색합니다.

1. **DNS 영역**을 만들고 영역의 용도를 설명합니다. 이름에 contoso.internal.com을 사용할 수 있습니다.

1.  DNS 영역이 만들어질 때까지 기다립니다. 페이지를  **새로고침** 해야 할 수도 있습니다.

**DNS 레코드 집합 추가**

**참조**: [자습서: 영역 리소스 레코드를 참조하는 별칭 레코드 만들기](https://learn.microsoft.com/azure/dns/tutorial-alias-rr)

1. 영역이 만들어지면  **+레코드 집합**을 선택합니다.

1.  **종류**  드롭다운을 사용하여 다양한 종류의 레코드를 볼 수 있습니다. 다양한 레코드 종류가 사용되는 방법을 검토합니다. 레코드 종류를 변경할 때 레코드 정보가 어떻게 변경되는지 확인합니다.

1. **A** 레코드를 예로 만듭니다. 

