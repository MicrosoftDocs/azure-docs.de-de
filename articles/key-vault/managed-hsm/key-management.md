---
title: 'Verwalten von Schlüsseln in einem verwalteten HSM: Azure Key Vault | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie Schlüssel in einem verwalteten HSM verwalten.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 3f054638e09061c652946c9c2db1a32db73c23d9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521032"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Verwalten eines verwalteten HSM über die Azure-Befehlszeilenschnittstelle

> [!NOTE]
> Von Key Vault werden zwei Arten von Ressourcen unterstützt: Tresore und verwaltete HSMs. In diesem Artikel werden **verwaltete HSMs** behandelt. Informationen zum Verwalten eines Tresors finden Sie unter [Verwalten von Key Vault mit der Azure CLI](../general/manage-with-cli2.md).

Eine Übersicht über verwaltete HSMs finden Sie unter [Was ist verwaltetes HSM von Azure Key Vault? (Vorschauversion)](overview.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Ein Abonnement für Microsoft Azure. Falls Sie über kein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial) registrieren.
* Azure-Befehlszeilenschnittstelle ab Version 2.12.0. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).
* Ein verwaltetes HSM in Ihrem Abonnement. Weitere Informationen finden Sie unter [Schnellstart: Bereitstellen und Aktivieren eines verwalteten HSM mithilfe der Azure-Befehlszeilenschnittstelle](quick-create-cli.md). Dort erfahren Sie, wie Sie ein verwaltetes HSM bereitstellen und aktivieren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Geben Sie Folgendes ein, um sich über die Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

Weitere Informationen zu den Anmeldeoptionen für die Befehlszeilenschnittstelle finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

> [!NOTE]
> In allen nachfolgenden Befehlen werden zwei Verwendungsmethoden gezeigt: eine mit den Parametern **--hsm-name** und **--name** (für den Schlüsselnamen) und eine mit dem Parameter **--id** , um die Angabe der gesamten URL (ggf. einschließlich des Schlüsselnamens) zu ermöglichen. Letztere Methode ist hilfreich, wenn der Aufrufer (ein Benutzer oder eine Anwendung) keinen Lesezugriff auf die Steuerungsebene und nur eingeschränkten Zugriff auf die Datenebene hat.

## <a name="create-an-hsm-key"></a>Erstellen eines HSM-Schlüssels

Verwenden Sie den Befehl `az keyvault key create`, um einen Schlüssel zu erstellen.

### <a name="create-an-rsa-key"></a>Erstellen eines RSA-Schlüssels

Das folgende Beispiel zeigt, wie Sie einen **RSA** -Schlüssel mit 3072 Bit erstellen, der nur für Vorgänge (--ops) vom Typ **wrapKey, unwrapKey** verwendet wird. 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Beachten Sie, dass der Vorgang `get` nur den öffentlichen Schlüssel und Schlüsselattribute zurückgibt. Er gibt nicht den privaten Schlüssel (im Falle eines asymmetrischen Schlüssels) oder das Schlüsselmaterial (im Falle eines symmetrischen Schlüssels) zurück.

### <a name="create-an-ec-key"></a>Erstellen eines EC-Schlüssels

Das folgende Beispiel zeigt, wie Sie einen **EC** -Schlüssel mit P-256-Kurve erstellen, der nur für Vorgänge (--ops) vom Typ **Signieren und Überprüfen** verwendet wird und über zwei Tags ( **usage** und **appname** ) verfügt. Mithilfe von Tags können dem Schlüssel zusätzliche Metadaten für die Nachverfolgung und Verwaltung hinzugefügt werden.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Erstellen eines symmetrischen 256-Bit-Schlüssels

Das folgende Beispiel zeigt, wie Sie einen **symmetrischen** Schlüssel mit 256 Bit erstellen, der nur für Vorgänge (--ops) vom Typ **Verschlüsseln und Entschlüsseln** verwendet wird.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Anzeigen von Schlüsselattributen und Tags

Verwenden Sie den Befehl `az keyvault key show`, um Attribute, Versionen und Tags für einen Schlüssel anzuzeigen.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Auflisten von Schlüsseln

