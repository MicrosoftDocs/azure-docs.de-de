---
title: Bereitstellen des OPC Twin-Moduls für Azure von Grund auf | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie OPC Twin von Grund auf neu bereitstellen.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f0fc3722ee440b6f50b86f916afef7ddc5876eef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693404"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Bereitstellen des OPC Twin-Moduls und der Abhängigkeiten von Grund auf

Das OPCTwin-Modul wird auf IoT Edge ausgeführt und stellt mehrere Edge-Dienste für die OPC Device Twin- und Registry-Dienste (Gerätezwilling und Registrierung) zur Verfügung. 

Es gibt mehrere Optionen zum Bereitstellen von Modulen für Ihr [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, z. B.:

- [Bereitstellen von Azure IoT Edge-Modulen über das Azure-Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe der Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Weitere Informationen zu den Details und eine Anleitung zur Bereitstellung finden Sie im [GitHub-Repository](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Bereitstellungsmanifest

Alle Module werden mit einem Bereitstellungsmanifest bereitgestellt.  Ein Beispielmanifest für die Bereitstellung von [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) und [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) ist unten angegeben.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"CapAdd\":[\"NET_ADMIN\"]}}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Bereitstellen über das Azure-Portal

Die einfachste Möglichkeit zum Bereitstellen der Module auf einem Azure IoT Edge-Gatewaygerät ist die Verwendung des Azure-Portals.  

### <a name="prerequisites"></a>Voraussetzungen

1. Stellen Sie die OPC Twin-[Abhängigkeiten](howto-opc-twin-deploy-dependencies.md) bereit, und beschaffen Sie die sich ergebende `.env`-Datei. Notieren Sie sich den bereitgestellten `hub name` der Variablen `PCS_IOTHUBREACT_HUB_NAME` in der sich ergebenden `.env`-Datei.

2. Registrieren und starten Sie ein [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)- oder [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows)-IoT Edge-Gateway, und notieren Sie sich dessen `device id`.

### <a name="deploy-to-an-edge-device"></a>Bereitstellen auf einem Edgegerät

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem IoT Hub.

2. Wählen Sie im Menü auf der linken Seite die Option **IoT Edge**.

3. Klicken Sie in der Liste der Geräte auf die ID des Zielgeräts.

4. Wählen Sie **Module festlegen** aus.

5. Wählen Sie im Abschnitt **Bereitstellungsmodule** der Seite die Optionen **Hinzufügen** und **IoT Edge-Modul**.

6. Verwenden Sie im Dialogfeld **Benutzerdefiniertes IoT Edge-Modul** den Namen `opctwin` als Namen für das Modul, und geben Sie dann den *Image-URI* für den Container wie folgt an:

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Verwenden Sie den folgenden JSON-Code unter *Erstellungsoptionen*:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   Füllen Sie bei Bedarf die optionalen Felder aus. Weitere Informationen zu Containererstellungsoptionen, Neustartrichtlinien und gewünschtem Status finden Sie unter [Gewünschte EdgeAgent-Eigenschaften](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Weitere Informationen zum Modulzwilling finden Sie unter [Definieren oder Aktualisieren gewünschter Eigenschaften](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Wählen Sie **Speichern**, und wiederholen Sie Schritt **5**.  

8. Verwenden Sie im Dialogfeld „Benutzerdefiniertes IoT Edge-Modul“ den Namen `opcpublisher` als Namen für das Modul und den *Image-URI* für den Container wie folgt: 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Verwenden Sie den folgenden JSON-Code unter *Erstellungsoptionen*:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Wählen Sie **Speichern** und dann **Weiter**, um mit dem Abschnitt „Routen“ fortzufahren.

10. Fügen Sie auf der Registerkarte „Routen“ Folgendes hinzu: 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    Wählen Sie anschließend **Weiter**.

11. Überprüfen Sie die Bereitstellungsinformationen und das Manifest.  Es sollte dem obigen Bereitstellungsmanifest ähneln.  Klicken Sie auf **Submit** (Senden).

12. Nachdem Sie die Module auf Ihrem Gerät bereitgestellt haben, können Sie sie auf der Seite **Gerätedetails** des Portals anzeigen. Auf dieser Seite werden die Namen der einzelnen bereitgestellten Modul sowie nützliche Informationen wie der Bereitstellungsstatus und der Exitcode angezeigt.

## <a name="deploying-using-azure-cli"></a>Bereitstellen per Azure CLI

### <a name="prerequisites"></a>Voraussetzungen

1. Installieren Sie die aktuelle Version der [Azure-Befehlszeilenschnittstelle (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) über [diese Seite](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Schnellstart

1. Speichern Sie das obige Bereitstellungsmanifest in der Datei `deployment.json`.  

2. Wenden Sie die Konfiguration mit dem folgenden Befehl auf ein IoT Edge-Gerät an:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Beim Parameter `device id` wird die Groß-/Kleinschreibung berücksichtigt. Der content-Parameter verweist auf die gespeicherte Bereitstellungsmanifestdatei. 
    ![az IoT Edge set-modules-Ausgabe](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Nachdem Sie die Module auf Ihrem Gerät bereitgestellt haben, können Sie sie mit dem folgenden Befehl anzeigen:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Beim Parameter für die Geräte-ID wird die Groß-/Kleinschreibung berücksichtigt. ![az iot hub module-identity list-Ausgabe](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>Lokales Ausführen und Debuggen

Für die Problembehandlung und das Debuggen ist es nützlich, die Edgemodule lokal mit dem [IoT Edge-Entwicklungssimulator](https://github.com/Azure/iotedgehubdev) auszuführen.  Es wird eine lokale Entwicklungsumgebung mit einem Simulator zum Erstellen, Entwickeln, Testen, Ausführen und Debuggen von Azure IoT Edge-Modulen und -Lösungen mit denselben Komponenten bzw. demselben Code wie in der Produktion bereitgestellt.

### <a name="prerequisites"></a>Voraussetzungen

1. Stellen Sie die OPC Twin-[Abhängigkeiten](howto-opc-twin-deploy-dependencies.md) bereit.

2. Installieren Sie [Docker CE (18.02.0+)](https://www.docker.com/community-edition) unter [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) oder [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Installieren Sie [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (Nur für **Linux** erforderlich. Compose ist bereits in der Windows-/macOS-Installation von Docker CE enthalten.).

4. Installieren Sie [Python (2.7/3.5+) und Pip](https://www.python.org/).

5. Installieren Sie „iotedgehubdev“, indem Sie den folgenden Befehl in Ihrem Terminal ausführen.

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Installieren Sie `iotedgehubdev` unter Linux/macOS im Verzeichnis **root**. (*Verwenden Sie für den Befehl „pip install“ nicht die Option „--user“* .)
> Stellen Sie sicher, dass auf demselben Computer zusätzlich zu iotedgehubdev keine Azure IoT Edge-Runtime ausgeführt wird, da hierfür dieselben Ports benötigt werden.

### <a name="quickstart"></a>Schnellstart

1. Befolgen Sie die Anleitung unter [Registrieren eines neuen Azure IoT Edge-Geräts über das Azure-Portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Kopieren Sie die Edge-Geräteverbindungszeichenfolge.

2. Richten Sie den Simulator mit der Edge-Verbindungszeichenfolge ein.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Kopieren Sie das obige Manifest in die Datei `deployment.json` in demselben Ordner.  Starten Sie die Bereitstellung im Simulator mit:

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Beenden Sie den Simulator mit:

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie OPC Twin von Grund auf bereitstellen, lautet unser Vorschlag für den nächsten Schritt:

> [!div class="nextstepaction"]
> [Bereitstellen von OPC Twin in einem vorhandenen Projekt](howto-opc-twin-deploy-existing.md)