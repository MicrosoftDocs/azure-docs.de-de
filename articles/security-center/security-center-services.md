---
title: Azure Security Center-Funktionen nach Betriebssystem, Computertyp und Cloud
description: Hier erfahren Sie, welche Azure Security Center-Funktionen abhängig von Betriebssystem, Typ und Cloudbereitstellung verfügbar sind.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: 0e1f669b6955d5c5b70f2f29ff39ba5aef0a9a56
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544815"
---
# <a name="feature-coverage-for-machines"></a>Funktionsabdeckung für Computer

Die beiden folgenden Registerkarten enthalten die Azure Security Center-Features, die für virtuelle Computer und Server unter Windows und Linux verfügbar sind.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Unterstützte Funktionen für virtuelle Computer und Server <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-Computer**](#tab/features-windows)

|**Feature**|**Dokumentation zu virtuellen Computern**|**Azure Virtual Machine Scale Sets**|**Computer mit Azure Arc-Unterstützung**|**Azure Defender erforderlich**
|----|:----:|:----:|:----:|:----:|
|[Integration in Microsoft Defender für Endpunkt](security-center-wdatp.md)|✔</br>(für unterstützte Versionen)|✔</br>(für unterstützte Versionen)|✔|Ja|
|[Virtual Machine-Verhaltensanalysen (und Sicherheitswarnungen)](alerts-reference.md)|✔|✔|✔|Ja|
|[Dateilose Sicherheitswarnungen](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Netzwerkbasierte Sicherheitswarnungen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-Time-VM-Zugriff](security-center-just-in-time.md)|✔|-|-|Ja|
|[Native Sicherheitsrisikobewertung](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Dateiintegritätsüberwachung](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptive Anwendungssteuerungen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netzwerkübersicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptives Erhöhen des Netzwerkschutzes](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Empfehlungen und Bedrohungsschutz für in Docker gehostete IaaS-Container|-|-|-|Ja|
|Fehlende Bewertung von BS-Patches|✔|✔|✔|Azure: Nein<br><br>Arc-Unterstützung: Ja|
|Bewertung von Sicherheitsfehlkonfigurationen|✔|✔|✔|Azure: Nein<br><br>Arc-Unterstützung: Ja|
|[Endpoint Protection-Bewertung](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nein<br><br>Arc-Unterstützung: Ja|
|Bewertung der Datenträgerverschlüsselung|✔</br>(für [unterstützte Szenarien](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nein|
|Sicherheitsrisikobewertung durch Drittanbieter|✔|-|✔|Nein|
|[Netzwerksicherheitsbewertung](security-center-network-recommendations.md)|✔|✔|-|Nein|


### <a name="linux-machines"></a>[**Linux-Computer**](#tab/features-linux)

|**Feature**|**Dokumentation zu virtuellen Computern**|**Azure Virtual Machine Scale Sets**|**Computer mit Azure Arc-Unterstützung**|**Azure Defender erforderlich**
|----|:----:|:----:|:----:|:----:|
|[Integration in Microsoft Defender für Endpunkt](security-center-wdatp.md)|-|-|-|-|
|[Virtual Machine-Verhaltensanalysen (und Sicherheitswarnungen)](./azure-defender.md)|✔</br>(für unterstützte Versionen)|✔</br>(für unterstützte Versionen)|✔|Ja|
|[Dateilose Sicherheitswarnungen](alerts-reference.md#alerts-windows)|-|-|-|Ja|
|[Netzwerkbasierte Sicherheitswarnungen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-Time-VM-Zugriff](security-center-just-in-time.md)|✔|-|-|Ja|
|[Native Sicherheitsrisikobewertung](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Dateiintegritätsüberwachung](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptive Anwendungssteuerungen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netzwerkübersicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptives Erhöhen des Netzwerkschutzes](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Empfehlungen und Bedrohungsschutz für in Docker gehostete IaaS-Container|✔|✔|✔|Ja|
|Fehlende Bewertung von BS-Patches|✔|✔|✔|Azure: Nein<br><br>Arc-Unterstützung: Ja|
|Bewertung von Sicherheitsfehlkonfigurationen|✔|✔|✔|Azure: Nein<br><br>Arc-Unterstützung: Ja|
|[Endpoint Protection-Bewertung](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Nein|
|Bewertung der Datenträgerverschlüsselung|✔</br>(für [unterstützte Szenarien](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nein|
|Sicherheitsrisikobewertung durch Drittanbieter|✔|-|✔|Nein|
|[Netzwerksicherheitsbewertung](security-center-network-recommendations.md)|✔|✔|-|Nein|

--- 


> [!TIP]
>Wenn Sie mit Features experimentieren möchten, die nur in Azure Defender verfügbar sind, können Sie sich für eine 30-tägige Testversion registrieren. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Unterstützte Endpoint Protection-Lösungen <a name="endpoint-supported"></a>

In der folgenden Tabelle finden Sie eine Matrix zu folgenden Fragen:

 - Ob Sie mit Azure Security Center jede Lösung für sich installieren können.
 - Welche Endpunktschutz-Lösungen Security Center erkennen kann. Wenn eine Endpoint Protection-Lösungen aus dieser Liste ermittelt wird, empfiehlt Security Center nicht deren Installation.

Informationen darüber, wann Empfehlungen für die einzelnen Schutzfunktionen generiert werden, finden Sie unter [Endpoint Protection: Bewertung und Empfehlungen](security-center-endpoint-protection.md).

| Endpoint Protection| Plattformen | Security Center-Installation | Security Center-Ermittlung |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 oder höher| Nein, in Betriebssystem integriert| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (siehe Hinweis unten) | Per Erweiterung | Ja |
| Trend Micro – Deep Security | Windows Server-Familie  | Nein | Ja |
| Symantec v12.1.1100+| Windows Server-Familie  | Nein | Ja |
| McAfee v10+ | Windows Server-Familie  | Nein | Ja |
| McAfee v10+ | Linux-Serverfamilie  | Nein | Ja |
| Sophos V9+| Linux-Serverfamilie  | Nein | Ja |

> [!NOTE]
> Für die Erkennung von System Center Endpoint Protection (SCEP) auf einem virtuellen Computer mit Windows Server 2008 R2 muss SCEP nach PowerShell (ab Version 3.0) installiert werden.



## <a name="feature-support-in-government-clouds"></a>Funktionsunterstützung in Government-Clouds

| Dienst/Funktion | US Gov | China Gov |
|------|:----:|:----:|
|[Just-In-Time-VM-Zugriff](security-center-just-in-time.md) (1)|✔|✔|
|[Dateiintegritätsüberwachung](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Adaptive Anwendungssteuerungen](security-center-adaptive-application.md) (1)|✔|✔|
|[Adaptives Erhöhen des Netzwerkschutzes](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Docker-Hosthärtung](harden-docker-hosts.md) (1)|✔|✔|
|[Integrierte Sicherheitsrisikobewertung für Computer](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender für Endpunkt](harden-docker-hosts.md) (1)|✔|-|
|[Verbinden von AWS-Konten](quickstart-onboard-aws.md) (1)|-|-|
|[Verbinden von GCP-Konten](quickstart-onboard-gcp.md) (1)|-|-|
|[Fortlaufendem Export](continuous-export.md)|✔|✔ (2)|
|[Workflowautomatisierung (Vorschauversion)](workflow-automation.md)|✔ (2)|✔|
|[Empfehlungsausnahmeregeln](exempt-resource.md)|-|-|
|[Warnungsunterdrückungsregeln](alerts-suppression-rules.md)|✔|✔|
|[E-Mail-Benachrichtigungen für Sicherheitswarnungen](security-center-provide-security-contact-details.md)|✔|✔|
|[Ressourcenbestand](asset-inventory.md)|✔|✔|
|[Azure Defender für App Service](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender für Storage](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender für SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender für Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender für DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md)|✔ (2)|✔|
|[Kubernetes-Workloadschutz](kubernetes-workload-protections.md)|✔|✔|
|||

(1) Erfordert **Azure Defender für Server**

(2) Teilweise


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Datenerfassung in Security Center mit dem Log Analytics-Agent](security-center-enable-data-collection.md).
- Erfahren Sie, wie [Daten von Security Center verwaltet und geschützt werden](security-center-data-security.md).
- Informieren Sie sich über die [Plattformen, die Security Center unterstützen](security-center-os-coverage.md).