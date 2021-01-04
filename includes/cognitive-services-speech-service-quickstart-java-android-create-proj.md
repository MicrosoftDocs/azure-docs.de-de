---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: 2efe17bdf597b7e3ba7d0277f06d4b24e9b51db3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187668"
---
1. Starten Sie Android Studio, und wählen Sie im **Begrüßungsfenster** die Option **Neues Android Studio-Projekt starten** aus.

    ![Screenshot des Begrüßungsfensters von Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Der **Assistent für die Projektauswahl** wird angezeigt. Wählen Sie **Phone and Tablet** (Telefon und Tablet) und im Feld für die Aktivitätsauswahl die Option **Empty Activity** (Leere Aktivität) aus. Wählen Sie **Weiter** aus.

   ![Screenshot: Assistent für die Projektauswahl](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Geben Sie im **Projektkonfigurationsbildschirm** als **Name** den Namen *Quickstart* (Schnellstart) und als **Paketname** den Namen *samples.speech.cognitiveservices.microsoft.com* ein. Wählen Sie anschließend ein Projektverzeichnis aus. Wählen Sie anschließend unter **Minimum API level** (API-Mindestebene) die Option **API 23: Android 6.0 (Marshmallow)** aus. Lassen Sie alle anderen Kontrollkästchen deaktiviert, und wählen Sie **Fertig stellen** aus.

   ![Screenshot: Projektkonfigurations-Assistent](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio benötigt einen Moment, um Ihr neues Android-Projekt vorzubereiten. Anschließend konfigurieren Sie das Projekt so, dass es über das Azure Cognitive Services Speech SDK informiert ist und Java 8 verwendet.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Die aktuelle Version des Cognitive Services Speech SDK ist 1.13.0.

Das Speech SDK für Android ist als [AAR (Android-Bibliothek)](https://developer.android.com/studio/projects/android-library) verpackt und enthält die erforderlichen Bibliotheken sowie die erforderlichen Android-Berechtigungen.
Es wird in einem Maven-Repository unter „https:\//csspeechstorage.blob.core.windows.net/maven/“ gehosted.

Richten Sie Ihr Projekt so ein, dass es das Speech SDK verwendet. Öffnen Sie das Fenster **Projektstruktur**, indem Sie **Datei** > **Projektstruktur** auf der Menüleiste von Android Studio auswählen. Nehmen Sie im Fenster **Projektstruktur** die folgenden Änderungen vor:

1. Wählen Sie in der Liste auf der linken Seite des Fensters **Projekt** aus. Bearbeiten Sie die Einstellungen für **Standardbibliothekrepository**, indem Sie ein Komma und die Maven-Repository-URL in einfache Anführungszeichen anfügen: 'https:\//csspeechstorage.blob.core.windows.net/maven/'.

   ![Screenshot der Fensters „Projektstruktur“](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Wählen Sie auf dem gleichen Bildschirm auf der linken Seite **App** aus. Wählen Sie dann die Registerkarte **Abhängigkeiten** oben im Fenster aus. Wählen Sie das grüne Pluszeichen ( **+** ) und dann **Bibliotheksabhängigkeit** im Dropdownmenü aus.

   ![Screenshot der Bibliotheksabhängigkeit](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Geben Sie im angezeigten Fenster den Namen und die Version des Speech SDK für Android ein: *com.microsoft.cognitiveservices.speech:client-sdk:1.13.0*. Klicken Sie anschließend auf **OK**.
   Das Speech SDK sollte nun der Liste der Abhängigkeiten hinzugefügt werden, wie hier dargestellt:

   ![Screenshot des Speech SDK in der Liste der Abhängigkeiten](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wählen Sie die Registerkarte **Eigenschaften** aus. Wählen Sie **1.9** für **Quellkompatibilität** und **Zielkompatibilität** aus.

   ![Screenshot der Quellkompatibilität und Zielkompatibilität](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Wählen Sie **OK** aus, um das Fenster **Projektstruktur** zu schließen und Ihre Änderungen auf das Projekt anzuwenden.
