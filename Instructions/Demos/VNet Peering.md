---
demo:
  title: 'VNET 피어링            '
  module: Module 01 - Introduction to Azure Virtual Networks
---
## VNet 피어링 구성

**참고:** 이 데모에서는 두 개의 가상 네트워크가 필요합니다.

**참조**: [VNet 피어링을 사용하여 가상 네트워크 연결 - 자습서](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal)

**첫 번째 가상 네트워크에서 VNet 피어링 구성**

1.  **Azure Portal**에서 첫 번째 가상 네트워크를 선택합니다. 피어링 값을 검토합니다. 

1.  **설정**에서  **피어링**을 선택하고 새 피어링을 **+ 추가**합니다.

1. 두 번째 가상 네트워크 피어링 구성 정보 아이콘을 사용하여 다른 설정을 검토합니다. 

1. 피어링이 완료되면 **피어링 상태**를 검토합니다. 

**두 번째 가상 네트워크에서 VNet 피어링 확인**

1.  **Azure Portal**에서 두 번째 가상 네트워크를 선택합니다.

1.  **설정**에서  **피어링**을 선택합니다.

1. 피어링이 자동으로 만들어졌습니다.  **피어링 상태**가  **연결됨**인지 확인합니다.

1. 랩에서 학생들은 피어링을 만들고 가상 머신 간의 연결을 테스트합니다. 
