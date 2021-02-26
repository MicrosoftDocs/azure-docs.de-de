---
title: 'Bring Your Own Key-Spezifikation: Azure Key Vault | Microsoft-Dokumentation'
description: In dieser Dokumentation wird die Bring Your Own Key-Spezifikation beschrieben.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581022"
---
# <a name="bring-your-own-key-specification"></a>Bring Your Own Key-Spezifikation

In diesem Dokument werden die Spezifikationen zum Importieren von durch HSM geschützten Schlüsseln aus lokalen HSMs von Kunden in Key Vault beschrieben.

## <a name="scenario"></a>Szenario

Ein Key Vault-Kunde möchte einen Schlüssel sicher aus dem lokalen HSM außerhalb von Azure in das Unterstützungs-HSM von Azure Key Vault übertragen. Der Vorgang zum Importieren eines Schlüssels, der außerhalb von Key Vault generiert wird, wird im Allgemeinen als Bring Your Own Key (BYOK) bezeichnet.

Dies sind die Anforderungen:
* Der zu übertragende Schlüssel ist niemals außerhalb eines HSM in Nur-Text-Form vorhanden.
* Außerhalb eines HSM wird der zu übertragende Schlüssel immer durch einen Schlüssel geschützt, der im Azure Key Vault-HSM gespeichert ist.

## <a name="terminology"></a>Begriff

|Schlüsselname|Schlüsseltyp|Origin|BESCHREIBUNG|
|---|---|---|---|
|Schlüsselaustauschschlüssel (Key Exchange Key, KEK)|RSA|Azure Key Vault-HSM|Dies ist ein durch HSM gestütztes RSA-Schlüsselpaar, das in Azure Key Vault generiert wurde.
Schlüssel zum Packen|AES|Anbieter-HSM|Dies ist ein (kurzlebiger) AES-Schlüssel, der vom lokalen HSM generiert wurde.
Zielschlüssel|RSA, EC, AES (nur verwaltetes HSM)|Anbieter-HSM|Der Schlüssel, der an das Azure Key Vault-HSM übertragen werden soll

**Schlüsselaustauschschlüssel:** Dies ist ein durch HSM gestützter Schlüssel, den der Kunde im Schlüsseltresor generiert, in den der BYOK-Schlüssel importiert wird. Dieser Schlüsselaustauschschlüssel (KEK) muss die folgenden Eigenschaften aufweisen:

* Es muss sich um einen RSA-HSM-Schlüssel handeln (4096, 3072 oder 2048 Bit).
* Die „key_ops“-Eigenschaft ist festgelegt (NUR „import“), sodass der Schlüssel nur während des BYOK-Vorgangs verwendet werden kann.
* Er muss sich im gleichen Tresor befinden, in den der Zielschlüssel importiert werden soll.

## <a name="user-steps"></a>Benutzerschritte

Ein Benutzer muss für die Übertragung eines Schlüssels die folgenden Schritte ausführen:

1. Generieren Sie einen KEK.
2. Rufen Sie den öffentlichen Schlüssel des KEK ab.
3. Verwenden Sie das vom HSM-Anbieter bereitgestellte BYOK-Tool. Importieren Sie den KEK in das Ziel-HSM, und exportieren Sie den durch den KEK geschützten Zielschlüssel.
4. Importieren Sie den geschützten Zielschlüssel in Azure Key Vault.

Kunden verwenden das BYOK-Tool und die vom HSM-Anbieter bereitgestellte Dokumentation, um Schritt 3 abzuschließen. Ein Schlüsselübertragungsblob (BYOK-Datei) wird erstellt.


## <a name="hsm-constraints"></a>HSM-Einschränkungen

Ein vorhandenes HSM kann Einschränkungen für verwaltete Schlüssel anwenden. Dazu gehört Folgendes:
* Das HSM muss möglicherweise so konfiguriert werden, dass es den Export zulässt, bei dem Schlüssel zum Packen verwendet werden.
* Der Zielschlüssel muss möglicherweise als „CKA_EXTRACTABLE“ gekennzeichnet werden, damit das HSM den kontrollierten Export zulässt.
* In einigen Fällen müssen der KEK und der Schlüssel zum Packen möglicherweise als „CKA_TRUSTED“ gekennzeichnet werden. Dies ermöglicht die Verwendung des Zielschlüssels zum Packen von Schlüsseln im HSM.

Die Konfiguration des Quell-HSM überschreitet im Allgemeinen den Umfang dieser Spezifikation. Microsoft erwartet, dass der HSM-Anbieter eine Dokumentation mit dem BYOK-Tool erstellt, die derartige Konfigurationsschritte beinhaltet.

> [!NOTE]
> Die unten beschriebenen Schritte 1, 2 und 4 können mit anderen Schnittstellen wie Azure PowerShell oder dem Azure-Portal ausgeführt werden. Sie können auch programmgesteuert mithilfe gleichwertiger Funktionen im Key Vault SDK ausgeführt werden.

### <a name="step-1-generate-kek"></a>Schritt 1: Generieren des KEK

Verwenden Sie den Befehl **az keyvault key create**, um den KEK mit auf „import“ festgelegten Schlüsselvorgängen zu erstellen. Notieren Sie sich den kid-Wert des Schlüsselbezeichners, der vom folgenden Befehl zurückgegeben wird.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Schritt 2: Abrufen des öffentlichen Schlüssels des KEK

