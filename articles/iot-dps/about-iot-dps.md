---
title: Übersicht über den Azure IoT Hub Device Provisioning-Dienst | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur Gerätebereitstellung in Azure mit Device Provisioning Service (DPS) und IoT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 6b33b866a10ad4a44cef14f3c86d8ca1f40c4750
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965373"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Bereitstellung von Geräten mit dem Azure IoT Hub Device Provisioning-Dienst
Microsoft Azure bietet einen umfangreichen Satz von integrierten öffentlichen Clouddiensten für alle Ihre IoT-Lösunganforderungen. IoT Hub Device Provisioning Service (DPS) ist ein Hilfsdienst für IoT Hub und ermöglicht eine unbeaufsichtigte Just-In-Time-Bereitstellung im richtigen IoT-Hub ganz ohne Benutzereingriff. DPS ermöglicht die skalierbare und sichere Bereitstellung von Millionen von Geräten.

## <a name="when-to-use-device-provisioning-service"></a>Wann sollte der Device Provisioning-Dienst verwendet werden?
DPS ist in vielen Bereitstellungsszenarien eine hervorragende Wahl, um Geräte mit IoT Hub zu verbinden für IoT Hub zu konfigurieren. Beispiele wären etwa:

* Bereitstellung ohne manuelles Eingreifen für eine einzelne IoT-Lösung ohne werkseitige Hartcodierung von IoT Hub-Verbindungsinformationen (Anfangssetup)
* Hubübergreifender Lastenausgleich für Geräte
* Herstellen der Verbindung von Geräten mit der IoT-Lösung ihrer Besitzer auf Basis der Verkaufstransaktionsdaten (Mehrinstanzenfähigkeit)
* Herstellen der Verbindung von Geräten mit einer bestimmten IoT-Lösung abhängig vom Anwendungsfall (Lösungsisolation)
* Herstellen der Verbindung eines Geräts mit IoT Hub mit der geringsten Wartezeit (Geo-Sharding)
* Erneute Bereitstellung basierend auf einer Änderung im Gerät
* Wechseln der Schlüssel, die vom Gerät verwendet werden, um eine Verbindung mit IoT Hub herzustellen (wenn keine X. 509-Zertifikate verwendet werden, um Verbindungen herstellen)

## <a name="behind-the-scenes"></a>Abläufe im Hintergrund
Alle im vorherigen Abschnitt aufgelisteten Szenarien können per DPS für die Bereitstellung ohne manuelles Eingreifen mit dem gleichen Ablauf implementiert werden. Viele der normalerweise mit der Bereitstellung verbundenen manuellen Schritte sind bei DPS automatisiert, um die Bereitstellung von IoT-Geräten zu beschleunigen und das Risiko manueller Fehler zu verringern. Im Folgenden wird beschrieben, was bei der Bereitstellung eines Geräts im Hintergrund geschieht. Der erste Schritt ist manuell, alle folgenden Schritte sind automatisiert.

![Grundlegender Bereitstellungsablauf](./media/about-iot-dps/dps-provisioning-flow.png)

1. Der Gerätehersteller fügt die Geräteregistrierungsinformationen der Registrierungsliste im Azure-Portal hinzu.
2. Das Gerät kontaktiert den werkseitig festgelegten DPS-Endpunkt. Das Gerät übergibt Identifikationsinformationen an DPS, um seine Identität nachzuweisen.
3. DPS überprüft die Identität des Geräts. Hierzu werden die Registrierungs-ID und der entsprechende Schlüssel anhand des Registrierungslisteneintrags überprüft – entweder unter Verwendung einer Nonce-Abfrage ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) oder einer standardmäßigen X.509-Überprüfung (X.509).
4. DPS registriert das Gerät bei einem IoT-Hub und gibt den [gewünschten Zwillingsstatus](../iot-hub/iot-hub-devguide-device-twins.md) des Geräts an.
5. Der IoT-Hub gibt die Geräte-ID-Informationen an DPS zurück.
6. DPS gibt die IoT-Hub-Verbindungsinformationen an das Gerät zurück. Das Gerät kann nun direkt Daten an den IoT Hub senden.
7. Das Gerät stellt eine Verbindung mit dem IoT Hub her.
8. Das Gerät ruft den gewünschten Status von seinem Gerätezwilling im IoT Hub ab.

## <a name="provisioning-process"></a>Bereitstellung
Der Bereitstellungsprozess eines Geräts mit DPS-Beteiligung umfasst zwei unabhängig ausführbare Schritte:

* Der **Fertigungsschritt**, in dem das Gerät werkseitig erstellt und vorbereitet wird, und
* Der **Cloudeinrichtungsschritt**, in dem der Device Provisioning-Dienst für die automatisierte Bereitstellung konfiguriert wird.

