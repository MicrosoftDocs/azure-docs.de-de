---
title: Azure Attestation – grundlegende Konzepte
description: Grundlegende Konzepte von Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 8ae5bcf103bbb2d2b952fa647ba591e49002f2ff
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921615"
---
# <a name="basic-concepts"></a>Grundlegende Konzepte

Im Folgenden finden Sie einige grundlegende Konzepte zu Microsoft Azure Attestation.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON Web Token](https://jwt.io/) (JWT) ist eine offene [RFC7519](https://tools.ietf.org/html/rfc7519)-Standardmethode zum sicheren Übertragen von Informationen zwischen Parteien als JavaScript Object Notation-Objekt (JSON). Diese Informationen können überprüft werden und sind vertrauenswürdig, da sie digital signiert sind. JWTs können mithilfe eines Geheimnisses oder eines öffentlichen/privaten Schlüsselpaars signiert werden.

## <a name="json-web-key-jwk"></a>JSON Web Key (JWK)

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) ist eine JSON-Datenstruktur, die einen kryptografischen Schlüssel darstellt. Diese Spezifikation definiert auch eine JSON-Datenstruktur für einen Satz von JWKs.

## <a name="attestation-provider"></a>Nachweisanbieter

Der Nachweisanbieter gehört zum Azure-Ressourcenanbieter mit dem Namen „Microsoft.Attestation“. Der Ressourcenanbieter ist ein Dienstendpunkt, der den Azure Attestation-REST-Vertrag bereitstellt und mithilfe von [Azure Resource Manager](../azure-resource-manager/management/overview.md) bereitgestellt wird. Jeder Nachweisanbieter berücksichtigt eine bestimmte erkennbare Richtlinie. 

Nachweisanbieter werden mit einer Standardrichtlinie für jeden Nachweistyp erstellt. (Beachten Sie, dass die VBS-Enclave über keine Standardrichtlinie verfügt.) Weitere Informationen zur Standardrichtlinie für SGX finden Sie unter [Beispiele einer Nachweisrichtlinie](policy-examples.md).

### <a name="regional-default-provider"></a>Regionaler Standardanbieter

Azure Attestation stellt in jeder Region einen Standardanbieter bereit. Kunden können den Standardanbieter für den Nachweis verwenden oder eigene Anbieter mit benutzerdefinierten Richtlinien erstellen. Die Standardanbieter sind für alle Azure AD-Benutzer zugänglich, und die einem Standardanbieter zugeordnete Richtlinie kann nicht geändert werden.

| Region | Nachweis-URI | 
|--|--|
| UK, Süden | `https://shareduks.uks.attest.azure.net` | 
| USA (Ost 2) | `https://sharedeus2.eus2.attest.azure.net` | 
| USA (Mitte) | `https://sharedcus.cus.attest.azure.net` | 
| East US| `https://sharedeus.eus.attest.azure.net` | 
| Kanada, Mitte | `https://sharedcac.cac.attest.azure.net` | 

## <a name="attestation-request"></a>Nachweisanforderung

Bei der Nachweisanforderung handelt es sich um ein serialisiertes JSON-Objekt, das von der Clientanwendung an den Nachweisanbieter gesendet wird. Das Anforderungsobjekt für eine SGX-Enclave verfügt über zwei Eigenschaften: 
- „Quote“ – der Wert der Eigenschaft „Quote“ ist eine Zeichenfolge, die eine Base64URL-codierte Darstellung des Nachweisangebots enthält.
- „EnclaveHeldData“ – der Wert der Eigenschaft „EnclaveHeldData“ ist eine Zeichenfolge, die eine Base64URL-codierte Darstellung der Enclave Held Data enthält.

Azure Attestation überprüft die angegebene Eigenschaft „Quote“ und stellt anschließend sicher, dass der SHA256-Hash der angegebenen Enclave Held Data in den ersten 32 Bytes des Felds „reportData“ im Angebot ausgedrückt ist. 

## <a name="attestation-policy"></a>Nachweisrichtlinie

Eine Nachweisrichtlinie wird zum Verarbeiten der Nachweisbeweise verwendet und kann von den Kunden konfiguriert werden. Der Kern von Azure Attestation ist ein Richtlinienmodul, das Ansprüche verarbeitet, die den Beweis darstellen. Mithilfe von Richtlinien können Sie feststellen, ob Azure Attestation ein Nachweistoken auf Grundlage von Beweisen ausstellen soll (oder nicht) und somit den Attester unterstützt (oder nicht). Entsprechend führt ein Fehler beim Übergeben aller Richtlinien dazu, dass kein JWT-Token ausgegeben wird.

Falls die Standardrichtlinie im Nachweisanbieter die Anforderungen nicht erfüllt, können Kunden benutzerdefinierte Richtlinien in allen Regionen erstellen, die von Azure Attestation unterstützt werden. Die Richtlinienverwaltung ist ein wichtiges Feature, das Azure Attestation den Kunden bereitstellt. Richtlinien sind nachweistypspezifisch und können zum Identifizieren von Enclaves oder zum Hinzufügen von Ansprüchen zum Ausgabetoken bzw. zum Ändern von Ansprüchen in einem Ausgabetoken verwendet werden. 

Weitere Informationen zum Inhalt von Standardrichtlinien und Beispiele finden Sie unter [Beispiele einer Nachweisrichtlinie](policy-examples.md).

