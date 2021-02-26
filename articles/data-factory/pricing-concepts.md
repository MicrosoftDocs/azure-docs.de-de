---
title: Grundlegendes zu Azure Data Factory-Preisen anhand von Beispielen
description: In diesem Artikel wird das Preismodell für Azure Data Factory mit ausführlichen Beispielen veranschaulicht.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: b9f163a7632ca59d4f97aef21d8d62157610ba73
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372806"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Grundlegendes zu Azure Data Factory-Preisen anhand von Beispielen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird das Preismodell für Azure Data Factory mit ausführlichen Beispielen veranschaulicht.

> [!NOTE]
> Die in diesen Beispielen verwendeten Preise sind hypothetisch und stellen keine tatsächliche Preisgestaltung dar.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Stündliches Kopieren von Daten aus AWS S3 in Azure Blob Storage

In diesem Szenario möchten Sie Daten aus AWS S3 stündlich in Azure Blob Storage kopieren.

Um dieses Szenario zu realisieren, müssen Sie eine Pipeline mit folgenden Elementen erstellen:

1. Eine Kopieraktivität mit einem Eingabedataset für die aus AWS S3 zu kopierenden Daten.

2. Ein Ausgabedataset für die Daten in Azure Storage.

3. Einen Zeitplantrigger, der die Pipeline einmal pro Stunde ausführt.

   ![Diagramm einer Pipeline mit einem Zeitplantrigger. In der Pipeline fließt die Kopieraktivität in ein Eingabedataset, das zu einem verknüpften AWS-S3-Dienst fließt. Außerdem fließt die Kopieraktivität auch in ein Ausgabedataset, das zu einem verknüpften Azure Storage-Dienst fließt.](media/pricing-concepts/scenario1.png)

| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 2 Lese-/Schreibzugriffentitäten  |
| Erstellen von Datasets | 4 Lese-/Schreibzugriffentitäten (2 für Dataseterstellung, 2 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 3 Lese-/Schreibzugriffentitäten (1 für Pipelineerstellung, 2 für Datasetverweise) |
| Abrufen der Pipeline | 1 Lese-/Schreibzugriffentität |
| Ausführen der Pipeline | 2 Aktivitätsausführungen (1 für den Trigger, 1 für die Aktivität) |
| Annahme für Kopieren der Daten: Ausführungszeit = 10 Minuten | 10 \* 4 Azure Integration Runtime (Standard-DIU-Einstellung = 4) Weitere Informationen zu Datenintegrationseinheiten und Optimieren der Kopierleistung finden Sie in [diesem Artikel](copy-activity-performance.md) |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 2 abgerufene Überwachungsausführungsaufzeichnungen (1 für Pipelineausführung, 1 für Aktivitätsausführung) |

**Preis für gesamtes Szenario: 0,16811 US-$**

- Data Factory-Vorgänge = **0,0001 US-$**
  - Lesen/Schreiben = 10\*00001 = 0,0001 US-$ [1 R/W = 0,50 US-$/50.000 = 0,00001]
  - Überwachung = 2\*000005 = 0,00001 US-$ [1 Überwachung = 0,25 US-$/50.000 = 0,000005]
- Pipelineorchestrierung &amp; -ausführung = **0,168 US-$**
  - Aktivitätsausführungen = 001\*2 = 0,002 [1 Ausführung = 1 US-$/1.000 = 0,001]
  - Datenverschiebungsaktivitäten = 0,166 US-$ (anteilig für die Ausführungszeit von 10 Minuten. 0,25 US-$/Stunde auf Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Stündliches Kopieren und Transformieren von Daten mit Azure Databricks

In diesem Szenario möchten Sie stündlich Daten aus AWS S3 in Azure Blob Storage kopieren und die Daten mit Azure Databricks transformieren.

Um dieses Szenario zu realisieren, müssen Sie eine Pipeline mit folgenden Elementen erstellen:

1. Eine Kopieraktivität mit einem Eingabedataset für die aus AWS S3 kopierten Daten und ein Ausgabedataset für die Daten in Azure Storage.
2. Eine Azure Databricks-Aktivität für die Datentransformation.
3. Ein Zeitplantrigger, um die Pipeline einmal pro Stunde auszuführen.

![Diagramm einer Pipeline mit einem Zeitplantrigger. In der Pipeline fließt die Kopieraktivität in ein Eingabedataset, ein Ausgabedataset und eine Databricks-Aktivität, die in Azure Databricks ausgeführt wird. Das Eingabedataset fließt in einen verknüpften AWS-S3-Dienst. Das Ausgabedataset fließt in einen verknüpften Azure Storage-Dienst.](media/pricing-concepts/scenario2.png)

| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 3 Lese-/Schreibzugriffentitäten  |
| Erstellen von Datasets | 4 Lese-/Schreibzugriffentitäten (2 für Dataseterstellung, 2 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 3 Lese-/Schreibzugriffentitäten (1 für Pipelineerstellung, 2 für Datasetverweise) |
| Abrufen der Pipeline | 1 Lese-/Schreibzugriffentität |
| Ausführen der Pipeline | 3 Aktivitätsausführungen (1 für den Trigger, 2 für die Aktivität) |
| Annahme für Kopieren der Daten: Ausführungszeit = 10 Minuten | 10 \* 4 Azure Integration Runtime (Standard-DIU-Einstellung = 4) Weitere Informationen zu Datenintegrationseinheiten und Optimieren der Kopierleistung finden Sie in [diesem Artikel](copy-activity-performance.md) |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 3 abgerufene Überwachungsausführungsaufzeichnungen (1 für Pipelineausführung, 2 für Aktivitätsausführung) |
| Annahme für Ausführung der Databricks-Aktivität: Ausführungszeit = 10 Minuten | 10 Minuten Ausführung der externen Pipelineaktivität |

**Preis für gesamtes Szenario: 0,16916 US-$**

- Data Factory-Vorgänge = **0,00012 US-$**
  - Lesen/Schreiben = 11\*00001 = 0,00011 US-$ [1 R/W = 0,50 US-$/50.000 = 0,00001]
  - Überwachung = 3\*000005 = 0,00001 US-$ [1 Überwachung = 0,25 US-$/50.000 = 0,000005]
- Pipelineorchestrierung &amp; -ausführung = **0,16904 US-$**
  - Aktivitätsausführungen = 001\*3 = 0,003 [1 Ausführung = 1 US-$/1.000 = 0,001]
  - Datenverschiebungsaktivitäten = 0,166 US-$ (anteilig für die Ausführungszeit von 10 Minuten. 0,25 US-$/Stunde auf Azure Integration Runtime)
  - Externe Pipelineaktivität = 0,000041 US-$ (anteilig für die Ausführungszeit von 10 Minuten. 0,00025 US-$/Stunde auf Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Stündliches Kopieren und Transformieren von Daten mit dynamischen Parametern

In diesem Szenario möchten Sie stündlich Daten aus AWS S3 in Azure Blob Storage kopieren und die Daten mit Azure Databricks transformieren (mit dynamischen Parametern im Skript).

Um dieses Szenario zu realisieren, müssen Sie eine Pipeline mit folgenden Elementen erstellen:

1. Eine Kopieraktivität mit einem Eingabedataset für die aus AWS S3 kopierten Daten und ein Ausgabedataset für die Daten in Azure Storage.
2. Eine Suchaktivität zur dynamischen Übergabe von Parametern an das Skript für die Transformation.
3. Eine Azure Databricks-Aktivität für die Datentransformation.
4. Ein Zeitplantrigger, um die Pipeline einmal pro Stunde auszuführen.

![Diagramm einer Pipeline mit einem Zeitplantrigger. In der Pipeline fließt die Kopieraktivität in ein Eingabedataset, ein Ausgabedataset und eine Suchaktivität, die in eine Databricks-Aktivität fließt, die in Azure Databricks ausgeführt wird. Das Eingabedataset fließt in einen verknüpften AWS-S3-Dienst. Das Ausgabedataset fließt in einen verknüpften Azure Storage-Dienst.](media/pricing-concepts/scenario3.png)

| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 3 Lese-/Schreibzugriffentitäten  |
| Erstellen von Datasets | 4 Lese-/Schreibzugriffentitäten (2 für Dataseterstellung, 2 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 3 Lese-/Schreibzugriffentitäten (1 für Pipelineerstellung, 2 für Datasetverweise) |
| Abrufen der Pipeline | 1 Lese-/Schreibzugriffentität |
| Ausführen der Pipeline | 4 Aktivitätsausführungen (1 für den Trigger, 3 für die Aktivität) |
| Annahme für Kopieren der Daten: Ausführungszeit = 10 Minuten | 10 \* 4 Azure Integration Runtime (Standard-DIU-Einstellung = 4) Weitere Informationen zu Datenintegrationseinheiten und Optimieren der Kopierleistung finden Sie in [diesem Artikel](copy-activity-performance.md) |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 4 abgerufene Überwachungsausführungsaufzeichnungen (1 für Pipelineausführung, 3 für Aktivitätsausführung) |
| Annahme für Ausführung der Suchaktivität: Ausführungszeit = 1 Minute | 1 Minute Ausführung der Pipelineaktivität |
| Annahme für Ausführung der Databricks-Aktivität: Ausführungszeit = 10 Minuten | 10 Minuten Ausführung der externen Pipelineaktivität |

**Preis für gesamtes Szenario: 0,17020 US-$**

- Data Factory-Vorgänge = **0,00013 US-$**
  - Lesen/Schreiben = 11\*00001 = 0,00011 US-$ [1 R/W = 0,50 US-$/50.000 = 0,00001]
  - Überwachung = 4\*000005 = 0,00002 US-$ [1 Überwachung = 0,25 US-$/50.000 = 0,000005]
- Pipelineorchestrierung &amp; -ausführung = **0,17007 US-$**
  - Aktivitätsausführungen = 001\*4 = 0,004 [1 Ausführung = 1 US-$/1.000 = 0,001]
  - Datenverschiebungsaktivitäten = 0,166 US-$ (anteilig für die Ausführungszeit von 10 Minuten. 0,25 US-$/Stunde auf Azure Integration Runtime)
  - Pipelineaktivität = 0,00003 US-$ (anteilig für die Ausführungszeit von 1 Minute. 0,002 US-$/Stunde auf Azure Integration Runtime)
  - Externe Pipelineaktivität = 0,000041 US-$ (anteilig für die Ausführungszeit von 10 Minuten. 0,00025 US-$/Stunde auf Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Verwenden der Zuordnungsdatenfluss-Debugfunktion für einen normalen Arbeitstag

Als Dateningenieur ist Sam täglich für das Entwerfen, Erstellen und Testen von Zuordnungsdatenflüssen verantwortlich. Sam melden sich morgens in der ADF-Benutzeroberfläche an und aktiviert den Debugmodus für Datenflüsse. Die Standardgültigkeitsdauer (TTL) für Debugsitzungen ist 60 Minuten. Sam arbeitet den ganzen Tag 8 Stunden lang, sodass die Debugsitzung nie abläuft. Daher fallen für diesen Tag folgende Gebühren für Sam an:

**8 (Stunden) x 8 (computeoptimierte Kerne) x 0,193 US-$ = 12,35 US-$**

Gleichzeitig meldet sich Chris, ein anderer Datenengineer, ebenfalls bei der Browserbenutzeroberfläche von ADF an, um Datenprofile zu erstellen und an ETL-Entwürfen zu arbeiten. Chris arbeitet nicht wie Sam den ganzen Tag in ADF. Chris muss lediglich den Datenflussdebugger für eine Stunde innerhalb desselben Zeitraums und an demselben Tag wie Sam oben verwenden. Diese Gebühren fallen bei Chris für die Debugnutzung an:

**1 (Stunde) × 8 (universelle Kerne) × 0,274 USD = 2,19 USD**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformieren von Daten im Blobspeicher mit Zuordnungsdatenflüssen

In diesem Szenario möchten Sie stündlich Daten in einem Blob-Speicher visuell in ADF Mapping Data Flows transformieren.

Um dieses Szenario zu realisieren, müssen Sie eine Pipeline mit folgenden Elementen erstellen:

1. Eine Data Flow-Aktivität mit der Transformationslogik.

2. Ein Eingabedataset für die Daten in Azure Storage.

3. Ein Ausgabedataset für die Daten in Azure Storage.

4. Einen Zeitplantrigger, der die Pipeline einmal pro Stunde ausführt.

| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 2 Lese-/Schreibzugriffentitäten  |
| Erstellen von Datasets | 4 Lese-/Schreibzugriffentitäten (2 für Dataseterstellung, 2 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 3 Lese-/Schreibzugriffentitäten (1 für Pipelineerstellung, 2 für Datasetverweise) |
| Abrufen der Pipeline | 1 Lese-/Schreibzugriffentität |
| Ausführen der Pipeline | 2 Aktivitätsausführungen (1 für den Trigger, 1 für die Aktivität) |
| Data Flow-Annahmen: Ausführungszeit = 10 Minuten + 10 Minuten TTL | 10 \* 16 allgemeine Computekerne mit einer TTL von 10 |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 2 abgerufene Überwachungsausführungsaufzeichnungen (1 für Pipelineausführung, 1 für Aktivitätsausführung) |

**Preis für gesamtes Szenario: 1,4631 US-$**

- Data Factory-Vorgänge = **0,0001 US-$**
  - Lesen/Schreiben = 10\*00001 = 0,0001 US-$ [1 R/W = 0,50 US-$/50.000 = 0,00001]
  - Überwachung = 2\*000005 = 0,00001 US-$ [1 Überwachung = 0,25 US-$/50.000 = 0,000005]
- Pipelineorchestrierung &amp; -ausführung = **1,463 US-$**
  - Aktivitätsausführungen = 001\*2 = 0,002 [1 Ausführung = 1 US-$/1.000 = 0,001]
  - Datenflussaktivitäten = 1,461 US-$ anteilig für 20 Minuten (10 Minuten Ausführungszeit + 10 Minuten TTL). 0,274 US-$ pro Stunde für die Azure Integration Runtime mit 16 allgemeinen Computekernen

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Datenintegration in einem verwalteten Azure Data Factory-VNET
In diesem Szenario möchten Sie die ursprünglichen Dateien in Azure Blob Storage löschen und Daten aus Azure SQL-Datenbank in Azure Blob Storage kopieren. Diese Ausführung erfolgt zweimal für unterschiedliche Pipelines. Die Ausführungszeit dieser beiden Pipelines überschneidet sich.
![Szenario 4:](media/pricing-concepts/scenario-4.png) Um dieses Szenario zu realisieren, müssen Sie zwei Pipelines mit folgenden Elementen erstellen:
  - Eine Pipelineaktivität – Löschaktivität.
  - Eine Kopieraktivität mit einem Eingabedataset für die aus Azure Blob Storage zu kopierenden Daten.
  - Ein Ausgabedataset für die Daten in Azure SQL-Datenbank.
  - Einen Zeitplantrigger zum Ausführen der Pipeline.


| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 4 Lese-/Schreibzugriffentitäten |
| Erstellen von Datasets | 8 Lese-/Schreibzugriffentitäten (4 für Dataseterstellung, 4 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 6 Lese-/Schreibzugriffentitäten (2 für Pipelineerstellung, 4 für Datasetverweise) |
| Abrufen der Pipeline | 2 Lese-/Schreibzugriffentitäten |
| Ausführen der Pipeline | 6 Aktivitätsausführungen (2 für Triggerausführung, 4 für die Aktivität) |
| Ausführen der Löschaktivität: jede Ausführungszeit = 5 min. Die Ausführung der Löschaktivität in der ersten Pipeline liegt zwischen 10:00 Uhr UTC und 10:05 Uhr UTC. Die Ausführung der Löschaktivität in der zweiten Pipeline liegt zwischen 10:02 Uhr UTC und 10:07 Uhr UTC.|Insgesamt eine 7-minütige Ausführung der Pipelineaktivitäten im verwalteten VNET. Die Pipelineaktivität unterstützt eine Parallelität von bis zu 50 im verwalteten VNET. |
| Annahme für die Kopieraktivität: jede Ausführungszeit = 10 min. Die Ausführung der Kopieraktivität in der ersten Pipeline liegt zwischen 10:06 Uhr UTC und 10:15 Uhr UTC. Die Ausführung der Löschaktivität in der zweiten Pipeline liegt zwischen 10:08 Uhr UTC und 10:17 Uhr UTC. | 10 × 4 Azure Integration Runtime (Standard-DIU-Einstellung = 4) Weitere Informationen zu Datenintegrationseinheiten und Optimieren der Kopierleistung finden Sie in [diesem Artikel](copy-activity-performance.md). |
| Annahme für Überwachung der Pipeline: Nur 2 Ausführungen aufgetreten | 6 abgerufene Überwachungsausführungsaufzeichnungen (2 für Pipelineausführung, 4 für Aktivitätsausführung) |


**Preis für gesamtes Szenario: 0,45523 USD**

- Data Factory-Vorgänge = 0,00023 USD
  - Lesen/Schreiben = 20 × 0,00001 = 0,0002 USD [1 R/W = 0,50 USD/50.000 = 0,00001]
  - Überwachung = 6 × 0,000005 = 0,00003 USD [1 Überwachung = 0,25 USD/50.000 = 0,000005]
- Pipelineorchestrierung und -ausführung = 0,455 USD
  - Aktivitätsausführungen = 0,001 × 6 = 0,006 [1 Ausführung = 1 USD/1.000 = 0,001]
  - Datenverschiebungsaktivitäten = 0,333 USD (anteilig für die Ausführungszeit von 10 Minuten. 0,25 US-$/Stunde auf Azure Integration Runtime)
  - Pipelineaktivität = 0,116 USD (anteilig für die Ausführungszeit von 7 Minuten. 1 USD/Stunde auf Azure Integration Runtime)

> [!NOTE]
> Diese Preise dienen nur als Beispiel.

**Häufig gestellte Fragen**

F: Können diese Aktivitäten gleichzeitig ausgeführt werden, wenn Sie mehr als 50 Pipelineaktivitäten ausführen möchten?

A: Es sind höchstens 50 gleichzeitige Pipelineaktivitäten zulässig.  Die 51. Pipelineaktivität wird in die Warteschlange eingereiht, bis ein Slot frei wird. Das gilt auch für externe Aktivitäten. Es sind höchstens 800 gleichzeitige externe Pipelineaktivitäten zulässig.

## <a name="next-steps"></a>Nächste Schritte

Jetzt kennen Sie die Preisgestaltung für Azure Data Factory und können beginnen!

- [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md)

- [Einführung in den Azure Data Factory-Dienst](introduction.md)

- [Visuelles Erstellen in Azure Data Factory](author-visually.md)
