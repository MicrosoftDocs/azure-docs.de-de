---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: c9ed54f11cade20af67a1c9bfe948b03e9d7b0d3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95098562"
---
In dieser Schnellstartanleitung erfahren Sie, wie Sie das Speech Devices SDK für Windows verwenden, um ein sprachaktiviertes Produkt zu erstellen oder es als Gerät für die [Unterhaltungstranskription](../conversation-transcription.md) zu verwenden. Für die Unterhaltungstranskription wird nur [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) unterstützt. Für andere Speech-Funktionen werden lineare Mikrofonarrays unterstützt, die eine Mikrofonarray-Geometrie bereitstellen.

Die Anwendung wird mit dem Speech SDK-Paket und der Eclipse Java-IDE (v4) unter 64-Bit-Windows erstellt. Sie wird in einer 64-Bit Java 8-JRE (Runtime Environment) ausgeführt.

Für diese Anleitung wird ein [Azure Cognitive Services-Konto](../overview.md#try-the-speech-service-for-free) mit einer Ressource für den Speech-Dienst benötigt.

Der Quellcode für die [Beispielanwendung](https://aka.ms/sdsdk-download-JRE) ist im Speech Devices SDK enthalten. Es ist auch auf [GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* Betriebssystem: 64-Bit-Windows
* Ein Mikrofonarray (beispielsweise [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/))
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Nur [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) oder [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Ein Azure-Abonnementschlüssel für den Speech-Dienst. [Hier erhalten Sie einen kostenlosen Schlüssel.](../overview.md#try-the-speech-service-for-free)
* Laden Sie die aktuelle Version des [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) für Java herunter, und extrahieren Sie die ZIP in Ihrem Arbeitsverzeichnis.
   > [!NOTE]
   > In dieser Schnellstartanleitung wird davon ausgegangen, dass die App in „C:\SDSDK\JRE-Sample-Release“ extrahiert wird.

Die Unterhaltungstranskription ist derzeit nur für „en-US“ und „zh-CN“ in den Regionen „centralus“ und „eastasia“ verfügbar. Sie müssen in einer dieser Regionen über einen Sprachschlüssel verfügen, um die Unterhaltungstranskription verwenden zu können.

Wenn Sie planen, die Absichten zu nutzen, benötigen Sie ein Abonnement des [Diensts „Language Understanding“ (LUIS)](../../luis/luis-how-to-azure-subscription.md). Weitere Informationen zu LUIS und zur Absichtserkennung finden Sie unter [Tutorial: Erkennen von Absichten anhand von gesprochener Sprache mit dem Speech SDK für C#](../how-to-recognize-intents-from-speech-csharp.md). Für diese App ist ein [Beispiel für ein LUIS-Modell](https://aka.ms/sdsdk-luis) verfügbar.

## <a name="create-and-configure-the-project"></a>Erstellen und Konfigurieren des Projekts

1. Starten Sie Eclipse.

1. Geben Sie im **Startprogramm für die Eclipse-IDE** im Feld **Arbeitsbereich** den Namen eines neuen Arbeitsbereichsverzeichnisses ein. Wählen Sie dann **Starten** aus.

   ![Screenshot: Startprogramm für Eclipse, in dem Sie den Namen Ihres Arbeitsbereichsverzeichnisses eingeben](../media/speech-devices-sdk/eclipse-launcher.png)

1. Nach kurzer Zeit wird das Hauptfenster der Eclipse-IDE angezeigt. Schließen Sie den Begrüßungsbildschirm, wenn ein solcher vorhanden ist.

1. Erstellen Sie über die Eclipse-Menüleiste ein neues Projekt, indem Sie **Datei** > **Neu** > **Java-Projekt** wählen. Falls dies nicht möglich ist, können Sie **Projekt** und dann **Java-Projekt** wählen.

1. Der **Assistent für ein neues Java-Projekt** wird gestartet. Verwenden Sie **Durchsuchen**, um nach dem Speicherort des Beispielprojekts zu suchen. Wählen Sie **Fertig stellen** aus.

   ![Screenshot: Assistent für ein neues Java-Projekt](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. Klicken Sie im **Paket-Explorer** mit der rechten Maustaste auf Ihr Projekt. Wählen Sie **Konfigurieren** > **In Maven-Projekt konvertieren** aus dem Kontextmenü aus. Wählen Sie **Fertig stellen** aus.

   ![Screenshot des Paket-Explorers](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Öffnen Sie die Datei „pom.xml“, und bearbeiten Sie sie.

    Erstellen Sie am Ende der Datei vor dem schließenden Tag `</project>` Elemente vom Typ `repositories` und `dependencies`, wie hier zu sehen, und stellen Sie sicher, dass der Wert von `version` Ihrer aktuellen Version entspricht:
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.14.0</version>
        </dependency>
    </dependencies>
   ```

1. Kopieren Sie den Inhalt von **Windows-x64** an den Speicherort des Java-Projekts (beispielsweise **C:\SDSDK\JRE-Sample-Release**).

1. Kopieren Sie `kws.table`, `participants.properties` und `Microsoft.CognitiveServices.Speech.extension.pma.dll` in den Projektordner **target\classes**.

## <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

1. Fügen Sie dem Quellcode den Abonnementschlüssel für Ihre Spracherkennung hinzu. Wenn Sie die Absichtserkennung ausprobieren möchten, fügen Sie auch Ihren Abonnementschlüssel für den [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) und die Anwendungs-ID hinzu.

   Für die Spracherkennung und LUIS werden Ihre Informationen in `FunctionsList.java` gespeichert:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Wenn Sie die Unterhaltungstranskription verwenden, sind Ihr Sprachschlüssel und die Regionsdaten auch in `Cts.java` erforderlich:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Das Standardschlüsselwort (keyword) ist „Computer“. Sie können auch eines der anderen angebotenen Schlüsselwörter wie „Machine“ oder „Assistant“ ausprobieren. Die Ressourcendateien für diese alternativen Schlüsselwörter finden Sie im Speech Devices SDK im Ordner „keyword“. Beispielsweise enthält `C:\SDSDK\JRE-Sample-Release\keyword\Computer` die Dateien für das Schlüsselwort „Computer“.

    > [!TIP]
    > Sie können auch [ein benutzerdefiniertes Schlüsselwort erstellen](../custom-keyword-basics.md).

    Wenn Sie ein neues Schlüsselwort verwenden möchten, aktualisieren Sie die folgende Zeile in `FunctionsList.java`, und kopieren Sie das Schlüsselwort in Ihre App. Gehen Sie beispielsweise wie folgt vor, wenn Sie das Schlüsselwort „Machine“ aus dem Schlüsselwortpaket `machine.zip` verwenden möchten:

   * Kopieren Sie die Datei `kws.table` aus dem ZIP-Paket in den Projektordner **target/classes**.
   * Aktualisieren Sie `FunctionsList.java` mit dem Schlüsselwortnamen:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Ausführen der Beispielanwendung aus Eclipse

1. Wählen Sie in der Eclipse-Menüleiste **Run** > **Run As** > **Java Application** (Ausführen > Ausführen als > Java-Anwendung). Wählen Sie anschließend **FunctionsList** und **OK**.

   ![Screenshot: Auswählen von „Java-Anwendung“](../media/speech-devices-sdk/eclipse-run-sample.png)

1. Die Beispielanwendung des Speech-Geräte-SDK wird gestartet und zeigt die folgenden Optionen an:

   ![Screenshot: Beispielanwendung des Speech-Geräte-SDK und Optionen](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Testen Sie die neue Demoversion der **Unterhaltungstranskription**. Beginnen Sie mit der Transkription, indem Sie **Session** > **Start** (Sitzung > Starten) verwenden. Standardmäßig ist jeder Gast. Wenn Sie aber über Stimmsignaturen der Teilnehmer verfügen, können diese im Projektordner **target/classes** in die Datei `participants.properties` eingefügt werden. Informationen zum Generieren der Stimmsignatur finden Sie unter [Transkribieren von Konversationen (SDK)](../how-to-use-conversation-transcription.md).

   ![Screenshot: Demo für Unterhaltungstranskription-Anwendung](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Erstellen und Ausführen einer eigenständigen Anwendung

1. Klicken Sie im **Paket-Explorer** mit der rechten Maustaste auf Ihr Projekt. Wählen Sie **Exportieren**.

1. Das Fenster **Exportieren** wird angezeigt. Erweitern Sie die Option **Java**, und wählen Sie **Runnable JAR file** (Ausführbare JAR-Datei) und dann **Weiter**.

   ![Screenshot: Exportfenster für die Auswahl der ausführbaren JAR-Datei](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Das Fenster **Runnable JAR File Export** (Ausführbare JAR-Datei: Export) wird angezeigt. Wählen Sie ein **Exportziel** für die Anwendung aus, und wählen Sie anschließend **Fertig stellen**.

   ![Screenshot: Exportfenster für ausführbare JAR-Datei, in dem Sie das Exportziel auswählen](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Fügen Sie `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` und `Microsoft.CognitiveServices.Speech.extension.pma.dll` in den oben gewählten Zielordner ein, da diese Dateien von der Anwendung benötigt werden.

1. Ausführen der eigenständigen Anwendung

   ```powershell
   java -jar SpeechDemo.jar
   ```