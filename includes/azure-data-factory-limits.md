---
title: include file
description: include file
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 1e8d13e7cf302c486bab291ef6482216122b62a3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681219"
---
Azure Data Factory ist ein mehrinstanzenfähiger Dienst mit den folgenden Standardgrenzwerten, die sicherstellen sollen, dass Kundenabonnements vor anderen Workloads geschützt sind. Wenden Sie sich an den Support, um die Grenzwerte bis zum Höchstwert für Ihr Abonnement zu erhöhen.

### <a name="version-2"></a>Version 2

| Resource | Standardlimit | Maximales Limit |
| -------- | ------------- | ------------- |
| Data Factorys in einem Azure-Abonnement | 800 | 800 |
| Gesamtanzahl von Entitäten (z. B. Pipelines, Datasets, Trigger, verknüpfte Dienste, private Endpunkte und Integration Runtimes) in einer Data Factory | 5\.000 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gesamtanzahl CPU-Kerne für Azure-SSIS Integration Runtimes unter einem Abonnement | 256 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gleichzeitige Pipelineausführungen pro Data Factory (von allen Pipelines der Factory gemeinsam genutzt) | 10.000  | 10.000 |
| Gleichzeitige externe Aktivitätsausführungen pro Abonnement und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Externe Aktivitäten werden in der Integration Runtime verwaltet, aber in verknüpften Diensten ausgeführt. Hierzu gehören unter anderem Databricks, gespeicherte Prozeduren, HDInsights und Web. Die Beschränkung gilt nicht für die selbstgehostete IR.</small> | 3,000 | 3,000 |
| Gleichzeitige Pipelineaktivitätsausführungen pro Abonnement und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>Pipelineaktivitäten werden in der Integration Runtime ausgeführt. Hierzu zählen unter anderem „Lookup“, „GetMetadata“ und „Delete“. Die Beschränkung gilt nicht für die selbstgehostete IR.</small> | 1\.000 | 1\.000                                                        |
| Gleichzeitige Erstellungsvorgänge pro Abonnement und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Einschließlich Verbindung testen, Ordnerliste und Tabellenliste durchsuchen und Datenvorschau anzeigen. Die Beschränkung gilt nicht für die selbstgehostete IR.</small> | 200 | 200                                                          |
| Gleichzeitige Nutzung von Datenintegrationseinheiten<sup>1</sup> pro Abonnement und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regionsgruppe 1<sup>2</sup>: 6.000<br>Regionsgruppe 2<sup>2</sup>: 3,000<br>Regionsgruppe 3<sup>2</sup>: 1.500 | Regionsgruppe 1<sup>2</sup>: 6.000<br/>Regionsgruppe 2<sup>2</sup>: 3,000<br/>Regionsgruppe 3<sup>2</sup>: 1.500 |
| Maximale Aktivitäten pro Pipeline, einschließlich interner Aktivitäten für Container | 40 | 40 |
| Maximale Anzahl verknüpfter Integration Runtimes, die für eine einzelne selbstgehostete Integration Runtime erstellt werden kann | 100 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale Parameter pro Pipeline | 50 | 50 |
| ForEach-Elemente | 100.000 | 100.000 |
| ForEach-Parallelität | 20 | 50 |
| Maximale Anzahl von Ausführungen in Warteschlange pro Pipeline | 100 | 100 |
| Zeichen pro Ausdruck | 8\.192 | 8\.192 |
| Mindestintervall für Trigger für ein rollierendes Fenster | 15 Min. | 15 Min. |
| Maximales Zeitlimit für Ausführungen von Pipelineaktivitäten | 7 Tage | 7 Tage |
| Bytes pro Objekt für Pipelineobjekte<sup>3</sup> | 200 KB | 200 KB |
| Bytes pro Objekt für Datasetobjekte und verknüpfte Dienstobjekte<sup>3</sup> | 100 KB | 2\.000 KB |
| Bytes pro Nutzdaten für jede Aktivitätsausführung<sup>4</sup> | 896 KB | 896 KB |
| Datenintegrationseinheiten<sup>1</sup> pro Kopieraktivitätsausführung | 256 | 256 |
| Schreiben von API-Aufrufen | 1\.200/h | 1\.200/h<br/><br/> Dieser Grenzwert wird von Azure Resource Manager, nicht von Azure Data Factory vorgegeben. |
| Lesen von API-Aufrufen | 12.500/h | 12.500/h<br/><br/> Dieser Grenzwert wird von Azure Resource Manager, nicht von Azure Data Factory vorgegeben. |
| Überwachungsabfragen pro Minute | 1\.000 | 1\.000 |
| Maximale Zeit für Datenfluss-Debugsitzung | 8 Std. | 8 Std. |
| Gleichzeitige Anzahl von Datenflüssen pro Integration Runtime | 50 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gleichzeitige Anzahl von Datenfluss-Debugsitzungen pro Benutzer pro Factory | 3 | 3 |
| Azure IR TTL-Limit für Datenfluss | 4 Std. |  4 Std. |

