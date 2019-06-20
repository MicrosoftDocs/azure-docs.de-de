---
title: Übersicht zur Azure IoT Hub-X.509-Sicherheit | Microsoft-Dokumentation
description: Übersicht – Authentifizieren von Geräten bei IoT Hub mit X.509-Zertifizierungsstellen.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3d02d3573902964a8549fa0eeb1f4f1471de1752
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257577"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Geräteauthentifizierung mit X.509-Zertifikaten

Dieser Artikel beschreibt, wie Zertifikate von X.509-Zertifizierungsstellen (Certificate Authority, CA) zum Authentifizieren von Geräten verwendet werden, die eine Verbindung mit IoT Hub herstellen.  In diesem Artikel erfahren Sie Folgendes:

* Abrufen eines X.509-Zertifizierungsstellenzertifikats
* Registrieren des X.509-Zertifizierungsstellenzertifikats bei IoT Hub
* Signieren von Geräten mit X.509-Zertifikaten
* Authentifizieren von Geräten, die von der X.509-Zertifizierungsstelle signiert sind

## <a name="overview"></a>Übersicht

Das X.509-CA-Feature ermöglicht die Geräteauthentifizierung bei IoT Hub mithilfe einer Zertifizierungsstelle (CA). Es vereinfacht den anfänglichen Geräteregistrierungsprozess und die Lieferkettenlogistik während der Herstellung des Geräts erheblich. [In diesem Szenarioartikel finden Sie weitere Informationen über den Wert der Verwendung von X.509-Zertifikaten](iot-hub-x509ca-concept.md) für die Geräteauthentifizierung.  Sie sollten diesen Szenarioartikel unbedingt lesen, bevor Sie fortfahren, da dort erläutert wird, warum die folgenden Schritte notwendig sind.

## <a name="prerequisite"></a>Voraussetzung

Die Verwendung des X.509-CA-Features setzt voraus, dass Sie über ein IoT Hub-Konto verfügen.  [Erfahren Sie, wie Sie eine IoT Hub-Instanz erstellen](quickstart-send-telemetry-dotnet.md), falls Sie noch keine besitzen.

## <a name="how-to-get-an-x509-ca-certificate"></a>Abrufen eines X.509-Zertifizierungsstellenzertifikats

Das X.509-CA-Zertifikat ist das oberste Glied der Kette von Zertifikaten für jedes Ihrer Geräte.  Je nach Verwendungszweck können Sie es erwerben oder erstellen.

Für Produktionsumgebungen sollten Sie ein X.509-CA-Zertifikat von einer öffentlichen Stammzertifizierungsstelle erwerben. Der Erwerb eines CA-Zertifikats hat den Vorteil, dass die als vertrauenswürdiger Drittanbieter fungierende Stammzertifizierungsstelle die Legitimität Ihrer Geräte bestätigt. Erwägen Sie diese Option, wenn Ihre Geräte Teil eines offenen IoT-Netzwerks sein sollen, in dem sie mit Produkten oder Diensten von Drittanbietern interagieren werden.

Für Experimente oder zur Verwendung in geschlossenen IoT-Netzwerken können Sie auch ein selbstsigniertes X.509-CA-Zertifikat erstellen.

Unabhängig davon, wie Sie Ihr X.509-CA-Zertifikat erhalten – stellen Sie sicher, dass sein entsprechender privater Schlüssel jederzeit geheim und geschützt ist.  Dies ist für die Vertrauensbildung bei der X.509-CA-Authentifizierung notwendig.

Erfahren Sie, wie Sie [ein selbstsigniertes CA-Zertifikat erstellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), das Sie für diese gesamte Featurebeschreibung zu Experimenten verwenden können.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Signieren von Geräten bei einer Zertifikatvertrauenskette

Der Besitzer eines X.509-Zertifikats kann kryptografisch eine Zwischenzertifizierungsstelle signieren, die wiederum eine andere Zwischenzertifizierungsstelle signieren kann usw., bis die letzte Zwischenzertifizierungsstelle den Prozess durch Signieren eines Geräts beendet. Das Ergebnis ist eine kaskadierte Kette von Zertifikaten, die als Zertifikatvertrauenskette bezeichnet wird. In der Realität ist dies praktisch eine Vertrauensdelegierung für das Signieren von Geräten. Diese Delegierung ist wichtig, da sie eine kryptografisch variable Verwahrungskette schafft und die Freigabe von Signaturschlüsseln vermeidet.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

