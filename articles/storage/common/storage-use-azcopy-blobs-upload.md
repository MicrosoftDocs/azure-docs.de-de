---
title: Hochladen von Dateien in Azure Blob Storage mithilfe von AzCopy v10 | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Sammlung von AzCopy-Beispielbefehlen, die Sie beim Hochladen von Dateien in Azure Blob Storage unterstützen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 11d40805cda2ea2e3693c6c93034ae19f1f0fcc0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907398"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>Hochladen von Dateien in Azure Blob Storage mithilfe von AzCopy v10

Mit dem Befehlszeilenprogramm AzCopy v10 können Sie Dateien und Verzeichnisse in Blob Storage hochladen. 

Beispiele für andere Arten von Aufgaben, z. B. das Herunterladen von Dateien, das Synchronisieren mit Blob Storage oder das Kopieren von Blobs zwischen Konten, finden Sie unter den Links im Abschnitt [Nächste Schritte](#next-steps) dieses Artikels.

## <a name="get-started"></a>Erste Schritte

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu erfahren, wie Sie dem Speicherdienst Autorisierungsanmeldeinformationen bereitstellen können.

> [!NOTE] 
> In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Anmeldeinformationen für Autorisierung mithilfe von Azure Active Directory (Azure AD) bereitgestellt haben.
>
> Wenn Sie lieber ein SAS-Token für die Autorisierung des Zugriffs auf Blobdaten verwenden möchten, können Sie dieses Token in jedem AzCopy-Befehl an die Ressourcen-URL anfügen. Beispiel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Erstellen eines Containers

Sie können den Befehl [azcopy make](storage-ref-azcopy-make.md) verwenden, um einen Container zu erstellen.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Beispiel** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Ausführliche Referenzdokumente finden Sie unter [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Hochladen einer Datei

Laden Sie eine Datei mithilfe des Befehls [azcopy copy](storage-ref-azcopy-copy.md) hoch.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Beispiel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Sie können eine Datei auch mit einem Platzhaltersymbol (*) an einer beliebigen Stelle im Dateipfad oder Dateinamen hochladen. Beispiel: `'C:\myDirectory\*.txt'` oder `C:\my*\*.txt`

## <a name="upload-a-directory"></a>Hochladen eines Verzeichnisses

Laden Sie ein Verzeichnis mithilfe des Befehls [azcopy copy](storage-ref-azcopy-copy.md) hoch. 

Dieses Beispiel kopiert ein Verzeichnis (sowie alle in diesem Verzeichnis enthaltenen Dateien) in einen Blobcontainer. Das Resultat ist ein gleichnamiges Verzeichnis im Container.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Beispiel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Um ein Verzeichnis innerhalb des Containers zu kopieren, geben Sie einfach den Namen des Verzeichnisses in Ihrer Befehlszeichenfolge an.

|    |     |
|--------|-----------|
| **Beispiel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Wenn Sie den Namen eines Verzeichnisses angeben, das im Container nicht vorhanden ist, erstellt AzCopy ein neues Verzeichnis dieses Namens.

## <a name="upload-directory-contents"></a>Hochladen von Verzeichnisinhalten

Laden Sie den Inhalt eines Verzeichnisses mithilfe des Befehls [azcopy copy](storage-ref-azcopy-copy.md) hoch. Verwenden Sie das Platzhaltersymbol (*) zum Hochladen des Inhalts, ohne das enthaltende Verzeichnis selbst zu kopieren.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Beispiel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


Fügen Sie das Flag `--recursive` an, um Dateien in allen Unterverzeichnissen hochzuladen.

## <a name="upload-specific-files"></a>Hochladen bestimmter Dateien

Sie können bestimmte Dateien mithilfe von vollständigen Dateinamen, partiellen Namen mit Platzhalterzeichen (*) oder mithilfe von Datums- und Uhrzeitwerten hochladen.

> [!TIP]
> In diesen Beispielen werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

### <a name="specify-multiple-complete-file-names"></a>Angeben mehrerer vollständiger Dateinamen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-path`. Trennen Sie einzelne Dateinamen durch ein Semikolon (`;`).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Beispiel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

In diesem Beispiel überträgt AzCopy das Verzeichnis `C:\myDirectory\photos` und die Datei `C:\myDirectory\documents\myFile.txt`. Fügen Sie die Option `--recursive` ein, um alle Dateien im Verzeichnis `C:\myDirectory\photos` zu übertragen.

Mit der Option `--exclude-path` können Sie auch Dateien ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

### <a name="use-wildcard-characters"></a>Verwenden von Platzhalterzeichen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-pattern`. Geben Sie Namen mithilfe von Platzhalterzeichen teilweise an. Trennen Sie die Namen durch Semikolons (`;`). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Beispiel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Mit der Option `--exclude-pattern` können Sie auch Dateien ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

Die Optionen `--include-pattern` und `--exclude-pattern` gelten nur für Dateinamen und nicht für den Pfad.  Wenn Sie alle Textdateien in einer Verzeichnisstruktur kopieren möchten, verwenden Sie die Option `–recursive`, um die gesamte Verzeichnisstruktur abzurufen. Verwenden Sie dann `–include-pattern`, und geben Sie `*.txt` an, um alle Textdateien abzurufen.

### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Hochladen von Dateien, die nach einem bestimmten Datum und einer bestimmten Uhrzeit geändert wurden 

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-after`. Geben Sie ein Datum und eine Uhrzeit im ISO 8601-Format an (z. B. `2020-08-19T15:04:00Z`). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Beispiel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Ausführliche Informationen finden Sie in den [azcopy copy](storage-ref-azcopy-copy.md)-Referenzdokumenten.

## <a name="upload-with-index-tags"></a>Hochladen mit Indextags

Sie können eine Datei hochladen und dem Zielblob [Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md) hinzufügen.  

Wenn Sie Azure AD-Autorisierung verwenden, muss Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen werden, oder ihm muss über eine benutzerdefinierte Azure-Rolle die Berechtigung für `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Vorgänge für Azure-Ressourcenanbieter](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) erteilt werden. Wenn Sie ein SAS-Token (Shared Access Signature) verwenden, muss es über die SAS-Berechtigung `t` Zugriff auf die Blobtags bieten.

Verwenden Sie zum Hinzufügen von Tags die Option `--blob-tags` zusammen mit einem URL-codierten Schlüssel-Wert-Paar. Wenn Sie beispielsweise den Schlüssel `my tag` und den Wert `my tag value` hinzufügen möchten, würden Sie dem Zielparameter `--blob-tags='my%20tag=my%20tag%20value'` hinzufügen. 

Trennen Sie mehrere Indextags durch ein kaufmännisches Und-Zeichen (`&`).  Wenn Sie beispielsweise den Schlüssel `my second tag` und den Wert `my second tag value` hinzufügen möchten, wäre die gesamte Optionszeichenfolge `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`.

In den folgenden Beispielen wird die Verwendung der Option `--blob-tags` gezeigt.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Hochladen einer Datei** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Hochladen eines Verzeichnisses** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Hochladen von Verzeichnisinhalten** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Wenn Sie ein Verzeichnis für die Quelle angeben, haben alle in das Ziel kopierten Blobs dieselben Tags, die Sie im Befehl angeben.

## <a name="upload-with-optional-flags"></a>Hochladen mit optionalen Flags

Sie können den Uploadvorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.

|Szenario|Flag|
|---|---|
|Dateien sollen als Anfügeblobs oder Seitenblobs hochgeladen werden.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|Hochladen auf eine bestimmte Zugriffsebene (z. B. die Archivebene)|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|

Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in diesen Artikeln:

- [Beispiele: Herunterladen](storage-use-azcopy-blobs-download.md)
- [Beispiele: Kopieren zwischen Konten](storage-use-azcopy-blobs-copy.md)
- [Beispiele: Synchronisieren](storage-use-azcopy-blobs-synchronize.md)
- [Beispiele: Amazon S3-Buckets](storage-use-azcopy-s3.md)
- [Beispiele: Azure Files](storage-use-azcopy-files.md)
- [Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)