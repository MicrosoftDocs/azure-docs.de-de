---
title: TLS-Zertifikatänderungen für Azure
description: TLS-Zertifikatänderungen für Azure
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 04b68899b796088bd4b89509a413c7a1fe5369bb
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475351"
---
# <a name="azure-tls-certificate-changes"></a>TLS-Zertifikatänderungen für Azure  

Microsoft aktualisiert Azure-Dienste für die Verwendung von TLS-Zertifikaten aus einer anderen Gruppe von Stammzertifizierungsstellen (Certificate Authorities, CAs). Diese Änderung wird vorgenommen, da die aktuellen Zertifikate der Zertifizierungsstellen eine der grundlegenden Anforderungen des Zertifizierungsstellen-/Browserforums nicht erfüllen und am 15. Februar 2021 widerrufen werden.

## <a name="when-will-this-change-happen"></a>Wann wird diese Änderung durchgeführt?

Bereits vorhandene Azure-Endpunkte wurden ab dem 13. August 2020 phasenweise umgestellt. Alle neu erstellten TLS/SSL-Azure-Endpunkte enthalten aktualisierte Zertifikate, die mit den neuen Stammzertifizierungsstellen verkettet sind.

Dienstspezifische Details:

- Für [Azure AD-Dienste](../../active-directory/index.yml) (Azure Active Directory) wurde diese Umstellung am 7. Juli 2020 initiiert.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) und [DPS](../../iot-dps/index.yml) verbleiben in der Baltimore CyberTrust Root-Zertifizierungsstelle, die Zwischenzertifizierungsstellen ändern sich jedoch. Klicken Sie [hier](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456), um ausführliche Informationen zu erhalten.
- [Azure Storage](../../storage/index.yml) verbleibt in der Baltimore CyberTrust Root-Zertifizierungsstelle, die Zwischenzertifizierungsstellen ändern sich jedoch. Klicken Sie [hier](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518), um ausführliche Informationen zu erhalten.
- [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) verbleibt in der Baltimore CyberTrust Root-Zertifizierungsstelle, die Zwischenzertifizierungsstellen ändern sich jedoch. Klicken Sie [hier](../../azure-cache-for-redis/cache-whats-new.md), um ausführliche Informationen zu erhalten.
- Azure Instance Metadata Service verbleibt in der Baltimore CyberTrust-Stammzertifizierungsstelle, die Zwischenzertifizierungsstellen ändern sich jedoch. Klicken Sie [hier](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html), um ausführliche Informationen zu erhalten.

> [!IMPORTANT]
> Kunden müssen ihre Anwendungen nach dieser Änderung ggf. aktualisieren, um Konnektivitätsfehler beim Herstellen einer Verbindung mit Azure-Diensten zu vermeiden.

## <a name="what-is-changing"></a>Was ändert sich?

Heutzutage sind die meisten der von Azure-Diensten verwendeten TLS-Zertifikate mit der folgenden Stammzertifizierungsstelle verkettet:

| Allgemeiner Name der Zertifizierungsstelle | Fingerabdruck (SHA-1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Von Azure-Diensten verwendete TLS-Zertifikate werden mit einer der folgenden Stammzertifizierungsstellen verkettet:

| Allgemeiner Name der Zertifizierungsstelle | Fingerabdruck (SHA-1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Wann kann ich den alten Zwischenfingerabdruck entfernen?

Die aktuellen Zertifizierungsstellenzertifikate werden bis zum 15. Februar 2021 *nicht* widerrufen. Nach diesem Datum können Sie die alten Fingerabdrücke aus Ihrem Code entfernen.

Sollte sich dieses Datum ändern, wird das neue Widerrufsdatum bekanntgegeben.

## <a name="will-this-change-affect-me"></a>Betrifft mich diese Änderung? 

Wir gehen davon aus, dass **die meisten Azure-Kunden nicht betroffen sein werden**.  Ihre Anwendung kann jedoch betroffen sein, wenn darin explizit eine Liste zulässiger Zertifizierungsstellen angegeben wird. Dies wird als Anheften von Zertifikaten bezeichnet.

Im Anschluss finden Sie verschiedene Methoden, mit denen Sie ermitteln können, ob Ihre Anwendung betroffen ist:

- Suchen Sie in Ihrem Quellcode nach dem Fingerabdruck, nach dem allgemeinen Namen und nach anderen Zertifikateigenschaften der Microsoft IT TLS-Zertifizierungsstellen, die [hier](https://www.microsoft.com/pki/mscorp/cps/default.htm) angegeben sind. Wird ein entsprechender Wert gefunden, ist Ihre Anwendung betroffen. Aktualisieren Sie in diesem Fall den Quellcode mit den neuen Zertifizierungsstellen, um das Problem zu beheben. Es empfiehlt sich grundsätzlich, sicherzustellen, dass Zertifizierungsstellen kurzfristig hinzugefügt oder bearbeitet werden können. Zertifizierungsstellenzertifikate müssen aufgrund von Branchenbestimmungen innerhalb von sieben Tagen ersetzt werden. Kunden, die das Anheften von Zertifikaten nutzen, müssen daher schnell reagieren.

- Wenn Sie über eine Anwendung mit Azure-API- oder Azure-Dienstintegration verfügen und nicht sicher sind, ob sie das Anheften von Zertifikaten nutzt, wenden Sie sich den Hersteller der Anwendung.

- Für unterschiedliche Betriebssysteme und Sprach-Runtimes, die mit Azure-Diensten kommunizieren, sind möglicherweise zusätzliche Schritte erforderlich, um eine ordnungsgemäße Zertifikatkette mit den neuen Stammzertifizierungsstellen einzurichten:
    - **Linux:** Bei vielen Distributionen müssen die Zertifizierungsstellen zu „/etc/ssl/certs“ hinzugefügt werden. Spezifische Anweisungen finden Sie in der Dokumentation der Distribution.
    - **Java:** Stellen Sie sicher, dass der Java-Schlüsselspeicher die oben aufgeführten Zertifizierungsstellen enthält.
    - **Windows in nicht verbundenen Umgebungen:** Für Systeme, die in nicht verbundenen Umgebungen ausgeführt werden, müssen die neuen Stammzertifizierungsstellen dem Speicher für vertrauenswürdige Stammzertifizierungsstellen und die Zwischenzertifizierungsstellen dem Speicher für Zwischenzertifizierungsstellen hinzugefügt werden.
    - **Android:** Überprüfen Sie die Dokumentation für Ihr Gerät und für Ihre Android-Version.
    - **Andere Hardwaregeräte (insbesondere IoT):** Wenden Sie sich an den Hersteller des Geräts.

- Wenn Sie über eine Umgebung mit Firewallregeln verfügen, die dazu führen, dass ausgehende Aufrufe nur für bestimmte CRL-Downloadorte (Certificate Revocation List, Zertifikatsperrliste) und/oder OCSP-Überprüfungsorte (Online Certificate Status-Protokoll) zugelassen werden. In diesem Fall müssen die folgenden CRL- und OCSP-URLs zugelassen werden:

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com
    - http://www&#46;microsoft&#46;com/pkiops

## <a name="next-steps"></a>Nächste Schritte

Sollten Sie weitere Fragen haben, wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/).
