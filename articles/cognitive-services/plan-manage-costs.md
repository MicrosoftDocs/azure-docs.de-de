---
title: Planen der Kostenverwaltung für Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Kosten für Azure Cognitive Services mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 661af89ad223978abbefd71dd8008577475c5875
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221374"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planen und Verwalten von Kosten für Azure Cognitive Services

In diesem Artikel erfahren Sie, wie Sie die Kosten für Azure Cognitive Services planen und verwalten. Verwenden Sie zunächst den Azure-Preisrechner, um die Kosten für Cognitive Services zu planen, bevor Sie Ressourcen für den Dienst hinzufügen, um die voraussichtlichen Kosten zu ermitteln. Überprüfen Sie dann beim Hinzufügen von Azure-Ressourcen die voraussichtlichen Kosten. Nachdem Sie mit der Nutzung von Cognitive Services-Ressourcen wie Speech, maschinelles Sehen, LUIS, Textanalyse, Übersetzer und Ähnlichem begonnen haben, können Sie Cost Management-Features verwenden, um Budgets festzulegen und die Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. Kosten für Cognitive Services stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für Cognitive Services erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse in Cost Management unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Schätzen der Kosten vor der Verwendung von Cognitive Services

Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die Kosten vor dem Hinzufügen von Cognitive Services zu schätzen.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Azure-Preisrechner für Cognitive Services" border="true":::

Wenn Sie Ihrem Arbeitsbereich neue Ressourcen hinzufügen, kehren Sie zu diesem Preisrechner zurück, und fügen Sie hier die gleiche Ressource hinzu, um Ihre Kostenschätzung zu aktualisieren.

Weitere Informationen finden Sie unter [Azure Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Grundlegendes zum Gesamtabrechnungsmodell für Cognitive Services

Cognitive Services wird in einer Azure-Infrastruktur ausgeführt, für die [Kosten anfallen](https://azure.microsoft.com/pricing/details/cognitive-services/), wenn Sie die neue Ressource bereitstellen. Es ist wichtig zu verstehen, dass die zusätzliche Infrastruktur Kosten verursachen kann. Sie müssen diese Kosten verwalten, wenn Sie Änderungen an bereitgestellten Ressourcen vornehmen. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Mit Cognitive Services üblicherweise anfallende Kosten

Nach der Bereitstellung einer Azure-Ressource werden die Kosten in der Regel durch Ihren Tarif und durch die API-Aufrufe bestimmt, die Sie an Ihren Endpunkt richten. Wenn der von Ihnen verwendete Dienst über eine Mindestabnahme verfügt, fällt bei Überschreitung der zugewiesenen Aufrufe in Ihrem Tarif ggf. eine entsprechende Zusatzgebühr an.

Bei Verwendung der folgenden Dienste können zusätzliche Kosten anfallen:

#### <a name="qna-maker"></a>QnA Maker

Wenn Sie Ressourcen für QnA Maker erstellen, werden unter Umständen auch Ressourcen für andere Azure-Dienste erstellt. Dazu gehören:

- [Azure App Service (für die Runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (für die Daten)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

#### <a name="qna-maker"></a>QnA Maker

Nach dem Löschen von QnA Maker-Ressourcen sind die folgenden Ressourcen möglicherweise weiterhin vorhanden. Hierfür fallen weiter Kosten an, bis Sie sie löschen.

- [Azure App Service (für die Runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (für die Daten)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Verwenden der Azure-Vorauszahlungsgutschrift mit Cognitive Services

Sie können Cognitive Services-Gebühren mit der Azure-Vorauszahlungsgutschrift (früher als Mindestverbrauch bezeichnet) bezahlen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

## <a name="monitor-costs"></a>Überwachen der Kosten

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Bei der Verwendung von Azure-Ressourcen mit Cognitive Services fallen Kosten an. Die Kosten pro Azure-Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder der Einheitennutzung (Bytes, Megabytes usw.). Sobald Cognitive Services verwendet wird, fallen Kosten an, die Sie über die [Kostenanalyse](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) anzeigen können.

Bei Verwendung der Kostenanalyse können Sie Cognitive Services-Kosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.

So zeigen Sie Cognitive Services-Kosten in der Kostenanalyse an:

1. Melden Sie sich beim Azure-Portal an.
2. Öffnen Sie den Bereich im Azure-Portal, und wählen Sie **Kostenanalyse** im Menü aus. Navigieren Sie beispielsweise zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie **Bereich** aus, um zu einem anderen Bereich der Kostenanalyse zu wechseln.
3. Standardmäßig werden die Kosten für Dienste im ersten Ringdiagramm angezeigt. Wählen Sie im Diagramm den Bereich „Cognitive Services“ aus.

Die tatsächlichen monatlichen Kosten werden beim ersten Öffnen der Kostenanalyse angezeigt. Im folgenden Beispiel werden alle monatlichen Nutzungskosten angezeigt.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Beispiel der akkumulierten Kosten für ein Abonnement":::

- Klicken Sie zum Eingrenzen der Kosten auf einen Dienst (z. B. Cognitive Services) auf die Option **Filter hinzufügen** und auf dann auf **Dienstname**. Wählen Sie dann **Cognitive Services** aus.

Im folgenden Beispiel werden nur die Kosten für Cognitive Services angezeigt.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Beispiel der akkumulierten Kosten für Cognitive Services":::

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und die Cognitive Services-Kosten nach Ressourcengruppe werden ebenfalls angezeigt. Von hier aus können Sie die Kosten selbst überprüfen.

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den Filteroptionen beim Erstellen eines Budgets finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise können Finanzteams die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.