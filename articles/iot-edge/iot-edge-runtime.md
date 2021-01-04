---
title: Verwalten von Geräten mithilfe der Runtime –Azure IoT Edge | Microsoft-Dokumentation
description: Erfahren Sie, wie die IoT Edge-Runtime Module, Sicherheit, Kommunikation und Berichterstellung auf Ihren Geräten verwaltet.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: c0c3a452c93b88483ac7027405665c26ceab8183
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368505"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur

Die Azure IoT Edge-Runtime ist eine Sammlung von Programmen, die aus einem Gerät ein IoT Edge-Gerät machen. Die Sammlung der IoT Edge-Runtime-Komponenten ermöglicht IoT Edge-Geräten den Empfang von Code für die Ausführung im Edgebereich und das Kommunizieren der Ergebnisse.

Die IoT Edge-Runtime führt auf IoT Edge-Geräten die folgenden Funktionen aus:

* Installieren und Aktualisieren von Workloads auf dem Gerät

* Aufrechterhalten von Azure IoT Edge-Sicherheitsstandards auf dem Gerät

* Sicherstellen, dass die [IoT Edge-Module](iot-edge-modules.md) immer ausgeführt werden

* Melden der Modulintegrität an die Cloud für die Remoteüberwachung

* Verwalten der Kommunikation zwischen nachgeschalteten Geräten und IoT Edge-Geräten.

* Verwalten der Kommunikation zwischen Modulen auf einem IoT Edge-Gerät

* Verwalten der Kommunikation zwischen einem IoT Edge-Gerät und der Cloud
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Verwalten der Kommunikation zwischen IoT Edge-Geräten
::: moniker-end

![Die IoT Edge-Runtime übermittelt an IoT Hub Einblicke und Informationen zur Modulintegrität](./media/iot-edge-runtime/Pipeline.png)

Die Aufgaben der IoT Edge-Laufzeit fallen in zwei Kategorien: Kommunikation und Modulverwaltung. Diese beiden Rollen werden von zwei Komponenten ausgeführt, die Bestandteil der IoT Edge-Runtime sind. Der *IoT Edge-Agent* stellt die Module bereit und überwacht sie, während der *IoT Edge-Hub* für die Kommunikation verantwortlich ist.

Sowohl der IoT Edge-Agent als auch der IoT Edge-Hub sind Module und können mit jedem anderen auf einem IoT Edge-Gerät ausgeführten Modul verglichen werden. Manchmal werden sie auch als die *Runtimemodule* bezeichnet.

## <a name="iot-edge-agent"></a>IoT Edge-Agent

Der IoT Edge-Agent ist eines der beiden Module, aus denen die Azure IoT Edge-Runtime besteht. Er ist für das Instanziieren von Modulen verantwortlich, stellt sicher, dass sie weiterhin ausgeführt werden, und meldet den Status der Module an IoT Hub. Diese Konfigurationsdaten werden als Eigenschaft des Modulzwillings des IoT Edge-Agents geschrieben.

Der [IoT Edge-Sicherheits-Daemon](iot-edge-security-manager.md) startet den IoT Edge-Agent beim Starten des Geräts. Der Agent ruft den Modulzwilling von IoT Hub ab und überprüft das Bereitstellungsmanifest. Das Bereitstellungsmanifest ist eine JSON-Datei, die die Module deklariert, die gestartet werden müssen.

Jedes Element im Bereitstellungsmanifest enthält spezifische Informationen zu einem Modul und wird vom IoT Edge-Agent zum Steuern des Lebenszyklus des Moduls verwendet. Weitere Informationen zu den vom IoT Edge-Agent zur Steuerung der Module verwendeten Eigenschaften finden Sie unter [Eigenschaften der Modulzwillinge von IoT Edge-Agent und IoT Edge-Hub](module-edgeagent-edgehub.md).

Der IoT Edge-Agent sendet eine Runtimeantwort an IoT Hub. Im Folgenden sehen Sie eine Liste der möglichen Antworten:
  
