---
title: Azure Attestation-Anspruchsätze
description: Die Anspruchsätze von Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e17002534d35f477467f0c35833560a0267dd596
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909776"
---
# <a name="claim-sets"></a>Anspruchsätze

Ansprüche, die während der Nachweisgenerierung für Enclaves mit Microsoft Azure Attestation generiert werden, können in die folgenden Kategorien unterteilt werden:

- **Eingehende Ansprüche** : Ansprüche, die von Microsoft Azure Attestation generiert werden, nachdem der Nachweisbeweis analysiert wurde

- **Ausgehende Ansprüche** : Ansprüche, die von Azure Attestation als Ausgabe erstellt werden. Dies enthält alle Ansprüche, die im Nachweistoken enthalten sein sollen.

- **Eigenschaftsansprüche** : Ansprüche, die von Azure Attestation als Ausgabe erstellt werden. Dies enthält alle Ansprüche, die Eigenschaften des Nachweistokens darstellen, wie z. B. die Codierung des Berichts, die Gültigkeitsdauer des Berichts usw.

Die folgenden Ansprüche werden von der JWT-RFC definiert und können von Azure Attestation im Antwortobjekt verwendet werden:

- **Anspruch „iss“ (Aussteller)** : Der Anspruch „iss“ (Aussteller) identifiziert den Prinzipal, der das JWT ausgestellt hat. Die Verarbeitung dieses Anspruchs ist in der Regel anwendungsspezifisch. Der Wert von „iss“ ist eine Zeichenfolge mit Beachtung der Groß-/Kleinschreibung, die einen StringOrURI-Wert enthält.
- **Anspruch „iat“ (Ausgestellt um)** : Der Anspruch „iat“ (Ausgestellt um) gibt den Zeitpunkt an, zu dem das JWT ausgestellt wurde. Dieser Anspruch kann verwendet werden, um das Alter des JWT zu bestimmen. Der Wert MUSS eine Zahl sein, die einen NumericDate-Wert enthält.
- **Anspruch „exp“ (Ablaufzeit)** : Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Die Verarbeitung des Anspruchs „exp“ erfordert es, dass das aktuelle Datum/die aktuelle Uhrzeit vor dem Wert liegt, der im Anspruch „exp“ festgelegt wurde.

  Hinweis: Dem Zeitpunkt der Ausstellung (iat) werden fünf Minuten Spielraum hinzugefügt, um Abweichungen der Uhr zu berücksichtigen.
- **Anspruch „nbf“ (Nicht vor)** : Der Anspruch „nbf“ (Nicht vor) gibt die Zeit an, vor der das JWT für die Bearbeitung NICHT akzeptiert wird. Die Verarbeitung des Anspruchs „nbf“ erfordert es, dass das aktuelle Datum/die aktuelle Uhrzeit dem Wert im Anspruch „nbf“ entspricht oder danach liegt.
  Hinweis: Dem Zeitpunkt der Ausstellung (iat) werden fünf Minuten Spielraum hinzugefügt, um Abweichungen der Uhr zu berücksichtigen.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Ansprüche, die von Azure Attestation in SGX-Enclaves ausgegeben werden

### <a name="incoming-claims"></a>Eingehende Ansprüche 

- **$is-debuggable** : ein boolescher Wert, der angibt, ob Debugging für die Enclave aktiviert ist oder nicht
- **$sgx-mrsigner** : hexadezimal codierter Wert des Felds „mrsigner“ des Angebots
- **$sgx-mrenclave** : hexadezimal codierter Wert des Felds „mrenclave“ des Angebots
- **$product-id**
- **$svn** : im Angebot codierte Sicherheitsversionsnummer 
- **$Tee** : Typ der Enclave 

### <a name="outgoing-claims"></a>Ausgehende Ansprüche

- **is-debuggable** : ein boolescher Wert, der angibt, ob Debugging für die Enclave aktiviert ist oder nicht
- **sgx-mrsigner** : hexadezimal codierter Wert des Felds „mrsigner“ des Angebots
- **sgx-mrenclave** : hexadezimal codierter Wert des Felds „mrenclave“ des Angebots
- **product-id**
- **svn** : im Angebot codierte Sicherheitsversionsnummer 
- **tee** : Typ der Enclave 
- **maa-ehd** :  Base64Url-codierte Version der in der Nachweisanforderung angegebenen „Enclave Held Data“ 
- **aas-ehd** :  Base64Url-codierte Version der in der Nachweisanforderung angegebenen „Enclave Held Data“ 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Ansprüche, die von Azure Attestation in VBS-Enclaves ausgegeben werden

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Eingehende Ansprüche (können auch als ausgehende Ansprüche verwendet werden)

