---
title: Überprüfen der Integritätscontainerinstanz
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Integritätscontainerinstanz überprüfen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009880"
---
### <a name="verify-that-a-container-is-running"></a>Überprüfen auf aktive Container

1. Wählen Sie die Registerkarte **Übersicht** aus, und kopieren Sie die IP-Adresse.
1. Öffnen Sie eine neue Registerkarte im Browser, und geben Sie die IP-Adresse ein. Geben Sie zum Beispiel `http://<IP-address>:5000 (http://55.55.55.55:5000`) ein. Die Homepage des Containers wird angezeigt, in der Sie erkennen, dass der Container ausgeführt wird.

    ![Anzeigen der Homepage des Containers, um sicherzustellen, dass er ausgeführt wird](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Klicken Sie auf den Link **Dienst-API-Beschreibung**, um die Swagger-Seite des Containers aufzurufen.

1. Wählen Sie eine der **POST**-APIs und dann **Jetzt ausprobieren** aus. Die Parameter einschließlich der folgenden Beispieleingabe werden angezeigt.

Es gibt mehrere URLs, mit denen Sie auch überprüfen können, ob der Container ausgeführt wird.

|Anforderung|Zweck|
|--|--|
|`http://localhost:5000/`|Der Container stellt eine Homepage bereit.|
|`http://localhost:5000/ready`|Dies ermöglicht eine Überprüfung mit GET-Anforderung, dass der Container bereit ist, eine Abfrage des Modells zu akzeptieren. Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden.|
|`http://localhost:5000/status`|Dies ermöglicht eine Überprüfung mit GET-Anforderung, wie z. B. mithilfe des /ready-Endpunkts, dass der Container ausgeführt wird, ohne dass eine Abfrage des Modells erforderlich ist. Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden.|
|`http://localhost:5000/swagger`|Über diese URL stellt der Container eine umfassende Dokumentation für die Endpunkte sowie die Funktion `Try it now` bereit. Diese Funktion ermöglicht Ihnen die Eingabe Ihrer Einstellungen in einem webbasierten HTML-Formular, sodass Sie die Abfrage ausführen können, ohne Code schreiben zu müssen. Nach der Rückgabe der Abfrage wird ein cURL-Beispielbefehl bereitgestellt, der das erforderliche Format für HTTP-Header und -Text veranschaulicht. |
|`http://localhost:5000/demo`| Bei Anforderung über einen Browser bietet diese Funktion eine interaktive Visualisierung der Ergebnisse aus Abfragen von Eingabetextbeispielen oder eines von Ihnen bereitgestellten Beispiels.  |

Verwenden Sie diese Anforderungs-URL (`http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`), um eine Abfrage an den Container zu übermitteln.
