---
title: Verwalten von Cloudyn-Budgets in Azure | Microsoft-Dokumentation
description: Anhand dieses Artikels können Sie schnell Budgets erstellen und in Cloudyn verwalten.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 9223f6d20f4f5e3901465379fe53c7b9c338e53f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969163"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Verwalten von Azure-Budgets mit Cloudyn

Die Einrichtung von Budgets und budgetbasierten Warnmeldungen hilft Ihnen, Ihre Cloudgovernance und Verantwortlichkeit zu verbessern. Anhand dieses Artikels können Sie schnell Budgets erstellen und in Cloudyn verwalten.

Wenn Sie ein Unternehmenskonto oder MSP-Konto haben, können Sie Ihre hierarchische Kostenentitätsstruktur verwenden, um monatliche Budgetkontingente verschiedenen Geschäftseinheiten, Abteilungen oder anderen Kostenentitäten zuzuordnen. Wenn Sie ein Premium-Konto haben, können Sie die Budgetverwaltung nutzen, die dann auf Ihre gesamten Cloudausgaben angewendet wird. Alle Budgets werden manuell zugewiesen.

Auf der Grundlage der zugewiesenen Budgets können Sie Schwellenwertwarnungen basierend auf dem prozentualen Anteil des verbrauchten Budgets festlegen und den Schweregrad der einzelnen Schwellenwerte festlegen.

Das zugewiesene Budget wird im Budgetbericht angezeigt. Benutzer können sehen, wann ihre Ausgaben über, unter oder auf dem Niveau ihres Verbrauchs im Laufe der Zeit liegen. Wenn Sie oben im Budgetbericht **Felder ein-/ausblenden** auswählen, können Sie die Kosten, das Budget, die akkumulierten Kosten und das Gesamtbudget anzeigen.

