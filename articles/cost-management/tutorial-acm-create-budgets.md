---
title: 'Tutorial: Erstellen und Verwalten von Azure-Budgets | Microsoft-Dokumentation'
description: Dieses Tutorial hilft bei der Planung und Abrechnung der Kosten für Azure-Dienste, die Sie in Anspruch nehmen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: eab45948b5f931377396d93d93e8955ba0f3e767
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65792853"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: Erstellen und Verwalten von Azure-Budgets

Budgets in Cost Management helfen Ihnen, die organisatorische Verantwortlichkeit zu planen und zu steigern. Mit Budgets können Sie die Azure-Dienste abrechnen, die Sie in einem bestimmten Zeitraum in Anspruch nehmen oder abonnieren. Sie unterstützen Sie dabei, andere über ihre Ausgaben zu informieren, um die Kosten proaktiv zu steuern und die Entwicklung der Ausgaben im Laufe der Zeit zu überwachen. Wenn die erstellte Budgetschwellenwerte überschritten werden, werden nur Benachrichtigungen ausgelöst. Keines Ihrer Ressourcen wird beeinträchtigt, und die Nutzung wird nicht beendet. Sie können Budgets verwenden, um Ausgaben bei der Kostenanalyse zu vergleichen und zu verfolgen.

Monatliche Budgets werden alle vier Stunden mit den Ausgaben verglichen. Daten und Benachrichtigungen zu verbrauchten Ressourcen sind jedoch binnen acht Stunden verfügbar.  

Die Budgets werden am Ende eines Zeitraums (monatlich, vierteljährlich oder jährlich) auf den gleichen Budgetbetrag automatisch zurückgesetzt, wenn Sie ein zukünftiges Ablaufdatum auswählen. Da sie auf den gleichen Budgetbetrag zurückgesetzt werden, müssen Sie separate Budgets anlegen, wenn die budgetierten Währungsbeträge für zukünftige Zeiträume abweichen.

Die Beispiele in diesem Tutorial führen Sie durch die Erstellung und Bearbeitung eines Budgets für ein Abonnement von Azure Enterprise Agreement (EA).

Sehen Sie sich das Video [Erstellen von Budgets zur Überwachung Ihrer Ausgaben mit Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) an, um zu erfahren, wie Sie in Azure Budgets erstellen können, um Ausgaben zu überwachen.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Budgets im Azure-Portal
> * Bearbeiten eines Budgets

## <a name="prerequisites"></a>Voraussetzungen

Budgets werden für eine Vielzahl von Azure-Kontotypen unterstützt. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](understand-cost-mgt-data.md). Um Budgets anzeigen zu können, müssen Sie mindestens über Lesezugriff auf Ihr Azure-Konto verfügen.

 Zum Anzeigen von Budgets für Azure EA-Abonnements müssen Sie über Lesezugriff verfügen. Zum Erstellen und Verwalten von Budgets müssen Sie über die Berechtigung „Mitwirkender“ verfügen. Sie können individuelle Budgets für EA-Abonnements und Ressourcengruppen erstellen. Sie können jedoch keine Budgets für EA-Abrechnungskonten erstellen.

Die folgenden Azure-Berechtigungen (oder Bereich) werden pro Abonnement für Budgets nach Benutzer und Gruppe unterstützt. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

- Besitzer – kann Budgets für ein Abonnement erstellen, ändern oder löschen.
- Mitwirkender und Mitwirkender für Cost Management – kann eigene Budgets erstellen, ändern oder löschen. Kann den Betrag für von anderen Personen erstellten Budgets ändern.
- Leser und Leser für Cost Management – kann Budgets anzeigen, für die er die Berechtigung hat.

Weitere Informationen zum Zuweisen der Berechtigung für Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Daten in Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

- Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-budget-in-the-azure-portal"></a>Erstellen eines Budgets im Azure-Portal

Sie können ein Azure-Abonnementbudget für den Zeitraum eines Monats, Quartals oder Jahres erstellen. Ihr Navigationsinhalt im Azure-Portal legt fest, ob Sie ein Budget für ein Abonnement oder für eine Verwaltungsgruppe anlegen.

Um ein Budget zu erstellen oder anzuzeigen, öffnen Sie den gewünschten Bereich im Azure-Portal, und wählen Sie im Menü **Budgets** aus. Navigieren Sie beispielsweise zu **Abonnements**, wählen Sie ein Abonnement in der Liste aus, und wählen Sie dann **Budgets** im Menü aus. Verwenden Sie die Pille **Bereich**, um zu einem anderen Bereich in „Budgets“ zu wechseln, z. B. zu einer Verwaltungsgruppe. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

Nachdem Sie Budgets erstellt haben, wird eine einfache Ansicht Ihrer aktuellen Ausgaben im Abgleich mit dem Budget angezeigt.

Klicken Sie auf **Hinzufügen**.

![Im Azure-Portal angezeigte Cost Management-Budgets](./media/tutorial-acm-create-budgets/budgets01.png)

Geben Sie im Fenster **Budget erstellen** einen Namen und einen Betrag für das Budget ein. Wählen Sie anschließend eine Dauer von einem Monat, einem Quartal oder einem Jahr aus. Wählen Sie als Nächstes ein Enddatum aus. Budgets erfordern mindestens einen Kostenschwellenwert (% des Budgets) und eine entsprechende E-Mail-Adresse. Sie können optional bis zu fünf Schwellenwerte und fünf E-Mail-Adressen in ein einzelnes Budget aufnehmen. Wenn ein Budgetschwellenwert erreicht wird, werden E-Mail-Benachrichtigungen normalerweise in weniger als acht Stunden empfangen. Weitere Informationen zu Benachrichtigungen finden Sie unter [Verwenden von Kostenwarnungen](cost-mgt-alerts-monitor-usage-spending.md).

