---
title: azcopy sync | Microsoft-Dokumentation
description: Dieser Artikel enthält Referenzinformationen zum Befehl „azcopy sync“.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: de97640ab462ac8ea7342d235d0fad802c232179
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783367"
---
# <a name="azcopy-sync"></a>azcopy sync

Repliziert den Quellspeicherort an den Zielspeicherort.

## <a name="synopsis"></a>Zusammenfassung

Die Zeitpunkte der letzten Änderung werden für den Vergleich verwendet. Die Datei wird übersprungen, wenn der Zeitpunkt der letzten Änderung im Ziel aktueller ist.

Folgende Paare werden unterstützt:

- lokal <–> Azure-Blob (SAS- oder OAuth-Authentifizierung kann verwendet werden)
- Azure-Blob <-> Azure-Blob (Die Quelle muss eine SAS enthalten oder öffentlich zugänglich sein, für das Ziel kann die SAS- oder OAuth-Authentifizierung verwendet werden.)
- Azure-Datei <-> Azure-Datei (Die Quelle muss eine SAS enthalten oder öffentlich zugänglich sein, für das Ziel sollte die SAS-Authentifizierung verwendet werden.)

Der Befehl „sync“ unterscheidet sich auf mehrere Arten vom Befehl „copy“:

1. Das „recursive“-Flag ist standardmäßig „true“, und „sync“ kopiert alle Unterverzeichnisse. „sync“ kopiert nur die Dateien der obersten Ebene in einem Verzeichnis, wenn das „recursive“-Flag „false“ ist.
2. Fügen Sie beim Synchronisieren zwischen virtuellen Verzeichnissen dem Pfad einen nachstehenden Schrägstrich hinzu (siehe Beispiele), wenn ein Blob über denselben Namen wie eines der virtuellen Verzeichnisse verfügt.
3. Wenn das `deleteDestination`-Flag auf „true“ oder „prompt“ festgelegt ist, löscht „sync“ Dateien und Blobs am Ziel, die in der Quelle nicht vorhanden sind.

## <a name="related-conceptual-articles"></a>Verwandte konzeptionelle Artikel

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Übertragen von Daten mit AzCopy und Blobspeicher](storage-use-azcopy-blobs.md)
- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Erweitert

Wenn Sie keine Dateierweiterung angeben, erkennt AzCopy den Inhaltstyp der Dateien beim Hochladen vom lokalen Datenträger automatisch anhand der Dateierweiterung oder des Inhalts (falls keine Erweiterung angegeben ist).

Die integrierte Nachschlagetabelle ist klein, aber unter Unix wird sie, falls verfügbar, um die mime.types-Datei(en) des lokalen Systems erweitert. Hierfür werden diese Namen verwendet:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Unter Windows werden MIME-Typen aus der Registrierung extrahiert.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Beispiele

Synchronisieren einer einzelnen Datei:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Wie oben, aber berechnen Sie außerdem einen MD5-Hash des Dateiinhalts, und speichern Sie diesen MD5-Hash als „Content-MD5“-Eigenschaft des Blobs. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchronisieren eines gesamten Verzeichnisses einschließlich der zugehörigen Unterverzeichnisse (beachten Sie, dass „recursive“ standardmäßig aktiviert ist):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

