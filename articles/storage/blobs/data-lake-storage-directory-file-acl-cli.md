---
title: Verwenden der Azure-Befehlszeilenschnittstelle (Azure CLI) für Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2
description: Verwenden Sie die Azure-Befehlszeilenschnittstelle, um Verzeichnisse, Dateien und Zugriffssteuerungslisten (ACLs) in Speicherkonten zu verwalten, die über einen hierarchischen Namespace verfügen.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42359eb8a2bfdad23589e0302b80e7806b388510
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913605"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Verwenden der Azure CLI zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/) zum Erstellen und Verwalten von Verzeichnissen, Dateien und Berechtigungen in Speicherkonten verwenden, die über einen hierarchischen Namespace verfügen. 

[Beispiele](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [Feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
> * Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.
> * Azure CLI, Version `2.6.0` oder höher.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Stellen Sie sicher, dass die korrekte Version der Azure CLI installiert ist.

1. Öffnen Sie [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-Befehlszeilenschnittstelle lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Überprüfen Sie mit dem folgenden Befehl, ob die installierte Version der Azure-Befehlszeilenschnittstelle `2.6.0` oder höher ist.

   ```azurecli
    az --version
   ```
   Wenn Ihre Version der Azure-Befehlszeilenschnittstelle kleiner als `2.6.0` ist, dann installieren Sie eine neuere Version. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

1. Wenn Sie die Azure-Befehlszeilenschnittstelle lokal verwenden, führen Sie den Anmeldebefehl aus.

   ```azurecli
   az login
   ```

   Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

   Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein. Melden Sie sich dann im Browser mit Ihren Anmeldeinformationen an.

   Weitere Informationen zu verschiedenen Authentifizierungsmethoden finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure CLI](./authorize-data-operations-cli.md).

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

> [!NOTE]
> Das in diesem Artikel dargestellte Beispiel zeigt die Azure Active Directory (AD)-Autorisierung. Weitere Informationen zu Autorisierungsmethoden finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure CLI](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Mit dem Befehl `az storage fs create` können Sie einen erstellen. 

In diesem Beispiel wird ein Container namens `my-file-system` erstellt.

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Anzeigen der Containereigenschaften

Sie können die Eigenschaften eines Containers mithilfe des Befehls `az storage fs show` in der Konsole ausgeben.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Auflisten von Containerinhalten

Listen Sie den Inhalt eines Verzeichnisses mit dem Befehl `az storage fs file list` auf.

In diesem Beispiel wird der Inhalt des Containers `my-file-system` aufgelistet.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Löschen eines Containers

Mit dem Befehl `az storage fs delete` können Sie einen Container löschen.

In diesem Beispiel wird der Container `my-file-system` gelöscht. 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie eine Verzeichnisreferenz mit dem Befehl `az storage fs directory create`. 

In diesem Beispiel wird dem Container `my-file-system`, der sich in einem Konto namens `mystorageaccount` befindet, ein Verzeichnis namens `my-directory` hinzugefügt.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Anzeigen von Verzeichniseigenschaften

Sie können die Eigenschaften eines Verzeichnisses auf der Konsole ausgeben, indem Sie den Befehl `az storage fs directory show` verwenden.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Mit dem Befehl `az storage fs directory move` können Sie ein Verzeichnis umbenennen oder verschieben.

In diesem Beispiel wird ein Verzeichnis innerhalb des gleichen Containers von `my-directory` in `my-new-directory` umbenannt.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird ein Verzeichnis in den Container `my-second-file-system` verschoben.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Löschen Sie ein Verzeichnis mit dem Befehl `az storage fs directory delete`.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Überprüfen, ob ein Verzeichnis vorhanden ist

Ermitteln Sie mit dem Befehl `az storage fs directory exists`, ob ein bestimmtes Verzeichnis im Container vorhanden ist.

Dieses Beispiel zeigt, ob im Container `my-file-system` ein Verzeichnis mit dem Namen `my-directory` vorhanden ist. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis

Laden Sie eine Datei mit dem Befehl `az storage fs file download` aus einem Verzeichnis herunter.

In diesem Beispiel wird eine Datei mit dem Namen `upload.txt` aus einem Verzeichnis mit dem Namen `my-directory` heruntergeladen. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Listen Sie den Inhalt eines Verzeichnisses mit dem Befehl `az storage fs file list` auf.

In diesem Beispiel wird der Inhalt eines Verzeichnisses namens `my-directory` aufgelistet, das sich im Container `my-file-system` eines Speicherkontos mit dem Namen `mystorageaccount` befindet. 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Laden Sie eine Datei mit dem Befehl `az storage fs directory upload` in ein Verzeichnis hoch.

In diesem Beispiel wird die Datei `upload.txt` in das Verzeichnis `my-directory` hochgeladen. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Anzeigen von Dateieigenschaften

Sie können die Eigenschaften einer Datei mit dem Befehl `az storage fs file show` auf der Konsole ausgeben.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Umbenennen oder Verschieben einer Datei

Mit dem Befehl `az storage fs file move` können Sie eine Datei umbenennen oder verschieben.

In diesem Beispiel wird eine Datei mit dem Namen `my-file.txt` in `my-file-renamed.txt` umbenannt.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Löschen von Dateien

Löschen Sie eine Datei mit dem Befehl `az storage fs file delete`.

In diesem Beispiel wird eine Datei namens `my-file.txt` gelöscht.

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-access-control-lists-acls"></a>Verwalten von Zugriffssteuerungslisten (Access Control Lists, ACLs)

Sie können Zugriffsberechtigungen für Verzeichnisse und Dateien abrufen, festlegen und aktualisieren.

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um Befehle zu autorisieren, stellen Sie sicher, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="get-an-acl"></a>Abrufen einer Zugriffssteuerungsliste

Ermitteln Sie die Zugriffssteuerungsliste (ACL) eines **Verzeichnisses** mit dem Befehl `az storage fs access show`.

In diesem Beispiel wird die Zugriffssteuerungsliste eines Verzeichnisses abgerufen und dann auf der Konsole ausgegeben.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Rufen Sie die Zugriffsberechtigungen einer **Datei** mit dem Befehl `az storage fs access show` ab. 

In diesem Beispiel wird die Zugriffssteuerungsliste einer Datei abgerufen und dann auf der Konsole ausgegeben.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Die folgende Abbildung zeigt die Ausgabe nach dem Abrufen der Zugriffssteuerungsliste eines Verzeichnisses.

![Abrufen der Ausgabe der Zugriffssteuerungsliste](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In diesem Beispiel verfügt der zuständige Benutzer über Berechtigungen zum Lesen, Schreiben und Ausführen. Die zuständige Gruppe verfügt lediglich über Berechtigungen zum Lesen und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Festlegen einer Zugriffssteuerungsliste

Verwenden Sie den Befehl `az storage fs access set`, um die Zugriffssteuerungsliste eines **Verzeichnisses** festzulegen. 

In diesem Beispiel wird die Zugriffssteuerungsliste auf ein Verzeichnis für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann auf der Konsole ausgegeben.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird die *Standard*-Zugriffssteuerungsliste auf ein Verzeichnis für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann in der Konsole ausgegeben.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Verwenden Sie den Befehl `az storage fs access set`, um die Zugriffssteuerungsliste einer **Datei** festzulegen. 

In diesem Beispiel wird die Zugriffssteuerungsliste auf eine Datei für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und anschließend auf der Konsole ausgegeben.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Die folgende Abbildung zeigt die Ausgabe nach dem Festlegen der Zugriffssteuerungsliste einer Datei.

![Abrufen der Ausgabe der Zugriffssteuerungsliste 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In diesem Beispiel verfügen der zuständige Benutzer und die zuständige Gruppe nur über Berechtigungen zum Lesen und Schreiben. Alle anderen Benutzer verfügen über Berechtigungen zum Schreiben und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Aktualisieren einer Zugriffssteuerungsliste (ACL)

Eine weitere Möglichkeit, diese Berechtigung festzulegen, ist die Verwendung des Befehls `az storage fs access set`. 

Aktualisieren Sie die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei, indem Sie den Parameter `-permissions` auf die Kurzform einer Zugriffssteuerungsliste festlegen.

In diesem Beispiel wird die Zugriffssteuerungsliste eines **Verzeichnisses** aktualisiert.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird die Zugriffssteuerungsliste einer **Datei** aktualisiert.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Sie können den zuständigen Benutzer und die Gruppe eines Verzeichnisses oder einer Datei auch aktualisieren, indem Sie die Parameter `--owner` oder `group` auf die Entitäts-ID oder den Benutzerprinzipalnamen (UPN) eines Benutzers festlegen. 

In diesem Beispiel wird der Besitzer eines Verzeichnisses geändert. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird der Besitzer einer Datei geändert. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="set-an-acl-recursively"></a>Rekursives Festlegen einer Zugriffssteuerungsliste

Sie können Zugriffssteuerungslisten rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen. Weitere Informationen finden Sie unter [Festlegen von Zugriffssteuerungslisten (ACLs) für Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Weitere Informationen

* [Beispiele](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
* [Geben Sie uns Feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)