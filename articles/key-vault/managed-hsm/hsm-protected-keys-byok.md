---
title: 'Gewusst wie: Generieren und Übertragen von HSM-geschützten Schlüsseln für verwaltetes HSM von Azure Key Vault – Azure Key Vault| Microsoft-Dokumentation'
description: Verwenden Sie diesen Artikel zum Planen, Generieren und Übertragen Ihrer eigenen durch HSM geschützten Schlüssel für die Nutzung mit verwaltetem HSM. Dieser Ansatz wird auch als Bring Your Own Key (BYOK) bezeichnet.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 1a15ed6b92ade96dd2ed9ef6ffbbe17e2b1452ef
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581396"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Importieren von durch HSM geschützten Schlüsseln in verwaltetes HSM (BYOK)

 Das verwaltete HSM von Azure Key Vault unterstützt den Import von Schlüsseln, die in Ihrem lokalen Hardwaresicherheitsmodul (HSM) generiert wurden. Die Schlüssel verlassen nie die HSM-Schutzgrenze. Dieses Szenario wird häufig als *Bring Your Own Key* (BYOK) bezeichnet. Das verwaltete HSM verwendet die HSM-Adapter von Marvell LiquidSecurity (validiert für FIPS 140-2 Level 3) zum Schützen der Schlüssel.

Verwenden Sie die Informationen in diesem Artikel zum Planen, Generieren und Übertragen Ihrer eigenen durch HSM geschützten Schlüssel für die Nutzung mit verwaltetem HSM.

