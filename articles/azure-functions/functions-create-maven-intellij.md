---
title: Erstellen einer Azure-Funktion mit Java und IntelliJ | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Java und IntelliJ eine einfache, durch HTTP ausgelöste serverlose App auf Azure erstellen und veröffentlichen können.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: azure functions, funktionen, ereignisverarbeitung, compute, serverlose architektur, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: da93c60b52edf509900adf89fb688a0596d9763b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61342231"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Erstellen Ihrer ersten Azure-Funktion mit Java und IntelliJ

In diesem Artikel lernen Sie Folgendes:
- Erstellen eines [serverlosen](https://azure.microsoft.com/overview/serverless-computing/)-Funktionsprojekts mit IntelliJ IDEA und Apache Maven
- Schritte zum Testen und Debuggen der Funktion in der integrierten Entwicklungsumgebung (IDE) auf Ihrem eigenen Computer
- Anweisungen zum Bereitstellen des Funktionsprojekts in Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Installieren Sie die folgende Software, um eine Funktion mit Java und IntelliJ zu entwickeln:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), Version 8
- [Apache Maven](https://maven.apache.org), Version 3.0 oder höher
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Community- oder Ultimate-Version mit Maven
- [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Damit Sie die Schritte in diesem Artikel ausführen können, muss die Umgebungsvariable JAVA_HOME auf den Installationsspeicherort des JDK festgelegt sein.

 Es wird empfohlen, die [Azure Functions Core Tools, Version 2](functions-run-local.md#v2), zu installieren. Sie stellen eine lokale Entwicklungsumgebung zum Schreiben, Ausführen und Debuggen von Azure Functions bereit.

## <a name="create-a-functions-project"></a>Erstellen eines Functions-Projekts

1. Wählen Sie in IntelliJ IDEA **Neues Projekt erstellen** aus.  
1. Wählen Sie im Fenster **Neues Projekt** im linken Bereich **Maven** aus.
1. Aktivieren Sie das Kontrollkästchen **Create from archetype** (Aus Archetyp erstellen), und wählen Sie dann **Add Archetype** (Archetyp hinzufügen) für den [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) aus.
1. Füllen Sie im Fenster **Add Archetype** (Archetyp hinzufügen) die Felder wie folgt aus:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Version_: Verwenden Sie die neueste Version aus [dem zentralen Repository.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Erstellung eines Maven-Projekts aus einem Archetyp in IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Wählen Sie **OK** und anschließend **Weiter** aus.
1. Geben Sie Ihre Details für das aktuelle Projekt ein, und wählen Sie **Fertig stellen** aus.

Maven erstellt die Projektdateien in einem neuen Ordner mit dem Wert von _ArtifactId_ als Name. Bei dem generierten Code des Projekts handelt es sich um eine einfache [durch HTTP ausgelöste](/azure/azure-functions/functions-bindings-http-webhook) Funktion, die den Hauptteil der über HTTP ausgelösten Anforderung wiederholt.

## <a name="run-functions-locally-in-the-ide"></a>Lokales Ausführen von Funktionen in der IDE

> [!NOTE]
> Um Funktionen lokal ausführen und debuggen zu können, achten Sie darauf, dass die [Azure Functions Core Tools, Version 2](functions-run-local.md#v2), installiert sind.

1. Importieren Sie Änderungen manuell, oder aktivieren Sie den [automatischen Import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Öffnen Sie die Symbolleiste **Maven-Projekte**.
1. Erweitern Sie **Lifecycle** (Lebenszyklus), und öffnen Sie dann **Paket**. Die Lösung wird erstellt und in einem neu erstellten Zielverzeichnis verpackt.
1. Erweitern Sie **Plug-Ins** > **azure-functions**, und öffnen Sie **azure-functions:run**, um die Azure Functions-Runtime lokal zu starten.  
  ![Maven-Symbolleiste für Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Schließen Sie das Dialogfeld „Ausführen“, wenn Sie den Test der Funktion beendet haben. Es kann nicht mehr als ein Funktionshost gleichzeitig aktiv sein und lokal ausgeführt werden.

## <a name="debug-the-function-in-intellij"></a>Debuggen der Funktion in IntelliJ

1. Um den Funktionshost im Debugmodus zu starten, fügen Sie **-DenableDebug** als Argument hinzu, wenn Sie Ihre Funktion ausführen. Sie können entweder die Konfiguration in [maven goals](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ändern oder den folgenden Befehl in einem Terminalfenster ausführen:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Dieser Befehl bewirkt, dass der Funktionshost einen Debugport an 5005 öffnet.

1. Wählen Sie im Menü **Run** (Ausführen) **Edit Configurations** (Konfigurationen bearbeiten) aus.
1. Wählen Sie das Pluszeichen **+** aus, um einen **Remote** hinzuzufügen.
1. Füllen Sie die Felder _Name_ und _Einstellungen_ aus, und wählen Sie dann **OK** aus, um die Konfiguration zu speichern.
1. Wählen Sie nach dem Setup **Debug <Name der Remotekonfiguration>** aus, oder drücken Sie auf der Tastatur UMSCHALT+F9, um mit dem Debuggen zu beginnen.

   ![Debuggen von Funktionen in IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. Wenn Sie das Debuggen abgeschlossen haben, beenden Sie den Debugger und den laufenden Prozess. Es kann nicht mehr als ein Funktionshost gleichzeitig aktiv sein und lokal ausgeführt werden.

## <a name="deploy-the-function-to-azure"></a>Bereitstellen der Funktion in Azure

1. Bevor Sie Ihre Funktion in Azure bereitstellen können, müssen Sie sich [mithilfe der Azure CLI anmelden](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Stellen Sie Ihren Code mit `azure-functions:deploy` als Maven-Ziel in einer neuen Funktion bereit. Sie können auch die Option **azure-functions:deploy** im Maven Projects-Fenster aktivieren.

   ```
   mvn azure-functions:deploy
   ```

1. Suchen Sie die URL für Ihre Funktion in der Azure CLI-Ausgabe nach der erfolgreichen Bereitstellung der Funktion.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Entwickeln von Java-Funktionen finden Sie im [Azure Functions-Java-Entwicklerhandbuch](functions-reference-java.md).
- Fügen Sie mit `azure-functions:add` als Maven-Ziel zusätzliche Funktionen mit verschiedenen Triggern zu Ihrem Projekt hinzu.
