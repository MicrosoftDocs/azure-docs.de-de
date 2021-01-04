---
title: Schätzen der Kosten mit dem Azure Cosmos DB Capacity Planner
description: Mit dem Azure Cosmos DB Capacity Planner können Sie den erforderlichen Durchsatz (RU/s) und die Kosten für Ihre Workload schätzen. In diesem Artikel wird beschrieben, wie Sie die neue Capacity Planner-Version verwenden, um den erforderlichen Durchsatz und die Kosten zu schätzen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 017aeaaa24e8ac2d493d0de81a7abb655bae5611
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342078"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Schätzen des Durchsatzes (RU/s) mit dem Azure Cosmos DB Capacity Planner
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die Konfiguration Ihrer Azure Cosmos-Datenbanken und -Container mit der richtigen Menge an bereitgestelltem Durchsatz bzw. [Anforderungseinheiten (RU/s)](request-units.md) für Ihre Workload ist sehr wichtig, um die Kosten und die Leistung zu optimieren. In diesem Artikel wird beschrieben, wie Sie den Azure Cosmos DB [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) verwenden, um eine Schätzung des erforderlichen Durchsatzes (RU/s) und der Kosten Ihrer Workload zu erhalten. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Schätzen des Durchsatzes und der Kosten mit Azure Cosmos DB Capacity Planner

Der Capacity Planner kann in zwei Modi verwendet werden.

