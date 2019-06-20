---
title: Problembehandlung für Azure Container Instances
description: Es wird beschrieben, wie Sie Probleme mit Azure Container Instances beheben.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 9dc3e19f9429a6055a799f3f013c732538fa370d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65070867"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Beheben von häufigen Problemen in Azure Container Instances

In diesem Artikel wird veranschaulicht, wie Sie häufige Probleme beim Verwalten oder Bereitstellen von Containern in Azure Container Instances behandeln. Weitere Informationen finden Sie in den [häufig gestellten Fragen](container-instances-faq.md).

## <a name="naming-conventions"></a>Benennungskonventionen

Wenn Sie Ihre Containerspezifikation definieren, erfordern bestimmte Parameter die Einhaltung von Benennungseinschränkungen. Nachfolgend sehen Sie eine Tabelle mit bestimmten Anforderungen für Containergruppeneigenschaften. Weitere Informationen zu Benennungskonventionen für Azure finden Sie unter [Benennungskonventionen][azure-name-restrictions] Im Azure Architecture Center.

| `Scope` | Länge | Schreibweise | Gültige Zeichen | Vorgeschlagenes Muster | Beispiel |
| --- | --- | --- | --- | --- | --- |
| Containergruppenname | 1-64 |Groß-/Kleinschreibung nicht beachten |Alphanumerisch und Bindestrich (beliebig), außer das erste oder letzte Zeichen |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Containername | 1-64 |Groß-/Kleinschreibung nicht beachten |Alphanumerisch und Bindestrich (beliebig), außer das erste oder letzte Zeichen |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Containerports | Zwischen 1 und 65535 |Integer |Eine ganze Zahl zwischen 1 und 65535 |`<port-number>` |`443` |
| DNS-Namensbezeichnung | 5–63 |Groß-/Kleinschreibung nicht beachten |Alphanumerisch und Bindestrich (beliebig), außer das erste oder letzte Zeichen |`<name>` |`frontend-site1` |
| Umgebungsvariable | 1 - 63 |Groß-/Kleinschreibung nicht beachten |Alphanumerisch und Unterstrich (beliebig), außer das erste oder letzte Zeichen |`<name>` |`MY_VARIABLE` |
| Volumename | 5–63 |Groß-/Kleinschreibung nicht beachten |Kleinbuchstaben, Zahlen und Bindestriche (beliebig), außer das erste oder letzte Zeichen. Zwei aufeinanderfolgende Bindestriche sind nicht erlaubt. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Betriebssystemversion des Images wird nicht unterstützt

