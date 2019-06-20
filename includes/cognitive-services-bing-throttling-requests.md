---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178375"
---
Der Dienst und Ihr Abonnementtyp bestimmen die zulässige Anzahl von Abfragen pro Sekunde (Queries Per Second, QPS). Stellen Sie sicher, dass Ihre Anwendung die erforderliche Logik enthält, um das Kontingent nicht zu überschreiten. Wenn der QPS-Grenzwert erreicht oder überschritten wird, ist die Anforderung nicht erfolgreich, und es wird ein HTTP 429-Statuscode zurückgegeben. Die Antwort enthält den Header `Retry-After`, der angibt, wie lange bis zum Senden einer weiteren Anforderung gewartet werden muss.

## <a name="denial-of-service-versus-throttling"></a>Denial-of-Service im Vergleich zur Drosselung

Der Dienst unterscheidet zwischen einem DoS-Angriff (Denial-of-Service) und einem QPS-Verstoß. Wenn der Dienst einen DoS-Angriff vermutet, ist die Anforderung erfolgreich (HTTP-Statuscode: 200 OK). Der Antworttext ist jedoch leer.