* 200 – OK
* 400 – Die Bereitstellungskonfiguration ist falsch formatiert oder ungültig.
* 417 – Für das Gerät ist keine Bereitstellungskonfiguration festgelegt.
* 412 – Die Schemaversion der Bereitstellungskonfiguration ist ungültig.
* 406 – das IoT Edge-Gerät ist offline oder sendet keine Statusberichte.
* 500 – in der IoT Edge-Runtime ist ein Fehler aufgetreten.

Weitere Informationen zum Erstellen von Bereitstellungsmanifesten finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md).

### <a name="security"></a>Sicherheit

Der IoT Edge-Agent hat eine wichtige Funktion für die Sicherheit eines IoT Edge-Geräts. Er überprüft beispielsweise das Image eines Moduls, bevor es gestartet wird.

Weitere Informationen zum Azure IoT Edge-Sicherheitsframework finden Sie in der Dokumentation zu [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>IoT Edge-Hub

Der IoT Edge-Hub ist das zweite Modul, aus dem die Azure IoT Edge-Runtime besteht. Er fungiert als lokaler Proxy für IoT Hub, indem er die gleichen Protokollendpunkte wie IoT Hub verfügbar macht. Diese Konsistenz bedeutet, dass Clients auf die gleiche Weise eine Verbindung mit der IoT Edge-Runtime wie mit IoT Hub herstellen können.

Der IoT Edge-Hub ist keine vollständige Version des lokal ausgeführten IoT Hub. Der IoT Edge-Hub delegiert einige Aufgaben im Hintergrund an IoT Hub. Der IoT Edge-Hub lädt beispielsweise bei seiner ersten Verbindung automatisch Autorisierungsinformationen von IoT Hub herunter, um einem Gerät die Verbindung zu ermöglichen. Nachdem die erste Verbindung hergestellt wurde, werden die Autorisierungsinformationen vom IoT Edge-Hub lokal zwischengespeichert. Alle anschließenden Verbindungen von diesem Gerät werden autorisiert, ohne dass noch mal Autorisierungsinformationen aus der Cloud heruntergeladen werden müssen.

### <a name="cloud-communication"></a>Cloudkommunikation

Der IoT Edge-Hub optimiert die Anzahl der tatsächlich hergestellten Verbindungen mit der Cloud, um die von der IoT Edge-Lösung genutzte Bandbreite zu verringern. Der IoT Edge-Hub fasst logische Verbindungen von Modulen oder Downstreamgeräten in einer einzelnen physischen Verbindung mit der Cloud zusammen. Die Details dieses Vorgangs sind für den Rest der Lösung transparent. Clients können nicht erkennen, dass statt einer eigenen Verbindung mit der Cloud für alle Clients eine gemeinsame Verbindung verwendet wird. Der IoT Edge-Hub kann für die Upstreamkommunikation mit der Cloud unabhängig der von den nachgeschalteten Geräten verwendeten Protokolle entweder das AMQP- oder das MQTT-Protokoll verwenden. Der IoT Edge-Hub unterstützt aktuell jedoch nur die Kombination logischer Verbindungen in eine einzelne physische Verbindung mit AMQP als Upstreamprotokoll und den dazugehörigen Multiplexingfunktionen. AMQP ist das Standardupstreamprotokoll.

![Der IoT Edge-Hub ist ein Gateway zwischen physischen Geräten und IoT Hub](./media/iot-edge-runtime/gateway-communication.png)

Der IoT Edge-Hub kann ermitteln, ob er mit IoT Hub verbunden ist. Wenn die Verbindung unterbrochen wird, speichert der IoT Edge-Hub Nachrichten oder Zwillingsaktualisierungen lokal. Sobald eine Verbindung wieder hergestellt wurde, werden alle Daten synchronisiert. Der Speicherort für diesen temporären Cache wird durch eine Eigenschaft des Modulzwillings des IoT Edge-Hubs festgelegt. Die Größe des Caches wird nicht begrenzt, solange die Speicherkapazität des Geräts ausreicht.  Weitere Informationen finden Sie unter [Offlinefunktionen](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Modulkommunikation

Der IoT Edge-Hub ermöglicht die Kommunikation zwischen Modulen. Die Verwendung des IoT Edge-Hubs als Nachrichtenbroker sorgt dafür, dass die Module voneinander unabhängig bleiben. Module müssen nur die Eingänge, an denen sie Nachrichten akzeptieren, und die Ausgänge, an die sie Nachrichten schreiben, angeben. Ein Lösungsentwickler kann diese Eingänge und Ausgänge zusammenfügen, damit die Module Daten in der speziellen Reihenfolge für diese Lösung verarbeiten.

![Der IoT Edge-Hub ermöglicht die Kommunikation zwischen Modulen.](./media/iot-edge-runtime/module-endpoints.png)

Ein Modul ruft zum Senden von Daten an den IoT Edge-Hub die SendEventAsync-Methode auf. Das erste Argument gibt an, an welchen Ausgang die Nachricht gesendet werden soll. Mit dem folgenden Pseudocode wird eine Nachricht an **output1** gesendet:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Um eine Nachricht zu empfangen, registrieren Sie einen Rückruf, der eingehende Nachrichten an einem speziellen Eingang verarbeitet. Mit dem folgenden Pseudocode wird die Funktion „messageProcessor“ registriert, die für die Verarbeitung aller an **input1** empfangenen Nachrichten verwendet werden soll:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Weitere Informationen zur ModuleClient-Klasse und ihre Kommunikationsmethoden finden Sie in der API-Referenz für Ihre bevorzugte SDK-Sprache: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) oder [Node.js](/javascript/api/azure-iot-device/moduleclient).

Der Lösungsentwickler muss die Regeln angeben, die festlegen, wie Nachrichten vom IoT Edge-Hub zwischen Modulen übergeben werden. Routingregeln werden in der Cloud definiert und an den IoT Edge-Hub in dessen Modulzwilling übertragen. Zum Definieren von Routen zwischen Modulen in Azure IoT Edge wird die gleiche Syntax wie für IoT Hub-Routen verwendet. Weitere Informationen finden Sie unter [Informationen zum Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md).

![Das Routing zwischen Modulen erfolgt über den IoT Edge-Hub](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Lokale Kommunikation

Der IoT Edge-Hub ermöglicht die lokale Kommunikation. Er ermöglicht die Kommunikation von Gerät zu Modul, von Modul zu Modul und von Gerät zu Gerät durch Verwendung von Nachrichtenbrokern, damit Geräte und Module unabhängig voneinander bleiben.

>[!NOTE]
> Das MQTT-Brokerfeature befindet sich mit der IoT Edge-Version 1.2 in der öffentlichen Vorschau. Es muss explizit aktiviert werden.

Der IoT Edge-Hub unterstützt zwei Brokermechanismen:

1. Die [von IoT Hub unterstützten Features für Nachrichtenrouting](../iot-hub/iot-hub-devguide-messages-d2c.md) und
2. einen universellen MQTT-Broker, der den [MQTT-Standard (Version 3.1.1)](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html) erfüllt.

#### <a name="using-routing"></a>Verwenden des Routings

Der erste Brokermechanismus verwendet dieselben Routingfeatures wie IoT Hub, um anzugeben, wie Nachrichten zwischen Geräten oder Modulen übermittelt werden. Geräte oder Module geben zuerst die Eingaben an, für die sie Nachrichten akzeptieren, sowie die Ausgaben, an die sie Nachrichten schreiben. Ein Lösungsentwickler kann Nachrichten dann zwischen einer Quelle, z. B. Ausgaben, und einem Ziel, z. B. Eingaben, mit möglichen Filtern weiterleiten.

![Das Routing zwischen Modulen erfolgt über den IoT Edge-Hub](./media/iot-edge-runtime/module-endpoints-routing.png)

Routing kann von Geräten oder Modulen genutzt werden, die mit den Azure IoT-Geräte-SDKs entweder über das AMQP- oder das MQTT-Protokoll erstellt wurden. Alle IoT Hub-Messagingprimitive, z. B. Telemetrie, direkte Methoden, C2D oder Zwillinge, werden unterstützt. Die Kommunikation über benutzerdefinierte Themen wird jedoch nicht unterstützt.

Weitere Informationen zu Routen finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md).

#### <a name="using-the-mqtt-broker"></a>Verwenden des MQTT-Brokers

Der zweite Brokermechanismus basiert auf dem MQTT-Standardbroker. MQTT ist ein einfaches Protokoll zum Übertragen von Nachrichten, das optimale Leistung für Geräte mit Ressourceneinschränkungen garantiert. Außerdem ist es ein häufig genutzter Standard für Veröffentlichungen und Abonnements. Geräte oder Module können Themen abonnieren, um Nachrichten zu erhalten, die von anderen Geräten oder Modulen veröffentlicht wurden. Der IoT Edge-Hub implementiert seinen eigenen MQTT-Broker, der die [Spezifikationen der MQTT-Version 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html) befolgt.

Der MQTT-Broker ermöglicht im Vergleich zum Routing zwei zusätzliche Kommunikationsmuster: lokale Übertragungen und Point-to-Point-Kommunikation. Lokale Übertragungen sind hilfreich, wenn ein Gerät oder Modul mehrere andere Geräte oder Module lokal benachrichtigen muss. Point-to-Point-Kommunikation ermöglicht es zwei IoT Edge-Geräten oder zwei IoT-Geräten, lokal zu kommunizieren, ohne einen Roundtrip zur Cloud durchführen zu müssen.

![Lokales Veröffentlichen und Abonnieren mit dem IoT Edge-Hub](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

Der MQTT-Broker kann von Geräten oder Modulen verwendet werden, die entweder mit den Azure IoT-Geräte-SDKs erstellt wurden, die über das MQTT-Protokoll kommunizieren, oder mit beliebigen universellen MQTT-Clients. Mit Ausnahme von C2D werden alle IoT Hub-Messagingprimitive unterstützt, z. B. Telemetrie, direkte Methoden oder Zwillinge. Spezielle IoT Hub-Themen, die von IoT Hub-Primitiven verwendet werden, werden unterstützt. Dasselbe gilt für benutzerdefinierte Themen.
Bei diesem Thema könnte es sich um ein IoT Hub-Sonderthema oder ein benutzerdefiniertes Thema handeln.

Im Gegensatz zu Routingmechanismen kann beim Sortieren von Nachrichten nur Best Effort garantiert werden. Das Filtern von Nachrichten wird vom Broker nicht unterstützt. Das Fehlen dieser Features ermöglicht es jedoch, dass der MQTT-Broker schneller ist als Routing.

Weitere Information zum MQTT-Broker finden Sie unter [Veröffentlichen und Abonnieren mit IoT Edge](how-to-publish-subscribe.md).

#### <a name="comparison-between-brokering-mechanisms"></a>Vergleich zwischen Brokermechanismen

Unten sehen Sie die für die einzelnen Brokermechanismen verfügbaren Features:

|Features  | Routing  | MQTT-Broker  |
|---------|---------|---------|
|D2C-Telemetrie    |     &#10004;    |         |
|Lokale Telemetrie     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Zwilling     |    &#10004;     |    &#10004;     |
|C2D für Geräte     |   &#10004;      |         |
|Sortieren     |    &#10004;     |         |
|Filtern     |     &#10004;    |         |
|Benutzerdefinierte Themen     |         |    &#10004;     |
|Gerät zu Gerät     |         |    &#10004;     |
|Lokale Übertragung     |         |    &#10004;     |
|Leistung     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Herstellen einer Verbindung zum IoT Edge-Hub

Der IoT Edge-Hub akzeptiert Verbindungen von Geräte- oder Modulclients, entweder über das MQTT-Protokoll oder über das AMQP-Protokoll.

>[!NOTE]
> Der IoT Edge-Hub unterstützt Clients, die sich über MQTT oder AMQP verbinden. Clients, die HTTP verwenden, werden jedoch nicht unterstützt.

Wenn ein Client eine Verbindung zum IoT Edge-Hub herstellt, geschieht Folgendes:

1. Wenn Transport Layer Security (TLS) verwendet wird (empfohlen), wird ein TLS-Kanal erstellt, um die verschlüsselte Kommunikation zwischen dem Client und dem IoT Edge-Hub zu ermöglichen.
2. Der Client sendet Authentifizierungsinformationen an den IoT Edge-Hub, um sich zu identifizieren.
3. Basierend auf der Autorisierungsrichtlinie autorisiert der IoT Edge-Hub die Verbindung oder lehnt sie ab.

#### <a name="secure-connections-tls"></a>Sichere Verbindungen (TLS)

Der IoT Edge-Hub akzeptiert standardmäßig nur mit Transport Layer Security (TLS) gesicherte Verbindungen, z. B. verschlüsselte Verbindungen, die ein Drittanbieter nicht entschlüsseln kann.

Wenn ein Client auf Port 8883 (MQTTS) oder 5671 (AMQPS) eine Verbindung zum IoT Edge-Hub herstellt, muss ein TLS-Kanal erstellt werden. Während des TLS-Handshake sendet der IoT Edge-Hub seine Zertifikatkette, die der Client überprüfen muss. Das Stammzertifikat des IoT Edge-Hub muss als vertrauenswürdiges Zertifikat auf dem Client installiert sein, um die Zertifikatkette überprüfen zu können. Wenn das Stammzertifikat nicht vertrauenswürdig ist, wird die Clientbibliothek vom IoT Edge-Hub mit einem Zertifikatüberprüfungsfehler abgelehnt.

Die für die Installation des Stammzertifikats des Brokers auf Geräteclients zu befolgenden Schritte werden unter [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md) und unter [Vorbereiten eines nachgeschalteten Geräts](how-to-connect-downstream-device.md#prepare-a-downstream-device) beschrieben. Module können dasselbe Stammzertifikat wie der IoT Edge-Hub nutzen, indem die IoT Edge-Daemon-API verwendet wird.

#### <a name="authentication"></a>Authentifizierung

Der IoT Edge-Hub akzeptiert nur Verbindungen von Geräten oder Modulen, die eine IoT Hub-Identität haben, die also z. B. in IoT Hub registriert wurden und über eine der drei von IoT Hub unterstützten Clientauthentifizierungsmethoden verfügen, um ihre Identität bestätigen zu können: [Authentifizierung mit symmetrischen Schlüsseln](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [selbstsignierte X.509-Authentifizierung](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), [von der Zertifizierungsstelle signierte X.509-Authentifizierung](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Diese IoT Hub-Identitäten können lokal vom IoT Edge-Hub überprüft werden, sodass Verbindungen auch offline hergestellt werden können.

Hinweise:

* IoT Edge-Module unterstützen aktuell nur die Authentifizierung mit symmetrischen Schlüsseln.
* MQTT-Clients, die nur einen lokalen Benutzernamen und Kennwörter aufweisen, werden vom MQTT-Broker des IoT Edge-Hub nicht akzeptiert, sie müssen IoT Hub-Identitäten verwenden.

#### <a name="authorization"></a>Autorisierung

Nach der Authentifizierung verfügt der IoT Edge-Hub über zwei Möglichkeiten, Clientverbindungen zu autorisieren:

* Es wird überprüft, ob ein Client zu den in IoT Hub definierten vertrauenswürdigen Clients gehört. Diese vertrauenswürdigen Clients werden angegeben, indem Beziehungen in IoT Hub eingerichtet werden (übergeordnet/untergeordnet oder Gerät/Modul). Wenn ein Modul in IoT Edge erstellt wird, wird automatisch eine Vertrauensstellungsbeziehung zwischen diesem Modul und dem dazugehörigen IoT Edge-Gerät hergestellt. Dies ist das einzige vom Routingbrokermechanismus unterstützte Autorisierungsmodell.

* Es wird eine Autorisierungsrichtlinie eingerichtet. Bei dieser Autorisierungsrichtlinie handelt es sich um ein Dokument, in dem alle autorisierten Clientidentitäten aufgeführt sind, die auf Ressourcen im IoT Edge-Hub zugreifen können. Hierbei handelt es sich um das primäre vom MQTT-Broker für den IoT Edge-Hub verwendete Autorisierungsmodell. Aber auch der MQTT-Broker kann Beziehungen (übergeordnet/untergeordnet und Gerät/Modul) für IoT Hub-Themen verstehen.

### <a name="remote-configuration"></a>Remotekonfiguration

Der IoT Edge-Hub wird vollständig von der Cloud gesteuert. Die Konfiguration wird von IoT Hub über den dazugehörigen [Modulzwilling](iot-edge-modules.md#module-twins) abgerufen. Sie hat folgenden Inhalt:

* Routenkonfiguration
* Mit auf Namespace-Ebene konfigurierten Autorisierungsrichtlinien
* MQTT-Bridgekonfiguration

Außerdem können weitere Konfigurationen über die Einrichtung von [Umgebungsvariablen für den IoT Edge-Hub](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md) vorgenommen werden.
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetrie der Laufzeitqualität

IoT Edge sammelt anonyme Telemetriedaten aus der Host-Laufzeit und Systemmodulen zur Verbesserung der Produktqualität. Diese Informationen werden als „Telemetrie der Laufzeitqualität“ bezeichnet. Die gesammelten Telemetriedaten werden vom IoT Edge-Agent in regelmäßigen Abständen als Gerät-zu-Cloud-Nachrichten an IoT Hub gesendet. Diese Nachrichten werden in der regulären Telemetrie der Kunden nicht angezeigt und verbrauchen kein Nachrichtenkontingent.

Der IoT Edge-Agent und der Hub generieren Metriken, die Sie sammeln können, um die Geräteleistung zu verstehen. Eine Teilmenge dieser Metriken wird vom IoT Edge-Agent im Rahmen der Telemetrie der Laufzeitqualität gesammelt. Die für die Telemetrie der Laufzeitqualität gesammelten Metriken werden mit dem Tag `ms_telemetry` gekennzeichnet. Informationen zu allen verfügbaren Metriken finden Sie unter [Zugreifen auf integrierte Metriken](how-to-access-built-in-metrics.md).

Alle persönlich oder unternehmensweit identifizierbaren Informationen, z. B. Geräte- und Modulnamen, werden vor dem Hochladen entfernt, um die anonyme Natur der Telemetrie der Laufzeitqualität sicherzustellen.

Der IoT Edge-Agent sammelt die Telemetriedaten stündlich und sendet alle 24 Stunden eine einzige Nachricht an IoT Hub.

Wenn Sie das Senden von Laufzeittelemetriedaten von Ihren Geräten deaktivieren möchten, gibt es dafür zwei Möglichkeiten:

* Legen Sie die Umgebungsvariable `SendRuntimeQualityTelemetry` auf `false` für **edgeAgent** fest, oder
* Deaktivieren Sie die Option während der Bereitstellung im Azure-Portal.

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu Azure IoT Edge-Modulen](iot-edge-modules.md)
* [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md)
* [Veröffentlichen und Abonnieren mit IoT Edge](how-to-publish-subscribe.md)
* [Informieren Sie sich zu IoT Edge-Laufzeitmetriken](how-to-access-built-in-metrics.md)
