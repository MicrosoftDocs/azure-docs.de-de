---
title: Terminologie zu Azure IoT Hub Device Provisioning Service | Microsoft-Dokumentation
description: In diesem Artikel wird die allgemeine Terminologie beschrieben, die für IoT Hub Device Provisioning Service (DPS) und IOT Hub verwendet wird.
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019445"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologie: Azure IoT Hub Device Provisioning Service (DPS)

Der IoT Hub Device Provisioning-Dienst ist ein Hilfsdienst für IoT Hub, mit dem Sie Geräte ohne manuelles Eingreifen auf einem angegebenen IoT Hub konfigurieren können. Mit DPS können Sie Millionen von Geräten auf sichere und skalierbare Weise [bereitstellen](about-iot-dps.md#provisioning-process).

Die Gerätebereitstellung ist ein zweiteiliger Prozess. Im ersten Schritt wird durch *Registrieren* des Geräts die Erstverbindung zwischen dem Gerät und der IoT-Lösung hergestellt. Im zweiten Schritt wird basierend auf den spezifischen Anforderungen der Lösung die geeignete *Konfiguration* auf das Gerät angewendet. Nach Ausführung beider Schritte ist das Gerät vollständig *bereitgestellt*. Mit dem Device Provisioning-Dienst werden beide Schritte automatisiert, sodass das Gerät nahtlos bereitgestellt wird.

Dieser Artikel bietet eine Übersicht über die Bereitstellungskonzepte, die sich am besten zur Verwaltung des *Diensts* eignen. Dieser Artikel ist besonders für Personen relevant, die am [Schritt zum Einrichten der Cloud](about-iot-dps.md#cloud-setup-step) bei der Vorbereitung von Geräten für die Bereitstellung beteiligt sind.

## <a name="service-operations-endpoint"></a>Endpunkt für Dienstvorgänge

Der Endpunkt für Dienstvorgänge ist der Endpunkt zum Verwalten der Diensteinstellungen und der Registrierungsliste. Dieser Endpunkt wird nur vom Dienstadministrator verwendet. Er wird nicht von Geräten verwendet.

## <a name="device-provisioning-endpoint"></a>Endpunkt zur Gerätebereitstellung

Der Endpunkt zur Gerätebereitstellung ist der einzelne Endpunkt, den alle Geräte für die automatische Bereitstellung verwenden. Die URL ist für alle Bereitstellungsdienstinstanzen identisch, sodass bei neuen Verbindungsinformationen in Supply Chain-Szenarien nicht die Firmware von Geräten aktualisiert werden muss. Durch den ID-Bereich wird die Mandantenisolation sichergestellt.

## <a name="linked-iot-hubs"></a>Verknüpfte IoT Hubs

Der Device Provisioning-Dienst kann nur Geräte in IoT Hubs bereitstellen, die mit dem Dienst verknüpft wurden. Durch Verknüpfen eines IoT Hub mit einer Instanz des Device Provisioning-Diensts erhält der Dienst Lese-/Schreibberechtigungen für die Geräteregistrierung des IoT Hubs. Durch die Verknüpfung kann der Device Provisioning-Dienst eine Geräte-ID registrieren und die Erstkonfiguration im Gerätezwilling festlegen. Verknüpfte IoT Hubs können sich in allen Azure-Regionen befinden. Sie können Hubs in anderen Abonnements mit Ihrem Bereitstellungsdienst verknüpfen.


## <a name="allocation-policy"></a>Zuordnungsrichtlinie

Die Einstellung auf Dienstebene, mit der festgelegt wird, wie der Device Provisioning-Dienst einem IoT Hub Geräte zuweist. Es gibt drei unterstützte Zuordnungsrichtlinien:

* **Gleichmäßig gewichtete Verteilung:** Bei verknüpften IoT Hubs ist die Wahrscheinlichkeit gleich hoch, dass Geräte für sie bereitgestellt werden. Dies ist die Standardeinstellung. Wenn Sie nur für eine IoT Hub-Instanz Geräte bereitstellen, können Sie diese Einstellung beibehalten.

* **Niedrigste Latenz:** Geräte werden für einen IoT Hub mit der geringsten Latenz für das Gerät bereitgestellt. Wenn mehrere verknüpfte IoT Hubs die gleiche geringste Latenz aufweisen, verteilt der Bereitstellungsdienst die Geräte auf diese Hubs.

* **Statische Konfiguration über die Registrierungsliste:** Die Angabe des gewünschten IoT Hub in der Registrierungsliste hat gegenüber der Zuordnungsrichtlinie auf Dienstebene Vorrang.

* **Benutzerdefiniert (Azure Functions)** : Mit einer Zuweisungsrichtlinie können Sie genauer steuern, wie Geräte einem IoT-Hub zugewiesen werden. Dazu werden Geräte mithilfe von benutzerdefiniertem Code in einer Azure-Funktion einem IoT-Hub zugewiesen. DPS ruft Ihren Azure Functions-Code auf und stellt Ihrem Code alle relevanten Informationen zum Gerät und zur Registrierung bereit. Der Funktionscode wird ausgeführt und gibt die IoT Hub-Informationen für die Bereitstellung des Geräts zurück.

## <a name="enrollment"></a>Anmeldung

Bei einer Registrierung handelt es sich um den Datensatz der Geräte oder Gerätegruppen, die mittels automatischer Bereitstellung registriert werden können. Der Registrierungsdatensatz enthält unter anderem folgende Informationen zum Gerät bzw. zur Gerätegruppe:
- Den verwendeten [Nachweismechanismus](#attestation-mechanism) des Geräts
- Die optionale anfängliche gewünschte Konfiguration
- Die gewünschte IoT Hub-Instanz
- Die gewünschte Geräte-ID

Im Device Provisioning-Dienst werden zwei Registrierungsarten unterstützt:

### <a name="enrollment-group"></a>Registrierungsgruppe

Eine Registrierungsgruppe stellt eine Gruppe von Geräten dar, die einen bestimmten Nachweismechanismus gemeinsam nutzen. Registrierungsgruppen unterstützen sowohl X.509 als auch symmetrische Schlüssel. Alle Geräte in der X.509-Registrierungsgruppe stellen X.509-Zertifikate bereit, die von der gleichen Stamm- oder Zwischenzertifizierungsstelle signiert wurden. Jedes Gerät in der Registrierungsgruppe mit symmetrischem Schlüssel stellt SAS-Token bereit, die vom symmetrischen Schlüssel der Gruppe abgeleitet werden. Der Name und das Zertifikat der Registrierungsgruppe müssen alphanumerisch sein und dürfen nur Kleinbuchstaben enthalten. Bindestriche sind zulässig.

> [!TIP]
> Es empfiehlt sich, eine Registrierungsgruppe für eine große Anzahl von Geräten, die eine gewünschte Erstkonfiguration gemeinsam nutzen, oder für Geräte zu verwenden, die alle demselben Mandanten zugeordnet sind.

### <a name="individual-enrollment"></a>Individuelle Registrierung

Eine individuelle Registrierung ist ein Eintrag für ein einzelnes Gerät, das registriert werden kann. Individuelle Registrierungen verwenden entweder untergeordnete X.509-Zertifikate oder SAS-Token (in einem physischen oder virtuellen TPM) als Nachweismechanismen. Die Registrierungs-ID in einer einzelnen Registrierung ist alphanumerisch und besteht aus Kleinbuchstaben und ggf. Bindestrichen. Bei individuellen Registrierungen ist möglicherweise die gewünschte IoT Hub-Geräte-ID angegeben.

> [!TIP]
> Individuelle Registrierungen sollten für Geräte verwendet werden, die besondere Erstkonfigurationen erfordern, oder für Geräte, die sich nur mittels SAS-Token per TPM-Nachweis authentifizieren können.


## <a name="attestation-mechanism"></a>Nachweismechanismus

Ein Nachweismechanismus ist eine Methode zum Überprüfen der Identität eines Geräts. Er wird für einen Registrierungseintrag konfiguriert und informiert den Bereitstellungsdienst darüber, welche Methode verwendet werden soll, wenn die Identität eines Geräts während der Registrierung überprüft wird.

> [!NOTE]
> IoT Hub verwendet das „Authentifizierungsschema“ für ein ähnliches Konzept in diesem Dienst.

Der Device Provisioning-Dienst unterstützt die folgenden Arten von Nachweisen:
* **X.509-Zertifikate** – basierend auf dem Standardauthentifizierungsablauf für X.509-Zertifikate. Weitere Informationen finden Sie unter [X.509-Nachweis](concepts-x509-attestation.md).
* **Trusted Platform Module (TPM)** – basierend auf einer Nonce-Abfrage mit dem TPM-Standard für Schlüssel zum Bereitstellen eines signierten SAS-Tokens (Shared Access Signature). Dies erfordert keine physische TPM-Instanz auf dem Gerät. Der Dienst erwartet für den Nachweis jedoch den Endorsement Key gemäß [TPM-Spezifikation](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Weitere Informationen finden Sie unter [TPM-Nachweis](concepts-tpm-attestation.md).
* **Symmetrischer Schlüssel** – basierend auf Shared Access Signature (SAS)-[Sicherheitstokens](../iot-hub/iot-hub-devguide-security.md#security-tokens), die eine Hashsignatur und ein eingebettetes Ablaufdatum haben. Weitere Informationen finden Sie unter [Symmetric key attestation](concepts-symmetric-key-attestation.md) (Nachweis des symmetrischen Schlüssels).


## <a name="hardware-security-module"></a>Hardwaresicherheitsmodul

Das Hardwaresicherheitsmodul (HSM) dient der sicheren, hardwarebasierten Speicherung von Gerätegeheimnissen und gilt als die sicherste Form der Geheimnisspeicherung. Sowohl X.509-Zertifikate als auch SAS-Token können im HSM gespeichert werden. HSMs können mit beiden Nachweismechanismen verwendet werden, die der Bereitstellungsdienst unterstützt.

> [!TIP]
> Es wird dringend empfohlen, ein HSM mit Geräten zu verwenden, um Geheimnisse sicher auf Ihren Geräten zu speichern.

Gerätegeheimnisse können auch in Software (Arbeitsspeicher) gespeichert werden. Dies gilt jedoch im Vergleich zu einem HSM als die weniger sichere Speicherform.



## <a name="id-scope"></a>ID-Bereich

Der ID-Bereich wird einem Device Provisioning-Dienst zugewiesen, wenn er vom Benutzer erstellt wird. Er dient der Identifizierung des jeweiligen Bereitstellungsdiensts, über den sich das Gerät registriert. Der ID-Bereich wird vom Dienst generiert. Er kann nicht geändert werden und stellt Eindeutigkeit sicher.

> [!NOTE]
> Eindeutigkeit ist wichtig für lang andauernde Bereitstellungsvorgänge und Zusammenführungs- sowie Kaufszenarien.


## <a name="registration"></a>Registrierung

Eine Registrierung ist der Datensatz eines Geräts, das über den Device Provisioning-Dienst erfolgreich in einem IoT Hub registriert/bereitgestellt wurde. Registrierungsdatensätze werden automatisch erstellt. Sie können gelöscht, jedoch nicht aktualisiert werden.


## <a name="registration-id"></a>Registrierungs-ID

Die Registrierungs-ID wird zur eindeutigen Identifizierung einer Geräteregistrierung in DPS verwendet. Die Geräte-ID muss im [ID-Bereich](#id-scope) des Bereitstellungsdiensts eindeutig sein. Jedes Gerät benötigt eine Registrierungs-ID. Die Registrierungs-ID ist alphanumerisch, es wird nicht zwischen Groß- und Kleinschreibung unterschieden, und sie kann Sonderzeichen wie Doppelpunkte, Punkte, Unterstriche und Bindestriche aufweisen.

* Bei TPM wird die Registrierungs-ID durch das TPM selbst bereitgestellt.
* Im Fall von X.509-basierten Nachweisen wird die Registrierungs-ID als Antragstellername des Zertifikats bereitgestellt.

## <a name="device-id"></a>Geräte-ID

Die Geräte-ID ist die ID, die in IoT Hub angezeigt wird. Die gewünschte Geräte-ID kann im Registrierungseintrag festgelegt werden, eine Festlegung ist aber nicht zwingend. Das Festlegen der gewünschten Geräte-ID wird nur in Einzelregistrierungen unterstützt. Wenn in der Registrierungsliste keine gewünschte Geräte-ID angegeben wurde, wird bei der Registrierung des Geräts die Registrierungs-ID als die Geräte-ID verwendet. Erfahren Sie mehr zu [Geräte-IDs in IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operationen (Operations)

Vorgänge stellen die Abrechnungseinheit des Device Provisioning-Diensts dar. Ein Vorgang entspricht der erfolgreichen Ausführung einer Anweisung für den Dienst. Vorgänge umfassen Registrierungen und erneute Registrierungen von Geräten sowie dienstseitige Änderungen wie das Hinzufügen und Aktualisieren von Registrierungslisteneinträgen.
