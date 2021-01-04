---
title: 'Produktionsbereitschaft und Best Practices: Azure'
description: Dieser Artikel enthält eine Anleitung zum Konfigurieren und Bereitstellen des Moduls Live Video Analytics in IoT Edge in Produktionsumgebungen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 215427e3524861a842349b197668d92167960e5c
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906334"
---
# <a name="production-readiness-and-best-practices"></a>Produktionsbereitschaft und Best Practices

Dieser Artikel enthält eine Anleitung zum Konfigurieren und Bereitstellen des Moduls Live Video Analytics in IoT Edge in Produktionsumgebungen. Lesen Sie zur Vorbereitung Ihrer IoT Edge-Lösung außerdem den Artikel [Vorbereiten der Bereitstellung einer IoT Edge-Lösung für die Produktion](../../iot-edge/production-checklist.md). 

> [!NOTE]
> Informieren Sie sich bei den IT-Abteilungen Ihrer Organisationen über mögliche Sicherheitsaspekte.

## <a name="running-the-module-as-a-local-user"></a>Ausführen des Moduls als lokaler Benutzer

Wenn Sie das Modul Live Video Analytics in IoT Edge auf einem Edgegerät bereitstellen, wird es standardmäßig mit erhöhten Rechten ausgeführt. Wenn Sie in einem solchen Fall die Protokolle im Modul überprüfen (`sudo iotedge logs {name-of-module}`), wird Folgendes angezeigt:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

In den folgenden Abschnitten wird erläutert, wie Sie die obige Warnung behandeln können.

### <a name="creating-and-using-a-local-user-account"></a>Erstellen und Verwenden eines lokalen Benutzerkontos

Sie können und sollten das Modul Live Video Analytics in IoT Edge in einer Produktionsumgebung unter einem Konto mit möglichst geringen Berechtigungen ausführen. Die folgenden Befehle zeigen z. B., wie Sie auf einer Linux-VM ein lokales Benutzerkonto erstellen:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Als Nächstes können Sie im Bereitstellungsmanifest die Umgebungsvariablen LOCAL_USER_ID und LOCAL_GROUP_ID auf Werte von anderen Benutzern und Gruppen als root festlegen:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Zuweisen von Berechtigungen für den Gerätespeicher

Das Modul Live Video Analytics in IoT Edge erfordert die Möglichkeit, Dateien in das lokale Dateisystem zu schreiben, wenn Folgendes zutrifft:

* Sie verwenden die Modulzwillingseigenschaft [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties): Geben Sie ein Verzeichnis im lokalen Dateisystem zum Speichern von Konfigurationsdaten an.
* Sie verwenden ein Mediendiagramm, um Videos in der Cloud aufzuzeichnen: Das Modul benötigt ein Verzeichnis auf dem Edgegerät als Cache. (Weitere Informationen finden Sie im Artikel zur [fortlaufenden Videoaufzeichnung](continuous-video-recording-concept.md).)
* [Die Aufzeichnung erfolgt in eine lokale Datei](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources): Geben Sie einen Dateipfad für das aufgezeichnete Video an.

Wenn Sie beabsichtigen, eine der oben genannten Optionen anzuwenden, sollten Sie sicherstellen, dass das obige Benutzerkonto Zugriff auf das entsprechende Verzeichnis hat. Nehmen Sie als Beispiel applicationDataDirectory. Sie können ein Verzeichnis auf dem Edgegerät erstellen und den Gerätespeicher mit dem Modulspeicher verknüpfen. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

Als Nächstes können Sie in den Erstellungsoptionen für das Edgemodul im Bereitstellungsmanifest eine Binds-Einstellung hinzufügen, die das Verzeichnis von oben („var/local/mediaservices/“) einem Verzeichnis im Modul zuordnet (z. B. „/var/lib/azuremediaservices/“). Sie verwenden das zuletzt genannte Verzeichnis dann außerdem als Wert für applicationDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Wenn Sie sich die Beispiele für Mediendiagramme für den Schnellstart und die Tutorials ansehen (z. B. zur [fortlaufenden Videoaufzeichnung](continuous-video-recording-tutorial.md)), werden Sie feststellen, dass das Mediencacheverzeichnis (localMediaCachePath) ein Unterverzeichnis von applicationDataDirectory verwendet. Dies ist die empfohlene Vorgehensweise, da der Cache vorübergehende Daten enthält.

### <a name="naming-video-assets-or-files"></a>Benennen von Videoressourcen oder -dateien

