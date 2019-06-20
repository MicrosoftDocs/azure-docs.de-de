---
title: Sicherheitskonzepte beim Azure IoT Hub Device Provisioning-Dienst | Microsoft-Dokumentation
description: Beschreibt Konzepte der Sicherheitsbereitstellung, die speziell für Geräte mit dem Device Provisioning-Dienst und IoT Hub gelten
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e35330874c647eba2cddde694563c8a1d9e83df5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775116"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Sicherheitskonzepte beim IoT Hub Device Provisioning-Dienst 

Der IoT Hub Device Provisioning-Dienst ist ein Hilfsdienst für IoT Hub, mit dem Sie Geräte ohne manuelles Eingreifen auf einem angegebenen IoT Hub konfigurieren können. Mit dem Device Provisioning-Dienst können Sie Millionen von Geräten auf sichere und skalierbare Weise [automatisch bereitstellen](concepts-auto-provisioning.md). Dieser Artikel bietet eine Übersicht über die *Sicherheitskonzepte* bei der Gerätebereitstellung. Dieser Artikel ist für alle Personen relevant, die an der Vorbereitung von Geräten für die Bereitstellung beteiligt sind.

## <a name="attestation-mechanism"></a>Nachweismechanismus

Der Nachweismechanismus ist die Methode für das Überprüfen der Identität eines Geräts. Der Nachweismechanismus ist auch für die Registrierungsliste wichtig, über die der Bereitstellungsdienst erfährt, welche Methode zum Nachweis für ein bestimmtes Gerät verwendet werden soll.

> [!NOTE]
> IoT Hub verwendet das „Authentifizierungsschema“ für ein ähnliches Konzept in diesem Dienst.

Der Device Provisioning-Dienst unterstützt die folgenden Arten von Nachweisen:
* **X.509-Zertifikate** – basierend auf dem Standardauthentifizierungsablauf für X.509-Zertifikate.
* **Trusted Platform Module (TPM)** – basierend auf einer Nonce-Abfrage mit dem TPM-Standard für Schlüssel zum Bereitstellen eines signierten SAS-Tokens (Shared Access Signature). Diese Art von Nachweis erfordert keine physische TPM-Instanz auf dem Gerät. Der Dienst erwartet für den Nachweis jedoch den Endorsement Key gemäß [TPM-Spezifikation](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrischer Schlüssel** – basierend auf Shared Access Signature (SAS)-[Sicherheitstokens](../iot-hub/iot-hub-devguide-security.md#security-tokens), die eine Hashsignatur und ein eingebettetes Ablaufdatum haben. Weitere Informationen finden Sie unter [Symmetric key attestation](concepts-symmetric-key-attestation.md) (Nachweis des symmetrischen Schlüssels).


## <a name="hardware-security-module"></a>Hardwaresicherheitsmodul

Das Hardwaresicherheitsmodul (HSM) dient der sicheren, hardwarebasierten Speicherung von Gerätegeheimnissen und gilt als die sicherste Form der Geheimnisspeicherung. Sowohl X.509-Zertifikate als auch SAS-Token können im HSM gespeichert werden. HSMs können mit beiden Nachweismechanismen verwendet werden, die die Bereitstellung unterstützt.

> [!TIP]
> Es wird dringend empfohlen, ein HSM mit Geräten zu verwenden, um Geheimnisse sicher auf Ihren Geräten zu speichern.

Gerätegeheimnisse können auch in Software (Arbeitsspeicher) gespeichert werden. Dies gilt jedoch im Vergleich zu einem HSM als die weniger sichere Speicherform.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM kann sich auf einen Standard zum sicheren Speichern von Schlüsseln, mit denen die Plattform authentifiziert wird, oder auf die E/A-Schnittstelle beziehen, die für die Interaktion mit den Modulen zum Implementieren des Standards verwendet wird. TPMs können als diskrete Hardware, integrierte Hardware, firmwarebasiert oder softwarebasiert vorhanden sein. Erfahren Sie mehr über [TPMs und TPM-Nachweis](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Der Device Provisioning-Dienst unterstützt nur TPM 2.0.

Der TPM-Nachweis basiert auf einer Nonce-Abfrage, die den Endorsement Key und den Speicherstammschlüssel verwendet, um ein signiertes Token für die Shared Access Signature (SAS) bereitzustellen.

### <a name="endorsement-key"></a>Endorsement Key

Der Endorsement Key ist ein asymmetrischer Schlüssel im TPM, der zur Fertigungszeit intern generiert oder eingefügt wurde und für jedes TPM eindeutig ist. Der Endorsement Key kann nicht geändert oder entfernt werden. Der private Teil des Endorsement Key wird niemals außerhalb des TPM freigegeben. Der öffentliche Teil des Endorsement Key wird dagegen für die Erkennung eines Original-TPM verwendet. Erfahren Sie mehr über den [Endorsement Key](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Speicherstammschlüssel

Der Speicherstammschlüssel wird im TPM gespeichert und zum Schützen von in Anwendungen erstellten TPM-Schlüsseln verwendet, sodass diese Schlüssel nicht ohne das TPM verwendet werden können. Der Speicherstammschlüssel wird generiert, wenn Sie den Besitz des TPM übernehmen. Wenn Sie das TPM löschen, damit ein neuer Benutzer den Besitz übernehmen kann, wird ein neuer Speicherstammschlüssel generiert. Erfahren Sie mehr über den [Speicherstammschlüssel](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509-Zertifikate

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate sind üblicherweise in einer Zertifikatvertrauenskette angeordnet, in der jedes Zertifikat in der Kette durch den privaten Schlüssel des nächsthöheren Zertifikats signiert ist. Den Abschluss der Kette bildet ein selbstsigniertes Stammzertifikat. Durch diese Anordnung entsteht eine delegierte Vertrauenskette vom Stammzertifikat, das durch eine vertrauenswürdige Stammzertifizierungsstelle generiert wird, über jede Zwischenzertifizierungsstelle bis zum untergeordneten Zertifikat für die endgültige Entität, das auf einem Gerät installiert ist. Weitere Informationen finden Sie unter [Geräteauthentifizierung mit X.509-Zertifikaten](/azure/iot-hub/iot-hub-x509ca-overview). 

Häufig repräsentiert die Zertifikatkette eine bestimmte logische oder physische Hierarchie im Zusammenhang mit den Geräten. Ein Hersteller kann beispielsweise:
- ein selbstsigniertes Stammzertifikat ausstellen
- das Stammzertifikat zum Erstellen eines eindeutigen Zertifizierungsstellen-Zwischenzertifikats für jedes Werk verwenden
- das Zertifikat jedes Werks zum Erstellen eines eindeutigen Zertifizierungsstellen-Zwischenzertifikats jeder Fertigungsstrecke im Werk verwenden
- und schließlich das Zertifikat der Fertigungsstrecke zum Erstellen eines eindeutigen Gerätezertifikats für jedes in dieser Strecke gefertigte Gerät verwenden. 

Weitere Informationen finden Sie unter [Konzeptgrundlagen der X.509-Zertifizierungsstellenzertifikate in der IoT-Branche](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Stammzertifikat

Ein Stammzertifikat ist ein selbstsigniertes X.509-Zertifikat, das eine Zertifizierungsstelle repräsentiert. Es handelt sich um den Endpunkt bzw. Vertrauensanker der Zertifikatkette. Stammzertifikate können von einer Organisation selbst ausgestellt oder bei einer Stammzertifizierungsstelle erworben werden. Weitere Informationen finden Sie unter [Abrufen von X.509-CA-Zertifikaten](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Das Stammzertifikat kann auch als Zertifizierungsstellen-Stammzertifikat bezeichnet werden.

### <a name="intermediate-certificate"></a>Zwischenzertifikat

Ein Zwischenzertifikat ist ein X.509-Zertifikat, das vom Stammzertifikat (oder einem anderen Zwischenzertifikat mit dem Stammzertifikat in seiner Kette) signiert wurde. Das letzte Zwischenzertifikat in einer Kette wird zum Signieren des untergeordneten Zertifikats verwendet. Ein Zwischenzertifikat kann auch als Zertifizierungsstellen-Zwischenzertifikat bezeichnet werden.

### <a name="end-entity-leaf-certificate"></a>Zertifikat für die endgültige Entität

Das untergeordnete Zertifikat bzw. Zertifikat für die endgültige Entität identifiziert den Zertifikatinhaber. Es verfügt in seiner Zertifikatkette über das Stammzertifikat sowie über null, ein oder mehrere Zwischenzertifikate. Mit dem untergeordneten Zertifikat werden kein anderen Zertifikate signiert. Es identifiziert das Gerät eindeutig beim Bereitstellungsdienst und wird zuweilen auch als Gerätezertifikat bezeichnet. Während der Authentifizierung verwendet das Gerät den mit diesem Zertifikat verknüpften Schlüssel, um auf eine Besitznachweisanforderung des Diensts zu antworten.

Die mit einem Eintrag [Individuelle Registrierung](./concepts-service.md#individual-enrollment) verwendeten untergeordneten Zertifikate erfordern, dass der **Antragsstellername** auf die Registrierungs-ID des Eintrags für die individuelle Registrierung gesetzt werden muss. Bei mit einem Eintrag [Registrierungsgruppe](./concepts-service.md#enrollment-group) verwendeten untergeordneten Zertifikate sollte der **Antragstellername** auf die gewünschte Geräte-ID gesetzt werden, die in den **Registrierungsdatensätzen** für das authentifizierte Gerät in der Registrierungsgruppe angezeigt wird.

Weitere Informationen finden Sie unter [Authentifizieren von Geräten, die mit X.509-Zertifikaten signiert sind](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Steuern des Gerätezugriffs auf den Bereitstellungsdienst mit X.509-Zertifikaten

Der Bereitstellungsdienst macht zwei Arten von Registrierungseinträgen verfügbar, mit denen Sie den Zugriff auf Geräte steuern können, die den X.509-Nachweismechanismus verwenden:  

- [Individuelle Registrierung](./concepts-service.md#individual-enrollment): Diese Einträge werden mit dem Gerätezertifikat konfiguriert, das einem bestimmten Gerät zugeordnet ist. Diese Einträge steuern Registrierungen für bestimmte Geräte.
- [Registrierungsgruppe](./concepts-service.md#enrollment-group): Diese Einträge sind einem bestimmten Zertifizierungsstellen-Zwischenzertifikat oder -Stammzertifikat zugeordnet. Diese Einträge steuern die Registrierungen für alle Geräte, die in ihrer Zertifikatkette über ein Zertifizierungsstellen-Zwischenzertifikat oder -Stammzertifikat verfügen. 

Wenn ein Gerät eine Verbindung mit dem Bereitstellungsdienst herstellt, räumt der Dienst spezifischeren Registrierungseinträge eine höhere Priorität ein als weniger spezifischen Registrierungseinträgen. Wenn also für das Gerät ein individueller Registrierungseintrag vorliegt, wendet der Bereitstellungsdienst diesen Eintrag an. Wenn keine individuelle Registrierung für das Gerät vorliegt und eine Registrierungsgruppe für das erste Zwischenzertifikat in der Zertifikatkette des Geräts vorhanden ist, wendet der Dienst diesen Eintrag an. Dies wird in der gesamten Kette bis zum Stammzertifikat fortgesetzt. Der Dienst wendet den ersten anwendbaren Eintrag an, den er findet. Dabei gilt Folgendes:

- Wenn der erste gefundene Registrierungseintrag aktiviert ist, stellt der Dienst das Gerät bereit.
- Wenn der erste gefundene Registrierungseintrag deaktiviert ist, stellt der Dienst das Gerät nicht bereit.  
- Wenn für keins der Zertifikate in der Zertifikatkette des Geräts ein Registrierungseintrag gefunden wird, stellt der Dienst das Gerät nicht bereit. 

Dieser Mechanismus und die hierarchische Struktur von Zertifikatketten bieten ein hohes Maß an Flexibilität bei der Steuerung des Zugriffs für einzelne Geräte ebenso wie für Gerätegruppen. Nehmen Sie beispielsweise an, Sie verfügen über fünf Geräte mit folgenden Zertifikatketten: 

- *Gerät 1*: Stammzertifikat -> Zertifikat A -> Zertifikat für Gerät 1
- *Gerät 2*: Stammzertifikat -> Zertifikat A -> Zertifikat für Gerät 2
- *Gerät 3*: Stammzertifikat -> Zertifikat A -> Zertifikat für Gerät 3
- *Gerät 4*: Stammzertifikat -> Zertifikat B -> Zertifikat für Gerät 4
- *Gerät 5*: Stammzertifikat -> Zertifikat B -> Zertifikat für Gerät 5

Anfangs können Sie einen einzigen aktivierten Gruppenregistrierungseintrag für das Stammzertifikat erstellen, um allen fünf Geräten den Zugriff zu ermöglichen. Sollte zu einem späteren Zeitpunkt Zertifikat B kompromittiert werden, können Sie einen deaktivierten Registrierungsgruppeneintrag für Zertifikat B erstellen, um die Registrierung von *Gerät 4* und *Gerät 5* zu verhindern. Wenn später auch *Gerät 3* kompromittiert wird, können Sie einen deaktivierten Registrierungseintrag für das Zertifikat dieses Geräts erstellen. Damit wird der Zugriff für *Gerät 3* widerrufen, *Gerät 1* und *Gerät 2* können sich jedoch weiterhin registrieren.