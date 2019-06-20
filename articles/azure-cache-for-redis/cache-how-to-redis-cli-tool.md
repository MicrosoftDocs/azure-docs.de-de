---
title: Verwenden von „redis-cli.exe“ mit Azure Cache for Redis | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie „redis-cli.exe“ mit Azure Cache for Redis verwenden.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: yegu
ms.openlocfilehash: 318d02f5da816ae8fe2fe199b9c87b3748d5d1fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133013"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Verwenden des Redis-Befehlszeilentools mit Azure Cache for Redis

*redis-cli.exe* ist ein gängiges Befehlszeilentool für die Interaktion mit einer Azure Cache for Redis-Instanz als Client. Dieses Tool ist auch für die Verwendung mit Azure Cache for Redis verfügbar.

Um das Tool auf Windows-Plattformen zu nutzen, laden Sie die [Redis-Befehlszeilentools für Windows](https://github.com/MSOpenTech/redis/releases/) herunter. 

Wenn Sie das Befehlszeilentool auf einer anderen Plattform ausführen möchten, laden Sie Azure Cache for Redis von [https://redis.io/download](https://redis.io/download) herunter.

## <a name="gather-cache-access-information"></a>Sammeln von Cachezugriffsinformationen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können die benötigten Informationen zum Zugriff auf den Cache mithilfe von drei Methoden sammeln:

1. Über Azure CLI mit [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell mithilfe von [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Über das Azure-Portal.

In diesem Abschnitt rufen Sie die Schlüssel aus dem Azure-Portal ab.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Aktivieren des Zugriffs für „redis-cli.exe“

Mit Azure Cache for Redis ist nur der SSL-Port (6380) standardmäßig aktiviert. Das `redis-cli.exe`-Befehlszeilentool unterstützt SSL nicht. Sie können zu seiner Verwendung zwischen zwei Konfigurationsoptionen wählen:

1. [Nicht-SSL-Port aktivieren (6379)](cache-configure.md#access-ports) - **Diese Konfiguration wird nicht empfohlen**, da in dieser Konfiguration die Zugriffsschlüssel über TCP in Klartext gesendet werden. Diese Änderung kann den Zugriff auf Ihren Cache beeinträchtigen. Das einzige Szenario, in dem Sie diese Konfiguration berücksichtigen könnten, ist der Zugriff auf einen Testcache.

2. Laden Sie [stunnel](https://www.stunnel.org/downloads.html) herunter, und installieren Sie die Anwendung.

    Führen Sie **stunnel GUI Start** zum Starten des Servers aus.

    Klicken Sie mit der rechten Maustaste auf das Taskleistensymbol für den stunnel-Server, und klicken Sie auf **Show Log Window (Protokollfenster anzeigen)** .

    Klicken Sie im stunnel-Menü „Log Window“ (Protokollfenster) auf **Configuration (Konfiguration)**  > **Edit Configuration (Konfiguration bearbeiten)** , um die aktuelle Konfigurationsdatei zu öffnen.

    Fügen Sie den folgenden Eintrag für *redis-cli.exe* im Abschnitt **Service definitions (Dienstdefinitionen)** hinzu. Fügen Sie Ihren tatsächlichen Cachename anstelle von `yourcachename` ein. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Speichern und schließen Sie die Konfigurationsdatei. 
  
    Klicken Sie im stunnel-Menü „Log Window“ (Protokollfenster) auf **Configuration (Konfiguration)**  > **Reload Configuration (Konfiguration erneut laden)** .


## <a name="connect-using-the-redis-command-line-tool"></a>Stellen Sie mit dem Redis-Befehlszeilentool eine Verbindung her.

Führen Sie bei Verwendung von stunnel *redis-cli.exe* aus, und übergeben Sie nur Ihren *Port* und *Zugriffsschlüssel* (primär oder sekundär) für die Herstellung der Verbindung mit dem Cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel mit „redis-cli.exe“](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Bei Verwendung eines Testcaches mit dem **unsicheren** Nicht-SSL-Port führen Sie `redis-cli.exe` aus, und übergeben Sie Ihren *Hostnamen*, *Port* und *Zugriffsschlüssel* (primär oder sekundär) für die Herstellung der Verbindung mit dem Testcache.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel mit „redis-cli.exe“](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung der [Redis-Konsole](cache-configure.md#redis-console) zur Befehlsausgabe.

