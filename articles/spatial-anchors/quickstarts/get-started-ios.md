---
title: 'Schnellstart: Erstellen einer iOS-App'
description: Hier wird beschrieben, wie Sie mit Azure Spatial Anchors in Swift oder Objective-C programmgesteuert eine iOS-App erstellen.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 4434118313989eb8bc70f1d44e977b2df3633050
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009074"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Schnellstart: Erstellen einer iOS-App mit Azure Spatial Anchors in Swift oder Objective-C

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit [Azure Spatial Anchors](../overview.md) in Swift oder Objective-C eine iOS-App erstellen. Azure Spatial Anchors ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. Nach Abschluss des Vorgangs verfügen Sie über eine ARKit-iOS-App, mit der ein räumlicher Anker gespeichert und abgerufen werden kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Spatial Anchors-Kontos
> * Konfigurieren des Bezeichners und Kontoschlüssels für das Spatial Anchors-Konto
> * Bereitstellen und Ausführen auf einem iOS-Gerät

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

- Einen für Entwickler geeigneten macOS-Computer mit der aktuellen Version von <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> und <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Git-Installation über Homebrew:
  1. Geben Sie den folgenden Befehl im Terminal als eine Zeile ein: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. 
  1. Führen Sie `brew install git` und `brew install git-lfs` aus.
  1. Aktualisieren Sie Ihre Git-Konfiguration mit `git lfs install` (für den aktuellen Benutzer) oder `git lfs install --system` (für das gesamte System).
- Ein für Entwickler geeignetes <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibles</a> iOS-Gerät.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öffnen des Beispielprojekts

Führen Sie über das Terminal die folgenden Aktionen aus.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installieren Sie die erforderlichen Pods, indem Sie CocoaPods verwenden:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Navigieren Sie zu `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Navigieren Sie zu `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Führen Sie `pod install --repo-update` aus, um die CocoaPods für das Projekt zu installieren.

Öffnen Sie nun `.xcworkspace` in Xcode.

> [!NOTE]
> Sehen Sie sich die [Schritte zur Problembehandlung](#cocoapods-issues-on-macos-catalina-1015) an, wenn nach dem Upgrade auf macOS Catalina (10.15) Probleme mit CocoaPod auftreten.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurieren des Kontobezeichners und -schlüssels

Im nächsten Schritt wird die App zur Verwendung des Kontobezeichners und Kontoschlüssels konfiguriert. Diese haben Sie beim [Einrichten der Spatial Anchors-Ressource](#create-a-spatial-anchors-resource) in einen Text-Editor kopiert.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Öffnen Sie `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Suchen Sie nach dem Feld `spatialAnchorsAccountKey`, und ersetzen Sie `Set me` durch den Kontoschlüssel.

Suchen Sie nach dem Feld `spatialAnchorsAccountId`, und ersetzen Sie `Set me` durch den Kontobezeichner.

Suchen Sie nach dem Feld `spatialAnchorsAccountDomain`, und ersetzen Sie `Set me` durch die Kontodomäne.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Öffnen Sie `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Suchen Sie nach dem Feld `SpatialAnchorsAccountKey`, und ersetzen Sie `Set me` durch den Kontoschlüssel.

Suchen Sie nach dem Feld `SpatialAnchorsAccountId`, und ersetzen Sie `Set me` durch den Kontobezeichner.

Suchen Sie nach dem Feld `SpatialAnchorsAccountDomain`, und ersetzen Sie `Set me` durch die Kontodomäne.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Bereitstellen der App auf Ihrem iOS-Gerät

Verbinden Sie das iOS-Gerät mit dem Macintosh-Computer, und legen Sie das **aktive Schema** auf Ihr iOS-Gerät fest.

![Auswählen des Geräts](./media/get-started-ios/select-device.png)

Wählen Sie **Build and then run the current scheme** (Aktuelles Schema erstellen und dann ausführen) aus.

![Bereitstellen und Ausführen](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Sollte ein Fehler vom Typ `library not found for -lPods-SampleObjC` auftreten, haben Sie wahrscheinlich anstelle der Datei vom Typ `.xcworkspace` die Datei vom Typ `.xcodeproj` geöffnet. Öffnen Sie die Datei vom Typ `.xcworkspace`, und wiederholen Sie den Vorgang.

Beenden Sie die App in Xcode, indem Sie **Beenden** wählen.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Cocoapods-Probleme unter macOS Catalina (10.15)

Wenn Sie vor kurzem auf macOS Catalina (10.15) aktualisiert haben und CocoaPods zuvor installiert waren, kann sich CocoaPods in einem fehlerhaften Zustand befinden, sodass Ihre Pods und `.xcworkspace`-Projektdateien möglicherweise nicht ordnungsgemäß konfiguriert werden. Um dieses Problem zu beheben, müssen Sie die CocoaPods neu installieren, indem Sie die folgenden Befehle ausführen:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>App stürzt ab, wenn sie für iOS 10.3.1 über ein persönliches Bereitstellungsprofil bzw. Entwicklerkonto bereitgestellt wird 

Wenn Sie Ihre iOS-App unter iOS 10.3.1 über ein persönliches Bereitstellungsprofil bzw. Entwicklerkonto bereitstellen, wird ggf. der folgende Fehler angezeigt: `Library not loaded: @rpath/ADAL...`. 

So lösen Sie das Problem:

- Verwenden Sie ein Bereitstellungsprofil, bei dem es sich nicht um ein persönliches Teamprofil (kostenpflichtiges Entwicklerkonto) handelt.
- Stellen Sie Ihre App auf einem iOS-Gerät mit iOS 13.3 oder früher oder einem Gerät mit iOS 13.4 Beta- oder -Releaseversion bereit.
- Weitere Informationen zu diesem Problem finden Sie bei [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Freigeben für Sitzungen und Geräte mit Azure Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
