---
title: 'Schützen der Kommunikation von OPC-Client und OPC PLC mit OPC Vault: Azure | Microsoft-Dokumentation'
description: Hier wird beschrieben, wie Sie die Kommunikation von OPC Client und OPC PLC schützen, indem Sie die zugehörigen Zertifikate per OPC Vault-Zertifizierungsstelle signieren.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61451088"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Schützen der Kommunikation von OPC-Client und OPC PLC

OPC Vault ist ein Microservice, mit dem der Zertifikatlebenszyklus für OPC UA-Serveranwendungen und -Clientanwendungen in der Cloud konfiguriert, registriert und verwaltet werden kann. In diesem Artikel wird veranschaulicht, wie Sie die Kommunikation von OPC-Client und OPC PLC schützen, indem Sie die zugehörigen Zertifikate per OPC Vault-Zertifizierungsstelle signieren.

Während des folgenden Einrichtungsvorgangs testet der OPC-Client die Konnektivität mit OPC PLC. Standardmäßig ist die Konnektivität nicht möglich, da beide Komponenten nicht mit den richtigen Zertifikaten bereitgestellt wurden. Wenn eine OPC UA-Komponente noch nicht mit einem Zertifikat bereitgestellt wurde, wird beim Starten ein selbstsigniertes Zertifikat generiert. Das Zertifikat kann aber von einer Zertifizierungsstelle signiert und auf der OPC UA-Komponente installiert werden. Nachdem dies für den OPC-Client und OPC PLC durchgeführt wurde, wird die Konnektivität aktiviert. Im folgenden Workflow ist der Prozess beschrieben. Einige Hintergrundinformationen zur OPC UA-Sicherheit finden Sie in [diesem Whitepaper](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Die vollständigen Informationen finden Sie in der OPC UA-Spezifikation.

Testbed: Die folgende Umgebung ist für das Testen konfiguriert.

OPC Vault-Skripts:
- Schützen der Kommunikation von OPC-Client und OPC PLC, indem die zugehörigen Zertifikate per OPC Vault-Zertifizierungsstelle signiert werden.

> [!NOTE]
> Weitere Informationen finden Sie im [GitHub-Repository](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Generieren eines selbstsignierten Zertifikats beim Start

**Vorbereitung**

- Stellen Sie sicher, dass die Umgebungsvariablen `$env:_PLC_OPT` und `$env:_CLIENT_OPT` nicht definiert sind, z. B. `$env:_PLC_OPT=""` in PowerShell.

- Legen Sie die Umgebungsvariable `$env:_OPCVAULTID` auf eine Zeichenfolge fest, anhand der Sie Ihre Daten in OPC Vault wiederfinden können. Hierbei sind nur alphanumerische Zeichen zulässig. In unserem Beispiel wurde „123456“ als Wert für diese Variable verwendet.

- Stellen Sie sicher, dass die Docker-Volumes `opcclient` und `opcplc` nicht vorhanden sind. Überprüfen Sie dies mit `docker volume ls`, und verwenden Sie `docker volume rm <volumename>` zum Entfernen. Unter Umständen müssen Sie auch Container mit `docker rm <containerid>` entfernen, wenn die Volumes noch von einem Container verwendet werden.

**Schnellstart**

Führen Sie den folgenden PowerShell-Befehl im Repositorystamm aus:

```
docker-compose -f connecttest.yml up
```

**Überprüfung**

Stellen Sie im Protokoll sicher, dass beim ersten Start keine Zertifikate installiert werden. Hier ist die Protokollausgabe von OPC PLC angegeben (ähnliche Ausgabe für den OPC-Client):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Führen Sie die obigen Vorbereitungsschritte aus, und löschen Sie die Docker-Volumes, wenn Sie sehen, dass Zertifikate gemeldet werden.

Vergewissern Sie sich, dass für die Verbindung mit OPC PLC ein Fehler aufgetreten ist. Die folgende Ausgabe sollte in der Protokollausgabe des OPC-Clients angezeigt werden:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
Der Grund für den Fehler ist, dass das Zertifikat nicht vertrauenswürdig ist. Dies bedeutet Folgendes: `opc-client` hat versucht, eine Verbindung mit `opc-plc` herzustellen, und eine Antwort mit dem Hinweis erhalten, dass `opc-client` für `opc-plc` nicht vertrauenswürdig ist. Die Ursache hierfür ist, dass `opc-plc` das von `opc-client` bereitgestellte Zertifikat nicht überprüfen konnte. Da dies ein selbstsigniertes Zertifikat ohne weitere Zertifikatkonfiguration unter `opc-plc` ist, ist für die Verbindung ein Fehler aufgetreten.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Signieren und Installieren von Zertifikaten in OPC UA-Komponenten

**Vorbereitung**
1. Sehen Sie sich die Protokollausgabe von Schritt 1 an, und rufen Sie die „CreateSigningRequest-Informationen“ für OPC PLC und den OPC-Client ab. Hier ist nur die Ausgabe für OPC PLC angegeben:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Navigieren Sie zur [OPC Vault-Website](https://opcvault.azurewebsites.net/).

1. Wählen Sie `Register New`.

1. Geben Sie die OPC PLC-Informationen aus dem Bereich `CreateSigningRequest information` der Protokollausgabe in die Eingabefelder auf der Seite `Register New OPC UA Application` ein, und wählen Sie unter „ApplicationType“ die Option `Server`.

1. Wählen Sie `Register` aus.

1. Wählen Sie auf der nächsten Seite (`Request New Certificate for OPC UA Application`) die Option `Request new Certificate with Signing Request`.

1. Fügen Sie auf der nächsten Seite (`Generate a new Certificate with a Signing Request`) die Zeichenfolge `CSR (base64 encoded)` aus der Protokollausgabe in das Eingabefeld `CreateRequest` ein. Achten Sie darauf, dass Sie die vollständige Zeichenfolge kopieren.

1. Wählen Sie `Generate New Certificate` aus.

1. Sie gelangen zu `View Certificate Request Details`. Auf dieser Seite können Sie alle erforderlichen Informationen zum Bereitstellen der Zertifikatspeicher von `opc-plc` herunterladen.

1. Auf dieser Seite:  
    - Wählen Sie unter `Download as Base64` die Option `Certificate`, kopieren Sie die Textzeichenfolge im Feld `EncodedBase64`, und speichern Sie sie zur späteren Verwendung. Wir verwenden hierfür später die Bezeichnung `<applicationcertbase64-string>`. Wählen Sie `Back` aus.

    - Wählen Sie unter `Download as Base64` die Option `Issuer` aus, kopieren Sie die Textzeichenfolge im Feld `EncodedBase64`, und speichern Sie sie zur späteren Verwendung. Wir verwenden hierfür später die Bezeichnung `<addissuercertbase64-string>`. Wählen Sie `Back` aus.

    - Wählen Sie unter `Download as Base64` die Option `Crl` aus, kopieren Sie die Textzeichenfolge im Feld `EncodedBase64`, und speichern Sie sie zur späteren Verwendung. Wir verwenden hierfür später die Bezeichnung `<updatecrlbase64-string>`. Wählen Sie `Back` aus.

1. Legen Sie in PowerShell jetzt eine Variable mit dem Namen `$env:_PLC_OPT` fest:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Ersetzen Sie die als Optionswerte übergebenen Zeichenfolgen durch Base64-Zeichenfolgen, die Sie von der Website abgerufen haben.

Wiederholen Sie den gesamten Prozess, indem Sie mit `Register New` (Schritt 3 oben) für den OPC-Client beginnen. Beachten Sie hierbei lediglich die folgenden Unterschiede:

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

**Überprüfung** Stellen Sie sicher, dass die beiden Komponenten nun über signierte Anwendungszertifikate verfügen. Überprüfen Sie die Protokollausgabe auf folgende Ausgabedaten:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Das Anwendungszertifikat ist vorhanden und von einer Zertifizierungsstelle signiert.

Vergewissern Sie sich im Protokoll, dass nun Zertifikate installiert sind. Unten ist die Protokollausgabe von OPC PLC angegeben, und der OPC-Client verfügt über eine ähnliche Ausgabe.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
Der Aussteller des Anwendungszertifikats ist die Zertifizierungsstelle `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.`, und OPC PLC vertraut auch allen Zertifikaten, die von dieser Zertifizierungsstelle signiert wurden.


Vergewissern Sie sich, dass die Verbindung mit OPC PLC erfolgreich hergestellt wurde und dass der OPC-Client Daten von OPC PLC lesen kann. Die folgende Ausgabe sollte in der Protokollausgabe des OPC-Clients angezeigt werden:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Wenn diese Ausgabe angezeigt wird, ist dies ein Hinweis darauf, dass der OPC-Client von OPC PLC jetzt als vertrauenswürdig angesehen wird (und umgekehrt). Beide verfügen jetzt über Zertifikate, die von einer Zertifizierungsstelle signiert wurden, und beide sehen die von der Zertifizierungsstelle signierten Zertifikate als vertrauenswürdig an.

> [!NOTE] 
> Die ersten beiden Überprüfungsschritte wurden hier zwar nur für OPC PLC angegeben, aber sie müssen auch für den OPC-Client ausgeführt werden.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie OPC Vault für ein vorhandenes Projekt bereitstellen, lautet unser Vorschlag für den nächsten Schritt:

> [!div class="nextstepaction"]
> [Bereitstellen von OPC Twin in einem vorhandenen Projekt](howto-opc-twin-deploy-existing.md)