---
title: SSH-Zugriff auf Linux-Container
description: Sie können eine SSH-Sitzung mit einem Linux-Container in Azure App Service öffnen. Benutzerdefinierte Linux-Container werden mit einigen Anpassungen an Ihrem benutzerdefinierten Image unterstützt.
keywords: Azure App Service, Web-App, Linux, OSS
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7178b8079bbb2411e0b38c3ef59a9981fb2d55be
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005238"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Öffnen einer SSH-Sitzung mit einem Linux-Container in Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) wird häufig für die Remoteausführung von Verwaltungsbefehlen über ein Befehlszeilenterminal verwendet. App Service für Linux bietet SSH-Unterstützung für den App-Container. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

Sie können auch direkt von Ihrem lokalen Entwicklungscomputer per SSH und SFTP eine Verbindung herstellen.

## <a name="open-ssh-session-in-browser"></a>Öffnen einer SSH-Sitzung im Browser

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Verwenden der SSH-Unterstützung mit benutzerdefinierten Docker-Images

Siehe [Konfigurieren von SSH in einem benutzerdefinierten Container](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Öffnen einer SSH-Sitzung per Remote-Shell

> [!NOTE]
> Diese Funktion steht derzeit als Vorschau zur Verfügung.
>

Mithilfe von TCP-Tunneln können Sie eine Netzwerkverbindung zwischen Ihrem Entwicklungscomputer und Web-App für Container herstellen, indem Sie eine authentifizierte WebSocket-Verbindung verwenden. Auf diese Weise können Sie eine SSH-Sitzung mit Ihrem Container, der unter App Service ausgeführt wird, auf dem Client Ihrer Wahl öffnen.

Zunächst müssen Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) (Azure CLI) installieren. Öffnen Sie [Azure Cloud Shell](../cloud-shell/overview.md), wenn Sie sich die Vorgehensweise ohne Installation der Azure CLI ansehen möchten. 

Öffnen Sie eine Remoteverbindung mit Ihrer App, indem Sie den Befehl [az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) verwenden. Geben Sie _\<subscription-id>_ , _\<group-name>_ und \_\<app-name>_ für Ihre App an.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` am Ende des Befehls ist der Vollständigkeit halber angegeben, falls Sie Cloud Shell verwenden. Hierbei wird der Prozess im Hintergrund ausgeführt, damit Sie den nächsten Befehl in derselben Shell ausführen können.

Mit der Befehlsausgabe erhalten Sie die Informationen, die Sie zum Öffnen einer SSH-Sitzung benötigen.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Öffnen Sie eine SSH-Sitzung mit Ihrem Container mit dem Client Ihrer Wahl, indem Sie den lokalen Port verwenden. Im folgenden Beispiel wird der Standardbefehl [ssh](https://ss64.com/bash/ssh.html) verwendet:

```bash
ssh root@127.0.0.1 -p <port>
```

Geben Sie nach Aufforderung `yes` ein, um mit der Verbindungsherstellung fortzufahren. Sie werden dann aufgefordert, das Kennwort einzugeben. Verwenden Sie `Docker!`, wie oben gezeigt.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

Nachdem Sie authentifiziert wurden, sollte der Begrüßungsbildschirm der Sitzung angezeigt werden.

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

Sie sind jetzt mit Ihrem Connector verbunden.  

Versuchen Sie, den Befehl [top](https://ss64.com/bash/top.html) auszuführen. Sie sollten den Prozess Ihrer App in der Prozessliste verfolgen können. In der folgenden Beispielausgabe ist dies der Prozess mit `PID 263`.

<pre>
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
</pre>

## <a name="next-steps"></a>Nächste Schritte

Im [Azure-Forum](/answers/topics/azure-webapps.html) können Sie Fragen stellen und Antworten auf Probleme erhalten.

Weitere Informationen zu Web-App für Container finden Sie in den folgenden Artikeln:

* [Introducing remote debugging of Node.js apps on Azure App Service from VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) (Einführung in das Remotedebuggen von Node.js-Apps unter Azure App Service über VS Code)
* [Schnellstart: Ausführen eines benutzerdefinierten Containers in App Service](quickstart-custom-container.md?pivots=container-linux)
* [Verwenden von Ruby in Azure App Service unter Linux](quickstart-ruby.md)
* [Azure App Service-Web-App für Container – FAQs](faq-app-service-linux.md)
