---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116717"
---
Die `Logging`-Einstellungen dienen zur Verwaltung der ASP.NET Core-Protokollierungsunterstützung für Ihren Container. Sie können für Ihren Container die gleichen Konfigurationseinstellungen und Werte verwenden wie für eine ASP.NET Core-Anwendung. 

Der Container unterstützt folgende Protokollanbieter:

|Anbieter|Zweck|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Der ASP.NET Core-Protokollierungsanbieter `Console`. Alle ASP.NET Core-Konfigurationseinstellungen und Standardwerte für diesen Protokollanbieter werden unterstützt.|
|[Debuggen](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Der ASP.NET Core-Protokollierungsanbieter `Debug`. Alle ASP.NET Core-Konfigurationseinstellungen und Standardwerte für diesen Protokollanbieter werden unterstützt.|
|[Datenträger](#disk-logging)|Der JSON-Protokollanbieter. Dieser Protokollanbieter schreibt Protokolldaten in die Ausgabeeinbindung.|

Dieser Containerbefehl speichert Protokollierungsinformationen im JSON-Format für die Ausgabeeinbindung:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Dieser Containerbefehl zeigt Debuginformationen an, denen `dbug` vorangestellt ist, während der Container ausgeführt wird:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Datenträgerprotokollierung

Der Protokollanbieter `Disk` unterstützt folgende Konfigurationseinstellungen:  

| NAME | Datentyp | BESCHREIBUNG |
|------|-----------|-------------|
| `Format` | Zeichenfolge | Das Ausgabeformat für Protokolldateien.<br/> **Hinweis:** Dieser Wert muss auf `json` festgelegt werden, um den Protokollanbieter zu aktivieren. Wenn dieser Wert bei der Containerinstanziierung angegeben wird, ohne eine Ausgabeeinbindung anzugeben, tritt ein Fehler auf. |
| `MaxFileSize` | Integer | Die maximale Größe einer Protokolldatei (in MB). Wenn die Größe der aktuellen Protokolldatei diesen Wert erreicht oder übersteigt, wird vom Protokollanbieter eine neue Protokolldatei erstellt. Bei Angabe von „-1“ wird die Größe der Protokolldatei nur durch die maximal zulässige Dateigröße für die Ausgabeeinbindung begrenzt (sofern vorhanden). Der Standardwert ist 1. |

Weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierungsunterstützung finden Sie unter [Protokollierung in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

