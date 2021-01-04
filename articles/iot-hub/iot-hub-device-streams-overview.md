---
title: Azure IoT Hub-Gerätestreams | Microsoft-Dokumentation
description: Eine Übersicht über Azure IoT Hub-Gerätestreams, die sichere bidirektionale TCP-Tunnel für eine Vielzahl von Szenarios mit Kommunikation zwischen Cloud und Gerät vereinfachen.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- devx-track-azurecli
ms.openlocfilehash: bdd9d5fd878094326331e60fc1a639eef08b7ea3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792462"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub-Gerätestreams (Vorschau)

Azure IoT Hub- *Gerätestreams* vereinfachen das Erstellen sicherer bidirektionaler TCP-Tunnel für eine Vielzahl von Szenarios mit Kommunikation zwischen Cloud und Gerät. Ein Gerätestream wird durch einen IoT Hub- *Streamingendpunkt* vermittelt, der als Proxy zwischen Ihrem Gerät und den Dienstendpunkten fungiert. Dieses Setup ist im folgenden Diagramm dargestellt. Es eignet sich besonders, wenn die Geräte sich hinter einer Netzwerkfirewall oder in einem privaten Netzwerk befinden. So erfüllen IoT Hub-Gerätestreams die Kundenanforderung, IoT-Geräte firewallfreundlich zu erreichen, ohne Eingangs- und Ausgangsports der Netzwerkfirewall weit zu öffnen.

