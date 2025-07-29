---
demo:
  title: 모듈 02 - 공유 서비스로 라우팅
  module: Module 02 - Design and implement hybrid networking
---
이 데모를 수강생들에게 보여주는 것을 고려해 보세요. 

[빠른 시작: ARM 템플릿을 사용하여 공유 서비스로 라우팅 - Azure Virtual WAN | Microsoft Docs](https://learn.microsoft.com/azure/virtual-wan/quickstart-route-shared-services-vnet-template)

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 모든 VNet 및 Branch(VPN/ER/P2S)가 액세스할 수 있는 워크로드로 공유 서비스 VNet에 액세스하도록 경로를 설정하는 방법을 설명합니다. 이러한 공유 워크로드의 예로는 도메인 컨트롤러 또는 파일 공유와 같은 서비스가 있는 가상 머신 또는 Azure Private Endpoint를 통해 내부적으로 노출되는 Azure 서비스가 포함될 수 있습니다.
ARM 템플릿은 프로젝트에 대한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 이 템플릿은 선언적 구문을 사용합니다. 선언적 구문에서는 배포를 만들기 위한 프로그래밍 명령의 시퀀스를 작성하지 않고 의도하는 배포를 설명합니다.
환경이 필수 조건을 충족하고 ARM 템플릿 사용에 익숙한 경우 Azure에 배포 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

