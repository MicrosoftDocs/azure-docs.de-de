---
title: Generieren und Übertragen von HSM-geschützten Schlüsseln – BYOK – für Azure Key Vault
description: Verwenden Sie diesen Artikel zum Planen, Generieren und Übertragen Ihrer eigenen durch HSM geschützten Schlüssel für die Nutzung mit Azure Key Vault. Dieser Ansatz wird auch als Bring Your Own Key (BYOK) bezeichnet.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 51ba981dcc6f36df3bfaacebb503782faed5c91f
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581005"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importieren von durch HSM geschützten Schlüsseln in Key Vault (BYOK)

Zur Steigerung der Sicherheit können Sie bei Verwendung von Azure Key Vault Schlüssel in ein HSM (Hardwaresicherheitsmodul) importieren oder darin generieren. Dieser Schlüssel verbleibt immer innerhalb der HSM-Grenzen. Dieses Szenario wird häufig als *Bring Your Own Key* (BYOK) bezeichnet. Key Vault verwendet die nCipher nShield-HSM-Produktfamilie (validiert für FIPS 140-2 Level 2) zum Schützen der Schlüssel.

Verwenden Sie die Informationen in diesem Artikel zum Planen, Generieren und Übertragen Ihrer eigenen durch HSM geschützten Schlüssel für die Nutzung mit Azure Key Vault.

