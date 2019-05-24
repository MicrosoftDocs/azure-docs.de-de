---
title: Überwachen von Verbrauch und Ausgaben mit Kostenwarnungen | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Verbrauch und Ausgaben in Azure Cost Management mithilfe von Kostenwarnungen überwachen können.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: f1bf62596b6edcc6fff6572e431f3a777be93f05
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002093"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Verwenden von Kostenwarnungen zum Überwachen von Verbrauch und Ausgaben

Dieser Artikel enthält grundlegende Informationen zur Verwendung von Cost Management-Warnungen zum Überwachen der Azure-Nutzung und der entsprechenden Ausgaben. Kostenwarnungen werden automatisch generiert, wenn Azure-Ressourcen genutzt werden. In den Warnungen werden alle aktiven Kostenverwaltungs- und Abrechnungswarnungen zusammen angezeigt. Wenn Ihr Verbrauch einen bestimmten Schwellenwert erreicht, werden in Cost Management Warnungen generiert. Es gibt drei Typen von Kostenwarnungen: Budgetwarnungen, Guthabenwarnungen und Warnungen zum Ausgabenkontingent für Abteilungen.

## <a name="budget-alerts"></a>Budgetwarnungen

Mit Budgetwarnungen werden Sie benachrichtigt, wenn Ausgaben basierend auf Verbrauch oder Kosten den in der [Warnungsbedingung für das Budget](tutorial-acm-create-budgets.md) definierten Betrag erreichen oder überschreiten. Cost Management-Budgets werden über das Azure-Portal oder die [Azure Consumption](https://docs.microsoft.com/rest/api/consumption)-API erstellt.

Im Azure-Portal werden Budgets nach Kosten definiert. Bei Verwendung der Azure Consumption-API werden Budgets nach Kosten oder nach Verbrauch definiert. Budgetwarnungen unterstützen kostenbasierte sowie nutzungsbasierte Budgets. Budgetwarnungen werden immer automatisch generiert, wenn die Warnungsbedingungen für das Budget erfüllt sind. Sie können alle Kostenwarnungen im Azure-Portal anzeigen. Immer wenn eine Warnung generiert wird, wird sie in den Kostenwarnungen angezeigt. Außerdem wird eine Warnung per E-Mail an alle Personen in der Liste der Warnungsempfänger für das Budget gesendet.

## <a name="credit-alerts"></a>Guthabenwarnungen

Mit Guthabenwarnungen werden Sie benachrichtigt, wenn die Zahlungsverpflichtungen des Azure-Guthabens genutzt werden. Zahlungsverpflichtungen betreffen Organisationen mit Enterprise Agreements. Guthabenwarnungen werden bei 90 % und bei 100 % Ihres Azure-Guthabens generiert. Immer wenn eine Warnung generiert wird, wird sie in den Kostenwarnungen und in den an die Kontobesitzer gesendeten E-Mails aufgeführt.

## <a name="department-spending-quota-alerts"></a>Warnungen zum Ausgabenkontingent für Abteilungen

Mit Warnungen zum Ausgabenkontingent für Abteilungen werden Sie benachrichtigt, wenn die Ausgaben einer Abteilung einen festgelegten Schwellenwert des Kontingents erreichen. Ausgabenkontingente werden im EA-Portal konfiguriert. Immer wenn ein Schwellenwert erreicht wird, wird eine E-Mail an Abteilungsbesitzer generiert, und die Warnung wird in den Kostenwarnungen angezeigt. Beispielsweise bei 50 % oder 75 % des Kontingents.

## <a name="supported-alert-features-by-offer-categories"></a>Unterstützte Warnungsfunktionen nach Angebotskategorien

Die Unterstützung von Warnungstypen hängt von der Art Ihres Azure-Kontos ab (Microsoft-Angebot). Die folgende Tabelle zeigt die Warnungsfunktionen, die von verschiedenen Microsoft-Angeboten unterstützt werden. Die vollständige Liste der Microsoft-Angebote finden Sie unter [Grundlegendes zu Cost Management-Daten](understand-cost-mgt-data.md).

| Warnungstyp | Enterprise Agreement | Microsoft-Kundenvereinbarung | Direkt via Internet/Nutzungsbasierte Zahlung |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Quelle | ✔ |✘ | ✘ |
| Ausgabenkontingent für Abteilung | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Anzeigen von Kostenwarnungen

Um Kostenwarnungen anzuzeigen, öffnen Sie im Azure-Portal den gewünschten Bereich, und wählen Sie im Menü **Budgets** aus. Verwenden Sie **Bereich**, um in einen anderen Bereich zu wechseln. Wählen Sie im Menü **Kostenwarnungen** aus. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

![Beispielabbildung für in Cost Management angezeigte Warnungen](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Die Gesamtanzahl der aktiven und geschlossenen Warnungen wird auf der Seite der Kostenwarnungen angezeigt.

Für alle Warnungen wird der jeweilige Warnungstyp angezeigt. In einer Budgetwarnung wird der Grund für die Generierung und der Name des entsprechenden Budgets angezeigt. Jede Warnung enthält das Datum, an dem sie erstellt wurde, den jeweiligen Status und den Bereich (Abonnement oder Verwaltungsgruppe), auf den sich die Warnung bezieht.

Mögliche Werte für den Status sind **Aktiv** und **Geschlossen**. Der Status „Aktiv“ gibt an, dass die Warnung weiterhin relevant ist. Der Status „Geschlossen“ gibt an, dass ein Benutzer die Warnung markiert und als nicht mehr relevant festgelegt hat.

Wählen Sie eine Warnung in der Liste aus, um die zugehörigen Details anzuzeigen. In den Warnungsdetails werden weitere Informationen zu der Warnung angezeigt. Budgetwarnungen enthalten einen Link zu dem Budget. Wenn für eine Budgetwarnung eine Empfehlung verfügbar ist, wird auch ein Link zu der Empfehlung angezeigt. Budgetwarnungen, Guthabenwarnungen und Warnungen zum Ausgabenkontingent für Abteilungen enthalten einen Link zu der Kostenanalyse, in der Sie die Kosten für den Bereich der Warnung überprüfen können. Im folgenden Beispiel werden die Ausgaben für eine Abteilung mit Warnungsdetails angezeigt.

![Beispielabbildung für die Ausgaben einer Abteilung mit Warnungsdetails](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Wenn Sie die Details einer geschlossenen Warnung anzeigen, können Sie sie erneut aktivieren, wenn eine manuelle Aktion erforderlich ist. Die folgende Abbildung zeigt ein Beispiel.

![Beispielabbildung für die Optionen zum Schließen und erneuten Aktivieren](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Weitere Informationen

- Wenn Sie noch kein Budget erstellt oder keine Warnungsbedingungen für ein Budget festgelegt haben, finden Sie entsprechende Anweisungen im Tutorial [Erstellen und Verwalten von Budgets](tutorial-acm-create-budgets.md).
