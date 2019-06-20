---
title: IoT-Bereitstellung von benutzerdefinierten simulierten Geräten – Azure | Microsoft-Dokumentation
description: In dieser Anleitung wird gezeigt, wie Sie benutzerdefinierte simulierte Geräte für den Solution Accelerator für die Remoteüberwachung bereitstellen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427568"
---
# <a name="deploy-a-new-simulated-device"></a>Bereitstellen eines neuen simulierten Geräts

Sowohl mit dem Solution Accelerator für Remoteüberwachung als auch dem für Gerätesimulation können Sie Ihre eigenen simulierten Geräte definieren. In diesem Artikel wird gezeigt, wie Sie einen angepassten "Chiller"-Gerätetyp und einen neuen "Lightbulb"-Gerätetyp (Leuchtmittel) für den Solution Accelerator für die Remoteüberwachung bereitstellen.

Bei den Schritten in diesem Artikel wird angenommen, dass Sie die Anleitung [Erstellen und Testen eines neuen simulierten Geräts](iot-accelerators-remote-monitoring-create-simulated-device.md) ausgeführt haben und über die Dateien zum Definieren des angepassten "Chiller"- und neuen "Lightbulb"-Gerätetyps verfügen.

Die Schritte in dieser Anleitung zeigen Ihnen, wie Sie die folgenden Aktionen ausführen:

1. Verwenden von SSH für den Zugriff auf das Dateisystem des virtuellen Computers, der den Solution Accelerator für die Remoteüberwachung hostet.

1. Konfigurieren von Docker zum Laden der Gerätemodelle von einem Speicherort außerhalb des Docker-Containers.

1. Ausführen des Solution Accelerators für die Remoteüberwachung mithilfe von benutzerdefinierten Gerätemodelldateien.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Um die in dieser Anleitung aufgeführten Schritte ausführen zu können, benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen dieser Anleitung benötigen Sie Folgendes:

