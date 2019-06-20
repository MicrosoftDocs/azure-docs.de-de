---
title: 'Schnellstart: Erkennen von Sprache, Java (Android) – Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mit dem Speech SDK Sprache in Java unter Android erkennen.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: f856694a5063551ba94bb6d322614aa490f13adb
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001885"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Schnellstart: Erkennen von Sprache in Java unter Android mit dem Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dieser Artikel enthält Informationen zur Entwicklung einer Java-Anwendung für Android mithilfe des Cognitive Services Speech SDK zum Umwandeln von Sprache in Text.
Die Anwendung basiert auf dem Maven-Paket für das Speech SDK (Version 1.5.1) sowie auf Android Studio 3.3.
Das Speech SDK ist zurzeit kompatibel mit Android-Geräten mit 32-/64-Bit-ARM-Prozessoren und Prozessoren, die mit Intel x86/x64 kompatibel sind.

> [!NOTE]
> Weitere Informationen zum SDK für sprachaktivierte Geräte und zum Roobo-Gerät finden Sie unter [SDK für sprachaktivierte Geräte](speech-devices-sdk.md).

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie einen Abonnementschlüssel für die Speech-Dienste. Sie können einen solchen Schlüssel kostenlos abrufen. Ausführliche Informationen finden Sie unter [Kostenloses Testen von Speech Services](get-started.md).

## <a name="create-and-configure-a-project"></a>Erstellen und Konfigurieren eines Projekts

