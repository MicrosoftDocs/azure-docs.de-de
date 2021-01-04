---
title: Dokumentation zum Speech-Geräte-SDK
titleSuffix: Azure Cognitive Services
description: Die Versionshinweise enthalten ein Protokoll über Updates, Verbesserungen, Fehlerbehebungen und Änderungen am Speech-Geräte-SDK. Dieser Artikel wird mit jeder Version des Speech-Geräte-SDK aktualisiert.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: af66f2ec56551a5177cd9323d216e9bf4b0c41be
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021846"
---
# <a name="release-notes-speech-devices-sdk"></a>Versionshinweise: Speech-Geräte-SDK

In den folgenden Abschnitten sind Änderungen in den neuesten Versionen aufgeführt.

## <a name="speech-devices-sdk-1110"></a>Speech Devices SDK 1.11.0:

- Unterstützung von [beliebigen Mikrofonarraygeometrien](how-to-devices-microphone-array-configuration.md) und Festlegen des Arbeitswinkels über eine [Konfigurationsdatei](https://aka.ms/sdsdk-micarray-json).
- Unterstützung für [Urbetter DDK](http://www.urbetter.com/products_56/278.html).
- Die Binärdateien für den [GGEC Speaker](https://aka.ms/sdsdk-download-speaker), der in unserem [Beispiel für den Sprach-Assistenten](https://aka.ms/sdsdk-speaker) verwendet wird, wurden freigegeben.
- Die Binärdateien für [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) und [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64) für Raspberry Pi und ähnliche Geräte wurden freigegeben.
- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.11.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).

## <a name="speech-devices-sdk-190"></a>Speech Devices SDK 1.9.0:

- Ausgangsbinärdateien für [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) werden bereitgestellt.
- Roobo v1 verwendet jetzt Maven für das Speech SDK
- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.9.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).

## <a name="speech-devices-sdk-170"></a>Speech Devices SDK 1.7.0:

- Linux ARM wird jetzt unterstützt.
- Anfängliche Binärdateien für [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) werden bereitgestellt (Linux ARM64).
- Windows-Benutzer können `AudioConfig.fromDefaultMicrophoneInput()` oder `AudioConfig.fromMicrophoneInput(deviceName)` verwenden, um das zu verwendende Mikrofon anzugeben.
- Die Größe der Bibliothek wurde optimiert.
- Unterstützung für die Erkennung mit mehreren Durchläufen unter Verwendung desselben Sprach-/Absichterkennungsobjekts wurde hinzugefügt.
- Beheben Sie gelegentlich auftretende Probleme, bei denen der Prozess nicht mehr reagierte, während die Erkennung beendet wurde.
- Beispiel-Apps enthalten jetzt eine „participants.properties“-Beispieldatei, um das Format der Datei zu veranschaulichen.
- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.7.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).

## <a name="speech-devices-sdk-160"></a>Speech Devices SDK 1.6.0:

- Unterstützung von [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) unter Windows und Linux mit gemeinsamer [Beispielanwendung](./speech-devices-sdk.md)
- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.6.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).

## <a name="speech-devices-sdk-151"></a>Speech Devices SDK 1.5.1:

- [Unterhaltungstranskription](./conversation-transcription.md) wurde in die Beispiel-App aufgenommen.
- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.5.1 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0: Release von Mai 2019

- Das Speech Devices SDK befindet sich nicht mehr in der geschlossenen Vorschau und ist nun allgemein verfügbar.
- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.5.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).
- Eine neue Schlüsselworttechnologie bringt erhebliche Qualitätsverbesserungen mit sich. Informationen finden Sie unter „Wichtige Änderungen“.
- Neue Audioverarbeitungspipeline für Fernfelderkennung

**Wichtige Änderungen**

- Aufgrund der neuen Schlüsselworttechnologie müssen alle Schlüsselwörter in unserem optimierten Schlüsselwortportal neu erstellt werden. Deinstallieren Sie die alte App, um alte Schlüsselwörter vollständig vom Gerät zu entfernen.
  - Deinstallieren Sie „com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp“ mit adb.

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0: Release von April 2019

- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.4.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1: Release März 2019

- Die [Speech SDK](./speech-sdk.md)-Komponente wurde auf Version 1.3.1 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).
- Informationen zur aktualisierten Schlüsselwortverarbeitung finden Sie unter „Wichtige Änderungen“.
- Beispielanwendung fügt die Auswahl der Sprache für die Spracherkennung und Übersetzung hinzu.

**Wichtige Änderungen**

- Das [Installieren eines Schlüsselworts](./custom-keyword-basics.md) wurde vereinfacht und ist jetzt Teil der App. Es ist keine separate Installation auf dem Gerät erforderlich.
- Die Schlüsselworterkennung wurde geändert, und es werden zwei Ereignisse unterstützt.
  - `RecognizingKeyword,` gibt an, dass das Ergebnis der Spracherkennung (nicht überprüften) Schlüsselworttext enthält.
  - `RecognizedKeyword` gibt an, dass die Schlüsselworterkennung abgeschlossen und das angegebene Schlüsselwort erkannt wurde.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0: Version 2018-Nov

- Die [Speech-SDK](./speech-sdk.md)-Komponente wurde auf Version 1.1.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).
- Die Spracherkennungsgenauigkeit im Fernfeld wird mit unserem erweiterten Audioverarbeitungsalgorithmus verbessert.
- Der Beispielanwendung wurde Unterstützung für die Erkennung von Chinesisch hinzugefügt.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1: Version 2018-Oct

- [Speech-SDK](./speech-sdk.md)-Komponente wurde auf Version 1.0.1 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](./releasenotes.md).
- Die Spracherkennungsgenauigkeit wird mit unserem verbesserten Audioverarbeitungsalgorithmus verbessert.
- Ein Fehler bei der kontinuierlichen Erkennung von Audiositzungen wurde behoben.

**Wichtige Änderungen**

- Mit diesem Release werden einige Breaking Changes eingeführt. Ausführliche Informationen zu den APIs finden Sie auf [dieser Seite](https://aka.ms/csspeech/breakingchanges_1_0_0).
- Die KWS-Modelldateien sind nicht mit dem Speech-Geräte-SDK 1.0.1 kompatibel. Vorhandene Schlüsselwortdateien werden nach dem Schreiben der neuen Schlüsselwortdateien auf dem Gerät gelöscht.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0: Version 2018-Aug

- Die Genauigkeit der Spracherkennung wurde verbessert, indem ein Fehler im Code für die Audiodatenverarbeitung behoben wurde.
- [Speech-SDK](./speech-sdk.md)-Komponente wurde auf Version 0.5.0 aktualisiert. Weitere Informationen finden Sie in den [Versionshinweisen](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733: Release von Mai 2018

Die erste öffentliche Vorschauversion des Cognitive Services Speech-Geräte-SDK.