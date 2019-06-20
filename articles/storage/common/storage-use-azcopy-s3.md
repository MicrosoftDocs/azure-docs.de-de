---
title: Übertragen von Daten in Azure Storage aus Amazon S3-Buckets mit AzCopy v10 | Microsoft-Dokumentation
description: Übertragen von Daten mit AzCopy und Amazon S3-Buckets
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0bc74ac0fe45f2502064340a0c3ce5b82694b06
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245275"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Kopieren von Daten aus Amazon S3-Buckets mit AzCopy

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel enthält Informationen zum Kopieren von Objekten, Ordnern und Buckets aus Amazon Web Services (AWS) S3 in Azure Blob Storage mit AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Auswählen der Angabe von Anmeldeinformationen für die Autorisierung

* Nutzen Sie für die Azure Storage-Autorisierung Azure Active Directory (AD) oder ein SAS-Token (Shared Access Signature).

* Verwenden Sie für die Autorisierung mit AWS S3 einen AWS-Zugriffsschlüssel und einen geheimen Zugriffsschlüssel.

### <a name="authorize-with-azure-storage"></a>Autorisieren mit Azure Storage

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und auszuwählen, wie Sie für den Speicherdienst Autorisierungsanmeldeinformationen bereitstellen können.

> [!NOTE]
> Bei den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Ihre Identität mithilfe des `AzCopy login`-Befehls authentifiziert haben. AzCopy verwendet Ihr Azure AD-Konto, um den Zugriff auf Daten in Blob Storage zu autorisieren.
>
> Wenn Sie lieber ein SAS-Token für die Autorisierung des Zugriffs auf Blobdaten verwenden möchten, können Sie dieses Token in jedem AzCopy-Befehl an die Ressourcen-URL anfügen.
>
> Beispiel: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorisieren mit AWS S3

Halten Sie Ihren AWS-Zugriffsschlüssel und geheimen Zugriffsschlüssel bereit, und legen Sie dann diese Umgebungsvariablen fest:

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-folders-and-buckets"></a>Kopieren von Objekten, Ordnern und Buckets

Da für AzCopy die [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)-API verwendet wird, werden Daten direkt zwischen AWS S3 und Speicherservern kopiert. Für diese Kopiervorgänge wird nicht die Netzwerkbandbreite Ihres Computers genutzt.

### <a name="copy-an-object"></a>Kopieren eines Objekts

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Beispiel** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> In den Beispielen in diesem Artikel werden URLs im Pfadstil für AWS S3-Buckets verwendet (z. B. `http://s3.amazonaws.com/<bucket-name>`). 
>
> Sie können auch virtuelle URLs im Hostingstil nutzen (z. B. `http://bucket.s3.amazonaws.com`). 
>
> Weitere Informationen zum virtuellen Hosten von Buckets finden Sie unter [Virtual Hosting of Buckets]](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) (Virtuelles Hosten von Buckets).

### <a name="copy-a-folder"></a>Kopieren eines Ordners

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<folder-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<folder-name>" --recursive=true` |
| **Beispiel** | `azcopy cp "https://s3.amazonaws.com/mybucket/myfolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myfolder" --recursive=true` |

### <a name="copy-a-bucket"></a>Kopieren eines Buckets

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Beispiel** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopieren aller Buckets in allen Regionen

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Beispiel** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopieren aller Buckets in einer bestimmten S3-Region

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Beispiel** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Unterschiede in Benennungsregeln für Objekte

