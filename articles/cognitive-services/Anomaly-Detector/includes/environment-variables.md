---
title: Umgebungsvariablen der Anomalieerkennung
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: mbullwin
ms.openlocfilehash: 5148114da7fcc654b1f0af431440ce74c7bc8d31
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371988"
---
### <a name="create-an-environment-variable"></a>Erstellen einer Umgebungsvariablen

>[!NOTE]
> Nach dem 1. Juli 2019 erstellte Endpunkte für Ressourcen, bei denen es sich nicht um Testressourcen handelt, verwenden das unten gezeigte benutzerdefinierte Format für Subdomänen. Weitere Informationen und eine vollständige Liste mit regionalen Endpunkten finden Sie unter [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../../cognitive-services-custom-subdomains.md). 

Erstellen Sie unter Verwendung des Schlüssels und des Endpunkts der von Ihnen erstellten Ressource zwei Umgebungsvariablen für die Authentifizierung:

* `ANOMALY_DETECTOR_KEY`: Der Ressourcenschlüssel zum Authentifizieren Ihrer Anforderungen.
* `ANOMALY_DETECTOR_ENDPOINT`: Der Ressourcenendpunkt zum Senden von API-Anforderungen. Er sieht wie folgt aus: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Führen Sie die Schritte für Ihr Betriebssystem aus:

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Starten Sie das Konsolenfenster neu, nachdem Sie die Umgebungsvariable hinzugefügt haben.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.bashrc` aus, damit die Änderungen wirksam werden.

#### <a name="macos"></a>[macOS](#tab/unix)

Bearbeiten Sie `.bash_profile`, und fügen Sie die Umgebungsvariable hinzu:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source .bash_profile` aus, damit die Änderungen wirksam werden.

***