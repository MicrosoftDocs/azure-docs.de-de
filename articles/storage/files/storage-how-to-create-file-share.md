---
title: Erstellen einer Azure-Dateifreigabe | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie über das Azure-Portal, mithilfe von PowerShell oder mit der Azure-Befehlszeilenschnittstelle eine Azure-Dateifreigabe in Azure Files erstellen.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d945d5b79c274aa8e142203c56b27eb673e36741
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510511"
---
# <a name="create-a-file-share-in-azure-files"></a>Erstellen einer Dateifreigabe in Azure Files
Sie können Azure-Dateifreigaben mit dem  [Azure-Portal](https://portal.azure.com/), den Azure Storage-PowerShell-Cmdlets, den Azure Storage-Clientbibliotheken oder der Azure Storage-REST-API erstellen. In diesem Tutorial lernen Sie Folgendes:
* Erstellen einer Azure-Dateifreigabe über das Azure-Portal
* [Erstellen einer Azure-Dateifreigabe mithilfe von PowerShell](#create-file-share-through-powershell)
* [Erstellen einer Azure-Dateifreigabe unter Verwendung der Befehlszeilenschnittstelle](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Voraussetzungen
Sie können eine Azure-Dateifreigabe entweder unter Verwendung eines bereits vorhandenen Storage-Kontos erstellen oder ein [neues Azure Storage-Konto erstellen](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Wenn Sie eine Azure-Dateifreigabe mithilfe von PowerShell erstellen möchten, benötigen Sie den Kontoschlüssel und -namen Ihres Speicherkontos. Wenn Sie PowerShell oder die Befehlszeilenschnittstelle verwenden möchten, benötigen Sie einen Speicherkontoschlüssel.

## <a name="create-a-file-share-through-the-azure-portal"></a>Erstellen einer Dateifreigabe über das Azure-Portal
1. **Navigieren Sie im Azure-Portal zum Blatt „Speicherkonto“:**    
    ![Blatt „Speicherkonto“](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Klicken Sie auf die Schaltfläche „Dateifreigabe hinzufügen“:**    
    ![Klicken Sie auf die Schaltfläche „Dateifreigabe hinzufügen“.](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Geben Sie einen Namen und ein Kontingent an. Der aktuelle maximale Wert für das Kontingent beträgt 5 TiB:**    
    ![Geben Sie einen Namen und das gewünschte Kontingent für die neue Dateifreigabe an.](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Zeigen Sie Ihre neue Dateifreigabe an:**  ![Anzeigen Ihrer neuen Dateifreigabe](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Laden Sie eine Datei hoch:**  ![Hochladen einer Datei](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Navigieren Sie zu Ihrer Dateifreigabe, und verwalten Sie Ihre Verzeichnisse und Dateien:**  ![Durchsuchen der Dateifreigabe](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Erstellen einer Dateifreigabe mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Laden Sie die Azure PowerShell-Cmdlets herunter und installieren Sie diese anschließend, um die Verwendung von PowerShell vorzubereiten. Informationen zum Installationspunkt und zu Installationsanweisungen finden Sie unter  [Installieren und Konfigurieren von Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).

> [!Note]  
> Sie sollten daher das neueste Azure PowerShell-Modul herunterladen und installieren bzw. ein Upgrade durchführen.

1. **Erstellen Sie einen Kontext für Ihr Speicherkonto und den Schlüssel:** Der Kontext kapselt den Speicherkontonamen und den Kontoschlüssel. Anweisungen zum Kopieren Ihres Kontoschlüssels aus dem  [Azure-Portal](https://portal.azure.com/) finden Sie unter  [Zugriffsschlüssel](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Erstellen Sie eine neue Dateifreigabe:**    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Der Name der Dateifreigabe darf nur Kleinbuchstaben enthalten. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter  [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Erstellen einer Dateifreigabe unter Verwendung der Befehlszeilenschnittstelle (CLI)
1. **Laden Sie zur Vorbereitung auf die Verwendung der Befehlszeilenschnittstelle (CLI) die Azure-Befehlszeilenschnittstelle herunter, und installieren Sie sie.**  
    Informationen finden Sie unter  [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) sowie unter [Erste Schritte mit der Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Erstellen Sie eine Verbindungszeichenfolge für das Speicherkonto, unter dem Sie die Freigabe erstellen möchten.**  
    Ersetzen Sie im folgenden Beispiel  ```<storage-account>``` und  ```<resource_group>``` durch Ihren Speicherkontonamen und Ihre Ressourcengruppe:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Erstellen der Dateifreigabe**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Nächste Schritte
* [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](storage-how-to-use-files-windows.md)
* [Verwenden des Azure-Dateispeichers unter Linux](../storage-how-to-use-files-linux.md)
* [Einbinden einer Azure-Dateifreigabe über SMB mit macOS](storage-how-to-use-files-mac.md)

Weitere Informationen zu Azure Files erhalten Sie über diese Links.

* [Häufig gestellte Fragen](../storage-files-faq.md)
* [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Beheben von Problemen mit Azure File Storage unter Windows)      
* [Troubleshoot Azure File storage problems in Linux](storage-troubleshoot-linux-file-connection-problems.md) (Beheben von Problemen mit Azure File Storage unter Linux)   
