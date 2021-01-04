---
title: Planen und Verwalten von Kosten für Azure Cosmos DB
description: Es wird beschrieben, wie Sie Kosten für Azure Cosmos DB planen und verwalten, indem Sie die Kostenanalyse im Azure-Portal verwenden.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 3632c098f865b1e5c4e76709a83176035be7abc2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351826"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planen und Verwalten von Kosten für Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Cosmos DB planen und verwalten können. Bevor Sie Ressourcen erstellen, verwenden Sie zunächst den Azure Cosmos DB Capacity Calculator, um Ihre Workloadkosten zu schätzen. Später können Sie die geschätzten Kosten überprüfen und beginnen, ihre Ressourcen zu erstellen.

Nachdem Sie mit der Nutzung der Azure Cosmos DB-Ressourcen begonnen haben, können Sie die Features für das Cost Management verwenden, um Budgets festzulegen und die Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. Die Kosten für Azure Cosmos DB stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für Azure Cosmos DB erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="provisioned-throughput-or-serverless"></a>Bereitgestellter Durchsatz oder serverlos

Azure Cosmos DB unterstützt zwei Typen von Kapazitätsmodi: [bereitgestellter Durchsatz](set-throughput.md) und [serverlos](serverless.md). Die Art und Weise, wie die Abrechnung für die Nutzung von Azure Cosmos DB erfolgt, variiert stark für diese beiden Modi, daher ist es wichtig, den Modus zu wählen, der am besten für Ihre Workload geeignet ist. Eine Anleitung und Empfehlungen dazu, wie diese Wahl zwischen bereitgestelltem Durchsatz und serverlos getroffen werden kann, finden Sie im Artikel [Wahl zwischen bereitgestelltem Durchsatz und serverlos](throughput-serverless.md).

### <a name="cost-analysis"></a>Kostenanalyse

Die Kostenanalyse in Cost Management unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>Einschätzen der Kosten für bereitgestellten Durchsatz vor der Verwendung von Azure Cosmos DB

Wenn Sie planen, Azure Cosmos DB im Modus „bereitgestellter Durchsatz“ zu verwenden, nutzen Sie den [Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/), um die Kosten zu schätzen, bevor Sie die Ressourcen in einem Azure Cosmos-Konto erstellen. Der Capacity Calculator wird genutzt, um eine Schätzung des erforderlichen Durchsatzes und der Kosten Ihrer Workload zu erhalten. Die Konfiguration Ihrer Azure Cosmos-Datenbanken und -Container mit der richtigen Menge an bereitgestelltem Durchsatz bzw. [Anforderungseinheiten (RU/s)](request-units.md) für Ihre Workload ist von entscheidender Bedeutung, um die Kosten und die Leistung zu optimieren. Für eine Kostenschätzung müssen Sie Details wie API-Typ, Anzahl von Regionen, Elementgröße, Lese-/Schreibanforderungen pro Sekunde und insgesamt gespeicherte Daten eingeben. Weitere Informationen zum Capacity Calculator finden Sie im Artikel zum Thema [Schätzung](estimate-ru-with-capacity-planner.md).

Im folgenden Screenshot sind der Durchsatz und die Kostenschätzung bei Verwendung des Capacity Calculators dargestellt:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Kostenschätzung im Azure Cosmos DB Capacity Calculator":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Schätzen der serverlosen Kosten vor der Verwendung von Azure Cosmos DB

Wenn Sie planen, Azure Cosmos DB im serverlosen Modus zu verwenden, müssen Sie schätzen, wie viele [Anforderungseinheiten](request-units.md) und GB an Speicher Sie möglicherweise monatlich verbrauchen werden. Sie können die erforderliche Menge an Anforderungseinheiten schätzen, indem Sie die Anzahl der Datenbankvorgänge auswerten, die in einem Monat ausgegeben würden, und diesen Wert mit den entsprechenden RU-Kosten multiplizieren. In der folgenden Tabelle sind die geschätzten RU-Kosten für allgemeine Datenbankvorgänge aufgeführt:

| Vorgang | Geschätzte Kosten | Notizen |
| --- | --- | --- |
| Erstellen eines Elements | 5 RUs | Durchschnittliche Kosten für ein 1 KB-Element mit weniger als 5 zu indizierenden Eigenschaften |
| Aktualisieren eines Elements | 10 RUs | Durchschnittliche Kosten für ein 1 KB-Element mit weniger als 5 zu indizierenden Eigenschaften |
| Lesen eines einzelnen Elements anhand seiner ID und seines Partitionsschlüssels (Punktlesevorgang) | 1 RU | Durchschnittliche Kosten für ein 1 KB-Element |
| Löschen eines Elements | 5 RUs | |
| Ausführen einer Abfrage | 10 RUs | Durchschnittliche Kosten für eine Abfrage, die alle Vorteile der [Indizierung](index-overview.md) nutzt und 100 Ergebnisse oder weniger liefert |

