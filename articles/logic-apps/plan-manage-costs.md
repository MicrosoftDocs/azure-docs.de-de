---
title: Planen der Kostenverwaltung für Azure Logic Apps
description: Erfahren Sie, wie Sie Kosten für Azure Logic Apps mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 58e12862cf00b500bced105d67fede8599c2a257
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99179834"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Planen und Verwalten von Kosten für Azure Logic Apps

Dieser Artikel unterstützt Sie beim Planen und Verwalten von Kosten für Azure Logic Apps. Ehe Sie in diesem Dienst Ressourcen erstellen oder hinzufügen, sollten Sie Ihre Kosten mithilfe des Azure-Preisrechners abschätzen. Nachdem Sie mit der Nutzung von Logic Apps-Ressourcen begonnen haben, können Sie mithilfe von [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Budgets festlegen und Kosten überwachen. Sie können auch prognostizierte Kosten überprüfen und Ausgabentrends überwachen, um Bereiche auszumachen, in denen ggf. Maßnahmen erforderlich sind.

Beachten Sie, dass Kosten für Logic Apps nur einen Teil der monatlichen Kosten in Ihrer Azure-Rechnung ausmachen. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für Logic Apps erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die in Ihrem Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern. Nachdem Sie sich mit der Verwaltung der Kosten für Logic Apps vertraut gemacht haben, können Sie ähnliche Methoden zur Verwaltung der Kosten für alle Azure-Dienste befolgen, die in Ihrem Abonnement genutzt werden.

## <a name="prerequisites"></a>Voraussetzungen

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) unterstützt die meisten Azure-Kontotypen. Unter [Grundlegendes zu Cost Management-Daten](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) finden Sie alle unterstützten Kontotypen. Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf Ihr Azure-Konto verfügen.

Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Grundlegendes zum Abrechnungsmodell

