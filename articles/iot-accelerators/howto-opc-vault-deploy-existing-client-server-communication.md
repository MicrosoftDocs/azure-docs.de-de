---
title: Schützen der Anwendung für OPC UA-Clients und OPC UA-Server mithilfe von OPC Vault – Azure | Microsoft-Dokumentation
description: Schützen Sie die Anwendung für OPC UA-Clients und OPC UA-Server mit einem neuen Schlüsselpaar und Zertifikat über OPC Vault.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61450663"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Schützen der Anwendung für OPC UA-Clients und OPC UA-Server 
OPC Vault ist ein Microservice, mit dem der Zertifikatlebenszyklus für OPC UA-Server- und -Clientanwendungen in der Cloud konfiguriert, registriert und verwaltet werden kann. In diesem Artikel erfahren Sie, wie Sie eine Anwendung für OPC UA-Clients und OPC UA-Server mit einem neuen Schlüsselpaar und Zertifikat über OPC Vault schützen.

Während des folgenden Einrichtungsvorgangs testet OPC Client die Konnektivität mit OPC PLC. Standardmäßig ist die Konnektivität nicht möglich, da beide Komponenten noch nicht mit den richtigen Zertifikaten bereitgestellt wurden. In diesem Workflow werden die selbstsignierten Zertifikate der OPC UA-Komponenten nicht verwendet und über OPC Vault signiert. Weitere Informationen finden Sie im vorherigen [Testbed](howto-opc-vault-deploy-existing-client-plc-communication.md). Stattdessen stellt dieses Testbed die Komponenten mit einem neuen Zertifikat sowie mit einem neuen privaten Schlüssel bereit, die beide von OPC Vault generiert werden. Einige Hintergrundinformationen zur OPC UA-Sicherheit finden Sie in diesem [Whitepaper](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Die vollständigen Informationen finden Sie in der OPC UA-Spezifikation.

Testbed: Die folgende Umgebung ist für das Testen konfiguriert.

OPC Vault-Skripts:
- Schützen Sie die Anwendung für OPC UA-Clients und OPC UA-Server mit einem neuen Schlüsselpaar und Zertifikat über OPC Vault.

> [!NOTE]
> Weitere Informationen finden Sie im [GitHub-Repository](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Generieren eines neuen Zertifikats und eines privaten Schlüssels 
**Vorbereitung**
- Stellen Sie sicher, dass die Umgebungsvariablen `$env:_PLC_OPT` und `$env:_CLIENT_OPT` nicht definiert sind. Beispiel: `$env:_PLC_OPT=""` in Ihrer PowerShell
- Legen Sie die Umgebungsvariable `$env:_OPCVAULTID` auf eine Zeichenfolge fest, anhand der Sie Ihre Daten in OPC Vault wiederfinden können. Es wird empfohlen, dafür eine sechsstellige Zahl festzulegen. In unserem Beispiel wurde „123456“ als Wert für die Variable verwendet.
- Stellen Sie sicher, dass die Docker-Volumes `opcclient` und `opcplc` nicht vorhanden sind. Überprüfen Sie dies mit `docker volume ls`, und verwenden Sie `docker volume rm <volumename>` zum Entfernen. Unter Umständen müssen Sie auch Container mit `docker rm <containerid>` entfernen, wenn die Volumes noch von einem Container verwendet werden.

**Schnellstart**
1. Navigieren Sie zur [OPC Vault-Website](https://opcvault.azurewebsites.net/).

1. Wählen Sie `Register New`.

1. Geben Sie die OPC PLC-Informationen wie im Bereich `CreateSigningRequest information` der Protokollausgabe des vorherigen Testbeds in die Eingabefelder auf der Seite `Register New OPC UA Application` ein, und wählen Sie unter „ApplicationType“ die Option `Server` aus.

1. Wählen Sie `Register`.

1. Wählen Sie auf der nächsten Seite (`Request New Certificate for OPC UA Application`) die Option `Request new KeyPair and Certificate` aus.

1. Fügen Sie auf der nächsten Seite (`Generate a new Certificate with a Signing Request`) die Zeichenfolge `CSR (base64 encoded)` aus der Protokollausgabe in das Eingabefeld `CreateRequest` ein. Achten Sie darauf, dass Sie die vollständige Zeichenfolge kopieren.

1. Wählen Sie auf der nächsten Seite (`Request New Certificate for OPC UA Application`) die Option `Request new Certificate with Signing Request` aus.

1. Geben Sie auf der nächsten Seite (`Generate a new KeyPair and for an OPC UA Application`) `CN=OpcPlc` als „SubjectName“, `opcplc-<_OPCVAULTID>` (ersetzen Sie `<_OPCVAULTID>` durch Ihren Wert) als „DomainName“, wählen Sie `PEM` als „PrivateKeyFormat“ aus, und geben Sie ein Kennwort ein (Wir verwenden hierfür später die Bezeichnung `<certpassword-string>`).

1. Wählen Sie `Generate New KeyPair`.

1. Sie gelangen zu `View Certificate Request Details`. Auf dieser Seite können Sie alle erforderlichen Informationen zum Bereitstellen der Zertifikatspeicher von `opc-plc` herunterladen.

1. Auf dieser Seite:  
    - Wählen Sie unter `Download as Base64` die Option `Certificate`, kopieren Sie die Textzeichenfolge im Feld `EncodedBase64`, und speichern Sie sie zur späteren Verwendung. Wir verwenden hierfür später die Bezeichnung `<applicationcertbase64-string>`. Wählen Sie `Back` aus.
    - Wählen Sie unter `Download as Base64` die Option `PrivateKey` aus, kopieren Sie die Textzeichenfolge im Feld `EncodedBase64`, und speichern Sie sie zur späteren Verwendung. Wir verwenden hierfür später die Bezeichnung `<privatekeybase64-string>`. Wählen Sie `Back` aus.
    - Wählen Sie unter `Download as Base64` die Option `Issuer` aus, kopieren Sie die Textzeichenfolge im Feld `EncodedBase64`, und speichern Sie sie zur späteren Verwendung. Wir verwenden hierfür später die Bezeichnung `<addissuercertbase64-string>`. Wählen Sie `Back` aus.
    - Wählen Sie unter `Download as Base64` die Option `Crl` aus, kopieren Sie die Textzeichenfolge im Feld `EncodedBase64`, und speichern Sie sie zur späteren Verwendung. Wir verwenden hierfür später die Bezeichnung `<updatecrlbase64-string>`. Wählen Sie `Back` aus.

1. Legen Sie in PowerShell jetzt eine Variable mit dem Namen `$env:_PLC_OPT` fest:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Ersetzen Sie die als Optionswerte übergebenen Zeichenfolgen durch Base64-Zeichenfolgen, die Sie von der Website abgerufen haben.  

1. Wiederholen Sie den gesamten Prozess, indem Sie mit `Register New` für OPC Client beginnen. Beachten Sie hierbei lediglich die folgenden Unterschiede:
    - Verwenden Sie die Protokollausgabe aus `opcclient`.
    - Wählen Sie während der Registrierung unter „ApplicationType“ die Option `Client` aus.
    - Verwenden Sie `$env:_CLIENT_OPT` als Name für die PowerShell-Variable.

    > [!NOTE] 
    > Beim Durcharbeiten dieses Szenarios haben Sie vielleicht bemerkt, dass die Werte `<addissuercertbase64-string>` und `<updatecrlbase64-string>` für `opcplc` und `opcclient` identisch sind. Dies gilt auch für unseren Anwendungsfall und kann Zeit beim Ausführen der Schritte sparen.

**Schnellstart**

Führen Sie den folgenden PowerShell-Befehl im Repositorystamm aus:

```
docker-compose -f connecttest.yml up
```

**Überprüfung**

Stellen Sie sicher, dass die beiden Komponenten nicht über ein signiertes Anwendungszertifikat verfügen. Prüfen Sie die Protokollausgabe. Unten ist die Ausgabe von OPC PLC angegeben, und OPC Client verfügt über eine ähnliche Protokollausgabe.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Wenn ein Anwendungszertifikat vorhanden ist, entfernen Sie die Docker-Volumes, wie in den Vorbereitungsschritten beschrieben.

Überprüfen Sie im Protokoll, ob das Zertifikat der OPC Vault-Zertifizierungsstelle sowohl im Zertifikatspeicher des Ausstellers als auch im vertrauenswürdigen Peerzertifikatspeicher installiert wurde. Unten ist die Protokollausgabe von OPC PLC angegeben, und OPC Client verfügt über eine ähnliche Protokollausgabe. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC PLC vertraut jetzt allen OPC UA-Clients mit Zertifikaten, die von OPC Vault signiert wurden.

Überprüfen Sie im Protokoll, ob das Format des privaten Schlüssels als PEM erkannt wird und ob das neue Anwendungszertifikat installiert ist. Unten ist die Protokollausgabe von OPC PLC angegeben, und OPC Client verfügt über eine ähnliche Protokollausgabe. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Das Anwendungszertifikat und der private Schlüssel werden jetzt im Anwendungszertifikatsspeicher installiert und von der OPC UA-Anwendung verwendet.

Vergewissern Sie sich, dass die Verbindung zwischen dem OPC-Client und OPC PLC erfolgreich hergestellt werden kann und der OPC-Client erfolgreich Daten aus OPC PLC lesen kann. Die folgende Ausgabe sollte in der Protokollausgabe des OPC-Clients angezeigt werden:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Wenn diese Ausgabe angezeigt wird, ist dies ein Hinweis darauf, dass der OPC Client von OPC PLC jetzt als vertrauenswürdig angesehen wird (und umgekehrt). Beide verfügen jetzt über Zertifikate, die von einer Zertifizierungsstelle signiert wurden, und beide sehen die von der Zertifizierungsstelle signierten Zertifikate als vertrauenswürdig an.

### <a name="a-testbed-for-opc-publisher"></a>Testbed für OPC Publisher ###

**Schnellstart**

Führen Sie den folgenden PowerShell-Befehl im Repositorystamm aus:
```
docker-compose -f testbed.yml up
```

**Überprüfung**
- Vergewissern Sie sich, dass Daten an den IoTHub gesendet werden, den Sie durch Festlegen von `_HUB_CS` mit [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) oder [iothub-explorer](https://github.com/Azure/iothub-explorer) konfiguriert haben.
- OPC Test-Client verwendet direkte IoTHub-Methodenaufrufe und OPC-Methodenaufrufe, um OPC Publisher so zu konfigurieren, dass Knoten vom OPC Testserver aus veröffentlicht bzw. deinstalliert werden.
- Beobachten Sie die Ausgabe auf Fehlermeldungen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie OPC Vault für ein vorhandenes Projekt bereitstellen, lautet unser Vorschlag für den nächsten Schritt:

> [!div class="nextstepaction"]
> [Bereitstellen von OPC Twin in einem vorhandenen Projekt](howto-opc-twin-deploy-existing.md)