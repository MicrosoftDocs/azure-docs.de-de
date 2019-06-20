---
title: Grundlegendes zu Azure Data Factory-Preisen anhand von Beispielen | Microsoft-Dokumentation
description: In diesem Artikel wird das Preismodell für Azure Data Factory mit ausführlichen Beispielen veranschaulicht.
documentationcenter: ''
author: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: a825982532047f6e311c5508394df243310f02ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233928"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Grundlegendes zu Azure Data Factory-Preisen anhand von Beispielen

In diesem Artikel wird das Preismodell für Azure Data Factory mit ausführlichen Beispielen veranschaulicht.

> [!NOTE]
> Die in diesen Beispielen verwendeten Preise sind hypothetisch und stellen keine tatsächliche Preisgestaltung dar.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Stündliches Kopieren von Daten aus AWS S3 in Azure Blob Storage

In diesem Szenario möchten Sie Daten aus AWS S3 stündlich in Azure Blob Storage kopieren.

Um dieses Szenario zu realisieren, müssen Sie eine Pipeline mit folgenden Elementen erstellen:

1. Eine Kopieraktivität mit einem Eingabedataset für die aus AWS S3 zu kopierenden Daten.

2. Ein Ausgabedataset für die Daten in Azure Storage.

3. Einen Zeitplantrigger, der die Pipeline einmal pro Stunde ausführt.

   ![Szenario1](media/pricing-concepts/scenario1.png)

| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 2 Lese-/Schreibzugriffentitäten  |
| Erstellen von Datasets | 4 Lese-/Schreibzugriffentitäten (2 für Dataseterstellung, 2 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 3 Lese-/Schreibzugriffentitäten (1 für Pipelineerstellung, 2 für Datasetverweise) |
| Abrufen der Pipeline | 1 Lese-/Schreibzugriffentität |
| Ausführen der Pipeline | 2 Aktivitätsausführungen (1 für den Trigger, 1 für die Aktivität) |
| Annahme für Kopieren der Daten: Ausführungszeit = 10 Minuten | 10 \* 4 Azure Integration Runtime (Standard-DIU-Einstellung = 4) Weitere Informationen zu Datenintegrationseinheiten und Optimieren der Kopierleistung finden Sie in [diesem Artikel](copy-activity-performance.md) |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 2 wiederholte Überwachungsausführungsaufzeichnungen (1 für die Pipelineausführung, 1 für die Aktivitätsausführung) |

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

![Szenario2](media/pricing-concepts/scenario2.png)

| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 3 Lese-/Schreibzugriffentitäten  |
| Erstellen von Datasets | 4 Lese-/Schreibzugriffentitäten (2 für Dataseterstellung, 2 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 3 Lese-/Schreibzugriffentitäten (1 für Pipelineerstellung, 2 für Datasetverweise) |
| Abrufen der Pipeline | 1 Lese-/Schreibzugriffentität |
| Ausführen der Pipeline | 3 Aktivitätsausführungen (1 für den Trigger, 2 für die Aktivität) |
| Annahme für Kopieren der Daten: Ausführungszeit = 10 Minuten | 10 \* 4 Azure Integration Runtime (Standard-DIU-Einstellung = 4) Weitere Informationen zu Datenintegrationseinheiten und Optimieren der Kopierleistung finden Sie in [diesem Artikel](copy-activity-performance.md) |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 3 wiederholte Überwachungsausführungsaufzeichnungen (1 für die Pipelineausführung, 2 für die Aktivitätsausführung) |
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

![Szenario3](media/pricing-concepts/scenario3.png)

| **Vorgänge** | **Typen und Einheiten** |
| --- | --- |
| Erstellen eines verknüpften Diensts | 3 Lese-/Schreibzugriffentitäten  |
| Erstellen von Datasets | 4 Lese-/Schreibzugriffentitäten (2 für Dataseterstellung, 2 für Verweise auf verknüpfte Dienste) |
| Erstellen der Pipeline | 3 Lese-/Schreibzugriffentitäten (1 für Pipelineerstellung, 2 für Datasetverweise) |
| Abrufen der Pipeline | 1 Lese-/Schreibzugriffentität |
| Ausführen der Pipeline | 4 Aktivitätsausführungen (1 für den Trigger, 3 für die Aktivität) |
| Annahme für Kopieren der Daten: Ausführungszeit = 10 Minuten | 10 \* 4 Azure Integration Runtime (Standard-DIU-Einstellung = 4) Weitere Informationen zu Datenintegrationseinheiten und Optimieren der Kopierleistung finden Sie in [diesem Artikel](copy-activity-performance.md) |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 4 wiederholte Überwachungsausführungsaufzeichnungen (1 für Pipelineausführung, 3 für Aktivitätsausführung) |
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

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>Verwenden des Debuggens für Mapping Data Flow für einen normalen Arbeitstag (Vorschaupreise)

Als Dateningenieur sind Sie täglich für das Entwerfen, Erstellen und Testen von Mapping Data Flows verantwortlich. Sie melden sich morgens in der ADF-Oberfläche an und aktivieren den Debugmodus für Datenflüsse. Die Standardgültigkeitsdauer (TTL) für Debugsitzungen ist 60 Minuten. Sie arbeiten den ganzen Tag 10 Stunden lang, sodass Ihre Debugsitzung nie abläuft. Daher fallen für diesen Tag folgende Gebühren an:

**10 (Stunden) X 8 (Kerne) x 0,112 US-$ = 8,96 US-$**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Transformieren von Daten im Blob-Speicher Mapping Data Flows (Vorschaupreise)

In diesem Szenario möchten Sie Daten in einem Blob-Speicher visuell in ADF Mapping Data Flows einem stundenweise ausgeführten Zeitplan transformieren.

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
| Data Flow-Annahmen: Ausführungszeit = 10 Minuten + 10 Minuten TTL | 10 \* 8 allgemeine Computekerne mit einem TTL-Wert von 10 |
| Annahme für Überwachung der Pipeline: Nur 1 Ausführung ist aufgetreten | 2 wiederholte Überwachungsausführungsaufzeichnungen (1 für die Pipelineausführung, 1 für die Aktivitätsausführung) |

**Preis für gesamtes Szenario: 0,3011 US-$**

- Data Factory-Vorgänge = **0,0001 US-$**
  - Lesen/Schreiben = 10\*00001 = 0,0001 US-$ [1 R/W = 0,50 US-$/50.000 = 0,00001]
  - Überwachung = 2\*000005 = 0,00001 US-$ [1 Überwachung = 0,25 US-$/50.000 = 0,000005]
- Pipelineorchestrierung &amp; -ausführung = **0,301 US-$**
  - Aktivitätsausführungen = 001\*2 = 0,002 [1 Ausführung = 1 US-$/1.000 = 0,001]
  - Data Flow-Aktivitäten = 0,299 US-$ anteilig für 20 Minuten (10 Minuten Ausführungszeit + 10 Minuten TTL). 0,112 US-$ pro Stunde für die Azure Integration Runtime mit 8 allgemeinen Computekernen

## <a name="next-steps"></a>Nächste Schritte

Jetzt kennen Sie die Preisgestaltung für Azure Data Factory und können beginnen!

- [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md)

- [Einführung in den Azure Data Factory-Dienst](introduction.md)

- [Visuelles Erstellen in Azure Data Factory](author-visually.md)
