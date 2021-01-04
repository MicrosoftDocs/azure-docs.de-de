---
title: 'Schnellstart: Erstellen einer Xamarin-iOS-App'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von Spatial Anchors und Xamarin eine iOS-App erstellen.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b62ca4b02a1bd28d150f233adf1dfb621995e1c4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009737"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Schnellstart: Erstellen einer Xamarin-iOS-App mit Azure Spatial Anchors

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe von [Azure Spatial Anchors](../overview.md) eine Xamarin-iOS-App erstellen. Azure Spatial Anchors ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. Nach Abschluss dieser Anleitung verfügen Sie über eine iOS-App, die einen räumlichen Anker speichern und abrufen kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Spatial Anchors-Kontos
> * Konfigurieren des Bezeichners und Kontoschlüssels für das Spatial Anchors-Konto
> * Bereitstellen und Ausführen auf einem iOS-Gerät

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:
- Ein Mac-Computer mit macOS High Sierra (10.13) oder höher und Folgendem:
  - Die neueste Version von Xcode und iOS SDK, installiert aus dem [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Eine aktuelle Version von <a href="/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio für Mac 8.1 oder höher</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git für macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öffnen des Beispielprojekts

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öffnen Sie `Xamarin/SampleXamarin.sln` in Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurieren des Kontobezeichners und -schlüssels

Im nächsten Schritt wird die App zur Verwendung des Kontobezeichners und Kontoschlüssels konfiguriert. Diese haben Sie beim [Einrichten der Spatial Anchors-Ressource](#create-a-spatial-anchors-resource) in einen Text-Editor kopiert.

Öffnen Sie `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Suchen Sie nach dem Feld `SpatialAnchorsAccountKey`, und ersetzen Sie `Set me` durch den Kontoschlüssel.

Suchen Sie nach dem Feld `SpatialAnchorsAccountId`, und ersetzen Sie `Set me` durch den Kontobezeichner.

Suchen Sie nach dem Feld `SpatialAnchorsAccountDomain`, und ersetzen Sie `Set me` durch die Kontodomäne.

## <a name="deploy-the-app-to-your-ios-device"></a>Bereitstellen der App auf Ihrem iOS-Gerät

Schalten Sie das iOS-Gerät ein, melden Sie sich an, und verbinden Sie es per USB-Kabel mit dem Computer.

Legen Sie das Startprojekt als **SampleXamarin.iOS** fest, ändern Sie die **Projektmappenkonfiguration** in **Release**, und wählen Sie in der Dropdownliste der Geräteauswahl das Gerät aus, auf dem die Bereitstellung erfolgen soll.

![Visual Studio-Konfiguration](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um Ihre App bereitzustellen und zu starten.

Wählen Sie in der App **Basic** aus, um die Demo auszuführen, und befolgen Sie die Anweisungen zum Platzieren und Widerrufen eines Ankers.

> ![Screenshot 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![Screenshot 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Screenshot 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Freigeben für Sitzungen und Geräte mit Azure Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)