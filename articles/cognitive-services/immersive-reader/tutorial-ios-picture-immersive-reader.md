---
title: 'Tutorial: Erstellen einer iOS-App, die ein Foto im plastischen Reader startet (Swift)'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial erstellen Sie eine iOS-App ohne Vorlage und fügen ihr die Funktion „Bild im plastischen Reader“ hinzu.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: d8ee6b2344f0c953233195a693e774e410a8a8ea
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368201"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Tutorial: Erstellen einer iOS-App, die den plastischen Reader mit Inhalt aus einem Foto startet (Swift)

Der [plastische Reader](https://www.onenote.com/learningtools) ist ein inklusiv konzipiertes Tool, das bewährte Techniken zur Verbesserung des Leseverständnisses implementiert.

Die [Cognitive Services-Lese-API für maschinelles Sehen](../computer-vision/concept-recognizing-text.md) erkennt mithilfe der neuesten Erkennungsmodelle von Microsoft Textinhalte eines Bilds und konvertiert den erkannten Text in einen computerlesbaren Zeichendatenstrom.

In diesem Tutorial erstellen Sie eine iOS-App ohne Vorlage und integrieren die Lese-API und den plastischen Reader mithilfe des SDK für den plastischen Reader. Ein vollständiges funktionierendes Beispiel dieses Tutorials finden Sie [hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Eine Ressource des plastischen Readers, die für die Authentifizierung mit Azure Active Directory konfiguriert ist. Befolgen Sie [diese Anweisungen](./how-to-create-immersive-reader.md) für die Einrichtung. Einige der hier erstellten Werte benötigen Sie bei der Konfiguration der Beispielprojekteigenschaften. Speichern Sie die Ausgabe Ihrer Sitzung zur späteren Verwendung in einer Textdatei.
* Um dieses Beispiel verwenden zu können, benötigen Sie ein Azure-Abonnement für den Cognitive Service für maschinelles Sehen. [Erstellen Sie über das Azure-Portal eine Cognitive Services-Ressource für maschinelles Sehen.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)

## <a name="create-an-xcode-project"></a>Erstellen eines Xcode-Projekts

Erstellen Sie ein neues Projekt in Xcode.

![Neues Projekt](./media/ios/xcode-create-project.png)

Wählen Sie **Einzelansicht-App** aus.

![Neue Einzelansicht-App](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Abrufen der SDK-CocoaPod-Instanz
Das SDK für den plastischen Reader lässt sich am einfachsten über CocoaPods verwenden. Gehen Sie zum Installieren über CocoaPods wie folgt vor:
1. Führen Sie die unter [Getting Started](http://guides.cocoapods.org/using/getting-started.html) (Erste Schritte) beschriebenen Schritte aus, um CocoaPods zu installieren.
2. Führen Sie `pod init` im Stammverzeichnis Ihres Xcode-Projekts aus, um eine Podfile-Datei zu erstellen.
3.  Fügen Sie `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` hinzu, um die CocoaPod-Instanz Ihrer Podfile-Datei hinzuzufügen. Ihre Podfile-Datei sollte wie folgt aussehen (mit dem Namen Ihres Ziels anstelle von „picture-to-immersive-reader-swift“):
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. Führen Sie im Terminal im Verzeichnis Ihres Xcode-Projekts den Befehl `pod install` aus, um den Pod für das SDK für den plastischen Reader zu installieren.
5. Fügen Sie `import immersive_reader_sdk` allen Dateien hinzu, die auf das SDK verweisen müssen.
6. Achten Sie beim Öffnen des Projekts darauf, dass Sie die Datei `.xcworkspace` verwenden, und nicht die Datei `.xcodeproj`.

## <a name="acquire-an-azure-ad-authentication-token"></a>Abrufen eines Azure AD-Authentifizierungstokens

Für diesen Teil benötigen Sie einige Werte aus dem obigen Schritt zur Konfiguration der Azure AD-Authentifizierung. Greifen Sie auf die Textdatei zurück, die Sie in dieser Sitzung gespeichert haben.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Erstellen Sie im Hauptprojektordner, der die Datei „ViewController.swift“ enthält, eine Swift-Klassendatei namens „Constants.swift“. Ersetzen Sie die Klasse durch den folgenden Code, und fügen Sie nach Bedarf Ihre eigenen Werte hinzu. Diese Datei darf nur als lokale Datei auf Ihrem Computer vorhanden sein. Committen Sie sie nicht in Ihre Quellcodeverwaltung, da sie Geheimnisse enthält, die nicht für die Öffentlichkeit bestimmt sind. Es wird empfohlen, keine Geheimnisse in Ihrer App zu speichern. Verwenden Sie stattdessen einen Back-End-Dienst, um das Token abzurufen, sodass die Geheimnisse außerhalb der App und außerhalb des Geräts gespeichert werden können. Der Back-End-API-Endpunkt muss durch eine Authentifizierung (beispielsweise [OAuth](https://oauth.net/2/)) geschützt werden, um zu verhindern, dass nicht autorisierte Benutzer Token abrufen und für Ihren Dienst „Plastischer Reader“ bzw. für die Abrechnung verwenden. Dies wird jedoch in diesem Tutorial nicht behandelt.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Einrichten der App für die Ausführung ohne Storyboard

Öffnen Sie „AppDelegate.swift“, und ersetzen Sie die Datei durch den folgenden Code:

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Hinzufügen von Funktionen zum Aufnehmen und Hochladen von Fotos

Benennen Sie „ViewController.swift“ in „PictureLaunchViewController.swift“ um, und ersetzen Sie die Datei durch den folgenden Code:

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

Legen Sie in Xcode das Archivschema fest, indem Sie einen Simulator oder ein Geräteziel auswählen.
![Archivschema](./media/ios/xcode-archive-scheme.png)<br/>
![Auswählen des Ziels](./media/ios/xcode-select-target.png)

Drücken Sie in Xcode die Tastenkombination STRG+R, oder klicken Sie auf die Wiedergabeschaltfläche, um das Projekt auszuführen. Daraufhin sollte die App im angegebenen Simulator oder auf dem angegebenen Gerät gestartet werden.

In Ihrer App sollte Folgendes angezeigt werden:

![Beispiel-App](./media/ios/picture-to-immersive-reader-ipad-app.png)

Verwenden Sie innerhalb der App die Schaltfläche „Take Photo“ (Foto aufnehmen) oder „Choose Photo from Library“ (Foto aus Bibliothek auswählen), um ein Foto mit Text aufzunehmen oder hochzuladen. Daraufhin wird der plastische Reader gestartet und der Text des Fotos angezeigt.

![Plastischer Reader](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die [Referenz zum SDK für den plastischen Reader](./reference.md).