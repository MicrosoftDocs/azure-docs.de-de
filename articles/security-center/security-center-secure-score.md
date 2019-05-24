---
title: Secure Score in Azure Security Center | Microsoft-Dokumentation
description: " Priorisieren Sie Sicherheitsempfehlungen mithilfe von Secure Score in Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: v-mohabe
ms.openlocfilehash: c23c9a2b9af1947c397b96431ae3c3bcd0e30aaa
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968288"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Verbessern des Secure Score in Azure Security Center


Bei so vielen Diensten, die Sicherheitsvorteile bieten, ist es oft schwierig zu wissen, welche Schritte zum Sichern und Schützen Ihrer Workload zuerst ausgeführt werden sollten. Der Azure Secure Score überprüft Ihre Sicherheitsempfehlungen und priorisiert sie für Sie, damit Sie wissen, welche Empfehlungen Sie zuerst durchführen sollten. Dies hilft Ihnen, die schwerwiegendsten Sicherheitsrisiken zu finden, damit Sie die Untersuchung priorisieren können. Secure Score ist ein Tool, mit dem Sie den Sicherheitsstatus bewerten können.

## <a name="secure-score-calculation"></a>Secure Score-Berechnung

Security Center ahmt die Arbeit des Sicherheitsanalysten nach, überprüft die Sicherheitsempfehlungen und wendet erweiterte Algorithmen an, um zu bestimmen, wie wichtig die jeweiligen Empfehlungen sind.
Azure Security Center überprüft ständig die aktiven Empfehlungen und berechnet auf dieser Grundlage Ihren Secure Score. Die jeweilige Bewertung einer Empfehlung ergibt sich aus deren Schweregrad und bewährten Methoden für die Sicherheit, die sich am stärksten auf die Sicherheit Ihrer Workload auswirken.

Security Center bietet Ihnen auch einen **Secure Score-Gesamtwert**. 

Beim **Secure Score-Gesamtwert** handelt es sich um eine Ansammlung aller Ihrer Empfehlungen. Sie können den Secure Score-Gesamtwert je nach Auswahl für alle Abonnements oder Verwaltungsgruppen anzeigen. Die Bewertung variiert je nach ausgewähltem Abonnement und aktiven Empfehlungen für diese Abonnements.

 
Um zu überprüfen, welche Empfehlungen die größte Auswirkung auf den Secure Score haben, können Sie die drei Empfehlungen mit den größten Auswirkungen im Security Center-Dashboard anzeigen, oder Sie können die Empfehlungen auf dem Listenblatt mithilfe der Spalte **Secure Score-Auswirkung** sortieren.


So zeigen Sie den Secure Score-Gesamtwert an:

1. Klicken Sie auf dem Azure-Dashboard auf **Security Center** und dann auf **Sicherheitsbewertung**.
2. Oben sehen Sie Hervorhebungen der Sicherheitsbewertung:
   - **Gesamte Sicherheitsbewertung** stellt die Bewertung nach Richtlinien und ausgewähltem Abonnement dar.
   - **Sicherheitsbewertung nach Kategorie** zeigt Ihnen, welche Ressourcen, die meiste Aufmerksamkeit benötigen.
   - **Beste Empfehlungen gemäß Auswirkung auf die Sicherheitsbewertung** enthält eine Liste mit Empfehlungen, deren Umsetzung Ihre Sicherheitsbewertung am stärksten verbessert.
 
   ![Secure Score](./media/security-center-secure-score/secure-score-dashboard.png)

3. In der folgenden Tabelle sehen Sie alle Ihre Abonnements und die jeweilige gesamte Sicherheitsbewertung.

   > [!NOTE]
   > Das Sicherheitsbewertungsergebnis einzelner Abonnements entspricht nicht der gesamten Sicherheitsbewertung. Die Sicherheitsbewertung ist eine Berechnung, die auf dem Verhältnis zwischen Ihren fehlerfreien Ressourcen und der Gesamtzahl Ihrer Ressourcen gemäß Empfehlung basiert, nicht ein Ergebnis der Ihre Abonnements übergreifenden Sicherheitsbewertungen. 
   >
4. Klicken Sie auf **Empfehlungen anzeigen**, um die Empfehlungen für dieses Abonnement anzuzeigen, die Sie durchführen können, um Ihre Sicherheitsbewertung zu verbessern.
4. In der Liste der Empfehlungen können Sie sehen, dass für jede Empfehlung eine Spalte mit der **Auswirkung auf die Sicherheitsbewertung** enthalten ist. Dieser Wert gibt an, um wie viel sich der Secure Score-Gesamtwert verbessert, wenn Sie den Empfehlungen folgen. Wenn Sie im folgenden Bildschirm beispielsweise **Sicherheitsrisiken in Containersicherheitskonfigurationen beseitigen**, erhöht sich Ihr Secure Score um 35 Punkte.

   ![Secure Score](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Einzelner Secure Score

Sie können auch einzelne Secure Scores anzeigen. Diese sind auf dem Blatt mit einzelnen Empfehlungen enthalten.  

Der **Secure Score für Empfehlungen** ist eine Berechnung, die auf dem Verhältnis zwischen fehlerfreien Ressourcen und der Gesamtzahl Ihrer Ressourcen basiert. Wenn die Anzahl der fehlerfreien Ressourcen der Gesamtzahl der Ressourcen entspricht, erhalten Sie den maximalen Secure Score von 50 für die Empfehlung. Um Ihren Secure Score dem maximalen Wert anzunähern, beheben Sie die fehlerhaften Ressourcen, indem Sie den Empfehlungen folgen.

Anhand der **Auswirkungen der Empfehlung** können Sie sehen, um wie viel sich der Secure Score verbessert, wenn Sie die empfohlenen Schritte ausführen. Wenn Ihr Secure Score beispielsweise 42 beträgt und für **Auswirkungen der Empfehlung** der Wert +3 angegeben ist, verbessert sich der Secure Score bei Ausführung der in der Empfehlung angegebenen Schritte auf 45.

Die Empfehlung zeigt, welche Bedrohungen für Ihre Workload bestehen, falls die Schritte zur Behebung nicht ausgeführt werden.

![Secure Score für einzelne Empfehlung](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde gezeigt, wie Sie den Sicherheitsstatus mithilfe von **Secure Score** in Azure Security Center verbessern. Weitere Informationen zu Security Center finden Sie unter folgendem Link:

* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.


