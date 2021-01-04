---
title: Planen und Verwalten von Kosten für Azure Data Factory
description: In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Data Factory planen und verwalten können.
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: e2f61085d3dab6d4489aa190204be4169ea5c2fb
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638005"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Planen und Verwalten von Kosten für Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory ist ein serverloser und elastischer Datenintegrationsdienst, der für die Cloud skaliert wurde.  Dies bedeutet, dass es keine feste Computegröße gibt, die Sie bei der Planung als Spitzenlast berücksichtigen müssen. Stattdessen geben Sie an, wie viele Ressourcen bei Bedarf pro Vorgang zuzuordnen sind, sodass Sie die ETL-Prozesse auf eine besser skalierbare Weise gestalten können. Außerdem wird ADF auf Basis eines verbrauchsbasierten Plans abgerechnet, sodass Sie nur für die tatsächliche Nutzung bezahlen.

In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Data Factory planen und verwalten können.

*   Zunächst erstellen Sie zu Beginn des ETL-Projekts einen Proof of Concept und verwenden eine Kombination aus Verbrauch pro Pipeline und Preisrechner, um die Kosten zu schätzen.
*   Nachdem Sie Ihre Pipelines in der Produktionsumgebung bereitgestellt haben, verwenden Sie die Cost Management-Funktionen, um Budgets festzulegen und die Kosten zu überwachen. Sie können auch die prognostizierten Kosten überprüfen und Ausgabentrends ermitteln.
*   Außerdem können Sie die Nutzung pro Pipeline und pro Aktivität abrufen, um zu ermitteln, welche Pipelines und welche Aktivitäten am meisten Kosten verursachen, und Kandidaten für eine Kostensenkung identifizieren.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Schätzen von Kosten anhand der Nutzung bei Pipeline- und Aktivitätsausführung und des Preisrechners

Sie können den [ADF-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=data-factory) verwenden, um eine Schätzung der Kosten für die Ausführung Ihrer ETL-Arbeitsauslastung in Azure Data Factory zu erhalten.  Um den Rechner zu verwenden, müssen Sie Details wie die Anzahl der Aktivitätsausführungen, die Anzahl der Stunden für Datenintegrationseinheiten, den für den Datenfluss verwendeten Computetyp, die Anzahl der Kerne, die Anzahl der Instanzen, die Ausführungsdauer usw. eingeben.

Eine der häufig gestellten Fragen zum Preisrechner ist, welche Werte als Eingaben verwendet werden sollen.  Während der Proof-of-Concept-Phase können Sie Testläufe mithilfe von Beispieldatasets durchführen, um den Verbrauch verschiedener ADF-Einheiten zu ermitteln.  Anschließend können Sie basierend auf der Nutzung des Beispieldatasets eine Projektion der Nutzung für das vollständige Dataset und den Operationalisierungszeitplan erstellen.

> [!NOTE]
> Die in diesen Beispielen verwendeten Preise sind hypothetisch und stellen keine tatsächliche Preisgestaltung dar.

Angenommen, Sie müssen täglich 1 TB Daten von AWS S3 zu Azure Data Lake Gen2 verschieben.  Sie können ein Proof of Concept für das Verschieben von 100 GB Daten durchführen, um den Durchsatz der Datenerfassung zu messen und den entsprechenden abgerechneten Verbrauch zu ermitteln.

Im Folgenden finden Sie ein Detailbeispiel für die Ausführung der Kopieraktivität (Ihre tatsächliche Zeit hängt von der Form Ihres spezifischen Datasets, von Netzwerkgeschwindigkeiten, von Ausgangsgrenzwerten beim S3-Konto, von Eingangsgrenzwerten bei ADLS Gen2 und anderen Faktoren ab).

![Ausführung der S3-Kopieraktivität](media/plan-manage-costs/s3-copy-run-details.png)

