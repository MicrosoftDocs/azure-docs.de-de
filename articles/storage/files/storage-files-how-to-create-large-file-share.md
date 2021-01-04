---
title: Aktivieren und Erstellen großer Dateifreigaben – Azure Files
description: In diesem Artikel erfahren Sie, wie Sie große Dateifreigaben aktivieren und erstellen.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5acb8e347046780c84f0aa324b997abb5e2aa840
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629222"
---
# <a name="enable-and-create-large-file-shares"></a>Aktivieren und Erstellen großer Dateifreigaben

Wenn Sie in Ihrem Speicherkonto große Dateifreigaben aktivieren, können Ihre Dateifreigaben auf bis zu 100 TiB hochskaliert werden. Gleichzeitig werden die IOPS- und Durchsatzgrenzwerte für Standardfreigaben erhöht. Sie können diese Skalierung auch bei Ihren vorhandenen Speicherkonten für vorhandene Dateifreigaben aktivieren. Weitere Informationen finden Sie unter [Skalierungsziele für Dateifreigaben und Dateien](storage-files-scale-targets.md#azure-files-scale-targets). 

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Falls Sie die Azure CLI verwenden möchten, [installieren Sie die neueste Version](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Falls Sie das Azure PowerShell-Modul verwenden möchten, [installieren Sie die neueste Version](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="restrictions"></a>Beschränkungen

Derzeit können Sie nur lokal redundanten Speicher (LRS) oder zonenredundanten Speicher (ZRS) für Konten mit aktivierter großer Dateifreigabe verwenden. Sie können keinen geozonenredundanten Speicher (GZRS), georedundanten Speicher (GRS), georedundanten Speicher mit Lesezugriff (RA-GRS) oder geozonenredundanten Speicher mit Lesezugriff (RA-GZRS) verwenden.

Die Aktivierung großer Dateifreigaben für ein Konto kann nicht rückgängig gemacht werden. Danach kann Ihr Konto nicht mehr in GZRS, GRS, RA-GRS oder RA-GZRS konvertiert werden.

## <a name="create-a-new-storage-account"></a>Erstellen eines neuen Speicherkontos

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Azure-Portal **Alle Dienste** aus. 
1. Geben Sie in der Liste der Ressourcen **Speicherkonten** ein. Die Liste wird während Ihrer Eingabe gefiltert. Wählen Sie **Speicherkonten**.
1. Wählen Sie im angezeigten Fenster **Speicherkonten** die Option **Hinzufügen** aus.
1. Wählen Sie das Abonnement aus, das Sie zum Erstellen des Speicherkontos verwenden möchten.
1. Wählen Sie unter dem Feld **Ressourcengruppe** die Option **Neu erstellen**. Geben Sie einen Namen für die neue Ressourcengruppe ein.

    ![Screenshot: Erstellen einer Ressourcengruppe im Portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Geben Sie als Nächstes einen Namen für Ihr Speicherkonto ein. Der Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls 3 bis 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.
1. Wählen Sie einen Standort für Ihr Speicherkonto aus.
1. Legen Sie die Replikation auf **lokal redundanten Speicher** oder **zonenredundanten Speicher** fest.
1. Behalten Sie die Standardwerte für die folgenden Felder bei:

   |Feld  |Wert  |
   |---------|---------|
   |Bereitstellungsmodell     |Ressourcen-Manager         |
   |Leistung     |Standard         |
   |Kontoart     |StorageV2 (universell v2)         |
   |Zugriffsebene     |Heiß         |

1. Wählen Sie **Erweitert** und dann das Optionsfeld **Aktiviert** rechts neben **Große Dateifreigaben** aus.
1. Wählen Sie **Überprüfen + erstellen**, um die Speicherkontoeinstellungen zu überprüfen und das Konto zu erstellen.

    ![Screenshot mit der Optionsschaltfläche „Aktiviert“ für ein neues Speicherkonto im Azure-Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Klicken Sie auf **Erstellen**.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Installieren Sie zunächst die neueste Version der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), damit Sie große Dateifreigaben aktivieren können.

Um ein Speicherkonto mit aktivierten großen Dateifreigaben zu erstellen, verwenden Sie den folgenden Befehl. Ersetzen Sie `<yourStorageAccountName>`, `<yourResourceGroup>` und `<yourDesiredRegion>` durch Ihre Angaben.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Installieren Sie zunächst die neueste Version von PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0), damit Sie große Dateifreigaben aktivieren können.

Um ein Speicherkonto mit aktivierten großen Dateifreigaben zu erstellen, verwenden Sie den folgenden Befehl. Ersetzen Sie `<yourStorageAccountName>`, `<yourResourceGroup>` und `<yourDesiredRegion>` durch Ihre Angaben.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Aktivieren großer Dateifreigaben für ein vorhandenes Konto

Sie können große Dateifreigaben auch für Ihre vorhandenen Konten aktivieren. Nach dem Aktivieren großer Dateifreigaben kann keine Konvertierung mehr in GZRS, GRS, RA-GRS oder RA-GZRS durchgeführt werden. Die Aktivierung großer Dateifreigaben für dieses Speicherkonto kann nicht rückgängig gemacht werden.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und wechseln Sie zu dem Speicherkonto, für das Sie große Dateifreigaben aktivieren möchten.
1. Öffnen Sie das Speicherkonto, und wählen Sie **Konfiguration** aus.
1. Wählen Sie für **Große Dateifreigaben** die Option **Aktiviert** und dann **Speichern** aus.
1. Wählen Sie **Übersicht** und dann **Aktualisieren** aus.

![Auswählen der Optionsschaltfläche „Aktiviert“ für ein vorhandenes Speicherkonto im Azure-Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Sie haben jetzt große Dateifreigaben für Ihr Speicherkonto aktiviert. Als Nächstes müssen Sie das [Kontingent der vorhandenen Freigabe aktualisieren](#expand-existing-file-shares), um die erweiterte Kapazität und Skalierung nutzen zu können.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den folgenden Befehl, um große Dateifreigaben für Ihr vorhandenes Konto zu aktivieren. Ersetzen Sie `<yourStorageAccountName>` und `<yourResourceGroup>` durch Ihre Angaben.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Sie haben jetzt große Dateifreigaben für Ihr Speicherkonto aktiviert. Als Nächstes müssen Sie das [Kontingent der vorhandenen Freigabe aktualisieren](#expand-existing-file-shares), um die erweiterte Kapazität und Skalierung nutzen zu können.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie den folgenden Befehl, um große Dateifreigaben für Ihr vorhandenes Konto zu aktivieren. Ersetzen Sie `<yourStorageAccountName>` und `<yourResourceGroup>` durch Ihre Angaben.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Sie haben jetzt große Dateifreigaben für Ihr Speicherkonto aktiviert. Als Nächstes müssen Sie das [Kontingent der vorhandenen Freigabe aktualisieren](#expand-existing-file-shares), um die erweiterte Kapazität und Skalierung nutzen zu können.

---

## <a name="create-a-large-file-share"></a>Erstellen einer großen Dateifreigabe

Nachdem Sie große Dateifreigaben für Ihr Speicherkonto aktiviert haben, können Sie darin Dateifreigaben mit höheren Kontingenten erstellen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Das Erstellen einer großen Dateifreigabe ist nahezu identisch mit dem Erstellen einer standardmäßigen Dateifreigabe. Der Hauptunterschied besteht darin, dass Sie ein Kontingent von bis zu 100 TiB festlegen können.

1. Wählen Sie in Ihrem Speicherkonto die Option **Dateifreigaben** aus.
1. Klicken Sie auf **+ Dateifreigabe**.
1. Geben Sie einen Namen für Ihre Dateifreigabe ein. Sie können auch die gewünschte Kontingentgröße (bis zu 100 TiB) festlegen. Klicken Sie anschließend auf **Erstellen**. 

![Benutzeroberfläche des Azure-Portals mit den Feldern „Name“ und „Kontingent“](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Erstellen einer großen Dateifreigabe den folgenden Befehl. Ersetzen Sie `<yourStorageAccountName>`, `<yourStorageAccountKey>` und `<yourFileShareName>` durch Ihre Angaben.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie zum Erstellen einer großen Dateifreigabe den folgenden Befehl. Ersetzen Sie `<YourStorageAccountName>`, `<YourStorageAccountKey>` und `<YourStorageAccountFileShareName>` durch Ihre Angaben.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Erweitern vorhandener Dateifreigaben

Nachdem Sie große Dateifreigaben für Ihr Speicherkonto aktiviert haben, können Sie auch vorhandene Dateifreigaben in diesem Konto auf das höhere Kontingent erweitern. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie in Ihrem Speicherkonto die Option **Dateifreigaben** aus.
1. Klicken Sie mit der rechten Maustaste auf die Dateifreigabe, und wählen Sie dann **Kontingent** aus.
1. Geben Sie die gewünschte neue Größe ein, und wählen Sie dann **OK** aus.

![Benutzeroberfläche des Azure-Portals mit „Kontingent“ für vorhandene Dateifreigaben](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den folgenden Befehl, um das Kontingent auf die maximale Größe festzulegen. Ersetzen Sie `<yourStorageAccountName>`, `<yourStorageAccountKey>` und `<yourFileShareName>` durch Ihre Angaben.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie den folgenden Befehl, um das Kontingent auf die maximale Größe festzulegen. Ersetzen Sie `<YourStorageAccountName>`, `<YourStorageAccountKey>` und `<YourStorageAccountFileShareName>` durch Ihre Angaben.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Nächste Schritte

* [Einbinden einer Dateifreigabe unter Windows](storage-how-to-use-files-windows.md)
* [Einbinden einer Dateifreigabe unter Linux](storage-how-to-use-files-linux.md)
* [Einbinden einer Dateifreigabe unter macOS](storage-how-to-use-files-mac.md)