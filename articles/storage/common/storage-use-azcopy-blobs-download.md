---
title: Herunterladen von Blobs aus Azure Blob Storage mithilfe AzCopy v10 | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Sammlung von AzCopy-Beispielbefehlen, die Sie beim Herunterladen von Blobs aus Azure Blob Storage unterstützen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f09e30d6bf68cfb11d9bf808838f6cc029ed942a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907441"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Herunterladen von Blobs aus Azure Blob Storage mithilfe AzCopy v10

Mit dem Befehlszeilenprogramm AzCopy v10 können Sie Blobs und Verzeichnisse aus Blob Storage herunterladen. 

Beispiele für andere Arten von Aufgaben, z. B. das Hochladen von Dateien, das Synchronisieren mit Blob Storage oder das Kopieren von Blobs zwischen Konten, finden Sie unter den Links im Abschnitt [Nächste Schritte](#next-steps) dieses Artikels.

## <a name="get-started"></a>Erste Schritte

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu erfahren, wie Sie dem Speicherdienst Autorisierungsanmeldeinformationen bereitstellen können.

> [!NOTE] 
> In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Anmeldeinformationen für Autorisierung mithilfe von Azure Active Directory (Azure AD) bereitgestellt haben.
>
> Wenn Sie lieber ein SAS-Token für die Autorisierung des Zugriffs auf Blobdaten verwenden möchten, können Sie dieses Token in jedem AzCopy-Befehl an die Ressourcen-URL anfügen. Beispiel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Herunterladen eines Blobs

Laden Sie ein Blob mithilfe des Befehls [azcopy copy](storage-ref-azcopy-copy.md) herunter.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Beispiel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Wenn der `Content-md5`-Eigenschaftswert eines Blobs einen Hash enthält, berechnet AzCopy einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der `Content-md5`-Eigenschaft des Blobs gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt. Wenn diese Werte nicht übereinstimmen, wird der Download nicht durchgeführt, es sei denn, Sie überschreiben dieses Verhalten, indem Sie `--check-md5=NoCheck` oder `--check-md5=LogOnly` an den Kopierbefehl anfügen.

## <a name="download-a-directory"></a>Herunterladen eines Verzeichnisses

Laden Sie ein Verzeichnis mithilfe des Befehls [azcopy copy](storage-ref-azcopy-copy.md) herunter.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Beispiel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

In diesem Beispiel wird das Verzeichnis `C:\myDirectory\myBlobDirectory` erstellt, das alle heruntergeladenen Blobs enthält.

## <a name="download-directory-contents"></a>Herunterladen von Verzeichnisinhalten

Mithilfe des Platzhaltersymbols (*) können Sie die Inhalte eines Verzeichnisses herunterladen, ohne das Verzeichnis selbst zu kopieren.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

> [!NOTE]
> Dieses Szenario wird derzeit nur für Konten unterstützt, die keinen hierarchischen Namespace besitzen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Beispiel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Fügen Sie das Flag `--recursive` an, um Dateien in allen Unterverzeichnissen herunterzuladen.

## <a name="download-specific-blobs"></a>Herunterladen bestimmter Blobs

Sie können bestimmte Blobs mithilfe von vollständigen Dateinamen, partiellen Namen mit Platzhalterzeichen (*) oder mithilfe von Datums- und Uhrzeitwerten herunterladen. 

> [!TIP]
> In diesen Beispielen werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

#### <a name="specify-multiple-complete-blob-names"></a>Angeben mehrerer vollständiger Blobnamen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-path`. Trennen Sie einzelne Blobnamen durch ein Semikolon (`;`).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Beispiel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

In diesem Beispiel überträgt AzCopy das Verzeichnis `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` und die Datei `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt`. Fügen Sie die Option `--recursive` ein, um alle Blobs im Verzeichnis `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` zu übertragen.

Mit der Option `--exclude-path` können Sie Dateien auch ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

#### <a name="use-wildcard-characters"></a>Verwenden von Platzhalterzeichen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-pattern`. Geben Sie Namen mithilfe von Platzhalterzeichen teilweise an. Trennen Sie die Namen durch Semikolons (`;`).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Beispiel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Mit der Option `--exclude-pattern` können Sie Dateien auch ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

Die Optionen `--include-pattern` und `--exclude-pattern` gelten nur für Blobnamen und nicht für den Pfad.  Wenn Sie alle Textdateien (Blobs) in einer Verzeichnisstruktur kopieren möchten, verwenden Sie die Option `–recursive` zum Abrufen der gesamten Verzeichnisstruktur. Verwenden Sie dann `–include-pattern`, und geben Sie `*.txt` an, um alle Textdateien abzurufen.

#### <a name="download-blobs-that-were-modified-after-a-date-and-time"></a>Herunterladen von Blobs, die nach einem bestimmten Datum und einer bestimmten Uhrzeit geändert wurden 

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-after`. Geben Sie ein Datum und eine Uhrzeit im ISO 8601-Format an (z. B. `2020-08-19T15:04:00Z`). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Beispiel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Ausführliche Informationen finden Sie in den [azcopy copy](storage-ref-azcopy-copy.md)-Referenzdokumenten.

#### <a name="download-previous-versions-of-a-blob"></a>Herunterladen früherer Versionen eines Blobs

Wenn Sie die [Blobversionsverwaltung](../blobs/versioning-enable.md) aktiviert haben, können Sie frühere Versionen eines Blobs herunterladen. 

Erstellen Sie zunächst eine Textdatei, die eine Liste mit [Versions-IDs](../blobs/versioning-overview.md) enthält. Jede Versions-ID muss in einer eigenen Zeile stehen. Beispiel: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Verwenden Sie dann den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--list-of-versions`. Geben Sie den Speicherort der Textdatei mit der Liste der Versionen an (z. B. `D:\\list-of-versions.txt`).  

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Beispiel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

Der Name jeder heruntergeladenen Datei beginnt mit der Versions-ID, gefolgt vom Namen des Blobs. 

## <a name="download-with-optional-flags"></a>Herunterladen mit optionalen Flags

Sie können den Downloadvorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.

|Szenario|Flag|
|---|---|
|Automatisches Dekomprimieren von Dateien|**DECOMPRESS**|
|Geben Sie an, wie detailliert die kopierbezogenen Protokolleinträge sein sollen.|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
|Geben Sie an, ob und wie die in Konflikt stehenden Dateien und Blobs im Ziel überschrieben werden sollen.|**--overwrite**=\[true\|false\|ifSourceNewer\|prompt\]|

Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in diesen Artikeln:

- [Beispiele: Hochladen](storage-use-azcopy-blobs-upload.md)
- [Beispiele: Kopieren zwischen Konten](storage-use-azcopy-blobs-copy.md)
- [Beispiele: Synchronisieren](storage-use-azcopy-blobs-synchronize.md)
- [Beispiele: Amazon S3-Buckets](storage-use-azcopy-s3.md)
- [Beispiele: Azure Files](storage-use-azcopy-files.md)
- [Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)