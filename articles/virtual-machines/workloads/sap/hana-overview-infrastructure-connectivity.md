---
title: Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Konfigurieren Sie die benötigte Verbindungsinfrastruktur, um SAP HANA in Azure (große Instanzen) zu verwenden.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f6f8a057651ce56731c180a2ccbb05e35d8fefc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967549"
---
# <a name="sap-hana-large-instances-deployment"></a>Bereitstellung von SAP HANA (große Instanzen) 

In diesem Artikel wird davon ausgegangen, dass Sie SAP HANA in Azure (große Instanzen) von Microsoft gekauft haben. Machen Sie sich vor dem Lesen dieses Artikels mit den Hintergrundinformationen unter [Informationen zu Begriffen](hana-know-terms.md) und [Verfügbare SKUs für HLI](hana-available-skus.md) vertraut.


Microsoft benötigt zum Bereitstellen der Einheiten vom Typ „HANA (große Instanzen)“ die folgenden Informationen:

- Kundenname.
- Kontaktinformationen für geschäftliche Fragen (einschließlich E-Mail-Adresse und Telefonnummer).
- Kontaktinformationen für technische Fragen (einschließlich E-Mail-Adresse und Telefonnummer).
- Kontaktinformationen für technische Fragen in Bezug auf das Netzwerk (einschließlich E-Mail-Adresse und Telefonnummer).
- Azure-Bereitstellungsregion (z.B. „USA, Westen“, „Australien, Osten“ oder „Europa, Norden“).
- SKU für SAP HANA in Azure (große Instanzen) (Konfiguration).
- Für jede Azure-Bereitstellungsregion:
    - Ein /29-IP-Adressbereich für ER-P2P-Verbindungen, über die virtuelle Azure-Netzwerke mit HANA (große Instanzen) verbunden werden.
    - Ein /24-CIDR-Block, der für den HANA (große Instanzen)-Server-IP-Pool verwendet wird.
    - Bei der Verwendung von [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) zum Aktivieren des direkten Umleitens lokaler Einheiten an Einheiten großer HANA-Instanzen oder zum Umleiten zwischen Einheiten großer HANA-Instanzen in verschiedenen Azure-Regionen müssen Sie einen weiteren /29-IP-Adressbereich reservieren. Dieser Bereich darf sich mit keinem der anderen IP-Adressbereiche überschneiden, die Sie zuvor definiert haben.
- Die IP-Adressbereichswerte, die im Attribut für den „Adressraum des virtuellen Netzwerks“ jedes virtuellen Azure-Netzwerks verwendet werden, für das Verbindungen mit HANA (große Instanzen) hergestellt werden.
- Daten für jedes System vom Typ „HANA (große Instanzen)“:
  - Der gewünschte Hostname – idealerweise mit vollqualifiziertem Domänennamen.
  - Die gewünschte IP-Adresse für die Einheit „HANA (große Instanzen)“ aus dem Adressbereich des Server-IP-Pools. (Die ersten 30 IP-Adressen im Adressbereich des Server-IP-Pools sind für die interne Nutzung in HANA (große Instanzen) reserviert.)
  - SAP HANA-SID-Name für die SAP HANA-Instanz (zum Erstellen der benötigten SAP HANA-bezogenen Datenträgervolumes erforderlich). Microsoft benötigt die HANA-SID für die Erstellung der Berechtigungen für „sidadm“ auf den NFS-Volumes. Diese Volumes werden an die Einheit „HANA (große Instanzen)“ angefügt. Außerdem wird die HANA-SID als eine der Namenskomponenten der Datenträgervolumes verwendet, die bereitgestellt werden. Wenn Sie auf der Einheit mehr als eine HANA-Instanz ausführen möchten, sollten Sie mehrere HANA-SIDs auflisten. Für jede Instanz wird jeweils ein separater Volumesatz zugewiesen.
  - Unter dem Linux-Betriebssystem verfügt der Benutzer „sidadm“ über eine Gruppen-ID. Diese ID wird benötigt, um die erforderlichen SAP HANA-bezogenen Datenträgervolumes zu erstellen. Bei einer SAP HANA-Installation wird die Gruppe „sapsys“ in der Regel mit der Gruppen-ID „1001“ erstellt. Der Benutzer „sidadm“ gehört zu dieser Gruppe.
  - Unter dem Linux-Betriebssystem verfügt der Benutzer „sidadm“ über eine Benutzer-ID. Diese ID wird benötigt, um die erforderlichen SAP HANA-bezogenen Datenträgervolumes zu erstellen. Listen Sie alle sidadm-Benutzer auf, wenn Sie auf der Einheit mehrere HANA-Instanzen ausführen. 
- Die ID des Azure-Abonnements, mit dem SAP HANA in Azure HANA (große Instanzen) direkt verbunden wird. Diese Abonnement-ID verweist auf das Azure-Abonnement, für das die HANA-Einheiten (große Instanzen) berechnet werden.

Nachdem Sie die obigen Informationen angegeben haben, wird SAP HANA in Azure (große Instanzen) von Microsoft bereitgestellt. Microsoft sendet Ihnen die Informationen, mit denen Sie Ihre virtuellen Azure-Netzwerke mit HANA (große Instanzen) verknüpfen können. Sie können auch auf die Einheiten vom Typ „HANA (große Instanzen)“ zugreifen.

Verwenden Sie die folgende Sequenz, um eine Verbindung mit HANA (große Instanzen) herzustellen, nachdem Microsoft die Bereitstellung durchgeführt hat:

1. [Verbinden von virtuellen Azure-Computern mit HANA (große Instanzen)](hana-connect-azure-vm-large-instances.md)
2. [Herstellen einer ExpressRoute-Verbindung zwischen einem VNET und HANA (große Instanzen)](hana-connect-vnet-express-route.md)
3. [Zusätzliche Netzwerkanforderungen (optional)](hana-additional-network-requirements.md)