Azure Logic Apps wird in Azure-Infrastruktur ausgeführt, für die [Kosten anfallen](https://azure.microsoft.com/pricing/details/logic-apps/), sobald Sie neue Ressourcen bereitstellen. Stellen Sie sicher, dass Sie das [Abrechnungsmodell für den Dienst Azure Logic Apps samt zugehörigen Azure-Ressourcen](logic-apps-pricing.md) verstehen und Kosten unter Berücksichtigung dieser Abhängigkeiten verwalten, wenn Sie Änderungen an bereitgestellten Ressourcen vornehmen.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Üblicherweise anfallende Kosten für Azure Logic Apps

Der Dienst Azure Logic Apps bietet basierend auf den von Ihnen erstellten und genutzten Ressourcen unterschiedliche Preismodelle:

* Logik-App-Ressourcen, die Sie im mehrinstanzenfähigen Dienst Azure Logic Apps erstellen und ausführen, unterliegen einem [nutzungsabhängigen Preismodell](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Logik-App-Ressourcen, die Sie in einer [Integrationsdienstumgebung (Integration Service Environment, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erstellen und ausführen, unterliegen einem [Festpreismodell](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Es folgen weitere Ressourcen, die Kosten verursachen, wenn Sie sie für den Einsatz mit Logik-Apps erstellen:

* Ein [Integrationskonto](../logic-apps/logic-apps-pricing.md#integration-accounts) ist eine gesonderte Ressource, die Sie erstellen und zum Aufbauen von B2B-Integrationen mit Logik-Apps verknüpfen. Integrationskonten unterliegen einem [Festpreismodell](../logic-apps/logic-apps-pricing.md#integration-accounts), bei dem sich der Preis nach dem Typ des Integrationskontos oder dem von Ihnen gewählten *Tarif* richtet.

* Eine [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) ist eine gesonderte Ressource, die Sie als Bereitstellungsort für Logik-Apps erstellen, die direkten Zugriff auf Ressourcen in einem virtuellen Netzwerk benötigen. ISEs unterliegen einem [Festpreismodell](../logic-apps/logic-apps-pricing.md#fixed-pricing), dessen Tarif auf der von Ihnen erstellten ISE SKU und anderen Einstellungen basiert.

* Ein [benutzerdefinierter Connector](../logic-apps/logic-apps-pricing.md#consumption-pricing) ist eine separate Ressource, die Sie für eine REST-API erstellen, die keinen vorgefertigten Connector hat, den Sie in Ihren Logik-Apps verwenden können. Für die Ausführung benutzerdefinierter Connectors gilt ein [nutzungsabhängiges Preismodell](../logic-apps/logic-apps-pricing.md#consumption-pricing), außer wenn Sie sie in einer ISE einsetzen.

* Im mehrinstanzenfähigen Dienst Azure Logic Apps fallen für [Datenaufbewahrung und Speichernutzung](../logic-apps/logic-apps-pricing.md#data-retention) Kosten im Rahmen eines [Festpreismodells](../logic-apps/logic-apps-pricing.md#fixed-pricing) an. Beispielsweise werden Ein- und Ausgaben aus dem Ausführungsverlauf im Hintergrundspeicher aufbewahrt, der sich von Speicherressourcen unterscheidet, die Sie unabhängig in Ihrer Logik-App erstellen, verwalten und nutzen.

  In einer ISE entstehen durch Datenaufbewahrung und Speichernutzung keine Kosten.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Nachdem Sie eine Logik-App gelöscht haben, können im Dienst Azure Logic Apps keine neuen Workflowinstanzen erstellt oder ausgeführt werden. Alle laufenden und noch ausstehenden Ausführungen werden jedoch bis zu ihrem Ende fortgesetzt. Abhängig von der Anzahl dieser Ausführungen kann dies einige Zeit in Anspruch nehmen. Weitere Informationen finden Sie unter [Verwalten von Logik-Apps](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Wenn Sie nach dem Löschen einer Logik-App noch über die folgenden Ressourcen verfügen, werden so lange Kosten verursacht, bis Sie sie löschen:

* Azure-Ressourcen, die Sie unabhängig von der Logik-App, die sich mit diesen Ressourcen verbindet, erstellen und verwalten, z. B. Azure-Funktions-Apps, Event Hub- und Event Grid-Instanzen usw.

* Integrationskonten

* Integrationsdienstumgebungen (ISEs)

  Wenn Sie [eine ISE löschen](ise-manage-integration-service-environment.md#delete-ise), sind das zugehörige virtuelle Azure-Netzwerk, die Subnetze und andere zugehörige Ressourcen weiterhin vorhanden. Nachdem Sie die ISE gelöscht haben, müssen Sie möglicherweise eine bestimmte Anzahl von Stunden warten, ehe Sie versuchen können, das virtuelle Netzwerk oder die Subnetze zu löschen.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Nutzen von Guthaben mit Azure Logic Apps

Sie können Gebühren für Azure Logic Apps mit Ihrem EA-Mindestverbrauchsguthaben verrechnen. Allerdings können Sie mit dem EA-Mindestverbrauchsguthaben keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus Azure Marketplace, verrechnen.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Schätzen der Kosten

Ehe Sie in Azure Logic Apps Ressourcen erstellen, sollten Sie Ihre Kosten mithilfe des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) abschätzen. Weitere Informationen finden Sie unter [Preismodell für Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. Wählen Sie auf der Seite [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) im Menü auf der linken Seite **Integration** > **Azure Logic Apps** aus.

   ![Screenshot des Azure-Preisrechners mit Auswahl von „Azure Logic Apps“.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Scrollen Sie auf der Seite nach unten zum Preisrechner für Azure Logic Apps. Geben Sie in den verschiedenen Abschnitten für Azure-Ressourcen, die in direktem Zusammenhang mit Azure Logic Apps stehen, die Anzahl der Ressourcen ein, die Sie verwenden möchten, sowie die Anzahl der Intervalle, in denen Sie diese Ressourcen verwenden möchten.

   Dieser Screenshot zeigt ein Beispiel einer Kostenschätzung mit dem Preisrechner:

   ![Beispiel für die geschätzten Kosten im Azure-Preisrechner](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Um Ihre Kostenschätzungen zu aktualisieren, sobald Sie neue zugehörige Ressourcen erstellen und einsetzen, kehren Sie zu diesem Rechner zurück, und aktualisieren Sie hier die entsprechenden Ressourcen.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Erstellen von Budgets und Warnungen

Um Sie bei der aktiven Verwaltung der Kosten Ihres Azure-Kontos oder Abonnements zu unterstützen, können Sie [Budgets](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) und [Warnungen](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) einrichten, indem Sie den Dienst [Azure Cost Management and Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) und dessen Funktionen nutzen.  Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich.

Basierend auf den Ausgaben im Vergleich zu Budget- und Kostenschwellenwerten werden die Beteiligten durch Warnungen automatisch über Ausgabenanomalien und das Risiko einer Ausgabenüberschreitung informiert. Wenn Sie eine detailliertere Überwachung wünschen, können Sie auch Budgets mit Filtern für bestimmte Ressourcen oder Dienste in Azure festlegen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die zusätzliche Kosten verursachen. Weitere Informationen zu Filteroptionen finden Sie unter [Gruppierungs- und Filteroptionen](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Überwachen der Kosten

Die Kosten pro Ressourcennutzungseinheit variieren je nach Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder Einheitennutzung (Bytes, Megabytes usw.). Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Wenn Sie die Funktionen zur Kostenanalyse verwenden, können Sie die Kosten als Graphen und Tabellen in verschiedenen Zeitintervallen anzeigen. Wenn Sie Budgets und Kostenprognosen aufgestellt haben, können Sie auch problemlos feststellen, wo Budgets überschritten wurden und wo es zu Mehrausgaben gekommen sein könnte.

Sobald Sie anfangen, Kosten für Ressourcen zu verursachen, die Sie in Azure erstellen oder nutzen, können Sie diese Kosten auf die folgenden Arten überprüfen und überwachen:

* [Überwachen von Logik-App-Ausführungen und Speichernutzung](#monitor-billing-metrics) mithilfe von Azure Monitor

* Erstellen einer [Kostenanalyse](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) mithilfe von [Azure Cost Management and Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Überwachen von Logik-App-Ausführungen und Speichernutzung

In Azure Monitor können Sie diese Metriken für eine bestimmte Logik-App einsehen:

* Ausführungen abrechenbarer Aktionen
* Ausführungen abrechenbarer Trigger
* Nutzungsabrechnung für Ausführungen nativer Vorgänge
* Nutzungsabrechnung für Ausführungen von Standardconnectors
* Nutzungsabrechnung für Ausführungen mit Speicherverbrauch
* Gesamtzahl der abrechenbaren Ausführungen

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Anzeigen von Metriken zu Ausführungen und Speichernutzung

1. Suchen Sie im Azure-Portal nach Ihrer Logik-App, und öffnen Sie sie. Wählen Sie im Menü Ihrer Logik-App unter **Überwachung** die Option **Metriken** aus.

1. Öffnen Sie im rechten Bereich unter **Diagrammtitel** in der Metrikleiste die Liste **Metrik**, und wählen Sie die gewünschte Metrik aus.

   > [!NOTE]
   > Die Speichernutzung wird als die Anzahl der Speichereinheiten (GB) gemessen, die Ihre Logik-App nutzt und abgerechnet wird. Ausführungen, die im Speicher weniger als 500 MB belegen, werden in der Überwachungsansicht möglicherweise nicht angezeigt, jedoch trotzdem in Rechnung gestellt.

   ![Screenshot mit dem Bereich „Metriken“ mit geöffneter Liste „Metrik“.](./media/logic-apps-pricing/select-metric.png)

1. Wählen Sie rechts oben im Bereich den gewünschten Zeitraum aus.

1. [Führen Sie die folgenden Schritte aus](#view-input-output-sizes), um andere Speichernutzungsdaten zu untersuchen, insbesondere die Größe von Ein- und Ausgaben von Aktionen im Ausführungsverlauf Ihrer Logik-App.

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Anzeigen der Größen von Ein- und Ausgaben von Aktionen im Ausführungsverlauf

1. Suchen Sie im Azure-Portal nach Ihrer Logik-App, und öffnen Sie sie.

1. Wählen Sie im Menü Ihrer Logik-App die Option **Übersicht** aus.

1. Wählen Sie im rechten Bereich unter **Ausführungsverlauf** die Ausführung aus, zu der die Ein- und Ausgaben gehören, Sie einsehen möchten.

1. Wählen Sie unter **Logik-App-Ausführung** die Option **Ausführungsdetails** aus.

1. Wählen Sie im Bereich **Details zur Logik-App-Ausführung** in der Tabelle mit den Aktionen, in der Status und Dauer jeder Aktion aufgeführt sind, die Aktion aus, die Sie anzeigen möchten.

1. Suchen Sie im Bereich **Logik-App-Aktion** die Größe der Eingaben und Ausgaben dieser Aktion. Suchen Sie unter **Eingabelink** und **Ausgabelink** die Links zu diesen Ein- und Ausgaben.

   > [!NOTE]
   > Bei Schleifen werden nur für die Aktionen der obersten Ebene Ein- und Ausgaben angezeigt. Für Aktionen innerhalb von geschachtelten Schleifen wird für die Ein- und Ausgaben die Größe 0 (null) angezeigt, und es werden keine Links bereitgestellt.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Erstellen einer Kostenanalyse mithilfe von Azure Cost Management and Billing

Zur Prüfung der Kosten des Diensts Azure Logic Apps auf Basis eines bestimmten Bereichs, z. B. eines Azure-Abonnements, bieten sich die Funktionen zur [Kostenanalyse](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in [Azure Cost Management and Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) an.

1. Öffnen Sie im Azure-Portal den gewünschten Bereich, z. B. Ihr Azure-Abonnement. Wählen Sie im linken Menü unter **Cost Management** die Option **Kostenanalyse** aus.

   Wenn Sie den Kostenanalysebereich erstmals öffnen, zeigt der obere Graph die Ist- und prognostizierten Nutzungskosten für alle Dienste im Abonnement für den aktuellen Monat.

   ![Screenshot des Azure-Portals und des Kostenanalysebereichs mit einem Beispiel für die Ist- und prognostizierten Kosten in einem Abonnement.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Um den Bereich zu ändern, wählen Sie im Bereich **Kostenanalyse** auf der Leiste „Filter“ den Filter **Bereich** aus. Wechseln Sie in **Bereich auswählen** zum gewünschten Bereich.

   Darunter zeigen die Ringdiagramme die laufenden Kosten nach Azure-Diensten, nach Azure-Region (Standort) und nach Ressourcengruppe.

   ![Screenshot des Azure-Portals und Kostenanalysebereichs mit Beispielringdiagrammen für Dienste, Regionen und Ressourcengruppen.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Um das Diagramm auf einen bestimmten Bereich, z. B. einen Dienst oder eine Ressource, zu filtern, wählen Sie in der Leiste „Filter“ **Filter hinzufügen** aus.

1. Wählen Sie in der linken Liste den Filtertyp aus, z. B. **Dienstname**. Wählen Sie in der rechten Liste den Filter aus, z. B. **Logik-Apps**. Wenn Sie fertig sind, wählen Sie das grüne Häkchen aus.

   ![Screenshot des Azure-Portals und Kostenanalysebereichs mit Filterauswahl.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Hier ist beispielsweise das Ergebnis für den Dienst Azure Logic Apps:

   ![Screenshot des Azure-Portals und Kostenanalysebereichs mit nach „Logik-Apps“ gefilterten Ergebnissen.](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Exportieren von Kostendaten

Wenn Sie weitere Datenanalysen zu Kosten durchführen müssen, können Sie [Kostendaten in ein Speicherkonto exportieren](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Beispielsweise kann ein Finanzteam diese Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Weitere Möglichkeiten zum Verwalten und Senken von Kosten

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Probieren Sie diese Optionen aus, um die Kosten für Ihre Logik-Apps und zugehörige Ressourcen zu senken:

* Verwenden Sie nach Möglichkeit [integrierte Trigger und Aktionen](../connectors/apis-list.md#built-in), die weniger Kosten pro Ausführung verursachen als [Trigger und Aktionen des verwalteten Connectors](../connectors/apis-list.md#managed-connectors).

  Beispielsweise lassen sich Kosten beim Zugriff auf andere Ressourcen senken, indem Sie die [HTTP-Aktion](../connectors/connectors-native-http.md) verwenden oder eine Funktion aufrufen, die Sie mithilfe des [Diensts Azure Functions](../azure-functions/functions-overview.md) und der integrierten [Azure Functions-Aktion](../logic-apps/logic-apps-azure-functions.md) erstellt haben. Die Nutzung von Azure Functions ist jedoch auch mit Kosten verbunden, weshalb Sie Ihre Optionen unbedingt vergleichen sollten.

* [Legen Sie genaue Triggerbedingungen](logic-apps-workflow-actions-triggers.md#trigger-conditions) für die Ausführung eines Workflows fest.

  Sie können beispielsweise angeben, dass ein Trigger nur dann ausgelöst wird, wenn eine Website einen internen Serverfehler zurückgibt. Geben Sie in der JSON-Definition des Triggers mit der Eigenschaft `conditions` eine Bedingung an, die auf den Statuscode des Triggers verweist.

* Wenn ein Trigger eine Abfrage- und Webhookversion hat, probieren Sie die Webhookversion aus, die auf das Eintreten des angegebenen Ereignisses wartet, ehe sie ausgelöst wird, anstatt regelmäßig auf das Ereignis zu prüfen.

* Rufen Sie Ihre Logik-App über einen anderen Dienst auf, damit der Trigger nur dann ausgelöst wird, wenn der Workflow laufen soll.

  Sie können beispielsweise Ihre Logik-App über eine Funktion aufrufen, die Sie mit dem Dienst Azure Functions erstellen und ausführen. Ein Beispiel finden Sie unter [Aufrufen oder Auslösen von Logik-Apps mithilfe von Azure Functions und Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Deaktivieren Sie Logik-Apps](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps), die nicht ständig laufen müssen, oder [löschen Sie Logik-Apps](manage-logic-apps-with-azure-portal.md#delete-logic-apps), die Sie überhaupt nicht mehr benötigen. Deaktivieren Sie nach Möglichkeit alle anderen Ressourcen, die nicht ständig aktiv sein müssen.

## <a name="next-steps"></a>Nächste Schritte

* [Optimieren der Cloudinvestitionen mit Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Verwalten von Kosten mithilfe der Kostenanalyse](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Vermeiden unerwarteter Kosten](https://docs.microsoft.com/azure/cost-management-billing/understand/analyze-unexpected-charges?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Absolvieren des Lernpfads zu [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)


