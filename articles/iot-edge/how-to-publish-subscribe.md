---
title: Veröffentlichen und Abonnieren mit Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden des IoT Edge-MQTT-Brokers zum Veröffentlichen und Abonnieren von Nachrichten
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 005830575ba7f45d30fed71a73e7a419e4d98220
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922590"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Veröffentlichen und Abonnieren mit Azure IoT Edge

Sie können den Azure IoT Edge-MQTT-Broker zum Veröffentlichen und Abonnieren von Nachrichten verwenden. In diesem Artikel wird gezeigt, wie Sie eine Verbindung mit diesem Broker herstellen, Nachrichten über benutzerdefinierte Themen veröffentlichen und abonnieren sowie Primitive für das IoT Hub-Messaging verwenden. Der IoT Edge-MQTT-Broker ist in den IoT Edge-Hub integriert. Weitere Informationen finden Sie unter [den Brokerfunktionen des IoT Edge-Hubs](iot-edge-runtime.md).

> [!NOTE]
> Der IoT Edge-MQTT-Broker ist zurzeit in der öffentlichen Vorschau verfügbar.

## <a name="pre-requisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem gültigen Abonnement
- [Azure CLI](/cli/azure/) mit der installierten CLI-Erweiterung `azure-iot`. Weitere Informationen finden Sie in den [Installationsschritten der Azure IoT-Erweiterung für Azure CLI](/cli/azure/azure-cli-reference-for-iot).
- Ein **IoT-Hub** der SKUs F1, S1, S2 oder S3.
- Sie haben ein **IoT Edge-Gerät mit Version 1.2 oder höher**. Da sich der IoT Edge-MQTT-Broker zurzeit in der öffentlichen Vorschau befindet, legen Sie die folgenden Umgebungsvariablen für den edgeHub-Container auf „true“ fest, um den MQTT-Broker zu aktivieren:

   | Name | Wert |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- Auf dem IoT Edge-Gerät sind **Mosquitto-Clients** installiert. In diesem Artikel werden die beliebten Mosquitto-Clients [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) und [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html) verwendet. Stattdessen könnten aber auch andere MQTT-Clients verwendet werden. Führen Sie zum Installieren der Mosquitto-Clients auf einem Ubuntu-Gerät den folgenden Befehl aus:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Installieren Sie nicht den Mosquitto-Server, da dies dazu führen kann, dass die MQTT-Ports (8883 und 1883) blockiert werden und es zu einem Konflikt mit dem IoT Edge-Hub kommt.

## <a name="connect-to-iot-edge-hub"></a>Herstellen einer Verbindung mit IoT Edge-Hub

Beim Herstellen einer Verbindung mit IoT Edge-Hub werden dieselben Schritte ausgeführt, die im [Artikel „Connecting to IoT Hub with a generic MQTT client“](../iot-hub/iot-hub-mqtt-support.md) (Herstellen einer Verbindung mit IoT Hub mit einem generischen MQTT-Client) oder im [Artikel „Conceptual description of the IoT Edge hub“](iot-edge-runtime.md) (Konzeptionelle Beschreibung des IoT Edge-Hubs) beschrieben werden. Diese Schritte sind die folgenden:

1. Der MQTT-Client richtet optional eine *sichere Verbindung* mit dem IoT Edge-Hub mithilfe von Transport Layer Security (TLS) ein.
2. Der MQTT-Client *authentifiziert* sich selbst beim IoT Edge-Hub.
3. Der IoT Edge-Hub *autorisiert* den MQTT-Client entsprechend seiner Autorisierungsrichtlinie.

### <a name="secure-connection-tls"></a>Sichere Verbindung (TLS)

Mithilfe von Transport Layer Security (TLS) wird eine verschlüsselte Kommunikation zwischen dem Client und dem IoT Edge-Hub hergestellt.

Verwenden Sie zum Deaktivieren von TLS den Port 1883 (MQTT), und binden Sie den edgeHub-Container an Port 1883.

