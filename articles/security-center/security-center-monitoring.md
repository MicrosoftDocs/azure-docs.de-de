---
title: Stärken Ihres Sicherheitsstatus mit Azure Security Center | Microsoft-Dokumentation
description: Dieser Artikel hilft Ihnen dabei, durch Überwachung Ihrer Ressourcen in Azure Security Center Ihren Sicherheitsstatus zu stärken.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 28b4667a9ceb4b3534d85ba28668f06c750e22c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703843"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Stärken Ihres Sicherheitsstatus mit Azure Security Center
Dieser Artikel hilft Ihnen dabei, Ihren Sicherheitsstatus zu stärken. Stellen Sie mithilfe der Überwachungsfunktionen von Azure Security Center möglichst hohe Ressourcensicherheit sicher, und überwachen Sie die Einhaltung von Richtlinien.

## <a name="how-do-you-strengthen-your-security-posture"></a>Wie stärken Sie Ihren Sicherheitsstatus?
Überwachung wird häufig als Beobachten und Warten auf ein Eintreten eines Ereignisses aufgefasst, damit auf diese Situation reagiert werden kann. Das Stärken des Sicherheitsstatus bezeichnet eine proaktive Strategie, bei der Ihre Ressourcen überwacht werden, um Systeme zu erkennen, die nicht den Unternehmensstandards oder bewährten Methoden entsprechen.

Nach Aktivierung der [Sicherheitsrichtlinien](tutorial-security-policy.md) für die Ressourcen eines Abonnements analysiert Security Center die Sicherheit Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Informationen zur Netzwerkkonfiguration sind sofort verfügbar. Abhängig davon, auf wie vielen Ihrer (virtuellen und physischen) Computer der Agent installiert ist, kann es eine Stunde oder länger dauern, bis die Konfigurationsinformationen für die Computer (beispielsweise Sicherheitsupdatestatus und Betriebssystemkonfiguration) gesammelt wurden und verfügbar sind. Eine vollständige Liste der Probleme und Möglichkeiten zum Erhöhen der Sicherheit Ihres Netzwerks und Beheben von Risiken enthält die Kachel **Empfehlungen**.

Sie können den Sicherheitsstatus Ihrer Ressourcen sowie ggf. vorliegende Probleme nach Ressourcentyp anzeigen:

- Weitere Informationen zum Überwachen der Integrität Ihrer Computerressourcen und Apps sowie zum Abrufen von Empfehlungen zur Verbesserung der Sicherheit finden Sie unter [Schützen von Computern und Anwendungen in Azure Security Center](security-center-virtual-machine-protection.md)
- Weitere Informationen zum Überwachen Ihrer Netzwerkressourcen (z.B. virtuelle Computer, Netzwerksicherheitsgruppen und Endpunkte) sowie zum Abrufen von Empfehlungen zur Verbesserung der Sicherheit finden Sie unter [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md). 
- Weitere Informationen zum Überwachen Ihrer Daten- und Speicherressourcen (z.B. SQL-Server und Speicherkonten) sowie zum Abrufen von Empfehlungen zur Verbesserung der Sicherheit finden Sie unter [Schützen des Azure SQL-Diensts und Ihrer Daten in Azure Security Center](security-center-sql-service-recommendations.md). 
- Weitere Informationen zum Überwachen Ihrer Identitäts- und Zugriffsressourcen, einschließlich MFA und Kontoberechtigungen, sowie zum Abrufen von Empfehlungen zur Verbesserung der Sicherheit finden Sie unter [Überwachen von Identität und Zugriff in Azure Security Center](security-center-identity-access.md). 
- Weitere Informationen zum Überwachen des Just-In-Time-Zugriffs auf Ihre Ressourcen finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](security-center-just-in-time.md). 


Weitere Informationen zur Anwendung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).



![Kachel „Sicherheitsintegrität der Ressource“](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Weitere Informationen
In diesem Artikel haben Sie erfahren, wie Sie die Überwachungsfunktionen in Azure Security Center verwenden können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center](tutorial-security-policy.md): Erfahren Sie, wie Sie Sicherheitseinstellungen in Azure Security Center konfigurieren.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md): Hier erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Enthält häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
