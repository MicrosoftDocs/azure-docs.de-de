---
title: Ändern, Trainieren der App, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: In dieser Schnellstartanleitung für Java fügen Sie einer Home Automation-App Beispieläußerungen hinzu, und Sie trainieren die App.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: ce2cf0603e584684edda1b1f14a12b52fbbb928c
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357140"
---
# <a name="quickstart-change-model-using-java"></a>Schnellstart: Ändern des Modells mit Java 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [GSON-JSON-Bibliothek von Google](https://github.com/google/gson)

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-Datei mit Beispieläußerungen

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Erstellen des Schnellstartcodes

1. Fügen Sie die Java-Abhängigkeiten der Datei namens `AddUtterances.java` hinzu.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Erstellen Sie die `AddUtterances`-Klasse. Diese Klasse enthält alle folgenden Codeausschnitte.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Fügen Sie die LUIS-Konstanten zur Klasse hinzu. Kopieren Sie den folgenden Code, und fügen Sie Ihren Erstellungsschlüssel, die Anwendungs-ID und die Versions-ID ein.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Fügen Sie der AddUtterances-Klasse die aufzurufende Methode der LUIS-API hinzu. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Fügen Sie der AddUtterances-Klasse die Methode für die HTTP-Antwort der LUIS-API hinzu.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Fügen Sie der AddUtterances-Klasse eine Ausnahmebehandlung hinzu. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Fügen Sie der AddUtterances-Klasse die main-Funktion hinzu.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Erstellen des Codes

Kompilieren Sie „AddUtterance“ mit den Abhängigkeiten:

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Ausführen des Codes
Wenn Sie `AddUtterance` ohne Argumente aufrufen, werden die LUIS-Äußerungen zur App hinzugefügt, ohne die App zu trainieren.

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

Die folgende Befehlszeile zeigt das Ergebnis des Aufrufs der API zum Hinzufügen von Äußerungen an. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Entfernen Sie nach dem Durchführen dieses Schnellstarts alle Dateien, die Sie im Rahmen der Schnellstartanleitung erstellt haben. 

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"] 
> [Programmgesteuertes Erstellen einer LUIS-App](luis-tutorial-node-import-utterances-csv.md) 
