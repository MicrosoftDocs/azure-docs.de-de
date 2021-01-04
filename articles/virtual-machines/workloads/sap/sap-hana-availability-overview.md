---
title: Verfügbarkeit von SAP HANA für Azure-VMs – Übersicht | Microsoft-Dokumentation
description: In diesem Artikel werden SAP HANA-Vorgänge auf nativen Azure-VMs beschrieben.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ac74bb4b37535d0c0b095ea300afc15aa0978ed8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950124"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer

Sie können zahlreiche Azure-Funktionen verwenden, um unternehmenskritische Datenbanken wie SAP HANA auf Azure-VMs bereitzustellen. Dieser Artikel enthält Anweisungen dazu, wie Verfügbarkeit für auf Azure-VMs gehosteten SAP HANA-Instanzen erzielt werden kann. In diesem Artikel werden mehrere Szenarien beschrieben, die Sie in der Azure-Infrastruktur zur Erhöhung der Verfügbarkeit von SAP HANA in Azure implementieren können. 

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie mit den folgenden IaaS-Grundlagen (Infrastructure-as-a-Service) in Azure vertraut sind: 

- Bereitstellen virtueller Computer oder virtueller Netzwerke über das Azure-Portal oder PowerShell.
- Verwendung der plattformübergreifenden Azure-Befehlszeilenschnittstelle (Azure CLI), einschließlich der Möglichkeit zur Verwendung von JavaScript Object Notation-Vorlagen (JSON)

Dieser Artikel setzt außerdem voraus, dass Sie mit der Installation, der Verwaltung und dem Betrieb von SAP HANA-Instanzen vertraut sind. Es ist besonders wichtig, mit der Einrichtung und den Vorgängen der HANA-Systemreplikation vertraut zu sein. Diese umfasst Aufgaben wie das Sichern und Wiederherstellen von SAP HANA-Datenbanken.

Die folgenden Artikel bieten einen guten Überblick über die Verwendung von SAP HANA in Azure:

- [Manuelle Installation von SAP HANA (Einzelinstanz) auf Azure-VMs](./hana-get-started.md)
- [Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern (VMs)](sap-hana-high-availability.md)
- [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](./sap-hana-backup-guide.md)

Auch die folgenden Artikel zu SAP HANA sind zu empfehlen:

- [High Availability for SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html) (Hochverfügbarkeit für SAP HANA)
- [FAQ: High Availability for SAP HANA](https://archive.sap.com/documents/docs/DOC-66702) (FAQs: Hochverfügbarkeit für SAP HANA)
- [How to Perform System Replication for SAP HANA](https://archive.sap.com/documents/docs/DOC-47702) (Gewusst wie: Durchführen der Replikation für SAP HANA)
- [SAP HANA 2.0 SPS 01 What’s New: High Availability](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/) (SAP HANA 2.0 SPS 01 – Neuerungen: Hochverfügbarkeit)
- [Network Recommendations for SAP HANA System Replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html) (Empfehlungen zum Netzwerk für die SAP HANA-Systemreplikation)
- [SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (SAP HANA-Systemreplikation)
- [SAP HANA Service Auto-Restart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) (Automatischer Neustart des SAP HANA-Diensts)
- [Konfigurieren der SAP HANA-Systemreplikation](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Bevor Sie mit der Definition Ihrer Verfügbarkeitsarchitektur in Azure fortfahren, wird neben der Bereitstellung von VMs in Azure außerdem empfohlen, den Artikel [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](../../manage-availability.md) zu lesen.

## <a name="service-level-agreements-for-azure-components"></a>Vereinbarungen zum Servicelevel für Azure-Komponenten

Azure bietet unterschiedliche Verfügbarkeits-SLAs für verschiedene Komponenten wie Netzwerke, Speicher und VMs. Sämtliche SLAs sind dokumentiert. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/). 

Unter [SLA für virtuelle Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) werden drei verschiedene SLAs für drei unterschiedliche Konfigurationen beschrieben:

- Eine einzelne VM, die [Azure SSD Premium](../../managed-disks-overview.md) für den Betriebssystemdatenträger und alle Datenträger verwendet. Diese Option bietet eine monatliche Betriebszeit von 99,9 Prozent.
- Mehrere (mindestens zwei) VMs, die in einer [Azure-Verfügbarkeitsgruppe](../../windows/tutorial-availability-sets.md) organisiert sind. Diese Option bietet eine monatliche Betriebszeit von 99,95 Prozent.
- Mehrere (mindestens zwei) VMs, die in einer [Verfügbarkeitszone](../../../availability-zones/az-overview.md) organisiert sind. Diese Option bietet eine monatliche Betriebszeit von 99,99 Prozent.

Gleichen Sie Ihre Anforderungen an die Verfügbarkeit mit den SLAs ab, die Azure-Komponenten bereitstellen können. Wählen Sie dann Ihre Szenarien für SAP HANA, um die von Ihnen benötigte Verfügbarkeitsstufe zu erreichen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Verfügbarkeit von SAP HANA innerhalb einer Azure-Region](./sap-hana-availability-one-region.md).
- Erfahren Sie mehr über [SAP HANA-Verfügbarkeit in den Azure-Regionen](./sap-hana-availability-across-regions.md). 