> [!IMPORTANT] 
> Beachten Sie die Anmerkungen aus der Tabelle oben. Für eine genauere Schätzung der tatsächlichen Kosten Ihrer Vorgänge können Sie den [Azure Cosmos DB-Emulator](local-emulator.md) verwenden und [die genauen RU-Kosten Ihrer Vorgänge messen](find-request-unit-charge.md). Obwohl der Azure Cosmos DB-Emulator serverlos nicht unterstützt, gibt er eine RU-Standardgebühr für Datenbankvorgänge an und kann für diese Schätzung verwendet werden.

Sobald Sie die Gesamtzahl der Anforderungseinheiten und GB an Speicherplatz berechnet haben, die Sie voraussichtlich in einem Monat verbrauchen werden, erhalten Sie mit der folgenden Formel Ihren Kostenvoranschlag: **([Anzahl der Anforderungseinheiten] / 1.000.000 * 0,25 USD) + ([GB an Speicherplatz] * 0,25 USD)** .

> [!NOTE]
> Die im vorherigen Beispiel gezeigten Kosten dienen nur zu Demonstrationszwecken. Aktuelle Preisinformationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="review-estimated-costs-in-the-azure-portal"></a>Überprüfen von geschätzten Kosten im Azure-Portal

Wenn Sie damit beginnen, Azure Cosmos DB-Ressourcen über das Azure-Portal zu nutzen, können Sie die geschätzten Kosten anzeigen. Führen Sie die folgenden Schritte aus, um die Kostenschätzung zu überprüfen:

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrem Azure Cosmos-Konto.
1. Navigieren Sie zum Abschnitt **Übersicht**.
1. Überprüfen Sie das Diagramm **Kosten** unten in der Übersicht. Dieses Diagramm zeigt eine Schätzung Ihrer aktuellen Kosten über einen konfigurierbaren Zeitraum an:
1. Erstellen Sie einen neuen Container, z. B. einen Graph-Container.
1. Geben Sie den Durchsatz ein, der für Ihre Workload benötigt wird, z. B. 400 RU/s. Nach dem Eingeben des Durchsatzwerts wird die Kostenschätzung wie im folgenden Screenshot angezeigt:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Kostenschätzung im Azure-Portal":::

Wenn Ihr Azure-Abonnement über ein Ausgabenlimit verfügt, wird von Azure verhindert, dass Ihre Ausgaben den Guthabenbetrag übersteigen. Beim Erstellen und Nutzen von Azure-Ressourcen wird Ihr Guthaben verwendet. Wenn Sie Ihr Guthabenlimit erreicht haben, werden die von Ihnen bereitgestellten Ressourcen für den Rest des Abrechnungszeitraums deaktiviert. Sie können Ihr Guthabenlimit nicht ändern, aber Sie können es entfernen. Weitere Informationen zu Ausgabenlimits finden Sie unter [Azure-Ausgabenlimit](../cost-management-billing/manage/spending-limit.md).

Sie können Gebühren für Azure Cosmos DB mit Ihrem Azure Enterprise Agreement-Mindestverbrauchsguthaben bezahlen. Allerdings können Sie mit dem Mindestverbrauchsguthaben keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus Azure Marketplace, bezahlen.

## <a name="monitor-costs"></a>Überwachen der Kosten

Wenn Sie Ressourcen mit Azure Cosmos DB verwenden, fallen Kosten an. Die Kosten pro Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder der Nutzung von Anforderungseinheiten. Nach Beginn der Verwendung von Azure Cosmos DB fallen Kosten an. Sie werden im Azure-Portal im Bereich [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) angezeigt.

Bei Verwendung der Kostenanalyse können Sie die Azure Cosmos DB-Kosten in Graphen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktuell“, „Vorheriger Monat“ und „Jahr“. Außerdem können Sie Kosten nach Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln und erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten wurden. 

So zeigen Sie Azure Cosmos DB-Kosten in der Kostenanalyse an:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie den Bereich im Azure-Portal, und wählen Sie **Kostenanalyse** im Menü aus. Navigieren Sie beispielsweise zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie **Bereich** aus, um zu einem anderen Bereich der Kostenanalyse zu wechseln.

1. Standardmäßig werden die Kosten für alle Dienste im ersten Ringdiagramm angezeigt. Wählen Sie im Diagramm den Bereich „Azure Cosmos DB“ aus.

1. Wählen Sie zum Eingrenzen der Kosten auf einen Dienst, z. B. Azure Cosmos DB, die Option **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie anschließend in der Liste die Option **Azure Cosmos DB** aus. In diesem Beispiel werden nur die Kosten für Azure Cosmos DB angezeigt:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Überwachen der Kosten im Bereich „Kostenanalyse“":::

Im obigen Beispiel sind die aktuellen Kosten für Azure Cosmos DB für den Monat Februar angegeben. Darüber hinaus enthalten die Diagramme auch die Azure Cosmos DB-Kosten nach Standort und Ressourcengruppe.

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den Filteroptionen beim Erstellen eines Budgets finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise können Finanzteams die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Informationen zu den Preisen in Azure Cosmos DB:

* [Preismodell in Azure Cosmos DB](how-pricing-works.md)
* [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimieren der Abfragekosten in Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optimieren der Speicherkosten in Azure Cosmos DB](optimize-cost-storage.md)
* Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.