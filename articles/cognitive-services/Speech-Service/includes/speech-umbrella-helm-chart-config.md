---
title: Installieren von Speech-Containern
titleSuffix: Azure Cognitive Services
description: Beschreibt die Konfigurationsoptionen für ein übergeordnetes Helm-Diagramm zu Sprache.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002275"
---
### <a name="speech-umbrella-chart"></a>Sprache (übergeordnetes Diagramm)

Werte im übergeordneten Diagramm auf oberster Ebene überschreiben die entsprechenden Werte im untergeordneten Diagramm. Deshalb sollten alle lokal angepassten Werte hier hinzugefügt werden.

|Parameter|BESCHREIBUNG|Standard|
| -- | -- | -- | -- |
| `speechToText.enabled` | Gibt an, ob der **Spracherkennungs**-Dienst aktiviert ist. | `true` |
| `speechToText.verification.enabled` | Gibt an, ob die `helm test`-Funktion für den **Spracherkennungs**-Dienst aktiviert ist. | `true` |
| `speechToText.verification.image.registry` | Das Docker-Imagerepository, das von `helm test` zum Testen des **Spracherkennungs**-Diensts verwendet wird. Helm erstellt separate Pods innerhalb des Clusters zum Testen und ruft das *test-use*-Image aus dieser Registrierung ab. | `docker.io` |
| `speechToText.verification.image.repository` | Das Docker-Imagerepository, das von `helm test` zum Testen des **Spracherkennungs**-Diensts verwendet wird. Der Helm-Testpod verwendet dieses Repository zum Abrufen des *test-use*-Images. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Das Docker-Imagetag, das mit `helm test` für den **Spracherkennungs**-Dienst verwendet wird. Der Helm-Testpod verwendet dieses Tag zum Abrufen des *test-use*-Images. | `latest` |
| `speechToText.verification.image.pullByHash` | Gibt an, ob das Docker-Image *test-use* mithilfe von Hashing abgerufen wird. Wenn `true`, sollte `speechToText.verification.image.hash` mit einem gültigen Imagehashwert hinzugefügt werden. | `false` |
| `speechToText.verification.image.arguments` | Die Argumente, die zum Ausführen des Docker-Images *test-use* verwendet werden. Der Helm-Testpod übergibt diese Argumente beim Ausführen von `helm test` an den Container. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Gibt an, ob der **Sprachsynthese**-Dienst aktiviert ist. | `true` |
| `textToSpeech.verification.enabled` | Gibt an, ob die `helm test`-Funktion für den **Spracherkennungs**-Dienst aktiviert ist. | `true` |
| `textToSpeech.verification.image.registry` | Das Docker-Imagerepository, das von `helm test` zum Testen des **Spracherkennungs**-Diensts verwendet wird. Helm erstellt separate Pods innerhalb des Clusters zum Testen und ruft das *test-use*-Image aus dieser Registrierung ab. | `docker.io` |
| `textToSpeech.verification.image.repository` | Das Docker-Imagerepository, das von `helm test` zum Testen des **Spracherkennungs**-Diensts verwendet wird. Der Helm-Testpod verwendet dieses Repository zum Abrufen des *test-use*-Images. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Das Docker-Imagetag, das mit `helm test` für den **Spracherkennungs**-Dienst verwendet wird. Der Helm-Testpod verwendet dieses Tag zum Abrufen des *test-use*-Images. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Gibt an, ob das Docker-Image *test-use* mithilfe von Hashing abgerufen wird. Wenn `true`, sollte `textToSpeech.verification.image.hash` mit einem gültigen Imagehashwert hinzugefügt werden. | `false` |
| `textToSpeech.verification.image.arguments` | Die Argumente für die Ausführung mit dem Docker-Image *test-use*. Der Helm-Testpod übergibt diese Argumente beim Ausführen von `helm test` an den Container. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |