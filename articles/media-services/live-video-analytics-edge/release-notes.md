---
title: 'Versionshinweise zu Live Video Analytics in IoT Edge: Azure'
description: Dieses Thema enthält Versionshinweise zu Releases, Verbesserungen, Fehlerbehebungen und bekannten Problemen von Video Analytics in IoT Edge.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: f130b93b8d799c371a640f2b29c22c0d77834cba
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954394"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Versionshinweise zu Live Video Analytics in IoT Edge

>Sie können eine Benachrichtigung erhalten, wann auf dieser Seite Updates vorhanden sind, indem Sie die URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` kopieren und in Ihren RSS-Feedreader einfügen.

Dieser Artikel bietet Folgendes:

* Neueste Versionen
* Bekannte Probleme
* Behebung von Programmfehlern
* Veraltete Funktionen

<hr width=100%>

## <a name="january-12-2021"></a>12. Januar 2021

Dieses Releasetag gilt für die Aktualisierung des Moduls vom Januar 2021 und lautet:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> In den Schnellstarts und Tutorials verwenden die Bereitstellungsmanifeste das Tag „2“ (live-video-analytics:2). Daher sollte eine einfache erneute Bereitstellung solcher Manifeste das Modul auf Ihren Edgegeräten aktualisieren.
### <a name="bug-fixes"></a>Behebung von Programmfehlern 

* Die Felder `ActivationSignalOffset`, `MinimumActivationTime` und `MaximumActivationTime` in Signalgateprozessoren wurden fälschlicherweise als erforderliche Eigenschaften festgelegt. Sie sind nun **optionale** Eigenschaften.
* Es wurde ein Verwendungsfehler behoben, durch den Live Video Analytics auf einem IoT Edge-Modul abstürzte, wenn die Bereitstellung in bestimmten Regionen erfolgte.

<hr width=100%>

## <a name="december-14-2020"></a>14. Dezember 2020
Dieses Release ist eine Aktualisierung der öffentlichen Vorschauversion von Live Video Analytics in IoT Edge. Das Releasetag ist

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Modulupdates
* Unterstützung für die Verwendung von mehr als einem HTTP-Erweiterungsprozessor und einem gRPC-Erweiterungsprozessor pro Graphtopologie hinzugefügt.
* Unterstützung für die [Speicherplatzverwaltung für Senkenknoten](upgrading-lva-module.md#disk-space-management-with-sink-nodes) hinzugefügt.
* Der `MediaGraphGrpcExtension`-Knoten unterstützt jetzt die [extensionConfiguration](grpc-extension-protocol.md)-Eigenschaft für die Verwendung mehrerer KI-Modelle auf einem einzelnen gRPC-Server.
* Unterstützung für das Sammeln von Metriken für das Live Video Analytics-Modul im [Prometheus-Format](https://prometheus.io/docs/practices/naming/) wurde hinzugefügt. Erfahren Sie mehr über das [Erfassen und Anzeigen von Metriken in Azure Monitor](monitoring-logging.md#azure-monitor-collection-via-telegraf). 
* Die Möglichkeit zum Filtern der Ausgabeauswahl wurde hinzugefügt. Sie können **reine Audiodaten** oder **reine Videodaten** oder **Audio- und Videodaten** mit `outputSelectors` an einen beliebigen MediaGraph-Knoten weitergeben. 
* Der Verarbeitungsknoten für Bildfrequenzfilter wurde als **veraltet** gekennzeichnet.  
    * Die Verwaltung der Bildfrequenz ist jetzt auf den Knoten des Graph-Erweiterungsprozessors selbst verfügbar.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-Erweiterung
* [Live Video Analytics auf IoT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) wurde veröffentlicht: Eine Visual Studio Code-Erweiterung, die Sie beim Verwalten von LVA-Mediengraphen unterstützt. Diese Erweiterung arbeitet mit dem **LVA 2.0-Modul** und bietet die Bearbeitung und Verwaltung von Mediengraphen auf einer schlanken und benutzerfreundlichen grafischen Oberfläche.
## <a name="september-22-2020"></a>22. September 2020

Dieses Releasetag gilt für die Aktualisierung des Moduls vom September 2020 und lautet:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> In den Schnellstartanleitungen und Tutorials verwenden die Bereitstellungsmanifeste das Tag 1 (live-video-analytics:1). Daher sollte eine einfache erneute Bereitstellung solcher Manifeste das Modul auf Ihren Edgegeräten aktualisieren.

### <a name="module-updates"></a>Modulupdates

* Ein neuer Grapherweiterungsknoten mit dem Namen [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) kann in das [Modul für die räumliche Analyse](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview) (Vorschau) aus Cognitive Services integriert werden.
* Unterstützung für Linux ARM64-Geräte hinzugefügt: Verwenden Sie [manuelle Schritte](deploy-iot-edge-device.md) für die Bereitstellung auf solchen Geräten.

### <a name="documentation-updates"></a>Dokumentationsupdates

* Für die Verwendung von Live Video Analytics in IoT Edge auf Azure Stack Edge-Geräten sind [Anweisungen](deploy-azure-stack-edge-how-to.md) verfügbar.
* Neues Tutorial zum Entwickeln szenarienspezifischer Modelle für maschinelles Sehen mit dem [Dienst Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) und deren Einsatz zum [Analysieren von Livevideo](custom-vision-tutorial.md) in Echtzeit.

<hr width=100%>

## <a name="august-19-2020"></a>19. August 2020

Dieses Releasetag gilt für die Aktualisierung des Moduls vom August 2020 und lautet:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> In den Schnellstartanleitungen und Tutorials verwenden die Bereitstellungsmanifeste das Tag 1 (live-video-analytics:1). Daher sollte eine einfache erneute Bereitstellung solcher Manifeste das Modul auf Ihren Edgegeräten aktualisieren.

### <a name="module-updates"></a>Modulupdates

* Sie können jetzt mithilfe des gRPC-Frameworks eine hohe Leistung für die Übertragung von Dateninhalten zwischen Live Video Analytics in IoT Edge und Ihrer benutzerdefinierten Erweiterung erzielen. Im [Schnellstart](analyze-live-video-use-your-grpc-model-quickstart.md) erfahren Sie mehr zu den ersten Schritten.
* Umfassendere regionale Bereitstellung von Live Video Analytics, wobei nur der Clouddienst aktualisiert wurde.  
* Live Video Analytics ist jetzt in 25 weiteren Regionen auf der ganzen Welt verfügbar. Hier finden Sie eine [Liste](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) mit diesen Regionen.  
* Auch die [Einrichtung](https://aka.ms/lva-edge/setup-resources-for-samples) für Schnellstarts wurde mit Unterstützung neuer Regionen aktualisiert.
    * Es gibt keinen Handlungsaufruf für diejenigen, die Ressourcen bereits eingerichtet haben.

### <a name="bug-fixes"></a>Behebung von Programmfehlern 

* Veraltete Azure-Erweiterung wird im Einrichtungsskript nicht mehr verwendet.

<hr width=100%>

## <a name="july-13-2020"></a>13. July 2020

Dieses Releasetag gilt für die Aktualisierung des Moduls vom Juli 2020 und lautet:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> In den Schnellstartanleitungen und Tutorials verwenden die Bereitstellungsmanifeste das Tag 1 (live-video-analytics:1). Daher sollte eine einfache erneute Bereitstellung solcher Manifeste das Modul auf Ihren Edgegeräten aktualisieren.

### <a name="module-updates"></a>Modulupdates

* Sie können jetzt Graphtopologien erstellen, die sowohl einen Knoten der Medienobjektsenke als auch einen Knoten der Dateisenke hinter einem Signalgate-Prozessorknoten aufweisen. Ein Beispiel finden Sie in der [Topologie](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files).

### <a name="bug-fixes"></a>Behebung von Programmfehlern

* Verbesserungen bei der Validierung gewünschter Eigenschaften

<hr width=100%>

## <a name="june-1-2020"></a>1\. Juni 2020

Dieses Release ist erste öffentliche Vorschauversion von Live Video Analytics in IoT Edge. Das Releasetag ist

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Unterstützte Funktionen

* Analyse von Live-Videostreams mithilfe von KI-Modulen Ihrer Wahl und optionales Aufzeichnen von Videosignalen auf dem Edge-Gerät oder in der Cloud
* Ausführung unter den von IoT Edge [unterstützten](../../iot-edge/support.md) AMD64-Linux-Betriebssystemen
* Bereitstellung und Konfiguration des Moduls über den IoT Hub mithilfe des Azure-Portals oder von Visual Studio Code
* Verwalten von [Graphtopologien und Graphinstanzen](media-graph-concept.md#media-graph-topologies-and-instances) remote oder lokal mithilfe der folgenden Aufrufe direkter Methoden

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](overview.md)
