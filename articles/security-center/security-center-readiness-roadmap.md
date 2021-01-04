---
title: Roadmap für Azure Security Center-Bereitschaft | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Roadmap für die Bereitschaft zur Nutzung von Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: fcaf427c2e0ab275a5a6e08306dda785bca690d6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784166"
---
# <a name="azure-security-center-readiness-roadmap"></a>Roadmap für Azure Security Center-Bereitschaft
Dieses Dokument enthält eine Roadmap für die Bereitschaft, die Ihnen als Hilfe bei den ersten Schritten mit Azure Security Center dient.

## <a name="understanding-security-center"></a>Grundlegendes zu Security Center
Azure Security Center ermöglicht eine einheitliche Sicherheitsverwaltung und erweiterten Schutz vor Bedrohungen für Workloads, die in Azure, lokal und in anderen Clouds ausgeführt werden. 

Verwenden Sie für die ersten Schritte mit Security Center die folgenden Ressourcen:

Artikel
- [Einführung in Azure Security Center](security-center-introduction.md)
- [Schnellstarthandbuch zu Azure Security Center](security-center-get-started.md)

Videos
- [Kurzes Einführungsvideo](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Overview of Security Center Prevention, Detection and Response Capabilities](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/) (Übersicht über Präventions-, Erkennungs- und Antwortfunktionen für Security Center)

## <a name="planning-and-operations"></a>Planung und Betrieb

Zur optimalen Nutzung von Security Center sollten Sie verstanden haben, wie der Dienst von den verschiedenen Personen oder Teams in Ihrer Organisation genutzt wird, um die Anforderungen in Bezug auf sicheren Betrieb, Überwachung, Governance und Reaktionen auf Vorfälle zu erfüllen.

Verwenden Sie die folgenden Ressourcen als Hilfe bei der Planung und beim Betrieb.

- [Planungs- und Betriebshandbuch für Azure Security Center](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Onboarding von Computern für Security Center
Security Center erkennt automatisch alle Azure-Abonnements oder -Arbeitsbereiche, die nicht durch Azure Defender geschützt werden. Dazu zählen unter anderem Azure-Abonnements mit Security Center Free sowie Arbeitsbereiche, für die die Sicherheitslösung nicht aktiviert ist.

Verwenden Sie die folgenden Ressourcen als Hilfe während des Onboarding-Prozesses.

- [Integrieren Azure-fremder Computer](quickstart-onboard-machines.md)
- [Azure Security Center Hybrid – Übersicht](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Beheben von Sicherheitsproblemen mit Security Center
Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von verbundenen Partnerlösungen, z.B. Lösungen zum Schutz von Firewalls und Endpunkten, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren.

Verwenden Sie die folgenden Ressourcen als Hilfe beim Verwalten von Sicherheitswarnungen und Schützen Ihrer Ressourcen.

Artikel    
- [Überwachen der Sicherheitsintegrität in Azure Security Center](./security-center-monitoring.md)
- [Schützen Ihres Netzwerks in Azure Security Center](./security-center-network-recommendations.md)
- [Schützen des Azure SQL-Diensts und Ihrer Daten in Azure Security Center](./security-center-remediate-recommendations.md)


Video    
- [Beheben von Sicherheitsproblemen mit Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Verwenden von Security Center zum Reagieren auf Vorfälle
Zur Reduzierung von Kosten und Schäden ist es wichtig, dass ein Plan zur Reaktion auf Vorfälle vorhanden ist, bevor es zu einem Angriff kommt. Sie können Azure Security Center in unterschiedlichen Phasen einer Reaktion auf Vorfälle nutzen.

Verwenden Sie die folgenden Ressourcen, um sich darüber zu informieren, wie Security Center in Ihren Prozess zur Reaktion auf Vorfälle integriert werden kann.

Videos    
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response) (Azure Security Center zur Reaktion auf Vorfälle)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g) (Schnelles Reagieren auf Bedrohungen mit Sicherheitsabläufen und -untersuchungen der nächsten Generation)

Artikel    
* [Verwenden von Azure Security Center zur Reaktion auf Vorfälle](./tutorial-security-incident.md)
* [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Erweiterter Cloudschutz

Für Azure-VMs können erweiterte Funktionen für die Cloudverteidigung in Security Center genutzt werden. Zu diesen Funktionen gehören auch der bedarfsabhängige Zugriff auf virtuelle Computer (VMs) und die adaptive Anwendungssteuerung.

Verwenden Sie die folgenden Ressourcen, um sich darüber zu informieren, wie Sie diese Funktionen in Security Center verwenden.

Videos    
* [Azure Security Center – Just-in-Time VM Access](https://youtu.be/UOQb2FcdQnU) (Azure Security Center – Bedarfsabhängiger VM-Zugriff)
* [Azure Security Center - Adaptive Application Controls](https://youtu.be/wWWekI1Y9ck) (Azure Security Center – Adaptive Anwendungssteuerungen)

Artikel    
* [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](./security-center-just-in-time.md)
* [Adaptive Anwendungssteuerungen in Azure Security Center](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Praktische Aktivitäten

* [Praktische Übungen zu Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Web Application Firewall (WAF) recommendation playbook in Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff) (Playbook zu Web Application Firewall (WAF) mit Empfehlungen zu Security Center)
* [Azure Security Center-Playbook: Sicherheitswarnungen](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Seite mit der Security Center-Dokumentation](./index.yml)
* [Seite mit der Dokumentation zur Security Center-REST-API](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Azure Security Center – Häufig gestellte Fragen](./faq-general.md)
* [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/)
* [Azure-Identitätsverwaltung und Sicherheit der Zugriffssteuerung – Bewährte Methoden](../security/fundamentals/identity-management-best-practices.md)
* [Bewährte Methoden für die Netzwerksicherheit](../security/fundamentals/network-best-practices.md)
* [PaaS-Empfehlungen](../security/fundamentals/paas-deployments.md)
* [Compliance](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics-Kunden können jetzt ihre Hybrid Cloud-Workloads mit Azure Security Center schützen](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Communityressourcen

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Q&A-Seite für Security Center](/answers/topics/azure-security-center.html)