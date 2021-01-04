---
title: Kopieren von Blobs zwischen Azure-Speicherkonten mit AzCopy v10 | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Sammlung von AzCopy-Beispielbefehlen, die Sie beim Kopieren von Blobs zwischen Speicherkonten unterstützen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f536e163e3d19d91c150506ab44fdd9cbc02c693
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907396"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Kopieren von Blobs zwischen Azure-Speicherkonten mithilfe von AzCopy v10

Sie können Blobs, Verzeichnisse und Container mithilfe des Befehlszeilenprogramms AzCopy v10 zwischen Speicherkonten kopieren. 

Beispiele für andere Arten von Aufgaben, z. B. das Hochladen von Dateien, das Herunterladen von Blobs und das Synchronisieren mit Blob Storage finden Sie unter den Links im Abschnitt [Nächste Schritte](#next-steps) dieses Artikels.

AzCopy verwendet die [Server-zu-Server](/rest/api/storageservices/put-block-from-url)-[APIs](/rest/api/storageservices/put-page-from-url), sodass Daten direkt zwischen Speicherservern kopiert werden. Für diese Kopiervorgänge wird nicht die Netzwerkbandbreite Ihres Computers genutzt.

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu erfahren, wie Sie Autorisierungsanmeldeinformationen für den Speicherdienst bereitstellen können. 

## <a name="guidelines"></a>Richtlinien

Wenden Sie die folgenden Richtlinien auf Ihre AzCopy-Befehle an. 

- Fügen Sie ein SAS-Token an jede Quell-URL an. 

  Wenn Sie Autorisierungsanmeldeinformationen mithilfe von Azure Active Directory (Azure AD) angeben, können Sie das SAS-Token nur in der Ziel-URL weglassen. Stellen Sie sicher, dass Sie die richtigen Rollen in Ihrem Zielkonto eingerichtet haben. Weitere Informationen finden Sie unter [Option 1: Verwenden von Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Ihre Identität mithilfe von Azure AD authentifiziert haben. Deshalb werden darin die SAS-Token in der Ziel-URL weggelassen.

-  Wenn Sie in ein Premium-Blockblob-Speicherkonto kopieren, lassen Sie die Zugriffsebene für ein Blob beim Kopiervorgang weg, indem Sie den Wert für `s2s-preserve-access-tier` auf `false` festlegen (Beispiel: `--s2s-preserve-access-tier=false`). Premium-Blockblob-Speicherkonten unterstützen keine Zugriffsebenen. 

- Wenn Sie in ein Konto oder aus einem Konto mit einem hierarchischen Namespace kopieren, verwenden Sie in der URL-Syntax `blob.core.windows.net` statt `dfs.core.windows.net`. Der [Multiprotokollzugriff für Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) ermöglicht Ihnen die Verwendung von `blob.core.windows.net`. Er ist die einzige unterstützte Syntax für Kopierszenarien zwischen Konten (Konto-zu-Konto). 

- Sie können den Durchsatz von Kopiervorgängen erhöhen, indem Sie den Wert der Umgebungsvariablen `AZCOPY_CONCURRENCY_VALUE` festlegen. Weitere Informationen finden Sie unter [Optimieren des Durchsatzes](storage-use-azcopy-configure.md#optimize-throughput). 

- Wenn die Quellblobs Indextags haben und Sie diese Tags beibehalten möchten, müssen Sie sie auf die Zielblobs erneut anwenden. Informationen zum Festlegen von Indextags finden Sie im Abschnitt [Kopieren von Blobs und Hinzufügen von Indextags](#copy-between-accounts-and-add-index-tags) dieses Artikels.

## <a name="copy-a-blob"></a>Kopieren eines Blobs

Kopieren Sie ein Blob mit dem Befehl [azcopy copy](storage-ref-azcopy-copy.md) in ein anderes Speicherkonto. 

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Beispiel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

Der Kopiervorgang verläuft synchron – wenn also der Befehl zurückgegeben wird, bedeutet dies, dass alle Dateien kopiert wurden. 

## <a name="copy-a-directory"></a>Kopieren eines Verzeichnisses

Kopieren Sie ein Verzeichnis mit dem Befehl [azcopy copy](storage-ref-azcopy-copy.md) in ein anderes Speicherkonto. 

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Beispiel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Der Kopiervorgang verläuft synchron – wenn also der Befehl zurückgegeben wird, bedeutet dies, dass alle Dateien kopiert wurden.

## <a name="copy-a-container"></a>Kopieren eines Containers

Kopieren Sie einen Container mit dem Befehl [azcopy copy](storage-ref-azcopy-copy.md) in ein anderes Speicherkonto.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Beispiel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Der Kopiervorgang verläuft synchron – wenn also der Befehl zurückgegeben wird, bedeutet dies, dass alle Dateien kopiert wurden.

## <a name="copy-containers-directories-and-blobs"></a>Kopieren von Containern, Verzeichnissen und Blobs

Kopieren Sie alle Container, Verzeichnisse und Blobs mit dem Befehl [azcopy copy](storage-ref-azcopy-copy.md) in ein anderes Speicherkonto.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Beispiel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

Der Kopiervorgang verläuft synchron – wenn also der Befehl zurückgegeben wird, bedeutet dies, dass alle Dateien kopiert wurden.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Kopieren von Blobs und Hinzufügen von Indextags

Kopieren Sie Blobs in ein anderes Speicherkonto, und fügen Sie dem Zielblob [Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md) hinzu.

Wenn Sie Azure AD-Autorisierung verwenden, muss Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen werden, oder ihm muss über eine benutzerdefinierte Azure-Rolle die Berechtigung für `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Vorgänge für Azure-Ressourcenanbieter](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) erteilt werden. Wenn Sie ein SAS-Token (Shared Access Signature) verwenden, muss es über die SAS-Berechtigung `t` Zugriff auf die Blobtags bieten.

Verwenden Sie zum Hinzufügen von Tags die Option `--blob-tags` zusammen mit einem URL-codierten Schlüssel-Wert-Paar. 

Wenn Sie beispielsweise den Schlüssel `my tag` und den Wert `my tag value` hinzufügen möchten, würden Sie dem Zielparameter `--blob-tags='my%20tag=my%20tag%20value'` hinzufügen. 

Trennen Sie mehrere Indextags durch ein kaufmännisches Und-Zeichen (`&`).  Wenn Sie beispielsweise den Schlüssel `my second tag` und den Wert `my second tag value` hinzufügen möchten, wäre die gesamte Optionszeichenfolge `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`.

In den folgenden Beispielen wird die Verwendung der Option `--blob-tags` gezeigt.

> [!TIP]
> In diesen Beispielen werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Verzeichnis** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Container** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Konto** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

Der Kopiervorgang verläuft synchron – wenn also der Befehl zurückgegeben wird, bedeutet dies, dass alle Dateien kopiert wurden.

> [!NOTE]
> Wenn Sie ein Verzeichnis, einen Container oder ein Konto für die Quelle angeben, haben alle in das Ziel kopierten Blobs dieselben Tags, die Sie im Befehl angeben.

## <a name="copy-with-optional-flags"></a>Kopieren mit optionalen Flags

Sie können den Kopiervorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.

|Szenario|Flag|
|---|---|
|Kopieren Sie Blobs als Block-, Seiten- oder Anfügeblobs.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|Kopieren auf eine bestimmte Zugriffsebene (z. B. die Archivebene)|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
|Dateien sollen automatisch dekomprimiert werden.|**--decompress**=\[gzip\|deflate\]|

Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in diesen Artikeln:

- [Beispiele: Hochladen](storage-use-azcopy-blobs-upload.md)
- [Beispiele: Herunterladen](storage-use-azcopy-blobs-download.md)
- [Beispiele: Synchronisieren](storage-use-azcopy-blobs-synchronize.md)
- [Beispiele: Amazon S3-Buckets](storage-use-azcopy-s3.md)
- [Beispiele: Azure Files](storage-use-azcopy-files.md)
- [Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)