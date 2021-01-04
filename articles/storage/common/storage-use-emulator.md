---
title: Verwenden des Azure-Speicheremulators für Entwicklung und Tests
description: Der Azure-Speicheremulator bietet eine kostenlose lokale Entwicklungsumgebung zum Entwickeln und Testen Ihrer Azure Storage-Anwendungen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 6d9e0e63a3586be139620c154616acfcba2ab10f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498216"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Verwenden des Azure-Speicheremulators für Entwicklung und Tests

Der Microsoft Azure-Speicheremulator ist ein Tool zum Emulieren der Azure Blob-, -Warteschlangen- und -Tabellendienste für die lokale Entwicklung. Sie können die Anwendung mit den Speicherdiensten lokal testen, ohne ein Azure-Abonnement zu erstellen oder Kosten zu verursachen. Wenn Ihre Anwendung im Emulator erwartungsgemäß funktioniert, können Sie ein Azure-Speicherkonto in der Cloud verwenden.

> [!IMPORTANT]
> Der Azure-Speicheremulator wird nicht mehr aktiv entwickelt. [**Azurite**](storage-use-azurite.md) ist die Plattform für den Speicheremulator, die zukünftig verwendet wird. Azurite ersetzt den Azure-Speicheremulator. Azurite wird weiterhin aktualisiert, um die neuesten Versionen der Azure Storage-APIs zu unterstützen. Weitere Informationen finden Sie unter [**Verwenden des Azurite-Emulators für lokale Azure Storage-Entwicklung**](storage-use-azurite.md).

## <a name="get-the-storage-emulator"></a>Abrufen des Speicheremulators

