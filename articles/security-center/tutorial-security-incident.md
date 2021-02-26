---
title: 'Tutorial: Antworten auf Warnungen – Azure Security Center'
description: In diesem Tutorial erfahren Sie, wie Sie Sicherheitswarnungen selektieren und die Grundursache und den Bereich eines Vorfalls ermitteln.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: aeabfb42d378f22ff660214517fea031d678b208
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635573"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Tutorial: Selektieren, Untersuchen und Beantworten von Sicherheitswarnungen
Security Center analysiert Ihre Hybrid Cloud-Workloads ständig mithilfe von Advanced Analytics- und Threat Intelligence-Funktionen, um Sie vor potenziell schädlichen Aktivitäten auf Ihren Cloudressourcen warnen zu können. Sie können auch Warnungen aus anderen Sicherheitsprodukten und -diensten in Security Center integrieren. Nach der Auslösung einer Warnung muss schnell gehandelt werden, um das potenzielle Sicherheitsproblem zu untersuchen und zu beheben. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Selektieren von Sicherheitswarnungen
> * Untersuchen einer Sicherheitswarnung zum Ermitteln der Grundursache
> * Reagieren auf eine Sicherheitswarnung und Beseitigen der Grundursache

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durchlaufen der in diesem Tutorial behandelten Features muss Azure Defender aktiviert sein. Sie können Azure Defender kostenlos testen. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/). In der Schnellstartanleitung [Erste Schritte mit Security Center](security-center-get-started.md) werden die erforderlichen Schritte für das Upgrade erläutert.


## <a name="triage-security-alerts"></a>Selektieren von Sicherheitswarnungen
Mit Security Center erhalten Sie einen einheitlichen Überblick über alle Sicherheitswarnungen. Sicherheitswarnungen werden nach Schweregrad der erkannten Aktivität angeordnet. 

Beurteilen Sie die Warnungen auf der Seite **Sicherheitswarnungen**:

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Liste der Sicherheitswarnungen von Azure Security Center":::

Überprüfen Sie auf dieser Seite die aktiven Sicherheitswarnungen in Ihrer Umgebung, um die Warnungen auszuwählen, die Sie zuerst untersuchen möchten.

