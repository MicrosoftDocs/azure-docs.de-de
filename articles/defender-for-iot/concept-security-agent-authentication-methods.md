---
title: Authentifizierungsmethoden des Sicherheits-Agents
description: Lernen Sie die verschiedenen Authentifizierungsmethoden kennen, die für den Defender für IoT-Dienst verfügbar sind.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: c72fa87201fb0d7fdd526f0e6f4eeb2c35192fc2
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521644"
---
# <a name="security-agent-authentication-methods"></a>Authentifizierungsmethoden des Sicherheits-Agents

In diesem Artikel werden die verschiedenen Authentifizierungsmethoden erläutert, die Sie mit dem AzureIoTSecurity-Agent für die Authentifizierung beim IoT Hub verwenden können.

Für jedes Gerät, das im IoT Hub in Defender für IoT integriert ist, ist ein Sicherheitsmodul erforderlich. Für die Authentifizierung des Geräts kann Defender für IoT eine der beiden Methoden verwenden. Wählen Sie die Methode aus, die sich am besten für Ihre vorhandene IoT-Lösung eignet.

> [!div class="checklist"]
> * Option „SecurityModule“
> * Geräteoption

## <a name="authentication-methods"></a>Authentifizierungsmethoden

Für den AzureIoTSecurity-Agent von Azure Defender für IoT gibt es die beiden folgenden Authentifizierungsmethoden:

- Authentifizierungsmodus **SecurityModule**<br>
Der Agent wird unabhängig von der Geräteidentität mithilfe der Sicherheitsmodulidentität authentifiziert.
Verwenden Sie diesen Authentifizierungstyp, wenn der Sicherheits-Agent eine dedizierte Authentifizierungsmethode über Sicherheitsmodul (nur symmetrischer Schlüssel) verwenden soll.

- Authentifizierungsmodus **Gerät**<br>
Bei dieser Methode wird der Sicherheits-Agent zunächst mit der Geräteidentität authentifiziert. Nach der ersten Authentifizierung führt der Defender für IoT-Agent den Aufruf **REST** am IoT Hub durch und verwendet dabei die REST-API mit den Authentifizierungsdaten des Geräts. Der Defender für IoT-Agent fordert dann die Authentifizierungsmethode und -daten des Sicherheitsmoduls vom IoT Hub an. Im letzten Schritt nimmt der Defender für IoT-Agent eine Authentifizierung beim Defender für IoT-Modul vor.

Verwenden Sie diesen Authentifizierungstyp, wenn der Sicherheits-Agent eine vorhandene Geräteauthentifizierungsmethode (selbstsigniertes Zertifikat oder symmetrischer Schlüssel) erneut verwenden soll.

Informationen zur Konfiguration finden Sie unter [Security agent installation parameters (Installationsparameter für den Sicherheits-Agent)](#security-agent-installation-parameters).

## <a name="authentication-methods-known-limitations"></a>Bekannte Einschränkungen von Authentifizierungsmethoden

- Der Authentifizierungsmodus **SecurityModule** unterstützt nur die Authentifizierung mit symmetrischen Schlüsseln.
- Der Authentifizierungsmodus **Gerät** unterstützt kein von der Zertifizierungsstelle signiertes Zertifikat.

## <a name="security-agent-installation-parameters"></a>Installationsparameter für den Sicherheits-Agent

Beim [Bereitstellen eines Sicherheits-Agents](how-to-deploy-agent.md) müssen Authentifizierungsdetails als Argumente angegeben werden.
Diese Argumente werden in der folgenden Tabelle gezeigt.

|Linux-Parametername | Windows-Parametername | Kurzform für Parameter |BESCHREIBUNG|Tastatur|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity|AuthenticationIdentity|aui|Authentifizierungsidentität| **SecurityModule** oder **Device**|
|authentication-method|AuthenticationMethod|aum|Authentifizierungsmethode|**SymmetricKey** oder **SelfSignedCertificate**|
|file-path|FilePath|f|Vollständiger Pfad der Datei, die das Zertifikat oder den symmetrischen Schlüssel enthält| |
|host-name|HostName|hn|Vollqualifizierter Domänenname (FQDN) des IoT Hubs|Beispiel: ContosoIotHub.azure-devices.net|
|device-id|deviceId|di|Geräte-ID|Beispiel: MyDevice1|
|certificate-location-kind|CertificateLocationKind|cl|Speicherort des Zertifikats|**LocalFile** oder **Store**|
|

Wenn Sie das Installationsskript des Sicherheits-Agents verwenden, wird die folgende Konfiguration automatisch ausgeführt. Um die Authentifizierung des Sicherheits-Agents manuell zu bearbeiten, bearbeiten Sie die Konfigurationsdatei.

## <a name="change-authentication-method-after-deployment"></a>Ändern der Authentifizierungsmethode nach der Bereitstellung

Wenn Sie einen Sicherheits-Agent mit einem Installationsskript bereitstellen, wird automatisch eine Konfigurationsdatei erstellt.

Um Authentifizierungsmethoden nach der Bereitstellung zu ändern, muss die Konfigurationsdatei manuell bearbeitet werden.

### <a name="c-based-security-agent"></a>C#-basierter Sicherheits-Agent

Bearbeiten Sie _Authentication.config_ mit den folgenden Parametern:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C-basierter Sicherheits-Agent

Bearbeiten Sie _LocalConfiguration.json_ mit den folgenden Parametern:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```
## <a name="next-steps"></a>Nächste Schritte

- [Security agents overview (Sicherheits-Agents (Übersicht))](security-agent-architecture.md)
- [Deploy security agent (Bereitstellen eines Sicherheits-Agents)](how-to-deploy-agent.md)
- [Access raw security data (Zugreifen auf Sicherheitsrohdaten)](how-to-security-data-access.md)