- Eine bereitgestellte Instanz des [Solution Accelerators für die Remoteüberwachung](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Eine lokale **Bash**-Shell zum Ausführen der Befehle `ssh` und `scp`. Unter Windows können Sie **Bash** auf einfache Weise installieren, indem Sie [Git](https://git-scm.com/download/win) installieren.
- Ihre benutzerdefinierten Gerätemodelldateien, z.B. die in [Erstellen und Testen eines neuen simulierten Geräts](iot-accelerators-remote-monitoring-create-simulated-device.md) beschriebenen.

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurieren von Docker

In diesem Abschnitt konfigurieren Sie Docker zum Laden der Gerätemodelldateien aus dem Ordner **/tmp/devicemodels** auf dem virtuellen Computer (statt aus dem Docker-Container). Führen Sie die Befehle in diesem Abschnitt in einer **Bash**-Shell auf Ihrem lokalen Computer aus:

In diesem Abschnitt konfigurieren Sie Docker zum Laden der Gerätemodelldateien aus dem Ordner **/tmp/devicemodels** auf dem virtuellen Computer (statt aus dem Docker-Container). Führen Sie die Befehle in diesem Abschnitt in einer **Bash**-Shell auf Ihrem lokalen Computer aus:

1. Verwenden Sie SSH zum Herstellen einer Verbindung zwischen Ihrem lokalen Computer und dem virtuellen Computer in Azure. Bei dem folgenden Befehl wird angenommen, dass die öffentliche IP-Adresse des virtuellen Computers **vm-vikxv** gleich **104.41.128.108** lautet. Ersetzen Sie diesen Wert durch die öffentliche IP-Adresse Ihres virtuellen Computers aus dem vorherigen Abschnitt:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Führen Sie die Aufforderungen zum Anmelden am virtuellen Computer mit dem Kennwort aus, das Sie im vorherigen Abschnitt festgelegt haben.

1. Konfigurieren Sie den Gerätesimulationsdienst zum Laden der Gerätemodelle von außerhalb des Containers. Öffnen Sie zuerst die Docker-Konfigurationsdatei:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Suchen Sie die Einstellungen für den **Gerätesimulationscontainer**, und bearbeiten Sie die Einstellung **Volumes**, wie im folgenden Codeausschnitt gezeigt wird:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Speichern Sie die Änderungen.

1. Kopieren Sie die vorhandenen Gerätemodelldateien aus dem Container an den neuen Speicherort. Suchen Sie zuerst die Container-ID für den Gerätesimulationscontainer:

    ```sh
    sudo docker ps
    ```

    Kopieren Sie dann die Gerätemodelldateien in den Ordner **tmp** auf dem virtuellen Computer. Beim folgenden Befehl wird angenommen, dass die Container-ID "c378d6878407" lautet. Ersetzen Sie diesen Wert durch die ID des Gerätesimulationscontainers:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Lassen Sie das **Bash**-Fenster mit Ihrer SSH-Sitzung geöffnet.

1. Kopieren Sie Ihre benutzerdefinierten Gerätemodelldateien auf den virtuellen Computer. Führen Sie diesen Befehl in einer anderen **Bash**-Shell auf dem Computer aus, auf dem Sie Ihre benutzerdefinierten Gerätemodelle erstellt haben. Navigieren Sie zuerst zu dem lokalen Ordner, in dem Ihre JSON-Gerätemodelldateien gespeichert sind. Bei dem folgenden Befehl wird angenommen, dass die öffentliche IP-Adresse des virtuellen Computers **104.41.128.108** lautet. Ersetzen Sie diesen Wert durch die öffentliche IP-Adresse Ihres virtuellen Computers. Geben Sie bei der entsprechenden Aufforderung das Kennwort für den virtuellen Computer ein:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Starten Sie den Docker-Container für Gerätesimulation erneut, um die neuen Gerätemodelle verwenden zu können. Führen Sie die folgenden Befehle in der **Bash**-Shell mit der geöffneten SSH-Sitzung auf dem virtuellen Computer aus:

    ```sh
    sudo /app/start.sh
    ```

    Wenn Sie den Status der aktiven Docker-Container und deren Container-IDs anzeigen möchten, verwenden Sie den folgenden Befehl:

    ```sh
    sudo docker ps
    ```

    Wenn Sie das Protokoll aus dem Gerätesimulationscontainer anzeigen möchten, führen Sie den folgenden Befehl aus. Ersetzen Sie die Container-ID durch die ID Ihres Gerätesimulationscontainers:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Ausführen der Simulation

Jetzt können Sie Ihre benutzerdefinierten Gerätemodelle in der Remoteüberwachungslösung verwenden:

1. Starten Sie Ihr Dashboard für die Remoteüberwachung aus [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Fügen Sie über die Seite **Geräte** simulierte Geräte hinzu. Wenn Sie ein neues simuliertes Gerät hinzufügen, stehen Ihre neuen Gerätemodelle zum Auswählen zur Verfügung.

1. Sie können auf dem Dashboard Gerätetelemetriedaten anzeigen und Gerätemethoden aufrufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mehr wissen möchten, lassen Sie den Solution Accelerator für die Remoteüberwachung weiterhin bereitgestellt.

Falls Sie den Solution Accelerator nicht mehr benötigen, können Sie ihn auf der Seite [Bereitgestellte Lösungen](https://www.azureiotsolutions.com/Accelerators#dashboard) löschen, indem Sie ihn auswählen und dann auf **Lösung löschen** klicken.

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung wurde gezeigt, wie Sie benutzerdefinierte Gerätemodelle für den Solution Accelerator für Gerätesimulation bereitstellen. Im nächsten empfohlenen Schritt erfahren Sie, wie Sie [ein echtes Gerät mit Ihrer Remoteüberwachungslösung verbinden](iot-accelerators-connecting-devices-node.md).
