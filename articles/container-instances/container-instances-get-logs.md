---
title: Abrufen von Containerprotokollen und -ereignissen mit Azure Container Instances
description: Hier erfahren Sie, wie Sie beim Debuggen Containerprotokolle und -ereignisse mit Azure Container Instances verwenden.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f286e2136b12a88e65e40f8fb956542233f71715
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60579781"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Abrufen von Containerprotokollen und -ereignissen in Azure Container Instances

Wenn Sie über einen fehlerhaften Container verfügen, können Sie zunächst mit [az container logs][az-container-logs] die Protokolle anzeigen und die Standardausgabe und Standardfehler mit [az container attach][az-container-attach] streamen.

## <a name="view-logs"></a>Anzeigen von Protokollen

Sie können den Befehl [az container logs][az-container-logs] verwenden, um Protokolle aus Ihrem Anwendungscode in einem Container anzuzeigen.

Hier ist die Protokollausgabe des aufgabenbasierten Beispielcontainers aus [Ausführen von Aufgaben in Containern in Azure Container Instances](container-instances-restart-policy.md) angegeben, die sich ergibt, nachdem eine ungültige URL zur Verarbeitung bereitgestellt wurde:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
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

Mit dem Befehl [az container attach][az-container-attach] können Sie während des Containerstartvorgangs Diagnoseinformationen bereitstellen. Nachdem der Container gestartet wurde, werden STDOUT und STDERR auf Ihre lokale Konsole gestreamt.

Hier ist beispielsweise die Ausgabe aus dem aufgabenbasierten Container unter [Ausführen von Aufgaben in Containern in Azure Container Instances](container-instances-restart-policy.md) angegeben, die angezeigt wird, nachdem eine gültige URL einer großen zu verarbeitenden Textdatei bereitgestellt wurde:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
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

Wenn die Bereitstellung Ihres Containers nicht erfolgreich ist, sollten Sie die Diagnoseinformationen des Azure Container Instances-Ressourcenanbieters prüfen. Führen Sie zum Anzeigen der Ereignisse für Ihren Container den Befehl [az container show][az-container-show] aus:

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

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
