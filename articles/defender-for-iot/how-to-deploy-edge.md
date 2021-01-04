---
title: Bereitstellen eines IoT Edge-Sicherheitsmoduls
description: Erfahren Sie, wie Sie einen Defender für IoT-Sicherheits-Agent auf einem IoT Edge-Gerät bereitstellen.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 75a9d1267a8824801e3c24fc25c687f29a808187
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339930"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Bereitstellen eines Sicherheitsmoduls auf Ihrem IoT Edge-Gerät

Das Modul **für IoT** stellt eine umfassende Sicherheitslösung für Ihr IoT Edge-Gerät bereit.
Das Sicherheitsmodul sammelt, aggregiert und analysiert Sicherheitsrohdaten von Ihrem Betriebs- und Containersystem und gibt verwertbare Sicherheitsempfehlungen und -warnungen aus.
Weitere Informationen finden Sie unter [Azure IoT Edge-Sicherheitsmodul](security-edge-architecture.md).

In diesem Artikel erfahren Sie, wie Sie ein Sicherheitsmodul auf Ihrem IoT Edge-Gerät bereitstellen.

## <a name="deploy-security-module"></a>Bereitstellen eines Sicherheitsmoduls

Gehen Sie wie folgt vor, um ein Defender für IoT-Sicherheitsmodul für IoT Edge bereitzustellen.

### <a name="prerequisites"></a>Voraussetzungen

1. Vergewissern Sie sich in Ihrem IoT-Hub, dass Ihr Gerät [als IoT Edge-Gerät registriert](../iot-edge/how-to-manual-provision-symmetric-key.md#register-a-new-device) ist.

1. Das Defender für IoT Edge-Modul erfordert, dass das [AuditD-Framework](https://linux.die.net/man/8/auditd) auf dem IoT Edge-Gerät installiert ist.

    - Installieren Sie das Framework, indem Sie den folgenden Befehl auf Ihrem IoT Edge-Gerät ausführen:

    `sudo apt-get install auditd audispd-plugins`

    - Überprüfen Sie, ob AuditD aktiv ist, indem Sie den folgenden Befehl ausführen:

    `sudo systemctl status auditd`<br>
    - Die erwartete Antwort ist `active (running)`.

### <a name="deployment-using-azure-portal"></a>Bereitstellung über das Azure-Portal

1. Öffnen Sie im Azure-Portal den **Marketplace**.

1. Wählen Sie **Internet der Dinge** aus, suchen Sie nach **Defender für IoT**, und wählen Sie den Eintrag aus.

   ![Auswählen von Defender für IoT](media/howto/edge-onboarding-8.png)

1. Klicken Sie auf **Erstellen**, um die Bereitstellung zu konfigurieren.

1. Wählen Sie das Azure-**Abonnement** Ihres IoT-Hubs und dann Ihren **IoT-Hub** aus.<br>Wählen Sie **Für Gerät bereitstellen** aus, um ein einzelnes Gerät als Bereitstellungsziel auszuwählen, oder wählen Sie **Im gewünschten Umfang bereitstellen** aus, um mehrere Geräte als Ziel festzulegen, und klicken Sie dann auf **Erstellen**. Weitere Informationen zur bedarfsorientierten Bereitstellung finden Sie im Thema zum [Bereitstellen](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Wenn Sie **Im gewünschten Umfang bereitstellen** ausgewählt haben, fügen Sie den Gerätenamen und die Details hinzu, bevor Sie in den folgenden Schritten mit der Registerkarte **Module hinzufügen** fortfahren.

Führen Sie jeden Schritt zum Erstellen einer IoT Edge-Bereitstellung für Defender für IoT aus.

#### <a name="step-1-modules"></a>Schritt 1: Module

1. Wählen Sie das Modul **AzureSecurityCenterforIoT** aus.
1. Ändern Sie auf der Registerkarte **Moduleinstellungen** den **Namen** in **azureiotsecurity**.
1. Fügen Sie auf der Registerkarte **Umgebungsvariablen** bei Bedarf eine Variable hinzu (z. B. auf Debugebene).
1. Fügen Sie auf der Registerkarte **Optionen für Containererstellung** die folgende Konfiguration hinzu:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Fügen Sie auf der Registerkarte **Einstellungen für Modulzwilling** die folgende Konfiguration hinzu:

   Eigenschaft für Modulzwilling:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Inhalt der Modulzwillingseigenschaft: 

   ```json
     {

     }
   ```
    
   Weitere Informationen zum Konfigurieren des Agents finden Sie unter [Tutorial: Konfigurieren von Sicherheits-Agents](./how-to-agent-configuration.md).

1. Wählen Sie **Update** aus.

#### <a name="step-2-runtime-settings"></a>Schritt 2: Runtimeeinstellungen

1. Wählen Sie **Runtimeeinstellungen** aus.
1. Ändern Sie unter **Edge Hub** das **Image** in **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Vergewissern Sie sich, dass **Erstellungsoptionen** auf die folgende Konfiguration festgelegt ist:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. Wählen Sie **Speichern** aus.

1. Wählen Sie **Weiter** aus.

#### <a name="step-3-specify-routes"></a>Schritt 3: Angeben von Routen

1. Stellen Sie auf der Registerkarte **Routen angeben** sicher, dass Sie über eine Route verfügen (explizit oder implizit), mit der Nachrichten vom Modul **azureiotsecurity** entsprechend den folgenden Beispielen an **$upstream** weitergeleitet werden. Wählen Sie erst, wenn die Route vorhanden ist, **Weiter** aus.

   Beispielrouten:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Wählen Sie **Weiter** aus.

#### <a name="step-4-review-deployment"></a>Schritt 4: Überprüfen der Bereitstellung

- Überprüfen Sie auf der Registerkarte **Bereitstellung überprüfen** die Bereitstellungsinformationen, und wählen Sie dann **Erstellen** aus, um die Bereitstellung abzuschließen.

## <a name="diagnostic-steps"></a>Schritte zum Diagnostizieren von Problemen

Falls ein Problem auftritt, sind Containerprotokolle die beste Möglichkeit, um sich über den Zustand eines Geräts mit einem IoT Edge-Sicherheitsmodul zu informieren. Verwenden Sie die Befehle und Tools in diesem Abschnitt zum Sammeln von Informationen.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Sicherstellen, dass die erforderlichen Container installiert sind und wie erwartet funktionieren

1. Führen Sie auf Ihrem IoT Edge-Gerät den folgenden Befehl aus:

    `sudo docker ps`

1. Stellen Sie sicher, dass die folgenden Container ausgeführt werden:

   | Name | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Falls die mindestens erforderlichen Container nicht vorhanden sind, überprüfen Sie, ob Ihr IoT Edge-Bereitstellungsmanifest den empfohlenen Einstellungen entspricht. Weitere Informationen finden Sie unter [Deploy IoT Edge module](#deployment-using-azure-portal) (Bereitstellen eines IoT Edge-Moduls).

### <a name="inspect-the-module-logs-for-errors"></a>Überprüfen der Modulprotokolle auf Fehler

1. Führen Sie auf Ihrem IoT Edge-Gerät den folgenden Befehl aus:

   `sudo docker logs azureiotsecurity`

1. Wenn Sie ausführlichere Protokolle benötigen, fügen Sie der Bereitstellung des Moduls **azureiotsecurity** die folgende Umgebungsvariable hinzu: `logLevel=Debug`.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Konfigurationsoptionen finden Sie in der Schrittanleitung für die Modulkonfiguration.
> [!div class="nextstepaction"]
> [Schrittanleitung für die Modulkonfiguration](./how-to-agent-configuration.md)