Mediendiagramme ermöglichen die Erstellung von Medienobjekten in der Cloud oder von MP4-Dateien am Edge. Medienobjekte können durch [fortlaufende Videoaufzeichnungen](continuous-video-recording-tutorial.md) oder [ereignisbasierter Videoaufzeichnungen](event-based-video-recording-tutorial.md) generiert werden. Obwohl diese Medienobjekte und Dateien beliebig benannt werden können, wird als Benennungsstruktur für Medienobjekte bei der fortlaufenden Videoaufzeichnung Folgendes empfohlen: „&lt;beliebigerText&gt;-${System.GraphTopologyName}-${System.GraphInstanceName}“. Beispielsweise können Sie assetNamePattern in der Medienobjektsenke wie folgt festlegen:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Bei der ereignisbasierten Videoaufzeichnung wird für die generierten Medienobjekte das folgende Benennungsmuster empfohlen: „&lt;beliebigerText&gt;-$ {System.DateTime}“. Die Systemvariable stellt sicher, dass Medienobjekte nicht überschrieben werden, wenn Ereignisse gleichzeitig eintreten. Beispielsweise können Sie assetNamePattern in der Medienobjektsenke wie folgt festlegen:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Wenn Sie mehrere Instanzen desselben Diagramms ausführen, können Sie den Namen der Diagrammtopologie und den Instanznamen verwenden, um sie zu unterscheiden. Beispielsweise können Sie assetNamePattern in der Medienobjektsenke wie folgt festlegen:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

Bei MP4-Videoclips, die mit der ereignisbasierten Videoaufzeichnung am Edge generiert werden, wird empfohlen, in das Benennungsmuster den DateTime-Wert einzufügen. Bei mehreren Instanzen desselben Diagramms wird darüber hinaus empfohlen, die Systemvariablen GraphTopologyName und GraphInstanceName zu verwenden. Sie können beispielsweise filePathPattern bei einer Dateisenke wie folgt festlegen: 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

oder 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```

### <a name="keeping-your-vm-clean"></a>Bereinigen des virtuellen Computers

Es kann passieren, dass die Linux-VM, die Sie als Edgegerät verwenden, nicht mehr reagiert, wenn sie nicht regelmäßig gewartet wird. Es ist sehr wichtig, die Caches zu bereinigen und unnötige Pakete sowie nicht verwendete Container von der VM zu entfernen. Zu diesem Zweck finden Sie im Folgenden eine Reihe empfohlener Befehle, die Sie auf Ihrer Edge-VM verwenden können.

1. `sudo apt-get clean`

    Der Befehl „apt-get clean“ löscht das lokale Repository der abgerufenen Paketdateien, die in „/var/cache“ verblieben sind. Dabei werden die Verzeichnisse „/var/cache/apt/archives/“ und „/var/cache/apt/archives/partial/“ bereinigt. Die einzigen Dateien, die in „/var/cache/apt/archives“ verbleiben, sind die Sperrdatei und das Unterverzeichnis „partial“. Der Befehl „apt-get clean“ wird normalerweise verwendet, um bei Bedarf Speicherplatz freizugeben. Dies erfolgt im Allgemeinen im Rahmen der regelmäßigen geplanten Wartung. Weitere Informationen finden Sie unter [Cleaning up with apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html) (Bereinigen mit apt-get).
1. `sudo apt-get autoclean`

    Die Option „apt-get autoclean“ löscht wie „apt-get clean“ abgerufene Paketdateien aus dem lokalen Repository, beschränkt dies jedoch ausschließlich auf Dateien, die nicht mehr heruntergeladen werden können und nicht mehr benötigt werden. Damit können Sie verhindern, dass der Cache zu groß wird.
1. `sudo apt-get autoremove1`

    Mit der Option „autoremove“ werden Pakete entfernt, die automatisch installiert wurden, da sie von einem anderen Paket benötigt wurden. Wenn aber diese anderen Pakete entfernt wurden, werden sie nicht mehr benötigt.
1. `sudo docker image ls` zeigt eine Liste der Docker-Images auf Ihrem Edgesystem an.
1. `sudo docker system prune `

    Docker nutzt zum Bereinigen nicht verwendeter Objekte einen konservativen Ansatz (häufig als „Garbage Collection“ bezeichnet), der z. B. Images, Container, Volumes und Netzwerke umfasst: Diese Objekte werden in der Regel nur dann entfernt, wenn Sie Docker explizit dazu auffordern. Dies kann dazu führen, dass Docker zusätzlichen Speicherplatz verwendet. Für jeden Objekttyp bietet Docker einen Befehl zum Löschen („prune“). Darüber hinaus können Sie „docker system prune“ verwenden, um mehrere Typen von Objekten gleichzeitig zu bereinigen. Weitere Informationen finden Sie unter [Prune unused Docker objects](https://docs.docker.com/config/pruning/) (Löschen nicht verwendeter Docker-Objekte).
1. `sudo docker rmi REPOSITORY:TAG`

    Da die Updates im Edgemodul durchgeführt werden, kann Ihre Docker-Instanz auch ältere Versionen des Edgemoduls enthalten. In einem solchen Fall empfiehlt es sich, mit dem Befehl „docker rmi“ bestimmte Images zu entfernen, die durch das Versionstag des Images identifiziert werden.

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erste Schritte mit Live Video Analytics in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
