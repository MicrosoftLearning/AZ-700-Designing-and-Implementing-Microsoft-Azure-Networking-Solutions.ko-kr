---
Exercise:
  title: M03 - 단원 4 ExpressRoute 게이트웨이 구성
  module: Module 03 - Design and implement Azure ExpressRoute
---
# M03-단원 4 ExpressRoute 게이트웨이 구성

## 연습 시나리오

![가상 네트워크 게이트웨이 다이어그램.](../media/4-exercise-configure-expressroute-gateway.png)

ExpressRoute를 통해 Azure 가상 네트워크와 온-프레미스 네트워크를 연결하려면 먼저 가상 네트워크 게이트웨이를 만들어야 합니다. 가상 네트워크 게이트웨이는 네트워크 간에 IP 경로를 교환하고 네트워크 트래픽을 라우팅하는 두 가지 용도로 사용됩니다.

### 대화형 랩 시뮬레이션

>**참고**: 이전에 제공되었던 랩 시뮬레이션은 사용 중지되었습니다.

### 예상 시간: 60분(배포 대기 시간 ~45분 포함)

**게이트웨이 유형**

가상 네트워크 게이트웨이를 만들 때 몇 가지 설정을 지정해야 합니다. 필수 설정 중 하나인 '-GatewayType'은 게이트웨이를 ExpressRoute에 사용할 것인지 아니면 VPN 트래픽에 사용할 것인지 지정합니다. 두 가지 게이트웨이 유형은 다음과 같습니다.

- **Vpn** - 퍼블릭 인터넷을 통해 암호화된 트래픽을 전송하려면 ‘VPN’ 유형의 게이트웨이를 사용합니다. 이를 VPN 게이트웨이라고도 합니다. 사이트 간, 지점 및 사이트 간, VNet 간 연결은 모두 VPN Gateway를 사용합니다.
- **ExpressRoute** - 프라이빗 연결을 통해 네트워크 트래픽을 전송하려면 'ExpressRoute' 유형의 게이트웨이를 사용합니다. 이를 ExpressRoute 게이트웨이라고도 하며 ExpressRoute를 구성할 때 사용되는 게이트웨이 유형입니다.

각각의 가상 네트워크에는 게이트웨이 유형당 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다. 예를 들어 -GatewayType VPN을 사용하는 하나의 가상 네트워크 게이트웨이와 -GatewayType ExpressRoute를 사용하는 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다.

### 작업 기술

이 연습에서 다음을 수행합니다.

- 작업 1: VNet 및 게이트웨이 서브넷 만들기
- 작업 2: 가상 네트워크 게이트웨이 만들기

## 작업 1: VNet 및 게이트웨이 서브넷 만들기

1. 아무 Azure Portal 페이지에서나 **리소스, 서비스 및 문서 검색**에 가상 네트워크를 입력하고 결과에서 **가상 네트워크**를 선택합니다.

1. 가상 네트워크 페이지에서 **+만들기**를 선택합니다.

1. 가상 네트워크 만들기 창의 **기본 사항** 탭에서 다음 표의 정보를 사용하여 VNet을 만듭니다.

   | **설정**          | **값**                        |
   | -------------------- | -------------------------------- |
   | Virtual Network 이름 | CoreServicesVNet                 |
   | 리소스 그룹       | ContosoResourceGroup             |
   | 위치             | 미국 동부                          |

1. **다음: IP 주소**를 선택합니다.

1. **IP 주소** 탭의 **IPv4 주소 공간**에 10.20.0.0/16을 입력한 다음 **+ 서브넷 추가**를 선택합니다.

1. 서브넷 추가 창에서 다음 표의 정보를 사용하여 서브넷을 만듭니다.

   | **설정**                  | **값**               |
   | ---------------------------- | ----------------------- |
   | 서브넷 목적               | 가상 네트워크 게이트웨이 |
   | 게이트웨이 서브넷 주소 공간 | 10.20.0.0/27            |

서브넷 이름은 자동으로 채워집니다.

1. 그런 후 **추가**를 선택합니다.

1. 가상 네트워크 만들기 페이지에서 **검토 + 만들기**를 선택합니다.

   ![Azure Portal - 게이트웨이 서브넷 추가](../media/add-gateway-subnet.png)

