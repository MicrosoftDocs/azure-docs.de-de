---
title: Bekannte Probleme mit dem Project Acoustics-Plug-In
titlesuffix: Azure Cognitive Services
description: Bei Verwendung der Designervorschau für Project Acoustics können die folgenden bekannten Probleme auftreten.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 50de4d983ed24440d655cf5b9ba3fb5e33d8d7cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335346"
---
# <a name="project-acoustics-known-issues"></a>Bekannte Probleme mit Projekt Akustik
Bei Verwendung der Designervorschau für Project Acoustics können die folgenden bekannten Probleme auftreten.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Beim Umbenennen einer Szene gehen akustische Parameter verloren

Wenn Sie eine Szene umbenennen, werden nicht alle zu dieser Szene gehörenden akustischen Parameter automatisch an die neue Szene übertragen. Sie bleiben jedoch in der alten ASSET-Datei erhalten. Suchen Sie nach der Datei **Szenenname_Akustikparameter.asset** im Verzeichnis **Editor** neben Ihrer Szenendatei. Benennen Sie Ihre Datei entsprechend dem neuen Szenennamen um.

## <a name="unity-crashes-when-closing-project"></a>Unity stürzt beim Schließen des Projekts ab

Die neuesten Versionen von Unity (2018.2 und höher) haben den bekannten Fehler, dass Unity abstürzt, wenn Sie Ihr Projekt schließen. Dieses Problem wird mit [diesem Unity-Problem](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project) nachverfolgt.

## <a name="deploying-to-android-from-some-unity-versions"></a>Bereitstellen auf Android von einigen Unity-Versionen aus

Einige Unity-Versionen weisen einen Fehler beim Bereitstellen von Audio-Plug-Ins auf Android auf. Stellen Sie sicher, dass Sie keine Version verwenden, die von [diesem Fehler](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) betroffen sind.

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Die Fehlermeldung „could not find metadata file System.Security.dll“ (Die Metadatendatei „System.Security.dll“ wurde nicht gefunden) wird angezeigt

Stellen Sie sicher, dass für die Scripting Runtime Version in den Player-Einstellungen **.NET 4.x Equivalent** (.NET 4.x-Entsprechung) festgelegt ist, und starten Sie Unity neu.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Ich habe beim Herstellen einer Verbindung mit Azure Authentifizierungsprobleme

Vergewissern Sie sich, dass Sie die richtigen Anmeldeinformationen für Ihr Azure-Konto verwendet haben, dass Ihr Konto den im Bake-Vorgang angeforderten Knotentyp unterstützt und dass Ihre Systemuhr stimmt.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Das Abbrechen eines Bake-Vorgangs lässt die Registerkarte für den Bake-Vorgang im Zustand „Wird gelöscht“.
Projekt Akustik bereinigt alle Azure-Ressourcen für einen Auftrag nach erfolgreichem Abschluss oder Abbruch. Der Vorgang kann bis zu 5 Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte
* Sehen Sie sich den Beispielinhalt für [Unity](unity-quickstart.md) oder [Unreal](unreal-quickstart.md) an.

