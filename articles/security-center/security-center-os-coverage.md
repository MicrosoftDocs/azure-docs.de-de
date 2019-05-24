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
ms.date: 5/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 910e9acaea5c8f95dd344bc8e5454cd2bd3b7a25
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968328"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Von Azure Security Center unterstützte Features und Plattformen

Die Überwachung des Sicherheitsstatus und entsprechende Empfehlungen sind für virtuelle Computer (VMs), die mit dem klassischen Bereitstellungsmodell oder mit dem Resource Manager-Bereitstellungsmodell erstellt wurden, und für Computer verfügbar.

> [!NOTE]
> Erfahren Sie mehr über das [klassische und das Resource Manager-Bereitstellungsmodell](../azure-classic-rm.md) für Azure-Ressourcen.
>
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
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Unterstützte Plattformen für Linux-Computer und -VMs
Die folgenden Linux-Betriebssysteme werden unterstützt:

* Ubuntu-Versionen 12.04 LTS, 14.04 LTS und 16.04 LTS
* Debian-Versionen 6, 7, 8 und 9
* CentOS-Versionen 5, 6 und 7
* Red Hat Enterprise Linux-Versionen (RHEL) 5, 6 und 7
* SUSE Linux Enterprise Server-Versionen (SLES) 11 und 12
* Oracle Linux-Versionen 5, 6 und 7
* Amazon Linux 2012.09 bis 2017
* OpenSSL 1.1.0 wird nur auf x86_64-Plattformen (64 Bit) unterstützt.

## <a name="vms-and-cloud-services"></a>VMs und Clouddienste
VMs, die in einem Clouddienst ausgeführt werden, werden ebenfalls unterstützt. Es werden nur Clouddienst-Webrollen und -Workerrollen überwacht, die in Produktionsslots ausgeführt werden. Weitere Informationen zu Clouddiensten finden Sie unter [Übersicht zu Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Unterstützte IaaS-Features

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||
|----|----|----|----|----|
|Environment|Azure|Nicht-Azure|Azure|Nicht-Azure|
|Warnungen der VMBA-Bedrohungserkennung|✔|✔|✔ (für unterstützte Versionen)|✔|
|Warnungen der netzwerkbasierten Bedrohungserkennung|✔|X|✔|X|
|Windows Defender ATP-Integration|✔ (für unterstützte Versionen)|✔|X|X|
|Fehlende Patches|✔|✔|✔|✔|
|Sicherheitskonfigurationen|✔|✔|✔|✔|
|Endpoint Protection|✔|✔|X|X|
|JIT-VM-Zugriff|✔|X|✔|X|
|Adaptive Anwendungssteuerungen|✔|✔|✔|✔|
|FIM|✔|✔|✔|✔|
|Datenträgerverschlüsselung|✔|X|✔|X|
|Drittanbieterbereitstellung|✔|X|✔|X|
|NSGs|✔|X|✔|X|
|Erkennung von Fileless-Bedrohungen|✔|✔|X|X|
|Netzwerkübersicht|✔|X|✔|X|
|Adaptive Netzwerksteuerungen|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>Unterstützte Endpunktschutz-Lösungen

In der folgenden Tabelle finden Sie eine Matrix zu folgenden Fragen:
 - Ob Sie mit Azure Security Center jede Lösung für sich installieren können.
 - Welche Endpunktschutz-Lösungen Security Center erkennen kann. Wenn eine dieser Endpunktschutz-Lösungen ermittelt wird, empfiehlt Security Center nicht deren Installation.

| Endpoint Protection| Plattformen | Security Center-Installation | Security Center-Ermittlung |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Nein, in Betriebssystem integriert| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (siehe Hinweis unten) | Per Erweiterung | Ja |
| Trend Micro – alle Versionen | Windows Server-Familie  | Nein  | Ja |
| Symantec v12.1.1100+| Windows Server-Familie  | Nein  | Ja |
| McAfee v10+ | Windows Server-Familie  | Nein  | Ja |
| Kaspersky| Windows Server-Familie  | Nein  | Nein   |
| Sophos| Windows Server-Familie  | Nein  | Nein   |

> [!NOTE]
> - Für die Erkennung von System Center Endpoint Protection (SCEP) auf einem virtuellen Computer mit Windows Server 2008 R2 muss SCEP nach PowerShell 3.0 (oder einer höheren Version) installiert werden.
>
>

## <a name="supported-paas-features"></a>Unterstützte PaaS-Features 


|Dienst|Empfehlungen|Bedrohungserkennung|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob-Speicherkonten*|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNETs|✔| Nicht verfügbar|
|Subnetze|✔| Nicht verfügbar|
|NICs|✔| ✔|
|NSGs|✔| Nicht verfügbar|
|Abonnement|✔| ✔|

\* Diese Features werden derzeit in der öffentlichen Vorschau unterstützt. 



## <a name="next-steps"></a>Nächste Schritte

- Hier erfahren Sie, wie Sie [die Entwurfsaspekte in Bezug auf die Einführung von Azure Security Center planen und verstehen](security-center-planning-and-operations-guide.md).
- Hier finden Sie Informationen zur [Verhaltensanalyse bei virtuellen Computern und zur Absturzabbild-Speicheranalyse in Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Lesen Sie [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md).
- Lesen Sie [Blogbeiträge zur Sicherheit und Compliance von Azure](https://blogs.msdn.com/b/azuresecurity/).
