---
title: Häufig auftretende Probleme und Problembehandlung
description: Informationen zum Beheben von häufigen Problemen beim Bereitstellen, Ausführen oder Verwalten von Azure Container Instances
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d8e7fb85e369f5f278436370944eafeb1fb6a50e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779514"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Beheben von häufigen Problemen in Azure Container Instances

In diesem Artikel wird veranschaulicht, wie Sie häufige Probleme beim Verwalten oder Bereitstellen von Containern in Azure Container Instances behandeln. Weitere Informationen finden Sie in den [häufig gestellten Fragen](container-instances-faq.md).

Weitere Unterstützung finden Sie in den verfügbaren Optionen für **Hilfe und Support** im [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Probleme bei der Bereitstellung von Containergruppen
### <a name="naming-conventions"></a>Benennungskonventionen

Wenn Sie Ihre Containerspezifikation definieren, erfordern bestimmte Parameter die Einhaltung von Benennungseinschränkungen. Nachfolgend sehen Sie eine Tabelle mit bestimmten Anforderungen für Containergruppeneigenschaften. Weitere Informationen finden Sie unter [Benennungskonventionen][azure-name-restrictions] im Azure Architecture Center und unter [Benennungsregeln und -einschränkungen für Azure-Ressourcen][naming-rules].

| `Scope` | Länge | Schreibweise | Gültige Zeichen | Vorgeschlagenes Muster | Beispiel |
| --- | --- | --- | --- | --- | --- |
| Containername<sup>1</sup> | 1 - 63 |Kleinbuchstaben | Alphanumerisch und Bindestrich (beliebig), außer das erste oder letzte Zeichen |`<name>-<role>-container<number>` |`web-batch-container1` |
| Containerports | Zwischen 1 und 65535 |Integer |Eine ganze Zahl zwischen 1 und 65535 |`<port-number>` |`443` |
| DNS-Namensbezeichnung | 5–63 |Groß-/Kleinschreibung nicht beachten |Alphanumerisch und Bindestrich (beliebig), außer das erste oder letzte Zeichen |`<name>` |`frontend-site1` |
| Umgebungsvariable | 1 - 63 |Groß-/Kleinschreibung nicht beachten |Alphanumerisch und Unterstrich (beliebig), außer das erste oder letzte Zeichen |`<name>` |`MY_VARIABLE` |
| Volumename | 5–63 |Kleinbuchstaben |Alphanumerisch und Bindestriche an beliebiger Stelle, außer als erstes oder letztes Zeichen. Zwei aufeinanderfolgende Bindestriche sind nicht erlaubt. |`<name>` |`batch-output-volume` |

<sup>1</sup>Einschränkung auch für Containergruppennamen, wenn sie nicht unabhängig von Containerinstanzen angegeben werden, z. B. mit Bereitstellungen mit dem Befehl `az container create`.

### <a name="os-version-of-image-not-supported"></a>Betriebssystemversion des Images wird nicht unterstützt

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

### <a name="unable-to-pull-image"></a>Pullvorgang für Image nicht möglich

Wenn Azure Container Instances den anfänglichen Pullvorgang für Ihr Image nicht durchführen kann, werden eine Zeit lang erneute Versuche gestartet. Wenn während des Pullvorgangs für das Image weiterhin ein Fehler auftritt, tritt auch während der Bereitstellung in ACI schließlich ein Fehler auf, und Sie sehen möglicherweise einen `Failed to pull image`-Fehler.

Um dieses Problem zu beheben, löschen Sie die Containerinstanz, und wiederholen Sie die Bereitstellung. Stellen Sie sicher, dass das Bild in der Registrierung vorhanden ist und Sie den Imagenamen richtig eingegeben haben.

Kann das Image nicht gepullt werden, werden in der Ausgabe von [az container show][az-container-show] Ereignisse wie die folgenden angezeigt:

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
### <a name="resource-not-available-error"></a>Ressource nicht verfügbar

Aufgrund der unterschiedlichen Auslastung regionaler Ressourcen in Azure wird beim Versuch, eine Containerinstanz bereitzustellen, möglicherweise die folgende Fehlermeldung angezeigt:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Dieser Fehler gibt an, dass aufgrund starker Auslastung in der Region, in der die Bereitstellung durchgeführt werden soll, die für den Container angegebenen Ressourcen zum aktuellen Zeitpunkt nicht zugeordnet werden können. Führen Sie einen oder mehrere der folgenden Schritte aus, um das Problem zu beheben.

* Überprüfen Sie, ob Ihre Einstellungen für die Containerbereitstellung innerhalb der unter [Regionale Verfügbarkeit für Azure Container Instances](container-instances-region-availability.md) definierten Parameter liegen.
* Geben Sie niedrigere CPU- und Arbeitsspeichereinstellungen für den Container an.
* Führen Sie die Bereitstellung in einer anderen Azure-Region durch.
* Führen Sie die Bereitstellung zu einem späteren Zeitpunkt durch.

## <a name="issues-during-container-group-runtime"></a>Probleme während der Laufzeit von Containergruppen
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Container wird fortlaufend beendet und neu gestartet (kein Vorgang mit langer Ausführungsdauer)

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

Die Container Instances-API und das Azure-Portal enthalten eine `restartCount`-Eigenschaft. Die Anzahl von Neustarts für einen Container kann über die Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az container show][az-container-show] überprüft werden. In der folgenden (gekürzten) Beispielausgabe können Sie die `restartCount`-Eigenschaft am Ende der Ausgabe sehen.

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

