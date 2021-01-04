---
title: PowerShell-Beispielskripts
description: Enthält Beispiele, die veranschaulichen, wie Sie das Front-End mit PowerShell-Skripts nutzen.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb8cc98a020cb382a6941c1e410eab4543594629
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009754"
---
# <a name="example-powershell-scripts"></a>PowerShell-Beispielskripts

Azure Remote Rendering verfügt über die beiden folgenden REST-APIs:

- [REST-API für die Konvertierung](../how-tos/conversion/conversion-rest-api.md)
- [REST-API für Sitzungen](../how-tos/session-rest-api.md)

Das [Repository mit den ARR-Beispielen](https://github.com/Azure/azure-remote-rendering) enthält im Ordner *Scripts* Beispielskripts für die Interaktion mit den REST-APIs des Diensts. In diesem Artikel wird die Nutzung beschrieben.

> [!TIP]
> Ferner ist ein [Tool mit grafischer Benutzeroberfläche unter dem Namen ARRT](azure-remote-rendering-asset-tool.md) für die Interaktion mit dem Dienst verfügbar, das eine komfortable Alternative zum Verwenden von Skripts darstellt. ![ARRT](./media/azure-remote-rendering-asset-tool.png "ARRT-Screenshot")

> [!CAUTION]
> Zu häufiges Aufrufen von REST-API-Befehlen bewirkt, dass der Server eine Drosselung durchführt und schließlich einen Fehler zurückgibt. Die HTTP-Fehlercode-ID ist in diesem Fall 429 („zu viele Anforderungen“). Als Faustregel sollte eine Verzögerung von **5–10 Sekunden zwischen nachfolgenden Aufrufen** erfolgen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der Beispielskripts benötigen Sie eine funktionsfähige [Azure PowerShell](/powershell/azure/)-Instanz.

1. Installieren Sie Azure PowerShell:
    1. Öffnen Sie ein PowerShell-Fenster mit Administratorrechten.
    1. Führen Sie `Install-Module -Name Az -AllowClobber` aus.

1. Vergewissern Sie sich, dass Ihre [Ausführungsrichtlinie](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) richtig festgelegt wurde, falls Sie beim Ausführen von Skripts Fehler erhalten:
    1. Öffnen Sie ein PowerShell-Fenster mit Administratorrechten.
    1. Führen Sie `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` aus.

1. [Vorbereiten von Azure Storage-Konten](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Melden Sie sich bei Ihrem Abonnement an, unter dem sich Ihr Azure Remote Rendering-Konto befindet:
    1. Öffnen Sie ein PowerShell-Fenster.
    1. Führen Sie `Connect-AzAccount` aus, und befolgen Sie die Anweisungen auf dem Bildschirm.

    > [!NOTE]
    > Falls Ihre Organisation über mehrere Abonnements verfügt, müssen Sie ggf. die Argumente für die Abonnement-ID und den Mandanten angeben. Weitere Informationen finden Sie in der [Dokumentation zu „Connect-AzAccount“](/powershell/module/az.accounts/connect-azaccount).

1. Laden Sie den Ordner *Scripts* aus dem [Azure Remote Rendering GitHub-Repository](https://github.com/Azure/azure-remote-rendering) herunter.

## <a name="configuration-file"></a>Konfigurationsdatei

Neben den `.ps1`-Dateien befindet sich das Element `arrconfig.json`, das Sie ausfüllen müssen:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Ersetzen Sie die umgekehrten Schrägstriche im Pfad „LocalAssetDirectoryPath“, indem Sie doppelte umgekehrte Schrägstriche (\\\\) als Escapezeichen verwenden. Verwenden Sie in allen anderen Pfaden, z. B. „inputFolderPath“ und „inputAssetPath“ reguläre Schrägstriche (/).

> [!CAUTION]
> Optionale Werte müssen ausgefüllt werden, oder Sie müssen den Schlüssel und den Wert vollständig entfernen. Wenn Sie z. B. nicht den Parameter `"outputAssetFileName"` verwenden, müssen Sie die gesamte Zeile in `arrconfig.json` löschen.

### <a name="accountsettings"></a>accountSettings

Informationen zu `arrAccountId` und `arrAccountKey` finden Sie unter [Erstellen eines Azure Remote Rendering-Kontos](../how-tos/create-an-account.md).
Informationen zu `region` finden Sie in der [Liste mit den verfügbaren Regionen](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Diese Struktur muss ausgefüllt werden, wenn Sie **RenderingSession.ps1** ausführen möchten:

- **vmSize:** Dient zum Auswählen der Größe des virtuellen Computers. Sie können zwischen [*standard*](../reference/vm-sizes.md) und [*premium*](../reference/vm-sizes.md) wählen. Beenden Sie Renderingsitzungen, wenn Sie sie nicht mehr benötigen.
- **maxLeaseTime:** Der Zeitraum, wie lange Sie die VM leasen möchten. Sie wird heruntergefahren, nachdem die Lease abgelaufen ist. Die Leasedauer kann später verlängert werden (siehe unten).

### <a name="assetconversionsettings"></a>assetConversionSettings

Diese Struktur muss ausgefüllt werden, wenn Sie **Conversion.ps1** ausführen möchten.

Ausführliche Informationen finden Sie unter [Vorbereiten von Azure Storage-Konten](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Skript: RenderingSession.ps1

Mit diesem Skript werden Renderingsitzungen erstellt, abgefragt und beendet.

> [!IMPORTANT]
> Vergewissern Sie sich, dass Sie in „arrconfig.json“ die Abschnitte *accountSettings* und *renderingSessionSettings* ausgefüllt haben.

### <a name="create-a-rendering-session"></a>Erstellen einer Renderingsitzung

Übliche Nutzung mit vollständig ausgefüllter Datei „arrconfig.json“:

```PowerShell
.\RenderingSession.ps1
```

Mit dem Skript wird die [REST-API für die Sitzungsverwaltung](../how-tos/session-rest-api.md) aufgerufen, um einen virtuellen Renderingcomputer mit den angegebenen Einstellungen zu starten. Bei erfolgreicher Ausführung wird die *sessionId* abgerufen. Anschließend werden die Sitzungseigenschaften abgefragt, bis die Sitzung bereit ist oder ein Fehler aufgetreten ist.

Gehen Sie wie folgt vor, um eine **andere Konfigurationsdatei** zu verwenden:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Sie können **einzelne Einstellungen der Konfigurationsdatei außer Kraft setzen**:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Sie können Folgendes verwenden, um eine **Sitzung ohne Abruf zu starten**:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

Die *sessionId*, die mit dem Skript abgerufen wird, muss an die meisten anderen Sitzungsbefehle übergeben werden.

### <a name="retrieve-session-properties"></a>Abrufen von Sitzungseigenschaften

Führen Sie Folgendes aus, um die Eigenschaften einer Sitzung abzurufen:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Verwenden Sie `-Poll`, wenn gewartet werden soll, bis die Sitzung *bereit* ist oder ein Fehler aufgetreten ist.

### <a name="list-active-sessions"></a>Auflisten der aktiven Sitzungen

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Beenden einer Sitzung

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Ändern von Sitzungseigenschaften

Derzeit wird nur die Änderung des maxLeaseTime-Elements einer Sitzung unterstützt.

> [!NOTE]
> Die Leasedauer beginnt immer ab dem Zeitpunkt, zu dem die Sitzungs-VM ursprünglich erstellt wurde. Erhöhen Sie also *maxLeaseTime* um eine Stunde, um die Leasedauer für die Sitzung um eine Stunde zu verlängern.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Skript: Conversion.ps1

Dieses Skript wird verwendet, um Eingabemodelle in das spezifische Runtimeformat für Azure Remote Rendering zu konvertieren.

> [!IMPORTANT]
> Vergewissern Sie sich, dass Sie in „arrconfig.json“ die Abschnitte *accountSettings* und *assetConversionSettings* ausgefüllt haben.

Mit dem Skript werden die beiden Optionen für die Nutzung von Speicherkonten für den Dienst veranschaulicht:

- Mit dem Azure Remote Rendering-Konto verknüpftes Speicherkonto
- Gewähren des Zugriffs auf Speicher über Shared Access Signatures (SAS)

### <a name="linked-storage-account"></a>Verknüpftes Speicherkonto

Nachdem Sie „arrconfig.json“ vollständig ausgefüllt und ein Speicherkonto verknüpft haben, können Sie den folgenden Befehl verwenden. Die Verknüpfung Ihres Speicherkontos ist unter [Erstellen eines Kontos](../how-tos/create-an-account.md#link-storage-accounts) beschrieben.

Die Verwendung eines verknüpften Speicherkontos ist die bevorzugte Vorgehensweise zur Nutzung des Konvertierungsdiensts, da keine Shared Access Signatures generiert werden müssen.

```PowerShell
.\Conversion.ps1
```

1. Laden Sie alle in `assetConversionSettings.modelLocation` enthaltenen Dateien in den Eingabeblobcontainer hoch, der sich unter dem angegebenen `inputFolderPath` befindet.
1. Rufen Sie die [REST-API für die Modellkonvertierung](../how-tos/conversion/conversion-rest-api.md) auf, um die [Modellkonvertierung](../how-tos/conversion/model-conversion.md) zu starten.
1. Rufen Sie den Konvertierungsstatus ab, bis die Konvertierung erfolgreich abgeschlossen wurde oder ein Fehler aufgetreten ist.
1. Geben Sie die Details des Speicherorts der konvertierten Datei aus (Speicherkonto, Ausgabecontainer, Dateipfad im Container).

### <a name="access-to-storage-via-shared-access-signatures"></a>Zugriff auf Speicher über Shared Access Signatures

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Dies bewirkt Folgendes:

1. Hochladen der lokalen Datei aus `assetConversionSettings.localAssetDirectoryPath` in den Eingabeblobcontainer
1. Generieren eines SAS-URI für den Eingabecontainer
1. Generieren eines SAS-URI für den Ausgabecontainer
1. Aufrufen der [REST-API für die Modellkonvertierung](../how-tos/conversion/conversion-rest-api.md), um die [Modellkonvertierung](../how-tos/conversion/model-conversion.md) zu starten
1. Abrufen des Konvertierungsstatus, bis die Konvertierung erfolgreich abgeschlossen wurde oder ein Fehler aufgetreten ist
1. Ausgeben der Details des Speicherorts der konvertierten Datei (Speicherkonto, Ausgabecontainer, Dateipfad im Container)
1. Ausgeben eines SAS-URI für das konvertierte Modell im Ausgabeblobcontainer

### <a name="additional-command-line-options"></a>Weitere Befehlszeilenoptionen

Gehen Sie wie folgt vor, um eine **andere Konfigurationsdatei** zu verwenden:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Sie können Folgendes verwenden, um nur die **Modellkonvertierung ohne Abruf zu starten**:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Sie können in der Konfigurationsdatei **einzelne Einstellungen außer Kraft setzen**, indem Sie die folgenden Befehlszeilenschalter verwenden:

* **Id:** Für „GetConversionStatus“ verwendete „ConversionId“
* **ArrAccountId:** „arrAccountId“ von „accountSettings“
* **ArrAccountKey:** Außerkraftsetzung für „arrAccountKey“ von „accountSettings“
* **Region:** Außerkraftsetzung der Region von „accountSettings“
* **ResourceGroup:** Außerkraftsetzung für „resourceGroup“ von „assetConversionSettings“
* **StorageAccountName:** Außerkraftsetzung für „storageAccountName“ von „assetConversionSettings“
* **BlobInputContainerName:** Außerkraftsetzung für „blobInputContainer“ von „assetConversionSettings“
* **LocalAssetDirectoryPath:** Außerkraftsetzung für „localAssetDirectoryPath“ von „assetConversionSettings“
* **InputAssetPath:** Außerkraftsetzung für „inputAssetPath“ von „assetConversionSettings“
* **BlobOutputContainerName:** Außerkraftsetzung für „blobOutputContainerName“ von „assetConversionSettings“
* **OutputFolderPath:** Außerkraftsetzung für „outputFolderPath“ von „assetConversionSettings“
* **OutputAssetFileName:** Außerkraftsetzung für „outputAssetFileName“ von „assetConversionSettings“

Beispielsweise können Sie einige vorhandene Optionen wie folgt kombinieren:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Ausführen der einzelnen Konvertierungsstufen

Sie können Folgendes verwenden, wenn Sie einzelne Schritte des Prozesses ausführen möchten:

Hochladen von Daten nur für „LocalAssetDirectoryPath“

```PowerShell
.\Conversion.ps1 -Upload
```

Ausschließliches Starten des Konvertierungsprozesses für ein Modell, das bereits in Blobspeicher hochgeladen wurde (kein Ausführen von „Hochladen“, kein Abruf des Konvertierungsstatus). Das Skript gibt eine *conversionId* aus.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Sie können den Status für diese Konvertierung wie folgt abrufen:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Verwenden Sie `-Poll`, wenn gewartet werden soll, bis die Konvertierung abgeschlossen oder ein Fehler aufgetreten ist.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Rendern eines Modells mit Unity](../quickstarts/render-model.md)
- [Schnellstart: Konvertieren eines Modells für das Rendering](../quickstarts/convert-model.md)
- [Modellkonvertierung](../how-tos/conversion/model-conversion.md)