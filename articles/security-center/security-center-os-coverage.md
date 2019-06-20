---
title: Von Azure Security Center unterstützte Features und Plattformen | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Liste der vom Azure Security Center unterstützten Features und Plattformen.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: monhaber
ms.openlocfilehash: e2d66db2ae3c379db86d40603d919489fdf15eed
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144257"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Von Azure Security Center unterstützte Features und Plattformen

Die Überwachung des Sicherheitsstatus und entsprechende Empfehlungen sind für virtuelle Computer (VMs), die mit dem klassischen Bereitstellungsmodell oder mit dem Resource Manager-Bereitstellungsmodell erstellt wurden, und für Computer verfügbar.

> [!NOTE]
> Erfahren Sie mehr über das [klassische und das Resource Manager-Bereitstellungsmodell](../azure-classic-rm.md) für Azure-Ressourcen.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plattformen mit Unterstützung für den Datensammlungs-Agent 

In diesem Abschnitt werden die Plattformen aufgeführt, auf denen der Azure Security Center-Agent ausgeführt werden kann und von denen er Daten sammeln kann.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Unterstützte Plattformen für Windows-Computer und -VMs
Die folgenden Windows-Betriebssysteme werden unterstützt:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Die Integration in Windows Defender ATP unterstützt nur Windows Server 2012 R2 und Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Unterstützte Plattformen für Linux-Computer und -VMs

Die folgenden Linux-Betriebssysteme werden unterstützt:

> [!NOTE]
> Die Liste der unterstützten Linux-Betriebssysteme ändert sich ständig. Bei Bedarf können Sie [hier](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) klicken, um die aktuelle Liste der unterstützten Versionen anzuzeigen, falls seit der letzten Veröffentlichung in diesem Thema Änderungen vorgenommen wurden.

64 Bit
* CentOS 6 und 7
* Amazon Linux 2017.09
* Oracle Linux 6 und 7
* Red Hat Enterprise Linux Server 6 und 7
* Debian GNU/Linux 8 und 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS und 18.04 LTS
* SUSE Linux Enterprise Server 12

32 Bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 und 9
* Ubuntu Linux 14.04 LTS und 16.04 LTS

