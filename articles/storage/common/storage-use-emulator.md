---
title: Verwenden des Azure-Speicheremulators für Entwicklung und Tests | Microsoft-Dokumentation
description: Der Azure-Speicheremulator bietet eine kostenlose lokale Entwicklungsumgebung zum Entwickeln und Testen Ihrer Azure Storage-Anwendungen. Hier erhalten Sie Informationen zur Autorisierung von Anforderungen, zum Herstellen einer Verbindung mit dem Emulator über eine Anwendung und zum Verwenden des Befehlszeilentools.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: article
ms.date: 08/10/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 5f55228c80142b2a21af585cb04d16f148460af0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65149095"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Verwenden des Azure-Speicheremulators für Entwicklung und Tests

Der Microsoft Azure-Speicheremulator bietet eine lokale Umgebung, die die Azure-Blob-, -Warteschlangen- und -Tabellendienste für Entwicklungszwecke emuliert. Durch Verwendung des Speicheremulators können Sie die Anwendung bezüglich der Speicherdienste lokal testen, ohne ein Azure-Abonnement zu erwerben oder sonstige Kosten zu verursachen. Wenn Sie mit der Funktion der Anwendung im Emulator zufrieden sind, können Sie zur Verwendung eines Azure-Speicherkontos in der Cloud wechseln.