## <a name="benefits-of-policy-signing"></a>Vorteile der Richtliniensignatur

Eine Nachweisrichtlinie bestimmt letztendlich, ob Azure Attestation ein Nachweistoken ausstellt. Die Richtlinie bestimmt auch die Ansprüche, die im Nachweistoken generiert werden sollen. Daher ist es von größter Wichtigkeit, dass die vom Dienst ausgewertete Richtlinie tatsächlich die vom Administrator geschriebene Richtlinie ist und nicht von externen Entitäten manipuliert oder geändert wurde. 

Das Vertrauensmodell definiert das Autorisierungsmodell des Nachweisanbieters, um die Richtlinie zu definieren und zu aktualisieren.  Zwei Modelle werden unterstützt – eines basierend auf der Azure AD-Autorisierung und eines basierend auf dem Besitz von kundenseitig verwalteten kryptografischen Schlüsseln (als isoliertes Modell bezeichnet).  Das isolierte Modell ermöglicht es Azure Attestation, sicherzustellen, dass die vom Kunden übermittelte Richtlinie nicht manipuliert wird.

Im isolierten Modell erstellt der Administrator einen Nachweisanbieter, der einen Satz von X.509-Zertifikaten mit vertrauenswürdiger Signatur in einer Datei angibt. Der Administrator kann anschließend dem Nachweisanbieter eine signierte Richtlinie hinzufügen. Bei der Verarbeitung der Nachweisanforderung überprüft Azure Attestation die Signatur der Richtlinie mithilfe des öffentlichen Schlüssels, der entweder durch den Parameter „jwk“ oder den Parameter „x5c“ im Header dargestellt wird.  Azure Attestation überprüft auch, ob der öffentliche Schlüssel im Anforderungsheader in der Liste der vertrauenswürdigen Signaturzertifikate enthalten ist, die dem Nachweisanbieter zugeordnet sind. Auf diese Weise kann die vertrauende Seite (Azure Attestation) einer Richtlinie vertrauen, die mit den X.509-Zertifikaten signiert ist, die sie kennt. 

Beispiele finden Sie unter [Beispiele für Richtliniensignaturgeber-Zertifikate](policy-signer-examples.md).

## <a name="attestation-token"></a>Nachweistoken

Die Antwort von Azure Attestation ist eine JSON-Zeichenfolge, deren Wert JWT enthält. Azure Attestation verpackt die Ansprüche und generiert ein signiertes JWT. Der Signaturvorgang wird mit einem selbstsignierten Zertifikat ausgeführt, dessen Antragstellername mit dem AttestUri-Element des Nachweisanbieters übereinstimmt.

Die Get OpenID Metadata-API gibt eine OpenID Configuration-Antwort zurück, wie vom [OpenID Connect Discovery-Protokoll](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig) festgelegt. Die API ruft Metadaten zu den Signaturzertifikaten ab, die von Azure Attestation verwendet werden.

Beispiel für ein JWT, das für eine SGX-Enclave generiert wurde:

```
{
  “alg”: “RS256”,
  “jku”: “https://tradewinds.us.attest.azure.net/certs”,
  “kid”: “f1lIjBlb6jUHEUp1/Nh6BNUHc6vwiUyMKKhReZeEpGc=”,
  “typ”: “JWT”
}.{
  “maa-ehd”: <input enclave held data>,
  “exp”: 1568187398,
  “iat”: 1568158598,
  “is-debuggable”: false,
  “iss”: “https://tradewinds.us.attest.azure.net”,
  “nbf”: 1568158598,
  “product-id”: 4639,
  “sgx-mrenclave”: “”,
  “sgx-mrsigner”: “”,
  “svn”: 0,
  “tee”: “sgx”
}.[Signature]
```
Ansprüche wie „exp“, „iat“, „iss“ oder „nbf“ werden von der [JWT-RFC](https://tools.ietf.org/html/rfc7517) definiert, und die verbleibenden werden von Azure Attestation generiert. Weitere Informationen finden Sie unter [von Azure Attestation ausgegebene Ansprüche](claim-sets.md).

## <a name="encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten

Zum Schutz von Kundendaten werden die Daten von Azure Attestation in Azure Storage gespeichert. Azure Storage verschlüsselt ruhende Daten, wenn diese in Rechenzentren geschrieben werden, und entschlüsselt sie für Kunden, damit diese darauf zugreifen können. Diese Verschlüsselung erfolgt mithilfe eines von Microsoft verwalteten Verschlüsselungsschlüssels. 

Zusätzlich zum Schutz von Daten in Azure Storage wird von Azure Attestation auch Azure Disk Encryption (ADE) genutzt, um virtuelle Dienstcomputer zu verschlüsseln. Wenn Azure Attestation in einer Enclave in Azure Confidential Computing-Umgebungen ausgeführt wird, wird die ADE-Erweiterung derzeit nicht unterstützt. In solchen Szenarien ist die Auslagerungsdatei deaktiviert, um zu verhindern, dass Daten im Arbeitsspeicher gespeichert werden. 

Auf den lokalen Festplattenlaufwerken der Azure Attestation-Instanz werden keine Kundendaten dauerhaft gespeichert.


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Einrichten von Azure Attestation mithilfe von PowerShell](quickstart-powershell.md)
