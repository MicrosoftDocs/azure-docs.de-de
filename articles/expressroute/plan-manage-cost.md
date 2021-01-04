---
title: Planen der Kostenverwaltung für Azure ExpressRoute
description: Hier erfahren Sie, wie Sie Kosten für Azure ExpressRoute mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 38b6aa35c1b12e3fdaed4a60cd6e241a05d42efe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501288"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Planen und Verwalten von Kosten für Azure ExpressRoute

In diesem Artikel wird beschrieben, wie Sie die Kosten für ExpressRoute planen und verwalten. Bevor Sie Ressourcen für den Dienst zur Kostenschätzung hinzufügen, verwenden Sie zunächst den Azure-Preisrechner, um die Kosten für ExpressRoute zu planen. Überprüfen Sie dann beim Hinzufügen von Azure-Ressourcen die geschätzten Kosten. 

Nachdem Sie mit der Nutzung von ExpressRoute-Ressourcen begonnen haben, können Sie Cost Management-Features verwenden, um Budgets festzulegen und Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. 

Beachten Sie, dass Kosten für ExpressRoute nur einen Teil der monatlichen Kosten in Ihrer Azure-Rechnung ausmachen. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für ExpressRoute erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse in Cost Management unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. 

Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Vergleich von Local, Standard und Premium

