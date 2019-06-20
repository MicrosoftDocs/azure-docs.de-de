---
title: Zertifikate für Gerätesicherheit – Azure IoT Edge | Microsoft-Dokumentation
description: Azure IoT Edge verwendet Zertifikate, um Geräte, Module und untergeordnete Geräte zu validieren und sichere Verbindungen zwischen diesen herzustellen.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 91cde6965f3635d6d2acfaf581f570779020f8ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445294"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Details zur Verwendung von Azure IoT Edge-Zertifikaten

IoT Edge-Zertifikate werden für die Module und Downstream-IoT-Geräte verwendet, um die Identität und Rechtmäßigkeit des Runtimemoduls des [IoT Edge-Hubs](iot-edge-runtime.md#iot-edge-hub) zu überprüfen, mit dem sie eine Verbindung herstellen. Diese Überprüfungen ermöglichen eine sichere TLS-Verbindung (Transport Layer Security) zwischen Runtime, Modulen und IoT-Geräten. Wie IoT Hub selbst erfordert IoT Edge eine sichere und verschlüsselte Verbindung zwischen IoT-Downstreamgeräten (oder Blattgeräten) und IoT Edge-Modulen. Um eine sichere TLS-Verbindung herzustellen, bietet das IoT Edge-Hubmodul eine Serverzertifikatkette zur Verbindung von Clients, damit sie seine Identität bestätigen.

In diesem Artikel wird erläutert, wie IoT Edge-Zertifikate in Produktions-, Entwicklungs- und Testszenarien funktionieren. Die Skripts sind zwar unterschiedlich (PowerShell oder Bash), aber die Konzepte sind bei Linux und Windows identisch.

## <a name="iot-edge-certificates"></a>IoT Edge-Zertifikate

Hersteller sind normalerweise nicht die Endbenutzer eines IoT Edge-Geräts. Manchmal besteht die einzige Beziehung zwischen den beiden darin, dass der Endbenutzer oder Operator ein generisches Gerät vom Hersteller erwirbt. In anderen Fällen fertigt der Hersteller nach einem Vertrag ein benutzerdefiniertes Gerät für den Operator. Der Entwurf des IoT Edge-Zertifikats versucht, beide Szenarien in Betracht zu ziehen.

Die folgende Abbildung veranschaulicht die IoT Edge-Verwendung von Zertifikaten. Je nach der Anzahl der beteiligten Entitäten sind null, ein oder mehrere Zwischensignaturzertifikate zwischen dem Zertifikat der Stammzertifizierungsstelle und dem Zertifikat der Gerätezertifizierungsstelle möglich. Hier wird ein Fall gezeigt.

![Diagramm typischer Zertifikatsbeziehungen](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Zertifizierungsstelle

Die Zertifizierungsstelle, kurz „ZS“ genannt (Certificate Authority, CA), ist eine Entität, die digitale Zertifikate ausstellt. Eine Zertifizierungsstelle fungiert als vertrauenswürdige dritte Partei zwischen Besitzer und Empfänger des Zertifikats. Ein digitales Zertifikat zertifiziert den Empfänger des Zertifikats als Besitzer eines öffentlichen Schlüssels. Die Zertifikatsvertrauenskette stellt zunächst ein Stammzertifikat aus, das in allen von der Zertifizierungsstelle ausgestellten Zertifikaten als Grundlage der Vertrauensstellung gilt. Danach kann der Besitzer mit dem Stammzertifikat zusätzliche Zwischenzertifikate („untergeordnete Zertifikate“) ausgeben.

### <a name="root-ca-certificate"></a>Zertifikat der Stammzertifizierungsstelle

Ein Zertifikat der Stammzertifizierungsstelle ist der Vertrauensanker des gesamten Prozesses. In Produktionsszenarien wird dieses ZS-Zertifikat normalerweise bei einer vertrauenswürdigen kommerziellen Zertifizierungsstelle wie Baltimore, Verisign oder DigiCert erworben. Wenn Sie vollständige Kontrolle über die Geräte haben, die mit Ihren IoT Edge-Geräten verbunden sind, kann eine Zertifizierungsstelle auf Unternehmensebene verwendet werden. In jedem Fall ist die gesamte Zertifikatkette ab dem IoT Edge Hub ununterbrochen, sodass die Blattknoten-IoT-Geräte dem Stammzertifikat vertrauen müssen. Sie können das Zertifikat der Stammzertifizierungsstelle im Speicher der vertrauenswürdigen Stammzertifizierungsstelle speichern oder die Details des Zertifikats in Ihrem Anwendungscode bereitstellen.

### <a name="intermediate-certificates"></a>Zwischenzertifikate

In einem typischen Fertigungsprozess zum Erstellen sicherer Geräte werden die Zertifikate der Stammzertifizierungsstelle selten direkt verwendet – in erster Linie wg. des Risikos von Datenlecks oder Offenlegung. Das Zertifikat der Stammzertifizierungsstelle erstellt ein oder mehrere Zertifizierungsstellen-Zwischenzertifikate und signiert diese digital. Es kann nur ein Zwischenzertifikat oder eine Kette dieser Zwischenzertifikate vorhanden sein. Folgende Szenarien erfordern z.B. eine Kette von Zwischenzertifikaten:

* Eine Hierarchie von Abteilungen innerhalb eines Herstellers.

* Mehrere Unternehmen sind nacheinander an der Produktion eines Geräts beteiligt.

* Ein Kunde erwirbt ein Zertifikat einer Stammzertifizierungsstelle und leitet ein Signaturzertifikat für den Hersteller ab, womit der die Geräte signiert, die er im Auftrag des Kunden herstellt.

In jedem Fall verwendet der Hersteller am Ende dieser Kette ein Zertifikat der Zwischenzertifizierungsstelle, um das am Endgerät platzierte Zertifikat der Gerätezertifizierungsstelle zu signieren. Im Allgemeinen werden diese Zwischenzertifikate in der Fertigungsanlage streng geheim gehalten. Zur Verwendung durchlaufen sie strenge, sowohl physische als auch elektronische Prozesse.

### <a name="device-ca-certificate"></a>Zertifikat der Gerätezertifizierungsstelle

Das Zertifikat der Gerätezertifizierungsstelle wird aus dem letzten Zertifikat der Zwischenzertifizierungsstelle im Prozess generiert und davon signiert. Dieses Zertifikat wird auf dem IoT Edge-Gerät selbst installiert, vorzugsweise an einem sicheren Speicherort wie einem Hardwaresicherheitsmodul (HSM). Darüber hinaus identifiziert ein Zertifikat der Gerätezertifizierungsstelle eindeutig ein IoT Edge-Gerät. Für IoT Edge kann das Zertifikat der Gerätezertifizierungsstelle andere Zertifikate ausstellen. Das Zertifikat der Gerätezertifizierungsstelle stellt beispielsweise Blattknotengeräte-Zertifikate aus, mit denen Geräte bei [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) authentifiziert werden.

### <a name="iot-edge-workload-ca"></a>IoT Edge-Workloadzertifizierungsstelle

Der [IoT Edge-Sicherheits-Manager](iot-edge-security-manager.md) erstellt das Zertifikat der Workloadzertifizierungsstelle, das erste auf der „Operator“-Seite des Prozesses, beim ersten Start von IoT Edge. Dieses Zertifikat wird aus dem „Zertifikat der Gerätezertifizierungsstelle“ generiert und davon signiert. Mit diesem Zertifikat, das nur ein anderes Zwischensignaturzertifikat ist, werden alle anderen von der IoT Edge-Runtime verwendeten Zertifikate generiert und signiert. Zurzeit ist dies in erster Linie das im folgenden Abschnitt erläuterte IoT Edge-Hubserverzertifikat, aber in Zukunft könnten auch andere Zertifikate für die Authentifizierung von IoT Edge-Komponenten infrage kommen.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge-Hubserverzertifikat

Das IoT Edge-Hubserverzertifikat ist das eigentliche Zertifikat, das Blattknotengeräten und Modulen während des Herstellens der von IoT Edge geforderten TLS-Verbindung zur Identitätsüberprüfung präsentiert wird. Dieses Zertifikat stellt die vollständige Kette der zu seinem Generieren verwendeten Signaturzertifikate dar – bis zum Zertifikat der Stammzertifizierungsstelle, dem das IoT-Blattgerät vertrauen muss. Beim Generieren durch den IoT Edge-Sicherheits-Manager wird als allgemeiner Name (Common Name, CN) dieses IoT Edge-Hubzertifikats die hostname-Eigenschaft in der Datei „config.yaml“ nach der Konvertierung in Kleinbuchstaben festgelegt. Dies ist eine häufige Ursache für eine Verwechslung mit IoT Edge.

## <a name="production-implications"></a>Auswirkungen auf die Produktion

Eine angemessene Frage wäre: „Warum benötigt IoT Edge das zusätzliche Zertifikat der „Workloadzertifizierungsstelle“? Könnte IoT Edge nicht das Zertifikat der Gerätezertifizierungsstelle verwenden, um direkt das IoT Edge-Hubserverzertifikat zu generieren?“. Technisch gesehen, ja. Allerdings soll dieses „Workload“-Zwischenzertifikat“ zur Abgrenzung zwischen den Interessen des Herstellers des Geräts und des Operators des Geräts dienen. Stellen Sie sich ein Szenario vor, in dem ein IoT Edge-Gerät verkauft oder von einem Kunden an einen anderen übertragen wird. Sie wünschen wahrscheinlich, dass das vom Hersteller bereitgestellte Zertifikat der Gerätezertifizierungsstelle unveränderlich ist. Die für den Betrieb des Geräts spezifischen „Workload“-Zertifikate sollten jedoch gelöscht und für die neue Bereitstellung neu erstellt werden.

Da die Prozesse für Fertigung und Betrieb getrennt sind, berücksichtigen Sie beim Vorbereiten von Produktionsgeräten die folgenden Auswirkungen:

* Mit einem beliebigen zertifikatbasierten Prozess sollten das Zertifikat der Stammzertifizierungsstelle und alle Zertifikate der Zwischenzertifizierungsstelle während des gesamten Prozesses des Veröffentlichens auf einem IoT Edge-Gerät geschützt und überwacht werden. Der Hersteller des IoT Edge-Geräts sollten über sichere Prozesse für richtige Speicherung und Nutzung ihrer Zwischenzertifikate verfügen. Darüber hinaus sollte das Zertifikat der Gerätezertifizierungsstelle in so sicherem Speicher wie auf dem Gerät selbst möglich gespeichert werden, vorzugsweise in einem Hardwaresicherheitsmodul.

* Das IoT Edge-Hubserverzertifikat wird den Clientgeräten und Modulen, die eine Verbindung herstellen, durch IoT Edge-Hub präsentiert. Der allgemeine Name (Common Name, CN) des Zertifikats der Gerätezertifizierungsstelle **darf nicht** identisch mit dem hostname-Wert sein, der in „config.yaml“ auf dem IoT Edge-Gerät verwendet wird. Der Name, der von Clients zum Herstellen der Verbindung mit IoT Edge (z.B. über den GatewayHostName-Parameter der Verbindungszeichenfolge oder den Befehl CONNECT in MQTT) verwendet wird, **darf nicht** mit dem im Zertifikat der Gerätezertifizierungsstelle verwendeten allgemeinen Namen identisch sein. Diese Einschränkung ist darauf zurückzuführen, dass IoT Edge Hub die gesamte Vertrauenskette für die Überprüfung durch Clients präsentiert. Wenn das IoT Edge-Hubserverzertifikat und das Zertifikat der Gerätezertifizierungsstelle über denselben CN verfügen, geraten Sie in eine Überprüfungsschleife, und das Zertifikat wird ungültig.

* Da das Zertifikat der Gerätezertifizierungsstelle vom IoT Edge-Sicherheits-Daemon zum Generieren der endgültigen IoT Edge-Zertifikate verwendet wird, muss es selbst ein Signaturzertifikat sein, also Funktionen für die Zertifikatsignatur besitzen. Das Anwenden von „V3 Basic constraints CA:True“ auf das Zertifikat der Gerätezertifizierungsstelle richtet automatisch die erforderlichen Schlüsselverwendungseigenschaften ein.

>[!Tip]
> Wenn Sie bereits die Einrichtung von IoT Edge als transparentes Gateway in einem Entwicklungs-/Testszenario unter Verwendung unserer „Komfortskripts“ (siehe nächster Abschnitt) durchgeführt und den gleichen Hostnamen beim Erstellen des Zertifikats der Gerätezertifizierungsstelle, wie für den Hostnamen in „config.yaml“, verwendet haben, haben Sie sich vielleicht gefragt, warum es funktioniert. Um die Entwicklererfahrung zu verbessern, tragen Komfortskripts die Erweiterung „.ca“ am Ende des Namens, den Sie an das Skript übergeben. Wenn Sie also z.B. für beide Gerätenamen in den Skripts und im Hostnamen in „config.yaml“ „mygateway“ verwendet haben, erfolgt vor der Verwendung als CN für das Zertifikat der Gerätezertifizierungsstelle die Umwandlung in „mygateway.ca“.

## <a name="devtest-implications"></a>Auswirkungen auf Entwicklung/Test

Um Entwicklungs- und Testszenarien zu vereinfachen, bietet Microsoft eine Reihe von [Komfortskripts](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) zum Generieren von nicht zur Produktion bestimmten, für IoT Edge im transparenten Gatewayszenario geeignete Zertifikate an. Beispiele für die Funktionsweise der Skripts finden Sie unter [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md).

Diese Skripts generieren Zertifikate, die der in diesem Artikel erläuterten Zertifikatskettenstruktur folgen. Die folgenden Befehle generieren das „Zertifikat der Stammzertifizierungsstelle“ und ein einzelnes „Zertifikat der Zwischenzertifizierungsstelle“.

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Diese Befehle generieren ebenso das „Zertifikat der Gerätezertifizierungsstelle“.

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* Der diesen Skripts übergebene **\<Gatewaygerätename\>** **darf nicht** mit dem Parameter „hostname“ in „config.yaml“ identisch sein. Mit den Skripts vermeiden Sie durch Anhängen der Zeichenfolge „.ca“ an den **\<Gatewaygerätenamen\>** Probleme durch Namenskonflikte, falls ein Benutzer IoT Edge an beiden Orten mit dem gleichen Namen einrichtet. Die Verwendung des gleichen Namens sollte jedoch grundsätzlich vermieden werden.

>[!Tip]
> Um Verbindungen Ihrer Geräte-IoT-„Blattgeräte“ und Anwendungen herzustellen, die unser IoT-Geräte-SDK über IoT Edge verwenden, müssen Sie den optionalen GatewayHostName-Parameter dem Ende der Verbindungszeichenfolge des Geräts hinzufügen. Wenn das Edge Hub-Serverzertifikat generiert wird, basiert es auf einer Kleinbuchstabenversion des Hostnamens aus „config.yaml“ – damit die Namen übereinstimmen und die Überprüfung der TLS-Zertifikats erfolgreich ausgeführt werden kann, sollten Sie darum den GatewayHostName-Parameter in Kleinbuchstaben eingeben.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Beispiel für IoT Edge-Zertifikathierarchie

Ein Beispiel dieses Zertifikatspfads veranschaulicht der folgende Screenshot eines ausgeführten IoT Edge-Geräts, das als transparentes Gateway eingerichtet ist. OpenSSL wird verwendet, um eine Verbindung mit IoT Edge Hub herzustellen und die Zertifikate zu überprüfen und zu sichern.

![Screenshot der Zertifikatshierarchie auf jeder Ebene](./media/iot-edge-certs/iotedge-cert-chain.png)

Sie sehen die Hierarchie der Zertifikatstiefe im Screenshot dargestellt:

| Zertifikat der Stammzertifizierungsstelle         | Zertifikat der Azure IoT Hub-Zertifizierungsstelle – nur Test                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Zertifikat der Zwischenzertifizierungsstelle | Zertifikat der Azure IoT Hub-Zwischenzertifizierungsstelle – nur Test                                                                 |
| Zertifikat der Gerätezertifizierungsstelle       | iotgateway.ca („iotgateway“ wurde als <Gatewayhostname> den Komfortskripts übergeben)      |
| Zertifikat der Workloadzertifizierungsstelle     | IoT Edge-Workloadzertifizierungsstelle                                                                                       |
| IoT Edge-Hubserverzertifikat | iotedgegw.Local (entspricht dem „Hostname“ aus „config.yaml“)                                                |

## <a name="next-steps"></a>Nächste Schritte

[Grundlegendes zu Azure IoT Edge-Modulen](iot-edge-modules.md)

[Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md)