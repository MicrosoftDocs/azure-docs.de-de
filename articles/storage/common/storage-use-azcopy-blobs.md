---
title: Übertragen von Daten nach oder aus Azure Blob Storage mit AzCopy v10 | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Auflistung von AzCopy Beispiel Befehle, mit denen Sie Container erstellen, Kopieren von Dateien und Verzeichnisse zwischen lokalen Dateisystemen und Containern zu synchronisieren.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 140f2ec6252eac2958f236b2ffb48225fa16fe2b
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688055"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Übertragen von Daten mit AzCopy und Blob Storage

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Daten in ein oder aus einem Speicherkonto zu kopieren. Dieser Artikel enthält Beispielbefehle, die mit Blob Storage funktionieren.

## <a name="get-started"></a>Erste Schritte

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu erfahren, wie Sie dem Speicherdienst Autorisierungsanmeldeinformationen bereitstellen können.

> [!NOTE]
> Bei den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Ihre Identität mithilfe des `AzCopy login`-Befehls authentifiziert haben. AzCopy verwendet Ihr Azure AD-Konto, um den Zugriff auf Daten in Blob Storage zu autorisieren.
>
> Wenn Sie lieber ein SAS-Token für die Autorisierung des Zugriffs auf Blobdaten verwenden möchten, können Sie dieses Token in jedem AzCopy-Befehl an die Ressourcen-URL anfügen.
>
> Beispiel: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Erstellen eines Containers

Sie können den AzCopy-Befehl `make` verwenden, um einen Container zu erstellen. Die Beispiele in diesem Abschnitt erstellen einen Container namens `mycontainer`.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Beispiel** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Beispiel** (hierarchischer Namespace) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Hochladen von Dateien

Sie können AzCopy verwenden `copy` Befehl zum Hochladen von Dateien und Verzeichnissen auf Ihrem lokalen Computer.

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Hochladen einer Datei
> * Ein Verzeichnis hochladen
> * Hochladen von Dateien mithilfe von Platzhalterzeichen

> [!NOTE]
> AzCopy berechnet und speichert den MD5-Hashcode der Datei nicht automatisch. Wenn AzCopy diese Vorgänge ausführen soll, fügen Sie das Flag `--put-md5` an jeden Kopierbefehl an. Auf diese Weise berechnet AzCopy beim Herunterladen des Blobs einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der `Content-md5`-Eigenschaft des Blobs gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt.

