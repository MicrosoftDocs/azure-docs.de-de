---
title: 'Schnellstart: Erstellen einer Unity iOS-App mit Azure Spatial Anchors | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie eine iOS-App mit Unity erstellen, indem Sie Spatial Anchors verwenden.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 41189d4945ef22f8a587a51717b7dd6f40ac39ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690548"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Schnellstart: Erstellen einer Unity iOS-App mit Azure Spatial Anchors

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit [Azure Spatial Anchors](../overview.md) eine Unity iOS-App erstellen. Azure Spatial Anchors ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. Nach Abschluss des Vorgangs verfügen Sie über eine mit Unity erstellte ARKit-iOS-App, mit der ein räumlicher Anker gespeichert und abgerufen werden kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Spatial Anchors-Kontos
> * Vorbereiten von Unity-Buildeinstellungen
> * Herunterladen und Importieren des Unity-ARKit-Plug-Ins
> * Konfigurieren des Bezeichners und Kontoschlüssels für das Spatial Anchors-Konto
> * Exportieren des Xcode-Projekts
> * Bereitstellen und Ausführen auf einem iOS-Gerät

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

- Einen macOS-Computer mit Installation von <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a>, <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> und <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Git-Installation über HomeBrew. Geben Sie den folgenden Befehl in einer einzelnen Terminalzeile ein: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Führen Sie anschließend `brew install git` aus.
- Ein für Entwickler geeignetes <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibles</a> iOS-Gerät.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Öffnen des Beispielprojekts in Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurieren des Kontobezeichners und -schlüssels

Navigieren Sie im Bereich **Projekt** zu `Assets/AzureSpatialAnchorsPlugin/Examples`, und öffnen Sie die Szenendatei `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Speichern Sie die Szene, indem Sie **Datei** -> **Speichern** wählen.

## <a name="export-the-xcode-project"></a>Exportieren des Xcode-Projekts

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Befolgen Sie in der App die Anleitung zum Anordnen und Abrufen eines Ankers.

> [!NOTE]
> Falls beim Ausführen der App die Kamera nicht als Hintergrund angezeigt wird (sondern stattdessen beispielsweise ein leerer blauer Bereich oder andere Texturen), müssen Sie die Ressourcen wahrscheinlich erneut in Unity importieren. Beenden Sie die App. Wählen Sie in Unity im Menü am oberen Rand die Option **Assets > Re-import all** (Ressourcen > Alle erneut importieren). Führen Sie die App dann erneut aus.

Beenden Sie die App in Xcode, indem Sie **Beenden** wählen.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Freigeben für Sitzungen und Geräte mit Azure Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
