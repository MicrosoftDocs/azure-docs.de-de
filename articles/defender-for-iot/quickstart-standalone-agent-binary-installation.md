---
title: Installieren eines Micro-Agents für Defender für IoT (Vorschau)
titleSuffix: Azure Defender for IoT
description: Hier erfahren Sie, wie Sie den Micro-Agent für Defender installieren und authentifizieren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 0841bbd8baa524d3eea3afcbffc0aa5ead41409e
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810048"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>Installieren eines Micro-Agents für Defender für IoT (Vorschau)

In diesem Artikel erfahren Sie, wie Sie den Micro-Agent für Defender installieren und authentifizieren.

## <a name="prerequisites"></a>Voraussetzungen

Vor der Installation des Moduls „Defender für IoT“ muss im IoT Hub eine Modulidentität erstellt werden. Weitere Informationen zum Erstellen einer Modulidentität finden Sie unter [Schnellstart: Erstellen eines Azure IoT-Sicherheitsmodulzwillings](quickstart-create-security-twin.md).

## <a name="install-the-package"></a>Installieren des Pakets

Installieren und konfigurieren Sie das Microsoft-Paketrepository gemäß [dieser Anleitung](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software). 

Das für Debian 9 erforderliche Repository ist in der Anleitung nicht enthalten. Verwenden Sie die folgenden Befehle, um es hinzuzufügen: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Verwenden Sie den folgenden Befehl, um das Paket des Micro-Agents für Defender unter Debian oder unter einer Ubuntu-basierten Linux-Distribution zu installieren:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Authentifizierungsmethoden für den Micro-Agent 

Zur Authentifizierung des Micro-Agents für Defender für IoT werden die beiden folgenden Optionen verwendet: 

- Verbindungszeichenfolge. 

- Zertifikat

### <a name="authenticate-using-a-connection-string"></a>Authentifizieren mithilfe einer Verbindungszeichenfolge

So verwenden Sie die Authentifizierung per Verbindungszeichenfolge:

1. Platzieren Sie eine Datei mit dem Namen `connection_string.txt`, die die UTF-8-codierte Verbindungszeichenfolge enthält, im Verzeichnis `/var/defender_iot_micro_agent` des Defender-Agents. Geben Sie hierzu den folgenden Befehl ein:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    Die Datei `connection_string.txt` sollte sich nun am folgenden Pfad befinden: `/var/defender_iot_micro_agent/connection_string.txt`.

1. Starten Sie den Dienst mithilfe des folgenden Befehls neu:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Authentifizieren mithilfe eines Zertifikats

So verwenden Sie die Authentifizierung per Zertifikat:

1. Gehen Sie wie [hier](../iot-hub/iot-hub-security-x509-get-started.md) beschrieben vor, um ein Zertifikat zu erhalten.

1. Platzieren Sie den PEM-codierten öffentlichen Teil des Zertifikats und den privaten Schlüssel im Defender-Agent-Verzeichnis in der Datei `certificate_public.pem`bzw. `certificate_private.pem`. 

1. Platzieren Sie die passende Verbindungszeichenfolge in der Datei `connection_string.txt`. Die Verbindungszeichenfolge sollte wie folgt aussehen: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Durch diese Zeichenfolge wird der Defender-Agent darauf vorbereitet, dass ein Zertifikat für die Authentifizierung bereitgestellt wird. 

1. Starten Sie den Dienst mithilfe des folgenden Befehls neu:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Überprüfen Ihrer Installation

So überprüfen Sie Ihre Installation:

1. Vergewissern Sie sich mithilfe des folgenden Befehls, dass der Micro-Agent ordnungsgemäß ausgeführt wird:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Vergewissern Sie sich, dass der Dienst stabil ist (`active` und angemessene Uptime des Prozesses):

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Vergewissern, dass Ihr Dienst stabil und aktiv ist.":::
 
## <a name="testing-the-system-end-to-end"></a>Umfassendes Testen des Systems 

Sie können das System umfassend testen, indem Sie auf dem Gerät eine Triggerdatei erstellen. Die Triggerdatei sorgt dafür, dass die Baselineüberprüfung im Agent die Datei als Baselineverstoß erkennt. 

Erstellen Sie mithilfe des folgenden Befehls eine Datei im Dateisystem:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Im Hub wird eine Empfehlung für einen Baselinevalidierungsfehler angezeigt. `CceId` hat hierbei den Wert „CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0“: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Empfehlung für Baselinevalidierungsfehler im Hub" lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Es kann bis zu einer Stunde dauern, bis die Empfehlung im Hub angezeigt wird. 

## <a name="micro-agent-versioning"></a>Versionsverwaltung für den Micro-Agent 

Wenn Sie eine bestimmte Version des Micro-Agents für Defender für IoT installieren möchten, führen Sie den folgenden Befehl aus: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen des Micro-Agents für Defender auf der Grundlage von Quellcode](quickstart-building-the-defender-micro-agent-from-source.md)