1. Starten Sie Android Studio, und wählen Sie im Begrüßungsfenster die Option **Neues Android Studio-Projekt starten** aus.

    ![Screenshot des Begrüßungsfensters von Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Der**Assistent für die Projektauswahl wird angezeigt**. Wählen Sie **Phone and Tablet** (Telefon und Tablet) und im Feld für die Aktivitätsauswahl die Option **Empty Activity** (Leere Aktivität) aus. Klicken Sie auf **Weiter**.

   ![Screenshot: Assistent für die Projektauswahl](media/sdk/qs-java-android-02-target-android-devices.png)

1. Geben Sie im**Projektkonfigurationsbildschirm** **Quickstart** als **Name** und **samples.speech.cognitiveservices.microsoft.com** als **Paketname** ein. Wählen Sie dann ein Projektverzeichnis aus. Wählen Sie anschließend unter **Minimum API level** (API-Mindestebene) die Option **API 23: Android 6.0 (Marshmallow)** aus, lassen Sie alle anderen Kontrollkästchen deaktiviert, und wählen Sie **Finish** (Fertig stellen) aus.

   ![Screenshot: Projektkonfigurations-Assistent](media/sdk/qs-java-android-03-create-android-project.png)

Android Studio benötigt einen Moment, um Ihr neues Android-Projekt vorzubereiten. Anschließend konfigurieren Sie das Projekt so, dass es über das Speech SDK informiert ist und Java 8 verwendet.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Die aktuelle Version des Cognitive Services Speech SDK ist `1.5.1`.

Das Speech SDK für Android ist als [AAR (Android-Bibliothek)](https://developer.android.com/studio/projects/android-library) verpackt und enthält die erforderlichen Bibliotheken sowie die erforderlichen Android-Berechtigungen.
Es wird in einem Maven-Repository unter „https:\//csspeechstorage.blob.core.windows.net/maven/“ gehosted.

Richten Sie Ihr Projekt so ein, dass es das Speech SDK verwendet. Öffnen Sie das Fenster „Projektstruktur“, indem Sie **Datei** > **Projektstruktur** in der Menüleiste von Android Studio auswählen. Nehmen Sie im Fenster „Projektstruktur“ die folgenden Änderungen vor:

1. Wählen Sie in der Liste auf der linken Seite des Fensters **Projekt** aus. Bearbeiten Sie die Einstellungen für **Standardbibliothekrepository**, indem Sie ein Komma und die Maven-Repository-URL (in einfache Anführungszeichen eingeschlossen) anfügen. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Screenshot der Fensters „Projektstruktur“](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Wählen Sie auf dem gleichen Bildschirm auf der linken Seite **App** aus. Wählen Sie dann die Registerkarte **Abhängigkeiten** oben im Fenster aus. Wählen Sie das grüne Pluszeichen (+) und dann **Bibliotheksabhängigkeit** im Dropdownmenü aus.

   ![Screenshot der Fensters „Projektstruktur“](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Geben Sie im angezeigten Fenster den Namen und die Version des Speech SDK für Android ein: `com.microsoft.cognitiveservices.speech:client-sdk:1.5.1`. Wählen Sie dann **OK**aus.
   Das Speech SDK sollte nun der Liste der Abhängigkeiten hinzugefügt werden, wie unten dargestellt:

   ![Screenshot der Fensters „Projektstruktur“](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wählen Sie die Registerkarte **Eigenschaften** aus. Wählen Sie **1.8** für **Quellkompatibilität** und **Zielkompatibilität** aus.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Wählen Sie **OK** aus, um das Fenster „Projektstruktur“ zu schließen und Ihre Änderungen auf das Projekt anzuwenden.

## <a name="create-user-interface"></a>Erstellen der Benutzeroberfläche

Sie erstellen eine einfache Benutzeroberfläche für die Anwendung. Bearbeiten Sie das Layout Ihrer Hauptaktivität `activity_main.xml`. Das Layout beinhaltet zunächst eine Titelleiste mit dem Namen Ihrer Anwendung und ein TextView-Element mit dem Text „Hello World!“.

* Klicken Sie auf das TextView-Element. Ändern Sie dessen ID-Attribut oben rechts in `hello`.

* Ziehen Sie aus der Palette oben links im Fenster `activity_main.xml` eine Schaltfläche in den leeren Bereich über dem Text.

* Geben Sie in den Attributen der Schaltfläche auf der rechten Seite für das `onClick`-Attribut den Wert `onSpeechButtonClicked` ein. Wir schreiben eine Methode mit diesem Namen, um das Button-Ereignis zu verarbeiten.  Ändern Sie dessen ID-Attribut oben rechts in `button`.

* Verwenden Sie das Zauberstabsymbol oben im Designer, um die Layouteinschränkungen abzuleiten.

  ![Screenshot des Zauberstabsymbols](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Der Text und die grafische Darstellung Ihrer Benutzeroberfläche sollten jetzt etwa wie folgt aussehen:

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Hinzufügen von Beispielcode

1. Öffnen Sie die Quelldatei `MainActivity.java`. Ersetzen Sie den Code in dieser Datei durch die folgenden Codezeilen:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Die `onCreate`-Methode enthält Code, der Mikrofon- und Internetberechtigungen anfordert und die native Plattformbindung initialisiert. Die Konfiguration der nativen Plattformbindungen ist nur ein Mal erforderlich. Sie sollte früh während der Anwendungsinitialisierung ausgeführt werden.

   * Die Methode `onSpeechButtonClicked` ist (wie bereits erwähnt) der Klickhandler für die Schaltfläche. Ein Klick auf die Schaltfläche löst die Spracherkennungstranskription aus.

1. Ersetzen Sie in der gleichen Datei die Zeichenfolge `YourSubscriptionKey` durch Ihren Abonnementschlüssel.

1. Ersetzen Sie außerdem die Zeichenfolge `YourServiceRegion` durch die [Region](regions.md), die mit Ihrem Abonnement verknüpft ist (z.B. `westus` für das kostenlose Testabonnement).

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

1. Verbinden Sie Ihr Android-Gerät mit Ihrem Entwicklungs-PC. Stellen Sie sicher, dass Sie den [Entwicklungsmodus und USB-Debuggen](https://developer.android.com/studio/debug/dev-options) für das Gerät aktiviert haben.

1. Um die Anwendung zu erstellen, drücken Sie STRG+F9, oder wählen Sie **Erstellen** > **Projekt erstellen** in der Menüleiste aus.

1. Um die Anwendung zu starten, drücken Sie UMSCHALT+F10, oder wählen Sie **Ausführen** >  **'App' ausführen** aus.

1. Wählen Sie im daraufhin angezeigten Fenster mit Bereitstellungszielen Ihr Android-Gerät aus.

   ![Screenshot des Fensters „Bereitstellungsziel auswählen“](media/sdk/qs-java-android-12-deploy.png)

Drücken Sie die Schaltfläche in der Anwendung, um einen Spracherkennungsabschnitt zu beginnen. Die nächsten 15 Sekunden gesprochene englische Sprache werden an die Spracherkennungsdienste gesendet und transkribiert. Das Ergebnis wird in der Android-Anwendung und im logcat-Fenster in Android Studio angezeigt.

![Screenshot der Android-Anwendung](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Java-Beispiele auf GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Weitere Informationen

- [Tutorial: Erstellen eines benutzerdefinierten Akustikmodells](how-to-customize-acoustic-models.md)
- [Tutorial: Erstellen eines benutzerdefinierten Sprachmodells](how-to-customize-language-model.md)
