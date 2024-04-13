---
Exercise:
  title: M01 - 단원 4 Azure에서 가상 네트워크 설계 및 구현
  module: Module 01 - Introduction to Azure Virtual Networks
---
# M01-단원 4 Azure에서 가상 네트워크 설계 및 구현

## 연습 시나리오

이제 Azure Portal에서 가상 네트워크를 배포할 준비가 되었습니다.

가상의 조직 Contoso Ltd.에서 인프라 및 애플리케이션을 Azure로 마이그레이션하는 과정에 있다고 가정해봅니다. 본인이 네트워크 엔지니어의 역할을 맡고 있어서 이러한 가상 네트워크의 리소스를 지원하기 위해 세 개의 가상 네트워크와 서브넷을 계획하고 구현해야 합니다.

**참고:** **[대화형 랩 시뮬레이션](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Design%20and%20implement%20a%20virtual%20network%20in%20Azure)** 을 사용하여 이 랩을 원하는 속도로 클릭할 수 있습니다. 대화형 시뮬레이션과 호스트된 랩 간에 약간의 차이가 있을 수 있지만 보여주는 핵심 개념과 아이디어는 동일합니다.

### 예상 소요 시간: 20분

**CoreServicesVnet** 가상 네트워크는 **미국 동부** 지역에 배포됩니다. 이 가상 네트워크에는 가장 많은 수의 리소스가 포함됩니다. 이 네트워크는 VPN 연결을 통해 온-프레미스 네트워크에 연결됩니다. 이 네트워크에는 웹 서비스, 데이터베이스 및 비즈니스 운영에 중요한 기타 시스템이 포함됩니다. 도메인 컨트롤러 및 DNS와 같은 공유 서비스도 여기에 있습니다. 많은 양의 증가가 예상되므로 이 가상 네트워크에는 대용량 주소 공간이 필요합니다.

**ManufacturingVnet** 가상 네트워크는 조직의 제조 시설 위치와 가까운 **서유럽** 지역에 배포됩니다. 이 가상 네트워크에는 제조 시설의 운영을 위한 시스템이 포함됩니다. 조직에서는 시스템의 많은 연결된 내부 디바이스에서 온도와 같은 데이터를 검색할 것으로 예상하므로 확장할 수 있는 IP 주소 공간이 필요합니다.

**ResearchVnet** 가상 네트워크는 조직의 연구/개발 팀 위치와 가까운 **동남아시아** 지역에 배포됩니다. 연구/개발 팀은 이 가상 네트워크를 사용합니다. 이 팀은 확장될 것으로 예상되지 않는 작고 안정적인 리소스 세트를 유지합니다. 또한 업무용 소수의 가상 머신을 위한 소수의 IP 주소가 필요합니다.

![Contoso의 네트워크 레이아웃.
온-프레미스 10.10.0.0/16 ResearchVNet 동남 아시아 10.40.40.0/24 CoreServicesVNet 미국 동부 10.20.0.0/16 ManufacturingVNet 서유럽 10.30.0.0/16
](../media/design-implement-vnet-peering.png)

다음 리소스를 만듭니다.

| **Virtual Network** | **지역**     | **가상 네트워크 주소 공간** | **서브넷**                | **서브넷**    |
| ------------------- | -------------- | --------------------------------- | ------------------------- | ------------- |
| CoreServicesVnet    | 미국 동부        | 10.20.0.0/16                      |                           |               |
|                     |                |                                   | GatewaySubnet             | 10.20.0.0/27  |
|                     |                |                                   | SharedServicesSubnet      | 10.20.10.0/24 |
|                     |                |                                   | DatabaseSubnet            | 10.20.20.0/24 |
|                     |                |                                   | PublicWebServiceSubnet    | 10.20.30.0/24 |
| ManufacturingVnet   | 서유럽    | 10.30.0.0/16                      |                           |               |
|                     |                |                                   | ManufacturingSystemSubnet | 10.30.10.0/24 |
|                     |                |                                   | SensorSubnet1             | 10.30.20.0/24 |
|                     |                |                                   | SensorSubnet2             | 10.30.21.0/24 |
|                     |                |                                   | SensorSubnet3             | 10.30.22.0/24 |
| ResearchVnet        | 동남아시아 | 10.40.0.0/16                      |                           |               |
|                     |                |                                   | ResearchSystemSubnet      | 10.40.0.0/24  |

이러한 가상 네트워크와 서브넷은 기존 리소스를 수용하면서도 예상된 성장을 허용하는 방식으로 구성됩니다. 이러한 가상 네트워크 및 서브넷을 만들어 네트워킹 인프라의 기반을 구축해 보겠습니다.

이 연습에서 다음을 수행합니다.

+ 작업 1: Contoso 리소스 그룹 만들기
+ 작업 2: CoreServicesVnet 가상 네트워크 및 서브넷 만들기
+ 작업 3: ManufacturingVnet 가상 네트워크 및 서브넷 만들기
+ 작업 4: ResearchVnet 가상 네트워크 및 서브넷 만들기
+ 작업 5: VNet 및 서브넷 만들기 확인

## 작업 1: Contoso 리소스 그룹 만들기

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. 홈페이지의 **Azure 서비스**에서 **리소스 그룹**을 선택합니다.  

3. 리소스 그룹에서 **+ 만들기**를 선택합니다.

4. 다음 표의 정보를 사용하여 리소스 그룹을 만듭니다.

| **Tab**         | **옵션**                                 | **값**            |
| --------------- | ------------------------------------------ | -------------------- |
| 기본 사항          | Resource group                             | ContosoResourceGroup |
|                 | 지역                                     | (미국) 미국 동부         |
| 태그            | 변경 필요 없음                        |                      |
| 검토 + 만들기 | 설정을 검토하고 **만들기**를 선택합니다. |                      |

