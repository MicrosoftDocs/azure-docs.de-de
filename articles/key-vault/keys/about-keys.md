---
title: 'Informationen zu Schlüsseln: Azure Key Vault'
description: Hier finden Sie eine Übersicht über die Azure Key Vault-REST-Schnittstelle sowie Informationen für Entwickler zu Schlüsseln.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 2ae7b28d5e9e7a520ee8cbd090b6681d5ad7015a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422755"
---
# <a name="about-keys"></a>Informationen zu Schlüsseln

Azure Key Vault bietet zwei Arten von Ressourcen für die Speicherung und Verwaltung kryptografischer Schlüssel:

|Ressourcentyp|Schlüsselschutzmethoden|Basis-URL des Datenebenenendpunkts|
|--|--|--|
| **Tresore** | Softwaregeschützt<br/><br/>und<br/><br/>Durch HSM geschützt (mit Premium-SKU)</li></ul> | https://{Tresorname}.vault.azure.net |
| **Pools verwalteter HSMs** | Durch HSM geschützt | https://{HSM-Name}.managedhsm.azure.net |
||||

- **Tresore:** Tresore bieten eine kostengünstige, leicht bereitzustellende, mehrinstanzenfähige, zonenresiliente (sofern verfügbar) und hochverfügbare Schlüsselverwaltungslösung, die für die meisten gängigen Cloudanwendungsszenarien geeignet ist.
- **Verwaltete HSMs:** Verwaltetes HSM bietet einzelinstanzfähige, zonenresiliente (sofern verfügbar) und hochverfügbare HSMs zum Speichern und Verwalten Ihrer kryptografischen Schlüssel. Diese Lösung eignet sich am besten für Anwendungen und Verwendungsszenarien, in denen Schlüsseln mit hohem Wert verwendet werden. Darüber hinaus ermöglicht sie die Erfüllung besonders strenger Sicherheits- und Complianceanforderungen sowie entsprechender gesetzlicher Anforderungen. 

> [!NOTE]
> Mit Tresoren können neben kryptografischen Schlüsseln auch verschiedene Arten von Objekten wie etwa Geheimnisse, Zertifikate und Speicherkontoschlüssel gespeichert und verwaltet werden.

Kryptografische Schlüssel in Key Vault werden als JSON Web Key-Objekte (JWK) dargestellt. Die Spezifikationen von JavaScript Object Notation (JSON) und JavaScript Object Signing and Encryption (JOSE) lauten wie folgt:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Die grundlegenden JWK/JWA-Spezifikationen wurden erweitert, um Schlüsseltypen zu ermöglichen, die speziell für die Implementierung von Azure Key Vault und verwalteten HSMs verwendet werden. 

Durch HSM geschützte Schlüssel (auch HSM-Schlüssel genannt) werden in einem HSM (Hardwaresicherheitsmodul) verarbeitet und verlassen nie die HSM-Schutzgrenze. 

- Von Tresoren werden **FIPS 140-2 Level 2**-zertifizierte HSMs verwendet, um HSM-Schlüssel in einer gemeinsam genutzten HSM-Back-End-Infrastruktur zu schützen. 
- Von Pools verwalteter HSMs werden **FIPS 140-2 Level 3**-zertifizierte HSMs verwendet, um Ihre Schlüssel zu schützen. Jeder HSM-Pool ist eine isolierte Einzelmandanteninstanz mit eigener [Sicherheitsdomäne](../managed-hsm/security-domain.md) und vollständiger kryptografischer Isolation von allen anderen HSM-Pools, die die gleiche Hardwareinfrastruktur nutzen.

Diese Schlüssel werden in HSM-Pools mit einem einzelnen Mandanten geschützt. Sie können einen RSA-Schlüssel, einen EC-Schlüssel und einen symmetrischen Schlüssel importieren – in Soft-Form oder durch Exportieren von einem kompatiblen HSM-Gerät. Außerdem können Sie Schlüssel in HSM-Pools generieren. Wenn Sie HSM-Schlüssel unter Verwendung der in der [BYOK-Spezifikation (Bring Your Own Key)](../keys/byok-specification.md) beschriebenen Methode importieren, ermöglicht dies den sicheren Transport von Schlüsselmaterial in Pools verwalteter HSMs. 

Weitere Informationen zu geografischen Grenzen finden Sie unter [Datenschutz](https://azure.microsoft.com/support/trust-center/privacy/).

## <a name="key-types-and-protection-methods"></a>Schlüsseltypen und Schutzmethoden

Von Key Vault werden RSA-Schlüssel, EC-Schlüssel und symmetrische Schlüssel unterstützt. 

### <a name="hsm-protected-keys"></a>HSM-geschützte Schlüssel

|Schlüsseltyp|Tresore (nur Premium-SKU)|Pools verwalteter HSMs|
|--|--|--|--|
**EC-HSM**: Elliptic Curve-Schlüssel|HSM mit FIPS 140-2 Level 2|HSM mit FIPS 140-2 Level 3
**RSA-HSM**: RSA-Schlüssel|HSM mit FIPS 140-2 Level 2|HSM mit FIPS 140-2 Level 3
**oct-HSM**: Symmetrisch|Nicht unterstützt|HSM mit FIPS 140-2 Level 3
||||

### <a name="software-protected-keys"></a>Softwaregeschützte Schlüssel

|Schlüsseltyp|Tresore|Pools verwalteter HSMs|
|--|--|--|--|
**RSA**: Softwaregeschützter RSA-Schlüssel|FIPS 140-2 Level 1|Nicht unterstützt
**EC**: Softwaregeschützter Elliptic Curve-Schlüssel.|FIPS 140-2 Level 1|Nicht unterstützt
||||

Ausführliche Informationen zu den einzelnen Schlüsseltypen, Algorithmen, Vorgängen, Attributen und Tags finden Sie unter [Schlüsseltypen, Algorithmen und Vorgänge](about-keys-details.md).

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zu Key Vault](../general/overview.md)
- [Informationen zu verwaltetem HSM](../managed-hsm/overview.md)
- [Informationen zu Geheimnissen](../secrets/about-secrets.md)
- [Informationen zu Zertifikaten](../certificates/about-certificates.md)
- [Übersicht über die Key Vault-REST-API](../general/about-keys-secrets-certificates.md)
- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)