oder

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronisieren Sie nur die Dateien innerhalb eines Verzeichnisses, nicht aber Unterverzeichnisse oder die Dateien innerhalb von Unterverzeichnissen:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchronisieren Sie eine Teilmenge von Dateien in einem Verzeichnis (z.B. nur JPG- und PDF-Dateien oder wenn der Dateiname `exactName` lautet):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Synchronisieren Sie ein gesamtes Verzeichnis, doch schließen Sie bestimmte Dateien aus dem Umfang aus (z. B. jede Datei, die mit „foo“ beginnt oder mit „bar“ endet):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Synchronisieren eines einzelnen Blobs:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synchronisieren eines virtuellen Verzeichnisses:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synchronisieren eines virtuellen Verzeichnisses mit demselben Namen wie ein Blob (Fügen Sie dem Pfad einen nachstehenden Schrägstrich hinzu, um die Namen zu unterscheiden.):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synchronisieren eines Azure-Dateiverzeichnisses (dieselbe Syntax wie bei einem Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Wenn „include“/„exclude“-Flags zusammen verwendet werden, würden nur Dateien berücksichtigt, die mit den „include“-Mustern übereinstimmen. Dateien, die mit den „exclude“-Mustern übereinstimmen, würden jedoch immer ignoriert.

## <a name="options"></a>Tastatur

**--block-size-mb** float  Verwendet diese Blockgröße (in MiB) beim Hochladen in Azure Storage oder beim Herunterladen aus Azure Storage. Der Standardwert wird anhand der Dateigröße automatisch berechnet. Dezimalzahlen sind zulässig (Beispiel: `0.25`).

**--check-md5** string  Gibt an, wie streng MD5-Hashes beim Herunterladen überprüft werden sollten. Diese Option ist nur beim Herunterladen verfügbar. Folgende Werte sind verfügbar: `NoCheck`, `LogOnly`, `FailIfDifferent`, `FailIfDifferentOrMissing`. (Standardwert: `FailIfDifferent`) (Standardwert: `FailIfDifferent`)

**--delete-destination** string   Definiert, ob zusätzliche Dateien am Ziel gelöscht werden sollen, die in der Quelle nicht vorhanden sind. Könnte auf `true`, `false` oder `prompt` festgelegt werden. Wenn sie auf `prompt` festgelegt wurde, wird dem Benutzer eine Frage gestellt, bevor Dateien und Blobs zum Löschen geplant werden. (Standardwert: `false`) (Standardwert: `false`)

**--exclude-attributes** string (nur Windows): schließt Dateien aus, deren Attribute mit der Attributliste übereinstimmen. Beispiel: `A;S;R`

**--exclude-path** string  Schließt diese Pfade beim Vergleich der Quelle mit dem Ziel aus. Diese Option unterstützt keine Platzhalterzeichen (*). Überprüft das Präfix des relativen Pfads (z. B. `myFolder;myFolder/subDirName/file.pdf`).

**--exclude-pattern string**   Schließt Dateien aus, deren Name der Musterliste entspricht. Beispiel: `*.jpg;*.pdf;exactName`

**--help**  Hilfe zu „sync“.

**--include-attributes** string  (Nur Windows) Schließt nur Dateien ein, deren Attribute mit der Attributliste übereinstimmen. Beispiel: `A;S;R`

**--include-pattern** string   Schließt nur Dateien ein, deren Name der Musterliste entspricht. Beispiel: `*.jpg;*.pdf;exactName`

**--log-level** string  Definiert die Ausführlichkeit des Protokolls für die Protokolldatei. Verfügbare Ebenen: `INFO`(alle Anforderungen und Antworten), `WARNING`(langsame Antworten), `ERROR`(nur fehlerhafte Anforderungen) und `NONE`(keine Ausgabeprotokolle). (Standardwert: `INFO`) 

**--preserve-smb-info:** standardmäßig FALSE. Behält SMB-Eigenschaftsinformationen ( letzte Schreibzeit, Erstellungszeit, Attributbits) zwischen SMB-fähigen Ressourcen (Windows und Azure Files) bei. Dieses Flag gilt sowohl für Dateien als auch für Ordner, es sei denn, ein reiner Dateifilter ist angegeben (z. B. „include-pattern“).  Die für Ordner übertragenen Informationen sind die gleichen wie die für Dateien, mit Ausnahme der letzten Schreibzeit, die für Ordner nicht gespeichert wird.

**--preserve-smb-permissions:** standardmäßig FALSE. Behält SMB-ACLs zwischen SMB-fähigen Ressourcen (Windows und Azure Files) bei. Dieses Flag gilt sowohl für Dateien als auch für Ordner, es sei denn, ein reiner Dateifilter ist angegeben (z. B. `include-pattern`).

**--put-md5:**  Erstellt einen MD5-Hash jeder Datei und speichert den Hash als „Content-MD5“-Eigenschaft des Zielblobs bzw. der Zieldatei. (Standardmäßig wird der Hash NICHT erstellt.) Nur beim Hochladen verfügbar.

**--recursive**  Standardmäßig `True`; überprüfen Sie Unterverzeichnisse rekursiv, wenn Sie zwischen Verzeichnissen synchronisieren. (Standardwert: `True`) 

**--s2s-preserve-access-tier**  Behält die Zugriffsebene beim Kopieren zwischen Diensten bei. Informationen zur Sicherstellung, dass das Zielspeicherkonto das Festlegen der Zugriffsebene unterstützt, finden Sie unter [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](../blobs/storage-blob-storage-tiers.md). Verwenden Sie in den Fällen, in denen das Festlegen der Zugriffsebene nicht unterstützt wird, „s2sPreserveAccessTier=false“, um das Kopieren der Zugriffsebene zu umgehen. (Standardwert: `true`) 

## <a name="options-inherited-from-parent-commands"></a>Von übergeordneten Befehlen geerbte Optionen

|Option|BESCHREIBUNG|
|---|---|
|–cap-mbps uint32|Begrenzt die Übertragungsrate (in Megabit pro Sekunde). Der Schritt-für-Schritt-Durchsatz kann von der Obergrenze geringfügig abweichen. Wenn diese Option auf „null“ festgelegt oder weggelassen wird, ist der Durchsatz nicht begrenzt.|
|–output-type string|Format der Befehlsausgabe. Folgende Optionen sind verfügbar: „text“ und „json“. Der Standardwert lautet „text“.|
|--trusted-microsoft-suffixes-Zeichenfolge   |Gibt zusätzliche Domänensuffixe an, an die Azure Active Directory-Anmeldetoken gesendet werden können.  Der Standardwert ist ' *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net'. Alle hier aufgelisteten Werte werden zum Standardwert hinzugefügt. Aus Sicherheitsgründen sollten Sie hier nur Microsoft Azure-Domänen platzieren. Trennen Sie mehrere E-Mail-Adressen durch Semikolons voneinander.|

## <a name="see-also"></a>Weitere Informationen

- [azcopy](storage-ref-azcopy.md)