Beide Schritte fügen sich nahtlos in bestehende Fertigungs- und Bereitstellungsprozesse ein. DPS vereinfacht sogar einige Bereitstellungsprozesse, die mit manuellen Schritten verbunden sind, um Verbindungsinformationen auf das Gerät zu übertragen.

### <a name="manufacturing-step"></a>Fertigungsschritt
Dieser Schritt umfasst alles, was in der Fertigung geschieht. An diesem Schritt sind Hardwareentwickler und -hersteller, Integrator und/oder Endhersteller des Geräts beteiligt. Dieser Schritt betrifft das Erstellen der Hardware selbst.

DPS fügt dem Fertigungsprozess keinen neuen Schritt hinzu, sondern wird vielmehr in den vorhandenen Schritt eingebunden, mit dem die grundlegende Software und (im Idealfall) das HSM auf dem Gerät installiert werden. Statt dass in diesem Schritt eine Geräte-ID erstellt wird, wird das Gerät mit den Bereitstellungsdienstinformationen programmiert, sodass es den Bereitstellungsdienst aufrufen kann, um beim Einschalten seine Verbindungsinformationen/IoT-Lösungszuweisung zu erhalten.

Auch in diesem Schritt liefert der Hersteller dem Bereitsteller/Bediener des Geräts Indentifikationsschlüsselinformationen. Die Angabe dieser Informationen ist möglicherweise einfach nur die Bestätigung, dass alle Geräte über ein X.509-Zertifikat verfügen, das aus einem vom Gerätebereitsteller/-bediener bereitgestellten Signaturzertifikat generiert wurde, kann jedoch auch komplexer sein und das Extrahieren des öffentlichen Teils eines TPM-Endorsement Keys aus jedem TPM-Gerät beinhalten. Diese Dienste werden heute von vielen Hardwareherstellern angeboten.

### <a name="cloud-setup-step"></a>Cloudeinrichtungsschritt
In diesem Schritt wird die Cloud für die ordnungsgemäße automatische Bereitstellung konfiguriert. Im Allgemeinen sind zwei Arten von Benutzern am Cloudeinrichtungsschritt beteiligt: eine Person (ein Gerätebediener), die weiß, wie Geräte erstmalig eingerichtet werden müssen, und eine andere Person (ein Lösungsoperator), die weiß, wie Geräte auf die IoT Hubs aufgeteilt werden sollen.

Eine einmalige anfängliche Einrichtung der Bereitstellung muss ausgeführt werden, und dies wird im Allgemeinen vom Lösungsoperator übernommen. Sobald der Bereitstellungsdienst konfiguriert ist, muss er nicht geändert werden, solange sich der Anwendungsfall nicht ändert.

Wenn der Dienst für die automatische Bereitstellung konfiguriert ist, muss er auf das Registrieren von Geräten vorbereitet werden. Dieser Schritt erfolgt durch den Gerätebediener, der die gewünschte Konfiguration der Geräte kennt und dafür verantwortlich ist, sicherzustellen, dass der Bereitstellungsdienst ordnungsgemäß die Identität des Geräts nachweisen kann, wenn es seinen IoT Hub sucht. Die Gerätebediener übernimmt die Indentifikationsschlüsselinformationen vom Hersteller und fügt sie der Registrierungsliste hinzu. Die Registrierungsliste kann später aktualisiert werden, indem neue Einträge hinzugefügt oder vorhandene Einträge mit den neuesten Informationen zu den Geräten aktualisiert werden.

## <a name="registration-and-provisioning"></a>Registrierung und Bereitstellung
*Bereitstellung* kann abhängig von der Branche, wo der Begriff verwendet wird, verschiedene Bedeutungen haben. Im Kontext der Bereitstellung von IoT-Geräten für ihre Cloudlösung ist sie ein aus zwei Teilen bestehender Prozess:

1. Im ersten Teil wird durch Registrieren des Geräts eine erstmalige Verbindung des Geräts mit der IoT-Lösung hergestellt.
2. Im zweiten Schritt wird die richtige Konfiguration des Geräts basierend auf den spezifischen Anforderungen der Lösung, für die es registriert wurde, angewendet.

Nach Ausführung dieser beiden Schritte kann das Gerät als vollständig bereitgestellt bezeichnet werden. Einige Clouddienste ermöglichen nur den ersten Schritt des Bereitstellungsvorgangs, das Registrieren von Geräten beim Endpunkt der IoT-Lösung, aber nicht die Erstkonfiguration. DPS automatisiert beide Schritte für eine nahtlose Bereitstellung des Geräts.

