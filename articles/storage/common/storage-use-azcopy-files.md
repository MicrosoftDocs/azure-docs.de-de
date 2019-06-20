---
title: Übertragen von Daten nach oder aus Azure Files mithilfe von AzCopy v10 | Microsoft-Dokumentation
description: Übertragen von Daten mit AzCopy und Dateispeicher.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687935"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Übertragen von Daten mit AzCopy und Dateispeicher 

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel enthält Beispielbefehle, die mit Azure Files funktionieren.

Bevor Sie beginnen, lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und sich mit dem Tool vertraut zu machen.

## <a name="create-file-shares"></a>Erstellen von Dateifreigaben

Sie können den AzCopy-Befehl `make` verwenden, um eine Dateifreigabe zu erstellen. Mit dem Beispiel in diesem Abschnitt wird eine Dateifreigabe namens `myfileshare` erstellt.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Beispiel** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Hochladen von Dateien

Sie können den AzCopy-Befehl `copy` verwenden, um Dateien und Verzeichnisse von Ihrem lokalen Computer hochzuladen.

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Hochladen einer Datei
> * Hochladen eines Verzeichnisses
> * Hochladen von Dateien mithilfe von Platzhalterzeichen

> [!NOTE]
> AzCopy berechnet und speichert den MD5-Hashcode der Datei nicht automatisch. Wenn AzCopy diese Vorgänge ausführen soll, fügen Sie das Flag `--put-md5` an jeden Kopierbefehl an. Auf diese Weise berechnet AzCopy beim Herunterladen der Datei einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der `Content-md5`-Eigenschaft der Datei gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt.

### <a name="upload-a-file"></a>Hochladen einer Datei

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Beispiel** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Hochladen eines Verzeichnisses

Dieses Beispiel kopiert ein Verzeichnis (sowie alle in diesem Verzeichnis enthaltenen Dateien) in eine Dateifreigabe. Das Resultat ist ein gleichnamiges Verzeichnis in der Dateifreigabe.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Beispiel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Um ein Verzeichnis innerhalb der Dateifreigabe zu kopieren, geben Sie einfach den Namen des Verzeichnisses in Ihrer Befehlszeichenfolge an.

|    |     |
|--------|-----------|
| **Beispiel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Wenn Sie den Namen eines Verzeichnisses angeben, das in der Dateifreigabe nicht vorhanden ist, erstellt AzCopy ein neues Verzeichnis dieses Namens.

### <a name="upload-the-contents-of-a-directory"></a>Hochladen der Inhalte eines Verzeichnisses

Mithilfe des Platzhaltersymbols (*) können Sie die Inhalte eines Verzeichnisses hochladen, ohne das Verzeichnis selbst zu kopieren.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Beispiel** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Fügen Sie das Flag `--recursive` an, um Dateien in allen Unterverzeichnissen hochzuladen.

## <a name="download-files"></a>Herunterladen von Dateien

Sie können den AzCopy-Befehl `copy` verwenden, um Dateien, Verzeichnisse und Dateifreigaben auf Ihren lokalen Computer herunterzuladen.

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Herunterladen einer Datei
> * Herunterladen eines Verzeichnisses
> * Herunterladen von Dateien mithilfe von Platzhalterzeichen

> [!NOTE]
> Wenn der `Content-md5`-Eigenschaftswert einer Datei einen Hash enthält, berechnet AzCopy einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der `Content-md5`-Eigenschaft der Datei gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt. Wenn diese Werte nicht übereinstimmen, wird der Download nicht durchgeführt, es sei denn, Sie überschreiben dieses Verhalten, indem Sie `--check-md5=NoCheck` oder `--check-md5=LogOnly` an den Kopierbefehl anfügen.

### <a name="download-a-file"></a>Herunterladen einer Datei

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Beispiel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Herunterladen eines Verzeichnisses

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Beispiel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Dieses Beispiel erstellt ein Verzeichnis namens `C:\myDirectory\myFileShareDirectory`, das alle heruntergeladenen Dateien enthält.

### <a name="download-the-contents-of-a-directory"></a>Herunterladen der Inhalte eines Verzeichnisses

Mithilfe des Platzhaltersymbols (*) können Sie die Inhalte eines Verzeichnisses herunterladen, ohne das Verzeichnis selbst zu kopieren.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Beispiel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Fügen Sie das Flag `--recursive` an, um Dateien in allen Unterverzeichnissen herunterzuladen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in den folgenden Artikeln:

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)

- [Übertragen von Daten mit AzCopy und Blob Storage](storage-use-azcopy-blobs.md)

- [Übertragen von Daten mit AzCopy und Amazon S3-Buckets](storage-use-azcopy-s3.md)

- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)