### <a name="container-takes-a-long-time-to-start"></a>Start des Containers dauert sehr lange

Die folgenden drei Hauptfaktoren tragen zur Dauer des Containerstartvorgangs in Azure Container Instances bei:

* [Imagegröße](#image-size)
* [Imagespeicherort](#image-location)
* [Zwischengespeicherte Images](#cached-images)

Für Windows-Images gelten noch [weitere Aspekte](#cached-images).

#### <a name="image-size"></a>Imagegröße

Wenn das Starten Ihres Containers sehr lange dauert, aber nach einiger Zeit erfolgreich ist, sollten Sie sich zuerst die Größe des Containerimage ansehen. Da Azure Container Instances den Pullvorgang für Ihr Containerimage nach Bedarf durchführt, ist die Startdauer direkt von der Größe abhängig.

Sie können die Größe Ihres Containerimages mit dem Befehl `docker images` in der Docker CLI anzeigen:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Sie können die Imagegrößen klein halten, indem Sie sicherstellen, dass Ihr endgültiges Image keine Elemente enthält, die zur Laufzeit nicht erforderlich sind. Eine Möglichkeit ist die Verwendung von [mehrstufigen Builds][docker-multi-stage-builds]. Mit mehrstufigen Builds können Sie leicht sicherstellen, dass das endgültige Image nur die Artefakte enthält, die Sie für Ihre Anwendung benötigen – und keinen zusätzlichen Inhalt, der zur Erstellungszeit benötigt wurde.

#### <a name="image-location"></a>Imagespeicherort

Eine weitere Möglichkeit, die Auswirkungen des Pullvorgangs für das Image auf die Startdauer Ihres Containers zu reduzieren, ist das Hosten des Containerimages in [Azure Container Registry](../container-registry/index.yml) in derselben Region, in der Sie Containerinstanzen bereitstellen möchten. Auf diese Weise wird der Netzwerkpfad verkürzt, den das Containerimage zurücklegen muss, sodass sich die Downloadzeit erheblich reduziert.

#### <a name="cached-images"></a>Zwischengespeicherte Images

Azure Container Instances verwendet einen Mechanismus für die Zwischenspeicherung, um die Dauer des Containerstartvorgangs für Images basierend auf allgemeinen [Windows-Basisimages](container-instances-faq.md#what-windows-base-os-images-are-supported) (u. a. `nanoserver:1809`, `servercore:ltsc2019` und `servercore:1809`) zu verkürzen. Häufig verwendete Linux-Images wie `ubuntu:1604` und `alpine:3.6` werden auch zwischengespeichert. Vermeiden Sie sowohl bei Windows- als auch Linux-Images die Verwendung des `latest`-Tags. Lesen Sie zur Anleitung [Bewährte Methoden für Imagetags](../container-registry/container-registry-image-tag-version.md) der Containerregistrierung. Verwenden Sie die API [List Cached Images][list-cached-images] (Zwischengespeicherte Images auflisten), um eine aktuelle Liste der zwischengespeicherten Images und Tags zu erhalten.

> [!NOTE]
> Die Verwendung von auf Windows Server 2019 basierenden Images in Azure Container Instances befindet sich im Vorschaustadium.

#### <a name="windows-containers-slow-network-readiness"></a>Windows-Container verlangsamen die Netzwerkbereitschaft

Bei der ersten Erstellung verfügen Windows-Container ggf. für bis zu 30 Sekunden (selten noch länger) über keine eingehende oder ausgehende Konnektivität. Wenn Ihre Containeranwendung eine Internetverbindung benötigt, fügen Sie eine Verzögerungs- und Wiederholungslogik hinzu, die 30 Sekunden für den Aufbau der Internetverbindung zulässt. Nach der erstmaligen Einrichtung sollten Containernetzwerke ordnungsgemäß fortgesetzt werden.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Verbinden mit zugrundeliegender Docker-API zum Ausführen privilegierte Container nicht möglich

Azure Container Instances bietet keinen direkten Zugriff auf die zugrundeliegende Infrastruktur, die Containergruppen hostet. Dazu gehört der Zugriff auf die Docker-API, die auf dem Containerhost läuft, und die Ausführung von privilegierten Containern. Wenn Sie Docker-Interaktion benötigen, lesen Sie die [REST-Referenzdokumentation](/rest/api/container-instances/), um zu sehen, was die ACI-API unterstützt. Wenn Sie etwas nicht finden, senden Sie eine Anforderung an die [ACI-Feedbackforen](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Auf die IP-Adresse der Containergruppe kann aufgrund von Portkonflikten nicht zugegriffen werden

Azure Container Instances unterstützt noch nicht die Portzuordnung wie bei der regulären Docker-Konfiguration. Wenn die IP-Adresse einer Containergruppe unerwartet nicht erreichbar ist, vergewissern Sie sich, dass Sie das Containerimage so konfiguriert haben, dass es an den Ports lauscht, die Sie in der Containergruppe mithilfe der Eigenschaft `ports` verfügbar gemacht haben.

Wenn Sie überprüfen möchten, ob Azure Container Instances an dem Port lauschen kann, den Sie im Containerimage konfiguriert haben, testen Sie eine Bereitstellung des Images `aci-helloworld`, das den Port verfügbar macht. Führen Sie außerdem die App `aci-helloworld` aus, sodass sie an dem Port lauscht. `aci-helloworld` akzeptiert die optionale Umgebungsvariable `PORT`, um den Standardport 80 zu überschreiben, an dem gelauscht wird. Wenn Sie z.B. Port 9000 testen möchten, legen Sie die [Umgebungsvariable](container-instances-environment-variables.md) beim Erstellen der Containergruppe fest:

1. Richten Sie die Containergruppe so ein, dass Port 9000 verfügbar gemacht wird, und übergeben Sie die Portnummer als Wert der Umgebungsvariablen. Das Beispiel ist für die Bash-Shell formatiert. Wenn Sie eine andere Shell, wie PowerShell oder Eingabeaufforderung bevorzugen, müssen Sie die Variablenzuweisung entsprechend anpassen.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Suchen Sie die IP-Adresse der Containergruppe in der Befehlsausgabe von `az container create`. Suchen Sie den Wert von **ip**. 
1. Nachdem der Container erfolgreich bereitgestellt wurde, navigieren Sie im Browser zu der IP-Adresse und dem Port der Container-App, z. B. `192.0.2.0:9000`. 

    In der Web-App sollte die Meldung „Willkommen bei Azure Container Instances“ angezeigt werden.
1. Wenn Sie den Container nicht mehr benötigen, entfernen Sie ihn mithilfe des Befehls `az container delete`:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Containerprotokolle und -ereignisse abrufen](container-instances-get-logs.md), um Ihre Container zu debuggen.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