## <a name="features-of-the-device-provisioning-service"></a>Features des Device Provisioning-Diensts
Dank zahlreicher Features eignet sich DPS perfekt für die Bereitstellung von Geräten.

* Unterstützung des **sicheren Nachweises** für Identitäten auf X.509- und TPM-Basis.
* Eine **Registrierungsliste** mit der vollständigen Aufzeichnung von Geräten/Gerätegruppen, die sich zu einem beliebigen Zeitpunkt registrieren könnten. Die Registrierungsliste enthält Informationen zu der gewünschten Konfiguration des Geräts, sobald es registriert ist, und sie kann jederzeit aktualisiert werden.
* **Mehrere Zuordnungsrichtlinien**, um die DPS-basierte Zuweisung von Geräten zu IoT-Hubs für Ihre Szenarien zu steuern: Niedrigste Latenz, gleichmäßig gewichtete Verteilung (Standard) und statische Konfiguration über die Registrierungsliste. Wartezeit wird mithilfe der gleichen Methode ermittelt wie bei [Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#performance).
* **Überwachungs- und Diagnoseprotokolle**, um sicherzustellen, dass alles ordnungsgemäß funktioniert
* **Unterstützung für mehrere Hubs**, sodass Device Provisioning Service (DPS) Geräte mehreren IoT Hubs zuweisen kann. DPS kann über mehrere Azure-Abonnements hinweg mit Hubs kommunizieren.
* **Regionsübergreifende Unterstützung**, um DPS das Zuweisen von Geräten zu IoT-Hubs in anderen Regionen zu ermöglichen.
* Mit der **Verschlüsselung für ruhende Daten** können Daten in DPS auf transparente Weise mit der 256-Bit-AES-Verschlüsselung – einer der stärksten verfügbaren Blockchiffren – ver- und entschlüsselt werden. Die Verschlüsselung ist darüber hinaus mit dem FIPS 140-2-Standard konform.


Weitere Informationen zu den mit der Gerätebereitstellung verbundenen Konzepten und Features finden Sie im Thema [DPS-Terminologie](concepts-service.md) sowie in den anderen konzeptionellen Themen desselben Abschnitts.

## <a name="cross-platform-support"></a>Plattformübergreifende Unterstützung
DPS kann wie alle Azure IoT-Dienste plattformübergreifend mit verschiedenen Betriebssystemen verwendet werden. Azure bietet Open Source-SDKs in einer Vielzahl von [Sprachen](https://github.com/Azure/azure-iot-sdks) zur Erleichterung von Geräteverbindungen und Dienstverwaltung. DPS unterstützt die folgenden Protokolle zum Verbinden von Geräten:

* HTTPS
* AMQP
* AMQP über Websockets
* MQTT
* MQTT über Websockets

Für Dienstvorgänge unterstützt DPS nur HTTPS-Verbindungen.

## <a name="regions"></a>Regions
DPS steht in vielen Regionen zur Verfügung. Eine für alle Dienste laufend aktualisierte Liste der vorhandenen und neu angekündigten Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/). Die Verfügbarkeit des Device Provisioning-Diensts können Sie auf der Seite [Azure-Status](https://azure.microsoft.com/status/) überprüfen.

> [!NOTE]
> DPS ist global und nicht an einen Standort gebunden. Sie müssen jedoch eine Region zum Speichern der Metadaten für Ihr DPS-Profil angeben.

## <a name="availability"></a>Verfügbarkeit
Für DPS gilt eine [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/iot-hub/) von 99,9 Prozent. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

## <a name="quotas"></a>Kontingente
Für jedes Azure-Abonnement gelten standardmäßig bestimmte Kontingentgrenzen, die den Umfang Ihrer IoT-Lösung beeinträchtigen könnten. Die aktuelle Grenze auf Abonnementbasis sind 10 Device Provisioning-Dienste pro Abonnement.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Weitere Informationen zu Kontingentgrenzen finden Sie hier:
* [Einschränkungen für Azure-Abonnementdienste](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Verwandte Azure-Komponenten
DPS automatisiert die Gerätebereitstellung mit Azure IoT Hub. Erfahren Sie mehr über [IoT Hub](../iot-hub/index.yml).

## <a name="next-steps"></a>Nächste Schritte
Sie haben jetzt einen Überblick über die Bereitstellung von IoT-Geräten in Azure erhalten. Der nächste Schritt ist, dass Sie ein End-to-End-IoT-Szenario testen.

[Einrichten von IoT Hub Device Provisioning Service über das Azure-Portal](quick-setup-auto-provision.md)

[Erstellen und Bereitstellen eines simulierten Geräts](quick-create-simulated-device.md)

[Einrichten von Geräten für die Bereitstellung](tutorial-set-up-device.md)