### <a name="upload-a-file"></a>Hochladen einer Datei

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Beispiel** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy lädt Daten standardmäßig in Blockblobs hoch. Um Dateien als Anfügeblobs oder Seitenblobs hochzuladen, verwenden Sie das Flag `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Ein Verzeichnis hochladen

In diesem Beispiel kopiert ein Verzeichnis (und alle Dateien in diesem Verzeichnis), in einen Blob-Container. Das Ergebnis ist ein Verzeichnis im Container mit demselben Namen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Beispiel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Um zu einem Verzeichnis innerhalb des Containers zu kopieren, geben Sie einfach der Name dieses Verzeichnisses, in der Befehlszeichenfolge.

|    |     |
|--------|-----------|
| **Beispiel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Wenn Sie den Namen eines Verzeichnisses, die in den Container nicht vorhanden ist angeben, erstellt AzCopy ein neues Verzeichnis mit diesem Namen.

### <a name="upload-the-contents-of-a-directory"></a>Hochladen der Inhalte eines Verzeichnisses

Sie können den Inhalt eines Verzeichnisses hochladen, ohne kopieren enthaltenden Verzeichnisses selbst mit dem Platzhaltersymbol (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Beispiel** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Fügen Sie der `--recursive` Flag zum Hochladen von Dateien in allen untergeordneten Verzeichnissen.

## <a name="download-files"></a>Herunterladen von Dateien

Sie können AzCopy verwenden `copy` Befehl zum Herunterladen von Blobs, Containern und Verzeichnisse auf Ihrem lokalen Computer.

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Herunterladen einer Datei
> * Laden Sie ein Verzeichnis herunter.
> * Herunterladen von Dateien mithilfe von Platzhalterzeichen

> [!NOTE]
> Wenn der `Content-md5`-Eigenschaftswert eines Blobs einen Hash enthält, berechnet AzCopy einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der `Content-md5`-Eigenschaft des Blobs gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt. Wenn diese Werte nicht übereinstimmen, wird der Download nicht durchgeführt, es sei denn, Sie überschreiben dieses Verhalten, indem Sie `--check-md5=NoCheck` oder `--check-md5=LogOnly` an den Kopierbefehl anfügen.

### <a name="download-a-file"></a>Herunterladen einer Datei

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Beispiel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Laden Sie ein Verzeichnis herunter.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Beispiel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

In diesem Beispiel führt ein Verzeichnis namens `C:\myDirectory\myBlobDirectory` , die alle der heruntergeladenen Dateien enthält.

### <a name="download-the-contents-of-a-directory"></a>Laden Sie den Inhalt eines Verzeichnisses

Sie können den Inhalt eines Verzeichnisses herunterladen, ohne selbst enthaltenden Verzeichnisses mithilfe das Platzhaltersymbol (*) kopieren.

> [!NOTE]
> Dieses Szenario wird derzeit nur für Konten unterstützt, die keinen hierarchischen Namespace besitzen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Beispiel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Fügen Sie der `--recursive` Flag zum Herunterladen von Dateien in allen untergeordneten Verzeichnissen.

## <a name="copy-blobs-between-storage-accounts"></a>Kopieren von Blobs zwischen Speicherkonten

Sie können AzCopy verwenden, um Blobs in andere Speicherkonten zu kopieren. Der Kopiervorgang verläuft synchron – wenn also der Befehl zurückgegeben wird, bedeutet dies, dass alle Dateien kopiert wurden.

> [!NOTE]
> Dieses Szenario wird derzeit nur für Konten unterstützt, die keinen hierarchischen Namespace besitzen.

AzCopy verwendet die [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)-API, daher werden Daten direkt zwischen Speicherservern kopiert. Diese Kopiervorgänge nutzen nicht die Netzwerkbandbreite Ihres Computers.

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Kopieren eines Blobs in ein anderes Speicherkonto
> * Kopieren eines Verzeichnisses in ein anderes Speicherkonto
> * Kopieren eines Containers in ein anderes Speicherkonto
> * Kopieren Sie alle Container, Verzeichnisse und Dateien in ein anderes Speicherkonto

### <a name="copy-a-blob-to-another-storage-account"></a>Kopieren eines Blobs in ein anderes Speicherkonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Beispiel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopieren eines Verzeichnisses in ein anderes Speicherkonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Beispiel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopieren eines Containers in ein anderes Speicherkonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Beispiel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Kopieren Sie alle Container, Verzeichnisse und Dateien in ein anderes Speicherkonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Beispiel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchronisieren von Dateien

Sie können die Inhalte eines lokalen Dateisystems mit einem Blobcontainer synchronisieren. Sie können auch einen Blobcontainer mit einem lokalen Dateisystem auf Ihrem Computer synchronisieren. Die Synchronisierung erfolgt unidirektional. Anders gesagt: Sie wählen aus, welcher der beiden Endpunkte die Quelle und welcher das Ziel ist.

> [!NOTE]
> Das aktuelle Release von AzCopy synchronisiert nicht zwischen anderen Quellen und Zielen (beispielsweise: Dateispeicher oder Amazon Web Services (AWS) S3-Buckets).

Der Befehl `sync` vergleicht Dateinamen und die Zeitstempel der letzten Änderung. Legen Sie die `--delete-destination` optionales Kennzeichen auf einen Wert von `true` oder `prompt` zum Löschen von Dateien im Zielverzeichnis, wenn diese Dateien nicht mehr im Quellverzeichnis vorhanden sind.

Wenn Sie das `--delete-destination`-Flag auf `true` festlegen, löscht AzCopy Dateien, ohne zur Bestätigung aufzufordern. Wenn eine Bestätigungsaufforderung angezeigt werden soll, bevor AzCopy eine Datei löscht, legen Sie das `--delete-destination`-Flag auf `prompt` fest.

> [!NOTE]
> Um ein versehentliches Löschen zu verhindern, aktivieren Sie das Feature [Vorläufiges Löschen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete), bevor Sie das Flag `--delete-destination=prompt|true` verwenden.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Synchronisieren eines Containers mit einem lokalen Dateisystem

In diesem Fall ist das lokale Dateisystem die Quelle, und der Container ist das Ziel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Beispiel** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy sync "C:\myDirectory" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Synchronisieren eines lokalen Dateisystem mit einem Container

In diesem Fall ist der Container die Quelle, und das lokale Dateisystem ist das Ziel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Beispiel** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
| **Beispiel** (hierarchischer Namespace) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in den folgenden Artikeln:

- [Übertragen von Daten mit AzCopy v10](storage-use-azcopy-v10.md)

- [Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)

- [Kopieren von Daten aus Amazon S3-Buckets mit AzCopy](storage-use-azcopy-s3.md)

- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)