> [!NOTE]
> Diese Funktion steht für Azure China 21ViaNet nicht zur Verfügung. Diese Importmethode ist nur für [unterstützte HSMs](#supported-hsms) verfügbar. 

Weitere Informationen und ein Tutorial zu den ersten Schritten mit verwaltetem HSM finden Sie unter [Was ist verwaltetes HSM?](overview.md).

## <a name="overview"></a>Übersicht

Im Folgenden finden Sie eine Übersicht über den Prozess. Einige der Schritte, die Sie ausführen müssen, werden weiter unten in diesem Artikel beschrieben.

* Generieren Sie in verwaltetem HSM einen Schlüssel (als Schlüsselaustauschschlüssel (*Key Exchange Key*, KEK) bezeichnet). Der KEK muss ein RSA-HSM-Schlüssel sein, der nur den Schlüsselvorgang `import` umfasst. 
* Laden Sie den öffentlichen Schlüssel des KEK als PEM-Datei herunter.
* Übertragen Sie den öffentlichen Schlüssel des KEK auf einen Offlinecomputer, der mit einem lokalen HSM verbunden ist.
* Verwenden Sie auf dem Offlinecomputer das von Ihrem HSM-Anbieter bereitgestellte BYOK-Tool, um eine BYOK-Datei zu erstellen. 
* Der Zielschlüssel wird mit einem KEK verschlüsselt. Die Verschlüsselung bleibt bis zur Übertragung an das verwaltete HSM bestehen. Nur die verschlüsselte Version Ihres Schlüssels verlässt das lokale HSM.
* Ein KEK, der innerhalb eines verwalteten HSMs generiert wurde, kann nicht exportiert werden. HSMs erzwingen die Regel, dass keine unverschlüsselte Version eines KEK außerhalb eines verwalteten HSMs vorhanden sein darf.
* Der KEK muss sich in demselben verwalteten HSM befinden, in das der Zielschlüssel importiert werden soll.
* Wenn die BYOK-Datei in das verwaltete HSM hochgeladen wird, verwendet ein verwaltetes HSM den privaten Schlüssel des KEK, um das Zielschlüsselmaterial zu entschlüsseln und als HSM-Schlüssel zu importieren. Dieser Vorgang erfolgt vollständig innerhalb des HSM. Der Zielschlüssel verbleibt stets in den Schutzgrenzen des HSM.


## <a name="prerequisites"></a>Voraussetzungen

Für die Verwendung der Azure CLI-Befehle in diesem Artikel benötigen Sie Folgendes:

* Ein Abonnement für Microsoft Azure. Falls Sie über kein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial) registrieren.
* Azure-Befehlszeilenschnittstelle ab Version 2.12.0. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).
* Ein verwaltetes HSM, die [unterstützte HSMs-Liste](#supported-hsms) in Ihrem Abonnement. Weitere Informationen finden Sie unter [Schnellstart: Bereitstellen und Aktivieren eines verwalteten HSM mithilfe der Azure-Befehlszeilenschnittstelle](quick-create-cli.md). Dort erfahren Sie, wie Sie ein verwaltetes HSM bereitstellen und aktivieren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Geben Sie Folgendes ein, um sich über die Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

Weitere Informationen zu den Anmeldeoptionen für die Befehlszeilenschnittstelle finden Sie unter [Anmelden mit Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

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
|Schlüsselaustauschschlüssel (Key Exchange Key, KEK)|RSA| 2\.048 Bit<br />3\.072 Bit<br />4\.096 Bit|Verwaltetes HSM|Dies ist ein durch HSM gestütztes RSA-Schlüsselpaar, das im verwalteten HSM generiert wurde.|
|Zielschlüssel|
||RSA|2\.048 Bit<br />3\.072 Bit<br />4\.096 Bit|Anbieter-HSM|Der an das verwaltete HSM zu übertragende Schlüssel|
||EC|P-256<br />P-384<br />P-521|Anbieter-HSM|Der an das verwaltete HSM zu übertragende Schlüssel|
||Symmetrischer Schlüssel (oct-HSM)|128 Bit<br />192 Bit<br />256 Bit|Anbieter-HSM|Der an das verwaltete HSM zu übertragende Schlüssel|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Generieren und Übertragen des Schlüssels an das verwaltete HSM

So generieren und übertragen Sie den Schlüssel an das verwaltete HSM

* [Schritt 1: Generieren eines KEK](#step-1-generate-a-kek)
* [Schritt 2: Herunterladen des öffentlichen Schlüssels des KEK](#step-2-download-the-kek-public-key)
* [Schritt 3: Generieren des Schlüssels und Vorbereiten für die Übertragung](#step-3-generate-and-prepare-your-key-for-transfer)
* [Schritt 4: Übertragen Ihres Schlüssels an das verwaltete HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Schritt 1: Generieren eines KEK

Ein KEK ist ein RSA-Schlüssel, der in einem verwalteten HSM generiert wird. Der KEK wird zum Verschlüsseln des Schlüssels verwendet, den Sie importieren möchten (*Zielschlüssel*).

Für den KEK gilt Folgendes:
- Es muss sich um einen RSA-HSM-Schlüssel handeln (2.048 Bit, 3.072 Bit oder 4.096 Bit).
- Er muss im selben verwalteten HSM generiert werden, in das Sie den Zielschlüssel importieren möchten.
- Die zulässigen Schlüsselvorgänge müssen auf `import` festgelegt sein.

> [!NOTE]
> Der KEK muss als einzigen zulässigen Schlüsselvorgang „import“ aufweisen. „import“ schließt sich mit allen anderen wichtigen Vorgängen gegenseitig aus.

Verwenden Sie den Befehl [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-create), um den KEK mit auf `import` festgelegten Schlüsselvorgängen zu erstellen. Notieren Sie die Schlüssel-ID (`kid`), die vom folgenden Befehl zurückgegeben wird. (Sie verwenden den `kid`-Wert in [Schritt 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Schritt 2: Herunterladen des öffentlichen Schlüssels des KEK

Verwenden Sie den Befehl [az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-download), um den öffentlichen Schlüssel des KEK in eine PEM-Datei herunterzuladen. Der importierte Zielschlüssel ist mit dem öffentlichen Schlüssel des KEK verschlüsselt.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Übertragen Sie die Datei „KEKforBYOK.publickey.pem“ an Ihren Offlinecomputer. Sie benötigen diese Datei im nächsten Schritt.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Schritt 3: Generieren des Schlüssels und Vorbereiten für die Übertragung

Informieren Sie sich in der Dokumentation Ihres HSM-Anbieters darüber, wie Sie das BYOK-Tool herunterladen und installieren. Befolgen Sie die Anweisungen des HSM-Anbieters, um einen Zielschlüssel zu generieren und dann eine Schlüsselübertragungsdatei (BYOK-Datei) zu erstellen. Das BYOK-Tool verwendet den `kid` aus [Schritt 1](#step-1-generate-a-kek) und die Datei „KEKforBYOK.publickey.pem“, die Sie in [Schritt 2](#step-2-download-the-kek-public-key) heruntergeladen haben, um einen verschlüsselten Zielschlüssel in einer BYOK-Datei zu generieren.

Übertragen Sie die BYOK-Datei an Ihren verbundenen Computer.

> [!NOTE] 
> Das Importieren von RSA-Schlüsseln mit 1.024 Bit wird nicht unterstützt. Derzeit wird das Importieren eines EC-Schlüssels (Elliptic Curve, elliptische Kurve) nicht unterstützt.
>
> **Bekanntes Problem:** Das Importieren eines RSA 4K-Zielschlüssels von Luna HSMs wird nur mit Firmware 7.4.0 oder höher unterstützt.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Schritt 4: Übertragen Ihres Schlüssels an das verwaltete HSM

Um den Schlüsselimport abzuschließen, übertragen Sie das Schlüsselübertragungspaket (BYOK-Datei) von Ihrem nicht verbundenen Computer an den mit dem Internet verbundenen Computer. Verwenden Sie den Befehl [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-import), um die BYOK-Datei in das verwaltete HSM hochzuladen.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Wenn der Upload erfolgreich ist, werden die Eigenschaften des importierten Schlüssels in der Azure-Befehlszeilenschnittstelle angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Sie können diesen HSM-geschützten Schlüssel jetzt in Ihrem verwalteten HSM verwenden. Weitere Informationen finden Sie in dieser [Gegenüberstellung von Preisen und Funktionen](https://azure.microsoft.com/pricing/details/key-vault/).