Beim Gerätezertifikat (auch als „untergeordnetes Zertifikat“ bezeichnet) muss der *Antragstellername* auf die **Geräte-ID** festgelegt werden, die bei der Registrierung des IoT-Geräts im Azure IoT Hub verwendet wurde. Diese Einstellung ist für die Authentifizierung erforderlich.

Hier erfahren Sie, wie Sie [eine Zertifikatkette erstellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), wie es beim Signieren von Geräten üblich ist.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Registrieren des X.509-Zertifizierungsstellenzertifikats bei IoT Hub

Registrieren Sie Ihr X.509-CA-Zertifikat bei IoT Hub, wo es zum Authentifizieren Ihrer Geräte während der Registrierung und Verbindungsherstellung verwendet wird.  Das Registrieren des X.509-CA-Zertifikats ist ein Zwei-Schritte-Prozess: Upload der Zertifikatsdatei und Eigentumsnachweis.

Im Uploadprozess wird eine Datei hochgeladen, die Ihr Zertifikat enthält.  Diese Datei darf nie private Schlüssel enthalten.

Der Eigentumsnachweisschritt ist mit einer kryptografischen Anforderung und einem Antwortprozess zwischen Ihnen und IoT Hub verbunden.  Im Hinblick auf die Tatsache, dass digitale Zertifikatinhalte öffentlich und daher anfällig gegenüber Lauschangriffen sind, möchte IoT Hub sicherstellen, dass Sie das CA-Zertifikat tatsächlich besitzen.  Dazu wird eine zufällige Anforderung generiert, die Sie mit den entsprechenden privaten Schlüssel des CA-Zertifikats signieren müssen.  Wenn Sie den privaten Schlüssel wie zuvor empfohlen geheim gehalten und geschützt haben, sind nur Sie in der Lage, diesen Schritt abzuschließen. Die Geheimhaltung privater Schlüssel ist bei dieser Methode die Quelle des Vertrauens.  Schließen Sie diesen Schritt nach Signieren der Anforderung ab, indem Sie eine Datei mit den Ergebnissen hochladen.

Hier erfahren Sie, wie Sie [Ihr CA-Zertifikat registrieren](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub).

## <a name="how-to-create-a-device-on-iot-hub"></a>Erstellen eines Geräts auf IoT Hub

Um einen Identitätswechsel des Geräts auszuschließen, müssen Sie IoT Hub mitteilen, welche Geräte zu erwarten sind.  Hierzu erstellen Sie einen Geräteeintrag in der Geräteregistrierung von IoT Hub.  Dieser Vorgang ist bei Verwendung von [Device Provisioning Service](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) von IoT Hub automatisiert. 

Hier erfahren Sie, wie Sie [ein Gerät in IoT Hub manuell erstellen](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Erstellen eines X.509-Geräts für Ihren IoT Hub

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Authentifizieren von Geräten, die mit X.509-Zertifikaten signiert sind

Wenn das X.509-CA-Zertifikat registriert ist und die Geräte in einer Zertifikatvertrauenskette signiert sind, bleibt noch die Geräteauthentifizierung, wenn das Gerät (auch zum ersten Mal) eine Verbindung herstellt.  Wenn ein X.509-CA-signiertes Gerät eine Verbindung herstellt, lädt es die Zertifikatkette zur Validierung hoch. Die Kette enthält alle Zwischenzertifizierungsstellen und Gerätezertifikate.  Anhand dieser Informationen authentifiziert IoT Hub das Gerät in einem Zwei-Schritte-Prozess.  IoT Hub überprüft die Zertifikatkette kryptografisch auf interne Konsistenz und richtet dann eine Eigentumsnachweisanforderung an das Gerät.  IoT Hub deklariert das Gerät bei einer positiven Eigentumsnachweisantwort des Geräts als authentisch.  Diese Deklaration setzt voraus, dass der private Schlüssel für das Gerät geschützt ist und nur das Gerät auf diese Anforderung erfolgreich reagieren kann.  Sie sollten sichere Chips wie Hardwaresicherheitsmodule (Hardware Secure Modules, HSM) zum Schützen privater Schlüssel in Geräten verwenden.

Eine erfolgreiche Geräteverbindung mit IoT Hub schließt den Authentifizierungsprozess ab und weist auch auf eine ordnungsgemäße Einrichtung hin.

Hier erfahren Sie, wie Sie [diesen Schritt der Geräteverbindung](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates) abschließen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [den Wert der X.509-CA-Authentifizierung](iot-hub-x509ca-concept.md) in IoT.

Nutzen Sie zum Einstieg den [IoT Hub Device Provisioning-Dienst](https://docs.microsoft.com/azure/iot-dps/).