Verwenden Sie den Befehl `az keyvault key list`, um alle Schlüssel in einem verwalteten HSM aufzulisten.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Löschen eines Schlüssels

Verwenden Sie den Befehl `az keyvault key delete`, um einen Schlüssel aus einem verwalteten HSM zu löschen. Beachten Sie, dass vorläufiges Löschen immer aktiviert ist. Ein gelöschter Schlüssel bleibt daher im gelöschten Zustand und kann bis zum Ablauf der Aufbewahrungsdauer wiederhergestellt werden. Danach wird der Schlüssel bereinigt (endgültig gelöscht) und kann nicht mehr wiederhergestellt werden.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Auflisten gelöschter Schlüssel

Verwenden Sie den Befehl `az keyvault key list-deleted`, um alle Schlüssel im gelöschten Zustand in Ihrem verwalteten HSM aufzulisten.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Wiederherstellen eines gelöschten Schlüssels (Rückgängigmachen des Löschvorgangs)

Verwenden Sie den Befehl `az keyvault key list-deleted`, um alle Schlüssel im gelöschten Zustand in Ihrem verwalteten HSM aufzulisten. Wenn Sie bei der Schlüsselwiederherstellung einen gelöschten Schlüssel mithilfe des Parameters „--id“ wiederherstellen (also den Löschvorgang rückgängig machen) möchten, müssen Sie sich den Wert `recoveryId` des gelöschten Schlüssels notieren, den Sie durch Ausführen des Befehls `az keyvault key list-deleted` erhalten haben.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Bereinigen (endgültiges Löschen) eines Schlüssels

Verwenden Sie den Befehl `az keyvault key purge`, um einen Schlüssel zu bereinigen (endgültig zu löschen).

> [!NOTE]
> Sollte für das verwaltete HSM der Löschschutz aktiviert sein, ist der Löschvorgang nicht zulässig. Der Schlüssel wird nach Ablauf des Aufbewahrungszeitraums automatisch bereinigt.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Erstellen einer Schlüsselsicherung

Verwenden Sie `az keyvault key backup`, um eine Schlüsselsicherung zu erstellen. Bei der Sicherungsdatei handelt es sich um ein verschlüsseltes Blob, das kryptografisch an die Sicherheitsdomäne des Quell-HSM gebunden ist. Sie kann nur in HSMs mit der gleichen Sicherheitsdomäne wiederhergestellt werden. Weitere Informationen zur Sicherheitsdomäne finden Sie [hier](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Wiederherstellen eines einzelnen Schlüssels aus einer Sicherung

Verwenden Sie `az keyvault key restore`, um einen einzelnen Schlüssel wiederherzustellen. Das Quell-HSM, in dem die Sicherung erstellt wurde, muss über die gleiche Sicherheitsdomäne verfügen wie das Ziel-HSM, in dem der Schlüssel wiederhergestellt wird.

> [!NOTE]
> Die Wiederherstellung ist nicht erfolgreich, wenn ein gleichnamiger Schlüssel im aktiven oder gelöschten Zustand vorhanden ist.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Importieren eines Schlüssels aus einer Datei

Verwenden Sie den Befehl `az keyvault key import`, um einen Schlüssel (nur RSA und EC) aus einer Datei zu importieren. Die Zertifikatdatei muss über einen privaten Schlüssel verfügen und die PEM-Codierung verwenden (gemäß Definition in den RFCs [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Informationen zum Importieren eines Schlüssels aus Ihrem lokalen HSM in ein verwaltetes HSM finden Sie unter [Importieren von durch HSM geschützten Schlüsseln in verwaltetes HSM (BYOK)](hsm-protected-keys-byok.md).

## <a name="next-steps"></a>Nächste Schritte

- Die vollständige Referenz zur Azure CLI für Key Vault-Befehle finden Sie in der [Referenz für die Schlüsseltresor-CLI](/cli/azure/keyvault).
- Eine Programmierreferenz finden Sie im [Entwicklerhandbuch für Azure Key Vault](../general/developers-guide.md).
- Weitere Informationen zur Rollenverwaltung für verwaltetes HSM finden Sie [hier](role-management.md).
- Weitere Informationen zu bewährten Methoden für verwaltetes HSM finden Sie [hier](best-practices.md).