Laden Sie den Teil des öffentlichen Schlüssels des KEK herunter, und speichern Sie ihn in einer PEM-Datei.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Schritt 3: Generieren von Schlüsselübertragungsblobs mithilfe des vom HSM-Anbieter bereitgestellten BYOK-Tools

Der Kunde verwendet das vom HSM-Anbieter bereitgestellte BYOK-Tool zum Erstellen von Schlüsselübertragungsblobs (wird als BYOK-Datei gespeichert). Der öffentliche KEK-Schlüssel (PEM-Datei) ist eine der Eingaben für dieses Tool.

#### <a name="key-transfer-blob"></a>Schlüsselübertragungsblob
Langfristig möchte Microsoft den „CKM_RSA_AES_KEY_WRAP“-Mechanismus (PKCS#11) für die Übertragung des Zielschlüssels in Azure Key Vault verwenden, da mit diesem Mechanismus ein einzelnes Blob erstellt und, was noch wichtiger ist, der zwischenzeitliche AES-Schlüssel von den beiden HSMs verarbeitet wird, sodass dieser kurzlebig ist. Dieser Mechanismus ist derzeit in einigen HSMs nicht verfügbar, aber die Kombination aus dem Schutz des Zielschlüssels mit „CKM_AES_KEY_WRAP_PAD“ mithilfe eines AES-Schlüssels und des Schutzes des AES-Schlüssels mit „CKM_RSA_PKCS_OAEP“ erzeugt ein entsprechendes Blob.

Der Klartext des Zielschlüssels hängt vom Schlüsseltyp ab: 
* RSA-Schlüssel: DER-Codierung [RFC3447] des öffentlichen ASN.1-Schlüssels wird von PKCS#8 [RFC5208] umschlossen 
* EC-Schlüssel: DER-Codierung [RFC5915] des privaten ASN.1-Schlüssels wird von PKCS#8 [RFC5208] umschlossen
* Oktettschlüssel: Rawbytes des Schlüssels

Die Bytes des Klartextschlüssels werden dann mithilfe des „CKM_RSA_AES_KEY_WRAP“-Mechanismus umgewandelt:
* Ein kurzlebiger AES-Schlüssel wird mit dem RSA-Schlüssel zum Packen mithilfe von RSA-OAEP mit SHA-1 generiert und verschlüsselt.
* Der codierte Klartextschlüssel wird mit dem AES-Schlüssel im Rahmen der AES-Schlüsselverpackung mit Auffüllung verschlüsselt.
* Der verschlüsselte AES-Schlüssel und der verschlüsselte Klartextschlüssel werden verkettet, um das finale Verschlüsselungstextblob zu erstellen.

Das Format des Übertragungsblobs verwendet die kompakte „JSON Web Encryption“-Serialisierung (RFC7516) in erster Linie für die Übermittlung der erforderlichen Metadaten an den Dienst für die korrekte Entschlüsselung.

Wenn „CKM_RSA_AES_KEY_WRAP_PAD“ verwendet wird, lautet die JSON-Serialisierung des Übertragungsblobs wie folgt:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* kid: Dies ist der Schlüsselbezeichner des KEK. Für Key Vault-Schlüssel sieht dieser folgendermaßen aus: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621.
* alg: Dies steht für Algorithmus. 
* dir: Dies steht für den direkten Modus, d. h. der kid-Wert, auf den verwiesen wird, wird zum Schützen des Verschlüsselungstexts verwendet. Dies ist eine genaue Darstellung von „CKM_RSA_AES_KEY_WRAP“.
* generator: Dies ist ein Informationsfeld, das den Namen und die Version des BYOK-Tools und den Hersteller und das Modell des Quell-HSM angibt. Diese Informationen sind für die Problembehandlungen und Supportvorgänge vorgesehen.

Das JSON-Blob wird in einer Datei mit der Erweiterung „.byok“ gespeichert, sodass Azure PowerShell- und CLI-Clients diese ordnungsgemäß behandeln, wenn die Befehle „Add-AzKeyVaultKey“ (PSH) oder „az keyvault key import“ (CLI) verwendet werden.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Schritt 4: Hochladen des Schlüsselübertragungsblobs zum Importieren des HSM-Schlüssels

Der Kunde überträgt das Schlüsselübertragungsblob (BYOK-Datei) in eine Onlinearbeitsstation und führt dann den Befehl **az keyvault key import** aus, um dieses Blob als neuen durch HSM gestützten Schlüssel in Key Vault zu importieren. 

Verwenden Sie den folgenden Befehl, um einen RSA-Schlüssel zu importieren:
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
Zum Importieren eines EC-Schlüssels müssen Sie den Schlüsseltyp und den Kurvennamen angeben.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


Wenn der obige Befehl ausgeführt wird, wird wie folgt eine REST-API-Anforderung ausgeführt:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Anforderungstext beim Importieren eines RSA-Schlüssels:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Anforderungstext beim Importieren eines EC-Schlüssels:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Der Wert „key_hsm“ ist der gesamte Inhalt der „KeyTransferPackage-ContosoFirstHSMkey.byok“-Datei, die im Base64-Format codiert ist.

## <a name="references"></a>References
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)

## <a name="next-steps"></a>Nächste Schritte
* Ausführliche BYOK-Anweisungen: [Importieren von durch HSM geschützten Schlüsseln in Key Vault (BYOK)](hsm-protected-keys-byok.md)

