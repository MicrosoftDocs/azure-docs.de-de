---
title: Download des Azure Kinect Body Tracking SDK
description: Erfahren Sie, wie Sie die einzelnen Versionen des Azure Kinect Sensor SDK unter Windows oder Linux herunterladen.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, Update herunterladen, aktuell, verfügbar, installieren, Körper, Body Tracking
ms.openlocfilehash: 0ac0598d893617f341b9e1fd4d45c0c3e3f3c619
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359594"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Download des Azure Kinect Body Tracking SDK

In diesem Dokument finden Sie Links zur Installation der einzelnen Versionen des Azure Kinect Body Tracking SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Inhalt des Azure Kinect Body Tracking SDK

- Header und Bibliotheken zum Erstellen einer Body Tracking-Anwendung mit Azure Kinect DK
- Weitervertreibbare DLLs, die von Body Tracking-Anwendungen zur Verwendung von Azure Kinect DK benötigt werden
- Beispielanwendungen für das Body Tracking

## <a name="windows-download-links"></a>Downloadlinks für Windows

Version       | Download
--------------|----------
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Linux-Installationsanweisungen

Derzeit wird nur die Distribution Ubuntu 18.04 unterstützt. Weitere Informationen zum Anfordern der Unterstützung für andere Distributionen finden Sie auf [dieser Seite](https://aka.ms/azurekinectfeedback).

Zunächst müssen Sie das [Paketrepository von Microsoft](https://packages.microsoft.com/) gemäß [diesen Anweisungen](/windows-server/administration/linux-package-repository-for-microsoft-software) konfigurieren.

Das Paket `libk4abt<major>.<minor>-dev` enthält die Header und CMake-Dateien für die Erstellung auf der Grundlage von `libk4abt`.
Das Paket `libk4abt<major>.<minor>` enthält die freigegebenen Objekte, die erforderlich sind, um von `libk4abt` abhängige ausführbare Dateien auszuführen. Es enthält auch den Beispielviewer.

Für die allgemeinen Tutorials ist das Paket `libk4abt<major>.<minor>-dev` erforderlich. Führen Sie zum Installieren Folgendes aus:

`sudo apt install libk4abt1.0-dev`

Wurde der Befehl erfolgreich ausgeführt, kann das SDK verwendet werden.

> [!NOTE]
> Merken Sie sich bei der Installation des SDK den Installationspfad. Beispiel: C:\Programme\Azure Kinect Body Tracking SDK 1.0.0. In diesem Pfad finden Sie die Beispiele, auf die in den entsprechenden Artikeln verwiesen wird.
> Body Tracking-Beispiele befinden sich im Repository „Azure-Kinect-Samples“ im Ordner [body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples). Dort finden Sie die Beispiele, auf die in den entsprechenden Artikeln verwiesen wird.

## <a name="change-log"></a>Änderungsprotokoll

### <a name="v101"></a>v1.0.1
* [Fehlerbehebung] Es wurde der Fehler behoben, dass das SDK abstürzt, wenn „onnxruntime.dll“ aus dem Pfad im Windows-Build 19025 oder höher geladen wird: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [Feature] Dem MSI-Installationsprogramm wurde ein C#-Wrapper hinzugefügt.
* [Fehlerbehebung] Es wurde der Fehler behoben, dass Kopfdrehungen nicht richtig erkannt wurden: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Fehlerbehebung] Es wurde der Fehler behoben, dass die CPU-Auslastung auf Linux-Computern bis zu 100 % beträgt: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [Beispiele] Dem Beispielrepository wurden zwei Beispiele hinzugefügt. Beispiel 1 zeigt, wie Body Tracking-Ergebnisse aus dem Tiefenraum in den Farbraum transformiert werden: [Link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample). Beispiel 2 demonstriert die Ermittlung der Bodenebene: [Link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

### <a name="v095"></a>v0.9.5
* [Feature] Unterstützung für C#. Das NuGet-Paket enthält einen C#-Wrapper.
* [Feature] Unterstützung für mehrere Tracker. Das Erstellen mehrerer Tracker ist zulässig. Benutzer können jetzt mehrere Tracker erstellen, um das Body Tracking von verschiedenen Azure Kinect-Geräten durchzuführen.
* [Feature] Unterstützung der Multithreadverarbeitung für den CPU-Modus. Bei Ausführung im CPU-Modus werden alle Kerne genutzt, um maximale Geschwindigkeit zu erzielen.
* [Feature] `gpu_device_id` wurde zur Struktur `k4abt_tracker_configuration_t` hinzugefügt. Benutzer können ein anderes als das standardmäßige GPU-Gerät angeben, um den Body Tracking-Algorithmus auszuführen.
* [Fehlerbehebung/Breaking Change] Ein Rechtschreibfehler im Namen eines Gelenks wurde korrigiert. Der Name des Gelenks wurde von `K4ABT_JOINT_SPINE_NAVAL` zu `K4ABT_JOINT_SPINE_NAVEL` geändert.

### <a name="v094"></a>v0.9.4
* [Feature] Unterstützung für Handgelenke wurde hinzugefügt. Das SDK bietet Informationen zu drei zusätzlichen Gelenken für jede Hand: HAND, HANDTIP, THUMB (HAND, FINGERSPITZE, DAUMEN).
* [Feature] Für jedes erkannte Gelenk wurde ein Konfidenzlevel für die Vorhersage hinzugefügt.
* [Feature] Unterstützung für den CPU-Modus wurde hinzugefügt. Durch Ändern des Werts `cpu_only_mode` in `k4abt_tracker_configuration_t` kann das SDK jetzt im CPU-Modus ausgeführt werden, sodass Benutzer keine leistungsfähige Grafikkarte benötigen.

### <a name="v093"></a>v0.9.3
* [Feature] Es wurde ein neues DNN-Modell veröffentlicht: dnn_model_2_0.onnx. Dieses verbessert die Stabilität beim Body Tracking.
* [Feature] Die temporale Glättung ist jetzt standardmäßig deaktiviert. Die nachverfolgten Gelenke reagieren jetzt besser.
* [Feature] Die Genauigkeit der Körperindexzuordnung wurde verbessert.
* [Fehlerbehebung] Es wurde ein Fehler mit der Effektivität der Einstellung für die Sensorausrichtung behoben.
* [Fehlerbehebung] Der Typ von „body_index_map“ wurde von K4A_IMAGE_FORMAT_CUSTOM in K4A_IMAGE_FORMAT_CUSTOM8 geändert.
* [Bekanntes Problem] Zwei Körper in unmittelbarer Nähe zueinander können zu einer einzigen Instanz zusammengeführt werden.

### <a name="v092"></a>v0.9.2
* [Breaking Change] Die Abhängigkeit wurde auf das neueste Azure Kinect Sensor SDK 1.2.0 aktualisiert.
* [API-Änderung] Die Funktion `k4abt_tracker_create` akzeptiert eine Eingabe von `k4abt_tracker_configuration_t`. 
* [API-Änderung] Die `k4abt_frame_get_timestamp_usec`-API wurde in `k4abt_frame_get_device_timestamp_usec` geändert, damit sie spezifischer für das Sensor SDK 1.2.0 gilt und mehr Konsistenz mit diesem aufweist.
* [Feature] Benutzer können jetzt beim Erstellen des Trackers die Ausrichtung der Sensoren angeben, um beim Aufstellen in verschiedenen Winkeln genauere Body Tracking-Ergebnisse zu erzielen.
* [Feature] Die neue `k4abt_tracker_set_temporal_smoothing`-API wurde bereitgestellt, mit der das Ausmaß an Glättung geändert werden kann, das Benutzer anwenden möchten.
* [Feature] Der C++-Wrapper „k4abt.hpp“ wurde hinzugefügt.
* [Feature] Versionsdefinitionsheader „k4abtversion.h“ wurde hinzugefügt.
* [Fehlerbehebung] Ein Fehler wurde behoben, der eine extrem hohe CPU-Auslastung verursacht hat.
* [Fehlerbehebung] Ein Fehler wurde behoben, der zum Absturz der Protokollierung führte.

### <a name="v091"></a>v0.9.1
* [Fehlerbehebung] Ein Arbeitsspeicherverlust beim Zerstören des Trackers wurde behoben.
* [Fehlerbehebung] Bessere Fehlermeldungen für fehlende Abhängigkeiten.
* [Fehlerbehebung] Fehler ohne Absturz beim Erstellen einer zweiten Trackerinstanz.
* [Fehlerbehebung] Umgebungsvariablen der Protokollierung funktionieren jetzt ordnungsgemäß.
* Linux-Unterstützung

### <a name="v090"></a>v0.9.0

* [Breaking Change] Die SDK-Abhängigkeit wurde auf CUDA 10.0 herabgestuft (von CUDA 10.1). Die ONNX-Runtime bietet offiziell Unterstützung bis CUDA 10.0.
* [Breaking Change] Es wurde von der TensorFlow-Runtime zur ONNX-Runtime gewechselt. Damit wurden die Zeit bis zum Start des ersten Frames und die Arbeitsspeichernutzung verringert. Außerdem wurde die binäre Größe des SDK reduziert.
* [API-Änderung] `k4abt_tracker_queue_capture()` wurde in `k4abt_tracker_enqueue_capture()` umbenannt.
* [API-Änderung] `k4abt_frame_get_body()` wurde in zwei separate Funktionen unterteilt: `k4abt_frame_get_body_skeleton()` und `k4abt_frame_get_body_id()`. Jetzt können Sie die Körper-ID abfragen, ohne immer die gesamte Skelettstruktur kopieren zu müssen.
* [API-Änderung] Die Funktion `k4abt_frame_get_timestamp_usec()` wurde hinzugefügt, um die Schritte zum Abfragen des Zeitstempels für einen Körperframe für Benutzer zu vereinfachen.
* Darüber hinaus wurden die Genauigkeit und Tracking-Zuverlässigkeit des Body Tracking-Algorithmus verbessert.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Kinect DK: Übersicht](about-azure-kinect-dk.md)

- [Schnellstart: Einrichten Ihres Azure Kinect DK-Geräts](set-up-azure-kinect-dk.md)

- [Einrichten von Azure Kinect Body Tracking](body-sdk-setup.md)