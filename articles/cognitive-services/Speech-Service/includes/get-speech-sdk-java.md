---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: fcc04cc4599681e3542b275d238b75352d690a95
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135992"
---
:::row:::
    :::column span="3":::
        Das Java SDK für Android ist als <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android-Bibliothek) <span class="docon docon-navigate-external x-hidden-focus"></span></a> gepackt und enthält die erforderlichen Bibliotheken sowie die erforderlichen Android-Berechtigungen. Es wird in einem Maven-Repository unter `https://csspeechstorage.blob.core.windows.net/maven/` als Paket `com.microsoft.cognitiveservices.speech:client-sdk:1.14.0` gehostet.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Um das Paket im Android Studio-Projekt zu nutzen, nehmen Sie die folgenden Änderungen vor:

1. Fügen Sie der Datei *build.gradle* auf Projektebene Folgendes im Abschnitt `repositories` hinzu:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Fügen Sie der Datei *build.gradle* auf Modulebene Folgendes im Abschnitt `dependencies` hinzu:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.14.0'
  ```

Das Java SDK ist auch Teil des [Speech-Geräte-SDK](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Quellcode zum Android-spezifischen Java-Schnellstart <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Quellcode zum Schnellstart für die Java-Runtime (JRE) <span class="docon docon-navigate-external x-hidden-focus"></span></a>
