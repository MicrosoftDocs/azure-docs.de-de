---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178365"
---
## <a name="validate-that-a-container-is-running"></a>Überprüfen auf aktive Container 

Es gibt mehrere Möglichkeiten zu überprüfen, ob ein Container aktiv ist. 

|Anforderung|Zweck|
|--|--|
|`http://localhost:5000/`|Der Container stellt eine Homepage bereit.|
|`http://localhost:5000/status`|Anforderung mit GET, um zu überprüfen, ob der Container aktiv ist, ohne eine Endpunktabfrage zu verursachen. Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden.|
|`http://localhost:5000/swagger`|Der Container stellt eine umfassende Dokumentation für die Endpunkte sowie die Funktion `Try it now` bereit. Diese Funktion ermöglicht Ihnen die Eingabe Ihrer Einstellungen in einem webbasierten HTML-Formular, sodass Sie die Abfrage ausführen können, ohne Code schreiben zu müssen. Nach der Rückgabe der Abfrage wird ein cURL-Beispielbefehl bereitgestellt, der das erforderliche Format für HTTP-Header und -Text veranschaulicht. |

![Homepage des Containers](./media/cognitive-services-containers-api-documentation/container-webpage.png)