> [!NOTE]
> Diese Funktion steht für Azure China 21ViaNet nicht zur Verfügung. 
> 
> Diese Importmethode ist nur für [unterstützte HSMs](#supported-hsms) verfügbar. 

Weitere Informationen und ein Tutorial mit den ersten Schritten mit Key Vault (einschließlich der Erstellung eines Schlüsseltresors für durch HSM geschützte Schlüssel) finden Sie unter [Was ist Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Übersicht

Im Folgenden finden Sie eine Übersicht über den Prozess. Einige der Schritte, die Sie ausführen müssen, werden weiter unten in diesem Artikel beschrieben.

* Generieren Sie in Key Vault einen Schlüssel (als Schlüsselaustauschschlüssel (*Key Exchange Key*, KEK) bezeichnet). Der KEK muss ein RSA-HSM-Schlüssel sein, der nur den Schlüsselvorgang `import` umfasst. Nur die Premium-SKU von Key Vault unterstützt RSA-HSM-Schlüssel.
* Laden Sie den öffentlichen Schlüssel des KEK als PEM-Datei herunter.
* Übertragen Sie den öffentlichen Schlüssel des KEK auf einen Offlinecomputer, der mit einem lokalen HSM verbunden ist.
* Verwenden Sie auf dem Offlinecomputer das von Ihrem HSM-Anbieter bereitgestellte BYOK-Tool, um eine BYOK-Datei zu erstellen. 
* Der Zielschlüssel wird mit einem KEK verschlüsselt. Die Verschlüsselung bleibt bis zur Übertragung an die HSMs in Key Vault bestehen. Nur die verschlüsselte Version Ihres Schlüssels verlässt das lokale HSM.
* Ein KEK, der innerhalb eines Key Vault-HSM generiert wurde, kann nicht exportiert werden. HSMs erzwingen die Regel, dass keine unverschlüsselte Version eines KEK außerhalb eines Key Vault-HSM vorhanden sein darf.
* Der KEK muss sich in derselben Key Vault-Instanz befinden, in die der Zielschlüssel importiert werden soll.
* Wenn die BYOK-Datei in Key Vault hochgeladen wird, verwendet das Key Vault-HSM den privaten Schlüssel des KEK, um das Zielschlüsselmaterial zu entschlüsseln und als HSM-Schlüssel zu importieren. Dieser Vorgang erfolgt vollständig innerhalb eines Key Vault-HSM. Der Zielschlüssel verbleibt stets in den Schutzgrenzen des HSM.

## <a name="prerequisites"></a>Voraussetzungen

In der folgenden Tabelle sind die Voraussetzungen für die Verwendung von BYOK in Azure Key Vault aufgeführt:

| Anforderung | Weitere Informationen |
| --- | --- |
| Ein Azure-Abonnement |Um in Azure Key Vault einen Schlüsseltresor erstellen zu können, benötigen Sie ein Azure-Abonnement. [Registrieren Sie sich für eine kostenlose Testversion.](https://azure.microsoft.com/pricing/free-trial/) |
| Eine Key Vault-Instanz mit Premium-SKU zum Importieren von durch HSM geschützten Schlüsseln |Weitere Informationen zu den Dienstebenen und Funktionen in Azure Key Vault finden Sie unter [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/). |
| Ein HSM aus der Liste der unterstützten HSMs sowie ein BYOK-Tool mit den zugehörigen Anweisungen Ihres HSM-Anbieters | Sie müssen über die Berechtigungen für ein HSM und grundlegende Kenntnisse in der Verwendung Ihres HSM verfügen. Weitere Informationen finden Sie unter [Unterstützte HSMs](#supported-hsms). |
| Azure-Befehlszeilenschnittstelle Version 2.1.0 oder höher | Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Unterstützte HSMs

|Herstellername|Herstellertyp|Unterstützte HSM-Modelle|Weitere Informationen|
|---|---|---|---|
|nCipher|Hersteller,<br/>HSM als Dienst|<ul><li>HSM-Produktfamilie „nShield“</li><li>nShield als Dienst</ul>|[nCipher – Neues BYOK-Tool und -Dokumentation](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Hersteller|<ul><li>Produktfamilie „Luna HSM 7“ mit Firmwareversion 7.3 oder neuer</li></ul>| [Luna – BYOK-Tool und -Dokumentation](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Hersteller,<br/>HSM als Dienst|<ul><li>Self-Defending Key Management Service (SDKMS, selbstverteidigender Schlüsselverwaltungsdienst)</li><li>Equinix SmartKey</li></ul>|[Exportieren von SDKMS-Schlüsseln nach Cloudanbietern für BYOK – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Hersteller|Alle Liquid Security-HSMs mit<ul><li>Firmwareversion 2.0.4 oder höher</li><li>Firmwareversion 3.2 oder höher</li></ul>|[Marvell – BYOK-Tool und -Dokumentation](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Mehrere HSM-Marken und -Modelle, einschließlich<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Weitere Informationen finden Sie auf der [Cryptomathic-Website](https://www.cryptomathic.com/azurebyok).|[Cryptomathic – BYOK-Tool und -Dokumentation](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Hersteller, HSM-as-a-Service|Primus-HSM-Familie, Securosys Clouds HSM|[Primus – BYOK-Tool und -Dokumentation](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Mehrere HSM-Marken und -Modelle, einschließlich<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Weitere Informationen finden Sie auf der [StorMagic-Website](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm).|[SvKMS und Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Hersteller|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
||||


## <a name="supported-key-types"></a>Unterstützte Schlüsseltypen

|Schlüsselname|Schlüsseltyp|Schlüsselgröße/Kurve|Origin|BESCHREIBUNG|
|---|---|---|---|---|
|Schlüsselaustauschschlüssel (Key Exchange Key, KEK)|RSA| 2\.048 Bit<br />3\.072 Bit<br />4\.096 Bit|Azure Key Vault-HSM|Ein durch HSM gestütztes RSA-Schlüsselpaar, das in Azure Key Vault generiert wurde|
|Zielschlüssel|
||RSA|2\.048 Bit<br />3\.072 Bit<br />4\.096 Bit|Anbieter-HSM|Der Schlüssel, der an das Azure Key Vault-HSM übertragen werden soll|
||EC|P-256<br />P-384<br />P-521|Anbieter-HSM|Der Schlüssel, der an das Azure Key Vault-HSM übertragen werden soll|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generieren und Übertragen des Schlüssels an das Key Vault-HSM

So generieren Sie Ihren Schlüssel und übertragen ihn an das Key Vault-HSM

* [Schritt 1: Generieren eines KEK](#step-1-generate-a-kek)
* [Schritt 2: Herunterladen des öffentlichen Schlüssels des KEK](#step-2-download-the-kek-public-key)
* [Schritt 3: Generieren des Schlüssels und Vorbereiten für die Übertragung](#step-3-generate-and-prepare-your-key-for-transfer)
* [Schritt 4: Übertragen des Schlüssels an Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Schritt 1: Generieren eines KEK

Ein KEK ist ein RSA-Schlüssel, der in einem Key Vault-HSM generiert wird. Der KEK wird zum Verschlüsseln des Schlüssels verwendet, den Sie importieren möchten (*Zielschlüssel*).

Für den KEK gilt Folgendes:
- Es muss sich um einen RSA-HSM-Schlüssel handeln (2.048 Bit, 3.072 Bit oder 4.096 Bit).
- Er muss im selben Schlüsseltresor generiert werden, in den Sie den Zielschlüssel importieren möchten.
- Die zulässigen Schlüsselvorgänge müssen auf `import` festgelegt sein.

> [!NOTE]
> Der KEK muss als einzigen zulässigen Schlüsselvorgang „import“ aufweisen. „import“ schließt sich mit allen anderen wichtigen Vorgängen gegenseitig aus.

Verwenden Sie den Befehl [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create), um den KEK mit auf `import` festgelegten Schlüsselvorgängen zu erstellen. Notieren Sie die Schlüssel-ID (`kid`), die vom folgenden Befehl zurückgegeben wird. (Sie verwenden den `kid`-Wert in [Schritt 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Schritt 2: Herunterladen des öffentlichen Schlüssels des KEK

Verwenden Sie den Befehl [az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download), um den öffentlichen Schlüssel des KEK in eine PEM-Datei herunterzuladen. Der importierte Zielschlüssel ist mit dem öffentlichen Schlüssel des KEK verschlüsselt.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Übertragen Sie die Datei „KEKforBYOK.publickey.pem“ an Ihren Offlinecomputer. Sie benötigen diese Datei im nächsten Schritt.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Schritt 3: Generieren des Schlüssels und Vorbereiten für die Übertragung

Informieren Sie sich in der Dokumentation Ihres HSM-Anbieters darüber, wie Sie das BYOK-Tool herunterladen und installieren. Befolgen Sie die Anweisungen des HSM-Anbieters, um einen Zielschlüssel zu generieren und dann eine Schlüsselübertragungsdatei (BYOK-Datei) zu erstellen. Das BYOK-Tool verwendet den `kid` aus [Schritt 1](#step-1-generate-a-kek) und die Datei „KEKforBYOK.publickey.pem“, die Sie in [Schritt 2](#step-2-download-the-kek-public-key) heruntergeladen haben, um einen verschlüsselten Zielschlüssel in einer BYOK-Datei zu generieren.

Übertragen Sie die BYOK-Datei an Ihren verbundenen Computer.

> [!NOTE] 
> Das Importieren von RSA-Schlüsseln mit 1.024 Bit wird nicht unterstützt. Das Importieren eines Elliptic Curve-Schlüssels mit der Kurve P-256K wird nicht unterstützt.
> 
> **Bekanntes Problem:** Das Importieren eines RSA 4K-Zielschlüssels von Luna HSMs wird nur mit Firmware 7.4.0 oder höher unterstützt.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Schritt 4: Übertragen des Schlüssels an Azure Key Vault

Um den Schlüsselimport abzuschließen, übertragen Sie das Schlüsselübertragungspaket (BYOK-Datei) von Ihrem nicht verbundenen Computer an den mit dem Internet verbundenen Computer. Verwenden Sie den Befehl [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import), um die BYOK-Datei in das Key Vault-HSM hochzuladen.

Verwenden Sie zum Importieren eines RSA-Schlüssels den folgenden Befehl: Der Parameter „--kty“ ist optional und wird standardmäßig auf „RSA-HSM“ festgelegt.
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Zum Importieren eines EC-Schlüssels müssen Sie den Schlüsseltyp und den Kurvennamen angeben.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Wenn der Upload erfolgreich ist, werden die Eigenschaften des importierten Schlüssels in der Azure-Befehlszeilenschnittstelle angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Sie können diesen HSM-geschützten Schlüssel jetzt in Ihrem Schlüsseltresor verwenden. Weitere Informationen finden Sie in dieser [Gegenüberstellung von Preisen und Funktionen](https://azure.microsoft.com/pricing/details/key-vault/).



