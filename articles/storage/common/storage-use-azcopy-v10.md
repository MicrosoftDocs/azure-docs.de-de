---
title: Kopieren oder Verschieben von Daten in Azure Storage mit AzCopy v10 | Microsoft-Dokumentation
description: Verwenden Sie das Befehlszeilenprogramm AzCopy zum Verschieben oder Kopieren von Daten in oder aus Blob-, Data Lake- und Dateiinhalten. Kopieren Sie Daten aus lokalen Dateien nach Azure Storage oder innerhalb von bzw. zwischen Speicherkonten. Migrieren Sie Ihre Daten ganz einfach nach Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: b5a13dfd760f0c94343b151c9b4c1148c949e854
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790012"
---
# <a name="transfer-data-with-azcopy-v10"></a>Übertragen von Daten mit AzCopy v10

AzCopy ist das Befehlszeilenprogramm zum Kopieren von Daten für Microsoft Azure Blob Storage und File Storage. AzCopy verfügt über eine neu entworfene Befehlszeilenschnittstelle und eine neue Architektur für zuverlässige Datenübertragungen. Mit AzCopy können Sie Daten zwischen einem Dateisystem und einem Speicherkonto oder zwischen Speicherkonten kopieren.

## <a name="whats-new-in-azcopy-v10"></a>Neuerungen in AzCopy v10

- Dateisysteme werden mit Azure-Blobspeicher synchronisiert (oder umgekehrt). Verwenden Sie `azcopy sync <source> <destination>`. Ideal geeignet für Szenarien mit inkrementellen Kopien.
- Unterstützt Azure Data Lake Storage Gen2-APIs. Verwenden Sie `myaccount.dfs.core.windows.net` als URI zum Aufrufen der Data Lake Storage Gen2-APIs.
- Unterstützt das Kopieren eines gesamten Kontos (nur Blob-Dienst) in ein anderes Konto.
- Unterstützt das Kopieren von Daten aus einem Amazon Web Services-S3-Bucket.
- Die neuen [Put Block from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)-APIs werden verwendet, um das Kopieren von Konto zu Konto zu unterstützen. Die Datenübertragung ist schneller, da die Übertragung an den Client nicht erforderlich ist.
- Listet Dateien und Blobs unter einem bestimmten Pfad auf bzw. entfernt sie.
- Unterstützt Platzhaltermuster in einem Pfad sowie „--exclude“-Flags.
- Erstellt für jede AzCopy-Instanz eine Auftragsreihenfolge und eine zugehörige Protokolldatei. Sie können vorherige Aufträge anzeigen und neu starten und fehlgeschlagene Aufträge fortsetzen. AzCopy wiederholt außerdem eine Übertragung automatisch nach einem Fehler.
- Umfasst allgemeine Leistungsverbesserungen.

## <a name="download-and-install-azcopy"></a>Herunterladen und Installieren von AzCopy

### <a name="latest-production-version-v10"></a>Neueste Produktionsversion (v10)

Laden Sie die neueste Version von AzCopy herunter:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (ZIP)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (TAR)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (ZIP)

### <a name="latest-azcopy-supporting-table-storage-service-v73"></a>Die neueste AzCopy-Version unterstützt den Table Storage-Dienst (v7.3)