<sup>1</sup> Die Datenintegrationseinheit (Data Integration Unit, DIU) wird bei einem Cloud-zu-Cloud-Kopiervorgang verwendet. Weitere Informationen finden Sie unter [Datenintegrationseinheiten](../articles/data-factory/copy-activity-performance.md#data-integration-units). Weitere Informationen zur Abrechnung finden Sie unter [Azure Data Factory – Preise](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) ist [global verfügbar](https://azure.microsoft.com/global-infrastructure/services/), um Datencompliance, Effizienz und geringere Kosten für ausgehenden Netzwerkdatenverkehr zu gewährleisten. 

| Regionsgruppe | Regions |
| -------- | ------ |
| Regionsgruppe 1 | „USA, Mitte“, „USA, Osten“, „USA, Osten 2“, „Europa, Norden“, „Europa, Westen“ „USA, Westen“, „USA, Westen 2“ |
| Regionsgruppe 2 | „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Indien, Mitte“, „Japan, Osten“, „USA, Norden-Mitte“, „USA, Süden-Mitte“, „Asien, Südosten“, „USA, Westen-Mitte“ |
| Regionsgruppe 3 | „Kanada, Mitte“, „Asien, Osten“, „Frankreich, Mitte“, „Südkorea Mitte“, „Vereinigtes Königreich, Süden“ |

<sup>3</sup> Objekte für Pipelines, Datasets und verknüpfte Dienste stellen eine logische Gruppierung Ihrer Workload dar. Die Grenzwerte für diese Objekte beziehen sich nicht auf die Datenmenge, die Sie mit Azure Data Factory verschieben und verarbeiten können. Data Factory kann so skaliert werden, dass Petabytes an Daten verarbeitet werden können.

<sup>4</sup> Die Nutzdaten für jede Aktivitätsausführung enthalten die Aktivitätskonfiguration, die Konfigurationen der zugeordneten Datasets und verknüpften Dienste (sofern vorhanden) sowie einen kleinen Teil der Systemeigenschaften, die pro Aktivitätstyp generiert werden. Die Grenzwerte für diese Nutzdaten beziehen sich nicht auf die Datenmenge, die Sie mit Azure Data Factory verschieben und verarbeiten können. [Hier](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) erfahren Sie mehr über die Symptome und Empfehlungen bei Erreichen dieser Grenzwerte.

### <a name="version-1"></a>Version 1

| **Ressource** | **Standardlimit** | **Maximales Limit** |
| --- | --- | --- |
| Pipelines innerhalb einer Data Factory |2\.500 |[Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Datasets innerhalb einer Data Factory |5\.000 |[Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gleichzeitige Slices pro Dataset |10 |10 |
| Bytes pro Objekt für Pipelineobjekte<sup>1</sup> |200 KB |200 KB |
| Bytes pro Objekt für Datasets und Objekte für verknüpfte Dienste<sup>1</sup> |100 KB |2\.000 KB |
| Bedarfsgesteuerte Azure HDInsight-Clusterkerne innerhalb eines Abonnements<sup>2</sup> |60 |[Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Einheiten für Clouddatenverschiebungen pro Kopieraktivitätsausführung<sup>3</sup> |32 |32 |
| Anzahl der Wiederholungsversuche für Ausführungen von Pipelineaktivitäten |1\.000 |MaxInt (32 Bit) |

<sup>1</sup> Objekte für Pipelines, Datasets und verknüpfte Dienste stellen eine logische Gruppierung Ihrer Workload dar. Die Grenzwerte für diese Objekte beziehen sich nicht auf die Datenmenge, die Sie mit Azure Data Factory verschieben und verarbeiten können. Data Factory kann so skaliert werden, dass Petabytes an Daten verarbeitet werden können.

<sup>2</sup> Bedarfsgesteuerte HDInsight-Kerne werden über das Abonnement zugewiesen, das die Data Factory enthält. Daher ist der frühere Grenzwert der von Data Factory erzwungene Grenzwert für bedarfsgesteuerte HDInsight-Kerne. Er unterscheidet sich vom Grenzwert für Kerne, der Ihrem Azure-Abonnement zugeordnet ist.

<sup>3</sup> Die Einheit für Clouddatenverschiebungen (Cloud Data Movement Unit, DMU) für Version 1 wird in einem Cloud-zu-Cloud-Kopiervorgang verwendet. Weitere Informationen finden Sie unter [Einheiten für Clouddatenverschiebungen](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Weitere Informationen zur Abrechnung finden Sie unter [Azure Data Factory – Preise](https://azure.microsoft.com/pricing/details/data-factory/).

| **Ressource** | **Unteres Standardlimit** | **Minimales Limit** |
| --- | --- | --- |
| Planungsintervall |15 Minuten |15 Minuten |
| Intervall zwischen Wiederholungsversuchen |1 Sekunde |1 Sekunde |
| Timeoutwert für Wiederholungsversuche |1 Sekunde |1 Sekunde |

#### <a name="web-service-call-limits"></a>Limits für Webdienstaufrufe
Azure Resource Manager weist Grenzwerte für API-Aufrufe auf. Sie können API-Aufrufe mit einer Rate innerhalb der [API-Limits von Azure Resource Manager](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)ausführen.