Wenn Sie Sicherheitswarnungen selektieren, priorisieren Sie Warnungen nach ihrem Schweregrad, indem Sie sich den Warnungen mit höherem Schweregrad zuerst widmen. Weitere Informationen zum Schweregrad von Warnungen finden Sie unter [Wie werden Warnungen klassifiziert?](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> Sie können Azure Security Center mit verbreiteten SIEM-Lösungen wie Azure Sentinel verbinden und die Warnungen über ein Tool Ihrer Wahl nutzen. Weitere Informationen finden Sie unter [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Service-Management-Lösung](export-to-siem.md).


## <a name="investigate-a-security-alert"></a>Untersuchen einer Sicherheitswarnung

Gehen Sie wie folgt vor, sobald Sie entschieden haben, welche Warnung Sie zuerst untersuchen möchten:

1. Wählen Sie die gewünschte Warnung aus.
1. Wählen Sie auf der Seite mit der Übersicht über die Warnungen die Ressource aus, die zuerst untersucht werden soll.
1. Beginnen Sie mit der Untersuchung im linken Bereich, in dem allgemeine Informationen zur Sicherheitswarnung angezeigt werden.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Linker Bereich der Seite „Warnungsdetails“, in der die allgemeinen Informationen hervorgehoben sind":::

    In diesem Bereich wird Folgendes angezeigt:
    - Schweregrad, Status und Aktivitätszeit der Warnung
    - Beschreibung, die die genaue erkannte Aktivität erläutert
    - Betroffene Ressourcen
    - Kill Chain-Absicht der Aktivität auf der MITRE ATT&CK-Matrix

1. Ausführlichere Informationen zur Untersuchung der verdächtigen Aktivität finden Sie auf der Registerkarte **Warnungsdetails**.

1. Die Informationen auf dieser Seite reichen möglicherweise für eine Reaktion aus. Wenn Sie weitere Informationen benötigen:

    - Wenden Sie sich an den Ressourcenbesitzer, um zu überprüfen, ob die erkannte Aktivität ein False Positive ist.
    - Untersuchen der von der angegriffenen Ressource generierten unformatierten Protokolle

## <a name="respond-to-a-security-alert"></a>Reagieren auf eine Sicherheitswarnung
Nachdem Sie eine Warnung untersucht und ihr Ausmaß ermittelt haben, können Sie in Azure Security Center auf Sicherheitswarnungen reagieren:

1.  Öffnen Sie die Registerkarte **Aktion ausführen**, um die empfohlenen Reaktionen anzuzeigen.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Registerkarte „Maßnahme ergreifen“ zu Sicherheitswarnungen" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Im Abschnitt **Auswirkungen der Bedrohung minimieren**, finden Sie die manuellen Schritte zur Behebung des Problems.
1.  Um Ihre Ressourcen zu sichern und zukünftige Angriffe dieser Art zu verhindern, setzen Sie die Sicherheitsempfehlungen im Abschnitt **Künftige Angriffe verhindern** um.
1.  Um eine Logik-App mit automatisierten Reaktionsschriften zu starten, verwenden Sie den Abschnitt **Automatisierte Antwort auslösen**.
1.  Wenn die erkannte Aktivität *nicht* bösartig ist, können Sie künftige Warnungen dieser Art im Abschnitt **Ähnliche Warnungen unterdrücken** unterdrücken.

1.  Wenn Sie die Untersuchung der Warnung abgeschlossen und entsprechend reagiert haben, ändern Sie den Status in **Verworfen**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Festlegen des Status einer Warnung":::

    Dadurch wird die Warnung aus der Hauptwarnungsliste entfernt. Mit dem Filter auf der Seite mit der Liste der Warnungen können Sie alle Warnungen mit dem Status **Verworfen** anzeigen.

1.  Wir würden uns freuen, wenn Sie Microsoft Feedback zur Warnung zur Verfügung stellen würden:
    1. Markieren Sie die Warnung als **Hilfreich** oder **Nicht hilfreich**.
    1. Wählen Sie einen Grund aus und fügen Sie einen Kommentar hinzu.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Feedback zur Nützlichkeit einer Warnung an Microsoft":::

    > [!TIP]
    > Wir berücksichtigen Ihr Feedback, um unsere Algorithmen zu verbessern und bessere Sicherheitswarnungen bereitzustellen.

## <a name="end-the-tutorial"></a>Abschließen des Tutorials

Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Falls Sie weitere Schnellstartanleitungen und Tutorials durchgehen möchten, lassen Sie die automatische Bereitstellung sowie Azure Defender aktiviert. 

Wenn Sie nicht mit den anderen Tutorials fortfahren möchten oder wenn Sie eines dieser Features deaktivieren möchten:

1. Kehren Sie zum Hauptmenü von Security Center zurück, und wählen Sie **Preise und Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Wählen Sie zum Herabstufen **Azure Defender aus** aus.
1. Um die automatische Bereitstellung zu deaktivieren, öffnen Sie die Seite **Datensammlung**, und legen Sie **Automatische Bereitstellung** auf **Aus** fest.
4. Wählen Sie **Speichern** aus.

>[!NOTE]
> Wenn Sie die automatische Bereitstellung deaktivieren, wird der Log Analytics-Agent nicht von virtuellen Azure-Computern entfernt, auf denen der Agent bereitgestellt wurde. Wenn Sie die automatische Bereitstellung deaktivieren, schränkt dies die Sicherheitsüberwachung für Ihre Ressourcen ein.
>

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Informationen zu Security Center-Features erhalten, die zum Reagieren auf eine Sicherheitswarnung verwendet werden: Verwandte Informationen finden Sie hier:

- [Reagieren auf Warnungen zu Azure Defender für Key Vault](defender-for-key-vault-usage.md)
- [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md)
- [Einführung in Azure Defender](azure-defender.md)
