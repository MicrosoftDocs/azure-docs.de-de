---
title: Abrufen von Containerinstanzprotokollen und -ereignissen
description: Erfahren Sie, wie Sie Containerprotokolle und -ereignisse in Azure Container Instances abrufen, um die Behandlung von Containerproblemen zu unterstützen.
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20b6e1cfe6bb8f6ac721a401c3d0831d4f447edb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746965"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Abrufen von Containerprotokollen und -ereignissen in Azure Container Instances

Wenn Sie einen fehlerhaften Container in Azure Container Instances haben, können Sie zunächst mit [az container logs][az-container-logs] die Protokolle anzeigen sowie die Standardausgabe und Standardfehler mit [az container attach][az-container-attach] streamen. Sie können auch Protokolle und Ereignisse für Containerinstanzen im Azure-Portal anzeigen oder Protokoll- und Ereignisdaten für Containergruppen an [Azure Monitor-Protokolle](container-instances-log-analytics.md) senden.

## <a name="view-logs"></a>Anzeigen von Protokollen

Sie können den Befehl [az container logs][az-container-logs] verwenden, um Protokolle aus Ihrem Anwendungscode in einem Container anzuzeigen.

Im Folgenden finden Sie die Protokollausgabe des aufgabenbasierten Beispielcontainers aus [Festlegen der Befehlszeile in einer Containerinstanz](container-instances-start-command.md#azure-cli-example), nachdem eine ungültige URL mithilfe der Außerkraftsetzung einer Befehlszeilen bereitgestellt wurde:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Anfügen von Ausgabestreams

Der Befehl [az container attach][az-container-attach] liefert Diagnoseinformationen während des Containerstarts. Nachdem der Container gestartet wurde, werden STDOUT und STDERR auf Ihre lokale Konsole gestreamt.

Hier ist beispielsweise die Ausgabe aus dem aufgabenbasierten Container aus [Festlegen der Befehlszeile in einer Containerinstanz](container-instances-start-command.md#azure-cli-example), nachdem eine gültige URL einer großen zu verarbeitenden Textdatei bereitgestellt wurde:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Abrufen von Diagnoseereignissen

Wenn die Bereitstellung Ihres Containers nicht erfolgreich ist, überprüfen Sie die Diagnoseinformationen des Azure Container Instances-Ressourcenanbieters. Führen Sie zum Anzeigen der Ereignisse für Ihren Container den Befehl [az container show][az-container-show] aus:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Die Ausgabe enthält die wichtigsten Eigenschaften Ihres Containers und die Bereitstellungsereignisse (hier verkürzt dargestellt):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über das [Beheben von Container- und Bereitstellungsproblemen](container-instances-troubleshooting.md) für Azure Container Instances.

Erfahren Sie, wie Sie Protokoll- und Ereignisdaten für Containergruppen an [Azure Monitor-Protokolle](container-instances-log-analytics.md) senden.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
