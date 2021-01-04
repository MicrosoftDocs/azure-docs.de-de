---
title: azcopy copy| Microsoft-Dokumentation
description: Dieser Artikel enthält Referenzinformationen zum Befehl „azcopy copy“.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fd71f4eb56974b93637c23eddc81e5f33ce788b8
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512153"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopiert Quelldaten an einen Zielspeicherort.

## <a name="synopsis"></a>Zusammenfassung

Kopiert Quelldaten an einen Zielspeicherort. Die folgenden Richtungen werden unterstützt:

  - Lokal <-> Azure-Blob (SAS- oder OAuth-Authentifizierung)
  - Lokal <-> Azure Files (SAS-Authentifizierung für Freigabe/Verzeichnis)
  - Lokal <-> Azure Data Lake Storage Gen2 (Authentifizierung per SAS, OAuth oder gemeinsam verwendetem Schlüssel)
  - Azure-Blob (SAS oder öffentlich) -> Azure-Blob (SAS- oder OAuth-Authentifizierung)
  - Azure-Blob (SAS oder öffentlich) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure-Blob (SAS- oder OAuth-Authentifizierung)
  - Amazon Web Services (AWS) S3 (Zugriffsschlüssel) -> Azure-Blockblob (SAS- oder OAuth-Authentifizierung)

Weitere Informationen finden Sie im Abschnitt mit den Beispielen in diesem Artikel.

## <a name="related-conceptual-articles"></a>Verwandte konzeptionelle Artikel

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Übertragen von Daten mit AzCopy und Blobspeicher](storage-use-azcopy-blobs.md)
- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Erweitert

AzCopy erkennt automatisch den Inhaltstyp der Dateien, wenn Sie diese vom lokalen Datenträger hochladen. AzCopy erkennt den Inhaltstyp basierend auf der Dateierweiterung oder dem Inhalt (wenn keine Erweiterung angegeben ist).

Die integrierte Nachschlagetabelle ist klein, aber unter Unix wird sie, falls verfügbar, um die `mime.types`-Dateien des lokalen Systems erweitert. Hierfür werden diese Namen verwendet:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Unter Windows werden MIME-Typen aus der Registrierung extrahiert. Diese Funktion kann mit einem Flag deaktiviert werden. Weitere Informationen finden Sie im Abschnitt mit Flags in diesem Artikel.

Wenn Sie eine Umgebungsvariable über die Befehlszeile festlegen, kann diese Variable in Ihrem Befehlszeilenverlauf gelesen werden. Es empfiehlt sich gegebenenfalls, Variablen mit Anmeldeinformationen aus Ihrem Befehlszeilenverlauf zu löschen. Wenn Sie verhindern möchten, dass Variablen in Ihrem Verlauf angezeigt werden, können Sie den Benutzer mit einem Skript zur Eingabe seiner Anmeldeinformationen auffordern und die Umgebungsvariable festlegen.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Beispiele

Hochladen einer einzelnen Datei unter Verwendung der OAuth-Authentifizierung. Wenn Sie sich noch nicht bei AzCopy angemeldet haben, führen Sie vor dem Ausführen des folgenden Befehls den Befehl `azcopy login` aus.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Wie oben, aber berechnen Sie hierbei außerdem den MD5-Hash des Dateiinhalts, und speichern Sie ihn als „Content-MD5“-Eigenschaft des Blobs:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Hochladen einer einzelnen Datei unter Verwendung eines SAS-Tokens:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Hochladen einer einzelnen Datei unter Verwendung eines SAS-Tokens und einer Weiterleitung (nur Blockblobs):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Hochladen eines gesamten Verzeichnisses unter Verwendung eines SAS-Tokens:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