Azure Cost Management bietet ähnliche Funktionen wie Cloudyn. Azure Cost Management ist eine native Azure-Kostenverwaltungslösung. Die Lösung unterstützt Sie beim Analysieren von Kosten, beim Erstellen und Verwalten von Budgets, beim Exportieren von Daten sowie bei der Prüfung und Umsetzung von Optimierungsempfehlungen, um Kosten zu sparen. Weitere Informationen zu Budgets in Cost Management finden Sie unter [Erstellen und Verwalten von Budgets](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Erstellen von Budgets

Wenn Sie ein Budget erstellen, legen Sie es für Ihr Geschäftsjahr fest, sodass es für eine bestimmte Entität gilt.

So erstellen Sie ein Budget und weisen es einer Entität zu:

1. Navigieren Sie zu **Kosten** &gt; **Cost Management** &gt; **Budget**.
2. Wählen Sie auf der Seite „Budgetverwaltung“ unter **Entität** die Entität, in der Sie das Budget erstellen möchten.
3. Wählen Sie unter „Budgetjahr“ das Jahr, für das Sie das Budget erstellen möchten.
4. Legen Sie für jeden Monat einen Budgetwert fest. Wenn Sie fertig sind, klicken Sie auf **Speichern**.
In diesem Beispiel wurde das Monatsbudget für Juni 2018 auf 135.000 USD festgelegt. Das Gesamtbudget für das Jahr beläuft sich auf 1.615.000,00 USD.
![Erstellen Sie eine Budget-Seite, auf der Sie ein Budget für jeden Monat festlegen](./media/manage-budgets/set-budget.png)


So importieren Sie eine Datei für das Jahresbudget:

1. Wählen Sie unter **Aktionen** die Option **Export**, um eine leere CSV-Vorlage als Basis für Ihr Budget herunterzuladen.
2. Tragen Sie in die CSV-Datei Ihre Budgeteinträge ein, und speichern Sie sie lokal.
3. Wählen Sie unter **Aktionen** die Option **Import**.
4. Wählen Sie Ihre gespeicherte Datei aus, und klicken Sie auf **OK**.

Um Ihr abgeschlossenes Budget als CSV-Datei zu exportieren, wählen Sie unter **Aktionen** die Option **Export**, um die Datei herunterzuladen.

## <a name="view-budget-in-reports"></a>Anzeigen des Budgets in Berichten

Nach Abschluss wird Ihr Budget im Großteil der Kostenberichte unter **Kosten** &gt; **Kostenanalyse** und im Bericht mit der Gegenüberstellung der Kosten und des Budgets im Lauf der Zeit aufgeführt. Unter **Aktionen** können Sie auch Berichte basierend auf Budgetschwellenwerten planen.

Hier ist ein Beispiel für einen Kostenanalysebericht. Er zeigt das Gesamtbudget und die Kosten nach Workload und Verwendungstyp seit Jahresbeginn.

![Beispiel für Kostenanalysebericht mit Budget](./media/manage-budgets/cost-analysis-budget-example.png)

Im folgenden Beispiel wird der 22. Juni als aktuelles Datum angenommen. Die Kosten für Juni 2018 belaufen sich auf 71.611,28 USD im Vergleich zum monatlichen Budget von 135.000 USD. Die Kosten sind deutlich niedriger als das monatliche Budget, da es noch acht Tage bis zum Monatsende gibt.

Eine weitere Möglichkeit, den Bericht anzuzeigen, ist ein Vergleich der akkumulierten Kosten mit Ihrem Budget. Um die akkumulierten Kosten anzuzeigen, wählen Sie **Felder ein-/ausblenden**, anschließend **Akkumulierte Kosten** und **Gesamtbudget**. Hier ist ein Beispiel für die akkumulierten Kosten seit Anfang des Jahres.

![Beispiel der kumulierten Kosten und des Gesamtbudgets im Bericht mit dem Vergleich der Kosten und dem Budget im Zeitverlauf](./media/manage-budgets/accumulated-budget.png)

Irgendwann in der Zukunft könnten Ihre akkumulierten Kosten Ihr Budget übersteigen. Sie können das leichter erkennen, wenn Sie die Diagrammansicht auf den Typ _Zeile_ ändern.

![Budget in einem Liniendiagramm im Bericht „Kosten nach Monaten“](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Erstellen von Budgetwarnungen für einen Filter

Im vorherigen Beispiel sehen Sie, dass sich die akkumulierten Kosten dem Budget angenähert haben. Sie können automatische Budgetwarnungen erstellen, sodass Sie benachrichtigt werden, wenn die Ausgaben Ihr Budget erreichen oder überschreiten. Grundsätzlich ist eine Warnung ein geplanter Bericht mit einem Schwellenwert. Budgetwarnschwellenwerte beinhalten:

- Restkosten im Vergleich zum Budget – zum Angeben des Schwellenwerts für den Währungswert
- Kosten-Prozentanteil im Vergleich zum Budget – zum Angeben eines Schwellenwerts für den Prozentwert

Schauen wir uns ein Beispiel an.

Klicken Sie im Bericht mit dem Vergleich der Kosten und des Budgets im Laufe der Zeit auf **Aktionen** , und wählen Sie **Bericht planen** aus. Wählen Sie auf der Registerkarte „Schwellenwert“ eine Schwellenwertmetrik aus. Zum Beispiel **Kosten-Prozentanteil im Vergleich zum Budget**. Wählen Sie einen Warnungstypen, und geben Sie einen Prozentwert für das Budget ein. Wenn Sie nur einmalig benachrichtigt werden möchten, geben Sie unter **Anzahl der aufeinanderfolgenden Warnungen** _1_ ein. Klicken Sie auf **Speichern**.

![Erstellen einer Budgetwarnung im Feld „Speichern“ oder „Diesen Bericht planen“](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Nächste Schritte

- Das erste Tutorial zu Cloudyn finden Sie bei Bedarf unter [Überprüfen der Nutzung und der Kosten](tutorial-review-usage.md).
- Weitere Informationen zu den in Cloudyn verfügbaren Berichten finden Sie [hier](use-reports.md).
