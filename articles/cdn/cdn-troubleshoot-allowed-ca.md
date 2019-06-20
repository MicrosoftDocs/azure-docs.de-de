---
title: Zulässige Zertifizierungsstellen für die Aktivierung von benutzerdefiniertem HTTPS für Azure CDN | Microsoft-Dokumentation
description: Wenn Sie Ihr eigenes Zertifikat zur Aktivierung von HTTPS in einer benutzerdefinierten Domäne verwenden, müssen Sie für die Erstellung eine zulässige Zertifizierungsstelle (CA) verwenden.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 11651c2721756a4f750a5a5e78f86fdbd363fb9d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60323517"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Zulässige Zertifizierungsstellen für die Aktivierung von benutzerdefiniertem HTTPS für Azure CDN

Wenn Sie für eine benutzerdefinierte Azure CDN-Domäne (Azure Content Delivery Network) auf einem Endpunkt mit **Azure CDN Standard von Microsoft** die [HTTPS-Funktion durch Verwendung Ihres eigenen Zertifikats aktivieren](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), müssen Sie für die Erstellung Ihres SSL-Zertifikats eine zulässige Zertifizierungsstelle (CA) verwenden. Bei der Verwendung einer unzulässigen Zertifizierungsstelle oder eines selbstsignierten Zertifikats wird Ihre Anforderung abgelehnt.

> [!NOTE]
> Die Option zum Verwenden Ihres eigenen Zertifikats zur Aktivierung von benutzerdefiniertem HTTPS ist nur für Profile mit **Azure CDN Standard von Microsoft** verfügbar. 
>

Beim Erstellen Ihres eigenen Zertifikats sind die folgenden Zertifizierungsstellen zulässig:

- AddTrust External CA Root
- AlphaSSL Root CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 Extended Validation Server CA
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- DST Root CA X3
- D-trust Root Class 3 CA 2 2009
- Encryption Everywhere DV TLS CA
- Entrust Root Certification Authority
- Entrust Root Certification Authority - G2
- Entrust.net Certification Authority (2048)
- GeoTrust Global CA
- GeoTrust Primary Certification Authority
- GeoTrust Primary Certification Authority – G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA - SHA256 - G2
- GlobalSign Organization Validation CA - G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority – G2
- Go Daddy Secure Certificate Authority - G2
- QuoVadis Root CA2 G3
- RapidSSL RSA CA 2018
- Symantec Class 3 EV SSL CA - G3
- Symantec Class 3 Secure Server CA - G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Primary Root CA
- Thawte Primary Root CA – G2
- Thawte Primary Root CA – G3
- Thawte RSA CA 2018
- Thawte Timestamping CA
- TrustAsia TLS RSA CA
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority - G5
- VeriSign International Server CA - Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority


