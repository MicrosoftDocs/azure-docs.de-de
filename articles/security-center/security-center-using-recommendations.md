---
title: Befolgen von Azure Security Center-Empfehlungen zum Erhöhen der Sicherheit | Microsoft-Dokumentation
description: " Erfahren Sie, wie mithilfe von Sicherheitsrichtlinien und Empfehlungen in Azure Security Center Sicherheitsangriffe abgewehrt werden können. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 60bb1c3b81ef990993a2ce659a2b189c9d8a0eba
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967967"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Befolgen von Azure Security Center-Empfehlungen zum Erhöhen der Sicherheit
Sie können die Wahrscheinlichkeit eines Sicherheitsvorfalls verringern, indem Sie eine Sicherheitsrichtlinie und die Azure Security Center-Empfehlungen umsetzen. In diesem Artikel wird erläutert, wie mithilfe von Sicherheitsrichtlinien und Empfehlungen in Security Center Sicherheitsangriffe abgewehrt werden können. 

Security Center führt kontinuierliche Überprüfungen automatisch aus, um den Sicherheitsstatus Ihrer Azure-Ressourcen zu analysieren. Werden potenzielle Sicherheitslücken erkannt, erstellt Security Center Empfehlungen, die Sie beim Konfigurieren der erforderlichen Sicherheitskontrollen unterstützen. Security Center aktualisiert seine Empfehlungen innerhalb von 24 Stunden, mit folgenden Ausnahmen:

- Empfehlungen zu Betriebssystem-Sicherheitskonfigurationen werden innerhalb von 48 Stunden aktualisiert.
- Empfehlungen bei Endpoint Protection-Problemen werden innerhalb von 8 Stunden aktualisiert.

## <a name="scenario"></a>Szenario
Dieses Szenario veranschaulicht die Nutzung von Security Center zum Eindämmen der Wahrscheinlichkeit eines Sicherheitsvorfalls, indem Security Center-Empfehlungen befolgt und Maßnahmen ergriffen werden. In diesem Szenario werden das fiktive Unternehmen Contoso und die im [Planungs- und Betriebshandbuch](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) vorgestellten Rollen verwendet. In diesem Szenario konzentrieren wir uns auf die Rollen der folgenden Personen:

![Rollen im Szenario](./media/security-center-using-recommendations/scenario-roles.png)

Contoso hat vor Kurzem einige seiner lokalen Ressourcen nach Azure migriert. Contoso möchte ihre Ressourcen schützen und Sicherheitsrisiken für ihre Ressourcen in der Cloud verringern.

## <a name="use-azure-security-center"></a>Verwenden des Azure Security Centers
David, Mitarbeiter der IT-Sicherheit von Contoso, hat sich bereits für ein Onboarding beim Security Center im Rahmen des Azure Security Center-Abonnements von Contoso entschlossen, um Sicherheitsrisiken zu verhindern und zu erkennen. 

Das Security Center analysiert automatisch den Sicherheitszustand der Azure-Ressourcen von Contoso und wendet Standardsicherheitsrichtlinien an. Wenn Security Center potenzielle Sicherheitsrisiken ermittelt, werden basierend auf den in der Sicherheitsrichtlinie festgelegten Elementen **Empfehlungen** erstellt. 

David führt den Azure Security-Standardtarif für alle seine Abonnements aus, um den vollen Umfang an verfügbaren Empfehlungen und Sicherheitsfunktionen zu erhalten. Jeff nimmt ebenfalls ein Onboarding all seiner vorhandenen lokalen Server vor, die noch nicht zur Cloud migriert wurden, sodass er die Hybridunterstützung des Security Centers auf allen [Windows](quick-onboard-windows-computer.md)- und [Linux](quick-onboard-linux-computer.md)-Servern ausnutzen kann.

Jeff ist Besitzer einer Cloudworkload. Jeff ist verantwortlich für die Anwendung von Sicherheitskontrollen gemäß den Sicherheitsrichtlinien von Contoso. 