Wenn Sie über ein Abonnement mit nutzungsbasierter Bezahlung, ein MSDN-Abonnement oder ein Visual Studio-Abonnement verfügen, stimmt der Abrechnungszeitraum für Ihre Rechnung möglicherweise nicht mit dem Kalendermonat überein. Bei diesen Arten von Abonnements und Ressourcengruppen können Sie ein Budget erstellen, das Ihrem Rechnungszeitraum oder Kalendermonaten entspricht. Zum Erstellen eines Budgets, das Ihrem Rechnungszeitraum entspricht, wählen Sie als Zurücksetzungszeitraum „Abrechnungsmonat“, „Abrechnungsquartal“ oder „Abrechnungsjahr“ aus. Wenn Sie ein Budget entsprechend dem Kalendermonat erstellen möchten, wählen Sie als Zurücksetzungszeitraum „Monatlich“, „Vierteljährlich“ oder „Jährlich“ aus.

Hier ist ein Beispiel für das Erstellen einer Monatsbudget von 4.500 USD. Eine E-Mail-Warnung wird generiert, wenn 90 % des Budgets erreicht sind.

![Beispielinformationen im Feld „Budget erstellen“](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Die Erstellung eines Quartalsbudgets entspricht der eines Monatsbudget. Der Unterschied besteht darin, dass der Budgetbetrag für das Quartal gleichmäßig auf die drei Monate des Quartals verteilt wird. Erwartungsgemäß wird ein jährlicher Budgetbetrag gleichmäßig auf alle 12 Monate des Kalenderjahres verteilt.

Die aktuellen Ausgaben werden im Abgleich mit dem Budgets aktualisiert, wenn Cost Management aktualisierte Abrechnungsdaten erhält. In der Regel erfolgt dies täglich.

![Beispielinformationen mit aktuellen Ausgaben im Vergleich zu Budgets](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Nach der Erstellung eines Budgets wird es in der Kostenanalyse angezeigt. Die Betrachtung Ihres Budgets in Bezug auf Ihren Ausgabentrend ist einer der ersten Schritte in der [Analyse Ihrer Kosten und Ausgaben](quick-acm-cost-analysis.md).

![Beispiel für in der Kostenanalyse angezeigtes Budget und Ausgaben](./media/tutorial-acm-create-budgets/cost-analysis.png)

Im obigen Beispiel haben Sie ein Budget für ein Abonnement erstellt. Sie können jedoch auch ein Budget für eine Ressourcengruppe erstellen. Wenn Sie ein Budget für eine Ressourcengruppe erstellen möchten, navigieren Sie zu **Cost Management + Abrechnung** &gt; **Abonnements** &gt;wählen Sie ein Abonnement > **Ressourcengruppen** > wählen Sie eine Ressourcengruppe aus > **Budgets** > und denn Budget **hinzufügen**.

## <a name="edit-a-budget"></a>Bearbeiten eines Budgets

Abhängig von Ihrer Zugriffsebene können Sie ein Budget bearbeiten, um seine Eigenschaften zu ändern. Im folgenden Beispiel sind einige der Eigenschaften schreibgeschützt, da der Benutzer nur die Berechtigung „Mitwirkender“ für das Abonnement besitzt. Derzeit ist das **Ablaufdatum** deaktiviert und kann nach dem Festlegen nicht geändert werden.

![Beispiel für die Bearbeitung eines Budgets zum Ändern verschiedener Eigenschaften](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>Auslösen einer Aktionsgruppe

Wenn Sie ein Budget für einen Abonnement- oder Ressourcengruppenbereich erstellen oder bearbeiten, können Sie es so konfigurieren, dass es eine Aktionsgruppe aufruft. Die Aktionsgruppe kann verschiedene Aktionen ausführen, wenn der Budgetschwellenwert erreicht wird. Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/platform/action-groups.md). Weitere Informationen zum Verwenden von budgetbasierter Automatisierung mit Aktionsgruppen finden Sie unter [Verwalten von Kosten mit Azure-Budgets](../billing/billing-cost-management-budget-scenario.md).

Um Aktionsgruppen zu erstellen oder zu aktualisieren, klicken Sie beim Erstellen oder Bearbeiten eines Budgets auf **Aktionsgruppen verwalten**.

![Beispiel für das Erstellen eines Budgets zum Anzeigen von „Aktionsgruppen verwalten“](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Klicken Sie anschließend auf **Aktionsgruppe hinzufügen**, und erstellen Sie die Aktionsgruppe.


![Abbildung des Dialogfelds „Aktionsgruppe hinzufügen“](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Nachdem die Aktionsgruppe erstellt wurde, schließen Sie das Dialogfeld, um zu Ihrem Budget zurückzukehren.

Konfigurieren Sie Ihr Budget so, dass Ihre Aktionsgruppe verwendet wird, wenn ein bestimmter Schwellenwert erreicht wird. Bis zu fünf verschiedene Schwellenwerte werden unterstützt.

![Beispiel mit einer Aktionsgruppenauswahl für eine Warnungsbedingung](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Das folgende Beispiel zeigt Budgetschwellenwerte für 50 %, 75 % und 100 %. Jeder ist so konfiguriert, dass die angegebenen Aktionen innerhalb der angegebenen Aktionsgruppe ausgelöst werden.

![Beispiel mit Warnungsbedingungen, die mit verschiedenen Aktionsgruppen und Aktionenstypen konfiguriert wurden](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Budgets im Azure-Portal
> * Bearbeiten eines Budgets

Fahren Sie mit dem nächsten Tutorial fort, um einen sich wiederholenden Export für Ihre Cost Management-Daten zu erstellen.

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von exportierten Daten](tutorial-export-acm-data.md)