Wenn ein Client eine Verbindung mit dem MQTT-Broker über Port 8883 (MQTTS) herstellt, wird zum Aktivieren von TLS ein TLS-Kanal initiiert. Der Broker sendet seine Zertifikatkette, die der Client überprüfen muss. Zum Überprüfen der Zertifikatkette muss das Stammzertifikat des MQTT-Brokers auf dem Client als vertrauenswürdiges Zertifikat installiert sein. Wenn das Stammzertifikat nicht vertrauenswürdig ist, wird die Clientbibliothek vom MQTT-Broker mit einem Zertifikatüberprüfungsfehler abgelehnt. Die zum Installieren dieses Stammzertifikats des Brokers auf dem Client auszuführenden Schritte sind dieselben wie beim [transparenten Gateway](how-to-create-transparent-gateway.md) und werden unter [Vorbereiten eines nachgeschalteten Geräts](how-to-connect-downstream-device.md#prepare-a-downstream-device) beschrieben.

### <a name="authentication"></a>Authentifizierung

Damit sich ein MQTT-Client selbst authentifizieren kann, muss er zuerst ein CONNECT-Paket an den MQTT-Broker senden, um eine Verbindung in seinem Namen zu initiieren. Dieses Paket enthält drei Teile von Authentifizierungsinformationen: ein Feld `client identifier`, ein Feld `username` und ein Feld `password`:

- Das Feld `client identifier` ist der Name des Geräts oder der Modulname in IoT Hub. Es verwendet die folgende Syntax:

  - Für ein Gerät: `<device_name>`

  - Für ein Modul: `<device_name>/<module_name>`

   Zum Herstellen einer Verbindung mit dem MQTT-Broker muss ein Gerät oder ein Modul in IoT Hub registriert werden.

   Der Broker lässt das Herstellen einer Verbindung von mehreren Clients mit denselben Anmeldeinformationen nicht zu. Der Broker trennt den bereits verbundenen Client, wenn ein zweiter Client eine Verbindung mit denselben Anmeldeinformationen herstellt.

- Das Feld `username` wird vom Geräte- oder Modulnamen abgeleitet, und der Name des IoT-Hubs, zu dem das Gerät gehört, wird mithilfe der folgenden Syntax abgeleitet:

  - Für ein Gerät: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Für ein Modul: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- Das Feld `password` des CONNECT-Pakets ist abhängig vom Authentifizierungsmodus:

  - Wenn Sie die [Authentifizierung mit symmetrischen Schlüsseln](how-to-authenticate-downstream-device.md#symmetric-key-authentication) verwenden, ist das Feld `password` ein SAS-Token.
  - Bei der [selbstsignierten X.509-Authentifizierung](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) gibt es das Feld `password` nicht. In diesem Authentifizierungsmodus ist ein TLS-Kanal erforderlich. Der Client muss eine Verbindung mit Port 8883 herstellen, um eine TLS-Verbindung einzurichten. Während des TLS-Handshakes fordert der MQTT-Broker ein Clientzertifikat an. Mithilfe dieses Zertifikats wird die Identität des Clients überprüft. Daher wird das Feld `password` später nicht benötigt, wenn das CONNECT-Paket gesendet wird. Wenn Sie sowohl ein Clientzertifikat als auch das Kennwortfeld senden, führt dies zu einem Fehler, und die Verbindung wird geschlossen. MQTT-Bibliotheken und TLS-Clientbibliotheken verfügen normalerweise über eine Möglichkeit zum Senden eines Clientzertifikats, wenn eine Verbindung initiiert wird. Im Abschnitt [Using X509 Certificate for client authentication](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) (Verwenden eines X.509-Zertifikats für die Clientauthentifizierung) finden Sie ein schrittweises Beispiel dazu.

Von IoT Edge bereitgestellte Module verwenden [Authentifizierung mit symmetrischen Schlüsseln](how-to-authenticate-downstream-device.md#symmetric-key-authentication) und können die lokale [IoT Edge-Workload-API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) zum programmgesteuerten Abrufen eines SAS-Tokens auch im Offlinemodus aufrufen.

### <a name="authorization"></a>Autorisierung

Nachdem ein MQTT-Client für IoT Edge-Hub authentifiziert wurde, muss er zum Herstellen einer Verbindung autorisiert werden. Nachdem die Verbindung hergestellt wurde, muss sie zum Veröffentlichen oder Abonnieren in bestimmten Themen autorisiert werden. Diese Autorisierungen werden vom IoT Edge-Hub basierend auf seiner Autorisierungsrichtlinie erteilt. Die Autorisierungsrichtlinie ist eine Gruppe von als JSON-Struktur ausgedrückten Anweisungen, die an den IoT Edge-Hub über seinen Zwilling gesendet wird. Bearbeiten Sie einen IoT Edge-Hub-Zwilling, um dessen Autorisierungsrichtlinie zu konfigurieren.

> [!NOTE]
> Bei der öffentlichen Vorschau ist die Bearbeitung von Autorisierungsrichtlinien für den MQTT-Broker nur über Visual Studio, Visual Studio Code oder die Azure-Befehlszeilenschnittstelle verfügbar. Das Azure-Portal unterstützt zurzeit nicht die Bearbeitung des IoT Edge-Hub-Zwillings und seiner Autorisierungsrichtlinie.

Jede Autorisierungsrichtlinienanweisung besteht aus der Kombination von `identities`, den Auswirkungen von `allow` oder `deny` sowie `operations` und `resources`:

- `identities` beschreiben den Antragsteller für die Richtlinie. Er muss dem `client identifier` zugeordnet werden, der von Clients in deren CONNECT-Paket gesendet wird.
- Die Auswirkungen von `allow` oder `deny` definieren, ob Vorgänge zugelassen oder abgelehnt werden.
- `operations` definieren die zu autorisierenden Aktionen. `mqtt:connect`, `mqtt:publish` und `mqtt:subscribe` sind die drei heute unterstützten Aktionen.
- `resources` definieren das Objekt der Richtlinie. Es kann ein Thema oder Themamuster sein, das mit [MQTT-Platzhaltern](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) definiert wird.

Das nachstehende Beispiel für eine Autorisierungsrichtlinie erlaubt explizit nicht, dass der Client „rogue_client“ eine Verbindung herstellt, erlaubt aber, dass alle Azure IoT-Clients eine Verbindung herstellen können und dass „sensor_1“ im Thema `events/alerts` veröffentlicht.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Beim Schreiben Ihrer Autorisierungsrichtlinie müssen Sie einige Punkte beachten:

- Sie erfordert für den `$edgeHub`-Zwilling die Schemaversion 1.2.
- Standardmäßig werden alle Vorgänge abgelehnt.
- Autorisierungsanweisungen werden in der Reihenfolge ausgewertet, in der sie in der JSON-Definition angezeigt werden. Sie beginnt mit einem Blick auf `identities` und wählt dann die ersten „allow“- oder „deny“-Anweisungen aus, die mit der Anforderung übereinstimmen. Bei Konflikten zwischen „allow“- und „deny“-Anweisungen hat die „deny“-Anweisung Priorität.
- In der Autorisierungsrichtlinie können mehrere Variablen (z. B. Ersetzungen) verwendet werden:
    - `{{iot:identity}}` stellt die Identität des zurzeit verbundenen Clients dar. Beispiele sind eine Geräteidentität wie `myDevice` oder eine Modulidentität wie `myEdgeDevice/SampleModule`.
    - `{{iot:device_id}}` stellt die Identität des zurzeit verbundenen Geräts dar. Beispiele sind eine Geräteidentität wie `myDevice` oder die Geräteidentität, unter der ein Modul ausgeführt wird, etwa `myEdgeDevice`.
    - `{{iot:module_id}}` stellt die Identität des zurzeit verbundenen Moduls dar. Diese Variable ist bei verbundenen Geräten leer oder eine Modulidentität wie `SampleModule`.
    - `{{iot:this_device_id}}` stellt die Identität des IoT Edge-Geräts dar, auf dem die Autorisierungsrichtlinie ausgeführt wird. Beispiel: `myIoTEdgeDevice`.

Die Themen zu „Autorisierungen für IoT Hub“ werden etwas anders als benutzerdefinierte Themen behandelt. Hier sind wichtige Punkte, die Sie beachten sollten:

- Azure IoT-Geräte oder -Module benötigen eine explizite Autorisierungsregel zum Herstellen einer Verbindung mit dem IoT Edge-Hub-MQTT-Broker. Eine Standard-Autorisierungsrichtlinie zum Herstellen einer Verbindung wird unten gezeigt.
- Azure IoT-Geräte oder -Module können standardmäßig ohne eine explizite Autorisierungsregel auf ihre eigenen IoT-Hub-Themen zugreifen. Allerdings werden Autorisierungen in diesem Fall aus Beziehungen zwischen übergeordneten/untergeordneten Elementen abgeleitet, und diese Beziehungen müssen festgelegt werden. IoT Edge-Module werden automatisch als untergeordnete Elemente des zugehörigen IoT Edge-Geräts festgelegt, aber die Geräte müssen explizit als untergeordnete Elemente ihres IoT Edge-Gateways festgelegt werden.

Hier ist eine Standard-Autorisierungsrichtlinie, mit der es allen Azure IoT-Geräten oder -Modulen ermöglicht werden kann, sich mit dem Broker zu **verbinden**:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Nachdem Sie jetzt verstehen, wie eine Verbindung mit dem IoT Edge-MQTT-Broker hergestellt wird, sehen wir uns als Nächstes an, wie sie zum Veröffentlichen und Abonnieren von Nachrichten verwendet werden kann: zuerst in benutzerdefinierten Themen, dann in IoT Hub-Themen und schließlich in einem anderen MQTT-Broker.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Veröffentlichen und Abonnieren in benutzerdefinierten Themen

In diesem Artikel verwenden Sie den Client **sub_client**, der ein Thema abonniert, und den Client **pub_client**, der in einem Thema veröffentlicht. Sie verwenden die [Authentifizierung mit symmetrischem Schlüssel](how-to-authenticate-downstream-device.md#symmetric-key-authentication), doch dieser Vorgang kann auch mit [selbstsignierter X.509-Authentifizierung](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) oder [Authentifizierung mit einem X.509-Zertifikat, das von einer Zertifizierungsstelle signiert wurde](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication), durchgeführt werden.

### <a name="create-publisher-and-subscriber-clients"></a>Erstellen von Herausgeber- und Abonnentenclients

Erstellen Sie zwei IoT-Geräte in IoT Hub, und rufen Sie deren Kennwörter ab. Führen Sie über Ihr Terminal mithilfe der Azure CLI folgende Aufgaben aus:

1. Erstellen Sie zwei IoT-Geräte in IoT Hub, und machen Sie sie zu übergeordneten Elementen Ihres IoT Edge-Geräts:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Rufen Sie deren Kennwörter durch Generieren eines SAS-Tokens ab:

    - Für ein Gerät:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       Dabei ist „3600“ die Lebensdauer des SAS-Tokens in Sekunden (z. B. 3.600 = 1 Stunde).
    
    - Für ein Modul:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       Dabei ist „3600“ die Lebensdauer des SAS-Tokens in Sekunden (z. B. 3.600 = 1 Stunde).

3. Kopieren Sie das SAS-Token. Dies ist der Wert, der dem Schlüssel „sas“ aus der Ausgabe entspricht. Hier ist eine Beispielausgabe aus dem vorstehenden Azure CLI-Befehl:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autorisieren von Herausgeber- und Abonnentenclients

Zum Autorisieren des Herausgebers und Abonnenten bearbeiten Sie den IoT Edge-Hub-Zwilling über Azure CLI, Visual Studio oder Visual Studio Code, um die folgende Autorisierungsrichtlinie einzubeziehen:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Authentifizierung mit symmetrischen Schlüsseln ohne TLS

#### <a name="subscribe"></a>Abonnieren

Verbinden Sie Ihren MQTT-Client **sub_client** mit dem MQTT-Broker, und abonnieren Sie `test_topic`, indem Sie auf Ihrem IoT Edge-Gerät den folgenden Befehl ausführen:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

Dabei ist in diesem Beispiel `<edge_device_address>` = `localhost`, weil der Client auf demselben Gerät wie IoT Edge ausgeführt wird.

Beachten Sie, dass in diesem ersten Beispiel Port 1883 (MQTT) ohne TLS verwendet wird. Ein weiteres Beispiel mit Port 8883 (MQTTS) und aktiviertem TLS wird im nächsten Abschnitt gezeigt.

Der MQTT-Client **sub_client** wird jetzt gestartet und wartet in `test_topic` auf eingehende Nachrichten.

#### <a name="publish"></a>Veröffentlichen

Verbinden Sie Ihren MQTT-Client **pub_client** mit dem MQTT-Broker, und veröffentlichen Sie eine Nachricht in demselben `test_topic` wie oben, indem Sie auf Ihrem IoT Edge-Gerät über ein anderes Terminal den folgenden Befehl ausführen:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

Dabei ist in diesem Beispiel `<edge_device_address>` = `localhost`, weil der Client auf demselben Gerät wie IoT Edge ausgeführt wird.

Bei Ausführung des Befehls empfängt der MQTT-Client **sub_client** die Meldung „hello“.

### <a name="symmetric-keys-authentication-with-tls"></a>Authentifizierung mit symmetrischen Schlüsseln mit TLS

Zum Aktivieren von TLS muss der Port von 1883 (MQTT) in 8883 (MQTTS) geändert werden, und Clients müssen über das Stammzertifikat des MQTT-Brokers verfügen, damit sie die vom MQTT-Broker gesendete Zertifikatkette überprüfen können. Hierzu können Sie die im Abschnitt [Sichere Verbindung (TLS)](#secure-connection-tls) beschriebenen Schritte ausführen.

Weil die Clients im vorstehenden Beispiel auf demselben Gerät wie der MQTT-Broker ausgeführt werden, gelten dieselben Schritte auch zum Aktivieren von TLS, indem die Portnummer einfach von 1883 (MQTT) in 8883 (MQTTS) geändert wird.

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Veröffentlichen und Abonnieren in IoT Hub-Themen

Mit den [Azure IoT-Geräte-SDKs](https://github.com/Azure/azure-iot-sdks) können Clients bereits IoT Hub-Vorgänge durchführen, doch sie erlauben nicht das Veröffentlichen/Abonnieren in benutzerdefinierten Themen. IoT Hub-Vorgänge können mithilfe von MQTT-Clients durchgeführt werden, die die Semantik zum Veröffentlichen und Abonnieren verwenden, sofern die Protokolle der IoT Hub-Primitive berücksichtigt werden. Wir werden die Besonderheiten durchgehen, damit Sie verstehen, wie diese Protokolle funktionieren.

### <a name="send-telemetry-data-to-iot-hub"></a>Senden von Telemetriedaten an IoT Hub

Das Senden von Telemetriedaten an IoT Hub ähnelt der Veröffentlichung in einem benutzerdefinierten Thema, aber unter Verwendung eines bestimmten IoT Hub-Themas:

- Für ein Gerät werden die Telemetriedaten gesendet im Thema: `devices/<device_name>/messages/events`
- Für ein Modul werden die Telemetriedaten gesendet im Thema: `devices/<device_name>/<module_name>/messages/events`

Erstellen Sie außerdem eine Route, z. B. `FROM /messages/* INTO $upstream`, zum Senden von Telemetriedaten vom IoT Edge-MQTT-Broker an IoT Hub. Weitere Informationen zum Routing finden Sie unter [Deklarieren von Routen](module-composition.md#declare-routes).

### <a name="get-twin"></a>Abrufen eines Zwillings (Get twin)

Das Abrufen des Geräte-/Modulzwillings ist kein typisches MQTT-Muster. Der Client muss eine Anforderung für den Zwilling ausgeben, den IoT Hub bereitstellen wird.

Zum Empfangen von Zwillingen muss der Client ein bestimmtes IoT Hub-Thema (`$iothub/twin/res/#`) abonnieren. Dieser Themenname wird von IoT Hub geerbt, und alle Clients müssen dasselbe Thema abonnieren. Dies bedeutet nicht, dass Geräte oder Module den Zwilling voneinander empfangen. IoT Hub und IoT Edge-Hub wissen, welcher Zwilling wohin übermittelt werden sollte, auch wenn alle Geräte auf denselben Themennamen lauschen. 

Nachdem das Abonnement erstellt wurde, muss der Client den Zwilling anfordern, indem er eine Nachricht für ein bestimmtes IoT Hub-Thema (`$iothub/twin/GET/?rid=<request_id>/#`) veröffentlicht. Dabei ist `<request_id>` ein beliebiger Bezeichner. IoT Hub sendet dann seine Antwort mit den angeforderten Daten im Thema `$iothub/twin/res/200/?rid=<request_id>`, das der Client abonniert. Auf diese Weise kann ein Client seine Anforderungen mit den Antworten koppeln.

### <a name="receive-twin-patches"></a>Empfangen von Zwillingspatches

Zum Empfangen von Zwillingspatches muss ein Client ein spezielles IoT Hub-Thema (`$iothub/twin/PATCH/properties/desired/#`) abonnieren. Nachdem das Abonnement erstellt wurde, empfängt der Client die von IoT Hub in diesem Thema gesendeten Zwillingspatches. 

### <a name="receive-direct-methods"></a>Empfangen von direkten Methoden

Das Empfangen einer direkten Methode ähnelt dem Empfangen von vollständigen Zwillingen mit dem Zusatz, dass der Client zurückbestätigen muss, dass er den Aufruf empfangen hat. Zuerst abonniert der Client das spezielle IoT Hub-Thema `$iothub/methods/POST/#`. Nachdem eine direkte Methode in diesem Thema empfangen wurde, muss der Client den Anforderungsbezeichner `rid` aus dem Unterthema extrahieren, in dem die direkte Methode empfangen wird, und schließlich eine Bestätigungsmeldung im speziellen IoT Hub-Thema `$iothub/methods/res/200/<request_id>` veröffentlichen.

### <a name="send-direct-methods"></a>Senden von direkten Methoden

Weil das Senden einer direkten Methode ein HTTP-Aufruf ist, geschieht dies nicht über den MQTT-Broker. Informationen zum Senden einer direkten Methode an IoT Hub finden Sie unter [Verstehen und Aufrufen direkter Methoden](../iot-hub/iot-hub-devguide-direct-methods.md). Informationen zum lokalen Senden einer direkten Methode an ein anderes Modul finden Sie in diesem [Azure IoT C# SDK-Beispiel für einen direkten Methodenaufruf](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Veröffentlichen und Abonnieren zwischen MQTT-Brokern

Zum Verbinden von zwei MQTT-Brokern enthält der IoT Edge-Hub eine MQTT-Bridge. Eine MQTT-Bridge wird häufig zum Verbinden eines MQTT-Brokers verwendet, der auf einem anderen MQTT-Broker ausgeführt wird. In der Regel wird nur eine Teilmenge des lokalen Datenverkehrs an einen anderen Broker gepusht.

> [!NOTE]
> Die IoT Edge-Hub-Bridge kann zurzeit nur zwischen geschachtelten IoT Edge-Geräten verwendet werden. Sie kann nicht zum Senden von Daten an IoT Hub verwendet werden, da IoT Hub kein MQTT-Broker mit vollem Funktionsumfang ist. Weitere Informationen zur Unterstützung von IoT Hub-MQTT-Brokerfunktionen finden Sie unter [Kommunikation mit Ihrem IoT-Hub mithilfe des Protokolls MQTT](../iot-hub/iot-hub-mqtt-support.md). Weitere Informationen zum Schachteln von IoT Edge-Geräten finden Sie unter [Connect a downstream IoT Edge device to an Azure IoT Edge gateway](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) (Herstellen einer Verbindung zwischen einem nachgeschalteten IoT Edge-Gerät und einem Azure IoT Edge-Gateway). 

In einer geschachtelten Konfiguration fungiert die IoT Edge-Hub-MQTT-Bridge als Client des übergeordneten MQTT-Brokers. Daher müssen Autorisierungsregeln für den übergeordneten Edge-Hub so festgelegt werden, dass der untergeordnete Edge-Hub bestimmte benutzerdefinierte Themen veröffentlichen und abonnieren darf, für die die Bridge konfiguriert wurde.

Die IoT Edge-MQTT-Bridge wird über eine JSON-Struktur konfiguriert, die an den IoT Edge-Hub über seinen Zwilling gesendet wird. Bearbeiten Sie einen IoT Edge-Hub-Zwilling, um seine MQTT-Bridge zu konfigurieren.

> [!NOTE]
> Bei der öffentlichen Vorschau ist die Konfiguration der MQTT-Bridge nur über Visual Studio, Visual Studio Code oder Azure CLI verfügbar. Das Azure-Portal unterstützt zurzeit nicht die Bearbeitung des IoT Edge-Hub-Zwillings und seiner MQTT-Bridgekonfiguration.

Die MQTT-Bridge kann so konfiguriert werden, dass sie einen IoT Edge-Hub-MQTT-Broker mit mehreren externen Brokern verbindet. Für jeden externen Broker sind die folgenden Einstellungen erforderlich:

- `endpoint` ist die Adresse des Remote-MQTT-Brokers, mit dem eine Verbindung hergestellt werden soll. Zurzeit werden nur übergeordnete IoT Edge-Geräte unterstützt, die durch die Variable `$upstream` definiert werden.
- `settings` definiert, welche Themen für einen Endpunkt überbrückt werden müssen. Es kann mehrere Einstellungen pro Endpunkt geben, und zu dessen Konfiguration werden die folgenden Werte verwendet:
    - `direction`: entweder `in` zum Abonnieren der Themen des Remotebrokers oder `out` zum Veröffentlichen in den Themen des Remotebrokers.
    - `topic`: das abzugleichende Kernthemamuster. [MQTT-Platzhalter](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) können zum Definieren dieses Musters verwendet werden. Auf dieses Themenmuster auf dem lokalen Broker und dem Remotebroker können unterschiedliche Präfixe angewendet werden.
    - `outPrefix`: Ein Präfix, das auf das Muster `topic` auf dem Remotebroker angewandt wird.
    - `inPrefix`: Ein Präfix, das auf das Muster `topic` auf dem lokalen Broker angewandt wird.

Das folgende Beispiel zeigt eine IoT Edge-MQTT-Bridgekonfiguration, die alle in den Themen `alerts/#` eines übergeordneten IoT Edge-Geräts empfangenen Nachrichten auf einem untergeordneten IoT Edge-Gerät in denselben Themen erneut veröffentlicht und dann alle in den Themen `/local/telemetry/#` eines untergeordneten IoT Edge-Geräts gesendeten Nachrichten auf einem übergeordneten IoT Edge-Gerät in den Themen `/remote/messages/#` erneut veröffentlicht.

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Weitere Hinweise zur IoT Edge-Hub-MQTT-Bridge:
- Das MQTT-Protokoll wird automatisch als Upstreamprotokoll genutzt, wenn der MQTT-Broker verwendet und das jeweilige IoT Edge-Gerät in einer geschachtelten Konfiguration verwendet wird, z. B. bei einem angegebenen `parent_hostname`. Weitere Informationen zu Upstreamprotokollen finden Sie unter [Cloud-Kommunikation](iot-edge-runtime.md#cloud-communication). Weitere Informationen zu geschachtelten Konfigurationen finden Sie unter [Connect a downstream IoT Edge device to an Azure IoT Edge gateway](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) (Verbinden eines nachgeschalteten IoT Edge-Geräts mit einem Azure IoT Edge-Gateway).

## <a name="next-steps"></a>Nächste Schritte

[Informieren Sie sich zum IoT Edge-Hub](iot-edge-runtime.md#iot-edge-hub).