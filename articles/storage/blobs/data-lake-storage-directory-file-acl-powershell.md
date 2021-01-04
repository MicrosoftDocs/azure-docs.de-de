---
title: Azure Data Lake Storage Gen2 PowerShell für Dateien und Zugriffssteuerungslisten
description: Verwenden Sie PowerShell-Cmdlets, um Verzeichnisse, Dateien und Zugriffssteuerungslisten in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaa34da7dbdf8d315cf60d84cf15ef428f4c7900
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913350"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von PowerShell zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie PowerShell zum Erstellen und Verwalten von Verzeichnissen, Dateien und Berechtigungen in Speicherkonten verwenden, für die der hierarchische Namespace aktiviert ist. 

[Beispiel](/powershell/module/Az.Storage/) | [Zuordnung von Gen1 zu Gen2](#gen1-gen2-map) | [Feedback senden](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
> * Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.
> * .NET Framework 4.7.2 oder höher ist installiert. Siehe [Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) (Herunterladen von .NET Framework).
> * PowerShell Version `5.1` oder höher

## <a name="install-the-powershell-module"></a>Installieren des PowerShell-Moduls

1. Überprüfen Sie mit dem folgenden Befehl, ob PowerShell Version `5.1` oder höher installiert ist.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Informationen zum Aktualisieren der PowerShell-Version finden Sie unter [Aktualisieren einer vorhandenen Windows PowerShell-Version](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).
    
2. Installieren Sie das Modul **Az.Storage**.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Weitere Informationen zum Installieren von PowerShell-Modulen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

Öffnen Sie ein Windows PowerShell-Befehlsfenster, melden Sie sich dann mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, das Sie erstellen und in dem Sie Verzeichnisse verwalten möchten. In diesem Beispiel ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Wählen Sie als Nächstes aus, wie Ihre Befehle die Autorisierung für das Speicherkonto abrufen sollen. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Option 1: Abrufen der Autorisierung mithilfe von Azure Active Directory (AD)

Bei diesem Ansatz stellt das System sicher, dass Ihr Benutzerkonto über die entsprechenden Azure RBAC-Zuweisungen (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung von Azure) und ACL-Berechtigungen verfügt. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Option 2: Abrufen der Autorisierung mithilfe des Speicherkontoschlüssels

Bei diesem Ansatz überprüft das System nicht die Azure RBAC- oder ACL-Berechtigungen.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Mit dem Cmdlet `New-AzStorageContainer` können Sie einen erstellen. 

In diesem Beispiel wird ein Container mit dem Namen `my-file-system` erstellt.

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie mit dem Cmdlet `New-AzDataLakeGen2Item` eine Verzeichnisreferenz. 

In diesem Beispiel wird einem Container das Verzeichnis `my-directory` hinzugefügt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

In diesem Beispiel wird das gleiche Verzeichnis hinzugefügt, es werden jedoch auch die Berechtigungen, die umask, Eigenschaftswerte und Metadatenwerte festgelegt. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Anzeigen von Verzeichniseigenschaften

In diesem Beispiel werden mit dem Cmdlet `Get-AzDataLakeGen2Item` ein Verzeichnis abgerufen und dann die Eigenschaftswerte in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```
> [!NOTE]
> Lassen Sie zum Abrufen des Stammverzeichnisses des Containers den Parameter `-Path` weg.

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Mit dem Cmdlet `Move-AzDataLakeGen2Item` können Sie ein Verzeichnis umbenennen oder verschieben.

In diesem Beispiel wird ein Verzeichnis mit dem Namen `my-directory` in `my-new-directory` umbenannt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Verwenden Sie den Parameter `-Force`, wenn Sie ohne Eingabeaufforderungen überschreiben möchten.

Im folgenden Beispiel wird ein Verzeichnis mit dem Namen `my-directory` in ein Unterverzeichnis von `my-directory-2` mit dem Namen `my-subdirectory` verschoben. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Ein Verzeichnis kann mit dem Cmdlet `Remove-AzDataLakeGen2Item` gelöscht werden.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Mithilfe des Parameters `-Force` können Sie die Datei ohne Aufforderung entfernen.

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis

Laden Sie eine Datei mithilfe des Cmdlets `Get-AzDataLakeGen2ItemContent` aus einem Verzeichnis herunter.

In diesem Beispiel wird eine Datei mit dem Namen `upload.txt` aus einem Verzeichnis mit dem Namen `my-directory` heruntergeladen. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Listen Sie den Inhalt eines Verzeichnisses mithilfe des Cmdlets `Get-AzDataLakeGen2ChildItem` auf. Sie können den optionalen Parameter `-OutputUserPrincipalName` verwenden, um den Namen (anstelle der Objekt-ID) von Benutzern abzurufen.

In diesem Beispiel wird der Inhalt des Verzeichnisses `my-directory` aufgelistet.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Im folgenden Beispiel werden die Eigenschaften `ACL`, `Permissions`, `Group`und `Owner` der einzelnen Elemente im Verzeichnis aufgelistet. Der Parameter `-FetchProperty` ist erforderlich, um Werte für die Eigenschaft `ACL` abzurufen. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Um den Inhalt des Stammverzeichnisses des Containers aufzulisten, lassen Sie den Parameter `-Path` weg.

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Laden Sie eine Datei mithilfe des Cmdlets `New-AzDataLakeGen2Item` in ein Verzeichnis hoch.

In diesem Beispiel wird die Datei `upload.txt` in das Verzeichnis `my-directory` hochgeladen. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

In diesem Beispiel wird die gleiche Datei hochgeladen. Anschließend werden jedoch die Berechtigungen, die umask, die Eigenschaftswerte und die Metadatenwerte der Zieldatei festgelegt. In diesem Beispiel werden diese Werte außerdem in der Konsole ausgegeben.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Lassen Sie zum Hochladen einer Datei in das Stammverzeichnis des Containers den Parameter `-Path` weg.

## <a name="show-file-properties"></a>Anzeigen von Dateieigenschaften

In diesem Beispiel werden mit dem Cmdlet `Get-AzDataLakeGen2Item` eine Datei abgerufen und dann die Eigenschaftswerte in der Konsole ausgegeben.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Löschen von Dateien

Mit dem Cmdlet `Remove-AzDataLakeGen2Item` können Sie eine Datei löschen.

In diesem Beispiel wird die Datei `upload.txt` gelöscht. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Mithilfe des Parameters `-Force` können Sie die Datei ohne Aufforderung entfernen.

## <a name="manage-access-control-lists-acls"></a>Verwalten von Zugriffssteuerungslisten (Access Control Lists, ACLs)

Sie können Zugriffsberechtigungen für Verzeichnisse und Dateien abrufen, festlegen und aktualisieren.

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um Befehle zu autorisieren, stellen Sie sicher, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="get-an-acl"></a>Abrufen einer Zugriffssteuerungsliste

Verwenden Sie das Cmdlet `Get-AzDataLakeGen2Item`, um die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei abzurufen.

In diesem Beispiel wird die Zugriffssteuerungsliste des Stammverzeichnisses eines **Containers** abgerufen und dann an der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In diesem Beispiel wird die Zugriffssteuerungsliste eines **Verzeichnisses** abgerufen und dann in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

In diesem Beispiel wird die Zugriffssteuerungsliste einer **Datei** abgerufen und dann in der Konsole ausgegeben.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Die folgende Abbildung zeigt die Ausgabe nach dem Abrufen der Zugriffssteuerungsliste eines Verzeichnisses.

![Ausgabe der Zugriffssteuerungsliste für Verzeichnis abrufen](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In diesem Beispiel verfügt der zuständige Benutzer über Berechtigungen zum Lesen, Schreiben und Ausführen. Die zuständige Gruppe verfügt lediglich über Berechtigungen zum Lesen und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Festlegen einer Zugriffssteuerungsliste

Verwenden Sie das Cmdlet `set-AzDataLakeGen2ItemAclObject`, um eine Zugriffssteuerungsliste für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer zu erstellen. Verwenden Sie dann das Cmdlet `Update-AzDataLakeGen2Item`, um die Zugriffssteuerungsliste zu committen.

In diesem Beispiel wird die Zugriffssteuerungsliste für das Stammverzeichnis eines **Containers** für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann an der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In diesem Beispiel wird die Zugriffssteuerungsliste auf ein **Verzeichnis** für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
In diesem Beispiel wird die Zugriffssteuerungsliste auf eine **Datei** für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Die folgende Abbildung zeigt die Ausgabe nach dem Festlegen der Zugriffssteuerungsliste einer Datei.

![Ausgabe der Zugriffssteuerungsliste für Datei abrufen](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In diesem Beispiel verfügen der zuständige Benutzer und die zuständige Gruppe nur über Berechtigungen zum Lesen und Schreiben. Alle anderen Benutzer verfügen über Berechtigungen zum Schreiben und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="add-or-update-an-acl-entry"></a>Hinzufügen oder Aktualisieren eines ACL-Eintrags

Rufen Sie zuerst die Zugriffssteuerungsliste ab. Verwenden Sie dann das Cmdlet `set-AzDataLakeGen2ItemAclObject`, um einen ACL-Eintrag hinzuzufügen oder zu aktualisieren. Verwenden Sie das Cmdlet `Update-AzDataLakeGen2Item`, um die Zugriffssteuerungsliste zu committen.

Dieses Beispiel erstellt oder aktualisiert die Zugriffssteuerungsliste in einem **Verzeichnis** für einen Benutzer.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Entfernen eines ACL-Eintrags

Dieses Beispiel entfernt einen Eintrag aus einer vorhandenen Zugriffssteuerungsliste.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="set-an-acl-recursively"></a>Rekursives Festlegen einer Zugriffssteuerungsliste

Sie können Zugriffssteuerungslisten rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen. Weitere Informationen finden Sie unter [Festlegen von Zugriffssteuerungslisten (ACLs) für Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Zuordnung von Gen1 zu Gen2

In der folgenden Tabelle ist dargestellt, wie die für Data Lake Storage Gen1 verwendeten Cmdlets den Cmdlets für Data Lake Storage Gen2 zugeordnet werden.

|Data Lake Storage Gen1-Cmdlet| Data Lake Storage Gen2-Cmdlet| Notizen |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Standardmäßig listet das Cmdlet „Get-AzDataLakeGen2ChildItem“ nur die untergeordneten Elemente der ersten Ebene auf. Mit dem Parameter „-Recurse“ werden untergeordnete Elemente rekursiv aufgelistet. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Die Ausgabeelemente des Cmdlets „Get-AzDataLakeGen2Item“ verfügen über die folgenden Eigenschaften: ACL, Besitzer, Gruppe, Berechtigung.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Mit dem Cmdlet „Get-AzDataLakeGen2FileContent“ wird der Dateiinhalt in eine lokale Datei heruntergeladen.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Dieses Cmdlet lädt den neuen Dateiinhalt aus einer lokalen Datei hoch.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Mit dem Cmdlet „Update-AzDataLakeGen2Item“ wird nur ein einzelnes Element und nicht rekursiv aktualisiert. Wenn Sie rekursiv aktualisieren möchten, listen Sie Elemente mithilfe des Cmdlets „Get-AzDataLakeStoreChildItem“ auf, und führen Sie dann eine Pipeline zum Cmdlet „Update-AzDataLakeGen2Item“ aus.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Das Cmdlet „Get-AzDataLakeGen2Item“ meldet einen Fehler, wenn das Element nicht vorhanden ist.|

## <a name="see-also"></a>Weitere Informationen

* [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Storage PowerShell-Cmdlets](/powershell/module/az.storage)