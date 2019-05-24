---
title: Upgrade auf den Standard-Tarif von Azure Security Center für erhöhte Sicherheit | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu den Preisen für Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: v-mohabe
ms.openlocfilehash: 8f6f94fa8602dcc2b8eed19262f595cb18c40b57
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968307"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Upgrade auf den Standard-Tarif von Azure Security Center für erhöhte Sicherheit
Azure Security Center ermöglicht eine einheitliche Sicherheitsverwaltung und erweiterten Schutz vor Bedrohungen für Workloads, die in Azure, lokal und in anderen Clouds ausgeführt werden. Security Center bietet Transparenz und Kontrolle für Hybridcloud-Workloads, aktive Abwehrmaßnahmen zur Verringerung Ihrer Anfälligkeit für Bedrohungen sowie eine intelligente Erkennung, damit Sie mit der schnellen Entwicklung bei Cyberangriffen Schritt halten können.

## <a name="pricing-tiers"></a>Tarife
Security Center wird in zwei Tarifen angeboten:

- Der Tarif **Free** ist für alle Azure-Abonnements automatisch aktiviert und umfasst die Sicherheitsrichtlinie, die kontinuierliche Bewertung der Sicherheit und umsetzbare Sicherheitsempfehlungen für den Schutz Ihrer Azure-Ressourcen.
- Der Tarif **Standard** erweitert die Funktionen des Free-Tarifs auf Workloads, die in privaten und anderen öffentlichen Clouds ausgeführt werden. Er bietet eine einheitliche Sicherheitsverwaltung und Schutz vor Bedrohungen für Ihre Hybridcloud-Workloads. Im Standard-Tarif stehen zudem erweiterte Funktionen für die Bedrohungserkennung zur Verfügung. Diese verwenden unter anderem integrierte Verhaltensanalysen und Machine Learning, um Angriffe und Zero-Day-Exploits zu erkennen, sowie Zugriffs- und Anwendungskontrollen zur Verringerung der Angriffsfläche für Netzwerkangriffe und Schadsoftware. Sie können den Standard-Tarif kostenlos testen. Security Center Standard unterstützt Azure-Ressourcen wie VMs, VM-Skalierungsgruppen, App Service, SQL-Server und Speicherkonten. Wenn Sie über Azure Security Center Standard verfügen, sind Sie nicht mehr auf Support auf Basis des Ressourcentyps angewiesen. 


Weitere Informationen finden Sie auf der Seite mit den [Preisen für Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="try-standard-free-for-30-days"></a>Standard-Tarif kostenlos 30 Tage lang testen
Der Standard-Tarif wird für die ersten 30 Tage kostenlos angeboten. Wenn Sie den Dienst über diese 30 Tage hinaus nutzen, wird er automatisch in Rechnung gestellt.

Sie können ein Upgrade für ein gesamtes Azure-Abonnement auf den Standard-Tarif durchführen, da diese Änderung von allen Ressourcen des Abonnements geerbt wird.

So erhalten Sie den Standard-Tarif:

1. Klicken Sie im Hauptmenü von **Security Center** auf **Sicherheitsrichtlinie**.
2. Wählen Sie das Abonnement aus, das Sie auf den Standard-Tarif umstellen möchten.
3. Wählen Sie auf dem Blatt **Sicherheitsrichtlinie** die Option **Tarif** aus.
4. Wählen Sie **Standard** aus, um das Upgrade durchzuführen.
5. Klicken Sie auf **Speichern**.

(Die Preise in der Abbildung dienen nur als Beispiel.) ![Security Center-Tarife](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> Wenn Sie alle Security Center-Features aktivieren möchten, müssen Sie den Standard-Tarif auf das Abonnement mit den entsprechenden virtuellen Computern anwenden. Durch das Konfigurieren von Preisen für einen Arbeitsbereich werden Just-in-Time-VM-Zugriff, adaptive Anwendungssteuerungen und Netzwerkerkennungen für Azure-Ressourcen nicht aktiviert.
>
>

## <a name="why-upgrade-to-standard"></a>Gründe für ein Upgrade auf den Standard-Tarif
Security Center bietet mehr Sicherheit und besseren Schutz vor Bedrohungen für Ihre Hybridcloud-Workloads:

- **Hybridsicherheit:** Verschaffen Sie sich einen einheitlichen Überblick über die Sicherheit sämtlicher lokaler und cloudbasierter Workloads. Wenden Sie Sicherheitsrichtlinien an, und bewerten Sie kontinuierlich die Sicherheit Ihrer Hybridcloud-Workloads, um die Einhaltung von Sicherheitsstandards zu gewährleisten. Sammeln, durchsuchen und analysieren Sie Sicherheitsdaten aus verschiedensten Quellen (einschließlich Firewalls und Partnerlösungen).
- **Erweiterte Bedrohungserkennung:** Verwenden Sie erweiterte Analysefunktionen und den Microsoft Intelligent Security Graph, um der Entwicklung von Cyberangriffen immer einen Schritt voraus zu sein.  Nutzen Sie integrierte Verhaltensanalysen und Machine Learning, um Angriffe und Zero-Day-Exploits zu erkennen. Überwachen Sie Netzwerke, Computer und Clouddienste auf neue Angriffe und auf Aktivitäten nach einer Sicherheitsverletzung. Optimieren Sie die Untersuchung mit interaktiven Tools und kontextbezogenen Informationen zu Bedrohungen.
- **Zugriffs- und Anwendungskontrollen:** Blockieren Sie Schadsoftware und andere unerwünschte Anwendungen mithilfe von Whitelist-Empfehlungen, die auf Ihre spezifischen Workloads abgestimmt sind und durch Machine Learning unterstützt werden. Verkleinern Sie die Angriffsfläche des Netzwerks mit kontrolliertem Just-in-Time-Zugriff auf die Verwaltungsports virtueller Azure-Computer, um die Gefahr von Brute-Force- und anderen Netzwerkangriffen erheblich zu senken.


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden die Preise für Security Center vorgestellt. Weitere Informationen zur erweiterten Sicherheit und Bedrohungserkennung des Standard-Tarifs finden Sie unter den folgenden Links:

- [Erweiterte Bedrohungserkennung](security-center-threat-report.md)
- [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