oder

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Hochladen einer Gruppe von Dateien unter Verwendung eines SAS-Tokens und Platzhalterzeichen (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Hochladen von Dateien und Verzeichnissen unter Verwendung eines SAS-Tokens und Platzhalterzeichen (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Hochladen von Dateien und Verzeichnissen in ein Azure Storage-Konto und Festlegen der in der Abfragezeichenfolge codierten Tags für das Blob. 

- Verwenden Sie die folgende Syntax, um die Tags „{key = "bla bla", val = "foo"}“ und „{key = "bla bla 2", val = "bar"}“ festzulegen: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Schlüssel und Werte werden URL-codiert, und Schlüssel-Wert-Paare werden durch ein kaufmännisches Und-Zeichen (&) getrennt.

- Wenn Sie Tags für die Blobs festlegen, sind in SAS zusätzliche Berechtigungen („t“ für Tags) erforderlich, ohne die der Dienst einen Autorisierungsfehler zurückgibt.

Herunterladen einer einzelnen Datei unter Verwendung der OAuth-Authentifizierung. Wenn Sie sich noch nicht bei AzCopy angemeldet haben, führen Sie vor dem Ausführen des folgenden Befehls den Befehl `azcopy login` aus.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Herunterladen einer einzelnen Datei unter Verwendung eines SAS-Tokens:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Herunterladen einer einzelnen Datei unter Verwendung eines SAS-Tokens und anschließendes Weiterleiten der Ausgabe in eine Datei (nur Blockblobs):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Herunterladen eines gesamten Verzeichnisses unter Verwendung eines SAS-Tokens:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Hinweis zur Verwendung eines Platzhalterzeichens (*) in URLs:

Es gibt nur zwei Möglichkeiten, ein Platzhalterzeichen in einer URL zu verwenden. 

- Sie können einen Platzhalter direkt nach dem letzten Schrägstrich (/) einer URL verwenden. Durch die Verwendung des Platzhalterzeichens werden alle Dateien in einem Verzeichnis direkt an das Ziel kopiert, ohne sie in ein Unterverzeichnis einzufügen. 

- Sie können auch im Namen eines Containers ein Platzhalterzeichen verwenden, sofern die URL nur auf einen Container und nicht auf ein Blob verweist. Mit diesem Ansatz können Sie Dateien aus einer Teilmenge von Containern abrufen. 

Herunterladen der Inhalte eines Verzeichnisses, ohne das Verzeichnis selbst zu kopieren.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Herunterladen eines vollständigen Speicherkontos.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Herunterladen einer Teilmenge der Container innerhalb eines Speicherkontos unter Verwendung eines Platzhaltersymbols (*) im Containernamen.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Kopieren eines einzelnen Blobs in ein anderes Blob unter Verwendung eines SAS-Tokens.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieren eines einzelnen Blobs in ein anderes Blob unter Verwendung eines SAS-Tokens und eines Authentifizierungstokens. Sie müssen am Ende der URL für das Quellkonto ein SAS-Token verwenden. Für das Zielkonto ist jedoch kein Token erforderlich, wenn Sie sich mit dem Befehl `azcopy login` bei AzCopy anmelden. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Kopieren eines virtuellen Blobverzeichnisses in ein anderes unter Verwendung eines SAS-Tokens:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopieren aller Blobcontainer, Verzeichnisse und Blobs von einem Speicherkonto in ein anderes unter Verwendung eines SAS-Tokens:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieren eines einzelnen Objekts aus Amazon Web Services (AWS) S3 in Blob Storage unter Verwendung eines Zugriffsschlüssels und eines SAS-Tokens. Legen Sie zunächst die Umgebungsvariablen `AWS_ACCESS_KEY_ID` und `AWS_SECRET_ACCESS_KEY` für die AWS S3-Quelle fest.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieren eines gesamten Verzeichnisses aus AWS S3 in Blob Storage unter Verwendung eines Zugriffsschlüssels und eines SAS-Tokens. Legen Sie zunächst die Umgebungsvariablen `AWS_ACCESS_KEY_ID` und `AWS_SECRET_ACCESS_KEY` für die AWS S3-Quelle fest.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Unter https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html finden Sie ausführlichere Informationen zum Platzhalter „[folder]“.

Kopieren aller Buckets aus Amazon Web Services (AWS) in Blob Storage unter Verwendung eines Zugriffsschlüssels und eines SAS-Tokens. Legen Sie zunächst die Umgebungsvariablen `AWS_ACCESS_KEY_ID` und `AWS_SECRET_ACCESS_KEY` für die AWS S3-Quelle fest.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieren aller Buckets aus einer Amazon Web Services (AWS)-Region in Blob Storage unter Verwendung eines Zugriffsschlüssels und eines SAS-Tokens. Legen Sie zunächst die Umgebungsvariablen `AWS_ACCESS_KEY_ID` und `AWS_SECRET_ACCESS_KEY` für die AWS S3-Quelle fest.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieren einer Teilmenge der Buckets unter Verwendung eines Platzhaltersymbols (*) im Bucketnamen. Wie in den vorherigen Beispielen benötigen Sie einen Zugriffsschlüssel und ein SAS-Token. Achten Sie darauf, die Umgebungsvariablen `AWS_ACCESS_KEY_ID` und `AWS_SECRET_ACCESS_KEY` für die AWS S3-Quelle festzulegen.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Übertragen von Dateien und Verzeichnissen in ein Azure Storage-Konto und Festlegen der in der angegebenen Abfragezeichenfolge codierten Tags für das Blob. 

- Verwenden Sie die folgende Syntax, um die Tags „{key = "bla bla", val = "foo"}“ und „{key = "bla bla 2", val = "bar"}“ festzulegen: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Schlüssel und Werte werden URL-codiert, und Schlüssel-Wert-Paare werden durch ein kaufmännisches Und-Zeichen (&) getrennt.
    
- Wenn Sie Tags für die Blobs festlegen, sind in SAS zusätzliche Berechtigungen („t“ für Tags) erforderlich, ohne die der Dienst einen Autorisierungsfehler zurückgibt.

## <a name="options"></a>Tastatur

**--backup:** aktiviert SeBackupPrivilege unter Windows für Uploads oder SeRestorePrivilege für Downloads, damit AzCopy alle Dateien unabhängig von ihren Dateisystemberechtigungen lesen und alle Berechtigungen wiederherstellen kann. Erfordert, dass das Konto, unter dem AzCopy ausgeführt wird, bereits über diese Berechtigungen verfügt (z. B. Administratorrechte hat oder Mitglied der Gruppe `Backup Operators` ist). Dieses Flag aktiviert die Berechtigungen, die das Konto bereits hat.

**--blob-tags** string: Dient zum Festlegen von Tags für Blobs, um Daten in Ihrem Speicherkonto zu kategorisieren.

**--blob-type** string  Definiert den Typ des Blobs am Ziel. Wird zum Hochladen von Blobs und beim Kopieren zwischen Konten verwendet (Standardeinstellung: `Detect`). Gültige Werte sind unter anderem `Detect`, `BlockBlob`, `PageBlob` und `AppendBlob`. Beim Kopieren zwischen Konten bewirkt der Wert `Detect`, dass AzCopy den Typ des Zielblobs anhand des Typs des Quellblobs bestimmt. Beim Hochladen einer Datei bestimmt `Detect` anhand der Dateierweiterung, ob es sich um eine VHD- oder VHDX-Datei handelt. Eine VHD- oder VHDX-Datei wird von AzCopy als Seitenblob behandelt. (Standardwert: „Detect“)

**--block-blob-tier** string: lädt ein Blockblob unter Verwendung dieses Blobtarifs in Azure Storage hoch. (Standardwert: „None“)

**--block-size-mb** float: verwendet diese Blockgröße (in MiB) beim Hochladen in Azure Storage und beim Herunterladen aus Azure Storage. Der Standardwert wird anhand der Dateigröße automatisch berechnet. Dezimalzahlen sind zulässig (Beispiel: 0,25).

**--cache-control** string: legt den Cache-Control-Header fest. Wird beim Herunterladen zurückgegeben.

**--check-length:** überprüft nach der Übertragung die Länge einer Datei am Ziel. Wenn die Quelle und das Ziel nicht übereinstimmen, wird die Übertragung als fehlerhaft gekennzeichnet. (Standardwert: `true`)

**--check-md5** string: gibt an, wie streng MD5-Hashes beim Herunterladen überprüft werden sollten. Nur beim Herunterladen verfügbar. Verfügbare Optionen: `NoCheck`, `LogOnly`, `FailIfDifferent`, `FailIfDifferentOrMissing`. (Standardwert: `FailIfDifferent`) (Standardwert: „FailIfDifferent“)

**--content-disposition** string: legt den content-disposition-Header fest. Wird beim Herunterladen zurückgegeben.

**--content-encoding** string: legt den content-encoding-Header fest. Wird beim Herunterladen zurückgegeben.

**--content-language** string: legt den content-language-Header fest. Wird beim Herunterladen zurückgegeben.

**--content-type** string: gibt den Inhaltstyp der Datei an. Impliziert „no-guess-mime-type“. Wird beim Herunterladen zurückgegeben.

**--decompress:** dekomprimiert Dateien beim Herunterladen automatisch, wenn ihr content-encoding-Wert angibt, dass sie komprimiert sind. Die unterstützten content-encoding-Werte sind `gzip` und `deflate`. Die Dateierweiterungen `.gz`/`.gzip` oder `.zz` sind nicht erforderlich, werden jedoch entfernt, falls vorhanden.

**--exclude-attributes** string (nur Windows): schließt Dateien aus, deren Attribute mit der Attributliste übereinstimmen. Beispiel: A;S;R

**--exclude-blob-type** string: gibt optional den Blobtyp (`BlockBlob`/ `PageBlob`/ `AppendBlob`) an, der ausgeschlossen werden soll, wenn Blobs aus dem Container oder Konto kopiert werden. Die Verwendung dieses Flags gilt nicht für das Kopieren von Daten von einem nicht zu Azure gehörenden Dienst zu einem Dienst. Bei Angabe von mehreren Blobs sollte `;` als Trennzeichen verwendet werden. 

**--exclude-path** string: schließt diese Pfade beim Kopieren aus. Diese Option unterstützt keine Platzhalterzeichen (*). Überprüft das Präfix des relativen Pfads (z. B. `myFolder;myFolder/subDirName/file.pdf`). Wenn die Option in Verbindung mit einem Kontodurchlauf verwendet wird, enthalten Pfade keinen Containernamen.

**--exclude-pattern** string: schließt diese Dateien beim Kopieren aus. Diese Option unterstützt Platzhalterzeichen (*).

**--follow-symlinks:** verwendet beim Hochladen aus dem lokalen Dateisystem symbolische Links.

**--force-if-read-only:** erzwingt beim Überschreiben einer vorhandenen Datei unter Windows oder in Azure Files, dass das Überschreiben auch dann funktioniert, wenn für die vorhandene Datei das Nur-Lesen-Attribut festgelegt ist.

**--from-to** string: gibt optional die Kombination aus Quelle und Ziel an. Beispiel: `LocalBlob`, `BlobLocal`, `LocalBlobFS`.

**--help:** Hilfe zu „copy“.

**--include-after** string: schließt nur die Dateien ein, die ab dem angegebenen Datum bzw. der angegebenen Uhrzeit geändert wurden. Der Wert sollte das ISO 8601-Format aufweisen. Wenn keine Zeitzone angegeben ist, wird davon ausgegangen, dass der Wert in der lokalen Zeitzone des Computers angegeben ist, auf dem AzCopy ausgeführt wird, z. B. `2020-08-19T15:04:00Z` für eine UTC-Zeit oder `2020-08-19` für Mitternacht (00:00 Uhr) in der lokalen Zeitzone. Ab AzCopy 10.5 gilt dieses Flag nur für Dateien, nicht für Ordner, sodass Ordnereigenschaften nicht kopiert werden, wenn dieses Flag mit `--preserve-smb-info` oder `--preserve-smb-permissions` verwendet wird.

**--include-attributes** string (nur Windows): schließt Dateien ein, deren Attribute mit der Attributliste übereinstimmen. Beispiel: A;S;R

**--include-path** string: schließt nur diese Pfade beim Kopieren ein. Diese Option unterstützt keine Platzhalterzeichen (*). Überprüft das Präfix des relativen Pfads (z. B. `myFolder;myFolder/subDirName/file.pdf`).

**--include-pattern** string: schließt nur diese Dateien beim Kopieren ein. Diese Option unterstützt Platzhalterzeichen (*). Trennen Sie Dateien durch `;` voneinander ab.

**--list-of-versions** string  Gibt eine Datei an, bei der jede Versions-ID in einer separaten Zeile steht. Stellen Sie sicher, dass die Quelle auf ein einzelnes Blob verweisen muss und dass alle in der Datei mit diesem Flag angegebenen Versions-IDs nur zum Quellblob gehören dürfen. AzCopy lädt die angegebenen Versionen in den bereitgestellten Zielordner herunter. Weitere Informationen finden Sie unter [Herunterladen früherer Versionen eines Blobs](storage-use-azcopy-blobs.md#download-previous-versions-of-a-blob).

**--log-level** string: definiert, wie ausführlich die Protokolldatei sein soll. Verfügbare Stufen: INFO (alle Anforderungen/Antworten), WARNING (langsame Antworten), ERROR (nur fehlgeschlagene Anforderungen) und NONE (keine Ausgabeprotokolle). (Standardwert: `INFO`) 

**--metadata** string: führt Uploads in Azure Storage mit diesen Schlüssel-Wert-Paaren als Metadaten aus.

**--no-guess-mime-type:** verhindert, dass AzCopy anhand der Erweiterung oder des Inhalts der Datei den Inhaltstyp erkennt.

**--overwrite** string: überschreibt die in Konflikt stehenden Dateien und Blobs am Ziel, wenn dieses Flag auf TRUE festgelegt ist. (Standardwert: `true`) Mögliche Werte: `true`, `false`, `prompt` und `ifSourceNewer`. Für Ziele, die Ordner unterstützen, werden in Konflikt stehende Eigenschaften auf Ordnerebene überschrieben, wenn dieses Flag `true` ist oder wenn an der Eingabeaufforderung eine positive Antwort bereitgestellt wird. (Der Standardwert lautet „true“.)

**--page-blob-tier** string: lädt ein Seitenblob unter Verwendung dieses Blobtarifs in Azure Storage hoch. (Standardwert: `None`) (Standardwert: „None“)

**--preserve-last-modified-time:** nur verfügbar, wenn das Ziel ein Dateisystem ist.

**--preserve-owner:** wirkt sich nur beim Herunterladen aus, und auch nur dann, wenn `--preserve-smb-permissions` verwendet wird. Falls TRUE (Standardeinstellung), werden Besitzer und Gruppe der Datei bei Downloads beibehalten. Bei Festlegung auf FALSE behält `--preserve-smb-permissions` die ACLs trotzdem bei. Allerdings basieren Besitzer und Gruppe auf dem Benutzer, der AzCopy ausführt (Standardwert: TRUE).

**--preserve-smb-info:** standardmäßig FALSE. Behält SMB-Eigenschaftsinformationen ( letzte Schreibzeit, Erstellungszeit, Attributbits) zwischen SMB-fähigen Ressourcen (Windows und Azure Files) bei. Es werden nur die von Azure Files unterstützten Attributbits übertragen. Alle anderen werden ignoriert. Dieses Flag gilt sowohl für Dateien als auch für Ordner, es sei denn, ein reiner Dateifilter ist angegeben (z. B. „include-pattern“). Die für Ordner übertragenen Informationen sind die gleichen wie die für Dateien, mit Ausnahme der letzten Schreibzeit, die für Ordner nicht gespeichert wird.

**--preserve-smb-permissions:** standardmäßig FALSE. Behält SMB-ACLs zwischen SMB-fähigen Ressourcen (Windows und Azure Files) bei. Für Downloads müssen Sie auch das Flag `--backup` verwenden, um Berechtigungen wiederherzustellen, bei denen der neue Besitzer nicht der Benutzer ist, der AzCopy ausführt. Dieses Flag gilt sowohl für Dateien als auch für Ordner, es sei denn, ein reiner Dateifilter ist angegeben (z. B. `include-pattern`).

**--put-md5:** erstellt einen MD5-Hash jeder Datei und speichert den Hash als „Content-MD5“-Eigenschaft des Zielblobs bzw. der Zieldatei. (Standardmäßig wird der Hash NICHT erstellt.) Nur beim Hochladen verfügbar.

**--recursive:** durchsucht beim Hochladen aus dem lokalen Dateisystem Unterverzeichnisse rekursiv.

**--s2s-detect-source-changed:** erkennt, ob sich die Quelldatei bzw. das Quellblob beim Lesen ändert. (Dieser Parameter gilt nur für Dienst-zu-Dienst-Kopien, da die entsprechende Überprüfung dauerhaft für Uploads und Downloads aktiviert ist.)

**--s2s-handle-invalid-metadata** string   Gibt an, wie ungültige Metadatenschlüssel behandelt werden. Verfügbare Optionen: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (Standardwert: `ExcludeIfInvalid`) (Standardwert: „ExcludeIfInvalid“)

**--s2s-preserve-access-tier:** behält die Zugriffsebene beim Kopieren zwischen Diensten bei. Informationen zur Sicherstellung, dass das Zielspeicherkonto das Festlegen der Zugriffsebene unterstützt, finden Sie unter [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](../blobs/storage-blob-storage-tiers.md). Verwenden Sie in den Fällen, in denen das Festlegen der Zugriffsebene nicht unterstützt wird, „s2sPreserveAccessTier=false“, um das Kopieren der Zugriffsebene zu umgehen. (Standardwert: `true`)  (Der Standardwert lautet „true“.)

**--s2s-preserve-properties:** behält die vollständigen Eigenschaften beim Kopieren zwischen Diensten bei. Für AWS S3 und Azure Files mit mehr als einer Dateiquelle gibt der Auflistungsvorgang nicht die vollständigen Eigenschaften von Objekten und Dateien zurück. Um die vollständigen Eigenschaften beizubehalten, muss AzCopy eine zusätzliche Anforderung pro Objekt oder Datei senden. (Standardwert: „true“)

## <a name="options-inherited-from-parent-commands"></a>Von übergeordneten Befehlen geerbte Optionen

**--cap-mbps float:** begrenzt die Übertragungsrate (in Megabits pro Sekunde) Der Schritt-für-Schritt-Durchsatz kann von der Obergrenze geringfügig abweichen. Wenn diese Option auf „null“ festgelegt oder weggelassen wird, ist der Durchsatz nicht begrenzt.

**--output-type** string   Das Format der Befehlsausgabe. Folgende Optionen sind verfügbar: „text“ und „json“. Standardwert: `text`. (Standardwert: „text“)

**--trusted-microsoft-suffixes** string   Gibt zusätzliche Domänensuffixe an, an die Azure Active Directory-Anmeldetoken gesendet werden können.  Der Standardwert lautet `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Alle hier aufgelisteten Werte werden zum Standardwert hinzugefügt. Aus Sicherheitsgründen sollten Sie hier nur Microsoft Azure-Domänen platzieren. Trennen Sie mehrere E-Mail-Adressen durch Semikolons voneinander.

## <a name="see-also"></a>Weitere Informationen

- [azcopy](storage-ref-azcopy.md)