1. VNet이 유효성 검사를 통과하는지 확인한 다음 **만들기**를 선택합니다.

   >**참고:** 이중 스택 가상 네트워크를 사용 중이며 ExpressRoute를 통해 IPv6 기반 개인 피어링을 사용하려는 경우에는 IP6 주소 공간 추가를 선택하고 IPb6 주소 범위 값을 입력합니다.

## 작업 2: 가상 네트워크 게이트웨이 만들기

1. 아무 Azure Portal 페이지에서나 **리소스, 서비스 및 문서 검색(G+/)** 에 가상 네트워크 게이트웨이를 입력하고 결과에서 **가상 네트워크 게이트웨이**를 선택합니다.

1. 가상 네트워크 게이트웨이 페이지에서 **+만들기**를 선택합니다.

1. **가상 네트워크 게이트웨이 만들기** 페이지에서 다음 표의 정보를 사용하여 게이트웨이를 만듭니다.

   | **설정**               | **값**                  |
   | ------------------------- | -------------------------- |
   | **프로젝트 세부 정보**       |                            |
   | 리소스 그룹            | ContosoResourceGroup       |
   | **인스턴스 세부 정보**      |                            |
   | 속성                      | CoreServicesVnetGateway    |
   | 지역                    | 미국 동부                    |
   | 게이트웨이 유형              | ExpressRoute               |
   | SKU                       | 표준                   |
   | 가상 네트워크           | CoreServicesVNet           |
   | **공용 IP 주소**     |                            |
   | 공용 IP 주소         | 새로 만들기                 |
   | 공용 IP 주소 이름    | CoreServicesVnetGateway-IP |
   | 할당                | 구성할 수 없음           |

1. **검토 + 생성**를 선택합니다.

1. 게이트웨이 구성이 유효성 검사를 통과하는지 확인한 다음, **만들기**를 선택합니다.

1. 배포가 완료되면 **리소스로 이동**을 선택합니다.

   >**참고:** Gateway를 배포하는 데 최대 45분이 걸릴 수 있습니다.


## Copilot을 사용하여 학습 확장

Copilot은 Azure 스크립팅 도구를 사용하는 방법을 익히는 데 도움을 줍니다. 또한 Copilot은 랩에서 다루지 않는 영역이나 추가 정보가 필요한 영역을 지원할 수 있습니다. Edge 브라우저를 열고 Copilot(오른쪽 위)을 선택하거나 *copilot.microsoft.com*으로 이동하세요. 몇 분 정도 시간을 내어 이러한 프롬프트를 사용해 보세요.
+ Azure ExpressRoute는 Virtual WAN과 어떻게 다른가요? 기술을 함께 사용할 수 있나요? 예제를 제공합니다
+ ExpressRoute 공급자 모델과 ExpressRoute Direct 중에서 선택할 때 고려해야 할 사항은 무엇인가요?
+ Azure ExpressRoute SKU 및 해당 기능을 요약하는 테이블을 만듭니다.

## 자기 주도적 학습을 통해 자세히 알아보기

+ [Azure ExpressRoute 소개](https://learn.microsoft.com/training/modules/intro-to-azure-expressroute/) 이 모듈에서는 Azure ExpressRoute의 정의와 제공 기능을 알아봅니다.
+ [ExpressRoute 설계 및 구현](https://learn.microsoft.com/training/modules/design-implement-azure-expressroute/) 이 모듈에서는 Azure ExpressRoute, ExpressRoute Global Reach, ExpressRoute FastPath를 설계하고 구현하는 방법을 알아봅니다.

## 핵심 내용

축하합니다. 랩을 완료했습니다. 이 랩의 주요 내용은 다음과 같습니다. 
+ Azure ExpressRoute를 사용하면 조직이 온-프레미스 네트워크를 Microsoft Azure 및 Microsoft 365 클라우드에 직접 연결할 수 있습니다. Azure ExpressRoute는 Microsoft 파트너가 제공하는 전용 고대역폭 연결을 사용합니다.
+ Microsoft는 최소 99.95%의 ExpressRoute 전용 연결 가용성을 보장합니다. 연결은 비공개이며 전용 선을 통해 이동하며, 제3자가 트래픽을 가로챌 수 없습니다.
+ 온-프레미스 네트워크와 Microsoft 클라우드 간 연결은 CloudExchange 공동 배치, 지점 간 이더넷 연결, Any-to-Any(IPVPN) 연결, ExpressRoute Direct라는 네 가지 방법으로 만들 수 있습니다.
+ ExpressRoute 기능은 SKU(로컬, 표준, 프리미엄)에 의해 결정됩니다. 
