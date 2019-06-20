---
title: Azure IoT Hub Device Provisioning-Dienst – TPM-Nachweis
description: Dieser Artikel bietet eine konzeptionelle Übersicht über den Ablauf beim TPM-Nachweis mit dem IoT Device Provisioning-Dienst.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 07c5dbce0b98d1c197164f4fc77682f78ede57f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746430"
---
# <a name="tpm-attestation"></a>TPM-Nachweis

Der IoT Hub Device Provisioning-Dienst ist ein Hilfsdienst für IoT Hub, mit dem Sie Geräte ohne manuelles Eingreifen auf einem angegebenen IoT Hub konfigurieren können. Mit dem Device Provisioning-Dienst können Sie Millionen von Geräten auf sichere Weise bereitstellen.

Dieser Artikel beschreibt den Prozess des Identitätsnachweises bei Verwendung eines [TPM](./concepts-device.md). TPM steht für Trusted Platform Module und ist ein Typ von Hardwaresicherheitsmodul (HSM). In diesem Artikel wird davon ausgegangen, dass Sie ein diskretes, integriertes oder Firmware-TPM verwenden. Durch Software emulierte TPMs eignen sich gut für die Prototyperstellung oder Tests, bieten aber nicht das gleiche Maß an Sicherheit wie diskrete, integrierte oder Firmware-TPMs. Die Verwendung von Software-TPMs in der Produktion wird nicht empfohlen. Weitere Informationen zu den Arten von TPMs finden Sie unter [A Brief Introduction to TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf) (Eine kurze Einführung in TPM).

