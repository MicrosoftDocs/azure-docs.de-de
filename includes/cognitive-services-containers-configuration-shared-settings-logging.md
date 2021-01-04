---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2bfb700ac5c220b780c05c8d415a4506c7a2f871
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001167"
---
Die `Logging`-Einstellungen dienen zur Verwaltung der ASP.NET Core-Protokollierungsunterstützung für Ihren Container. Sie können für Ihren Container die gleichen Konfigurationseinstellungen und Werte verwenden wie für eine ASP.NET Core-Anwendung. 

Der Container unterstützt folgende Protokollanbieter:

|Anbieter|Zweck|
|--|--|
|[Console](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Der ASP.NET Core-Protokollierungsanbieter `Console`. Alle ASP.NET Core-Konfigurationseinstellungen und Standardwerte für diesen Protokollanbieter werden unterstützt.|
|[Debuggen](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Der ASP.NET Core-Protokollierungsanbieter `Debug`. Alle ASP.NET Core-Konfigurationseinstellungen und Standardwerte für diesen Protokollanbieter werden unterstützt.|
|[Datenträger](#disk-logging)|Der JSON-Protokollanbieter. Dieser Protokollanbieter schreibt Protokolldaten in die Ausgabeeinbindung.|

Dieser Containerbefehl speichert Protokollierungsinformationen im JSON-Format für die Ausgabeeinbindung:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Dieser Containerbefehl zeigt Debuginformationen an, denen `dbug` vorangestellt ist, während der Container ausgeführt wird:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Datenträgerprotokollierung

Der Protokollanbieter `Disk` unterstützt folgende Konfigurationseinstellungen:

| Name | Datentyp | BESCHREIBUNG |
|------|-----------|-------------|
| `Format` | String | Das Ausgabeformat für Protokolldateien.<br/> **Hinweis:** Dieser Wert muss auf `json` festgelegt werden, um den Protokollanbieter zu aktivieren. Wenn dieser Wert bei der Containerinstanziierung angegeben wird, ohne eine Ausgabeeinbindung anzugeben, tritt ein Fehler auf. |
| `MaxFileSize` | Integer | Die maximale Größe einer Protokolldatei (in MB). Wenn die Größe der aktuellen Protokolldatei diesen Wert erreicht oder übersteigt, wird vom Protokollanbieter eine neue Protokolldatei erstellt. Bei Angabe von „-1“ wird die Größe der Protokolldatei nur durch die maximal zulässige Dateigröße für die Ausgabeeinbindung begrenzt (sofern vorhanden). Der Standardwert ist 1. |

Weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierungsunterstützung finden Sie unter [Protokollierung in ASP.NET Core](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).