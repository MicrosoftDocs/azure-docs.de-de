---
title: Vollständige Sicherung/Wiederherstellung und selektive Wiederherstellung für verwaltete Azure-HSMs
description: Dieses Dokument enthält Informationen zur vollständigen Sicherung/Wiederherstellung sowie zur selektiven Wiederherstellung.
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e051a36b3c91fadc0c3b602cb4ba8e3dbcff1294
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367130"
---
# <a name="full-backup-and-restore"></a>Vollständige Sicherung und Wiederherstellung

> [!NOTE]
> Dieses Feature steht nur für den Ressourcentyp „Verwaltetes HSM“ zur Verfügung.

Verwaltete HSMs unterstützen das Erstellen einer vollständigen Sicherung des gesamten Inhalts des HSM – einschließlich aller Schlüssel, Versionen, Attribute, Tags und Rollenzuweisungen. Die Sicherung wird mit kryptografischen Schlüsseln verschlüsselt, die der Sicherheitsdomäne des HSM zugeordnet sind.

Sicherungsvorgänge werden auf der Datenebene durchgeführt. Der Aufrufer, der den Sicherungsvorgang initiiert, muss über die Berechtigung zum Ausführen von „dataAction“ ( **Microsoft.KeyVault/managedHsm/backup/start/action** ) verfügen.

Nur die folgenden integrierten Rollen sind zum Ausführen einer vollständigen Sicherung berechtigt:
- Managed HSM Administrator (Administrator für verwaltete HSMs)
- Managed HSM Backup (Sicherung verwalteter HSMs)

Zum Ausführen einer vollständigen Sicherung sind folgende Angaben erforderlich:
- HSM-Name oder -URL
- Speicherkontoname
- Speicherkonto-Blobspeichercontainer
- SAS-Token des Speichercontainers mit Berechtigungen vom Typ `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Vollständige Sicherung

Die Sicherung ist ein zeitintensiver Vorgang. Es wird jedoch sofort eine Auftrags-ID zurückgegeben. Mithilfe dieser Auftrags-ID können Sie den Status des Sicherungsprozesses überprüfen. Durch den Sicherungsprozess wird im angegebenen Container ein Ordner mit folgendem Namensmuster erstellt: **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** . Hierbei ist „HSM_NAME“ der Name des verwalteten HSM, das gesichert wird, und „YYYY“, „MM“, „DD“, „HH“, „mm“ und „SS“ sind Jahr, Monat, Tag, Stunde, Minuten und Sekunden des Zeitpunkts (in UTC), zu dem der Sicherungsbefehl empfangen wurde.

Während des Sicherungsvorgangs ist der Durchsatz des HSM möglicherweise eingeschränkt, da einige HSM-Partitionen durch den Sicherungsvorgang beansprucht werden.

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Vollständige Wiederherstellung

Mit einer vollständigen Wiederherstellung können Sie den Inhalt des HSM aus einer vorherigen Sicherung vollständig wiederherstellen – einschließlich aller Schlüssel, Versionen, Attribute, Tags und Rollenzuweisungen. Alles, was derzeit im HSM gespeichert ist, wird entfernt, und es wird in den Zustand zurückversetzt, in dem es sich zum Zeitpunkt der Sicherungserstellung befand.

> [!IMPORTANT]
> Die vollständige Wiederherstellung ist ein äußerst destruktiver Vorgang. Daher muss innerhalb der letzten 30 Minuten eine vollständige Sicherung durchgeführt worden sein, damit ein Vorgang vom Typ `restore` ausgeführt werden kann.

Wiederherstellungsvorgänge werden auf der Datenebene durchgeführt. Der Aufrufer, der den Wiederherstellungsvorgang startet, muss über die Berechtigung zum Ausführen von „dataAction“ ( **Microsoft.KeyVault/managedHsm/restore/start/action** ) verfügen. Das Quell-HSM, in dem die Sicherung erstellt wurde, und das Ziel-HSM, in dem die Wiederherstellung durchgeführt wird, **müssen** über die gleiche Sicherheitsdomäne verfügen. Weitere Informationen zur Sicherheitsdomäne verwalteter HSMs finden Sie [hier](security-domain.md).

Zum Ausführen einer vollständigen Wiederherstellung sind folgende Angaben erforderlich:
- HSM-Name oder -URL
- Speicherkontoname
- Speicherkonto-Blobcontainer
- SAS-Token des Speichercontainers mit Berechtigungen vom Typ `rl`
- Name des Speichercontainerordners, in dem die Quellsicherung gespeichert ist

Die Wiederherstellung ist ein zeitintensiver Vorgang. Es wird jedoch sofort eine Auftrags-ID zurückgegeben. Mithilfe dieser Auftrags-ID können Sie den Status des Wiederherstellungsprozesses überprüfen. Während des Wiederherstellungsprozesses wird das HSM in einen Wiederherstellungsmodus versetzt, und alle Datenebenenbefehle (mit Ausnahme von „check restore status“) sind deaktiviert.

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="backup-hsm"></a>Sichern des HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie den Artikel [Verwalten eines verwalteten HSM über die Azure-Befehlszeilenschnittstelle](key-management.md).
- Informieren Sie sich ausführlicher über die [Sicherheitsdomäne verwalteter HSMs](security-domain.md).