- **aikValidated** :  Ein boolescher Wert, der Informationen dazu enthält, ob das Zertifikat des Nachweisidentitätsschlüssels (Attestation Identity Key, AIK) überprüft wurde
- **aikPubHash** :  Eine Zeichenfolge mit base64 (SHA256 (öffentlicher AIK-Schlüssel im DER-Format))
- **tpmVersion** :   Ein ganzzahliger Wert, der die Hauptversion des Trusted Platform Module (TPM) enthält
- **secureBootEnabled** : Ein boolescher Wert, der angibt, ob der sichere Start aktiviert ist
- **iommuEnabled** :  Ein boolescher Wert, der angibt, ob die Input-output memory management unit (Iommu) aktiviert ist
- **bootDebuggingDisabled** : Ein boolescher Wert, der angibt, ob das Startdebugging deaktiviert ist
- **notSafeMode** :  Ein boolescher Wert, der angibt, wenn das Fenster nicht im abgesicherten Modus ausgeführt wird
- **notWinPE** :  Ein boolescher Wert, der angibt, wenn das Fenster nicht im WinPE-Modus ausgeführt wird
- **vbsEnabled** :  Ein boolescher Wert, der angibt, ob VBS aktiviert ist
- **vbsReportPresent** :  Ein boolescher Wert, der angibt, ob der VBS-Enclavebericht verfügbar ist
- **enclaveAuthorId** :  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Autor-ID enthält – den Autorenbezeichner des primären Moduls für die Enclave
- **enclaveImageId** :  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Image-ID enthält – den Imagebezeichner des primären Moduls für die Enclave
- **enclaveOwnerId** :  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Besitzer-ID enthält – den Bezeichner des Besitzers des primären Moduls für die Enclave
- **enclaveFamilyId** :  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Familien-ID enthält – der Familienbezeichner des primären Moduls für die Enclave
- **enclaveSvn** :  Ein ganzzahliger Wert, der die Sicherheitsversionsnummer des primären Moduls für die Enclave enthält
- **enclavePlatformSvn** :  Ein ganzzahliger Wert, der die Sicherheitsversionsnummer der Plattform enthält, die die Enclave hostet
- **enclaveFlags** :  Der enclaveFlags-Anspruch ist ein ganzzahliger Wert, der Flags enthält, die die Runtimerichtlinie für die Enclave beschreiben
  
### <a name="outgoing-claims"></a>Ausgehende Ansprüche

- **policy_hash** :  Zeichenfolgenwert, der den SHA256-Hash des Richtlinientexts enthält, der von BASE64URL(SHA256(BASE64URL(UTF8(Policy text)))) berechnet wurde
- **policy_signer** :  Enthält ein JWK mit dem öffentlichen Schlüssel oder der Zertifikatkette, die im Header der signierten Richtlinie vorhanden ist.
- **ver (Version)** :  Zeichenfolgenwert, der eine Version des Berichts enthält. Derzeit 1.0.
- **Anspruch „cnf“ (Bestätigung)** :  Der Anspruch „cnf“ wird verwendet, um den Eigentumsnachweisschlüssel zu identifizieren. Bestätigungsanspruch wie in RFC 7800 definiert, enthält den öffentlichen Teil des nachgewiesenen Enclaveschlüssel, der als JSON Web Key-Objekt (JWK) dargestellt ist (RFC 7517)
- **rp_data (Daten der vertrauenden Seite)** :  Die Daten der vertrauenden Seite, sofern in der Anforderung angegeben, die von der vertrauenden Seite als Nonce verwendet wird, um sicherzustellen, dass der Bericht aktuell ist
- **Anspruch „jti“ (JWT-ID)** : Der Anspruch "jti" (JWT-ID) stellt einen eindeutigen Bezeichner für das JWT bereit. Der Bezeichnerwert wird auf eine Art und Weise zugewiesen, die sicherstellt, dass eine geringe Wahrscheinlichkeit besteht, dass der gleiche Wert versehentlich einem anderen Datenobjekt zugewiesen ist.

### <a name="property-claims"></a>Eigenschaftsansprüche

- **report_validity_in_minutes** : Eine ganzzahliger Anspruch, der angibt, wie lange das Token gültig ist
  - **Default value(time)** : Ein Tag in Minuten
  - **Maximum value(time)** : Ein Jahr in Minuten
- **omit_x5c** : Boolescher Wert, der angibt, ob Azure Attestation das Zertifikat auslassen soll, mit dem die Dienstauthentizität nachgewiesen werden soll. Wenn dieser Wert wahr ist, wird x5t zum Nachweistoken hinzugefügt. Wenn dieser Wert falsch ist (Standard), wird x5t zum Nachweistoken hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Einrichten von Azure Attestation mithilfe von PowerShell](quickstart-powershell.md)