Dieser Artikel bezieht sich nur auf Geräte mit TPM 2.0 und HMAC-Schlüsselunterstützung sowie den entsprechenden Endorsement Keys. Er ist nicht für Geräte gedacht, die X.509-Zertifikate für die Authentifizierung verwenden. TPM ist ein branchenweiter ISO-Standard von Trusted Computing Group. Weitere Informationen zu TPM finden Sie in der [vollständigen TPM 2.0-Spezifikation](https://trustedcomputinggroup.org/tpm-library-specification/) oder der [ISO/IEC 11889-Spezifikation](https://www.iso.org/standard/66510.html). In diesem Artikel wird außerdem davon ausgegangen, dass Sie mit öffentlichen und privaten Schlüsselpaaren sowie deren Verwendung für die Verschlüsselung vertraut sind.

Die Geräte-SDKs des Device Provisioning-Diensts führen alle in diesem Artikel beschriebenen Schritte für Sie aus. Wenn Sie die SDKs auf Ihren Geräten verwenden, müssen Sie keine weiteren Implementierungen vornehmen. Dieser Artikel bietet eine konzeptionelle Beschreibung der Abläufe für den TPM-Sicherheitschip bei Bereitstellung Ihres Geräts, und es wird erläutert, warum diese Methode so sicher ist.

## <a name="overview"></a>Übersicht

TPMs verwenden den sogenannten Endorsement Key (EK) als sicheren Vertrauensanker. Der EK ist für das TPM eindeutig, und wenn er geändert wird, ändert sich im Grunde genommen das Gerät in ein neues Gerät.

TPMs verfügen noch über einen weiteren Schlüsseltyp, der Speicherstammschlüssel (Storage Root Key, SRK) genannt wird. Ein SRK kann vom Besitzer des TPM generiert werden, nachdem er die Besitzrechte für das TPM übernommen hat. Die Besitzübernahme für das TPM ist die TPM-spezifische Art zu sagen, dass "jemand ein Kennwort für das HSM festlegt". Wenn ein TPM-Gerät an einen neuen Besitzer verkauft wird, kann dieser die Besitzrechte für das TPM übernehmen, um einen neuen SRK zu generieren. Durch das Generieren eines neuen SRK wird sichergestellt, dass der vorherige Besitzer das TPM nicht verwenden kann. Da der SRK für den Besitzer des TPM eindeutig ist, kann er zum Versiegeln von Daten im TPM für diesen Besitzer verwendet werden. Der SRK stellt dem Besitzer eine Sandbox zum Speichern seiner Schlüssel bereit und bietet Widerrufbarkeit von Zugriffsrechten, wenn das Gerät oder TPM verkauft wird. Es ist wie beim Umziehen in ein neues Haus: Bei der Besitzübernahme werden die Türschlösser ausgetauscht und alle vom vorherigen Besitzer zurückgelassenen Möbel entfernt (SRK), doch können Sie die Adresse des Hauses nicht ändern (EK).

Sobald ein Gerät eingerichtet und zur Verwendung bereit ist, verfügt es sowohl über einen EK als auch einen SRK.

![Besitzübernahme für ein TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Hinweis zum Übernehmen der Besitzrechte am TPM: Die Besitzübernahme für ein TPM hängt von einer Vielzahl von Faktoren ab. Dazu gehören der TPM-Hersteller, die Gruppe der verwendeten TPM-Tools und das Betriebssystem des Geräts. Befolgen Sie die Anweisungen für Ihr jeweiliges System, um den Besitz zu übernehmen.

Der Device Provisioning-Dienst verwendet den öffentlichen Teil des EK (EK_pub) zum Identifizieren und Registrieren von Geräten. Der Gerätehersteller kann den EK_pub bei der Herstellung oder abschließenden Tests lesen und ihn in den Provisioning-Dienst hochladen, damit das Gerät beim Herstellen einer Verbindung für die Bereitstellung erkannt wird. Der Device Provisioning-Dienst nimmt keine Überprüfung des SRK oder Besitzers vor, sodass durch ein „Löschen“ des TPM Kundendaten unwiderruflich entfernt werden, doch wird der EK (und andere Herstellerdaten) beibehalten, und das Gerät wird weiterhin vom Device Provisioning-Dienst erkannt, wenn eine Verbindung für die Bereitstellung hergestellt wird.

## <a name="detailed-attestation-process"></a>Detaillierter Nachweisprozess

Wenn ein Gerät mit einem TPM zum ersten Mal eine Verbindung mit dem Device Provisioning-Dienst herstellt, vergleicht der Dienst zuerst den bereitgestellten EK_pub mit dem in der Registrierungsliste gespeicherten EK_pub. Stimmen die EK_pubs nicht überein, ist das Gerät für die Bereitstellung nicht zulässig. Stimmen die EK_pubs überein, fordert der Dienst vom Gerät einen Besitznachweis für den privaten Teil des EK über eine Nonce-Abfrage, bei der es sich um eine sichere Abfrage zum Nachweis der Identität handelt. Der Device Provisioning-Dienst generiert eine Nonce und verschlüsselt sie dann mit dem SRK und anschließend mit dem EK_pub, die beide vom Gerät beim anfänglichen Registrierungsaufruf bereitgestellt werden. Der private Teil des EK bleibt beim TPM immer geschützt. Dadurch werden Fälschungen verhindert, und es ist sichergestellt, dass SAS-Token für autorisierte Geräte auf sichere Weise bereitgestellt werden.

Es folgt nun eine detaillierte Beschreibung des Nachweisprozesses.

### <a name="device-requests-an-iot-hub-assignment"></a>Anforderung einer IoT Hub-Zuweisung durch das Gerät

Zuerst stellt das Gerät eine Verbindung mit dem Device Provisioning-Dienst her und fordert eine Bereitstellung an. Dabei teilt das Gerät dem Dienst seine Registrierungs-ID, einen ID-Bereich sowie den EK_pub und SRK_pub des TPM mit. Der Dienst sendet die verschlüsselte Nonce an das Gerät zurück und fordert das Gerät auf, sie zu entschlüsseln und dann diese entschlüsselte Nonce zum Signieren eines SAS-Tokens zu verwenden, um erneut eine Verbindung herzustellen und die Bereitstellung abzuschließen.

![Anforderung der Bereitstellung durch das Gerät](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce-Abfrage

Das Gerät empfängt die Nonce und verwendet die privaten Teile des EK und SRK zum Entschlüsseln der Nonce in das TPM. Durch die Reihenfolge der Nonce-Verschlüsselung wird die Vertrauensstellung vom EK, der unveränderlich ist, an den SRK delegiert, der sich ändern kann, wenn ein neuer Besitzer das TPM übernimmt.

![Entschlüsseln der Nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Überprüfen der Nonce und Empfangen der Anmeldeinformationen

Das Gerät kann dann ein SAS-Token anhand der entschlüsselten Nonce signieren und mit dem signierten SAS-Token erneut eine Verbindung mit dem Device Provisioning-Dienst herstellen. Nach Abschluss der Nonce-Abfrage lässt der Dienst eine Bereitstellung für das Gerät zu.

![Erneutes Herstellen einer Verbindung mit dem Device Provisioning-Dienst zum Überprüfen der EK-Besitzrechte](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Nächste Schritte

Nun stellt das Gerät eine Verbindung mit IoT Hub her, und Sie können darauf vertrauen, dass Ihre Geräteschlüssel sicher aufbewahrt sind. Nachdem Sie nun erfahren haben, wie der Device Provisioning-Dienst die Identität eines Geräts mit TPM auf sichere Weise überprüft, lesen Sie die folgenden Artikeln, um weitere Informationen zu erhalten:

* [Informationen zu allen Konzepten für die automatische Bereitstellung](./concepts-auto-provisioning.md)
* [Erste Schritte mit der automatischen Bereitstellung](./quick-setup-auto-provision.md) unter Verwendung von SDKs für den Ablauf