Wenn Sie ein Image angeben, das von Azure Container Instances nicht unterstützt wird, wird der Fehler `OsVersionNotSupported` zurückgegeben. Der Fehler ähnelt dem folgenden, wobei `{0}` der Name des Images ist, das Sie bereitstellen wollten:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Dieser Fehler tritt am häufigsten bei der Bereitstellung von Windows-Images auf, die auf dem SAC-Release (Semi-Annual Channel, halbjährlicher Kanal) 1709 oder 1803 basieren. Informationen zu unterstützten Windows-Images in Azure Container Instances finden Sie in den [häufig gestellten Fragen](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="unable-to-pull-image"></a>Pullvorgang für Image nicht möglich

Wenn Azure Container Instances den anfänglichen Pullvorgang für Ihr Image nicht durchführen kann, werden eine Zeit lang erneute Versuche gestartet. Wenn während des Pullvorgangs für das Image weiterhin ein Fehler auftritt, tritt auch während der Bereitstellung in ACI schließlich ein Fehler auf, und Sie sehen möglicherweise einen `Failed to pull image`-Fehler.

Um dieses Problem zu beheben, löschen Sie die Containerinstanz, und wiederholen Sie die Bereitstellung. Stellen Sie sicher, dass das Bild in der Registrierung vorhanden ist und Sie den Imagenamen richtig eingegeben haben.

Wenn das Image nicht abgerufen werden kann, werden in der Ausgabe von [az container show][az-container-show] beispielsweise folgende Ereignisse angezeigt:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Container wird fortlaufend beendet und neu gestartet (kein Vorgang mit langer Ausführungsdauer)

Bei Containergruppen ist standardmäßig eine [Neustartrichtlinie](container-instances-restart-policy.md) mit der Einstellung **Immer** festgelegt, sodass Container in der Containergruppe immer neu gestartet werden, nachdem sie bis zum Abschluss ausgeführt wurden. Wenn Sie taskbasierte Container ausführen möchten, müssen Sie diese Option eventuell in **OnFailure** oder **Never** ändern. Wenn Sie **OnFailure** festlegen und der Container weiterhin neu gestartet wird, liegt unter Umständen ein Problem mit der Anwendung oder dem Skript vor, die bzw. das im Container ausgeführt wird.

Bei der Ausführung von Containergruppen ohne Prozesse mit langer Ausführungsdauer werden Images möglicherweise wiederholt beendet und neu gestartet, wie etwa bei Ubuntu oder Alpine. Das Herstellen einer Verbindung über [EXEC](container-instances-exec.md) funktioniert nicht, da der Container keinen Prozess aufweist, der diesen aktiv hält. Um dieses Problem zu beheben, schließen Sie einen Startbefehl wie den folgenden in Ihre Containergruppenbereitstellung ein, damit der Container weiterhin ausgeführt wird.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Die Container Instances-API und das Azure-Portal enthalten eine `restartCount`-Eigenschaft. Die Anzahl von Neustarts für einen Container können Sie mit dem Befehl [az container show][az-container-show] in der Azure CLI überprüfen. In der folgenden (gekürzten) Beispielausgabe können Sie die `restartCount`-Eigenschaft am Ende der Ausgabe sehen.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Von den meisten Containerimages für Linux-Distributionen wird eine Shell, z.B. Bash, als Standardbefehl festgelegt. Da eine Shell allein kein Dienst mit langer Ausführungsdauer ist, werden diese Container sofort beendet und landen in einer Neustartschleife, wenn sie mit der Standardneustartrichtlinie **Always** konfiguriert wurden.

## <a name="container-takes-a-long-time-to-start"></a>Start des Containers dauert sehr lange

Die folgenden beiden Hauptfaktoren tragen zur Dauer des Containerstartvorgangs in Azure Container Instances bei:

* [Imagegröße](#image-size)
* [Imagespeicherort](#image-location)

Für Windows-Images gelten noch [weitere Aspekte](#cached-images).

### <a name="image-size"></a>Imagegröße

Wenn das Starten Ihres Containers sehr lange dauert, aber nach einiger Zeit erfolgreich ist, sollten Sie sich zuerst die Größe des Containerimage ansehen. Da Azure Container Instances den Pullvorgang für Ihr Containerimage nach Bedarf durchführt, ist die Startdauer direkt von der Größe abhängig.

Sie können die Größe Ihres Containerimages mit dem Befehl `docker images` in der Docker CLI anzeigen:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Sie können die Imagegrößen klein halten, indem Sie sicherstellen, dass Ihr endgültiges Image keine Elemente enthält, die zur Laufzeit nicht erforderlich sind. Eine Möglichkeit ist die Verwendung von [mehrstufigen Builds][docker-multi-stage-builds]. Mit mehrstufigen Builds können Sie leicht sicherstellen, dass das endgültige Image nur die Artefakte enthält, die Sie für Ihre Anwendung benötigen – und keinen zusätzlichen Inhalt, der zur Erstellungszeit benötigt wurde.

### <a name="image-location"></a>Imagespeicherort

Eine weitere Möglichkeit, die Auswirkungen des Pullvorgangs für das Image auf die Startdauer Ihres Containers zu reduzieren, ist das Hosten des Containerimages in [Azure Container Registry](/azure/container-registry/) in derselben Region, in der Sie Containerinstanzen bereitstellen möchten. Auf diese Weise wird der Netzwerkpfad verkürzt, den das Containerimage zurücklegen muss, sodass sich die Downloadzeit erheblich reduziert.

### <a name="cached-images"></a>Zwischengespeicherte Images

Azure Container Instances verwendet einen Mechanismus für die Zwischenspeicherung, um die Dauer des Containerstartvorgangs für Images basierend auf allgemeinen [Windows-Basisimages](container-instances-faq.md#what-windows-base-os-images-are-supported) (u. a. `nanoserver:1809`, `servercore:ltsc2019` und `servercore:1809`) zu verkürzen. Häufig verwendete Linux-Images wie `ubuntu:1604` und `alpine:3.6` werden auch zwischengespeichert. Verwenden Sie die API [List Cached Images][list-cached-images], um eine aktuelle Liste der zwischengespeicherten Images und Tags zu erhalten.

> [!NOTE]
> Die Verwendung von auf Windows Server 2019 basierenden Images in Azure Container Instances befindet sich im Vorschaustadium.

### <a name="windows-containers-slow-network-readiness"></a>Windows-Container verlangsamen die Netzwerkbereitschaft

Bei der ersten Erstellung verfügen Windows-Container ggf. für bis zu 30 Sekunden (selten noch länger) über keine eingehende oder ausgehende Konnektivität. Wenn Ihre Containeranwendung eine Internetverbindung benötigt, fügen Sie eine Verzögerungs- und Wiederholungslogik hinzu, die 30 Sekunden für den Aufbau der Internetverbindung zulässt. Nach der erstmaligen Einrichtung sollten Containernetzwerke ordnungsgemäß fortgesetzt werden.

## <a name="resource-not-available-error"></a>Ressource nicht verfügbar

Aufgrund der unterschiedlichen Auslastung regionaler Ressourcen in Azure wird beim Versuch, eine Containerinstanz bereitzustellen, möglicherweise die folgende Fehlermeldung angezeigt:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Dieser Fehler gibt an, dass aufgrund starker Auslastung in der Region, in der die Bereitstellung durchgeführt werden soll, die für den Container angegebenen Ressourcen zum aktuellen Zeitpunkt nicht zugeordnet werden können. Führen Sie einen oder mehrere der folgenden Schritte aus, um das Problem zu beheben.

* Überprüfen Sie, ob Ihre Einstellungen für die Containerbereitstellung innerhalb der unter [Regionale Verfügbarkeit für Azure Container Instances](container-instances-region-availability.md) definierten Parameter liegen.
* Geben Sie niedrigere CPU- und Arbeitsspeichereinstellungen für den Container an.
* Führen Sie die Bereitstellung in einer anderen Azure-Region durch.
* Führen Sie die Bereitstellung zu einem späteren Zeitpunkt durch.

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Verbinden mit zugrundeliegender Docker-API zum Ausführen privilegierte Container nicht möglich

Azure Container Instances bietet keinen direkten Zugriff auf die zugrundeliegende Infrastruktur, die Containergruppen hostet. Dazu gehört der Zugriff auf die Docker-API, die auf dem Containerhost läuft, und die Ausführung von privilegierten Containern. Wenn Sie Docker-Interaktion benötigen, lesen Sie die [REST-Referenzdokumentation](https://aka.ms/aci/rest), um zu sehen, was die ACI-API unterstützt. Wenn Sie etwas nicht finden, senden Sie eine Anforderung an die [ACI-Feedbackforen](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>Auf IP-Adressen kann aufgrund von Portkonflikten nicht zugegriffen werden

Azure Container Instances unterstützt derzeit keine Portzuordnung wie bei der regulären Docker-Konfiguration. Eine entsprechende Korrektur ist jedoch bereits geplant. Sollten IP-Adressen unerwartet nicht erreichbar sein, vergewissern Sie sich, dass Sie Ihr Containerimage so konfiguriert haben, dass es an den Ports lauscht, die Sie in Ihrer Containergruppe mithilfe der Eigenschaft `ports` verfügbar gemacht haben.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Containerprotokolle und -ereignisse abrufen](container-instances-get-logs.md), um Ihre Container zu debuggen.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
