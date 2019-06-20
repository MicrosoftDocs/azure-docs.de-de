---
title: Zuverlässigkeitsbewertung in Azure Security Center | Microsoft-Dokumentation
description: " Erfahren Sie, wie Sie die Zuverlässigkeitsbewertung von Azure Security Center verwenden. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 64bab5c1b99720eecb189834d7a11802cf919ca3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60916576"
---
# <a name="alert-confidence-score"></a>Zuverlässigkeitsbewertung für Warnungen 

Azure Security Center bietet Ihnen Einblick in alle Ressourcen, die Sie in Azure ausführen, und warnt Sie, wenn potenzielle Probleme erkannt werden. Angesichts der großen Anzahl von Warnungen kann es für ein Security Operations-Team schwierig sein, die Warnungen einzeln zu bearbeiten, und es wird notwendig, die zu untersuchenden Warnungen zu priorisieren. Das Untersuchen von Warnungen kann komplex und zeitaufwendig sein. Daher werden einige Warnungen ignoriert.

Die Zuverlässigkeitsbewertung in Security Center kann Ihrem Team dabei helfen, Warnungen auszusortieren und zu priorisieren. Security Center wendet automatisch bewährte Methoden der Branche, intelligente Algorithmen und von Analysten eingesetzte Prozesse an, um zu bestimmen, ob eine Bedrohung legitim ist, und bietet Ihnen aussagekräftige Einblicke in Form einer Zuverlässigkeitsbewertung.

## <a name="how-the-confidence-score-is-triggered"></a>Funktionsweise für das Auslösen der Zuverlässigkeitsbewertung

Warnungen werden generiert, wenn erkannt wird, dass verdächtige Prozesse auf Ihren virtuellen Computern ausgeführt werden. Security Center überprüft und analysiert diese Warnungen auf in Azure ausgeführten virtuellen Windows-Computern. Der Dienst führt mithilfe erweiterter Algorithmen automatische Prüfungen und Korrelationen für mehrere Entitäten und Datenquellen in der gesamten Organisation und in sämtlichen Azure-Ressourcen durch und zeigt eine Zuverlässigkeitsbewertung an, mit der angegeben wird, wie hoch die von Security Center bewertete Wahrscheinlichkeit ist, dass die Warnung echt ist und untersucht werden muss.

## <a name="understanding-the-confidence-score"></a>Grundlegendes zur Zuverlässigkeitsbewertung

Die Zuverlässigkeitsbewertung liegt zwischen 1 und 100 und gibt die von Security Center eingeschätzte Sicherheit dazu an, dass eine Warnung untersucht werden muss. Je höher die Bewertung, desto höher die von Security Center bewertete Sicherheit, dass die Warnung auf eine echte schädliche Aktivität hindeutet. Die Zuverlässigkeitsbewertung umfasst eine Liste der wichtigsten Gründe, aus denen die Warnung die entsprechende Zuverlässigkeitsbewertung erhalten hat. Die Zuverlässigkeitsbewertung erleichtert es Sicherheitsanalysten, die Bearbeitung von Warnungen zu priorisieren und die dringendsten Angriffe zuerst anzugehen und so letztendlich die für die Reaktion auf Angriffe und Sicherheitsverletzungen erforderliche Zeit zu verringern.

So zeigen Sie die Zuverlässigkeitsbewertung an
- Öffnen Sie im Security Center-Portal das Blatt „Sicherheitswarnungen“.
-  Die Warnungen und Incidents werden von der höchsten zur niedrigsten Bewertung sortiert, d.h, je höher von Security Center die Zuverlässigkeit bewertet wird, dass eine Warnung eine Bedrohung darstellt, desto weiter oben auf der Seite wird sie angezeigt. 


 ![Zuverlässigkeitsbewertung][1]

So zeigen Sie die Daten an, die zur Zuverlässigkeitsbewertung von Security Center für eine Warnung beigetragen haben
- Zeigen Sie auf dem Blatt „Sicherheitswarnung“ unter **Zuverlässigkeit**, die Beobachtungen an, die zur Zuverlässigkeitsbewertung beigetragen haben, und verschaffen Sie sich einen Überblick über die Warnung. Damit erhalten Sie einen besseren Einblick in die Aktivitäten, die die Warnung ausgelöst haben.

  ![Zuverlässigkeitsbewertung einer verdächtigen Aktivität][2]

Anhand der Zuverlässigkeitsbewertung von Security Center können Sie die Aussortierung von Warnungen in Ihrer Umgebung priorisieren. Die Zuverlässigkeitsbewertung spart Ihnen Zeit und Mühe, da Warnungen automatisch untersucht, bewährte Methoden der Branche und intelligente Algorithmen angewandt werden und nach Art eines virtuellen Analysten bestimmt wird, welche Bedrohungen real sind und auf welche Warnungen Sie Ihr Augenmerk richten müssen.


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde erläutert, wie die Untersuchung von Warnungen anhand der Zuverlässigkeitsbewertung priorisiert wird. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
