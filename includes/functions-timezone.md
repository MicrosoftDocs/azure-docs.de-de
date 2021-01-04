---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027811"
---
Als Standardzeitzone wird in Verbindung mit den CRON-Ausdrücken die Coordinated Universal Time (UTC) verwendet. Wenn Sie möchten, dass Ihr CRON-Ausdruck auf einer anderen Zeitzone basiert, erstellen Sie eine App-Einstellung für die Funktionen-App mit dem Namen `WEBSITE_TIME_ZONE`. 

Der Wert dieser Einstellung hängt davon ab, unter welchem Betriebssystem und Plan Ihre Funktions-App ausgeführt wird.

|Betriebssystem |Plan |Wert |
|-|-|-|
| **Windows** |All | Legen Sie den Wert auf den Namen der gewünschten Zeitzone fest, wie in der zweiten Zeile des vom Windows-Befehl `tzutil.exe /L` ausgegebenen Wertepaars angegeben. |
| **Linux** |Premium<br/>Dediziert |Legen Sie den Wert auf den Namen der gewünschten Zeitzone fest (gemäß [tz-Datenbank](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` wird im Linux-Verbrauchsplan zurzeit nicht unterstützt.

Ein Beispiel: Für die Zeitzone „Eastern Time“ in den USA (dargestellt durch `Eastern Standard Time` unter Windows oder `America/New_York` unter Linux) wird derzeit UTC-05:00 in der Standardzeit und UTC-04:00 in der Sommerzeit verwendet. Damit ein Timertrigger jeden Tag um 10:00 vormittags in der Zeitzone „Eastern Time“ ausgelöst wird, erstellen Sie eine App-Einstellung für Ihre Funktions-App mit dem Namen `WEBSITE_TIME_ZONE`, legen den Wert auf `Eastern Standard Time` (Windows) bzw. `America/New_York` (Linux) fest, und verwenden dann den folgenden NCRONTAB-Ausdruck: 

```
"0 0 10 * * *"
``` 

Wenn Sie `WEBSITE_TIME_ZONE` verwenden, wird die Uhrzeit an Abweichungen in der jeweiligen Zeitzone angepasst, einschließlich Sommerzeit und Änderungen an der Standardzeit.
