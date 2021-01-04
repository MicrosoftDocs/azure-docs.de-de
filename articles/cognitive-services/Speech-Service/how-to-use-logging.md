---
title: Speech SDK-Protokollierung – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Anmeldung im Speech SDK (C++, C#, Python, Objective-C, Java) aktivieren.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f044596cf084e68b6c9ca10e3da4fbdf4c8c2062
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026521"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Aktivieren der Protokollierung im Speech SDK

Die Protokollierung in eine Datei ist ein optionales Feature für das Speech SDK. Während der Entwicklung bietet die Protokollierung zusätzliche Informationen und Diagnosen aus den Hauptkomponenten des Speech SDK. Dies kann aktiviert werden, indem die Eigenschaft `Speech_LogFilename` für ein Sprachkonfigurationsobjekt auf den Speicherort und den Namen der Protokolldatei festgelegt wird. Die Protokollierung wird global aktiviert, sobald eine Erkennung aus dieser Konfiguration erstellt wird, und sie kann danach nicht mehr deaktiviert werden. Sie können den Namen einer Protokolldatei während einer aktiven Protokollierungssitzung nicht ändern.

> [!NOTE]
> Die Protokollierung ist seit Version 1.4.0 des Speech SDK in allen unterstützten Programmiersprachen verfügbar, mit Ausnahme von JavaScript.

## <a name="sample"></a>Beispiel

Der Name der Protokolldatei wird über ein Konfigurationsobjekt angegeben. Wenn Sie das `SpeechConfig` als Beispiel nehmen und davon ausgehen, dass Sie eine Instanz namens `config` erstellt haben:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Sie können über das Konfigurationsobjekt eine Erkennung erstellen. Dies aktiviert die Protokollierung für alle Erkennungen.

> [!NOTE]
> Wenn Sie ein `SpeechSynthesizer` aus dem Konfigurationsobjekt erstellen, wird die Protokollierung nicht aktiviert. Wenn die Protokollierung jedoch aktiviert ist, erhalten Sie auch eine Diagnose von `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Erstellen einer Protokolldatei auf verschiedenen Plattformen

Unter Windows oder Linux kann die Protokolldatei in einem beliebigen Pfad gespeichert werden, für den der Benutzer über die Schreibberechtigung verfügt. Schreibberechtigungen für Dateisystemspeicherorte in anderen Betriebssystemen können standardmäßig eingeschränkt oder begrenzt sein.

### <a name="universal-windows-platform-uwp"></a>Universelle Windows-Plattform (UWP)

UWP-Anwendungen müssen Protokolldateien an einem der Speicherorte für Anwendungsdaten (lokal, Roaming oder temporär) speichern. Eine Protokolldatei kann im lokalen Anwendungsordner erstellt werden:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Weitere Informationen zur Dateizugriffsberechtigung für UWP-Anwendungen finden Sie [hier](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Sie können eine Protokolldatei entweder im internen Speicher, im externen Speicher oder im Cacheverzeichnis speichern. Dateien, die im internen Speicher oder im Cacheverzeichnis erstellt werden, sind für die Anwendung privat. Es ist ratsam, eine Protokolldatei im externen Speicher zu erstellen.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Der obige Code speichert eine Protokolldatei im externen Speicher im Stammverzeichnis eines anwendungsspezifischen Verzeichnisses. Ein Benutzer kann mit dem Dateimanager auf die Datei zugreifen (normalerweise in `Android/data/ApplicationName/logfile.txt`). Die Datei wird gelöscht, wenn die Anwendung deinstalliert wird.

Sie müssen auch die Berechtigung `WRITE_EXTERNAL_STORAGE` in der Manifestdatei anfordern:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Weitere Informationen zur Daten- und Dateispeicherung für Android-Anwendungen finden Sie [hier](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Nur Verzeichnisse innerhalb der Sandbox der Anwendung sind zugänglich. Dateien können in den Verzeichnissen für Dokumente, Bibliotheken und temporäre Daten erstellt werden. Dateien im Dokumentenverzeichnis können einem Benutzer zur Verfügung gestellt werden Der folgende Codeausschnitt zeigt die Erstellung einer Protokolldatei im Verzeichnis der Anwendungsdokumente:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Um auf eine erstellte Datei zuzugreifen, fügen Sie die folgenden Eigenschaften der Eigenschaftsliste `Info.plist` der Anwendung hinzu:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Weitere Informationen zum iOS-Dateisystem finden Sie [hier](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkunden unserer Beispiele auf GitHub](https://aka.ms/csspeech/samples)