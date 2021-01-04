---
title: Übersicht über Schlüssel, Geheimnisse und Zertifikate in Azure Key Vault
description: Hier finden Sie eine Übersicht über die Azure Key Vault-REST-Schnittstelle sowie Informationen für Entwickler zu Schlüsseln, Geheimnissen und Zertifikaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: e0a45bde32fed651c4b38d203b3c75a6d928e7c5
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327082"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Übersicht über Schlüssel, Geheimnisse und Zertifikate in Azure Key Vault

Microsoft Azure-Anwendungen und -Benutzer können verschiedene Arten von Geheimnissen und Schlüsseldaten in Azure Key Vault speichern. Der Key Vault-Ressourcenanbieter unterstützt zwei Ressourcentypen: Tresore und verwaltete HSMs.

## <a name="dns-suffixes-for-base-url"></a>DNS-Suffixe für Basis-URL
 Die folgende Tabelle zeigt das DNS-Suffix der Basis-URL, das vom Datenebenenendpunkt für Tresore und Pools verwalteter HSMs in verschiedenen Cloudumgebungen verwendet wird.

Cloudumgebung | DNS-Suffix für Tresore | DNS-Suffix für verwaltete HSMs
---|---|---
Azure Cloud | .vault.azure.net | .managedhsm.azure.net
Azure-Cloud China | .vault.azure.cn | Nicht unterstützt
Azure US Government | .vault.usgovcloudapi.net | Nicht unterstützt
Azure German Cloud | .vault.microsoftazure.de | Nicht unterstützt
|||


## <a name="object-types"></a>Objekttypen
 In der folgenden Tabelle sind die Objekttypen und ihre Suffixe in der Basis-URL aufgeführt.

Objekttyp|URL-Suffix|Tresore|Pools verwalteter HSMs
--|--|--|--
**Kryptografische Schlüssel**||
HSM-geschützte Schlüssel|/keys|Unterstützt|Unterstützt
Softwaregeschützte Schlüssel|/keys|Unterstützt|Nicht unterstützt
**Andere Objekttypen**||
Geheimnisse|/secrets|Unterstützt|Nicht unterstützt
Zertifikate|/certificates|Unterstützt|Nicht unterstützt
Speicherkontoschlüssel|/storageaccount|Unterstützt|Nicht unterstützt
|||
- **Kryptografische Schlüssel:** Unterstützt mehrere Schlüsseltypen und Algorithmen und ermöglicht die Verwendung von softwaregeschützten und durch HSM geschützten Schlüsseln. Weitere Informationen finden Sie unter [Informationen zu Azure Key Vault-Schlüsseln](../keys/about-keys.md).
- **Geheimnisse:** Bieten einen sicheren Speicher für Geheimnisse wie Kennwörter und Datenbank-Verbindungszeichenfolgen. Weitere Informationen finden Sie unter [Informationen zu Azure Key Vault-Geheimnissen](../secrets/about-secrets.md).
- **Zertifikate**: Unterstützen Zertifikate, die auf Schlüsseln und Geheimnissen aufbauen, und fügt ein Feature für die automatisierte Verlängerung hinzu. Weitere Informationen finden Sie unter [Informationen zu Azure Key Vault-Zertifikaten](../certificates/about-certificates.md).
- **Azure-Speicherkontenschlüssel:** Kann die Schlüssel eines Azure Storage-Kontos für Sie verwalten. Intern kann Key Vault Schlüssel für ein Azure Storage-Konto auflisten (synchronisieren) und die Schlüssel in regelmäßigen Abständen erneut generieren (rotieren). Weitere Informationen finden Sie unter [Verwalten von Speicherkontoschlüsseln mit Key Vault und der Azure-Befehlszeilenschnittstelle](../secrets/overview-storage-keys.md).

Weitere allgemeine Informationen zu Key Vault finden Sie unter [Informationen zu Azure Key Vault](overview.md). Weitere Informationen zu Pools verwalteter HSMs finden Sie unter [Was ist verwaltetes HSM von Azure Key Vault?](../managed-hsm/overview.md).


## <a name="data-types"></a>Datentypen

In den JOSE-Spezifikationen finden Sie relevante Datentypen für Schlüssel, Verschlüsselung und Signatur.  

