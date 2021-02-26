---
title: Planen und Verwalten von Kosten für Azure API Management
description: Hier erfahren Sie, wie Sie Kosten für Azure API Management mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581328"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Planen und Verwalten von Kosten für API Management

In diesem Artikel erfahren Sie, wie Sie die Kosten für Azure API Management planen und verwalten. Bevor Sie Ressourcen für den Dienst zur Kostenschätzung hinzufügen, verwenden Sie zunächst den Azure-Preisrechner, um die Kosten für API Management zu planen. Nachdem Sie mit der Nutzung von API Management-Ressourcen begonnen haben, können Sie Cost Management-Features verwenden, um Budgets festzulegen und Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. 

Kosten für API Management stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für API Management erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse in Cost Management unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Schätzen der Kosten vor der Verwendung von API Management

Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die Kosten vor dem Hinzufügen von API Management zu schätzen. 

1. Suchen Sie nach *API Management*, oder wählen Sie **Integration** > **API Management** aus.
1. Wählen Sie **Ansicht** aus, um eine Standardkostenschätzung für eine API Management-Dienstinstanz hinzuzufügen.

> [!NOTE]
> Die im Beispiel gezeigten Kosten dienen nur zu Demonstrationszwecken. Informationen zur aktuellen Preisgestaltung finden Sie unter [Preisdetails zu API Management](https://azure.microsoft.com/pricing/details/api-management/).

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Schätzen der Kosten für die Developer-Dienstebene":::

* Die Standardkostenschätzung basiert auf einer API Management-Dienstinstanz in der **Developer**-[Dienstebene](api-management-features.md) mit 1 [Kapazitätseinheit](api-management-capacity.md). Die Dienstebene „Developer“ ist für Anwendungsfälle und Auswertungen außerhalb von Produktionsumgebungen vorgesehen. Sie unterliegt keiner Vereinbarung zum Servicelevel.

* Um Kosten für zusätzliche Kapazitätseinheiten oder eine andere Dienstebene zu schätzen, wählen Sie in den Dropdownlisten **Einheiten** und **Ebene** andere Optionen aus.

* Abhängig von der Verfügbarkeit von Features und der Dienstebene können zusätzliche Gebühren für die Verwendung von [selbstgehosteten Gateways](self-hosted-gateway-overview.md) anfallen.

Weitere Preis- und Featureinformationen finden Sie unter:

* [API Management-Preise](https://azure.microsoft.com/pricing/details/api-management/)
* [Featurebasierter Vergleich der Azure API Management-Tarife](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Verwenden von Guthaben mit API Management

Sie können API Management-Gebühren mit Ihrer Azure-Vorauszahlung (früher als Mindestverbrauch bezeichnet) bezahlen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

## <a name="monitor-costs"></a>Überwachen der Kosten

Wenn Sie Azure-Ressourcen mit API Management verwenden, fallen Kosten an. Die Kosten pro Azure-Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder der Einheitennutzung (Bytes, Megabytes usw.). Sobald mit der API Management-Nutzung begonnen wird, fallen Kosten an, und Sie können die Kosten in der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) sehen.

Bei Verwendung der Kostenanalyse können Sie API Management-Verbindungskosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.

> [!NOTE]
> Die im Beispiel gezeigten Kosten dienen nur zu Demonstrationszwecken. Ihre Kosten variieren in Abhängigkeit von der Ressourcennutzung und den aktuellen Preisen.

So zeigen Sie API Management-Kosten in der Kostenanalyse an

1. Melden Sie sich beim [Azure-Portal](https://azure.microsoft.com) an.
1. Öffnen Sie das Fenster **Kostenverwaltung + Abrechnung**, und wählen Sie im Menü die Option **Kostenverwaltung** und dann einen **Abrechnungsbereich** aus. Wählen Sie in der Liste beispielsweise ein Abonnement aus.
1. Wählen Sie im Menü die Option **Kostenverwaltung** und dann **Kostenanalyse** aus.
1. Standardmäßig werden die monatlichen Kosten für alle Dienste im ersten Ringdiagramm angezeigt. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Monatliche Kosten für Abonnement":::

1. Wählen Sie zum Eingrenzen der Kosten auf einen Dienst, z. B. API Management, die Option **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie dann **API Management** aus.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Beispiel der akkumulierten Kosten für API Management":::

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und API Management-Kosten nach Ressourcengruppe werden ebenfalls angezeigt. Von hier aus können Sie die Kosten selbst überprüfen.

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den Filteroptionen beim Erstellen eines Budgets finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie andere Personen benötigen, um zusätzliche Datenanalysen für Kosten ausführen zu können. Beispielsweise kann ein Finanzteam die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Weitere Optionen zum Verwalten und Reduzieren der Kosten für API Management

### <a name="choose-tier"></a>Auswählen einer Dienstebene

Lesen Sie den [featurebasierten Vergleich der Azure API Management-Dienstebenen](api-management-features.md), um zu entscheiden, welche Dienstebene für Ihre Szenarien geeignet ist. Die verschiedenen Dienstebenen unterstützen Kombinationen von Features und Funktionen, die für verschiedene Anwendungsfälle entwickelt wurden, mit unterschiedlichen Kosten. 

* Die Dienstebene **Verbrauch** (Consumption) bietet eine schlanke, serverlose Option, die keine Fixkosten verursacht. Die Abrechnung erfolgt basierend auf der Anzahl von API-Aufrufen an den Dienst oberhalb eines bestimmten Schwellenwerts. Die Kapazität wird auch automatisch basierend auf der Auslastung des Diensts skaliert.
* Die API Management-Dienstebenen **Developer**, **Basic**, **Standard** und **Premium** verursachen monatliche Kosten und bieten einen höheren Durchsatz sowie umfangreichere Features für Evaluierungs- und Produktionsworkloads. Sie können jederzeit ein [Upgrade](upgrade-and-scale.md) auf eine andere Dienstebene vornehmen.

### <a name="scale-using-capacity-units"></a>Skalieren mithilfe von Kapazitätseinheiten

Außer in der Dienstebene „Verbrauch“ unterstützt API Management die Skalierung durch Hinzufügen oder Entfernen von [*Kapazitätseinheiten*](api-management-capacity.md). Wenn die Last in einer API Management-Instanz zunimmt, kann das Hinzufügen von Kapazitätseinheiten wirtschaftlicher sein als ein Upgrade auf eine höhere Dienstebene. Die Höchstzahl der Einheiten hängt von der Dienstebene ab.

Jede Kapazitätseinheit verfügt über eine bestimmte Kapazität zur Verarbeitung von Anforderungen, die von der Ebene des Diensts abhängig ist. Beispielsweise hat eine Einheit der Basic-Dienstebene einen geschätzten maximalen Durchsatz von ungefähr 1.000 Anforderungen pro Sekunde. 

Wenn Sie Einheiten hinzufügen oder entfernen, werden Kapazität und Kosten proportional skaliert. Beispielsweise bieten zwei Einheiten der Standard-Dienstebene einen geschätzten Durchsatz von ungefähr 2.000 Anforderungen pro Sekunde. Der tatsächliche Durchsatz kann aufgrund der Größe von Anforderungen oder Antworten, Verbindungsmustern, der Anzahl von Clients, die Anforderungen tätigen, und anderer Faktoren abweichen.

[Überwachen](api-management-howto-use-azure-monitor.md) Sie die Kapazitätsmetrik für Ihre API Management-Instanz, um fundierter Entscheidungen im Hinblick auf die Skalierung oder ein Upgrade einer API Management-Instanz zur Bewältigung höherer Lasten treffen zu können.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.
- Weitere Informationen zur API Management-[Kapazität](api-management-capacity.md).
- Weitere Informationen zu Schritten zum Skalieren und für Upgrades von API Management mithilfe des [Azure-Portals](upgrade-and-scale.md), und erfahren Sie mehr über die [automatische Skalierung](api-management-howto-autoscale.md).