|**Mode**  |**Beschreibung**  |
|---------|---------|
|Basic|Es wird eine schnelle allgemeine Schätzung des Durchsatzes (RU/s) und der Kosten bereitgestellt. In diesem Modus werden die Azure Cosmos DB-Standardeinstellungen für die Indizierungsrichtlinie, Konsistenz und andere Parameter verwendet. <br/><br/>Verwenden Sie den Modus „Basic“, um eine schnelle, allgemeine Schätzung zu erhalten, wenn Sie eine potenzielle Workload für die Ausführung in Azure Cosmos DB auswerten.|
|Erweitert|Es wird eine ausführlichere Schätzung des Durchsatzes (RU/s) und der Kosten bereitgestellt. Sie können weitere Einstellungen anpassen, z. B. Indizierungsrichtlinie, Konsistenzebene und andere Parameter, die sich auf die Kosten und den Durchsatz auswirken. <br/><br/>Nutzen Sie den Modus „Advanced“, wenn Sie den Durchsatz (RU/s) für ein neues Projekt schätzen möchten oder eine ausführlichere Schätzung benötigen. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Schätzen des bereitgestellten Durchsatzes und der Kosten im Modus „Basic“
Navigieren Sie zum [Capacity Planner](https://cosmos.azure.com/capacitycalculator/), um eine schnelle Schätzung für Ihre Workload im Modus „Basic“ zu erhalten. Geben Sie die folgenden Parameter basierend auf Ihrer Workload ein: 

|**Input** (Eingabe)  |**Beschreibung**  |
|---------|---------|
|Number of regions (Anzahl von Regionen)|Azure Cosmos DB ist in allen Azure-Regionen verfügbar. Wählen Sie die Anzahl von Regionen aus, die für Ihre Workload erforderlich ist. Sie können Ihrem Cosmos-Konto eine beliebige Anzahl von Regionen zuordnen. Ausführlichere Informationen finden Sie in Azure Cosmos DB unter [Globale Verteilung](distribute-data-globally.md).|
|Schreibvorgänge in mehreren Regionen|Wenn Sie [Multi-region writes](distribute-data-globally.md#key-benefits-of-global-distribution) (Schreibvorgänge in mehreren Regionen) aktivieren, kann Ihre Anwendung Lese- und Schreibvorgänge für alle Azure-Regionen durchführen. Bei einer Deaktivierung der Schreibvorgänge in mehreren Regionen kann Ihre Anwendung Daten in einer Region schreiben. <br/><br/> Aktivieren Sie Schreibvorgänge in mehreren Regionen, wenn Sie mit einer Aktiv-Aktiv-Workload rechnen, für die Schreibvorgänge mit geringer Latenz in mehreren Regionen erforderlich sind. Ein Beispiel hierfür ist eine IoT-Workload, bei der große Datenmengen in unterschiedlichen Regionen in die Datenbank geschrieben werden. <br/><br/> Für Schreibvorgänge in mehreren Regionen ist eine Lese- und Schreibverfügbarkeit von 99,999 % garantiert. Für Schreibvorgänge in mehreren Regionen ist im Vergleich mit dem Schreiben in einzelnen Regionen ein höherer Durchsatz erforderlich. Weitere Informationen finden Sie in dem Artikel, in dem beschrieben wird, [wie sich Anforderungseinheiten (RUs) für Schreibvorgänge in einzelne und mehrere Regionen unterscheiden](optimize-cost-regions.md).|
|Gesamte gespeicherte Daten (pro Region)|Schätzung der gesamten Daten in GB, die in einer Region gespeichert sind.|
|Elementgröße|Die geschätzte Größe des Datenelements (z. B. Dokument), die von 1 KB bis 2 MB reicht. |
|Lesevorgänge pro Sekunde pro Region|Anzahl von erwarteten Lesevorgängen pro Sekunde. |
|Schreibvorgänge pro Sekunde pro Region|Anzahl von erwarteten Schreibvorgängen pro Sekunde. |

Wählen Sie **Berechnen** , nachdem Sie die erforderlichen Angaben eingefügt haben. Auf der Registerkarte **Kostenschätzung** werden die Gesamtkosten für den Speicher und den bereitgestellten Durchsatz angezeigt. Sie können die Anzeige über den Link **Details anzeigen** auf dieser Registerkarte erweitern, um eine Aufstellung des Durchsatzes zu erhalten, der für Lese- und Schreibanforderungen jeweils benötigt wird. Wählen Sie bei jeder Änderung des Werts eines Felds die Option **Berechnen** , um die geschätzten Kosten neu zu berechnen. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Capacity Planner: Modus „Basic“":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Schätzen des bereitgestellten Durchsatzes und der Kosten im Modus „Advanced“

Im Modus „Advanced“ können Sie weitere Einstellungen nutzen, die sich auf die Schätzung des Durchsatzes (RU/s) auswirken. Navigieren Sie zum Verwenden dieser Option zum [Capacity Planner](https://cosmos.azure.com/capacitycalculator/), und melden Sie sich am Tool mit einem Konto an, das Sie für Azure verwenden. Die Anmeldeoption befindet sich oben rechts. 

Nach dem Anmelden werden zusätzliche Felder angezeigt, die im Modus „Basic“ nicht vorhanden sind. Geben Sie die zusätzlichen Parameter basierend auf Ihrer Workload an. 

|**Input** (Eingabe)  |**Beschreibung**  |
|---------|---------|
|API|Azure Cosmos DB ist ein Dienst mit mehreren Modellen und APIs. Wählen Sie für neue Workloads die „SQL-API (Core-API)“ aus. |
|Number of regions (Anzahl von Regionen)|Azure Cosmos DB ist in allen Azure-Regionen verfügbar. Wählen Sie die Anzahl von Regionen aus, die für Ihre Workload erforderlich ist. Sie können Ihrem Cosmos-Konto eine beliebige Anzahl von Regionen zuordnen. Ausführlichere Informationen finden Sie in Azure Cosmos DB unter [Globale Verteilung](distribute-data-globally.md).|
|Schreibvorgänge in mehreren Regionen|Wenn Sie [Multi-region writes](distribute-data-globally.md#key-benefits-of-global-distribution) (Schreibvorgänge in mehreren Regionen) aktivieren, kann Ihre Anwendung Lese- und Schreibvorgänge für alle Azure-Regionen durchführen. Bei einer Deaktivierung der Schreibvorgänge in mehreren Regionen kann Ihre Anwendung Daten in einer Region schreiben. <br/><br/> Aktivieren Sie Schreibvorgänge in mehreren Regionen, wenn Sie mit einer Aktiv-Aktiv-Workload rechnen, für die Schreibvorgänge mit geringer Latenz in mehreren Regionen erforderlich sind. Ein Beispiel hierfür ist eine IoT-Workload, bei der große Datenmengen in unterschiedlichen Regionen in die Datenbank geschrieben werden. <br/><br/> Für Schreibvorgänge in mehreren Regionen ist eine Lese- und Schreibverfügbarkeit von 99,999 % garantiert. Für Schreibvorgänge in mehreren Regionen ist im Vergleich mit dem Schreiben in einzelnen Regionen ein höherer Durchsatz erforderlich. Weitere Informationen finden Sie in dem Artikel, in dem beschrieben wird, [wie sich Anforderungseinheiten (RUs) für Schreibvorgänge in einzelne und mehrere Regionen unterscheiden](optimize-cost-regions.md).|
|Standardkonsistenz|Azure Cosmos DB unterstützt fünf Konsistenzebenen, damit Entwickler eine gute Balance zwischen Konsistenz, Verfügbarkeit und Latenz erzielen können. Weitere Informationen finden Sie im Artikel zu [Konsistenzebenen](consistency-levels.md). <br/><br/> Standardmäßig nutzt Azure Cosmos DB die Sitzungskonsistenz, um sicherzustellen, dass Sie in einer Sitzung Ihre eigenen Schreibvorgänge lesen können. <br/><br/> Bei der Auswahl der starken oder begrenzten Veraltung wird für Lesevorgänge der doppelte Durchsatz (RU/s) im Vergleich zu „Sitzungskonsistenz“, „Präfixkonsistenz“ und „Letztliche Konsistenz“ benötigt. Die starke Konsistenz mit Schreibvorgängen in mehreren Regionen wird nicht unterstützt, und es wird automatisch die Nutzung von Schreibvorgängen in einer einzelnen Region mit starker Konsistenz festgelegt. |
|Indizierungsrichtlinie|Standardmäßig wird von Azure Cosmos DB eine [Indizierung aller Eigenschaften](index-policy.md) in allen Elementen durchgeführt, um für flexible und effiziente Abfragen zu sorgen (der Indizierungsrichtlinie **Automatisch** zugeordnet). <br/><br/> Wenn Sie **Aus** wählen, wird keine Eigenschaft indiziert. Dies führt zu den niedrigsten RU-Gebühren für Schreibvorgänge. Wählen Sie für die Richtlinie die Option **Aus** , falls Sie nur mit [Lesevorgängen für Datenpunkte](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?preserve-view=true&view=azure-dotnet) (Schlüsselwertsuchen) bzw. Schreibvorgängen und nicht mit Abfragen rechnen. <br/><br/> Die benutzerdefinierte Indizierungsrichtlinie ermöglicht es Ihnen, bestimmte Eigenschaften für den Index ein- oder auszuschließen, damit weniger Durchsatz und Speicher für Schreibvorgänge benötigt wird. Weitere Informationen finden Sie in den Artikeln zur [Indizierungsrichtlinie](index-overview.md) und zu den [Beispielen für Indizierungsrichtlinien](how-to-manage-indexing-policy.md#indexing-policy-examples).|
|Gesamte gespeicherte Daten (pro Region)|Schätzung der gesamten Daten in GB, die in einer Region gespeichert sind.|
|Workloadmodus|Wählen Sie **Konstant** , wenn Ihre Workloadmenge konstant ist. <br/><br/> Wählen Sie **Variable** , wenn sich Ihre Workloadmenge im Laufe der Zeit ändert.  Beispielsweise an einem bestimmten Tag oder in einem Monat. <br/><br/> Die folgenden Einstellungen sind verfügbar, wenn Sie die Workloadoption „Variable“ wählen:<ul><li>Percentage of time at peak (Spitzenzeiten in Prozent): Der Prozentsatz der Zeit eines Monats, in der für Ihre Workload der höchste Durchsatz (Spitzendurchsatz) erforderlich ist. <br/><br/> Wenn Sie beispielsweise über eine Workload verfügen, für die während der Geschäftszeiten an Werktagen zwischen 9 Uhr und 18 Uhr eine hohe Aktivität besteht, beträgt der Prozentsatz der Spitzenzeit: 45 Spitzenstunden/730 Stunden/Monat = ~6 %.<br/><br/></li><li>Reads/sec per region at peak (Lesevorgänge/s pro Region zur Spitzenzeit): Anzahl von Lesevorgängen, die zur Spitzenzeit pro Sekunde erwartet werden.</li><li>Writes/sec per region at peak (Schreibvorgänge/s pro Region zur Spitzenzeit): Anzahl von Schreibvorgängen, die zur Spitzenzeit pro Sekunde erwartet werden.</li><li>Reads/sec per region off peak (Lesevorgänge/s pro Region außerhalb der Spitzenzeit): Anzahl von Lesevorgängen, die außerhalb der Spitzenzeit pro Sekunde erwartet werden.</li><li>Writes/sec per region off peak (Schreibvorgänge/s pro Region außerhalb der Spitzenzeit): Anzahl von Schreibvorgängen, die außerhalb der Spitzenzeit pro Sekunde erwartet werden.</li></ul>Mit Intervallen für Spitzenzeiten und andere Zeiten können Sie Ihre Kosten optimieren, indem Sie [Ihren bereitgestellten Durchsatz entsprechend programmgesteuert zentral hoch- und herunterskalieren](set-throughput.md#update-throughput-on-a-database-or-a-container).|
|Elementgröße|Die Größe des Datenelements (z. B. Dokument), die von 1 KB bis 2 MB reicht. <br/><br/>Sie können auch die Option zum **Hochladen eines Beispieldokuments (JSON)** verwenden, um eine genauere Schätzung zu erhalten.<br/><br/>Wenn Ihre Workload über mehrere Arten von Elementen (mit unterschiedlichem JSON-Inhalt) in demselben Container verfügt, können Sie mehrere JSON-Dokumente hochladen und eine entsprechende Schätzung erhalten. Verwenden Sie die Schaltfläche **Add new item** (Neues Element hinzufügen), um mehrere JSON-Beispieldokumente hinzuzufügen.|

Sie können auch die Schaltfläche **Save Estimate** (Schätzung speichern) verwenden, um eine CSV-Datei mit der aktuellen Schätzung herunterzuladen. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Capacity Planner: Modus „Advanced“":::

Die im Azure Cosmos DB Capacity Planner angezeigten Preise sind Schätzungen, die auf den öffentlichen Preisen für Durchsatz und Speicher basieren. Alle Preise sind in US-Dollar angegeben. Auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/) sind alle Preise nach Region aufgeführt.  

## <a name="estimating-throughput-for-queries"></a>Schätzen des Durchsatzes für Abfragen

Mit dem Kapazitätsrechner von Azure Cosmos wird für die Workload die Durchführung von Punktlesevorgängen (ein Lesevorgang eines einzelnen Elements, z. B. eines Dokuments, nach ID und Partitionsschlüsselwert) und -schreibvorgängen angenommen. Führen Sie Ihre Abfrage zum Schätzen des Durchsatzes, der für Abfragen benötigt wird, für ein repräsentatives Dataset in einem Cosmos-Container aus, und [ermitteln Sie die Gebühr für Anforderungseinheiten](find-request-unit-charge.md). Multiplizieren Sie die Gebühren für Anforderungseinheiten (RUs) mit der Anzahl von Abfragen, deren Ausführung Sie pro Sekunde erwarten, um die insgesamt benötigten Anforderungseinheiten pro Sekunde (RU/s) zu erhalten. 

Falls für Ihre Workload beispielsweise eine Abfrage wie ``SELECT * FROM c WHERE c.id = 'Alice'`` erforderlich ist, die 100-mal pro Sekunde ausgeführt wird und über 10 RUs verfügt, benötigen Sie für diese Anforderungen insgesamt 100 Abfragen/s · 10 RUs/Abfrage = 1.000 RU/s. Addieren Sie diese Anforderungen pro Sekunde (RU/s) zu den Anforderungen pro Sekunde, die für die Lese- oder Schreibvorgänge Ihrer Workload erforderlich sind.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich weiter über das [Preismodell von Azure Cosmos DB](how-pricing-works.md).
* Erstellen Sie ein neues [Cosmos-Konto, eine Datenbank und einen Container](create-cosmosdb-resources-portal.md).
* Erfahren Sie, wie Sie die [Kosten für den bereitgestellten Durchsatz optimieren](optimize-cost-throughput.md) können.
* Informieren Sie sich über das [Optimieren der Kosten mit Reservekapazität](cosmos-db-reserved-capacity.md).