![„Übersicht über IoT Hub-Gerätestreams“](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Mithilfe von IoT Hub-Gerätestreams bleiben Geräte geschützt und müssen nur TCP-Ausgangsverbindungen für den IoT Hub-Streamingendpunkt über Port 443 öffnen. Nachdem ein Stream eingerichtet wurde, besitzen die dienstseitigen und geräteseitigen Anwendungen jeweils programmgesteuerten Zugriff auf ein WebSocket-Clientobjekt, um sich gegenseitig Rohbytes zu senden bzw. zu empfangen. Die durch diesen Tunnel bereitgestellte Zuverlässigkeit und garantierte Reihenfolge ist gleichwertig mit TCP.

## <a name="benefits"></a>Vorteile

IoT Hub-Gerätestreams bieten folgende Vorteile:

* **Firewallfreundliche, sichere Konnektivität:** IoT-Geräte können von Dienstendpunkten aus erreicht werden, ohne auf dem Gerät oder im Netzwerkumkreis einen Eingangsfirewallport zu öffnen (nur Ausgangskonnektivität zu IoT Hub über Port 443 ist erforderlich).

* **Authentifizierung:** Sowohl die Geräte- als auch die Dienstseite des Tunnels müssen sich bei IoT Hub mit den entsprechenden Anmeldeinformationen authentifizieren.

* **Verschlüsselung:** Standardmäßig verwenden IoT Hub-Gerätestreams TLS-aktivierte Verbindungen. Dadurch wird sichergestellt, dass der Datenverkehr immer verschlüsselt ist, unabhängig davon, ob die Anwendung Verschlüsselung verwendet.

* **Einfache Konnektivität:** Durch die Verwendung von Gerätestreams ist zum Herstellen von Konnektivität mit IoT-Geräten in vielen Fällen kein komplexes Setup virtueller privater Netzwerke mehr erforderlich.

* **Kompatibilität mit TCP/IP-Stapel:** IoT Hub-Gerätestreams können TCP/IP-Anwendungsdatenverkehr verarbeiten. Dies bedeutet, dass dieses Feature von einer Vielzahl proprietärer sowie auf Standards basierender Protokolle genutzt werden kann.

* **Einfache Verwendung in privaten Netzwerksetups:** Der Dienst kann mit einem Dienst kommunizieren, indem auf seine Geräte-ID verwiesen wird, anstatt auf die IP-Adresse des Geräts. Dies ist hilfreich in Situationen, in denen sich ein Gerät in einem privaten Netzwerk befindet und über eine private IP-Adresse verfügt oder die IP-Adresse dynamisch zugewiesen wird und der Dienstseite unbekannt ist.

## <a name="device-stream-workflows"></a>Gerätestreamworkflows

Ein Gerätestream wird initiiert, wenn der Dienst eine Verbindung mit einem Gerät durch Bereitstellung der Geräte-ID anfordert. Dieser Workflow passt besonders gut zum Client/Server-Kommunikationsmodell, einschließlich SSH und RDP, in dem ein Benutzer über ein SSH- oder RDP-Clientprogramm eine Remoteverbindung mit dem SSH- oder RDP-Server herstellen möchte, der auf dem Gerät ausgeführt wird.

Der Erstellungsvorgang für Gerätestreams umfasst eine Aushandlung zwischen dem Gerät, dem Dienst, dem IoT Hub-Hauptendpunkt und den Streamingendpunkten. Während der IoT Hub-Hauptendpunkt die Erstellung eines Gerätestreams orchestriert, verarbeitet der Streamingendpunkt den Datenverkehr zwischen dem Dienst und dem Gerät.

### <a name="device-stream-creation-flow"></a>Ablauf zur Gerätestreamerstellung

Die programmgesteuerte Erstellung eines Gerätestreams mit dem SDK umfasst die folgenden Schritte, die auch in der folgenden Abbildung dargestellt werden:

![„Handshakeprozess des Gerätestreams“](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Die Geräteanwendung registriert einen Rückruf, um im Voraus benachrichtigt zu werden, wenn ein neuer Gerätestream zum Gerät initiiert wird. Dieser Schritt findet in der Regel statt, wenn das Gerät gestartet wird und eine Verbindung mit der IoT Hub-Instanz herstellt.

2. Das dienstseitige Programm initiiert bei Bedarf einen Gerätestream durch Bereitstellen der Geräte-ID ( _nicht_ der IP-Adresse).

3. IoT Hub benachrichtigt das dienstseitige Programm durch Aufrufen des in Schritt 1 registrierten Rückrufs. Das Gerät kann die Anforderung der Streaminitiierung annehmen oder ablehnen. Diese Logik kann speziell auf Ihr Anwendungsszenario zugeschnitten sein. Wenn die Streamanforderung vom Gerät abgelehnt wird, informiert IoT Hub den Dienst entsprechend. Andernfalls folgen die unten aufgeführten Schritte.

4. Das Gerät erstellt über Port 443 eine sichere ausgehende TCP-Verbindung mit dem Streamingendpunkt und führt ein Upgrade der Verbindung auf WebSocket durch. Die URL des Streamingendpunkts sowie die zum Authentifizieren verwendeten Anmeldeinformationen werden dem Gerät von IoT Hub im Rahmen der Anforderung bereitgestellt, die in Schritt 3 gesendet wurde.

5. Der Dienst wird über das Ergebnis der Annahme des Streams durch das Gerät benachrichtigt und erstellt dann einen eigenen WebSocket-Client für den Streamingendpunkt. Auf ähnliche Weise empfängt er die Streamingendpunkt-URL und die Authentifizierungsinformationen von IoT Hub.

Beim obigen Handshakevorgang:

* Der Handshakevorgang muss innerhalb von 60 Sekunden abgeschlossen werden (Schritt 2 bis 5). Andernfalls tritt ein Timeoutfehler auf, und der Dienst wird entsprechend benachrichtigt.

* Nach Abschluss des obigen Ablaufs zur Streamerstellung fungiert der Streamingendpunkt als Proxy und überträgt Datenverkehr zwischen dem Dienst und dem Gerät über die jeweiligen WebSockets.

* Gerät und Dienst benötigen ausgehende Konnektivität über Port 443 mit dem wichtigsten IoT Hub-Endpunkt sowie dem Streamingendpunkt. Die URL dieser Endpunkte ist auf der Registerkarte *Übersicht* im IoT Hub-Portal verfügbar.

* Die Zuverlässigkeit und garantierte Reihenfolge eines eingerichteten Streams ist gleichwertig mit TCP.

* Alle Verbindungen mit IoT Hub und dem Streamingendpunkt verwenden TLS und sind verschlüsselt.

### <a name="termination-flow"></a>Beendigungsablauf

Ein eingerichteter Stream wird beendet, wenn eine der TCP-Verbindungen mit dem Gateway (durch den Dienst oder das Gerät) getrennt wird. Dies kann durch Schließen des WebSockets im Geräte- oder Dienstprogramm geplant stattfinden oder unbeabsichtigt aufgrund eines Timeouts bei der Netzwerkkonnektivität oder aufgrund eines Prozessfehlers. Bei Beendigung einer der Verbindungen (Gerät oder Dienst) mit dem Streamingendpunkt wird auch die andere TCP-Verbindung zwangsweise beendet, und Dienst und Gerät sind dafür zuständig, den Stream bei Bedarf neu zu erstellen.

## <a name="connectivity-requirements"></a>Konnektivitätsanforderungen

Sowohl die Geräte- als auch die Dienstseite eines Gerätestreams muss dazu in der Lage sein, TLS-fähige Verbindungen mit IoT Hub und dem zugehörigen Streamingendpunkt herzustellen. Hierfür ist eine ausgehende Konnektivität über Port 443 mit diesen Endpunkten erforderlich. Den diesen Endpunkten zugeordneten Hostnamen finden Sie auf der IoT Hub-Registerkarte *Übersicht* , wie in der folgenden Abbildung gezeigt:

![„Gerätestreamingendpunkte“](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternativ dazu können die Endpunktinformationen über die Azure CLI unter dem Abschnitt mit dem Hubeigenschaften abgerufen werden, insbesondere über die Schlüssel `property.hostname` und `property.deviceStreams`.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Die Ausgabe ist ein JSON-Objekt aller Endpunkte, mit denen das Gerät und der Dienst Ihres Hubs möglicherweise eine Verbindung herstellen muss, um einen Gerätestream einzurichten.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Stellen Sie sicher, dass mindestens Version 2.0.57 der Azure-Befehlszeilenschnittstelle installiert ist. Sie können die neueste Version über die Seite [Azure CLI installieren](/cli/azure/install-azure-cli) herunterladen.
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Erlauben ausgehender Konnektivität zu den Gerätestreamingendpunkten

Wie zu Anfang des Artikels bereits erwähnt, stellt Ihr Gerät während des Initiierungsprozesses für Gerätestreams eine ausgehende Verbindung mit dem IoT Hub-Streamingendpunkt her. Die Firewalls auf dem Gerät oder im Netzwerk müssen ausgehende Konnektivität mit dem Streaminggateway über Port 443 zulassen. (Beachten Sie, dass die Kommunikation hierbei über eine WebSocket-Verbindung erfolgt, die per TLS verschlüsselt ist.)

Den Hostnamen des Gerätestreamingendpunkts finden Sie im Azure IoT Hub-Portal auf der Registerkarte „Übersicht“. ![„Gerätestreamingendpunkte“](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternativ finden Sie diese Informationen mithilfe der Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Stellen Sie sicher, dass mindestens Version 2.0.57 der Azure-Befehlszeilenschnittstelle installiert ist. Sie können die neueste Version über die Seite [Azure CLI installieren](/cli/azure/install-azure-cli) herunterladen.
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Problembehandlung über Ressourcenprotokolle für Gerätestreams

Sie können Azure Monitor so einrichten, dass die von Ihrem IoT Hub ausgegebenen [Ressourcenprotokolle für Gerätestreams](monitor-iot-hub-reference.md#device-streams-preview) erfasst werden. Dies kann zur Problembehandlung sehr hilfreich sein.

Führen Sie die folgenden Schritte aus, um eine Diagnoseeinstellung zum Senden von Gerätestreamprotokollen für Ihren IoT Hub an Azure Monitor-Protokolle zu erstellen:

1. Navigieren Sie im Azure-Portal zu Ihrem IoT-Hub. Wählen Sie im linken Bereich unter **Überwachung** die Option **Diagnoseeinstellungen** aus. Wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.

2. Geben Sie einen Namen für Ihre Diagnoseeinstellung an, und wählen Sie aus der Liste der Protokolle das Protokoll **DeviceStreams** aus. Wählen Sie dann **An Log Analytics senden** aus. Sie werden durch die Auswahl eines vorhandenen Log Analytics-Arbeitsbereichs oder die Erstellung eines neuen Bereichs geführt.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Aktivieren von Gerätestreamprotokollen":::

3. Nachdem Sie eine Diagnoseeinstellung zum Senden Ihrer Gerätestreamprotokolle an einen Log Analytics-Arbeitsbereich erstellt haben, können Sie auf die Protokolle zugreifen, indem Sie im Azure-Portal im linken Bereich Ihres IoT-Hubs unter **Überwachung** die Option **Protokolle** auswählen. Gerätestreamprotokolle werden in der Tabelle `AzureDiagnostics` mit `Category=DeviceStreams` aufgeführt. Beachten Sie, dass es mehrere Minuten dauern kann, bis die Protokolle in der Tabelle angezeigt werden.

   Wie unten dargestellt, werden auch die Identität des Zielgeräts und das Ergebnis des Vorgangs in den Protokollen angegeben.

   ![„Zugriff auf Gerätestreamprotokolle“](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Weitere Informationen zur Verwendung von Azure Monitor bei IoT Hub finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md). Informationen zu allen Ressourcenprotokollen, Metriken und Tabellen, die für IoT Hub zur Verfügung stehen, finden Sie in der [Referenz zu Azure IoT Hub-Überwachungsdaten](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Während der öffentlichen Vorschau sind IoT Hub-Gerätestreams in den Regionen „USA, Mitte“, „USA, Mitte (EUAP)“, „Europa, Norden“ und „Asien, Südosten“ verfügbar. Achten Sie darauf, dass Sie Ihren Hub in einer dieser Regionen erstellen.

## <a name="sdk-availability"></a>SDK-Verfügbarkeit

Zwei Seiten jedes Streams (auf der Geräte- und der Dienstseite) verwenden das IoT Hub SDK zum Einrichten des Tunnels. Während der öffentlichen Vorschau können Kunden aus den folgenden SDK-Sprachen auswählen:

* Die C und C# SDKs unterstützen Gerätestreams auf der Geräteseite.

* Die NodeJS und C# SDKs unterstützen Gerätestreams auf der Dienstseite.

## <a name="iot-hub-device-stream-samples"></a>Beispiele für IoT Hub-Gerätstreams

Die IoT Hub-Seite enthält zwei [Schnellstartbeispiele](./index.yml). Diese Beispiele veranschaulichen die Verwendung von Gerätestreams durch Anwendungen.

* Das Beispiel *Echo* verdeutlicht die programmgesteuerte Verwendung von Gerätestreams (durch direktes Aufrufen der SDK-APIs).

* Im Beispiel mit dem *lokalen Proxy* wird das Tunneln von gängigem Client/Server-Anwendungsdatenverkehr (z.B. SSH, RDP oder Web) über Gerätestreams veranschaulicht.

Diese Beispiele werden weiter unten ausführlicher behandelt.

### <a name="echo-sample"></a>Echobeispiel

Das Echobeispiel veranschaulicht die programmgesteuerte Verwendung von Gerätestreams zum Senden und Empfangen von Bytes zwischen Dienst- und Geräteanwendungen. Beachten Sie, dass Sie Dienst- und Geräteanwendungen in verschiedenen Sprachen verwenden können. Beispielsweise können Sie das C-Geräteprogramm mit dem C#-Dienstprogramm verwenden.

Hier sind die Echobeispiele:

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in C# über IoT Hub-Gerätestreams (Vorschauversion)](quickstart-device-streams-echo-csharp.md)

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in Node.js über IoT Hub-Gerätestreams (Vorschauversion)](quickstart-device-streams-echo-nodejs.md)

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in C über IoT Hub-Gerätestreams (Vorschauversion)](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Beispiel mit lokalem Proxy (für SSH oder RDP)

Das Beispiel mit lokalem Proxy zeigt eine Möglichkeit zum Aktivieren eines Tunnels für den Datenverkehr einer vorhandenen Anwendung, der die Kommunikation zwischen einem Client- und einem Serverprogramm umfasst. Dieses Setup funktioniert für Client-/Serverprotokolle wie SSH und RDP, in denen die Dienstseite als Client fungiert (und SSH- oder RDP-Clientprogramme ausführt) und die Geräteseite als Server fungiert (und SSH-Daemon- oder RDP-Serverprogramme ausführt).

Dieser Abschnitt beschreibt die Verwendung von Gerätestreams, um für Benutzer das Herstellen einer SSH-Verbindung mit einem Gerät über Gerätestreams zu ermöglichen. (Die Situation ist für RDP oder andere Client-/Server-Anwendungen ähnlich, da dort der entsprechende Port des Protokolls verwendet wird.)

Das Setup nutzt zwei Programme für *lokale Proxys* , die in der folgenden Abbildung dargestellt sind: *Lokaler Geräteproxy* und *Lokaler Dienstproxy*. Die lokalen Proxyprogramme sind für die Durchführung des [Handshakes zur Gerätestreaminitiierung](#device-stream-creation-flow) mit IoT Hub sowie für die Interaktion mit dem SSH-Client und dem SSH-Daemon über reguläre Client-/Server-Sockets zuständig.

![„Gerätestream-Proxysetup für SSH/RDP“](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Der Benutzer führt den lokalen Dienstproxy aus, um einen Gerätestream an das Gerät zu initiieren.

2. Der lokale Proxy des Geräts akzeptiert die Anforderung zur Streaminitiierung, und der Tunnel wird mit dem Streamingendpunkt des IoT Hub hergestellt (wie oben beschrieben).

3. Der lokale Geräteproxy stellt eine Verbindung mit dem SSH-Daemonendpunkt her, der an Port 22 auf dem Gerät lauscht.

4. Der lokale Proxy des Diensts lauscht über einen angegebenen Port und wartet auf neue SSH-Verbindungen vom Benutzer (im Beispiel ist dies Port 2222, aber hierfür kann auch jeder andere verfügbare Port konfiguriert werden). Der Benutzer verweist den SSH-Client an den Port des lokalen Dienstproxys auf „localhost“.

### <a name="notes"></a>Notizen

* Durch die oben genannten Schritte wird ein End-to-End-Tunnel zwischen dem SSH-Client (auf der rechten Seite) und dem SSH-Daemon (auf der linken Seite) eingerichtet. Ein Teil dieser End-to-End-Konnektivität ist das Senden von Datenverkehr per Gerätestream an IoT Hub.

* Die Pfeile in der Abbildung zeigen die Richtung, in der Verbindungen zwischen Endpunkten hergestellt werden. Beachten Sie insbesondere, dass keine Verbindungen beim Gerät eingehen. (Dies wird häufig durch eine Firewall blockiert.)

* Die Auswahl von Port 2222 auf dem lokalen Proxy des Diensts ist eine willkürliche Auswahl. Der Proxy kann so konfiguriert werden, dass ein beliebiger anderer verfügbarer Port verwendet wird.

* Die Auswahl von Port 22 ist protokollabhängig und in diesem Fall spezifisch für SSH. Für RDP muss Port 3389 verwendet werden. Dies kann in den bereitgestellten Beispielprogrammen konfiguriert werden.

Verwenden Sie die folgenden Links, um Anweisungen zum Ausführen der lokalen Proxyprogramme in der Sprache Ihrer Wahl zu erhalten. Wie im [Echobeispiel](#echo-sample) können Sie Programme für den lokalen Geräteproxy und den lokalen Dienstproxy in verschiedenen Sprachen ausführen, da sie vollständig kompatibel sind.

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in C# über IoT Hub-Gerätestreams (Vorschauversion)](quickstart-device-streams-proxy-csharp.md)

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in Node.js über IoT Hub-Gerätestreams (Vorschauversion)](quickstart-device-streams-proxy-nodejs.md)

* [Schnellstart: Kommunizieren mit einer Geräteanwendung in C über IoT Hub-Gerätestreams (Vorschauversion)](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Gerätestreams finden Sie unter den folgenden Links.

> [!div class="nextstepaction"]
> [Device streams on IoT show (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0) (Show „Gerätestreams zu IoT“ auf Channel 9)