-   **algorithm**: ein unterstützter Algorithmus für einen Schlüsselvorgang, z.B. RSA1_5.  
-   **ciphertext-value**: Verschlüsselungstextoktette, codiert mit Base64URL.  
-   **digest-value**: die Ausgabe eines Hashalgorithmus, codiert mit Base64URL.  
-   **key-type**: einer der unterstützten Schlüsseltypen, z.B. RSA (Rivest-Shamir-Adleman).  
-   **plaintext-value**: Klartextoktette, codiert mit Base64URL.  
-   **signature-value**: die Ausgabe eines Signaturalgorithmus, codiert mit Base64URL.  
-   **base64URL**: ein mit Base64URL [RFC4648] codierter Binärwert.  
-   **boolean**: entweder TRUE oder FALSE.  
-   **Identity**: eine Identität in Azure Active Directory (AAD).  
-   **IntDate**: ein dezimaler JSON-Wert, der die Anzahl von Sekunden von 1970-01-01T0:0:0Z UTC bis zum angegebenen UTC-Datum / zur angegebenen UTC-Uhrzeit darstellt. Details in Bezug auf Datum/Uhrzeit im Allgemeinen und UTC im Besonderen finden Sie der Dokumentation zu RFC 3339.  

## <a name="objects-identifiers-and-versioning"></a>Objekte, Bezeichner und Versionsverwaltung

In Key Vault gespeicherte Objekte werden versioniert, wenn eine neue Instanz eines Objekts erstellt wird. Jeder Version wird ein eindeutiger Bezeichner und eine URL zugewiesen. Wenn ein Objekt zum ersten Mal erstellt wird, erhält es einen eindeutigen Versionsbezeichner und wird als aktuelle Version des Objekts gekennzeichnet. Beim Erstellen einer neuen Instanz mit dem gleichen Objektnamen erhält das neue Objekt einen eindeutigen Versionsbezeichner und wird zur aktuellen Version.  

Für die Adressierung von Objekten in Key Vault können Sie eine Version angeben oder bei Vorgängen für die aktuelle Version des Objekts die Version weglassen. Bei einem Schlüssel mit dem Namen `MasterKey` führt das Durchführen von Vorgängen ohne Angabe einer Version beispielsweise dazu, dass die neueste verfügbare Version verwendet wird. Das Ausführen von Vorgängen mit dem versionsspezifischen Bezeichner veranlasst das System, die spezifische Version des Objekts zu verwenden.  

### <a name="vault-name-and-object-name"></a>Tresorname und Objektname
Objekte werden in Key Vault über eine URL eindeutig identifiziert. Unabhängig vom geografischen Standort dürfen keine zwei Objekte im System die gleiche URL aufweisen. Die vollständige URL zu einem Objekt wird Objektbezeichner genannt. Die URL besteht aus einem Präfix, das den Schlüsseltresor, den Objekttyp, den vom Benutzer bereitgestellten Objektnamen und eine Objektversion identifiziert. Beim Objektnamen wird Groß-/Kleinschreibung nicht unterschieden, und er ist unveränderlich. Bezeichner, die nicht die Objektversion enthalten, heißen Basisbezeichner.  

Weitere Informationen finden Sie unter [Authentifizierung, Anforderungen und Antworten](authentication-requests-and-responses.md).

Ein Objektbezeichner hat das folgende allgemeine Format (abhängig vom Containertyp):  

- **Für Schlüsseltresore:** `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **Für Pools verwalteter HSMs:** `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Weitere Informationen zu den von den einzelnen Containertypen unterstützten Objekttypen finden Sie unter [Objekttypunterstützung](#object-types).

Hierbei gilt:  

| Element | BESCHREIBUNG |  
|-|-|  
|`vault-name` oder `hsm-name`|Der Name eines Schlüsseltresors oder Pools verwalteter HSMs im Microsoft Azure Key Vault-Dienst.<br /><br />Schlüsseltresornamen und Namen von Pools verwalteter HSMs werden vom Benutzer ausgewählt und sind global eindeutig.<br /><br />Der Name des Schlüsseltresors und des Pools verwalteter HSMs muss zwischen 3 und 24 Zeichen lang sein und darf nur die Ziffern 0-9, die Buchstaben a-z und A-Z sowie das Minuszeichen („-“) enthalten.|  
|`object-type`|Die Art des Objekts (Schlüssel, Geheimnisse oder Zertifikate)|  
|`object-name`|Ein `object-name` ist ein vom Benutzer bereitgestellter Name und muss innerhalb eines Schlüsseltresors eindeutig sein. Der Name muss eine Zeichenfolge mit 1 bis 127 Zeichen sein und mit einem Buchstaben beginnen und darf nur die Zeichen „0 - 9“, „a - z“, „A - Z“ und „-“ enthalten.|  
|`object-version`|Ein `object-version` ist ein vom System generierter, 32 Zeichen langer Zeichenfolgenbezeichner, der optional verwendet wird, um eine eindeutige Version eines Objekts zu adressieren.|  

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu Schlüsseln](../keys/about-keys.md)
- [Informationen zu Geheimnissen](../secrets/about-secrets.md)
- [Informationen zu Zertifikaten](../certificates/about-certificates.md)
- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
