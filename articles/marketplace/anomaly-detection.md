---
title: Anomalieerkennung für getaktete Abrechnungen | Azure Marketplace
description: Hier erfahren Sie, wie die automatische Anomalieerkennung für getaktete Abrechnungen dafür sorgt, dass Ihre Kunden für die gemessene Nutzung von Angeboten über Ihren kommerziellen Marketplace eine korrekte Rechnung erhalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989221"
---
# <a name="anomaly-detection-for-metered-billing"></a>Anomalieerkennung für getaktete Abrechnung

In diesem Artikel finden Sie Details zum Messdienst für den Marketplace sowie zu der dazugehörigen Funktion für Anomalieerkennung, die dafür sorgt, dass Kunden für ihre gemessene Nutzung korrekt abgerechnet werden. Die Option für getaktete Abrechnung ist aktuell für [SaaS-Angebote](plan-saas-offer.md) (Software-as-a-Service) und [Azure-Anwendungen](plan-azure-application-offer.md#types-of-plans) mit verwaltetem Anwendungsplan verfügbar. Diese Option ermöglicht es Partnern, Angebote über den kommerziellen Marketplace zu erstellen, die gemäß Einheiten berechnet werden, die nicht dem Standardtarif entsprechen.

Partner, die benutzerdefinierte Verbrauchseinheiten für SaaS-Anwendungen und verwaltete Anwendungen bereitstellen, werden in Partner Center über Anomalien in Form von Abweichungen von einem erwarteten Benutzerverhalten zu _Überschreitungsereignissen_ oder bestimmten _benutzerdefinierten Verbrauchseinheiten_ informiert. Partner Center verwendet zur Risikominimierung einen Dienst für die Anomalieerkennung, der Machine-Learning-Algorithmen anwendet, um ein normales Verhalten für die getaktete Abrechnung zu bestimmen, die gemessene Nutzung laut Abrechnung zu analysieren und dabei mögliche Anomalien zu erkennen. Der dafür erforderliche Benutzereingriff ist minimal. Auf Grundlage von _Modellen für die Anomalieerkennung_ für Datasets mit getakteter Nutzungsabrechnung werden Herausgeber in Partner Center informiert, wenn die gemeldete Nutzung die erwartete Nutzung überschreitet.

## <a name="usability-experience"></a>Nutzung

Der Partner muss die Überschreitungsnutzung seines Kunden bezüglich seiner SaaS- oder Azure Managed Applications-Angebote an Microsoft melden, bevor Microsoft eine Rechnung für den Kunden erstellen kann. Wenn eine falsche Nutzung gemeldet wird, erhält der Kunde möglicherweise eine falsche Rechnung, was die Glaubwürdigkeit von Microsoft sowie die des Partners schädigen kann.

Damit dieses Risiko umgangen werden kann, wird ein automatisiertes Anomalieerkennungsfeature bereitgestellt, das sowohl für SaaS-Apps als auch für Azure Managed Applications-Pläne angewendet werden kann. Bei diesem Feature handelt es sich um ein Machine Learning-Modell, das proaktiv die Nutzung überwacht und mit der getakteten Abrechnung vergleicht, um so einen zu erwartenden Nutzungswert innerhalb eines erwarteten Bereichs vorherzusagen. Wenn sich die Nutzung außerhalb des erwarteten Bereichs befindet, wird sie als Anomalie behandelt, und für den Partner wird eine Warnmeldung auf der Seite „Übersicht“ im kommerziellen Marketplace in Partner Center angezeigt.

Das Machine Learning-Modell analysiert die Überschreitungsnutzung täglich. Für den Herausgeber werden alle Anomalien im Hinblick auf die Überschreitungsnutzung seiner Kunden für die benutzerdefinierten Verbrauchseinheitsdimensionen aller seiner Angebote gemeldet.

### <a name="view-and-manage-metered-usage-anomalies"></a>Anzeigen und Verwalten von Anomalien bei der gemessenen Nutzung

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Analysieren**.
1. Klicken Sie auf die Registerkarte **Metered usage anomalies** (Anomalien bei der gemessenen Nutzung).

    [![Screenshot der Registerkarte „Metered usage anomalies“ (Anomalien bei der gemessenen Nutzung) auf der Seite „Usage“ (Nutzung)](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Abbildung 1: Registerkarte „Metered usage anomalies“ (Anomalien bei der gemessenen Nutzung)***

1. Als Herausgeber werden Sie für jede ermittelte Nutzungsanomalie im Hinblick auf die getaktete Abrechnung aufgefordert, Untersuchungen anzustellen und zu ermitteln, ob es sich um eine Anomalie handelt oder nicht. Klicken Sie auf **Mark as anomaly** (Als Anomalie kennzeichnen), um die Diagnose zu bestätigen.

     [![Screenshot des Dialogfelds „Mark as anomaly“ (Als Anomalie kennzeichnen)](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Abbildung 2: Dialogfeld „Mark as anomaly“ (Als Anomalie kennzeichnen)***

1. Wenn Sie der Meinung sind, dass es sich bei der erkannten Überschreitungsnutzung um keine Anomalie handelt, können Sie Feedback geben, indem Sie bei der gemeldeten Anomalie in Partner Center für die konkrete Überschreitungsnutzung auf **Not an anomaly** (Keine Anomalie) klicken.

    [![Screenshot des Dialogfelds „Why is it not an anomaly?“ (Warum ist es keine Anomalie?)](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Abbildung 3: Dialogfeld „Why is it not an anomaly?“ (Warum ist es keine Anomalie?)***

1. Sie können auf der Seite nach unten scrollen, damit eine Liste aller nicht bestätigter Anomalien angezeigt wird. Die Liste zeigt alle Anomalien an, die Sie nicht bestätigt haben. Für alle gemeldeten Anomalien in Partner Center können Sie entweder „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ auswählen.

   [![Screenshot der Liste nicht bestätigter Anomalien auf der Seite „Usage“ (Nutzung) in Partner Center](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Abbildung 4: Liste nicht bestätigter Anomalien in Partner Center***

1. Außerdem wird ein Aktionsprotokoll für Anomalien angezeigt, in dem die Aktionen aufgeführt sind, die Sie für die Überschreitungsnutzungen durchgeführt haben. Im Aktionsprotokoll wird angezeigt, für welche Überschreitungsnutzungsereignisse Sie „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ ausgewählt haben.

   [![Screenshot des Aktionsprotokolls für Anomalien auf der Seite „Usage“ (Nutzung)](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox)
   ***Abbildung 5: Aktionsprotokoll für Anomalien***

1. Rückwirkende Korrekturen für Überschreitungsnutzungsereignisse in exportierten Berichten werden in Partner Center-Analysen nicht unterstützt. Sie können in Partner Center die korrigierte Überschreitungsnutzung zu einer Anomalie eingeben, und die Details werden für eine weitere Untersuchung an Microsoft-Teams übermittelt. Je nach Untersuchungsergebnis überweist Microsoft ein Guthaben an den Kunden, dem ein zu hoher Betrag in Rechnung gestellt wurde. Wenn Sie eine der gekennzeichneten Anomalien auswählen, können Sie auf **Mark as anomaly** (Als Anomalie kennzeichnen) klicken, um die Überschreitungsnutzungsanomalie als tatsächliche Anomalie zu kennzeichnen.

   [![Screenshot des Dialogfelds „Mark as an anomaly“ (Als Anomalie kennzeichnen)](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox)
   ***Abbildung: 6: Dialogfeld „Mark as anomaly“ (Als Anomalie kennzeichnen)***

Wenn eine Überschreitungsnutzung in Partner Center erstmalig gemeldet wird, erhalten Sie 30 Tage Zeit, für die Anomalie „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ auszuwählen. Nach Ablauf der 30 Tage können Sie als Herausgeber für die Anomalien keine Aktionen mehr ausführen.

> [!IMPORTANT]
> Nicht gemeldete Überschreitungsnutzungseinheiten können nicht rückwirkend korrigiert und finanziell beglichen werden.

Sie können alle Anomalien (bestätigt oder nicht bestätigt) für einen ausgewählten Berechnungszeitraum anzeigen. Die verschiedenen Berechnungszeiträume sind die letzten 30 Tage, die letzten 60 Tage und die letzten 90 Tage.

> [!IMPORTANT]
> Sie sollten innerhalb von 30 Tagen nach erstmaliger Meldung einer Anomalie in Partner Center tätig werden.

Per Filter im Widget können Sie einzelne Angebote und einzelne benutzerdefinierte Verbrauchseinheiten auswählen.

Wenn Sie eine Überschreitungsnutzung als Anomalie gekennzeichnet haben oder ein Modell, das eine Anomalie gemeldet hat, bestätigt haben, können Sie die Auswahl nicht mehr zurück in „Keine Anomalie“ ändern.

> [!IMPORTANT]
> Wenn ein zu hoher Betrag berechnet wurde, können Sie Überschreitungsnutzungen erneut übermitteln.

## <a name="see-also"></a>Weitere Informationen
- [Volumenabrechnung für SaaS mit dem Messungsdienst für den kommerziellen Marketplace](./partner-center-portal/saas-metered-billing.md)
- [Getaktete Abrechnung für verwaltete Anwendungen](./partner-center-portal/azure-app-metered-billing.md)
