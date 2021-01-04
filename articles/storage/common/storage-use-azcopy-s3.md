---
title: Kopieren von Daten aus Amazon S3 in Azure Storage mithilfe von AzCopy | Microsoft-Dokumentation
description: Verwenden von AzCopy zum Kopieren von Daten aus Amazon S3 in Azure Storage. AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 07a8d2b394e8ca690925c677af676643064a9ba8
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96901829"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopieren von Daten aus Amazon S3 in Azure Storage mithilfe von AzCopy

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel enthält Informationen zum Kopieren von Objekten, Verzeichnissen und Buckets aus Amazon Web Services (AWS) S3 in Azure Blob Storage mit AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Auswählen, wie Sie die Autorisierungsanmeldeinformationen bereitstellen

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

Halten Sie Ihren AWS-Zugriffsschlüssel und geheimen Zugriffsschlüssel bereit, und legen Sie dann folgende Umgebungsvariablen fest:

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopieren von Objekten, Verzeichnisse und Buckets

Da für AzCopy die [Put Block From URL](/rest/api/storageservices/put-block-from-url)-API verwendet wird, werden Daten direkt zwischen AWS S3 und Speicherservern kopiert. Für diese Kopiervorgänge wird nicht die Netzwerkbandbreite Ihres Computers genutzt.

> [!TIP]
> In den Beispielen in diesem Abschnitt werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

 Diese Beispiele können auch für Konten mit einem hierarchischen Namespace verwendet werden. [Multiprotokollzugriff für Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) ermöglicht es Ihnen, dieselbe URL-Syntax (`blob.core.windows.net`) für diese Konten zu verwenden. 

### <a name="copy-an-object"></a>Kopieren eines Objekts

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Beispiel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Beispiel** (hierarchischer Namespace) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> In den Beispielen in diesem Artikel werden URLs im Pfadstil für AWS S3-Buckets verwendet (z. B. `http://s3.amazonaws.com/<bucket-name>`). 
>
> Sie können auch virtuelle URLs im Hostingstil nutzen (z. B. `http://bucket.s3.amazonaws.com`). 
>
> Weitere Informationen zum virtuellen Hosten von Buckets finden Sie unter [Virtual Hosting of Buckets]](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) (Virtuelles Hosten von Buckets).

### <a name="copy-a-directory"></a>Kopieren eines Verzeichnisses

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Beispiel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Beispiel** (hierarchischer Namespace)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> In diesem Beispiel wird das Flag `--recursive` angehängt, um Dateien in allen Unterverzeichnissen zu kopieren.

### <a name="copy-the-contents-of-a-directory"></a>Kopieren des Inhalts eines Verzeichnisses

Mithilfe des Platzhaltersymbols (*) können Sie die Inhalte eines Verzeichnisses kopieren, ohne das Verzeichnis selbst zu kopieren.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Beispiel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Beispiel** (hierarchischer Namespace)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Kopieren eines Buckets

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Beispiel** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Beispiel** (hierarchischer Namespace)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopieren aller Buckets in allen Regionen

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Beispiel** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Beispiel** (hierarchischer Namespace)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopieren aller Buckets in einer bestimmten S3-Region

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Beispiel** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Beispiel** (hierarchischer Namespace)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Unterschiede in Benennungsregeln für Objekte

AWS S3 verfügt über andere Namenskonventionen für Bucketnamen als Azure-Blobcontainer. Informationen zu diesem Thema finden Sie [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Wenn Sie die Entscheidung treffen, eine Gruppe mit Buckets in ein Azure-Speicherkonto zu kopieren, tritt für den Kopiervorgang ggf. ein Fehler aufgrund von Unterschieden bei der Benennung auf.

Mit AzCopy werden zwei der häufigsten Probleme behandelt, die auftreten können: Buckets, die Zeiträume enthalten, und Buckets, die aufeinander folgende Bindestriche enthalten. Namen von AWS S3-Buckets können Zeiträume und aufeinander folgende Bindestriche enthalten, aber für einen Container in Azure ist dies nicht möglich. Mit AzCopy werden Zeiträume durch Bindestriche und aufeinander folgende Bindestriche durch eine Zahl ersetzt, die für die Anzahl von aufeinander folgenden Bindestriche steht (Beispiel: ein Bucket mit dem Namen `my----bucket` wird zu `my-4-bucket`. 

Wenn mit AzCopy Dateien kopiert werden, wird auch eine Überprüfung auf Namenskonflikte durchgeführt und versucht, diese zu lösen. Falls beispielsweise Buckets mit den Namen `bucket-name` und `bucket.name` vorhanden sind, löst AzCopy einen Bucket mit dem Namen `bucket.name` zuerst in `bucket-name` und dann in `bucket-name-2` auf.

## <a name="handle-differences-in-object-metadata"></a>Behandeln von Unterschieden in Objektmetadaten

Bei AWS S3 und Azure sind in den Namen von Objektschlüsseln unterschiedliche Zeichen zulässig. Informationen zu den Zeichen, die in AWS S3 genutzt werden können, finden Sie [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Auf Azure-Seite unterliegen Blobobjektschlüssel den Benennungsregeln für [C#-Bezeichner](/dotnet/csharp/language-reference/).

Im Rahmen des AzCopy-Befehls `copy` können Sie einen Wert für das optionale Flag `s2s-handle-invalid-metadata` angeben. Hiermit wird festgelegt, wie Dateien verarbeitet werden sollen, bei denen die Metadaten der Datei inkompatible Schlüsselnamen enthalten. In der folgenden Tabelle werden die einzelnen Flagwerte beschrieben.

| Flagwert | BESCHREIBUNG  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standardoption) Die Metadaten sind nicht im übertragenen Objekt enthalten. AzCopy protokolliert eine Warnung. |
| **FailIfInvalid** | Objekte werden nicht kopiert. AzCopy protokolliert einen Fehler und fügt diese Fehler der Fehleranzahl hinzu, die in der Zusammenfassung der Übertragung angezeigt wird.  |
| **RenameIfInvalid**  | AzCopy löst den ungültigen Metadatenschlüssel auf und kopiert das Objekt in Azure, indem das aufgelöste Schlüssel-Wert-Paar aus den Metadaten verwendet wird. Informationen zu den genauen Schritten, die von AzCopy zum Umbenennen von Objektschlüsseln ausgeführt werden, finden Sie unten im Abschnitt [Umbenennen von Objektschlüsseln durch AzCopy](#rename-logic). Falls der Schlüssel von AzCopy nicht umbenannt werden kann, wird das Objekt nicht kopiert. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Umbenennen von Objektschlüsseln durch AzCopy

AzCopy führt die folgenden Schritte aus:

1. Ersetzt ungültige Zeichen durch „_“.

2. Fügt am Anfang eines neuen gültigen Schlüssels die Zeichenfolge `rename_` hinzu.

   Dieser Schlüssel wird verwendet, um den ursprünglichen **Wert** aus den Metadaten zu speichern.

3. Fügt am Anfang eines neuen gültigen Schlüssels die Zeichenfolge `rename_key_` hinzu.
   Dieser Schlüssel wird verwendet, um den ursprünglichen ungültigen **Schlüssel** aus den Metadaten zu speichern.
   Sie können mit diesem Schlüssel versuchen, die Metadaten auf Azure-Seite wiederherzustellen, weil der Metadatenschlüssel als Wert im Blobspeicherdienst gespeichert wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in den folgenden Artikeln:

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)

- [Übertragen von Daten](storage-use-azcopy-v10.md#transfer-data)

- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)

- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)
