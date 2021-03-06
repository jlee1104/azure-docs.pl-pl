---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver | Dokumenty firmy Microsoft
description: Przewodnik po wysokiej dostępności dla systemu SAP NetWeaver na maszynach wirtualnych platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1cfcc14a-6795-4cfd-a740-aa09d6d2b817
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25a0ac9ab349cfe8bef8e33d3f533bc5942e6323
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623826"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-setting-ha-nfs]:high-availability-guide-suse.md#setting-up-a-highly-available-nfs-server
[sap-suse-ascs-ha-setting-ha-anf]:high-availability-guide-suse-netapp-files.md#setting-up-the-azure-netapp-files-infrastructure
[sap-suse-ascs-ha-sap-installation]:high-availability-guide-suse.md#prepare-for-sap-netweaver-installation
[sap-suse-ascs-ha-sap-installation-anf]:high-availability-guide-suse-netapp-files.md
[sap-rhel-ascs-ha]:high-availability-guide-rhel.md
[sap-rhel-ascs-ha-sap-installation-anf]:high-availability-guide-rhel-netapp-files.md
[high-availability-guide-rhel-glusterfs]:high-availability-guide-rhel-glusterfs.md
[high-availability-guide-rhel-pacemaker]:high-availability-guide-rhel-pacemaker.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności sap multi-SID)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

Maszyny wirtualne platformy Azure to rozwiązanie dla organizacji, które potrzebują zasobów obliczeniowych, magazynowych i sieciowych, w minimalnym czasie i bez długich cykli zaopatrzenia. Maszyny wirtualne platformy Azure umożliwiają wdrażanie klasycznych aplikacji, takich jak ABAP, Java i stos ABAP+Java oparty na systemie SAP NetWeaver. Zwiększ niezawodność i dostępność bez dodatkowych zasobów lokalnych. Maszyny wirtualne platformy Azure obsługuje łączność między lokalizacjami, dzięki czemu można zintegrować maszyny wirtualne platformy Azure z domen lokalnych organizacji, chmur prywatnych i środowiska systemowego SAP.

Ta seria artykułów obejmuje:

* Architektura i scenariusze.
* Przygotowanie infrastruktury.
* Kroki instalacji SAP do wdrażania systemów SAP o wysokiej dostępności na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager.

    > [!IMPORTANT]
    > Zdecydowanie zaleca się użycie modelu wdrażania usługi Azure Resource Manager dla instalacji SAP. Oferuje wiele korzyści, które nie są dostępne w klasycznym modelu wdrażania. Dowiedz się więcej o [modelach wdrażania][virtual-machines-azure-resource-manager-architecture-benefits-arm] platformy Azure.   
    >
* Wysoka dostępność SAP na:
  * ![Windows][Logo_Windows]  **Windows**, używając **klastra trybu failover systemu Windows Server (WSFC)**
  * ![Linux][Logo_Linux] **Linux**, przy użyciu Linux **Cluster Framework**

W tych artykułach dowiesz się, jak chronić składniki pojedynczego punktu awarii (SPOF), takie jak SAP Central Services (ASCS/SCS) i systemy zarządzania bazami danych (DBMS). Dowiesz się również o nadmiarowych składnikach na platformie Azure, takich jak serwer aplikacji SAP.

## <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architektura i scenariusze wysokiej dostępności dla SAP NetWeaver

**Krótki opis:** W tym artykule omówimy architekturę wysokiej dostępności systemu SAP na platformie Azure. Omówimy, jak rozwiązać wysoką dostępność pojedynczego punktu awarii SAP (SPOF) i nadmiarowych składników oraz specyfikę infrastruktury platformy Azure o wysokiej dostępności. Omówimy również, w jaki sposób te części odnoszą się do komponentów systemu SAP. Ponadto dyskusja jest podzielona na specyfikę systemu Windows i Linux. Różne scenariusze wysokiej dostępności SAP są również objęte.

**Aktualizacja:** Październik 2017 r.

* [Architektura wysokiej dostępności i scenariusze dotyczące wysokiej dostępności maszyn wirtualnych platformy Azure dla systemu SAP NetWeaver][sap-high-availability-architecture-scenarios]

Artykuł obejmuje ![zarówno][Logo_Windows] Windows ![][Logo_Linux] **Windows** i Linux **Linux**.


