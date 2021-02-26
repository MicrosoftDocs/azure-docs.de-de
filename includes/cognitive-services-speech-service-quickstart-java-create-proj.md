---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 68e83ca0fc92247a31e840e76fc8019736b71dd9
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215431"
---
1. Starten Sie Eclipse.

1. Geben Sie im Eclipse-Startprogramm im Feld **Arbeitsbereich** den Namen eines neuen Arbeitsbereichsverzeichnisses ein. Wählen Sie dann **Starten** aus.

   ![Screenshot des Eclipse-Startprogramms](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Nach kurzer Zeit wird das Hauptfenster der Eclipse-IDE angezeigt. Schließen Sie den **Begrüßungsbildschirm**, sofern vorhanden.

1. Erstellen Sie über die Eclipse-Menüleiste ein neues Projekt, indem Sie **Datei** > **Neu** > **Projekt** auswählen.

1. Das Dialogfeld **Neues Projekt** wird angezeigt. Wählen Sie **Java-Projekt** aus, und wählen Sie dann **Weiter** aus.

   ![Screenshot des Dialogfelds „Neues Projekt“ mit hervorgehobenem Java-Projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Der **Assistent für ein neues Java-Projekt** wird gestartet. Geben Sie im Feld **Projektname** den Namen **Schnellstart** ein, und wählen Sie **JavaSE-1.8** als Ausführungsumgebung aus. Wählen Sie **Fertig stellen** aus.

   ![Screenshot des Assistenten für ein neues Java-Projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Wenn das Fenster **Zugeordnete Perspektive öffnen?** angezeigt wird, wählen Sie **Perspektive öffnen** aus.

1. Klicken Sie im **Paket-Explorer** mit der rechten Maustaste auf das Projekt **Schnellstart**. Wählen Sie **Konfigurieren** > **In Maven-Projekt konvertieren** aus dem Kontextmenü aus.

   ![Screenshot des Paket-Explorers](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Das Fenster **Neue POM-Datei erstellen** wird angezeigt. Geben Sie *com.microsoft.cognitiveservices.speech.samples* in das Feld **Gruppen-ID** und *Schnellstart* in das Feld **Artefakt-ID** ein. Wählen Sie dann **Fertig stellen** aus.

   ![Screenshot des Fensters „Neue POM-Datei erstellen“](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Öffnen Sie die Datei *pom.xml*, und bearbeiten Sie diese.

   * Erstellen Sie wie hier gezeigt vor dem schließenden Tag `</project>` am Ende der Datei ein `repositories`-Element mit einem Verweis auf das Maven-Repository für das Speech SDK:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Fügen Sie außerdem ein Element vom Typ `dependencies` mit der Version 1.15.0 des Speech SDK als Abhängigkeit hinzu:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Speichern Sie die Änderungen.
