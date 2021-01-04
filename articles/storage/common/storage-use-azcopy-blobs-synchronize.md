---
title: Synchronisieren mit Azure Blob Storage mithilfe von AzCopy v10 | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Sammlung von AzCopy-Beispielbefehlen, die Sie bei der Synchronisierung mit Azure Blob Storage unterstützen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 6d1e9e8eeddaaa2ce8c891888935faad12d40295
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907397"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Synchronisieren mit Azure Blob Storage mithilfe von AzCopy v10

Sie können lokalen Speicher mit Azure Blob Storage synchronisieren, indem Sie das Befehlszeilenprogramm AzCopy v10 verwenden. 

Sie können die Inhalte eines lokalen Dateisystems mit einem Blobcontainer synchronisieren. Sie können auch Container und virtuelle Verzeichnisse miteinander synchronisieren. Die Synchronisierung erfolgt unidirektional. Anders gesagt: Sie wählen aus, welcher der beiden Endpunkte die Quelle und welcher das Ziel ist. Bei der Synchronisierung werden auch Server-zu-Server-APIs verwendet. Die Beispiele in diesem Abschnitt können auch für Konten verwendet werden, die über einen hierarchischen Namespace verfügen. 

> [!NOTE]
> Das aktuelle Release von AzCopy synchronisiert nicht zwischen anderen Quellen und Zielen (beispielsweise: Dateispeicher oder Amazon Web Services (AWS) S3-Buckets).

Beispiele für andere Arten von Aufgaben, z. B. das Hochladen von Dateien, das Herunterladen von Blobs oder das Kopieren von Blobs zwischen Konten, finden Sie unter den Links im Abschnitt [Nächste Schritte](#next-steps) dieses Artikels.

## <a name="get-started"></a>Erste Schritte

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu erfahren, wie Sie dem Speicherdienst Autorisierungsanmeldeinformationen bereitstellen können.

> [!NOTE] 
> In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Anmeldeinformationen für Autorisierung mithilfe von Azure Active Directory (Azure AD) bereitgestellt haben.
>
> Wenn Sie lieber ein SAS-Token für die Autorisierung des Zugriffs auf Blobdaten verwenden möchten, können Sie dieses Token in jedem AzCopy-Befehl an die Ressourcen-URL anfügen. Beispiel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.ken>“.

## <a name="guidelines"></a>Richtlinien

- Mit dem Befehl [sync](storage-ref-azcopy-sync.md) werden Dateinamen und die Zeitstempel der letzten Änderung verglichen. Legen Sie das optionale Flag `--delete-destination` auf den Wert `true` oder `prompt` fest, um Dateien im Zielverzeichnis zu löschen, wenn diese im Quellverzeichnis nicht mehr vorhanden sind.

- Wenn Sie das Flag `--delete-destination` auf `true` festlegen, löscht AzCopy Dateien, ohne zur Bestätigung aufzufordern. Wenn eine Bestätigungsaufforderung angezeigt werden soll, bevor AzCopy eine Datei löscht, legen Sie das `--delete-destination`-Flag auf `prompt` fest.

- Um ein versehentliches Löschen zu verhindern, aktivieren Sie das Feature [Vorläufiges Löschen](../blobs/soft-delete-blob-overview.md), bevor Sie das Flag `--delete-destination=prompt|true` verwenden.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualisieren eines Containers mit Änderungen an einem lokalen Dateisystem

In diesem Fall ist der Container das Ziel und das lokale Dateisystem die Quelle. 

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Beispiel** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualisieren eines lokalen Dateisystems mit Änderungen an einem Container

In diesem Fall ist das lokale Dateisystem das Ziel und der Container die Quelle.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Beispiel** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Aktualisieren eines Containers mit Änderungen in einem anderen Container

Der erste Container in diesem Befehl ist die Quelle. Das zweite ist das Ziel.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Beispiel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualisieren eines Verzeichnisses mit Änderungen in einem Verzeichnis in einer anderen Dateifreigabe

Das erste Verzeichnis in diesem Befehl ist die Quelle. Das zweite ist das Ziel.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Beispiel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Synchronisieren mit optionalen Flags

Sie können den Synchronisierungsvorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.

|Szenario|Flag|
|---|---|
|Angeben, wie streng MD5-Hashes beim Herunterladen überprüft werden sollen|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
|Ausschließen von Dateien basierend auf einem Muster|**--exclude-path**|
|Angeben, wie detailliert die synchronisierungsbezogenen Protokolleinträge sein sollen|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|

Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-sync.md#options).

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in diesen Artikeln:

- [Beispiele: Hochladen](storage-use-azcopy-blobs-upload.md)
- [Beispiele: Herunterladen](storage-use-azcopy-blobs-download.md)
- [Beispiele: Kopieren zwischen Konten](storage-use-azcopy-blobs-copy.md)
- [Beispiele: Amazon S3-Buckets](storage-use-azcopy-s3.md)
- [Beispiele: Azure Files](storage-use-azcopy-files.md)
- [Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)