---
title: Installieren von Speech-Containern
titleSuffix: Azure Cognitive Services
description: Beschreibt die Konfigurationsoptionen für ein Helm-Diagramm zur Spracherkennung.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002273"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Spracherkennung (Unterdiagramm: charts/speechToText)

Zum Überschreiben des übergeordneten Diagramms fügen Sie das Präfix `speechToText.` bei einem beliebigen Parameter hinzu, um das Diagramm genauer zu definieren. Dadurch wird der entsprechende Parameter überschrieben. Beispielsweise überschreibt `speechToText.numberOfConcurrentRequest` den Parameter `numberOfConcurrentRequest`.

|Parameter|BESCHREIBUNG|Standard|
| -- | -- | -- |
| `enabled` | Gibt an, ob der **Spracherkennungs**-Dienst aktiviert ist. | `false` |
| `numberOfConcurrentRequest` | Die Anzahl gleichzeitiger Anforderungen für den **Spracherkennungs**-Dienst. In diesem Diagramm werden automatisch CPU- und Speicherressourcen basierend auf diesem Wert berechnet. | `2` |
| `optimizeForAudioFile`| Gibt an, ob der Dienst für die Audioeingabe über Audiodateien optimiert werden muss. Wenn `true`, werden in diesem Diagramm dem Dienst mehr CPU-Ressourcen zugeordnet. | `false` |
| `image.registry`| Die Docker-Imageregistrierung für **Spracherkennung**. | `containerpreview.azurecr.io` |
| `image.repository` | Das Docker-Imagerepository für **Spracherkennung**. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Das Docker-Imagetag für **Spracherkennung**. | `latest` |
| `image.pullSecrets` | Die Imagegeheimnisse zum Abrufen des Docker-Images für **Spracherkennung**. | |
| `image.pullByHash`| Gibt an, ob das Docker-Image mithilfe von Hashing abgerufen wird. Wenn `true`, ist `image.hash` erforderlich. | `false` |
| `image.hash`| Das Docker-Imagehash für **Spracherkennung**. Nur verwendet, wenn `image.pullByHash: true`.  | |
| `image.args.eula` (erforderlich) | Gibt an, dass Sie die Lizenz akzeptiert haben. Der einzige gültige Wert ist `accept`. | |
| `image.args.billing` (erforderlich) | Den URI des Abrechnungsendpunkts finden Sie im Azure-Portal auf der Speech-Übersichtsseite. | |
| `image.args.apikey` (erforderlich) | Wird zum Nachverfolgen von Abrechnungsinformationen verwendet. ||
| `service.type` | Die Kubernetes-Diensttyp des **Spracherkennungs**-Diensts. Weitere Informationen finden Sie unter den [Kubernetes-Anweisungen für Diensttypen ](https://kubernetes.io/docs/concepts/services-networking/service/) und beim Support des Cloudanbieters. | `LoadBalancer` |
| `service.port`|  Der Port des **Spracherkennungs**-Diensts. | `80` |
| `service.annotations` | Die **Spracherkennungs**-Anmerkungen für die Dienstmetadaten. Anmerkungen sind Schlüssel-Wert-Paare. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Gibt an, ob die [horizontale automatische Podskalierung](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) aktiviert ist. Wenn `true`, wird `speech-to-text-autoscaler` im Kubernetes-Cluster bereitgestellt. | `true` |
| `service.podDisruption.enabled` | Gibt an, ob das [Budget für die Unterbrechung von Pods](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) aktiviert ist. Wenn `true`, wird `speech-to-text-poddisruptionbudget` im Kubernetes-Cluster bereitgestellt. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Standpunktanalyse (Unterdiagramm: charts/speechToText)

Ab der Version 2.2.0 des Spracherkennungscontainers und Version 0.2.0 des Helm-Charts werden folgende Parameter für die Standpunktanalyse mit der Textanalyse-API verwendet.

|Parameter|BESCHREIBUNG|Werte|Standard|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Gibt an, ob der Dienst **Textanalyse** aktiviert ist.| TRUE/FALSE| `false`|
|`textanalytics.image.registry`| Die Docker-Imageregistrierung für **Textanalyse**.| Gültige Docker-Imageregistrierung| |
|`textanalytics.image.repository`| Das Docker-Imagerepository für **Textanalyse**.| Gültiges Docker-Imagerepository| |
|`textanalytics.image.tag`| Das Docker-Imagetag für **Textanalyse**.| Gültiges Docker-Imagetag| |
|`textanalytics.image.pullSecrets`| Die Imagegeheimnisse zum Pullen des Docker-Images für **Textanalyse**.| Gültiger Geheimnisname| |
|`textanalytics.image.pullByHash`| Gibt an, ob das Docker-Image per Hash gepullt wird.  `yes`: `image.hash` muss ebenfalls vorhanden sein. `no`: Legen Sie die Einstellung auf „false“ fest. Der Standardwert ist `false`.| TRUE/FALSE| `false`|
|`textanalytics.image.hash`| Der Docker-Imagehash für **Textanalyse**. Darf nur in Kombination mit `image.pullByHash:true` verwendet werden.| Gültiger Docker-Imagehash | |
|`textanalytics.image.args.eula`| Eines der für den Container vom Typ **Textanalyse** erforderlichen Argumente, um anzugeben, dass Sie die Lizenz akzeptiert haben. Der Wert dieser Option muss `accept` lauten.| `accept`, wenn Sie den Container verwenden möchten | |
|`textanalytics.image.args.billing`| Eines der für den Container vom Typ **Textanalyse** erforderlichen Argumente zur Angabe des URI des Abrechnungsendpunkts. Den URI des Abrechnungsendpunkts finden Sie im Azure-Portal auf der Speech-Übersichtsseite.|Gültiger URI des Abrechnungsendpunkts||
|`textanalytics.image.args.apikey`| Eines der für den Container vom Typ **Textanalyse** erforderlichen Argumente zur Nachverfolgung von Abrechnungsinformationen.| Gültiger API-Schlüssel||
|`textanalytics.cpuRequest`| Die angeforderte CPU für den Container vom Typ **Textanalyse**.| INT| `3000m`|
|`textanalytics.cpuLimit`| Die eingeschränkte CPU für den Container vom Typ **Textanalyse**.| | `8000m`|
|`textanalytics.memoryRequest`| Der angeforderte Arbeitsspeicher für den Container vom Typ **Textanalyse**.| | `3Gi`|
|`textanalytics.memoryLimit`| Der eingeschränkte Arbeitsspeicher für den Container vom Typ **Textanalyse**.| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| Das URI-Suffix der Standpunktanalyse. Der gesamte URI hat das Format „http://`<service>`:`<port>`/`<sentimentURISuffix>`“. | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Die Art des Diensts **Textanalyse** in Kubernetes. Weitere Informationen zu Kubernetes-Diensttypen finden Sie [hier](https://kubernetes.io/docs/concepts/services-networking/service/). | Gültiger Kubernetes-Diensttyp | `LoadBalancer` |
|`textanalytics.service.port`| Der Port des Diensts **Textanalyse**.| INT| `50085`|
|`textanalytics.service.annotations`| Die Anmerkungen, die Benutzer den Metadaten des Diensts **Textanalyse** hinzufügen können. Beispiel:<br/> **annotations:**<br/>`   ` **beliebige/Anmerkung1: Wert1**<br/>`  ` **beliebige/Anmerkung2: Wert2** | Anmerkungen, jeweils eine pro Zeile| |
|`textanalytics.serivce.autoScaler.enabled`| Gibt an, ob die [horizontale automatische Podskalierung](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) aktiviert ist. Falls ja, wird `text-analytics-autoscaler` im Kubernetes-Cluster bereitgestellt. | TRUE/FALSE| `true`|
|`textanalytics.service.podDisruption.enabled`| Gibt an, ob das [Budget für die Unterbrechung von Pods](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) aktiviert ist. Falls ja, wird `text-analytics-poddisruptionbudget` im Kubernetes-Cluster bereitgestellt.| TRUE/FALSE| `true`|
