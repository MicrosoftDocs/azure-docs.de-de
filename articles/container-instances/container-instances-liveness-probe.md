---
title: Konfigurieren von Livetests in Azure Container Instances
description: Erfahren Sie, wie Sie Livetests zu Neustart von fehlerhaften Containern in Azure Container Instances konfigurieren.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 89b76fc68c113b7931894c0cf003ffd846c646ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60583836"
---
# <a name="configure-liveness-probes"></a>Konfigurieren von Livetests

Containerisierte Anwendungen können über einen längeren Zeitraum laufen, was zu fehlerhaften Zuständen führen kann, die durch einen Neustart des Containers repariert werden müssen. Azure Container Instances unterstützt Livetests, um Konfigurationen einzubinden, sodass Ihr Container neu gestartet werden kann, wenn kritische Funktionen nicht ausgeführt werden.

Dieser Artikel erläutert die Bereitstellung einer Containergruppe mit integriertem Livetest, um den automatischen Neustart eines simulierten fehlerhaften Containers zu demonstrieren.

## <a name="yaml-deployment"></a>YAML-Bereitstellung

Erstellen Sie eine `liveness-probe.yaml`-Datei mit dem folgenden Ausschnitt. Diese Datei definiert eine Containergruppe, die einen NGNIX-Container enthält, der letztendlich fehlerhaft ist.

```yaml
apiVersion: 2018-06-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Führen Sie den folgenden Befehl aus, um diese Containergruppe mit der oben aufgeführten YAML-Konfiguration bereitzustellen:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Startbefehl

Die Bereitstellung definiert einen Startbefehl, der ausgeführt wird, wenn der Container zum ersten Mal läuft, definiert durch die `command`-Eigenschaft, die ein Zeichenfolgenarray akzeptiert. In diesem Beispiel wird eine Bash-Sitzung gestartet und eine Datei mit der Bezeichnung `healthy` im `/tmp`-Verzeichnis erstellt, indem dieser Befehl weitergegeben wird:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Die Bereitstellung wechselt vor dem Löschen der Datei in einen 30-sekündigen Ruhezustand, und geht anschließend in einen 10-minütige Ruhezustand über.

### <a name="liveness-command"></a>Livetestbefehl

Diese Bereitstellung definiert ein `livenessProbe`-Element, das einen `exec`-Livetestbefehl unterstützt, der als Livetestüberprüfung agiert. Wenn dieser Befehl mit einem Wert ungleich Null beendet wird, wird der Container beendet und neu gestartet, was bedeutet, dass die `healthy`-Datei nicht gefunden werden konnte. Wenn dieser Befehl erfolgreich mit Exitcode 0 beendet wird, wird keine Aktion ausgeführt.

Die `periodSeconds`-Eigenschaft gibt an, dass der Livetestbefehl alle 5 Sekunden ausgeführt werden soll.

## <a name="verify-liveness-output"></a>Überprüfen der Livetestausgabe

Innerhalb der ersten 30 Sekunden wird die vom Startbefehl erstellte `healthy`-Datei beendet. Wenn der Livetestbefehl das Vorhandensein der `healthy`-Datei überprüft, gibt der Statuscode einen Nullwert zurück. Dies zeigt einen Erfolg an, sodass kein Neustart ausgeführt wird.

Nach 30 Sekunden tritt ein Fehler bei `cat /tmp/healthy` auf, und es kommt zu Fehler- und Beendigungsereignissen.

Diese Ereignisse können über das Azure-Portal oder Azure CLI angezeigt werden.

![Fehlerhaftes Ereignis im Portal][portal-unhealthy]

Durch Anzeigen der Ereignisse im Azure-Portal werden bei einem Fehler beim Livetestbefehl Ereignisse vom Typ `Unhealthy` ausgelöst. Das nachfolgende Ereignis ist vom Typ`Killing`. Damit wird die Löschung eines Containers angezeigt, damit ein Neustart beginnen kann. Der Neustartzähler für den Container wird jedes Mal erhöht, wenn dies geschieht.

Neustarts werden vor Ort durchgeführt, sodass Ressourcen wie öffentliche IP-Adressen und knotenspezifische Inhalte erhalten bleiben.

![Neustartzähler im Portal][portal-restart]

Wenn im Livetest weiterhin Fehler auftreten, wodurch zu viele Neustarts ausgelöst werden, geht Ihr Container in eine exponentielle Backoff-Verzögerung über.

## <a name="liveness-probes-and-restart-policies"></a>Livetests und Neustartrichtlinien

Neustartrichtlinien ersetzen das Neustartverhalten, das von Livetests ausgelöst wird. Wenn Sie beispielsweise eine `restartPolicy = Never`  *und* einen Livetest einrichten, wird die Containergruppe bei einer fehlerhaften Livetestüberprüfung nicht neu gestartet. Für die Containergruppe gilt stattdessen die Neustartrichtlinie von `Never` der Containergruppe.

## <a name="next-steps"></a>Nächste Schritte

Task-basierte Szenarios erfordern möglicherweise einen Livetest zur Aktivierung automatischer Neustarts, wenn eine vorausgesetzte Funktion nicht richtig funktioniert. Weitere Informationen zum Ausführen aufgabenbasierter Containern finden Sie unter [Ausführen von Aufgaben in Containern in Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