## <a name="azure-infrastructure-preparation-for-sap-netweaver-high-availability-deployment"></a>Przygotowanie infrastruktury platformy Azure do wdrożenia wysokiej dostępności sap NetWeaver

**Krótki opis:** W artykułach wymienionych w tym miejscu omówimy kroki, które można wykonać, aby wdrożyć infrastrukturę platformy Azure w ramach przygotowań do instalacji SAP. Aby uprościć wdrażanie infrastruktury platformy Azure, szablony usługi SAP Azure Resource Manager są używane do automatyzacji całego procesu.

**Aktualizacja:** Marzec 2019 r.

* ![][Logo_Windows] [Przygotowanie infrastruktury platformy Azure systemu Windows pod kątem wysokiej dostępności systemu SAP przy użyciu klastra trybu failover systemu Windows i **udostępnionego dysku** dla wystąpień SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

* ![][Logo_Windows] [Przygotowanie infrastruktury platformy Azure systemu Windows pod kątem wysokiej dostępności systemu SAP przy użyciu klastra trybu failover systemu Windows i **udziału plików** dla wystąpień SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]

* ![][Logo_Linux] [Linux Przygotuj infrastrukturę platformy Azure dla wysokiej dostępności SAP przy użyciu struktury klastra SUSE Linux Enterprise Server dla wystąpień SAP ASCS/SCS][sap-suse-ascs-ha-setting-ha-nfs]

* ![][Logo_Linux] [Linux Przygotuj infrastrukturę platformy Azure dla wysokiej dostępności SAP przy użyciu struktury klastra SUSE Linux Enterprise Server dla wystąpień SAP ASCS/SCS z plikami NetApp platformy Azure][sap-suse-ascs-ha-setting-ha-anf]

* ![RHEL][Logo_Linux] [Przygotuj infrastrukturę platformy Azure dla wysokiej dostępności SAP ASCS/SCS — konfigurowanie systemu GlusterFS w systemie RHEL][high-availability-guide-rhel-glusterfs]

* ![RHEL][Logo_Linux] [Przygotuj infrastrukturę platformy Azure dla wysokiej dostępności SAP ASCS/SCS — konfigurowanie rozrusznika serca w RHEL][high-availability-guide-rhel-pacemaker]

## <a name="installation-of-an-sap-netweaver-high-availability-system-in-azure"></a>Instalacja systemu wysokiej dostępności SAP NetWeaver na platformie Azure

**Krótki opis:** W wymienionych w tym miejscu artykułach przedstawiono przykłady krok po kroku dotyczące instalacji i konfiguracji systemu SAP o wysokiej dostępności w klastrze klastra trybu failover systemu Windows Server i platformie klastrów systemu Linux na platformie Azure.

**Aktualizacja:** Marzec 2019 r.

* ![][Logo_Windows] [Wysoka dostępność systemu Windows Install SAP NetWeaver przy użyciu klastra trybu failover systemu Windows i **udostępnionego dysku** dla wystąpień SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]

* ![][Logo_Windows] [Wysoka dostępność systemu Windows Install SAP NetWeaver przy użyciu klastra trybu failover systemu Windows i udziału **plików** dla wystąpień SAP ASCS/SCS][sap-high-availability-installation-wsfc-file-share]

* ![][Logo_Linux] [Instaluj system SAP NetWeaver w systemie SUSE NetWeaver przy użyciu struktury klastra SUSE Linux Enterprise Server dla wystąpień SAP ASCS/SCS][sap-suse-ascs-ha-sap-installation]

* ![][Logo_Linux] [Instaluj system SAP NetWeaver w systemie SAP o wysokiej dostępności przy użyciu struktury klastra SUSE Linux Enterprise Server dla wystąpień SAP ASCS/SCS z plikami NetApp Azure][sap-suse-ascs-ha-sap-installation-anf]

* ![RHEL][Logo_Linux] [Zainstaluj SAP NetWeaver ASCS/SCS w konfiguracji o wysokiej dostępności na RHEL][sap-rhel-ascs-ha]

* ![RHEL][Logo_Linux] [Zainstaluj SAP NetWeaver ASCS/SCS w konfiguracji o wysokiej dostępności w RHEL za pomocą plików NetApp Azure][sap-rhel-ascs-ha-sap-installation-anf]