## <a name="get-the-storage-emulator"></a>Abrufen des Speicheremulators
Der Speicheremulator ist als Teil des [Microsoft Azure-SDK](https://azure.microsoft.com/downloads/)verfügbar. Sie können den Speicheremulator auch mithilfe des [eigenständigen Installationsprogramms](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) installieren (direkter Download). Zum Installieren des Speicheremulators benötigen Sie Administratorrechte auf Ihrem Computer.

Der Speicheremulator wird derzeit nur unter Windows ausgeführt. Eine Option für diejenigen, die einen Speicheremulator für Linux in Betracht ziehen, ist der von der Community unterstützte Open Source-Standardemulator [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Der Zugriff auf die Daten, die in einer bestimmten Version des Speicheremulators erstellt wurden, ist bei Verwendung einer anderen Version nicht garantiert. Wenn Sie die Daten langfristig beibehalten möchten, sollten Sie diese Daten in einem Azure Storage-Konto und nicht im Speicheremulator speichern.
> 
> Für den Speicheremulator sind spezifische Versionen der OData-Bibliotheken erforderlich. Das Ersetzen der OData-DLLs, die vom Speicheremulator mit anderen Versionen verwendet werden, wird nicht unterstützt und führt möglicherweise zu unerwartetem Verhalten. Es kann aber jede Version von OData, die vom Speicherdienst unterstützt wird, zum Senden von Anforderungen an den Emulator verwendet werden.

## <a name="how-the-storage-emulator-works"></a>Funktionsweise des Speicheremulators
Der Speicheremulator verwendet zum Emulieren von Azure Storage-Diensten eine lokale Microsoft SQL Server-Instanz und das lokale Dateisystem. Der Speicheremulator verwendet standardmäßig eine Datenbank in Microsoft SQL Server 2012 Express LocalDB. Sie können den Speicheremulator für den Zugriff auf eine lokale Instanz von SQL Server statt für den Zugriff auf die LocalDB-Instanz konfigurieren. Weitere Informationen finden Sie im Abschnitt [Starten und Initialisieren des Speicheremulators](#start-and-initialize-the-storage-emulator) weiter unten in diesem Artikel.

Der Speicheremulator stellt über die Windows-Authentifizierung eine Verbindung mit SQL Server oder LocalDB her.

Es bestehen einige Unterschiede in der Funktionsweise zwischen dem Speicheremulator und den Azure Storage-Diensten. Weitere Informationen zu diesen Unterschieden finden Sie im Abschnitt [Unterschiede zwischen dem Speicheremulator und Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) weiter unten in diesem Artikel.

## <a name="start-and-initialize-the-storage-emulator"></a>Starten und Initialisieren des Speicheremulators

So starten Sie den Azure-Speicheremulator
1. Wählen Sie die Schaltfläche **Start** aus, oder drücken Sie die **Windows-Taste**.
2. Geben Sie `Azure Storage Emulator` ein.
3. Wählen Sie den Emulator in der Liste der angezeigten Anwendungen aus.

Beim Start des Speicheremulators wird ein Eingabeaufforderungsfenster angezeigt. In diesem Konsolenfenster können Sie den Speicheremulator starten und beenden, Daten löschen, den Status abfragen und den Emulator initialisieren. Weitere Informationen finden Sie im Abschnitt [Referenz zum Speicheremulator-Befehlszeilentool](#storage-emulator-command-line-tool-reference) weiter unten in diesem Artikel.

Wenn der Emulator ausgeführt wird, wird im Infobereich der Windows-Taskleiste ein Symbol angezeigt.

Wenn Sie das Eingabeaufforderungsfenster des Speicheremulators schließen, wird der Speicheremulator weiterhin ausgeführt. Wiederholen Sie die Schritte oben zum Starten des Speicheremulators, um das Konsolenfenster des Speicheremulators wieder anzuzeigen.

Wenn Sie den Speicheremulator zum ersten Mal ausführen, wird die lokale Speicherumgebung für Sie initialisiert. Durch die Initialisierung wird in LocalDB eine Datenbank erstellt, und es werden für jeden lokalen Speicherdienst HTTP-Ports reserviert.

Der Speicheremulator wird standardmäßig unter `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` installiert.

> [!TIP]
> Im [Microsoft Azure Storage-Explorer](https://storageexplorer.com) können Sie mit lokalen Speicheremulatorressourcen arbeiten. Suchen Sie dazu nach dem Installieren und Starten des Speicheremulators in der Struktur der Storage-Explorer-Ressourcen unter „Speicherkonten“ nach „(Entwicklung)“.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initialisieren des Speicheremulators zur Verwendung einer anderen SQL-Datenbank

Sie können das Speicheremulator-Befehlszeilentool zum Initialisieren des Speicheremulators verwenden, damit dieser auf eine andere SQL-Datenbankinstanz als die Standardinstanz von LocalDB verweist:

1. Öffnen Sie das Konsolenfenster des Speicheremulators gemäß der Beschreibung im Abschnitt [Starten und Initialisieren des Speicheremulators](#start-and-initialize-the-storage-emulator).
1. Geben Sie im Konsolenfenster den folgenden Befehl ein, wobei `<SQLServerInstance>` der Name der SQL Server-Instanz ist. Geben Sie `(localdb)\MSSQLLocalDb` als SQL Server-Instanz ein, um LocalDB zu verwenden.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Mit dem folgenden Befehl können Sie den Emulator anweisen, die SQL Server-Standardinstanz zu verwenden:

   `AzureStorageEmulator.exe init /server .`

   Sie können auch den folgenden Befehl verwenden, mit dem die Datenbank als Standardinstanz von LocalDB erneut initialisiert wird:

   `AzureStorageEmulator.exe init /forceCreate`

Weitere Informationen zu diesen Befehlen finden Sie unter [Referenz zum Speicheremulator-Befehlszeilentool](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Mit [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) können Sie Ihre SQL Server-Instanzen, einschließlich der LocalDB-Installation, verwalten. Geben Sie im SMSS-Dialogfeld **Verbindung mit Server herstellen** im Feld **Servername:** den Namen `(localdb)\MSSQLLocalDb` an, um eine Verbindung mit der LocalDB-Instanz herzustellen.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Authentifizieren von Anforderungen an den Speicheremulator
Nachdem Sie den Speicheremulator installiert und gestartet haben, können Sie den Code testen. Wie bei Azure Storage in der Cloud muss jede Anforderung, die Sie mit dem Speicheremulator vornehmen, autorisiert werden, sofern es sich nicht um eine anonyme Anforderung handelt. Sie können Anforderungen an den Speicheremulator mit einem gemeinsam verwendeten Schlüssel oder mit einer Shared Access Signature (SAS) autorisieren.

### <a name="authorize-with-shared-key-credentials"></a>Autorisieren mit Benutzeranmeldeinformationen eines gemeinsam verwendeten Schlüssels
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorisieren mit einer SAS (Shared Access Signature)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Einige Azure Storage-Clientbibliotheken, wie z. B. die Xamarin-Bibliothek, unterstützen nur Authentifizierung mit einem SAS (Shared Access Signature)-Token. Sie können das SAS-Token mit einem Tool wie dem [Storage-Explorer](https://storageexplorer.com/) oder einer anderen Anwendung erstellen, die die Authentifizierung mit einem gemeinsam verwendeten Schlüssel unterstützt.

Sie können ein SAS-Token auch mithilfe von Azure PowerShell generieren. Im folgenden Beispiel wird ein SAS-Token mit vollen Berechtigungen für einen Blobcontainer generiert:

1. Installieren Sie Azure PowerShell, sofern noch nicht geschehen (die Verwendung der neuesten Version der Azure PowerShell-Cmdlets wird empfohlen). Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps).
2. Öffnen Sie Azure PowerShell, und führen Sie die folgenden Befehle aus. Ersetzen Sie hierbei `CONTAINER_NAME` durch einen Namen Ihrer Wahl:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Der resultierende SAS-URI für den neuen Container sollte etwa wie folgt lauten:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Die mit diesem Beispiel erstellte SAS gilt für einen Tag. Die Signatur gewährt uneingeschränkten Zugriff (Lesen, Schreiben, Löschen und Auflisten) auf die Blobs im Container.

Weitere Informationen zu Shared Access Signatures finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adressieren von Ressourcen im Speicheremulator
Die Dienstendpunkte für den Speicheremulator unterscheiden sich von denen eines Azure-Speicherkontos. Der Unterschied ist: Da der lokale Computer keine Domänennamenauflösung durchführt, müssen die Speicheremulator-Endpunkte lokale Adressen sein.

Verwenden Sie das folgende Schema, wenn Sie eine Ressource in einem Azure Storage-Konto adressieren. Hierbei ist der Kontoname Teil des URI-Hostnamens, und die adressierte Ressource ist Teil des URI-Pfads:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Der folgende URI ist z. B. eine gültige Adresse für ein Blob in einem Azure-Speicherkonto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Im Speicheremulator ist der Kontoname jedoch Teil des URI-Pfads und nicht des Hostnamens, da der lokale Computer Domänennamen nicht auflöst. Verwenden Sie das folgende URI-Format für eine Ressource im Speicheremulator:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Beispielsweise kann für den Zugriff auf ein Blob im Speicheremulator die folgende Adresse verwendet werden:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Die Dienstendpunkte für den Speicheremulator sind:

* Blob-Dienst: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Warteschlangendienst: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Tabellenspeicherdienst: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adressieren des sekundären Kontos mit RA-GRS
Ab Version 3.1 unterstützt der Speicheremulator georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Für Speicherressourcen in der Cloud und im lokalen Emulator können Sie auf den sekundären Speicherort zugreifen, indem Sie "-secondary" an den Kontonamen anfügen. Beispielsweise kann die folgende Adresse für den Zugriff auf ein Blob mithilfe des sekundären Speicherorts mit Lesezugriff verwendet werden:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. Weitere Informationen finden Sie in der [Microsoft Azure-Speicherclientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referenz zum Speicheremulator-Befehlszeilentool
Ab Version 3.0 wird beim Starten des Speicheremulators ein Konsolenfenster angezeigt. In der Befehlszeile des Konsolenfensters können Sie den Emulator starten und beenden, den Status abfragen und weitere Vorgänge ausführen.

> [!NOTE]
> Falls Sie den Microsoft Azure-Serveremulator installiert haben, wird beim Start des Speicheremulators ein Taskleistensymbol angezeigt. Klicken Sie mit der rechten Maustaste auf das Symbol, um ein Menü zu öffnen, das eine grafische Möglichkeit bietet, den Speicheremulator zu starten und zu beenden.
>
>

### <a name="command-line-syntax"></a>Befehlszeilensyntax
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Optionen
Geben Sie zum Anzeigen der Liste der Optionen an der Eingabeaufforderung `/help` ein.

| Option | BESCHREIBUNG | Get-Help | Argumente |
| --- | --- | --- | --- |
| **Starten** |Startet den Speicheremulator. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Startet den Emulator im aktuellen Prozess, anstatt einen neuen Prozess zu erstellen. |
| **Beenden** |Beendet den Speicheremulator. |`AzureStorageEmulator.exe stop` | |
| **Status** |Zeigt den Status des Speicheremulators an. |`AzureStorageEmulator.exe status` | |
| **Clear** |Löscht die Daten in allen Diensten, die an der Befehlszeile angegeben werden. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: Löscht Blobdaten. <br/>*queue*: Löscht Warteschlangendaten. <br/>*table*: Löscht Tabellendaten. <br/>*all*: Löscht sämtliche Daten in allen Diensten. |
| **Init** |Führt eine einmalige Initialisierung zur Einrichtung des Emulators durch. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Gibt den Server an, der die SQL-Instanz hostet. <br/>*-sqlinstance instanceName*: Gibt den Namen der zu verwendenden SQL-Instanz in der Standardserverinstanz an. <br/>*-forcecreate*: Erzwingt die Erstellung der SQL-Datenbank, auch wenn diese bereits vorhanden ist. <br/>*-skipcreate*: Überspringt das Erstellen der SQL-Datenbank. Dies hat Vorrang vor „-forcecreate“.<br/>*-reserveports*: Versucht, die den Diensten zugeordneten HTTP-Ports zu reservieren.<br/>*-unreserveports*: Versucht, Reservierungen für die den Diensten zugeordneten HTTP-Ports zu entfernen. Dies hat Vorrang vor „-reserveports“.<br/>*-inprocess*: Führt die Initialisierung im aktuellen Prozess aus, anstatt einen neuen Prozess zu erstellen. Der aktuelle Prozess muss mit erhöhten Rechten gestartet werden, wenn Portreservierungen geändert werden. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Unterschiede zwischen dem Speicheremulator und Azure Storage
Da der Speicheremulator eine emulierte Umgebung darstellt, die in einer lokalen SQL-Instanz ausgeführt wird, gibt es funktionelle Unterschiede zwischen dem Emulator und einem Azure-Speicherkonto in der Cloud:

* Der Speicheremulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel.
* Der Speicheremulator ist kein skalierbarer Speicherdienst und unterstützt keine große Anzahl von gleichzeitigen Clients.
* Wie in [Adressieren von Ressourcen im Speicheremulator](#addressing-resources-in-the-storage-emulator)beschrieben, werden Ressourcen im Speicheremulator im Vergleich zu einem Azure-Speicherkonto anders adressiert. Dieser Unterschied ist darauf zurückzuführen, dass die Domänennamensauflösung zwar in der Cloud, nicht aber auf dem lokalen Computer verfügbar ist.
* Ab Version 3.1 unterstützt das Speicheremulatorkonto georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Im Emulator ist für alle Konten RA-GRS aktiviert, und es entsteht niemals eine Verzögerung zwischen den primären und sekundären Replikaten. Die Vorgänge "Get Blob Service Stats", "Get Queue Service Stats" und "Get Table Service Stats" werden für das sekundäre Konto unterstützt. Sie geben immer den Wert des `LastSyncTime`-Antwortelements als aktuelle Uhrzeit der zugrunde liegenden SQL-Datenbank zurück.
* Die Endpunkte für Dateidienst und SMB-Protokolldienst werden im Speicheremulator zurzeit nicht unterstützt.
* Wenn Sie eine Version der Speicherdienste verwenden, die vom Emulator noch nicht unterstützt wird, gibt der Speicheremulator den Fehler „VersionNotSupportedByEmulator“ (HTTP-Statuscode 400 – Ungültige Anforderung) zurück.

### <a name="differences-for-blob-storage"></a>Unterschiede beim Blob-Speicher
Die folgenden Unterschiede gelten für Blob-Speicher im Emulator:

* Der Speicheremulator unterstützt nur Blob-Größen bis 2 GB.
* Die maximale Länge eines Blobnamens im Speicheremulator ist 256 Zeichen, während sie im Azure-Speicher 1024 Zeichen beträgt.
* Bei inkrementellen Kopien können Momentaufnahmen von überschriebenen Blobs kopiert werden, wodurch ein Fehler für den Dienst zurückgegeben wird.
* „Get Page Ranges Diff“ funktioniert nicht zwischen Momentaufnahmen, die per inkrementeller Blobkopie kopiert wurden.
* Ein Ablegevorgang für ein Blob kann bei einem Blob, das im Speicheremulator vorhanden ist und über eine aktive Lease verfügt, erfolgreich durchgeführt werden. Dies gilt auch, wenn die Lease-ID in der Anforderung nicht angegeben wurde.
* Vorgänge für Anfügeblobs  werden vom Emulator nicht unterstützt. Beim Versuch, einen Vorgang auf einen Anfügeblob anzuwenden, wird der Fehler „FeatureNotSupportedByEmulator“ (HTTP-Statuscode 400 – Ungültige Anforderung) zurückgegeben.

### <a name="differences-for-table-storage"></a>Unterschiede beim Tabellenspeicher
Die folgenden Unterschiede gelten für Tabellenspeicher im Emulator:

* Datumseigenschaften im Tabellenspeicherdienst im Speicheremulator unterstützen nur den von SQL Server 2005 unterstützten Bereich (sie müssen nach dem 1. Januar 1753 liegen). Alle Datumsangaben vor dem 1. Januar 1753 werden in diesen Wert geändert. Die Genauigkeit der Daten ist begrenzt auf die Genauigkeit von SQL Server 2005, d. h., Datumsangaben sind auf 1/300 Sekunde genau.
* Der Speicheremulator unterstützt Eigenschaftenwerte für Partitions- und Zeilenschlüssel von jeweils weniger als 512 Byte. Darüber hinaus darf die Gesamtgröße von Kontoname, Tabellenname und Schlüsseleigenschaftennamen 900 Byte nicht überschreiten.
* Die Gesamtgröße einer Zeile in einer Tabelle im Speicheremulator ist auf weniger als 1 MB beschränkt.
* Im Speicheremulator unterstützen Eigenschaften des Datentyps `Edm.Guid` oder `Edm.Binary` in Filterzeichenfolgen für Abfragen nur die Vergleichsoperatoren `Equal (eq)` und `NotEqual (ne)`.

### <a name="differences-for-queue-storage"></a>Unterschiede beim Warteschlangenspeicher
Es bestehen keine Unterschiede beim Warteschlangenspeicher im Emulator.

## <a name="storage-emulator-release-notes"></a>Speicheremulator – Versionshinweise

### <a name="version-57"></a>Version 5.7
Korrektur eines Fehlers, der zu einem Absturz führen würde, wenn die Protokollierung aktiviert ist.

### <a name="version-56"></a>Version 5.6
* Der Speicheremulator unterstützt nun die Version vom 28.03.2018 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.

### <a name="version-55"></a>Version 5.5
* Der Speicheremulator unterstützt nun Version 2017-11-09 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten.
* Unterstützung für die Blobeigenschaft **Created**, die die Bloberstellungszeit zurückgibt, wurde hinzugefügt.

### <a name="version-54"></a>Version 5.4
Zur Verbesserung der Stabilität der Installation versucht der Emulator nicht mehr, Ports während der Installationszeit zu reservieren. Wenn Portreservierungen gewünscht sind, verwenden Sie die *-reserveports*-Option des **init**-Befehls, um sie anzugeben.

### <a name="version-53"></a>Version 5.3
Der Speicheremulator unterstützt nun Version 2017-07-29 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten.

### <a name="version-52"></a>Version 5.2
* Der Speicheremulator unterstützt nun Version 2017-04-17 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten.
* Korrektur eines Fehlers, aufgrund dessen Tabelleneigenschaftswerte nicht ordnungsgemäß codiert wurden.

### <a name="version-51"></a>Version 5.1
Es wurde ein Fehler behoben, bei dem der Speicheremulator in einigen Antworten den Header `DataServiceVersion` zurückgegeben hat, während der Dienst dies nicht getan hat.

### <a name="version-50"></a>Version 5.0
* Der Speicheremulator-Installer führt keine Überprüfungen auf vorhandene MSSQL- und .NET Framework-Installationen mehr durch.
* Der Speicheremulator-Installer erstellt die Datenbank nicht mehr als Teil der Installation. Die Datenbank wird bei Bedarf immer noch als Teil des Starts erstellt.
* Die Datenbankerstellung erfordert keine Rechteerweiterungen mehr.
* Portreservierungen werden für den Start nicht mehr benötigt.
* Die folgenden Optionen wurden zu `init` hinzugefügt: `-reserveports` (erfordert Rechteerweiterungen), `-unreserveports` (erfordert Rechteerweiterungen), `-skipcreate`.
* Über die Speicheremulator-Benutzeroberflächenoption auf dem Taskleistensymbol wird nun die Befehlszeilenschnittstelle gestartet. Die alte GUI ist nicht mehr verfügbar.
* Einige DLLs wurden entfernt oder umbenannt.

### <a name="version-46"></a>Version 4.6
* Der Speicheremulator unterstützt nun Version 2016-05-31 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten.

### <a name="version-45"></a>Version 4.5
* Korrektur eines Fehlers, das verursacht hat, dass Initialisierung und Installation des Speicheremulators misslingen, wenn die Sicherungsdatenbank umbenannt wurde.

### <a name="version-44"></a>Version 4.4
* Der Speicheremulator unterstützt nun Version 2015-12-11 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten.
* Der Speicheremulator verfügt nun über eine effizientere Garbage Collection für die Verarbeitung zahlreicher Blobs.
* Ein Fehler wurde behoben, durch den die Validierung des Container-ACL-XML-Codes nicht exakt der Validierung durch den Speicherdienst entsprach.
* Ein Fehler, aufgrund dessen die Mindest- und Höchstwerte für „DateTime“ manchmal in der falschen Zeitzone gemeldet wurden, wurde behoben.

### <a name="version-43"></a>Version 4.3
* Der Speicheremulator unterstützt nun Version 2015-07-08 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten.

### <a name="version-42"></a>Version 4.2
* Der Speicheremulator unterstützt nun Version 2015-04-05 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten.

### <a name="version-41"></a>Version 4.1
* Der Speicheremulator unterstützt nun Version 2015-02-21 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten, mit Ausnahme der neuen Anfügeblob-Funktionen.
* Wenn Sie eine Version der Speicherdienste verwenden, die vom Emulator noch nicht unterstützt wird, gibt der Speicheremulator eine sinnvolle Fehlermeldung zurück. Es wird empfohlen, die neueste Version des Emulators zu verwenden. Wenn Sie den Fehler „VersionNotSupportedByEmulator“ (HTTP-Statuscode 400 – Ungültige Anforderung) erhalten, laden Sie die neueste Version des Speicheremulators herunter.
* Es wurde ein Fehler behoben, bei der eine Racebedingung während der gleichzeitigen Zusammenführungsvorgänge zu falschen Tabellenentitätsdaten geführt hat.

### <a name="version-40"></a>Version 4.0
* Die ausführbare Datei des Speicheremulators wurde in *AzureStorageEmulator.exe*umbenannt.

### <a name="version-32"></a>Version 3.2
* Der Speicheremulator unterstützt nun Version 2014-02-14 der Speicherdienste auf Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkten. Endpunkte für den Dateidienst im Speicheremulator werden derzeit nicht unterstützt. Ausführliche Informationen über die Version 2014-02-14 finden Sie unter [Versionsverwaltung für den Blob-Dienst, den Warteschlangendienst und den Tabellendienst in Microsoft Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>Version 3.1
* Lesezugriff auf den geografisch redundanten Speicher (RA-GRS) wird nun im Speicheremulator unterstützt. Die API-Vorgänge "Get Blob Service Stats", "Get Queue Service Stats" und "Get Table Service Stats" werden für das sekundäre Konto unterstützt. Sie geben immer den Wert des LastSyncTime-Antwortelements als aktuelle Uhrzeit der zugrunde liegenden SQL-Datenbank zurück. Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. In der Referenz zur Microsoft Azure Storage-Clientbibliothek für .NET finden Sie weitere Einzelheiten.

### <a name="version-30"></a>Version 3.0
* Der Azure-Speicheremulator wird nicht mehr im gleichen Paket wie der Serveremulator ausgeliefert.
* Die grafische Benutzeroberfläche des Speicheremulators wird durch eine skriptfähige Befehlszeilenschnittstelle ersetzt. Ausführliche Informationen zur Befehlszeilenschnittstelle finden Sie in der Referenz zum Speicheremulator-Befehlszeilentool. Die grafische Benutzeroberfläche wird weiterhin in Version 3.0 enthalten sein, es kann aber nur sie zugegriffen werden, wenn der Serveremulator installiert ist, indem Sie mit der rechten Maustaste auf das Taskleistensymbol klicken und die Speicheremulator-Benutzeroberfläche auswählen.
* Version 2013-08-15 der Azure Storage-Dienste wird jetzt vollständig unterstützt. (Zuvor wurde diese Version nur von Version 2.2.1 Preview des Speicheremulators unterstützt.)

## <a name="next-steps"></a>Nächste Schritte

* Evaluieren Sie den plattformübergreifenden von der Community unterstützten Open Source-Speicheremulator [Azurite](https://github.com/arafato/azurite). 
* [Azure Storage-Beispiele mit .NET](../storage-samples-dotnet.md) enthält Links zu mehreren Codebeispielen, die Sie bei der Anwendungsentwicklung verwenden können.
* Im [Microsoft Azure Storage-Explorer](https://storageexplorer.com) können Sie mit Ressourcen in Ihrem Storage-Konto in der Cloud und im Speicheremulator arbeiten.
