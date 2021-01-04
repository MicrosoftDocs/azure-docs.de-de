---
title: Azure Active Directory-Zertifizierungsstellen
description: Hier finden Sie eine Auflistung der von Azure verwendeten vertrauenswürdigen Zertifikate.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859130"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Von Azure Active Directory verwendete Zertifizierungsstellen

> [!IMPORTANT]
> Die Informationen auf dieser Seite sind nur für Entitäten relevant, die explizit eine Liste akzeptabler Zertifizierungsstellen (ZS) angeben. Dieser Ansatz, der als Anheften von Zertifikaten bezeichnet wird, sollte vermieden werden, es sei denn, es gibt keine andere Möglichkeit.

Für eine Entität, die versucht, auf die Identitätsdienste von Azure Active Directory (Azure AD) über die TLS/SSL-Protokolle zuzugreifen, werden Zertifikate der unten aufgeführten ZS angezeigt. Wenn die Entität diese ZS als vertrauenswürdig einstuft, kann sie diese Zertifikate verwenden, um die Identität und Legitimität der Identitätsdienste zu überprüfen und sichere Verbindungen herzustellen.

Zertifizierungsstellen können in Stamm-ZS und Zwischen-ZS klassifiziert werden. Stamm-ZS sind in der Regel eine oder mehrere Zwischen-ZS zugeordnet. In diesem Artikel werden die von den Identitätsdiensten in Azure AD verwendeten Stamm-ZS sowie die Zwischen-ZS aufgeführt, die diesen Stamm-ZS zugeordnet sind. Für jede ZS gibt es Uniform Resource Identifiers (URIs), um die dazugehörigen Dateien für den Zugriff auf Zertifizierungsstelleninfos (Authority Information Access, AIA) und mit der Sperrliste (Certificate Revocation List, CDP) herunterzuladen. Wenn erforderlich, wird auch ein URI zum OCSP-Endpunkt (Online Certificate Status Protocol) bereitgestellt.

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>In der öffentlichen Azure-Cloud und in Azure US Government Cloud verwendete ZS

Verschiedene Dienste verwenden möglicherweise unterschiedliche Stamm- oder Zwischen-ZS. Daher sind ggf. alle unten aufgeführten Einträge erforderlich.

### <a name="digicert-global-root-g2"></a>DigiCert Global Root G2


| Stamm-CA| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - |- |-|-|-|-|
| DigiCert Global Root G2| 033af1e6a711a 9a0bb2864b11d09fae5| 1. August 2013 <br>15. Januar 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>Zugeordnete Zwischen-ZS

| Ausstellende Zwischen-ZS| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - | - | - | - | - | 
| Microsoft Azure TLS Issuing CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29. Juli 2020<br>27. Juni 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS Issuing CA 02| 0c6ae97cced59983 8690a00a9ea53214| 29. Juli 2020<br>27. Juni 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS Issuing CA 05| 0d7bede97d8209967a 52631b8bdd18bd| 29. Juli 2020<br>27. Juni 2024| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS Issuing CA 06| 02e79171fb8021e93fe 2d983834c50c0| 29. Juli 2020<br>27. Juni 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| Stamm-CA| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 12. Mai 2000<br>12. Mai 2025| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Zugeordnete Zwischen-ZS

| Ausstellende Zwischen-ZS| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 21. Juli 2020<br>8\. Oktober 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 21. Juli 2020<br>20. Mai 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| Stamm-CA| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac95991c74a| 9\. November 2006<br>9\. November 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Zugeordnete Zwischen-ZS

| Ausstellende Zwischen-ZS| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 8\. März 2013 8. März 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |22. September 2020<br>22. September 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Für die Azure China 21Vianet-Cloud verwendete ZS

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| Stamm-CA| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| 9\. November 2006<br>9\. November 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>Zugeordnete Zwischen-ZS

| Ausstellende Zwischen-ZS| Seriennummer| Ausstellungsdatum Ablaufdatum| SHA-1-Fingerabdruck| URIs |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 4\. März 2020<br>4\. März 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Nächste Schritte
[Verschlüsselungsketten in Microsoft 365](/microsoft-365/compliance/encryption-office-365-certificate-chains)
