---
title: Azure IoT Hub Device Provisioning Service – X.509-Zertifikatnachweis
description: Beschreibt Konzepte, die für die Verwendung des X.509-Zertifikatnachweises bei Device Provisioning Service (DPS) und IoT Hub spezifisch sind.
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9eee315aac28847710662b463add7d6e68d8d505
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967294"
---
# <a name="x509-certificate-attestation"></a>X.509-Zertifikatnachweis

Dieser Artikel enthält eine Übersicht über die Konzepte des Device Provisioning-Diensts (DPS), die bei der Bereitstellung von Geräten mit dem X.509-Zertifikatnachweis zum Einsatz kommen. Dieser Artikel ist für alle Personen relevant, die an der Vorbereitung von Geräten für die Bereitstellung beteiligt sind.

X.509-Zertifikate können in einem Hardwaresicherheitsmodul (HSM) gespeichert werden.

> [!TIP]
> Es wird dringend empfohlen, ein HSM bei Geräten zu verwenden, um Geheimnisse wie das X.509-Zertifikat auf Ihren Geräten in der Produktionsumgebung sicher zu speichern.


## <a name="x509-certificates"></a>X.509-Zertifikate

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate sind üblicherweise in einer Zertifikatvertrauenskette angeordnet, in der jedes Zertifikat in der Kette durch den privaten Schlüssel des nächsthöheren Zertifikats signiert ist. Den Abschluss der Kette bildet ein selbstsigniertes Stammzertifikat. Durch diese Anordnung entsteht eine delegierte Vertrauenskette vom Stammzertifikat, das durch eine vertrauenswürdige Stammzertifizierungsstelle generiert wird, über jede Zwischenzertifizierungsstelle bis zum untergeordneten Zertifikat für die endgültige Entität, das auf einem Gerät installiert ist. Weitere Informationen finden Sie unter [Geräteauthentifizierung mit X.509-Zertifikaten](../iot-hub/iot-hub-x509ca-overview.md). 

Häufig repräsentiert die Zertifikatkette eine bestimmte logische oder physische Hierarchie im Zusammenhang mit den Geräten. Ein Hersteller kann beispielsweise:
- ein selbstsigniertes Stammzertifikat ausstellen
- das Stammzertifikat zum Erstellen eines eindeutigen Zertifizierungsstellen-Zwischenzertifikats für jedes Werk verwenden
- das Zertifikat jedes Werks zum Erstellen eines eindeutigen Zertifizierungsstellen-Zwischenzertifikats jeder Fertigungsstrecke im Werk verwenden
- und schließlich das Zertifikat der Fertigungsstrecke zum Erstellen eines eindeutigen Gerätezertifikats für jedes in dieser Strecke gefertigte Gerät verwenden. 

Weitere Informationen finden Sie unter [Konzeptgrundlagen der X.509-Zertifizierungsstellenzertifikate in der IoT-Branche](../iot-hub/iot-hub-x509ca-concept.md). 

### <a name="root-certificate"></a>Stammzertifikat