## <a name="vms-and-cloud-services"></a>VMs und Clouddienste
VMs, die in einem Clouddienst ausgeführt werden, werden ebenfalls unterstützt. Es werden nur Clouddienst-Webrollen und -Workerrollen überwacht, die in Produktionsslots ausgeführt werden. Weitere Informationen zu Clouddiensten finden Sie unter [Übersicht zu Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Unterstützte IaaS-Features

> [!div class="mx-tableFixed"]
> 

|Server|Windows|||Linux|||Preise|
|----|----|----|----|----|----|----|----|
|**Umgebung**|**Azure**||**Nicht-Azure**|**Azure**||**Nicht-Azure**||
||**Virtueller Computer**|**VM-Skalierungsgruppe**||**Virtueller Computer**|**VM-Skalierungsgruppe**|
|Warnungen der VMBA-Bedrohungserkennung|✔|✔|✔|✔ (für unterstützte Versionen)|✔ (für unterstützte Versionen)|✔|Empfehlungen (kostenlos) Bedrohungserkennung (Standard)|
|Warnungen der netzwerkbasierten Bedrohungserkennung|✔|✔|X|✔|✔|X|Standard|
|Windows Defender ATP-Integration|✔ (für unterstützte Versionen)|✔ (für unterstützte Versionen)|✔|X|X|X|Standard|
|Fehlende Patches|✔|✔|✔|✔|✔|✔|Kostenlos|
|Sicherheitskonfigurationen|✔|✔|✔|✔|✔|✔|Kostenlos|
|Bewertung von Endpoint Protection|✔|✔|✔|X|X|X|Kostenlos|
|JIT-VM-Zugriff|✔|X|X|✔|X|X|Standard|
|Adaptive Anwendungssteuerungen|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Bewertung der Datenträgerverschlüsselung|✔|✔|X|✔|✔|X|Kostenlos|
|Drittanbieterbereitstellung|✔|X|X|✔|X|X|Kostenlos|
|NSG-Bewertung|✔|✔|X|✔|✔|X|Kostenlos|
|Erkennung von Fileless-Bedrohungen|✔|✔|✔|X|X|X|Standard|
|Netzwerkübersicht|✔|✔|X|✔|✔|X|Standard|
|Adaptive Netzwerksteuerungen|✔|✔|X|✔|✔|X|Standard|
|Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen|✔|✔|✔|✔|✔|✔|Standard|
|Empfehlungen und Bedrohungserkennung für in Docker gehostete IaaS-Container|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Unterstützte Endpunktschutz-Lösungen

In der folgenden Tabelle finden Sie eine Matrix zu folgenden Fragen:
 - Ob Sie mit Azure Security Center jede Lösung für sich installieren können.
 - Welche Endpunktschutz-Lösungen Security Center erkennen kann. Wenn eine dieser Endpunktschutz-Lösungen ermittelt wird, empfiehlt Security Center nicht deren Installation.

Informationen darüber, wann Empfehlungen für die einzelnen Schutzfunktionen generiert werden, finden Sie unter [Endpoint Protection: Bewertung und Empfehlungen](security-center-endpoint-protection.md).

| Endpoint Protection| Plattformen | Security Center-Installation | Security Center-Ermittlung |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Nein, in Betriebssystem integriert| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (siehe Hinweis unten) | Per Erweiterung | Ja |
| Trend Micro – alle Versionen | Windows Server-Familie  | Nein | Ja |
| Symantec v12.1.1100+| Windows Server-Familie  | Nein | Ja |
| McAfee v10+ | Windows Server-Familie  | Nein | Ja |
| Kaspersky| Windows Server-Familie  | Nein | Nein  |
| Sophos| Windows Server-Familie  | Nein | Nein  |

> [!NOTE]
> - Für die Erkennung von System Center Endpoint Protection (SCEP) auf einem virtuellen Computer mit Windows Server 2008 R2 muss SCEP nach PowerShell 3.0 (oder einer höheren Version) installiert werden.

## <a name="supported-paas-features"></a>Unterstützte PaaS-Features


|Dienst|Empfehlungen (kostenlos)|Bedrohungserkennung (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob Storage-Konten|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNETs|✔| Nicht verfügbar|
|Subnetze|✔| Nicht verfügbar|
|NICs|✔| Nicht verfügbar|
|NSGs|✔| Nicht verfügbar|
|Abonnement|✔ **| ✔|
|Batch|✔| Nicht verfügbar|
|Service Fabric|✔| Nicht verfügbar|
|Automation-Konto|✔| Nicht verfügbar|
|Load Balancer|✔| Nicht verfügbar|
|Suchen,|✔| Nicht verfügbar|
|Service Bus|✔| Nicht verfügbar|
|Stream Analytics|✔| Nicht verfügbar|
|Event Hub|✔| Nicht verfügbar|
|Logik-Apps|✔| Nicht verfügbar|
|Speicherkonto|✔| Nicht verfügbar|
|Redis|✔| Nicht verfügbar|
|Data Lake Analytics|✔| Nicht verfügbar|
|Schlüsseltresor|✔| Nicht verfügbar|




\* Diese Features werden derzeit in der öffentlichen Vorschau unterstützt.

\*\* AAD-Empfehlungen sind nur für Standardabonnements verfügbar



## <a name="next-steps"></a>Nächste Schritte

- Hier erfahren Sie, wie Sie [die Entwurfsaspekte in Bezug auf die Einführung von Azure Security Center planen und verstehen](security-center-planning-and-operations-guide.md).
- Hier finden Sie Informationen zur [Verhaltensanalyse bei virtuellen Computern und zur Absturzabbild-Speicheranalyse in Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Lesen Sie [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md).
- Lesen Sie [Blogbeiträge zur Sicherheit und Compliance von Azure](https://blogs.msdn.com/b/azuresecurity/).