Der Speicheremulator ist als Teil des [Microsoft Azure-SDK](https://azure.microsoft.com/downloads/) verfügbar. Sie können den Speicheremulator auch mithilfe des [eigenständigen Installationsprogramms](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) installieren (direkter Download). Zum Installieren des Speicheremulators benötigen Sie Administratorrechte auf Ihrem Computer.

Der Speicheremulator wird derzeit nur unter Windows ausgeführt. Wenn Sie einen Speicheremulator für Linux benötigen, können Sie beispielsweise den von der Community gepflegten Open-Source-Speicheremulator[Azurite](https://github.com/azure/azurite) verwenden.

> [!NOTE]
> Der Zugriff auf die Daten, die in einer bestimmten Version des Speicheremulators erstellt wurden, ist bei Verwendung einer anderen Version nicht garantiert. Wenn Sie die Daten langfristig beibehalten möchten, sollten Sie diese Daten in einem Azure-Speicherkonto und nicht im Speicheremulator speichern.
> 
> Für den Speicheremulator sind spezifische Versionen der OData-Bibliotheken erforderlich. Das Ersetzen der OData-DLLs, die vom Speicheremulator mit anderen Versionen verwendet werden, wird nicht unterstützt und führt möglicherweise zu unerwartetem Verhalten. Es kann aber jede Version von OData, die vom Speicherdienst unterstützt wird, zum Senden von Anforderungen an den Emulator verwendet werden.

## <a name="how-the-storage-emulator-works"></a>Funktionsweise des Speicheremulators

Der Speicheremulator verwendet zum Emulieren von Azure Storage-Diensten eine lokale Microsoft SQL Server 2012 Express LocalDB-Instanz. Sie können den Speicheremulator für den Zugriff auf eine lokale Instanz von SQL Server statt für den Zugriff auf die LocalDB-Instanz konfigurieren. Weitere Informationen finden Sie weiter unten in diesem Artikel im Abschnitt [Starten und Initialisieren des Speicheremulators](#start-and-initialize-the-storage-emulator).

Der Speicheremulator stellt über die Windows-Authentifizierung eine Verbindung mit SQL Server oder LocalDB her.

Es bestehen einige Unterschiede in der Funktionsweise zwischen dem Speicheremulator und den Azure Storage-Diensten. Weitere Informationen zu diesen Unterschieden finden Sie im Abschnitt [Unterschiede zwischen dem Speicheremulator und Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) weiter unten in diesem Artikel.

## <a name="start-and-initialize-the-storage-emulator"></a>Starten und Initialisieren des Speicheremulators

So starten Sie den Azure-Speicheremulator

1. Wählen Sie die Schaltfläche **Start** aus, oder drücken Sie die **Windows-Taste**.
2. Geben Sie `Azure Storage Emulator` ein.
3. Wählen Sie den Emulator in der Liste der angezeigten Anwendungen aus.

Beim Start des Speicheremulators wird ein Eingabeaufforderungsfenster angezeigt. Über dieses Konsolenfenster kann der Speicheremulator gestartet und beendet werden. Darüber hinaus können Sie über die Eingabeaufforderung Daten löschen, den Status abrufen und den Emulator initialisieren. Weitere Informationen finden Sie im Abschnitt [Referenz zum Speicheremulator-Befehlszeilentool](#storage-emulator-command-line-tool-reference) weiter unten in diesem Artikel.

> [!NOTE]
> Der Azure-Speicheremulator startet möglicherweise nicht ordnungsgemäß, wenn im System ein anderer Speicheremulator (beispielsweise Azurite) aktiv ist.

Wenn der Emulator ausgeführt wird, wird im Infobereich der Windows-Taskleiste ein Symbol angezeigt.

Wenn Sie das Eingabeaufforderungsfenster des Speicheremulators schließen, wird er dennoch weiterhin ausgeführt. Wiederholen Sie die Schritte oben zum Starten des Speicheremulators, um sein Konsolenfenster wieder anzuzeigen.

Wenn Sie den Speicheremulator zum ersten Mal ausführen, wird die lokale Speicherumgebung für Sie initialisiert. Durch die Initialisierung wird in LocalDB eine Datenbank erstellt, und es werden für jeden lokalen Speicherdienst HTTP-Ports reserviert.

Der Speicheremulator wird standardmäßig unter `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` installiert.

> [!TIP]
> Im [Microsoft Azure Storage-Explorer](https://storageexplorer.com) können Sie mit lokalen Speicheremulatorressourcen arbeiten. Suchen Sie dazu, nachdem Sie den Speicheremulator installiert und gestartet haben, in der Struktur der Storage-Explorer-Ressourcen unter „Lokal & angefügt“ nach „(Emulator – Standardports) (Schlüssel)“.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initialisieren des Speicheremulators zur Verwendung einer anderen SQL-Datenbank

Sie können das Speicheremulator-Befehlszeilentool zum Initialisieren des Speicheremulators verwenden, damit dieser auf eine andere SQL-Datenbankinstanz als die Standardinstanz von LocalDB verweist:

1. Öffnen Sie das Konsolenfenster des Speicheremulators gemäß der Beschreibung im Abschnitt [Starten und Initialisieren des Speicheremulators](#start-and-initialize-the-storage-emulator).
1. Geben Sie im Konsolenfenster den folgenden Befehl ein, wobei `<SQLServerInstance>` der Name der SQL Server-Instanz ist. Geben Sie `(localdb)\MSSQLLocalDb` als SQL Server-Instanz ein, um LocalDB zu verwenden.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Mit dem folgenden Befehl können Sie den Emulator anweisen, die SQL Server-Standardinstanz zu verwenden:

   `AzureStorageEmulator.exe init /server .`

   Sie können auch den folgenden Befehl verwenden, mit dem die Datenbank als Standardinstanz von LocalDB initialisiert wird:

   `AzureStorageEmulator.exe init /forceCreate`

Weitere Informationen zu diesen Befehlen finden Sie unter [Referenz zum Speicheremulator-Befehlszeilentool](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Mit [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) können Sie Ihre SQL Server-Instanzen, einschließlich der LocalDB-Installation, verwalten. Geben Sie im SMSS-Dialogfeld **Verbindung mit Server herstellen** im Feld **Servername:** den Namen `(localdb)\MSSQLLocalDb` an, um eine Verbindung mit der LocalDB-Instanz herzustellen.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Authentifizieren von Anforderungen an den Speicheremulator

Nachdem Sie den Speicheremulator installiert und gestartet haben, können Sie den Code testen. Jede an den Speicheremulator gerichtete Anforderung muss autorisiert werden (es sei denn, es handelt sich um eine anonyme Anforderung). Sie können Anforderungen an den Speicheremulator mittels Authentifizierung mit einem gemeinsam verwendeten Schlüssel oder mit einer Shared Access Signature (SAS) autorisieren.

### <a name="authorize-with-shared-key-credentials"></a>Autorisieren mit Benutzeranmeldeinformationen eines gemeinsam verwendeten Schlüssels

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](./storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorisieren mit einer SAS (Shared Access Signature)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Einige Azure Storage-Clientbibliotheken, wie z. B. die Xamarin-Bibliothek, unterstützen nur Authentifizierung mit einem SAS (Shared Access Signature)-Token. Sie können das SAS-Token mit dem [Storage-Explorer](https://storageexplorer.com/) oder einer anderen Anwendung erstellen, die die Authentifizierung mit gemeinsam verwendetem Schlüssel unterstützt.

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

Weitere Informationen zu SAS (Shared Access Signatures) finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adressieren von Ressourcen im Speicheremulator

Die Dienstendpunkte für den Speicheremulator unterscheiden sich von den Endpunkten für ein Azure-Speicherkonto. Der lokale Computer führt keine Domänennamenauflösung durch, weshalb es sich bei den Speicheremulator-Endpunkten um lokale Adressen handeln muss.

Verwenden Sie das folgende Schema, wenn Sie eine Ressource in einem Azure Storage-Konto adressieren. Hierbei ist der Kontoname Teil des URI-Hostnamens, und die adressierte Ressource ist Teil des URI-Pfads:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Der folgende URI ist z. B. eine gültige Adresse für ein Blob in einem Azure-Speicherkonto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Da der lokale Computer keine Domänennamenauflösung durchführt, ist der Kontoname Teil des URI-Pfads und nicht des Hostnamens. Verwenden Sie das folgende URI-Format für eine Ressource im Speicheremulator:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Beispielsweise kann für den Zugriff auf ein Blob im Speicheremulator die folgende Adresse verwendet werden:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Die Dienstendpunkte für den Speicheremulator sind:

* Blob-Dienst: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Warteschlangendienst: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Tabellenspeicherdienst: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adressieren des sekundären Kontos mit RA-GRS

Ab Version 3.1 unterstützt der Speicheremulator georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Sie können auf den sekundären Speicherort zugreifen, indem Sie „-secondary“ an den Kontonamen anhängen. Beispielsweise kann die folgende Adresse für den Zugriff auf ein Blob mithilfe des sekundären Speicherorts mit Lesezugriff im Speicheremulator verwendet werden:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. Weitere Informationen finden Sie in der [Microsoft Azure-Speicherclientbibliothek für .NET](/previous-versions/azure/dn261237(v=azure.100)) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referenz zum Speicheremulator-Befehlszeilentool

Ab Version 3.0 wird beim Starten des Speicheremulators ein Konsolenfenster angezeigt. Verwenden Sie die Befehlszeile im Konsolenfenster, um den Emulator zu starten und zu beenden. Über die Befehlszeile können Sie auch den Status abfragen und andere Vorgänge ausführen.

> [!NOTE]
> Falls Sie den Microsoft Azure-Serveremulator installiert haben, wird beim Start des Speicheremulators ein Taskleistensymbol angezeigt. Klicken Sie mit der rechten Maustaste auf das Symbol, um ein Menü zu öffnen, das eine grafische Möglichkeit bietet, den Speicheremulator zu starten und zu beenden.
>
>

### <a name="command-line-syntax"></a>Befehlszeilensyntax

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Tastatur

Geben Sie zum Anzeigen der Liste der Optionen an der Eingabeaufforderung `/help` ein.

| Option | BESCHREIBUNG | Befehl | Argumente |
| --- | --- | --- | --- |
| **Starten** |Startet den Speicheremulator. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocess*: Startet den Emulator im aktuellen Prozess, anstatt einen neuen Prozess zu erstellen. |
| **Beenden** |Beendet den Speicheremulator. |`AzureStorageEmulator.exe stop` | |
| **Status** |Zeigt den Status des Speicheremulators an. |`AzureStorageEmulator.exe status` | |
| **Clear** |Löscht die Daten in allen Diensten, die an der Befehlszeile angegeben werden. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: Löscht Blobdaten. <br/>*queue*: Löscht Warteschlangendaten. <br/>*table*: Löscht Tabellendaten. <br/>*all*: Löscht sämtliche Daten in allen Diensten. |
| **Init** |Führt eine einmalige Initialisierung zur Einrichtung des Emulators durch. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Gibt den Server an, der die SQL-Instanz hostet. <br/>*-sqlinstance instanceName*: Gibt den Namen der zu verwendenden SQL-Instanz in der Standardserverinstanz an. <br/>*-forcecreate*: Erzwingt die Erstellung der SQL-Datenbank, auch wenn diese bereits vorhanden ist. <br/>*-skipcreate*: Überspringt das Erstellen der SQL-Datenbank. Dies hat Vorrang vor „-forcecreate“.<br/>*-reserveports*: Versucht, die den Diensten zugeordneten HTTP-Ports zu reservieren.<br/>*-unreserveports*: Versucht, Reservierungen für die den Diensten zugeordneten HTTP-Ports zu entfernen. Dies hat Vorrang vor „-reserveports“.<br/>*-inprocess*: Führt die Initialisierung im aktuellen Prozess aus, anstatt einen neuen Prozess zu erstellen. Der aktuelle Prozess muss mit erhöhten Rechten gestartet werden, wenn Portreservierungen geändert werden. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Unterschiede zwischen dem Speicheremulator und Azure Storage

Da der Speicheremulator eine lokale emulierte Umgebung ist, gibt es Unterschiede zwischen der Verwendung des Emulators und der Verwendung eines Azure-Speicherkontos in der Cloud:

* Der Speicheremulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel.
* Der Speicheremulator ist kein skalierbarer Speicherdienst und unterstützt keine große Anzahl gleichzeitiger Clients.
* Wie in [Adressieren von Ressourcen im Speicheremulator](#addressing-resources-in-the-storage-emulator)beschrieben, werden Ressourcen im Speicheremulator im Vergleich zu einem Azure-Speicherkonto anders adressiert. Der Unterschied ist darauf zurückzuführen, dass die Domänennamenauflösung zwar in der Cloud, nicht aber auf dem lokalen Computer verfügbar ist.
* Ab Version 3.1 unterstützt das Speicheremulatorkonto georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Im Emulator ist für alle Konten RA-GRS aktiviert, und es entsteht niemals eine Verzögerung zwischen den primären und sekundären Replikaten. Die Vorgänge "Get Blob Service Stats", "Get Queue Service Stats" und "Get Table Service Stats" werden für das sekundäre Konto unterstützt. Sie geben immer den Wert des `LastSyncTime`-Antwortelements als aktuelle Uhrzeit der zugrunde liegenden SQL-Datenbank zurück.
* Die Endpunkte für Dateidienst und SMB-Protokolldienst werden im Speicheremulator zurzeit nicht unterstützt.
* Wenn Sie eine Version der Speicherdienste verwenden, die vom Emulator nicht unterstützt wird, gibt der Emulator den Fehler „VersionNotSupportedByEmulator“ (HTTP-Statuscode 400: ungültige Anforderung) zurück.

### <a name="differences-for-blob-storage"></a>Unterschiede beim Blob-Speicher

Die folgenden Unterschiede gelten für Blob-Speicher im Emulator:

* Der Speicheremulator unterstützt nur Blob-Größen bis 2 GB.
* Die maximale Länge eines Blobnamens im Speicheremulator ist 256 Zeichen, während sie in Azure Storage 1024 Zeichen beträgt.
* Bei inkrementellen Kopien können Momentaufnahmen von überschriebenen Blobs kopiert werden, wodurch ein Fehler für den Dienst zurückgegeben wird.
* „Get Page Ranges Diff“ funktioniert nicht zwischen Momentaufnahmen, die per inkrementeller Blobkopie kopiert wurden.
* Ein Vorgang vom Typ „Put Blob“ kann ggf. bei einem Blob, das im Speicheremulator vorhanden ist und über eine aktive Lease verfügt, erfolgreich ausgeführt werden, auch wenn die Lease-ID in der Anforderung nicht angegeben wurde.
* Vorgänge für Anfügeblobs werden vom Emulator nicht unterstützt. Beim Versuch, einen Vorgang auf einen Anfügeblob anzuwenden, wird der Fehler „FeatureNotSupportedByEmulator“ (HTTP-Statuscode 400 – Ungültige Anforderung) zurückgegeben.

### <a name="differences-for-table-storage"></a>Unterschiede beim Tabellenspeicher

Die folgenden Unterschiede gelten für Tabellenspeicher im Emulator:

* Datumseigenschaften im Tabellenspeicherdienst im Speicheremulator unterstützen nur den von SQL Server 2005 unterstützten Bereich. (Sie müssen nach dem 1. Januar 1753 liegen.) Alle Datumsangaben vor dem 1. Januar 1753 werden in diesen Wert geändert. Die Genauigkeit der Daten ist begrenzt auf die Genauigkeit von SQL Server 2005, d. h., Datumsangaben sind auf 1/300 Sekunde genau.
* Der Speicheremulator unterstützt Eigenschaftenwerte für Partitions- und Zeilenschlüssel von jeweils weniger als 512 Byte. Die Gesamtgröße von Kontoname, Tabellenname und Schlüsseleigenschaftennamen darf maximal 900 Bytes betragen.
* Die Gesamtgröße einer Zeile in einer Tabelle im Speicheremulator ist auf weniger als 1 MB beschränkt.
* Im Speicheremulator unterstützen Eigenschaften des Datentyps `Edm.Guid` oder `Edm.Binary` in Filterzeichenfolgen für Abfragen nur die Vergleichsoperatoren `Equal (eq)` und `NotEqual (ne)`.

### <a name="differences-for-queue-storage"></a>Unterschiede beim Warteschlangenspeicher

Es bestehen keine Unterschiede beim Warteschlangenspeicher im Emulator.

## <a name="storage-emulator-release-notes"></a>Speicheremulator – Versionshinweise

### <a name="version-510"></a>Version 5.10

* Der Speicheremulator lehnt die Version vom 07.07.2019 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte nicht ab.

### <a name="version-59"></a>Version 5.9

* Der Speicheremulator lehnt die Version vom 02.02.2019 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte nicht ab.

### <a name="version-58"></a>Version 5.8

* Der Speicheremulator lehnt die Version vom 09.11.2018 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte nicht ab.

### <a name="version-57"></a>Version 5.7

* Korrektur eines Fehlers, der zu einem Absturz führen würde, wenn die Protokollierung aktiviert ist.

### <a name="version-56"></a>Version 5.6

* Der Speicheremulator unterstützt nun die Version vom 28.03.2018 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.

### <a name="version-55"></a>Version 5.5

* Der Speicheremulator unterstützt nun die Version vom 09.11.2017 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.
* Unterstützung für die Blobeigenschaft **Created**, die die Bloberstellungszeit zurückgibt, wurde hinzugefügt.

### <a name="version-54"></a>Version 5.4

* Zur Verbesserung der Stabilität der Installation versucht der Emulator nicht mehr, Ports während der Installationszeit zu reservieren. Portreservierungen können bei Bedarf mithilfe der Option *-reserveports* des Befehls **init** angegeben werden.

### <a name="version-53"></a>Version 5.3

* Der Speicheremulator unterstützt nun die Version vom 29.07.2017 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.

### <a name="version-52"></a>Version 5.2

* Der Speicheremulator unterstützt nun die Version vom 17.04.2017 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.
* Es wurde ein Fehler behoben, aufgrund dessen Tabelleneigenschaftswerte nicht ordnungsgemäß codiert wurden.

### <a name="version-51"></a>Version 5.1

* Es wurde ein Fehler behoben, bei dem der Speicheremulator in einigen Antworten den Header `DataServiceVersion` zurückgegeben hat, während der Dienst dies nicht getan hat.

### <a name="version-50"></a>Version 5.0

* Das Installationsprogramm des Speicheremulators führt keine Überprüfungen auf vorhandene MSSQL- und .NET Framework-Installationen mehr durch.
* Das Installationsprogramm des Speicheremulators erstellt die Datenbank nicht mehr als Teil der Installation. Die Datenbank wird bei Bedarf immer noch als Teil des Starts erstellt.
* Die Datenbankerstellung erfordert keine Rechteerweiterungen mehr.
* Portreservierungen werden für den Start nicht mehr benötigt.
* Die folgenden Optionen wurden zu `init` hinzugefügt: `-reserveports` (erfordert Rechteerweiterungen), `-unreserveports` (erfordert Rechteerweiterungen), `-skipcreate`.
* Über die Speicheremulator-Benutzeroberflächenoption des Taskleistensymbols wird nun die Befehlszeilenschnittstelle gestartet. Die alte GUI ist nicht mehr verfügbar.
* Einige DLLs wurden entfernt oder umbenannt.

### <a name="version-46"></a>Version 4.6

* Der Speicheremulator unterstützt nun die Version vom 31.05.2016 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.

### <a name="version-45"></a>Version 4.5

* Es wurde ein Fehler behoben, der dazu führte, dass der Speicheremulator nicht erfolgreich installiert und initialisiert werden konnte, wenn die Sicherungsdatenbank umbenannt wurde.

### <a name="version-44"></a>Version 4.4

* Der Speicheremulator unterstützt nun die Version vom 11.12.2015 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.
* Der Speicheremulator verfügt nun über eine effizientere Garbage Collection für die Verarbeitung zahlreicher Blobs.
* Ein Fehler wurde behoben, durch den die Validierung des Container-ACL-XML-Codes nicht exakt der Validierung durch den Speicherdienst entsprach.
* Ein Fehler, aufgrund dessen die Mindest- und Höchstwerte für „DateTime“ manchmal in der falschen Zeitzone gemeldet wurden, wurde behoben.

### <a name="version-43"></a>Version 4.3

* Der Speicheremulator unterstützt nun die Version vom 08.07.2015 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.

### <a name="version-42"></a>Version 4.2

* Der Speicheremulator unterstützt nun die Version vom 05.04.2015 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte.

### <a name="version-41"></a>Version 4.1

* Der Speicheremulator unterstützt nun die Version vom 21.02.2015 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte. Die neuen Anfügeblob-Features werden nicht unterstützt.
* Der Emulator gibt nun eine sinnvolle Fehlermeldung für nicht unterstützte Versionen von Speicherdiensten zurück. Es wird empfohlen, die neueste Version des Emulators zu verwenden. Wenn Sie den Fehler „VersionNotSupportedByEmulator“ (HTTP-Statuscode 400: ungültige Anforderung) erhalten, laden Sie die neueste Version des Emulators herunter.
* Es wurde ein Fehler behoben, bei der eine Racebedingung während der gleichzeitigen Zusammenführungsvorgänge zu falschen Tabellenentitätsdaten geführt hat.

### <a name="version-40"></a>Version 4.0

* Die ausführbare Datei des Speicheremulators wurde in *AzureStorageEmulator.exe* umbenannt.

### <a name="version-32"></a>Version 3.2

* Der Speicheremulator unterstützt nun die Version vom 14.02.2014 der Speicherdienste für Blob-, Warteschlangen- und Tabellenspeicherdienst-Endpunkte. Endpunkte für den Dateidienst werden im Speicheremulator derzeit nicht unterstützt. Ausführliche Informationen über die Version 2014-02-14 finden Sie unter [Versionsverwaltung für den Blob-Dienst, den Warteschlangendienst und den Tabellendienst in Microsoft Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>Version 3.1

* Lesezugriff auf den geografisch redundanten Speicher (RA-GRS) wird nun im Speicheremulator unterstützt. Die APIs `Get Blob Service Stats`, `Get Queue Service Stats` und `Get Table Service Stats` werden für das sekundäre Konto unterstützt und geben immer den Wert des LastSyncTime-Antwortelements als aktuelle Uhrzeit der zugrunde liegenden SQL-Datenbank zurück. Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. In der Referenz zur Microsoft Azure Storage-Clientbibliothek für .NET finden Sie weitere Einzelheiten.

### <a name="version-30"></a>Version 3.0

* Der Azure-Speicheremulator wird nicht mehr im gleichen Paket wie der Serveremulator ausgeliefert.
* Die grafische Benutzeroberfläche des Speicheremulators ist veraltet. Sie wurde durch eine skriptfähige Befehlszeilenschnittstelle ersetzt. Ausführliche Informationen zur Befehlszeilenschnittstelle finden Sie in der Referenz zum Speicheremulator-Befehlszeilentool. Die grafische Benutzeroberfläche wird weiterhin in Version 3.0 enthalten sein, es kann aber nur sie zugegriffen werden, wenn der Serveremulator installiert ist, indem Sie mit der rechten Maustaste auf das Taskleistensymbol klicken und die Speicheremulator-Benutzeroberfläche auswählen.
* Version 2013-08-15 der Azure Storage-Dienste wird jetzt vollständig unterstützt. (Zuvor wurde diese Version nur von Version 2.2.1 Preview des Speicheremulators unterstützt.)

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich den plattformübergreifenden, von der Community gepflegten Open-Source-Speicheremulator [Azurite](https://github.com/azure/azurite) an. 
* [Azure Storage-Beispiele mit .NET](./storage-samples-dotnet.md) enthält Links zu mehreren Codebeispielen, die Sie bei der Anwendungsentwicklung verwenden können.
* Im [Microsoft Azure Storage-Explorer](https://storageexplorer.com) können Sie mit Ressourcen in Ihrem Cloudspeicherkonto und im Speicheremulator arbeiten.

## <a name="see-also"></a>Weitere Informationen

* [Lokale Azure Storage-Entwicklung mit Azurite, Azure SDKs und Azure Storage-Explorer](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
