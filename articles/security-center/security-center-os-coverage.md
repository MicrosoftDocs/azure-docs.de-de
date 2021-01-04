---
title: Von Azure Security Center unterstützte Plattformen | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Liste der von Azure Security Center unterstützten Plattformen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 31ff3620bcab6b10b59582daa255ff1f259ab0d3
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852641"
---
# <a name="supported-platforms"></a>Unterstützte Plattformen 

Auf dieser Seite werden die von Azure Security Center unterstützten Plattformen und Umgebungen gezeigt.

## <a name="combinations-of-environments"></a>Kombinationen von Umgebungen <a name="vm-server"></a>

Security Center unterstützt virtuelle Computer und Server in unterschiedlichen Hybridumgebungen:

* Nur Azure
* Azure und lokal
* Azure und andere Clouds
* Azure, andere Clouds und lokal

Für eine unter einem Azure-Abonnement aktivierte Azure-Umgebung werden von Azure Security Center automatisch IaaS-Ressourcen ermittelt, die unter dem Abonnement bereitgestellt werden.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Security Center hängt vom [Log Analytics-Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent) ab. Stellen Sie sicher, dass auf ihren Computern eines der unterstützten Betriebssysteme für diesen Agent ausgeführt wird, wie auf den folgenden Seiten beschrieben:

* [Log Analytics-Agent für von Windows unterstützte Betriebssysteme](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Log Analytics-Agent für von Linux unterstützte Betriebssysteme](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Stellen Sie außerdem sicher, dass der Log Analytics-Agent [ordnungsgemäß für das Senden von Daten an Security Center](security-center-enable-data-collection.md#manual-agent) konfiguriert ist.

Weitere Informationen zu den spezifischen Security Center-Features, die unter Windows und Linux verfügbar sind, finden Sie unter [Featureabdeckung für Computer](security-center-services.md).

> [!NOTE]
> Azure Defender ist zwar für den Schutz von Servern konzipiert, die meisten Funktionen von **Azure Defender für Server** werden jedoch auch für Windows 10-Computer unterstützt. Ein Feature, das aktuell nicht unterstützt wird, ist die [in Security Center integrierte EDR-Lösung: Microsoft Defender für Endpunkt](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Verwaltete Dienste für virtuelle Computer <a name="virtual-machine"></a>

Unter einem Kundenabonnement werden virtuelle Computer auch im Rahmen einiger verwalteter Azure-Dienste erstellt, z. B. Azure Kubernetes (AKS), Azure Databricks usw. Security Center ermittelt auch diese virtuellen Computer, und der Log Analytics-Agent kann installiert und konfiguriert werden, wenn ein unterstütztes Betriebssystem verfügbar ist.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Virtuelle Computer, die in einem Clouddienst ausgeführt werden, werden ebenfalls unterstützt. Es werden nur Clouddienst-Webrollen und -Workerrollen überwacht, die in Produktionsslots ausgeführt werden. Weitere Informationen zu Clouddiensten finden Sie unter [Übersicht zu Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Der Schutz für VMs, die sich in Azure Stack befinden, wird ebenfalls unterstützt. Weitere Informationen zur Integration von Security Center in Azure Stack finden Sie unter [Integrieren Ihrer Azure Stack-VMs in Security Center](quickstart-onboard-machines.md). 

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Datenerfassung in Security Center mit dem Log Analytics-Agent](security-center-enable-data-collection.md).
- Erfahren Sie, wie [Daten von Security Center verwaltet und geschützt werden](security-center-data-security.md).
- Hier erfahren Sie, wie Sie [die Entwurfsaspekte in Bezug auf die Einführung von Azure Security Center planen und verstehen](security-center-planning-and-operations-guide.md).