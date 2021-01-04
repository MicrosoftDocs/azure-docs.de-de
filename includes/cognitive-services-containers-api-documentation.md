---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996421"
---
## <a name="validate-that-a-container-is-running"></a>Überprüfen auf aktive Container 

Es gibt mehrere Möglichkeiten zu überprüfen, ob ein Container aktiv ist. Suchen Sie die *externe IP*-Adresse und den verfügbar gemachten Port des betreffenden Containers, und öffnen Sie Ihren bevorzugten Webbrowser. Verwenden Sie die verschiedenen URLs der untenstehenden Anforderungen, um zu überprüfen, ob der Container ausgeführt wird. Die unten aufgeführten Beispiel-URLs für Anforderungen sind `http://localhost:5000`, aber Ihr spezifischer Container kann variieren. Denken Sie daran, dass Sie sich auf die *externe IP-Adresse* Ihres Containers und den verfügbar gemachten Port verlassen müssen.

| Anfrage-URL | Zweck |
|--|--|
| `http://localhost:5000/` | Der Container stellt eine Homepage bereit. |
| `http://localhost:5000/ready` | Dies ermöglicht eine Überprüfung mit GET-Anforderung, dass der Container bereit ist, eine Abfrage des Modells zu akzeptieren.  Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden. |
| `http://localhost:5000/status` | Dies ermöglicht auch eine Überprüfung mit GET-Anforderung, dass der zum Starten des Containers verwendete API-Schlüssel gültig ist, ohne dass eine Endpunktabfrage veranlasst wird. Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden. |
| `http://localhost:5000/swagger` | Der Container stellt eine umfassende Dokumentation für die Endpunkte sowie die Funktion **Jetzt ausprobieren** bereit. Diese Funktion ermöglicht Ihnen die Eingabe Ihrer Einstellungen in einem webbasierten HTML-Formular, sodass Sie die Abfrage ausführen können, ohne Code schreiben zu müssen. Nach der Rückgabe der Abfrage wird ein cURL-Beispielbefehl bereitgestellt, der das erforderliche Format für HTTP-Header und -Text veranschaulicht. |

![Homepage des Containers](./media/cognitive-services-containers-api-documentation/container-webpage.png)
