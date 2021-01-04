---
title: Verwalten von Geräteregistrierungen mithilfe von Azure-DPS-SDKs
description: Verwalten von Geräteregistrierungen im IoT Hub Device Provisioning Service (DPS) mithilfe der Service-SDKs
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: fasttrack-edit, iot
services: iot-dps
ms.openlocfilehash: 45a2b7a64006ab6963290be3ac86a3a5d1e4916d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010978"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Gewusst wie: Verwalten von Geräteregistrierungen mit Azure Device Provisioning Service-SDKs
Eine *Geräteregistrierung* erstellt einen Datensatz eines einzelnen Geräts oder einer Gruppe von Geräten, die sich jederzeit beim Device Provisioning-Dienst registrieren können. Der Registrierungsdatensatz enthält die erste gewünschte Konfiguration für die Geräte im Rahmen dieser Registrierung, einschließlich des gewünschten IoT Hubs. In diesem Artikel wird erläutert, wie Geräteregistrierungen für Ihren Bereitstellungsdienst programmgesteuert mithilfe von Azure IoT Provisioning Service-SDKs verwaltet werden.  Die SDKs sind auf GitHub im gleichen Repository wie Azure IoT-SDKs verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
* Rufen Sie die Verbindungszeichenfolge für die Device Provisioning-Dienstinstanz ab.
* Rufen Sie die Gerätesicherheitsartefakte für den [Nachweismechanismus](concepts-service.md#attestation-mechanism) ab:
    * [**Trusted Platform Module (TPM)**](./concepts-tpm-attestation.md):
        * Individuelle Registrierung: Registrierungs-ID und TPM Endorsement Key eines physischen Geräts oder TPM-Simulators
        * Die Registrierungsgruppe gilt nicht als TPM-Nachweis.
    * [**X.509**](./concepts-service.md#attestation-mechanism):
        * Individuelle Registrierung: Das [untergeordnetes Zertifikat](./concepts-service.md#attestation-mechanism) eines physischen Geräts oder des SDK-[DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)-Emulators.
        * Registrierungsgruppe: [ZS-/Stammzertifikat](./concepts-x509-attestation.md#root-certificate) oder [Zwischenzertifikat](./concepts-x509-attestation.md#intermediate-certificate) zum Generieren eines Gerätezertifikats auf einem physischen Gerät.  Dieses kann auch über den SDK-DICE-Emulator generiert werden.
* Die genauen API-Aufrufe können aufgrund von Sprachunterschieden voneinander abweichen. Überprüfen Sie die Beispiele, die wir auf GitHub als weitergehende Informationsquelle bereitstellen:
   * [Java Provisioning Service Client samples (Beispiele für den Bereitstellungsdienstclient für Java)](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js Provisioning Service Client samples (Beispiele für den Bereitstellungsdienstclient für Node.js)](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET Provisioning Service Client samples (Beispiele für den Bereitstellungsdienstclient für .NET)](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Erstellen einer Geräteregistrierung
Sie haben zwei Möglichkeiten, Ihre Geräte beim Bereitstellungsdienst zu registrieren:

* Eine **Registrierungsgruppe** ist ein Eintrag für eine Gruppe von Geräten mit einem gemeinsamen Nachweismechanismus von X. 509-Zertifikaten, signiert vom [Stammzertifikat](./concepts-x509-attestation.md#root-certificate) oder [Zwischenzertifikat](./concepts-x509-attestation.md#intermediate-certificate). Es empfiehlt sich, eine Registrierungsgruppe für eine große Anzahl von Geräten, die eine gewünschte Erstkonfiguration gemeinsam nutzen, oder für Geräte zu verwenden, die alle demselben Mandanten zugeordnet sind. Beachten Sie, dass Sie nur Geräte registrieren können, die den X. 509-Nachweismechanismus als *Registrierungsgruppen* verwenden. 

    Anhand des folgenden Workflows können Sie eine Registrierungsgruppe mit den SDKs erstellen:

    1. Bei einer Registrierungsgruppe wird als Nachweismechanismus das X.509-Stammzertifikat verwendet.  Rufen Sie die Dienst-SDK-API ```X509Attestation.createFromRootCertificate``` mit dem Stammzertifikat auf, um einen Nachweis für die Registrierung zu erstellen.  Das X.509-Stammzertifikat wird entweder als PEM-Datei oder Zeichenfolge bereitgestellt.
    1. Erstellen Sie mit dem erstellten ```attestation```-Bezeichner und einem eindeutigen ```enrollmentGroupId```-Bezeichner eine neue ```EnrollmentGroup```-Variable.  Optional können Sie Parameter wie ```IoTHubHostName``` und ```ProvisioningStatus``` festlegen.
    2. Rufen Sie mit ```EnrollmentGroup``` die Dienst-SDK-API ```createOrUpdateEnrollmentGroup``` in Ihrer Back-End-Anwendung auf, um eine Registrierungsgruppe zu erstellen.

* Eine **individuelle Registrierung** ist ein Eintrag für ein einzelnes Gerät, das registriert werden kann. Individuelle Registrierungen verwenden entweder X.509-Zertifikate oder SAS-Token (in einem physischen oder virtuellen TPM) als Nachweismechanismen. Wir empfehlen individuelle Registrierungen für Geräte, die besondere Erstkonfigurationen erfordern oder die nur SAS-Token über das TPM oder das virtuelle TPM als Nachweismechanismus verwenden können. Bei individuellen Registrierungen ist möglicherweise die gewünschte IoT Hub-Geräte-ID angegeben.

    Anhand des folgenden Workflows können Sie eine individuelle Registrierung mit den SDKs erstellen:
    
    1. Wählen Sie Ihren ```attestation```-Mechanismus aus, d.h. TPM oder X.509.
        1. **TPM**: Mit dem Endorsement Key eines physischen Geräts oder TPM-Simulators als Eingabe können Sie die Dienst-SDK-API ```TpmAttestation``` zum Erstellen eines Nachweis für die Registrierung aufrufen. 
        2. **X.509**: Mit dem Clientzertifikat als Eingabe können Sie die Dienst-SDK-API ```X509Attestation.createFromClientCertificate``` zum Erstellen eines Nachweises für die Registrierung aufrufen.
    2. Erstellen Sie mithilfe von ```attestation``` und einer eindeutigen ```registrationId``` als Eingabe eine neue ```IndividualEnrollment```-Variable, die sich auf Ihrem Gerät befindet oder über den TPM-Simulator generiert wurde.  Optional können Sie Parameter wie ```Device ID```, ```IoTHubHostName``` und ```ProvisioningStatus``` festlegen.
    3. Rufen Sie mit ```IndividualEnrollment``` die Dienst-SDK-API ```createOrUpdateIndividualEnrollment``` in Ihrer Back-End-Anwendung auf, um eine individuelle Registrierung zu erstellen.

Wenn Sie eine Registrierung erfolgreich erstellt haben, gibt der Device Provisioning-Dienst ein Registrierungsergebnis zurück. Dieser Workflow wird in den [oben genannten](#prerequisites) Beispielen veranschaulicht.

## <a name="update-an-enrollment-entry"></a>Aktualisieren eines Registrierungseintrags

Nachdem Sie einen Registrierungseintrag erstellt haben, sollten Sie die Registrierung aktualisieren.  Zu den möglichen Szenarien zählen das Aktualisieren der gewünschten Eigenschaft, das Aktualisieren der Nachweismethode oder das Widerrufen des Gerätezugriffs.  Es gibt unterschiedliche APIs für die individuelle Registrierung und Gruppenregistrierung, jedoch keinen Unterschied beim Nachweismechanismus.

Anhand des folgenden Workflows können Sie einen Registrierungseintrag aktualisieren:
* **Individuelle Registrierung:**
    1. Rufen Sie mithilfe der Dienst-SDK-API ```getIndividualEnrollment``` zuerst die neueste Registrierung vom Bereitstellungsdienst ab.
    2. Ändern Sie bei Bedarf den Parameter der neuesten Registrierung. 
    3. Rufen Sie anhand der neuesten Registrierung die Dienst-SDK-API ```createOrUpdateIndividualEnrollment``` auf, um Ihren Registrierungseintrag zu aktualisieren.
* **Gruppenregistrierung**:
    1. Rufen Sie mithilfe der Dienst-SDK-API ```getEnrollmentGroup``` zuerst die neueste Registrierung vom Bereitstellungsdienst ab.
    2. Ändern Sie bei Bedarf den Parameter der neuesten Registrierung.
    3. Rufen Sie anhand der neuesten Registrierung die Dienst-SDK-API ```createOrUpdateEnrollmentGroup``` auf, um Ihren Registrierungseintrag zu aktualisieren.

Dieser Workflow wird in den [oben genannten](#prerequisites) Beispielen veranschaulicht.

## <a name="remove-an-enrollment-entry"></a>Entfernen eines Registrierungseintrags

* **Individuelle Registrierungen** können durch Aufrufen der Dienst-SDK-API ```deleteIndividualEnrollment``` mit ```registrationId``` gelöscht werden.
* **Gruppenregistrierungen** können durch Aufrufen der Dienst-SDK-API ```deleteEnrollmentGroup``` mit ```enrollmentGroupId``` gelöscht werden.

Dieser Workflow wird in den [oben genannten](#prerequisites) Beispielen veranschaulicht.

## <a name="bulk-operation-on-individual-enrollments"></a>Massenvorgang für individuelle Registrierungen

Anhand des folgenden Workflows können Sie einen Massenvorgang zum Erstellen, Aktualisieren oder Entfernen verschiedener individueller Registrierungen durchführen:

1. Erstellen Sie eine Variable, die mehrere ```IndividualEnrollment``` enthält.  Die Implementierung dieser Variablen variiert je nach Sprache.  Weitere Informationen finden Sie im Beispiel für den Massenvorgang auf GitHub.
2. Rufen Sie die Dienst-SDK-API ```runBulkOperation``` mit ```BulkOperationMode``` auf, um den gewünschten Vorgang und Ihre Variable für individuelle Registrierungen zu verwenden. Es werden vier Modi unterstützt: „create“, „update“, „updateIfMatchEtag“ und „delete“.

Nachdem Sie einen Vorgang erfolgreich durchgeführt haben, gibt der Device Provisioning-Dienst ein Registrierungsergebnis zurück.

Dieser Workflow wird in den [oben genannten](#prerequisites) Beispielen veranschaulicht.