AWS S3 verfügt über andere Namenskonventionen für Bucketnamen als Azure-Blobcontainer. Informationen zu diesem Thema finden Sie [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Wenn Sie die Entscheidung treffen, eine Gruppe mit Buckets in ein Azure-Speicherkonto zu kopieren, tritt für den Kopiervorgang ggf. ein Fehler aufgrund von Unterschieden bei der Benennung auf.

Mit AzCopy werden zwei der häufigsten Probleme behandelt, die auftreten können: Buckets, die Zeiträume enthalten, und Buckets, die aufeinander folgende Bindestriche enthalten. Namen von AWS S3-Buckets können Zeiträume und aufeinander folgende Bindestriche enthalten, aber für einen Container in Azure ist dies nicht möglich. Mit AzCopy werden Zeiträume durch Bindestriche und aufeinander folgende Bindestriche durch eine Zahl ersetzt, die für die Anzahl von aufeinander folgenden Bindestriche steht (Beispiel: ein Bucket mit dem Namen `my----bucket` wird zu `my-4-bucket`. 

Wenn mit AzCopy Dateien kopiert werden, wird auch eine Überprüfung auf Namenskonflikte durchgeführt und versucht, diese zu lösen. Falls beispielsweise Buckets mit den Namen `bucket-name` und `bucket.name` vorhanden sind, löst AzCopy einen Bucket mit dem Namen `bucket.name` zuerst in `bucket-name` und dann in `bucket-name-2` auf.

## <a name="handle-differences-in-object-metadata"></a>Behandeln von Unterschieden in Objektmetadaten

Bei AWS S3 und Azure sind in den Namen von Objektschlüsseln unterschiedliche Zeichen zulässig. Informationen zu den Zeichen, die in AWS S3 genutzt werden können, finden Sie [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Auf Azure-Seite unterliegen Blobobjektschlüssel den Benennungsregeln für [C#-Bezeichner](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Im Rahmen des AzCopy-Befehls `copy` können Sie einen Wert für das optionale Flag `s2s-invalid-metadata-handle` angeben. Hiermit wird festgelegt, wie Dateien verarbeitet werden sollen, bei denen die Metadaten der Datei inkompatible Schlüsselnamen enthalten. In der folgenden Tabelle werden die einzelnen Flagwerte beschrieben.

| Flagwert | BESCHREIBUNG  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standardoption) Die Metadaten sind nicht im übertragenen Objekt enthalten. AzCopy protokolliert eine Warnung. |
| **FailIfInvalid** | Objekte werden nicht kopiert. AzCopy protokolliert einen Fehler und fügt diese Fehler der Fehleranzahl hinzu, die in der Zusammenfassung der Übertragung angezeigt wird.  |
| **RenameIfInvalid**  | AzCopy löst den ungültigen Metadatenschlüssel auf und kopiert das Objekt in Azure, indem das aufgelöste Schlüssel-Wert-Paar aus den Metadaten verwendet wird. Informationen zu den genauen Schritten, die von AzCopy zum Umbenennen von Objektschlüsseln ausgeführt werden, finden Sie unten im Abschnitt [Umbenennen von Objektschlüsseln durch AzCopy](#rename-logic). Falls der Schlüssel von AzCopy nicht umbenannt werden kann, wird das Objekt nicht kopiert. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Umbenennen von Objektschlüsseln durch AzCopy

AzCopy führt die folgenden Schritte aus:

1. Ersetzt ungültige Zeichen durch „_“.

2. Fügt am Anfang eines neuen gültigen Schlüssels die Zeichenfolge `rename_` hinzu.

   Dieser Schlüssel wird verwendet, um den ursprünglichen **Wert** aus den Metadaten zu speichern.

3. Fügt am Anfang eines neuen gültigen Schlüssels die Zeichenfolge `rename_key_` hinzu.
   Dieser Schlüssel wird verwendet, um den ursprünglichen ungültigen **Schlüssel** aus den Metadaten zu speichern.
   Sie können mit diesem Schlüssel versuchen, die Metadaten auf Azure-Seite wiederherzustellen, da der Metadatenschlüssel als Wert im Blobspeicherdienst gespeichert wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in den folgenden Artikeln:

- [Übertragen von Daten mit AzCopy v10](storage-use-azcopy-v10.md)

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Übertragen von Daten mit AzCopy und Blob Storage)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Übertragen von Daten mit AzCopy und Dateispeicher)

- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)