5. 리소스 그룹에서 **ContosoResourceGroup**이 목록에 표시되는지 확인합니다.

## 작업 2: CoreServicesVnet 가상 네트워크 및 서브넷 만들기

1. Azure Portal 홈페이지에서 전역 검색 표시줄로 이동하여 **가상 네트워크**를 검색하고 서비스 아래에서 가상 네트워크를 선택합니다.  ![가상 네트워크에 대한 Azure Portal 홈페이지 전역 검색 표시줄 결과](../media/global-search-bar.PNG)
2. 가상 네트워크 페이지에서 **만들기**를 선택합니다.  ![네트워크 만들기 마법사](../media/create-virtual-network.png)
3. 다음 표의 정보를 사용하여 CoreServicesVnet 가상 네트워크를 만듭니다.  
   ‎기본 IP 주소 공간 제거 또는 덮어쓰기![Azure 가상 네트워크 배포용 IP 주소 구성  ](../media/default-vnet-ip-address-range-annotated.png)

| **Tab**      | **옵션**         | **값**            |
| ------------ | ------------------ | -------------------- |
| 기본 사항       | 리소스 그룹     | ContosoResourceGroup |
|              | 이름               | CoreServicesVnet     |
|              | 지역             | (미국) 미국 동부         |
| IP 주소 | IPv4 주소 공간 | 10.20.0.0/16         |

 4. 다음 표의 정보를 사용하여 CoreServicesVnet 서브넷을 만듭니다.

 5. 각 서브넷 만들기를 시작하려면 **+ 서브넷 추가**를 선택합니다. 각 서브넷 만들기를 마치려면 **추가**를 선택합니다.

| **서브넷**             | **옵션**           | **값**              |
| ---------------------- | -------------------- | ---------------------- |
| GatewaySubnet          | 서브넷 이름          | GatewaySubnet          |
|                        | 서브넷 주소 범위 | 10.20.0.0/27           |
| SharedServicesSubnet   | 서브넷 이름          | SharedServicesSubnet   |
|                        | 서브넷 주소 범위 | 10.20.10.0/24          |
| DatabaseSubnet         | 서브넷 이름          | DatabaseSubnet         |
|                        | 서브넷 주소 범위 | 10.20.20.0/24          |
| PublicWebServiceSubnet | 서브넷 이름          | PublicWebServiceSubnet |
|                        | 서브넷 주소 범위 | 10.20.30.0/24          |

 6. CoreServicesVnet 및 연결된 서브넷 만들기를 마치려면 **검토 + 만들기**를 선택합니다.

 7. 구성이 유효성 검사를 통과했는지 확인한 다음 **만들기**를 선택합니다.

 8. 아래 표에 따라 각 VNet마다 1 -8 단계를 반복합니다.  

## 작업 3: ManufacturingVnet 가상 네트워크 및 서브넷 만들기

| **Tab**      | **옵션**         | **값**            |
| ------------ | ------------------ | -------------------- |
| 기본 사항       | 리소스 그룹     | ContosoResourceGroup |
|              | 이름               | ManufacturingVnet    |
|              | 지역             | (유럽) 서유럽 |
| IP 주소 | IPv4 주소 공간 | 10.30.0.0/16         |

| **서브넷**                | **옵션**           | **값**                 |
| ------------------------- | -------------------- | ------------------------- |
| ManufacturingSystemSubnet | 서브넷 이름          | ManufacturingSystemSubnet |
|                           | 서브넷 주소 범위 | 10.30.10.0/24             |
| SensorSubnet1             | 서브넷 이름          | SensorSubnet1             |
|                           | 서브넷 주소 범위 | 10.30.20.0/24             |
| SensorSubnet2             | 서브넷 이름          | SensorSubnet2             |
|                           | 서브넷 주소 범위 | 10.30.21.0/24             |
| SensorSubnet3             | 서브넷 이름          | SensorSubnet3             |
|                           | 서브넷 주소 범위 | 10.30.22.0/24             |

## 작업 4: ResearchVnet 가상 네트워크 및 서브넷 만들기

| **Tab**      | **옵션**         | **값**            |
| ------------ | ------------------ | -------------------- |
| 기본 사항       | 리소스 그룹     | ContosoResourceGroup |
|              | 이름               | ResearchVnet         |
|              | 지역             | 동남아시아       |
| IP 주소 | IPv4 주소 공간 | 10.40.0.0/16         |

| **서브넷**           | **옵션**           | **값**            |
| -------------------- | -------------------- | -------------------- |
| ResearchSystemSubnet | 서브넷 이름          | ResearchSystemSubnet |
|                      | 서브넷 주소 범위 | 10.40.0.0/24         |

## 작업 5: VNet 및 서브넷 만들기 확인

1. Azure Portal 홈페이지에서 **모든 리소스**를 선택합니다.

2. CoreServicesVnet, ManufacturingVnet 및 ResearchVnet이 표시되는지 확인합니다.

3. **CoreServicesVnet**을 선택합니다.

4. CoreServicesVnet의 **설정**에서 **서브넷**을 선택합니다.

5. CoreServicesVnet | 서브넷에서, 만든 서브넷이 나열되고 IP 주소 범위가 맞는지 확인합니다.

   ![CoreServicesVnet의 서브넷 목록](../media/verify-subnets-annotated.png)

6. 각 VNet마다 3~5단계를 반복합니다.

축하합니다! 리소스 그룹, 3개의 Vnet, 연결된 서브넷을 성공적으로 만들었습니다.
