---
title: Installieren von Speech-Containern
titleSuffix: Azure Cognitive Services
description: Beschreibt die Konfigurationsoptionen für ein Helm-Diagramm zur Sprachsynthese.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002274"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Sprachsynthese (Unterdiagramm: charts/textToSpeech)

Zum Überschreiben des übergeordneten Diagramms fügen Sie das Präfix `textToSpeech.` bei einem beliebigen Parameter hinzu, um das Diagramm genauer zu definieren. Dadurch wird der entsprechende Parameter überschrieben. Beispielsweise überschreibt `textToSpeech.numberOfConcurrentRequest` den Parameter `numberOfConcurrentRequest`.

|Parameter|BESCHREIBUNG|Standard|
| -- | -- | -- |
| `enabled` | Gibt an, ob der **Sprachsynthese**-Dienst aktiviert ist. | `false` |
| `numberOfConcurrentRequest` | Die Anzahl gleichzeitiger Anforderungen für den **Sprachsynthese**-Dienst. In diesem Diagramm werden automatisch CPU- und Speicherressourcen basierend auf diesem Wert berechnet. | `2` |
| `optimizeForTurboMode`| Gibt an, ob der Dienst für die Texteingabe über Textdateien optimiert werden muss. Wenn `true`, werden in diesem Diagramm dem Dienst mehr CPU-Ressourcen zugeordnet. | `false` |
| `image.registry`| Die Docker-Imageregistrierung für **Sprachsynthese**. | `containerpreview.azurecr.io` |
| `image.repository` | Das Docker-Imagerepository für **Sprachsynthese**. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Das Docker-Imagetag für **Sprachsynthese**. | `latest` |
| `image.pullSecrets` | Die Imagegeheimnisse zum Abrufen des Docker-Images für **Sprachsynthese**. | |
| `image.pullByHash`| Gibt an, ob das Docker-Image mithilfe von Hashing abgerufen wird. Wenn `true`, ist `image.hash` erforderlich. | `false` |
| `image.hash`| Das Docker-Imagehash für **Sprachsynthese**. Nur verwendet, wenn `image.pullByHash: true`.  | |
| `image.args.eula` (erforderlich) | Gibt an, dass Sie die Lizenz akzeptiert haben. Der einzige gültige Wert ist `accept`. | |
| `image.args.billing` (erforderlich) | Den URI des Abrechnungsendpunkts finden Sie im Azure-Portal auf der Speech-Übersichtsseite. | |
| `image.args.apikey` (erforderlich) | Wird zum Nachverfolgen von Abrechnungsinformationen verwendet. ||
| `service.type` | Die Kubernetes-Diensttyp des **Sprachsynthese**-Diensts. Weitere Informationen finden Sie unter den [Kubernetes-Anweisungen für Diensttypen ](https://kubernetes.io/docs/concepts/services-networking/service/) und beim Support des Cloudanbieters. | `LoadBalancer` |
| `service.port`|  Der Port des **Sprachsynthese**-Diensts. | `80` |
| `service.annotations` | Die **Sprachsynthese**-Anmerkungen für die Dienstmetadaten. Anmerkungen sind Schlüssel-Wert-Paare. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Gibt an, ob die [horizontale automatische Podskalierung](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) aktiviert ist. Wenn `true`, wird `text-to-speech-autoscaler` im Kubernetes-Cluster bereitgestellt. | `true` |
| `service.podDisruption.enabled` | Gibt an, ob das [Budget für die Unterbrechung von Pods](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) aktiviert ist. Wenn `true`, wird `text-to-speech-poddisruptionbudget` im Kubernetes-Cluster bereitgestellt. | `true` |