ExpressRoute verfügt über drei verschiedene Leitungs-SKUs: [*Local*](./expressroute-faqs.md#expressroute-local), *Standard* und [*Premium*](./expressroute-faqs.md#expressroute-premium). Die Art und Weise, wie Ihnen die ExpressRoute-Nutzung in Rechnung gestellt wird, ist bei diesen drei SKU-Typen unterschiedlich. Bei der Local-SKU wird Ihnen automatisch eine Datenflatrate in Rechnung gestellt. Bei der Standard- und Premium-SKU können Sie zwischen einem Volumentarif und einer Datenflatrate wählen. Alle eingehenden Daten sind kostenlos, außer bei Verwendung des Global Reach-Add-Ons. Es ist wichtig zu wissen, welche SKU-Typen und Datentarife am besten für Ihre Workload geeignet sind, um Kosten und Budget zu optimieren.

## <a name="estimate-costs"></a>Schätzen der Kosten

Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die Kosten zu schätzen, bevor Sie eine ExpressRoute-Verbindung erstellen. 

1. Wählen Sie links die Option **Netzwerk** und dann **Azure ExpressRoute** aus, um zu beginnen. 

1. Wählen Sie je nach Peeringstandort die geeignete *Zone* aus. Informationen zum Auswählen der geeigneten *Zone* in der Dropdownliste finden Sie unter [ExpressRoute-Konnektivitätsanbieter](./expressroute-locations-providers.md#partners). 

1. Wählen Sie dann die *SKU*, die *Verbindungsgeschwindigkeit* und den *Datentarif* für die gewünschte Schätzung aus. 

1. Geben Sie im Feld *Zusätzliche ausgehende Datenübertragungen* eine Schätzung in GB für die Menge an ausgehenden Daten ein, die Sie im Laufe eines Monats möglicherweise verwenden. 

1. Zum Schluss können Sie der Schätzung das *Global Reach-Add-On* hinzufügen.

Der folgende Screenshot zeigt die Kostenschätzung unter Verwendung des Preisrechners:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kostenschätzung für ExpressRoute im Azure-Rechner":::

Weitere Informationen finden Sie unter [Azure ExpressRoute – Preise](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>Geschätzte Kosten für ExpressRoute-Gateway

Wenn Sie ein ExpressRoute-Gateway zum Verknüpfen eines virtuellen Netzwerks mit der ExpressRoute-Verbindung verwenden, führen Sie die folgenden Schritte aus, um die Kosten für die Gatewaynutzung zu schätzen.

1. Wählen Sie links die Option **Netzwerk** und dann **VPN Gateway** aus, um zu beginnen. 

1. Wählen Sie die *Region* für das Gateway aus, und ändern Sie dann den *Typ* in **ExpressRoute-Gateways**.

1. Wählen Sie den *Gatewaytyp* in der Dropdownliste aus.

1. Geben Sie die *Gatewaystunden* ein. (720 Stunden = 30 Tage)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Grundlegendes zum Gesamtabrechnungsmodell für ExpressRoute

ExpressRoute wird in einer Azure-Infrastruktur ausgeführt, für die beim Bereitstellen der neuen Ressource zusammen mit ExpressRoute Kosten anfallen. Es ist wichtig zu verstehen, dass die zusätzliche Infrastruktur Kosten verursachen kann. Sie müssen diese Kosten verwalten, wenn Sie Änderungen an bereitgestellten Ressourcen vornehmen. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Bei ExpressRoute üblicherweise anfallende Kosten

Wenn Sie eine ExpressRoute-Verbindung erstellen, können Sie ein ExpressRoute-Gateway zum Verknüpfen des virtuellen Netzwerks mit der Verbindung erstellen. Gateways werden zu einem Stundensatz abgerechnet, zuzüglich der Kosten für eine ExpressRoute-Verbindung. Sehen Sie sich die Informationen unter [ExpressRoute – Preise](https://azure.microsoft.com/en-us/pricing/details/expressroute) an, und wählen Sie ExpressRoute-Gateways aus, um Tarife für verschiedene Gateway-SKUs anzuzeigen.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

Wenn Sie nach dem Löschen der ExpressRoute-Verbindung über ein ExpressRoute-Gateway verfügen, werden Ihnen die Kosten bis zum Löschen des Gateways weiter in Rechnung gestellt.

### <a name="using-monetary-credit"></a>Verwenden von Guthaben

Sie können ExpressRoute-Gebühren mit Ihrem EA-Mindestverbrauchsguthaben bezahlen. Allerdings können Sie mit dem EA-Mindestverbrauchsguthaben keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus Azure Marketplace, bezahlen.

## <a name="monitor-costs"></a>Überwachen der Kosten

Wenn Sie Azure-Ressourcen mit ExpressRoute verwenden, fallen Kosten an. Die Kosten pro Azure-Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder der Einheitennutzung (Bytes, Megabytes usw.) Sobald mit der ExpressRoute-Nutzung begonnen wird, fallen Kosten an, und Sie können die Kosten in der [Kostenanalyse](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) sehen.

Bei Verwendung der Kostenanalyse können Sie ExpressRoute-Verbindungskosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.

So zeigen Sie ExpressRoute-Kosten in der Kostenanalyse an:

1. Melden Sie sich beim Azure-Portal an.

1. Navigieren Sie zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie **Bereich** aus, um zu einem anderen Bereich der Kostenanalyse zu wechseln. Standardmäßig werden die Kosten für Dienste im ersten Ringdiagramm angezeigt.

    Die tatsächlichen monatlichen Kosten werden beim ersten Öffnen der Kostenanalyse angezeigt. Im folgenden Beispiel werden alle monatlichen Nutzungskosten angezeigt.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Beispiel der akkumulierten Kosten für ein Abonnement":::
    

1.  Wählen Sie zum Eingrenzen der Kosten auf einen Dienst, z. B. ExpressRoute, die Option **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie dann **ExpressRoute** aus.

    Im folgenden Beispiel werden nur die Kosten für ExpressRoute angezeigt.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Beispiel der akkumulierten Kosten für ExpressRoute":::

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und ExpressRoute-Kosten nach Ressourcengruppe werden ebenfalls angezeigt. Von hier aus können Sie die Kosten selbst überprüfen.

## <a name="create-budgets-and-alerts"></a>Erstellen von Budgets und Warnungen

Sie können [Budgets](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den Filteroptionen beim Erstellen eines Budgets finden Sie unter [Gruppen- und Filteroptionen](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise kann ein Finanzteam die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Preisgestaltung für Azure ExpressRoute. Informationen finden Sie in der [Übersicht über die Preise für Azure ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.