Laden Sie [AzCopy v7.3 mit Unterstützung für das Kopieren von Daten in den bzw. aus dem Microsoft Azure Table Storage-Dienst](https://aka.ms/downloadazcopynet) herunter.

## <a name="post-installation-steps"></a>Schritte nach der Installation

Eine Installation von AzCopy ist nicht erforderlich. Öffnen Sie Ihre bevorzugte Befehlszeilenanwendung, und navigieren Sie zu dem Ordner, in dem `azcopy.exe` enthalten ist. Bei Bedarf können Sie den Speicherort für den AzCopy-Ordner der Einfachheit halber dem Systempfad hinzufügen.

## <a name="authentication-options"></a>Authentifizierungsoptionen

AzCopy unterstützt die folgenden Optionen bei der Authentifizierung mit Azure Storage:
- **Azure Active Directory** (wird für **Blob- und Data Lake Storage Gen2-Dienste** unterstützt). Verwenden Sie ```.\azcopy login```, um sich mit Azure Active Directory anzumelden.  Dem Benutzer muss die [Rolle „Mitwirkender an Storage-Blobdaten“ zugewiesen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) sein, damit er per Azure Active Directory-Authentifizierung in den Blobspeicher schreiben kann. Verwenden Sie `azcopy login --identity`, um die Authentifizierung über verwaltete Identitäten für Azure-Ressourcen zu verwalten.
- **Shared Access Signature-Token [werden für Blob- und Dateidienste unterstützt]**. Fügen Sie das SAS-Token (Shared Access Signature) dem Blobpfad in der Befehlszeile hinzu, um es zu verwenden. Sie können das SAS-Token mit dem Azure-Portal, [Storage-Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken) oder anderen Tools Ihrer Wahl generieren. Weitere Informationen finden Sie unter [Beispiele](https://docs.microsoft.com/azure/storage/blobs/common/storage-dotnet-shared-access-signature-part-1).

## <a name="getting-started"></a>Erste Schritte

> [!TIP]
> **Ziehen Sie eine grafische Benutzeroberfläche vor?**
>
> Für [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) – ein Desktopclient, der die Verwaltung von Azure Storage-Daten vereinfacht – wird jetzt AzCopy verwendet, um Datenübertragungen in und aus Azure Storage zu beschleunigen.
>
> Aktivieren Sie AzCopy in Storage-Explorer über das Menü **Vorschau**.
> ![Aktivieren von AzCopy als Übertragungsmodul in Azure Storage-Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 verfügt über eine selbstdokumentierte Syntax. Wenn Sie sich an Azure Active Directory angemeldet haben, sieht die allgemeine Syntax wie folgt aus:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

Eine Liste der verfügbaren Befehle können Sie folgendermaßen abrufen:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Führen Sie den folgenden Befehl aus, um die Hilfeseite und Beispiele für einen bestimmten Befehl anzuzeigen:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Erstellen eines Blobcontainers oder einer Dateifreigabe 

**Erstellen eines Blobcontainers**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Erstellen einer Dateifreigabe**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Erstellen eines Blobcontainers mit Azure Data Lake Storage Gen2**

Wenn Sie hierarchische Namespaces in Ihrem Blobspeicherkonto aktiviert haben, können Sie mit dem folgenden Befehl einen neuen Blobcontainer zum Hochladen von Dateien erstellen.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Kopieren von Daten in Azure Storage

Verwenden Sie den Kopierbefehl, um Daten aus der Quelle in das Ziel zu übertragen. Die Quelle bzw. das Ziel kann Folgendes sein:
- Ein lokales Dateisystem
- Azure-Blob/virtuelles Verzeichnis/Container-URI
- Azure-Datei/Verzeichnis/Dateifreigabe-URI
- Azure Data Lake Storage Gen2-Dateisystem/Verzeichnis/Datei-URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Mit dem folgenden Befehl werden alle Dateien unter dem Ordner `C:\local\path` rekursiv in den Container `mycontainer1` hochgeladen, und im Container wird das Verzeichnis `path` erstellt. Wenn das Flag `--put-md5` bereitgestellt wird, berechnet und speichert AzCopy das md5-Hash jeder Datei in der Eigenschaft `Content-md5` des entsprechenden Blobs zur späteren Verwendung.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

Der folgende Befehl lädt alle Dateien unter dem Ordner `C:\local\path` (nicht rekursiv, also ohne Unterverzeichnisse) in den Container `mycontainer1` hoch:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

Verwenden Sie den folgenden Befehl, um weitere Beispiele abzurufen:

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>Kopieren von Blobdaten zwischen zwei Speicherkonten

Beim Kopieren von Daten zwischen zwei Speicherkonten wird die [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)-API und nicht die Netzwerkbandbreite des Clientcomputers genutzt. Daten werden zwischen zwei Azure Storage-Servern direkt kopiert, während AzCopy den Kopiervorgang einfach orchestriert. Diese Option ist derzeit nur für Blobspeicher verfügbar.

Um alle Blobdaten zwischen zwei Speicherkonten zu kopieren, verwenden Sie den folgenden Befehl:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Um einen Blobcontainer in einen anderen Blobcontainer zu kopieren, verwenden Sie den folgenden Befehl:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopieren eines VHD-Image in ein Speicherkonto

AzCopy lädt Daten standardmäßig in Blockblobs hoch. Um Dateien als Anfügeblobs oder Seitenblobs hochzuladen, verwenden Sie das Flag `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Sync: inkrementelles Kopieren und Löschen (nur Blob Storage)

Mit dem Synchronisierungsbefehl wird der Inhalt eines Quellverzeichnisses mit einem Verzeichnis im Zielverzeichnis synchronisiert, wobei Dateinamen und die Zeitstempel der letzten Änderung verglichen werden. Dieser Vorgang umfasst das optionale Löschen von Zieldateien, wenn diese in der Quelle nicht vorhanden sind und das Flag `--delete-destination=prompt|true` angegeben ist. Standardmäßig ist das Löschverhalten deaktiviert. 

> [!NOTE] 
> Verwenden Sie das Flag `--delete-destination` mit Vorsicht. Aktivieren Sie das Feature [Vorläufiges Löschen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete), bevor Sie das Löschverhalten bei der Synchronisierung aktivieren, um versehentliche Löschvorgänge in Ihrem Konto zu vermeiden. 
>
> Wenn `--delete-destination` auf TRUE festgelegt ist, löscht AzCopy Dateien, die in der Quelle nicht vorhanden sind, ohne Aufforderung des Benutzers aus dem Ziel. Verwenden Sie `--delete-destination=prompt`, wenn Sie zur Bestätigung aufgefordert werden möchten.

Verwenden Sie den folgenden Befehl, um Ihr lokales Dateisystem mit einem Speicherkonto zu synchronisieren:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Sie können auch einen Blobcontainer mit einem lokalen Dateisystem synchronisieren:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Mit diesem Befehl wird die Quelle basierend auf den Zeitstempeln der letzten Änderung inkrementell mit dem Ziel synchronisiert. Wenn Sie eine Datei in der Quelle hinzufügen oder löschen, verfährt AzCopy am Ziel auf die gleiche Weise. Vor dem Löschen werden Sie von AzCopy zum Bestätigen aufgefordert.

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>Kopieren von Daten aus Amazon Web Services (AWS) S3

Legen Sie für die Authentifizierung mit einem AWS S3-Bucket die folgenden Umgebungsvariablen fest:

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

Geben Sie zum Kopieren des Buckets in einen Blobcontainer den folgenden Befehl ein:

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

Weitere Informationen zum Kopieren von Daten aus AWS S3 mithilfe von AzCopy finden Sie auf der Seite [hier](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3).

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

### <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

Um die Proxyeinstellungen für AzCopy v10 zu konfigurieren, legen Sie die Umgebungsvariable „https_proxy“ mit dem folgenden Befehl fest:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optimieren des Durchsatzes

Legen Sie die Umgebungsvariable AZCOPY_CONCURRENCY_VALUE fest, um die Anzahl von gleichzeitigen Anforderungen zu konfigurieren und die Durchsatzleistung und Ressourcennutzung zu steuern. Standardmäßig ist der Wert auf 300 festgelegt. Die Verringerung des Werts schränkt die von AzCopy v10 verwendete Bandbreite und CPU ein.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Ändern des Speicherorts der Protokolldateien

Sie können den Speicherort der Protokolldateien bei Bedarf ändern oder, um eine Überfüllung des Betriebssystemdatenträgers zu vermeiden.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Ändern der Standardprotokollebene 

Standardmäßig ist die AzCopy-Protokollebene auf INFO festgelegt. Wenn Sie die Ausführlichkeit des Protokolls verringern möchten, um Speicherplatz zu sparen, überschreiben Sie die Einstellung mit der ``--log-level``-Option. Verfügbare Protokollebenen sind: DEBUG, INFO, WARNING, ERROR, PANIC und FATAL.

### <a name="review-the-logs-for-errors"></a>Überprüfen der Protokolle auf Fehler

Der folgende Befehl ruft alle Fehler mit dem Status UPLOADFAILED aus dem Protokoll 04dc9ca9-158f-7945-5933-564021086c79 ab:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Problembehandlung

AzCopy erstellt Protokolldateien und Plandateien für jeden Auftrag. Sie können die Protokolle verwenden, um potenzielle Probleme zu untersuchen und zu beheben. Die Protokolle enthalten den Status des Fehlers (UPLOADFAILED, COPYFAILED und DOWNLOADFAILED), den vollständigen Pfad und den Grund des Fehlers. Die Auftragsprotokolle und Plandateien sind im Ordner „%USERPROFILE\\.azcopy“ (unter Windows) bzw. im Ordner „$HOME\\.azcopy“ (unter Mac und Linux) gespeichert.

> [!IMPORTANT]
> Geben Sie beim Senden einer Anforderung an den Microsoft-Support (oder bei der Problembehandlung unter Einbeziehung eines Drittanbieters) die bearbeitete Version des Befehls an, den Sie ausführen möchten. So wird sichergestellt, dass die SAS nicht versehentlich offengelegt wird. Die editierte Version steht am Anfang der Protokolldatei.

### <a name="view-and-resume-jobs"></a>Anzeigen und Fortsetzen von Aufträgen

Jeder Übertragungsvorgang erstellt einen AzCopy-Auftrag. Verwenden Sie den folgenden Befehl, um den Verlauf von Aufträgen anzuzeigen:

```azcopy
.\azcopy jobs list
```

Verwenden Sie den folgenden Befehl, um die Auftragsstatistiken anzuzeigen:

```azcopy
.\azcopy jobs show <job-id>
```

Um die Übertragungen nach ihrem Status zu filtern, verwenden Sie den folgenden Befehl:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Verwenden Sie den folgenden Befehl, um einen fehlgeschlagenen bzw. abgebrochenen Auftrag fortzusetzen. Dieser Befehl verwendet seinen Bezeichner zusammen mit dem SAS-Token, da er aus Sicherheitsgründen nicht persistent ist:

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Fragen haben, Probleme melden oder allgemeines Feedback bereitstellen möchten, können Sie dies [auf GitHub](https://github.com/Azure/azure-storage-azcopy) tun.


