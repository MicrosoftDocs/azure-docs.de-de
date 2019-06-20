---
title: Containerempfehlungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument werden die Empfehlungen in Azure Security Center erläutert, die als Hilfe zum Schutz Ihrer Container dienen.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60705274"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Grundlegendes zu Azure Security Center-Containerempfehlungen

Wie Sie Ihre monolithischen Anwendungen zum Ausführen unternehmenskritischer, cloudnativer Anwendungen in Containern migrieren, können Sie die Vorteile der Features von Containern einschließlich müheloser und schneller Bereitstellung und Aktualisierung nutzen. Während die Anzahl der bereitgestellten Container kontinuierlich wächst, müssen Sicherheitslösungen eingerichtet werden, die Ihnen Einblick in den Sicherheitszustand Ihrer Container bieten und sie vor Bedrohungen schützen.

Azure Security Center bietet die folgenden Funktionen, um Ihre Container zu sichern:

- **Einblick in die Container, die auf IaaS-Linux-Computern gehostet werden**<br>In Azure Security Center zeigt die Registerkarte „Container“ alle mit Docker bereitgestellten VMs an. Bei der Untersuchung von Sicherheitsproblemen auf einem virtuellen Computer bietet Security Center zusätzliche Informationen, die im Zusammenhang mit den Containern im Computer stehen, z.B. die Docker-Version und die Anzahl der auf dem Host ausgeführten Images.

    ![Registerkarte „Container“](./media/security-center-container-recommendations/docker-recommendation.png)


- **Empfehlungen zur Sicherheit basierend auf der CIS-Benchmark für Docker**<br>Security Center scannt Ihre Docker-Konfigurationen und bietet Ihnen durch die Bereitstellung einer Liste aller festgestellten Regelverstöße Einblick in die Fehlkonfigurationen. Security Center bietet Richtlinien, mit denen Sie diese Probleme schnell beheben und Zeit sparen können. Security Center bewertet fortlaufend die Docker-Konfigurationen und unterrichtet Sie über deren aktuellen Zustand.

    ![Registerkarte „Container“](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Erkennung von Containerbedrohungen in Echtzeit**<br> Security Center bietet Bedrohungserkennung in Echtzeit für Ihre Container auf Linux-Computern mit AuditD-Komponente. Die Warnungen identifizieren mehrere verdächtige Docker-Aktivitäten, wie z.B. die Erstellung eines privilegierten Containers auf dem Host, ein Anzeichen, dass ein Secure Shell-Server (SSH) in einem Docker-Container ausgeführt wird, oder die Verwendung von Crypto-Minern. Mit diesen Informationen können Sie schnell Sicherheitsprobleme lösen und die Sicherheit Ihrer Container verbessern.

    ![Registerkarte „Container“](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Empfehlungen
Verwenden Sie die folgenden Tabellen als Referenz, um die verfügbaren Container, die auf IaaS-Linux-Computern gehostet sind, und die Sicherheitsbewertung ihrer Docker-Konfigurationen zu verstehen.

| Empfehlung | BESCHREIBUNG | Wiederherstellung |
| --- | --- | --- |
|Sicherheitsrisiken in Containersicherheitskonfigurationen beseitigen |Beseitigen Sie Sicherheitsrisiken in Containersicherheitskonfigurationen auf Grundlage bewährter Methoden für die Konfiguration.| So beseitigen Sie Sicherheitsrisiken in Containersicherheitskonfigurationen:<br>1. Überprüfen Sie die Liste der fehlerhaften Regeln.<br>2. Korrigieren Sie die einzelnen Regeln gemäß den angegebenen Anweisungen.|


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie in den folgenden Themen:

* [Überwachen von Identität und Zugriff in Azure Security Center](security-center-identity-access.md)
* [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md)
* [Schützen Ihres Azure SQL-Diensts in Azure Security Center](security-center-sql-service-recommendations.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Schützen von Computern und Anwendungen im Azure Security Center](security-center-virtual-machine-protection.md)
* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md) : Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.