Durch Verwendung der [Überwachung des Verbrauchs auf Pipelineausführungsebene](#monitor-consumption-at-pipeline-run-level) können Sie den entsprechenden Verbrauch für die Datenverschiebungseinheiten abrufen:

![Verbrauch für die S3-Kopierpipeline](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Folglich beträgt die Gesamtzahl von DIU-Stunden (Datenintegrationseinheiten) für den gesamten Monat, die für die Verschiebung von 1 TB pro Tag benötigt wird:

1,2667 (DIU-Stunden) * (1 TB / 100 GB) * 30 (Tage in einem Monat) = 380 DIU-Stunden

Nun können Sie 30 Aktivitätsausführungen und 380 DIU-Stunden im ADF-Preisrechner eingeben, um eine Schätzung ihrer monatlichen Rechnung zu erhalten:

![Preisrechner für den S3-Kopiervorgang](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Verwenden von Budgets und Kostenwarnungen

Sie können [Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md) erstellen, um Kosten zu verwalten, und Warnungen erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren.  Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten.  Wenn Sie ein Budget erstellen, können Sie es entweder auf Abonnementebene oder mit einer geringeren Granularität ausführen, indem Sie zusätzliche Filter hinzufügen, z. B. die Ressourcen-ID und den Namen der Verbrauchseinheit.  Sie können jedoch kein Budget für einzelne Pipelines innerhalb einer Factory erstellen.

## <a name="monitor-costs-at-factory-level"></a>Überwachen der Kosten auf Factory-Ebene

Sobald Sie beginnen, Azure Data Factory zu verwenden, können Sie die anfallenden Kosten im Azure-Portal im Bereich [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md) ablesen.

1. Um die [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md) anzuzeigen, öffnen Sie das Fenster **Kostenverwaltung + Abrechnung** , wählen im Menü die Option **Kostenverwaltung** aus und wählen dann **Kostenanalyse öffnen** aus.
2. In der Standardansicht werden die kumulierten Kosten für den aktuellen Monat angezeigt.  Sie können zu einem anderen Zeitbereich und einer anderen Granularität wechseln, z. B. täglich oder monatlich.
3. Wählen Sie zum Eingrenzen der Kosten auf einen Dienst wie Azure Data Factory die Option **Filter hinzufügen** und dann **Dienstname** aus.  Wählen Sie dann in der Liste **Azure Data Factory v2** aus.
4. Sie können zusätzliche Filter hinzufügen, um die Kosten für eine bestimmte Factory-Instanz und die Granularität bestimmter ADF-Verbrauchseinheiten zu analysieren.

   ![Kostenanalyse](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Überwachen der Nutzung auf Pipelineausführungsebene

Je nach Typ der Aktivitäten in der Pipeline, Menge der zu verschiebenden und transformierenden Daten und Komplexität der Transformation werden bei der Ausführung einer Pipeline verschiedene Verbrauchseinheiten für die Abrechnung in Azure Data Factory gezählt.

Sie können den Umfang der Nutzung für verschiedene Verbrauchseinheiten für einzelne Pipelineausführungen auf der Azure Data Factory-Benutzeroberfläche anzeigen. Um die Benutzeroberfläche für die Überwachung zu öffnen, wählen Sie die Kachel **Überwachen und verwalten** im Blatt „Data Factory“ des [Azure-Portals](https://portal.azure.com/) aus. Wenn Sie sich bereits auf der Benutzeroberfläche von ADF befinden, klicken Sie auf der linken Seitenleiste auf das Symbol **Überwachen** . Die standardmäßige Überwachungsansicht ist eine Liste der Pipelineausführungen.

Wenn Sie auf die Schaltfläche **Verbrauch** neben dem Namen der Pipeline klicken, wird ein Popupfenster angezeigt, in dem die aggregierte Nutzung der Pipelineausführung für alle Aktivitäten in der Pipeline angezeigt wird.

![Nutzung durch die Pipelineausführung](media/plan-manage-costs/pipeline-run-consumption.png)

![Details zur Pipelinenutzung](media/plan-manage-costs/pipeline-consumption-details.png)

In der Ansicht für die Nutzung durch die Pipelineausführung wird der für jede ADF-Verbrauchseinheit genutzte Betrag für die jeweilige Pipeline ausgeführt, der tatsächliche Preis wird jedoch nicht angezeigt, da der Betrag, der Ihnen in Rechnung gestellt wird, vom Typ Ihres Azure-Kontos und vom verwendeten Währungstyp abhängig ist.  Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>Überwachen der Nutzung auf Aktivitätsausführungsebene
Nachdem Sie den aggregierten Verbrauch auf Pipelineausführungsebene ermittelt haben, müssen Sie in bestimmten Szenarien einen weiteren Drilldown ausführen und ermitteln, welche Aktivität in der Pipeline die teuerste ist.

Um die Nutzung auf Aktivitätsausführungsebene abzurufen, wechseln Sie zur Benutzeroberfläche **Erstellen und überwachen** Ihrer Data Factory. Auf der Registerkarte **Überwachen** wird eine Liste der Pipelineausführungen angezeigt. Klicken Sie auf den Link **Pipelinename** , um auf die Liste von Aktivitätsausführungen in der Pipelineausführung zuzugreifen.  Klicken Sie auf die Schaltfläche **Ausgabe** neben dem Aktivitätsnamen, und suchen Sie in der JSON-Ausgabe nach der Eigenschaft **billableDuration** :

Im Folgenden sehen Sie eine Beispielausgabe für eine Kopieraktivitätsausführung:

![Ausgabe für Kopiervorgang](media/plan-manage-costs/copy-output.png)

Hier sehen Sie eine Beispielausgabe für die Ausführung einer Zuordnungsdatenflussaktivität:

![Datenflussausgabe](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Informationen zu den Preisen für Azure Data Factory:

- [Azure Data Factory – Preise](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Grundlegendes zu Azure Data Factory-Preisen anhand von Beispielen](./pricing-concepts.md)
- [Azure Data Factory-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=data-factory)