Jeff führt die folgenden Aufgaben aus:

- Überwachen der von Security Center bereitgestellten Sicherheitsempfehlungen
- Auswerten von Sicherheitsempfehlungen und entscheiden, ob sie umgesetzt werden sollen oder nicht
- Umsetzen von Sicherheitsempfehlungen

### <a name="remediate-threats-using-recommendations"></a>Beseitigen von Bedrohungen mittels Empfehlungen
Im Rahmen seiner täglichen Überwachungsaktivitäten meldet sich Jeff bei Azure an und öffnet das Security Center. 

1. Jeff wählt die Abonnements seiner Workload aus.

2. Jeff überprüft seine **Sicherheitsbewertung**, um einen Gesamteindruck davon zu bekommen, wie sicher die Abonnements sind, und er sieht, dass seine Bewertung 548 ist.

3. Jeff muss entscheiden, welche Empfehlungen er zuerst umsetzt. Also klickt Jeff auf „Sicherheitsbewertung“ und beginnt, die Empfehlungen abzuarbeiten, basierend darauf, um wie viel sie seine [Sicherheitsbewertung](security-center-secure-score.md) verbessern.

4. Da Jeff über viele verbundene virtuelle Computer und Server verfügt, entscheidet er, dass er sich auf **Compute und Apps** konzentriert.

5. Als Jeff auf **Compute und Apps** klickt, wird ihm eine Liste mit Empfehlungen angezeigt, und er arbeitet diese gemäß ihrer Auswirkungen auf die Sicherheitsbewertung ab.

6. Jeff verfügt über zahlreiche virtuelle Computer mit Internetzugriff, und da deren Ports verfügbar sind, macht er sich Sorgen, dass ein Angreifer die Kontrolle über die Server erlangen könnte. Also entscheidet sich Jeff für die Verwendung von (**Just-in-Time VM Access**)[security-center-just-in-time.md].

Jeff geht die Empfehlungen mit hoher und mittlerer Priorität weiter durch und trifft Entscheidungen zu ihrer Umsetzung. Bei jeder Empfehlung sieht sich Jeff die ausführlichen Informationen an, die vom Security Center bereitgestellt werden, um zu verstehen, welche Ressourcen betroffen sind, wie die Auswirkungen auf die Sicherheitsbewertung sind, und was jede Empfehlung bedeutet, sowie die Korrekturschritte dafür, wie jedes Problem korrigiert werden kann.

## <a name="conclusion"></a>Zusammenfassung
Die Überwachungsempfehlungen im Security Center helfen Ihnen dabei, Sicherheitsrisiken zu beseitigen, ehe ein Angriff erfolgt. Wenn Sie Korrekturen gemäß den Empfehlungen vornehmen, verbessern Sie Ihre Sicherheitsbewertung sowie den Sicherheitsstatus Ihrer Workloads. Security Center ermittelt automatisch neue Ressourcen, die Sie bereitstellen, bewertet diese anhand Ihrer Sicherheitsrichtlinie und stellt neue Empfehlungen zu deren Schutz bereit.


## <a name="next-steps"></a>Nächste Schritte
Stellen Sie sicher, dass Sie einen Überwachungsprozess eingerichtet haben, in dessen Rahmen Sie regelmäßig die Empfehlungen im Security Center überprüfen, damit Sie sicherstellen können, dass Ihre Ressourcen im Laufe der Zeit geschützt bleiben.

In diesem Szenario wurde veranschaulicht, wie Sicherheitsangriffe mithilfe von Sicherheitsrichtlinien und Empfehlungen in Security Center abgewehrt werden können. Im [Szenario für eine Reaktion auf Vorfälle](security-center-incident-response.md) erfahren Sie, wie Sie einen Plan zur Reaktion auf Vorfälle bereitstellen, bevor es zu einem Angriff kommt.

Informationen, wie Sie auf Bedrohungen mittels [Reaktion auf Vorfälle](security-center-incident-response.md) reagieren.
