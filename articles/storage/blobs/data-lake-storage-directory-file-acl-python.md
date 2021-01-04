---
title: Azure Data Lake Storage Gen2 Python SDK für Dateien und Zugriffssteuerungslisten
description: Verwenden Sie Python zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten (ACLs) in Speicherkonten, bei denen der hierarchische Namespace (HNS) aktiviert wurde.
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 7bbdf7961a934245b71829b7b50fc62c5b069d6b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913282"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von Python zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von Python Verzeichnisse, Dateien und Berechtigungen in Speicherkonten erstellen und verwalten, bei denen der hierarchische Namespace (HNS) aktiviert wurde. 

[Paket (Python-Paketindex)](https://pypi.org/project/azure-storage-file-datalake/) | [Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API-Referenz](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Feedback geben](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
> * Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.

## <a name="set-up-your-project"></a>Einrichten des Projekts

Installieren Sie die Azure Data Lake Storage-Clientbibliothek für Python mithilfe von [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Fügen Sie diese Importanweisungen am Anfang Ihrer Codedatei hinzu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine **DataLakeServiceClient**-Instanz erstellen, die das Speicherkonto darstellt. 

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen. 

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe eines Kontoschlüssels erstellt.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Ersetzen Sie den Platzhalterwert `storage_account_name` durch den Namen Ihres Speicherkontos.

- Ersetzen Sie den Platzhalterwert `storage_account_key` durch den Zugriffsschlüssel Ihres Speicherkontos.

### <a name="connect-by-using-azure-active-directory-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (AD)

Sie können die [Azure-Identitätsclientbibliothek für Python](https://pypi.org/project/azure-identity/) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.  Informationen zum Abrufen dieser Werte finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für Python](https://pypi.org/project/azure-identity/).

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Sie können eines erstellen, indem Sie die Methode **FileSystemDataLakeServiceClient.create_file_system** aufrufen.

In diesem Beispiel wird ein Container namens `my-file-system` erstellt.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie eine Verzeichnisreferenz, indem Sie die Methode **FileSystemClient.create_directory** aufrufen.

In diesem Beispiel wird einem Container das Verzeichnis `my-directory` hinzugefügt. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Rufen Sie die Methode **DataLakeDirectoryClient.rename_directory** auf, um ein Verzeichnis umzubenennen oder zu verschieben. Übergeben Sie den Pfad des gewünschten Verzeichnisses als Parameter. 

Im folgenden Beispiel wird ein Unterverzeichnis in `my-subdirectory-renamed` umbenannt.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Sie können ein Verzeichnis löschen, indem Sie die Methode **DataLakeDirectoryClient.delete_directory** aufrufen.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```


## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis 

Erstellen Sie zunächst eine Dateireferenz im Zielverzeichnis, in dem Sie eine Instanz der Klasse **DataLakeFileClient** erstellen. Laden Sie eine Datei hoch, indem Sie die Methode **DataLakeFileClient.append_data** aufrufen. Stellen Sie sicher, dass Sie den Uploadvorgang durch Aufrufen der Methode **DataLakeFileClient.flush_data** abschließen.

In diesem Beispiel wird eine Textdatei in das Verzeichnis `my-directory` hochgeladen.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Wenn Ihre Datei groß ist, muss Ihr Code die **DataLakeFileClient.append_data**-Methode mehrmals aufrufen. Erwägen Sie stattdessen die **DataLakeFileClient.upload_data**-Methode. Auf diese Weise können Sie die gesamte Datei in einem einzigen Aufruf hochladen. 

## <a name="upload-a-large-file-to-a-directory"></a>Hochladen einer großen Datei in ein Verzeichnis

Verwenden Sie die **DataLakeFileClient.upload_data**-Methode, um große Dateien hochzuladen, ohne die **DataLakeFileClient.append_data**-Methode mehrfach aufrufen zu müssen.

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis 

Öffnen Sie eine lokale Datei zum Schreiben. Erstellen Sie dann eine **DataLakeFileClient**-Instanz, die die herunterzuladende Datei darstellt. Rufen Sie die Methode **DataLakeFileClient.read_file** auf, um Bytes aus der Datei zu lesen und anschließend in die lokale Datei zu schreiben. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Listen Sie den Verzeichnisinhalt auf, indem Sie die Methode **FileSystemClient.get_paths** aufrufen und die Ergebnisse anschließend durchlaufen.

In diesem Beispiel wird der Pfad der einzelnen Unterverzeichnisse und Dateien gedruckt, die im Verzeichnis `my-directory` gespeichert sind.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="manage-access-control-lists-acls"></a>Verwalten von Zugriffssteuerungslisten (Access Control Lists, ACLs)

Sie können Zugriffsberechtigungen für Verzeichnisse und Dateien abrufen, festlegen und aktualisieren.

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um Zugriff zu autorisieren, stellen Sie sicher, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-directory-acls"></a>Verwalten von Verzeichnis-ACLs

Rufen Sie die Zugriffssteuerungsliste (Access Control List, ACL) eines Verzeichnisses ab, indem Sie die Methode **DataLakeDirectoryClient.get_access_control** aufrufen und die ACL durch Aufrufen der Methode **DataLakeDirectoryClient.set_access_control** festlegen.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, der in der Anwendung zum Autorisieren des Zugriffs verwendet wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

In diesem Beispiel wird die ACL des Verzeichnisses `my-directory` abgerufen und dann festgelegt. Mit der Zeichenfolge `rwxr-xrw-` werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lese- und Schreibzugriff gewährt wird.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

Sie können auch die ACL des Stammverzeichnisses eines Containers abrufen und festlegen. Um das Stammverzeichnis abzurufen, rufen Sie die **FileSystemClient._get_root_directory_client**-Methode auf.

### <a name="manage-file-permissions"></a>Verwalten von Dateiberechtigungen

Rufen Sie die Zugriffssteuerungsliste (Access Control List, ACL) einer Datei ab, indem Sie die Methode **DataLakeFileClient.get_access_control** aufrufen und die ACL durch Aufrufen der Methode **DataLakeFileClient.set_access_control** festlegen.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, der in der Anwendung zum Autorisieren des Zugriffs verwendet wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Im folgenden Beispiel wird die ACL der Datei `my-file.txt` abgerufen und dann festgelegt. Mit der Zeichenfolge `rwxr-xrw-` werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lese- und Schreibzugriff gewährt wird.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

### <a name="set-an-acl-recursively"></a>Rekursives Festlegen einer Zugriffssteuerungsliste

Sie können Zugriffssteuerungslisten rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen. Weitere Informationen finden Sie unter [Festlegen von Zugriffssteuerungslisten (ACLs) für Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Weitere Informationen

* [API-Referenzdokumentation](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Paket (Python-Paketindex)](https://pypi.org/project/azure-storage-file-datalake/)
* [Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geben](https://github.com/Azure/azure-sdk-for-python/issues)