Ein Stammzertifikat ist ein selbstsigniertes X.509-Zertifikat, das eine Zertifizierungsstelle repräsentiert. Es handelt sich um den Endpunkt bzw. Vertrauensanker der Zertifikatkette. Stammzertifikate können von einer Organisation selbst ausgestellt oder bei einer Stammzertifizierungsstelle erworben werden. Weitere Informationen finden Sie unter [Abrufen von X.509-CA-Zertifikaten](../iot-hub/iot-hub-security-x509-get-started.md#get-x509-ca-certificates). Das Stammzertifikat kann auch als Zertifizierungsstellen-Stammzertifikat bezeichnet werden.

### <a name="intermediate-certificate"></a>Zwischenzertifikat

Ein Zwischenzertifikat ist ein X.509-Zertifikat, das vom Stammzertifikat (oder einem anderen Zwischenzertifikat mit dem Stammzertifikat in seiner Kette) signiert wurde. Das letzte Zwischenzertifikat in einer Kette wird zum Signieren des untergeordneten Zertifikats verwendet. Ein Zwischenzertifikat kann auch als Zertifizierungsstellen-Zwischenzertifikat bezeichnet werden.

##### <a name="why-are-intermediate-certs-useful"></a>Warum sind Zwischenzertifikate nützlich?
Zwischenzertifikate werden auf verschiedene Arten verwendet. Beispielsweise können Zwischenzertifikate genutzt werden, um Geräte nach Produktlinien, Käufen durch Kunden, Unternehmensabteilungen oder Werken zu gruppieren. 

Angenommen, Contoso ist ein großer Konzern mit eigener Public Key-Infrastruktur (PKI), für die ein Stammzertifikat mit dem Namen *ContosoRootCert* verwendet wird. Jedes Tochterunternehmen von Contoso verfügt über ein eigenes Zwischenzertifikat, das mit *ContosoRootCert* signiert wurde. Von jedem Tochterunternehmen wird das eigene Zwischenzertifikat dann verwendet, um seine untergeordneten Zertifikate für die einzelnen Geräte zu signieren. In diesem Szenario hat Contoso die Möglichkeit, nur eine DPS-Instanz zu verwenden, für die *ContosoRootCert* per [Eigentumsnachweis](./how-to-verify-certificates.md) verifiziert wurde. Es kann eine Registrierungsgruppe für jedes Tochterunternehmen verwendet werden. Die einzelnen Niederlassungen müssen sich dann nicht um die Verifizierung von Zertifikaten kümmern.


### <a name="end-entity-leaf-certificate"></a>Zertifikat für die endgültige Entität

Das untergeordnete Zertifikat bzw. Zertifikat für die endgültige Entität identifiziert den Zertifikatinhaber. Es verfügt in seiner Zertifikatkette über das Stammzertifikat sowie über null, ein oder mehrere Zwischenzertifikate. Mit dem untergeordneten Zertifikat werden kein anderen Zertifikate signiert. Es identifiziert das Gerät eindeutig beim Bereitstellungsdienst und wird zuweilen auch als Gerätezertifikat bezeichnet. Während der Authentifizierung verwendet das Gerät den mit diesem Zertifikat verknüpften Schlüssel, um auf eine Besitznachweisanforderung des Diensts zu antworten.

Die mit einem Eintrag [Individuelle Registrierung](./concepts-service.md#individual-enrollment) verwendeten untergeordneten Zertifikate erfordern, dass der **Antragsstellername** auf die Registrierungs-ID des Eintrags für die individuelle Registrierung gesetzt werden muss. Bei untergeordneten Zertifikaten mit dem Eintrag [Registrierungsgruppe](./concepts-service.md#enrollment-group) sollte der **Antragstellername** auf die gewünschte Geräte-ID festgelegt werden, die in den **Registrierungsdatensätzen** für das authentifizierte Gerät in der Registrierungsgruppe angezeigt wird.

Weitere Informationen finden Sie unter [Authentifizieren von Geräten, die mit X.509-Zertifikaten signiert sind](../iot-hub/iot-hub-x509ca-overview.md#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Steuern des Gerätezugriffs auf den Bereitstellungsdienst mit X.509-Zertifikaten

Vom Bereitstellungsdienst werden zwei Registrierungstypen verfügbar gemacht, die Sie verwenden können, um den Gerätezugriff mit dem X.509-Nachweismechanismus zu steuern:  

- [Individuelle Registrierung](./concepts-service.md#individual-enrollment): Diese Einträge werden mit dem Gerätezertifikat konfiguriert, das einem bestimmten Gerät zugeordnet ist. Diese Einträge steuern Registrierungen für bestimmte Geräte.
- [Registrierungsgruppe](./concepts-service.md#enrollment-group): Diese Einträge sind einem bestimmten Zertifizierungsstellen-Zwischenzertifikat oder -Stammzertifikat zugeordnet. Diese Einträge steuern die Registrierungen für alle Geräte, die in ihrer Zertifikatkette über ein Zertifizierungsstellen-Zwischenzertifikat oder -Stammzertifikat verfügen. 

#### <a name="dps-device-chain-requirements"></a>Anforderungen für DPS-Gerätekette

Wenn ein Gerät versucht, die Registrierung per DPS mit einer Registrierungsgruppe durchzuführen, muss das Gerät die Zertifikatkette vom untergeordneten Zertifikat an ein Zertifikat senden, das per [Eigentumsnachweis](how-to-verify-certificates.md) verifiziert wurde. Andernfalls tritt ein Authentifizierungsfehler auf.

Wenn beispielsweise nur das Stammzertifikat verifiziert und ein Zwischenzertifikat in die Registrierungsgruppe hochgeladen wird, sollte vom Gerät die Zertifikatkette vom untergeordneten Zertifikat durchgehend bis zum verifizierten Stammzertifikat bereitgestellt werden. Diese Zertifikatkette enthält dann alle beteiligten Zwischenzertifikate. Für die Authentifizierung tritt ein Fehler auf, wenn die Zertifikatkette vom DPS nicht bis zu einem verifizierten Zertifikat durchlaufen werden kann.

Stellen Sie sich beispielsweise vor, dass ein großes Unternehmen die unten angegebene Gerätekette für ein Gerät verwendet.

![Beispiel für Gerätezertifikatkette](./media/concepts-x509-attestation/example-device-cert-chain.png) 

Nur das Stammzertifikat wird verifiziert, und das Zertifikat *intermediate2* wird in die Registrierungsgruppe hochgeladen.

![Beispiel: Verifiziertes Stammzertifikat](./media/concepts-x509-attestation/example-root-verified.png) 

Für die Authentifizierung tritt ein Fehler auf, wenn das Gerät während der Bereitstellung nur die folgende Gerätekette sendet. Der Grund ist, dass vom DPS kein Authentifizierungsversuch durchgeführt werden kann, ohne dass die Gültigkeit des Zertifikats *intermediate1* bestätigt wurde.

![Beispiel: Fehler für Zertifikatkette](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

Wenn das Gerät während der Bereitstellung die vollständige Gerätekette wie folgt sendet, kann vom DPS versucht werden, die Authentifizierung des Geräts durchzuführen.

![Beispiel für Gerätezertifikatkette](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> Zwischenzertifikate können auch per [Eigentumsnachweis](how-to-verify-certificates.md) verifiziert werden.


#### <a name="dps-order-of-operations-with-certificates"></a>DPS-Reihenfolge für Vorgänge mit Zertifikaten
Wenn ein Gerät eine Verbindung mit dem Bereitstellungsdienst herstellt, räumt der Dienst spezifischeren Registrierungseinträge eine höhere Priorität ein als weniger spezifischen Registrierungseinträgen. Wenn also für das Gerät ein individueller Registrierungseintrag vorliegt, wendet der Bereitstellungsdienst diesen Eintrag an. Wenn keine individuelle Registrierung für das Gerät vorliegt und eine Registrierungsgruppe für das erste Zwischenzertifikat in der Zertifikatkette des Geräts vorhanden ist, wendet der Dienst diesen Eintrag an. Dies wird in der gesamten Kette bis hinunter zum Stammzertifikat fortgesetzt. Der Dienst wendet den ersten anwendbaren Eintrag an, den er findet. Dabei gilt Folgendes:

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