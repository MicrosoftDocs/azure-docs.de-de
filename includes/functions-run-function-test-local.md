---
title: Includedatei
description: Includedatei
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132181"
---
## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Führen Sie den folgenden Befehl aus, um die Funktions-App zu starten. Die App wird mit der gleichen Azure Functions-Runtime ausgeführt, die in Azure vorliegt.

```bash
func host start --build
```

Die `--build`-Option ist erforderlich, um C#-Projekte zu kompilieren. Für ein JavaScript-Projekt benötigen Sie diese Option nicht.

Wenn der Functions-Host startet, schreibt er eine Ausgabe ähnlich der folgenden, die zur besseren Lesbarkeit beschnitten wurde:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Kopieren Sie die URL Ihrer `HttpTrigger`-Funktion aus der Runtimeausgabe, und fügen Sie sie in die Adressleiste Ihres Browsers ein. Hängen Sie anschließend die Abfragezeichenfolge `?name=<yourname>` an diese URL an, und führen Sie die Anforderung aus. Hier ist die Antwort des Browsers auf die von der lokalen Funktion zurückgegebenen GET-Anforderung abgebildet:

![Lokales Testen im Browser](./media/functions-run-function-test-local/functions-test-local-browser.png)

Jetzt haben Sie die Funktion lokal ausgeführt und können die Funktions-App und andere erforderliche Ressourcen in Azure erstellen.