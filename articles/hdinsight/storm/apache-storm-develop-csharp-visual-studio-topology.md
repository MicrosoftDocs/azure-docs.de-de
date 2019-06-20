---
title: Apache Storm-Topologien mit Visual Studio und C# – Azure HDInsight
description: Lernen Sie, wie Sie Storm-Topologien in C# erstellen. Erstellen Sie mit den Hadoop-Tools für Visual Studio eine einfache Wortanzahl-Topologie in Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 14aa45808f44f7ca6fe34b70ef282a99f230bf0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687752"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Entwickeln von C#-Topologien für Apache Storm mithilfe von Data Lake-Tools für Visual Studio

Erfahren Sie, wie Sie eine C#-Apache Storm-Topologie mit Azure Data Lake-Tools (Apache Hadoop) für Visual Studio erstellen. In diesem Dokument werden Sie durch die Erstellung eines Storm-Projekts in Visual Studio, durch den zugehörigen lokalen Test und dessen Bereitstellung eines Apache Storm-Clusters in Azure HDInsight geleitet.

Außerdem erfahren Sie, wie hybride Topologien erstellt werden, die C#- und Java-Komponenten verwenden.

> [!NOTE]  
> Während Sie für die Schritte in diesem Dokument eine Windows-Entwicklungsumgebung mit Visual Studio benötigen, kann das kompilierte Projekt an einen Linux- oder Windows-basierten HDInsight-Cluster übermittelt werden. Nur Linux-basierte Cluster, die nach dem 28. Oktober 2016 erstellt wurden, unterstützen SCP.NET-Topologien.

Um eine C#-Topologie mit einem Linux-basierten Cluster zu verwenden, müssen Sie das NuGet-Paket „Microsoft.SCP.Net.SDK“, das vom Projekt verwendet wird, auf Version 0.10.0.6 oder höher aktualisieren. Die Version des Pakets muss zudem mit der Hauptversion der Storm-Installation auf HDInsight übereinstimmen.

| HDInsight-Version | Apache Storm-Version | SCP.NET-Version | Mono-Standardversion |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(nur in Windows-basiertem HDInsight) | Nicht verfügbar |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> C#-Topologien in Linux-basierten Clustern müssen .NET 4.5 verwenden. Zudem muss Mono im HDInsight-Cluster ausgeführt werden. Überprüfen Sie die [Mono-Kompatibilität](https://www.mono-project.com/docs/about-mono/compatibility/) auf mögliche Inkompatibilitäten.

## <a name="install-visual-studio"></a>Installieren von Visual Studio

Sie können C#-Topologien mit SCP.NET entwickeln, indem Sie eine der folgenden Versionen von Visual Studio verwenden:

* Visual Studio 2012 mit Update 4

* Visual Studio 2013 mit Update 4 oder [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 oder [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (beliebige Edition)

## <a name="install-data-lake-tools-for-visual-studio"></a>Installieren von Data Lake-Tools für Visual Studio

Folgen Sie den Schritten unter [Get started using Data Lake tools for Visual Studio (Erste Schritte mit den Data Lake-Tools für Visual Studio)](../hadoop/apache-hadoop-visual-studio-tools-get-started.md), um Data Lake-Tools für Visual Studio zu installieren.

## <a name="install-java"></a>Installieren von Java

Wenn Sie eine Storm-Topologie aus Visual Studio senden, generiert SCP.NET eine ZIP-Datei, welche die Topologie und Abhängigkeiten enthält. Java wird zur Erstellung dieser ZIP-Dateien verwendet, weil ein Format verwendet wird, das mehr mit Linux-basierten Cluster kompatibel ist.

1. Installieren Sie den Java Developer Kit (JDK) 7 oder höher in Ihre Entwicklungsumgebung. Sie erhalten die Oracle JDK von [Oracle](https://aka.ms/azure-jdks). Sie können auch [other Java distributions (andere Java-Distributionen)](https://openjdk.java.net/) verwenden.

2. Die Umgebungsvariable `JAVA_HOME` muss auf das Verzeichnis mit Java zeigen.

3. Die Umgebungsvariable `PATH` muss das Verzeichnis `%JAVA_HOME%\bin` beinhalten.

Sie können die folgende C#-Konsolenanwendung verwenden, um sicherzustellen, dass Java und das JDK ordnungsgemäß installiert sind:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm-Vorlagen

Die Data Lake-Tools für Visual Studio umfassen die folgenden Vorlagen:

| Projekttyp | Zeigt |
| --- | --- |
| Storm-Anwendung |Ein leeres Projekt der Storm-Topologie. |
| Storm Azure SQL Writer-Beispiel |So schreiben Sie in eine Azure SQL-Datenbank. |
| Storm Azure Cosmos DB Reader – Beispiel |So lesen Sie aus Azure Cosmos DB. |
| Storm Azure Cosmos DB Writer – Beispiel |So schreiben Sie in Azure Cosmos DB. |
| Storm EventHub Reader-Beispiel |So lesen Sie aus Azure Event Hubs. |
| Storm EventHub Writer-Beispiel |So schreiben Sie in Azure Event Hubs. |
| Storm HBase Reader-Beispiel |So lesen Sie aus HBase auf HDInsight-Clustern. |
| Storm HBase Writer-Beispiel |So schreiben Sie in HBase auf HDInsight-Clustern. |
| Storm-Hybrid-Beispiel |So verwenden Sie eine Java-Komponente. |
| Storm-Beispiel |Eine einfache Wortzählungstopologie. |

> [!WARNING]  
> Nicht alle Vorlagen funktionieren mit Linux-basierten HDInsight-Clustern. Die von den Vorlagen verwendeten NuGet-Pakete sind möglicherweise nicht mit Mono kompatibel. Überprüfen Sie das Dokument [Kompatibilität mit Mono](https://www.mono-project.com/docs/about-mono/compatibility/), und verwenden Sie [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis), um potenzielle Probleme zu identifizieren.

In den Schritten dieses Dokuments verwenden Sie das grundlegende Storm-Anwendungsprojekt, um eine Topologie zu erstellen.

### <a name="apache-hbase-templates-notes"></a>Hinweise zu Apache HBase-Vorlagen

In den HBase Reader- und Writer-Vorlagen wird die HBase-REST-API verwendet, nicht die HBase-Java-API, um mit HBase auf einem HDInsight-Cluster zu kommunizieren.

### <a name="eventhub-templates-notes"></a>Hinweise zu EventHub-Vorlagen

> [!IMPORTANT]  
> Die Java-basierte EventHub-Spout-Komponente, die in der EventHub-Reader-Vorlage enthalten ist, funktioniert möglicherweise nicht mit Storm auf HDInsight Version 3.5 oder höher. Eine aktualisierte Version dieser Komponente finden Sie unter [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Ein Topologiebeispiel, die diese Komponente verwendet und mit Storm auf HDInsight 3.5 arbeitet, finden Sie unter [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Erstellen einer C#-Topologie

1. Öffnen Sie Visual Studio, wählen Sie **Datei** > **Neu** aus, und wählen Sie anschließend **Projekt** aus.

2. Erweitern Sie auf dem Fenster **Neues Projekt** die Option **Installiert** > **Vorlagen**, und wählen Sie anschließend **Azure Data Lake** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung**aus. Geben Sie unten im Bildschirm **WordCount** als Namen der Anwendung ein.

    ![Screenshot des Fensters Neues Projekt](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Nachdem Sie das Projekt erstellt haben, sollten die folgenden Dateien vorliegen:

   * **Program.cs**: Diese Datei definiert die Topologie für Ihr Projekt. Eine Standardtopologie, die aus einem Spout und einem Bolt besteht, wird standardmäßig erstellt.

   * **Spout.cs**: Ein Beispiel-Spout, der Zufallszahlen ausgibt.

   * **Bolt.cs**: Ein Beispiel-Bolt, der eine Anzahl der vom Spout ausgegebenen Zahlen aufnimmt.

     Wenn Sie das Projekt erstellen, lädt NuGet das neueste [SCP.NET-Paket](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) herunter.

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementieren des Spouts

1. Öffnen Sie **Spout.cs**. Mithilfe der Spouts werden Daten aus einer externen Quelle in eine Topologie eingelesen. Wichtigste Komponenten für einen Spout:

   * **NextTuple**: Wird von Storm aufgerufen, wenn der Spout neue Tupel ausgeben darf.

   * **Ack** (nur transaktionale Topologie): Verarbeitet Bestätigungen, die von anderen Komponenten in der Topologie initiiert wurden, für Tupel, die von dem Spout gesendet wurden. Durch die Tupelbestätigung erfährt der Spout, dass die Verarbeitung durch nachgelagerte Komponenten erfolgreich ausgeführt wurde.

   * **Fail** (nur transaktionale Topologie): Verarbeitet Tupel, bei denen bei der Verarbeitung anderer Komponenten in der Topologie ein Fehler aufgetreten ist. Das Implementieren einer Fail-Methode ermöglicht es Ihnen, das Tupel erneut auszugeben, sodass es erneut verarbeitet werden kann.

2. Ersetzen Sie den Inhalt der **Spout**-Klasse durch folgenden Text: Dieses Spout gibt einen Satz willkürlich in die Topologie aus.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementieren der Bolts

1. Löschen Sie die vorhandene Datei **Bolt.cs** aus dem Projekt.

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Neues Element** aus. Wählen Sie in der Liste die Option **Storm Bolt** aus, und geben Sie **Splitter.cs** als Namen ein. Wiederholen Sie diesen Vorgang, um einen zweiten Bolt namens **Counter.cs** zu erstellen.

   * **Splitter.cs**: Implementiert einen Bolt, der Sätze in einzelne Wörter unterteilt und einen neuen Datenstrom von Wörtern ausgibt.

   * **Counter.cs**: Implementiert einen Bolt, der jedes Wort zählt und einen neuen Datenstrom von Wörtern sowie die Anzahl der einzelnen Wörter ausgibt.

     > [!NOTE]  
     > Obwohl diese Bolts in Datenströme schreiben und aus diesen lesen, können Sie mithilfe eines Bolts auch mit einer Datenbank oder einem Dienst kommunizieren.

3. Öffnen Sie **Splitter.cs**. Die Datei enthält standardmäßig nur eine Methode: **Execute**. Diese Execute-Methode wird aufgerufen, wenn der Bolt ein zu verarbeitendes Tupel empfängt. Hier können Sie eingehende Tupel lesen und verarbeiten sowie ausgehende Tupel ausgeben.

4. Ersetzen Sie den Inhalt der **Splitter** -Klasse durch folgenden Code:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Öffnen Sie **Counter.cs**, und ersetzen Sie die Klasseninhalte durch folgenden Code:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Definieren der Topologie

Spouts und Bolts werden in einem Diagramm angeordnet, das definiert, wie die Daten zwischen den Komponenten verlaufen. Bei dieser Topologie sieht das Diagramm folgendermaßen aus:

![Diagramm der Anordnung von Komponenten](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Sätze werden vom Spout ausgegeben und an Instanzen des Splitter-Bolts verteilt. Das Splitter-Bolt unterteilt Sätze in Wörter, die an das Counter-Bolt verteilt werden.

Da die Wortanzahl lokal in der Counter-Instanz gespeichert wird, sollten Sie sicherstellen, dass bestimmte Wörter an dieselbe Instanz des Counter-Bolts geleitet werden. Jede Instanz verfolgt bestimmte Wörter. Seit der Splitter-Bolt keinen Zustand verwaltet, ist es unerheblich, welche Instanz des Splitters welchen Satz empfängt.

Öffnen Sie die Datei **Program.cs**. Die entscheidende Methode ist **GetTopologyBuilder**, mit der die an Storm gesendete Topologie definiert wird. Ersetzen Sie den Inhalt von **GetTopologyBuilder** durch Folgendes, um die zuvor beschriebene Topologie zu implementieren:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Übermitteln der Topologie

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **An Storm in HDInsight übermitteln** aus.

   > [!NOTE]  
   > Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein. Wenn Sie über mehrere Abonnements verfügen, melden Sie sich bei dem Abonnement an, das Ihren Storm-Cluster in HDInsight enthält.

2. Wählen Sie in der Dropdownliste **Storm Cluster** Ihren Storm-Cluster in HDInsight und dann die Option **Übermitteln** aus. Sie können über das Fenster **Ausgabe** überwachen, ob die Übermittlung erfolgreich ausgeführt wurde.

3. Sobald die Topologie erfolgreich übermittelt wurde, sollten die **Storm-Topologien** für den Cluster angezeigt werden. Wählen Sie die **WordCount** -Topologie in der Liste aus, um Informationen zur aktiven Topologie anzuzeigen.

   > [!NOTE]  
   > Sie können sich **Storm-Topologien** auch über den **Server-Explorer** anzeigen lassen. Erweitern Sie **Azure** > **HDInsight**, klicken Sie mit der rechten Maustaste auf einen Storm im HDInsight-Cluster, und wählen Sie anschließend **Storm-Topologien anzeigen** aus.

    Doppelklicken Sie auf die Komponente im Diagramm, um Informationen zu den Komponenten in der Topologie anzuzeigen.

4. Wählen Sie in der Ansicht **Topologiezusammenfassung** die Option **Beenden** aus, um die Topologie zu beenden.

   > [!NOTE]  
   > Storm-Topologien werden weiterhin ausgeführt, bis sie deaktiviert werden oder der Cluster gelöscht wird.

## <a name="transactional-topology"></a>Transaktionale Topologie

Die vorherige Topologie ist nicht transaktional. Die Komponenten in der Topologie implementieren keine Funktionalität zum Wiedergeben von Meldungen. Erstellen Sie für ein Beispiel einer transaktionalen Topologie ein Projekt, und wählen Sie **Storm Sample** (Storm-Beispiel) als Projekttyp aus.

Transaktionale Topologien implementieren Folgendes, um die Wiedergabe von Daten zu unterstützen:

* **Metadaten-Zwischenspeicherung**: Der Spout muss Metadaten zu den ausgegebenen Daten speichern, damit die Daten bei einem auftretenden Fehler erneut abgerufen und ausgegeben werden können. Da die in diesem Beispiel ausgegebene Datenmenge klein ist, werden die Rohdaten der einzelnen Tupel zur Wiedergabe in einem Wörterbuch gespeichert.

* **Ack**: Jeder Bolt in der Topologie kann `this.ctx.Ack(tuple)` aufrufen, um die erfolgreiche Verarbeitung eines Tupels zu bestätigen. Nachdem das Tupel von allen Bolts bestätigt wurde, wird die `Ack` -Methode des Spouts aufgerufen. Die `Ack`-Methode ermöglicht dem Spout die Entfernung von Daten, die für die Wiedergabe zwischengespeichert wurden.

* **Fail**: Jeder Bolt kann `this.ctx.Fail(tuple)` aufrufen, um anzuzeigen, dass bei der Verarbeitung eines Tupels ein Fehler aufgetreten ist. Der Fehler wird an die `Fail` -Methode des Spouts weitergegeben, wo das Tupel mithilfe der zwischengespeicherten Daten wiedergegeben werden kann.

* **Sequence ID**: Beim Ausgeben eines Tupels kann eine eindeutige Sequenz-ID angegeben werden. Der Wert identifiziert das Tupel für die Wiedergabeverarbeitung („Bestätigung“ und „Fehler“). Der Spout im Projekt **Storm-Beispiel** verwendet z.B. Folgendes beim Ausgeben von Daten:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Dieser Code gibt einen Tupel aus, das einen Satz im Standarddatenstrom enthält, wobei der Sequenz-ID-Wert in **lastSeqId**enthalten ist. In diesem Beispiel wird **lastSeqId** für jedes ausgegebene Tupel erhöht.

Wie im Projekt **Storm Sample** (Storm-Beispiel) veranschaulicht, kann zur Laufzeit auf Basis der Konfiguration angegeben werden, ob es sich um eine transaktionale Komponente handelt.

## <a name="hybrid-topology-with-c-and-java"></a>Hybridtopologie mit C# und Java

Sie können auch Data Lake-Tools für Visual Studio zum Erstellen von hybriden Topologien verwenden, bei denen einige Komponenten C# und andere Java sind.

Erstellen Sie für ein Beispiel einer hybriden Topologie ein Projekt, und wählen Sie **Storm Hybrid Sample** (Storm-Hybrid-Beispiel) aus. Dieser Beispieltyp veranschaulicht die folgenden Konzepte:

* **Java-Spout** und  **C#-Bolt**: Definition in **HybridTopology_javaSpout_csharpBolt**.

    * Eine transaktionale Version ist in **HybridTopologyTx_javaSpout_csharpBolt** definiert.

* **C#-Spout** und **Java-Bolt**: Definition in **HybridTopology_csharpSpout_javaBolt**.

    * Eine transaktionale Version ist in **HybridTopologyTx_csharpSpout_javaBolt** definiert.

  > [!NOTE]  
  > Diese Version veranschaulicht zudem, wie Sie Clojure-Code aus einer Textdatei als Java-Komponente verwenden.


Um die Topologie zu wechseln, die beim Übermitteln des Projekts verwendet wird, verschieben Sie vor der Übermittlung an den Cluster die Anweisung `[Active(true)]` in die Topologie, die Sie verwenden möchten.

> [!NOTE]  
> Alle erforderliche Java-Dateien werden im Rahmen dieses Projekts im Ordner **JavaDependency** bereitgestellt.

Bedenken Sie beim Erstellen und Übermitteln einer hybriden Topologie Folgendes:

* Verwenden Sie **JavaComponentConstructor**, um eine Instanz der Java-Klasse für einen Spout oder Bolt zu erstellen.

* Verwenden Sie **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** zum Serialisieren von Daten in bzw. aus Java-Komponenten von Java-Objekten zu JSON.

* Beim Übermitteln der Topologie an den Server müssen Sie die Option **Zusätzliche Konfigurationen** verwenden, um die **Java-Dateipfade** anzugeben. Der angegebene Pfad muss sich in dem Verzeichnis befinden, das die JAR-Dateien mit den Java-Klassen enthält.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.Net Version 0.9.4.203 bietet eine neue Klasse und Methode speziell für die Arbeit mit dem Event Hub-Spout (ein Java-Spout, der von Event Hub liest). Verwenden Sie die folgenden Methoden beim Erstellen einer Topologie, die einen Event Hub-Spout verwendet:

* **EventHubSpoutConfig**-Klasse: Erstellt ein Objekt, das die Konfiguration für die Spout-Komponente enthält.

* **TopologyBuilder.SetEventHubSpout**-Methode: Fügt die Event Hub-Spout-Komponente der Topologie hinzu.

> [!NOTE]  
> Sie müssen **CustomizedInteropJSONSerializer** weiterhin zum Serialisieren von Spout erzeugten Daten verwenden.

## <a id="configurationmanager"></a>Verwenden von ConfigurationManager

Verwenden Sie **ConfigurationManager** nicht zum Abrufen von Konfigurationswerten aus Bolt- und Spout-Komponenten. Dies kann zu einer NULL-Zeigerausnahme führen. Stattdessen wird die Konfiguration für das Projekt in der Storm-Topologie als Schlüssel- und Wertpaar im Topologiekontext übergeben. Jede Komponente, die von Konfigurationswerten abhängig ist, muss sie während der Initialisierung aus dem Kontext abrufen.

Der folgende Code veranschaulicht, wie diese Werte abgerufen werden:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Bei Verwendung einer `Get`-Methode zum Zurückgeben einer Instanz Ihrer Komponente müssen Sie sicherstellen, dass die beiden Parameter `Context` und `Dictionary<string, Object>` an den Konstruktor übergeben werden. Das folgende Beispiel ist eine einfache `Get`-Methode, die diese Werte ordnungsgemäß übergibt:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Gewusst wie: Aktualisieren von SCP.NET

Neuere Versionen von SCP.NET unterstützen die Paketaktualisierung über NuGet. Wenn ein neues Update verfügbar ist, erhalten Sie eine Upgradebenachrichtigung. Gehen Sie wie folgt vor, um eine Überprüfung auf ein Upgrade manuell durchzuführen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

2. Wählen Sie im Paket-Manager die Option **Updates**. Wenn ein Update verfügbar ist, wird es aufgeführt. Klicken Sie auf **Aktualisieren**, damit das Paket es installiert.

> [!IMPORTANT]  
> Wenn Ihr Projekt mit einer früheren Versionen von SCP.NET erstellt wurde, bei denen NuGet nicht verwendet wurde, müssen Sie zum Aktualisieren auf eine neuere Version die folgenden Schritte ausführen:
>
> 1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.
> 2. Suchen Sie über das Feld **Suche** nach **Microsoft.SCP.Net.SDK**, und fügen Sie es dem Projekt hinzu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Problembehandlung für allgemeine Probleme mit Topologien

### <a name="null-pointer-exceptions"></a>NULL-Zeigerausnahmen

Wenn Sie eine C#-Topologie mit einem Linux-basierten HDInsight-Cluster verwenden, können Bolt- und Spout-Komponenten, die zum Lesen von Konfigurationseinstellungen zur Laufzeit **ConfigurationManager** nutzen, NULL-Zeigerausnahmen zurückgeben.

Die Konfiguration wird für das Projekt in der Storm-Topologie als Schlüssel- und Wertpaar im Topologiekontext übergeben. Es kann über das Dictionary-Objekt abgerufen werden, das von Komponenten übergeben wird, wenn sie initialisiert werden.

Weitere Informationen finden Sie im Abschnitt [ConfigurationManager](#configurationmanager) dieses Dokuments.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Wenn Sie eine C#-Topologie mit einem Linux-basierten HDInsight-Cluster verwenden, kann der folgende Fehler auftreten:

    System.TypeLoadException: Failure has occurred while loading a type.

Dieser Fehler geschieht wenn Sie eine Binärdatei verwenden, die nicht mit der Version von .NET kompatibel ist, die Mono unterstützt.

Stellen Sie bei Linux-basierten HDInsight-Clustern sicher, dass das Projekt für .NET 4.5 kompilierte Binärdateien verwendet.

### <a name="test-a-topology-locally"></a>Lokales Testen einer Topologie

Obwohl es relativ einfach ist, eine Topologie in einem Cluster bereitzustellen, müssen Sie eine Topologie möglicherweise auch lokal testen. Gehen Sie wie folgt vor, um die Beispieltopologie in diesem Lernprogramm lokal in Ihrer Entwicklungsumgebung auszuführen und zu testen.

> [!WARNING]  
> Lokale Tests funktionieren nur für einfache C#-Topologien. Sie können keine lokalen Tests für hybride Topologien verwenden oder für Topologien, in denen mehrere Streams verwendet werden.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus. Wählen Sie in den Projekteigenschaften für **Ausgabetyp** die Option **Konsolenanwendung** aus.

    ![Bildschirmabbildung von Projekteigenschaften mit hervorgehobenem Ausgabetyp](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Denken Sie daran, für den **Ausgabetyp** später wieder **Klassenbibliothek** festzulegen, bevor die Topologie in einem Cluster bereitgestellt wird.

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Hinzufügen** > **Neues Element** aus. Wählen Sie **Klasse** aus, und geben Sie **LocalTest.cs** als Klassennamen ein. Klicken Sie abschließend auf **Hinzufügen**.

3. Öffnen Sie die Datei **LocalTest.cs**, und fügen Sie die folgende **using**-Anweisung am Anfang hinzu:

    ```csharp
    using Microsoft.SCP;
    ```

4. Fügen Sie den folgenden Code als Inhalt in die **LocalTest**-Klasse ein:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Nehmen Sie sich einen Moment Zeit, um die Codekommentare zu lesen. Dieser Code verwendet **LocalContext** , um die Komponenten in der Entwicklungsumgebung auszuführen, wobei der Datenstrom zwischen den Komponenten in Textdateien auf dem lokalen Laufwerk bestehen bleibt.

1. Öffnen Sie die Datei **Program.cs**, und fügen Sie der Methode **Main** Folgendes hinzu:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Speichern Sie die Änderungen, und klicken Sie dann auf **F5** oder wählen Sie **Debuggen** > **Debugging starten** aus, um das Projekt zu starten. Daraufhin sollte ein Konsolenfenster angezeigt und mit fortschreitendem Test der Status protokolliert werden. Nachdem **Tests abgeschlossen** angezeigt wurde, drücken Sie eine beliebige Taste, um das Fenster zu schließen.

3. Verwenden Sie **Windows Explorer**, um das Verzeichnis zu suchen, welches das Projekt enthält. Beispiel:  **C:\Benutzer\<Ihr_Benutzername>\Dokumente\Visual Studio 2013\Projects\WordCount\WordCount**. Öffnen Sie in diesem Verzeichnis das Unterverzeichnis **Bin**, und klicken Sie dann auf **Debug**. Es sollten die beim Ausführen der Tests erstellten Textdateien vorhanden sein: "sentences.txt", "counter.txt" und "splitter.txt". Öffnen Sie die einzelnen Textdateien, und überprüfen Sie die Daten.

   > [!NOTE]  
   > Zeichenfolgendaten bleiben in diesen Dateien als Array von Dezimalwerten erhalten. Beispiel: \[[97,103,111]] in der Datei **splitter.txt** entspricht dem Wort *and*.

> [!NOTE]  
> Vergewissern Sie sich, dass Sie für den **Projekttyp** vor der Bereitstellung in einem Storm in HDInsight-Cluster wieder **Klassenbibliothek** festlegen.

### <a name="log-information"></a>Protokollieren von Informationen

Sie können Informationen aus den Topologiekomponenten problemlos mithilfe von `Context.Logger` protokollieren. Der folgende Befehl generiert beispielsweise einen informativen Protokolleintrag:

```csharp
Context.Logger.Info("Component started");
```

Protokollierte Informationen können über das **Hadoop-Dienstprotokoll** angezeigt werden, das sich im **Server-Explorer** befindet. Erweitern Sie den Eintrag für Ihren Storm-Cluster in HDInsight, und erweitern Sie anschließend das **Hadoop-Dienstprotokoll**. Wählen Sie abschließend die anzuzeigende Protokolldatei aus.

> [!NOTE]  
> Die Protokolle werden in dem Azure-Speicherkonto gespeichert, das von Ihrem Cluster verwendet wird. Zum Anzeigen der Protokolle in Visual Studio müssen Sie sich beim Azure-Abonnement anmelden, welches das Speicherkonto besitzt.

### <a name="view-error-information"></a>Anzeigen von Fehlerinformationen

Gehen Sie wie folgt vor, um die Fehler anzuzeigen, die in einer aktiven Topologie aufgetreten sind:

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Storm in HDInsight-Cluster, und wählen Sie **Storm-Topologien anzeigen** aus.

2. Für **Spout** und **Bolts** enthält die Spalte **Letzter Fehler** die Informationen zum letzten Fehler.

3. Wählen Sie die **Spout-ID** oder **Bolt-ID** für die Komponente aus, für die ein Fehler aufgeführt ist. Auf der angezeigten Detailseite werden am unteren Rand zusätzliche Fehlerinformationen im Abschnitt **Fehler** aufgeführt.

4. Um weitere Informationen zu erhalten, wählen Sie einen **Port** im Abschnitt **Executor** aus, um das Storm-Arbeitsprotokoll für die letzten Minuten anzuzeigen.

### <a name="errors-submitting-topologies"></a>Fehler beim Übermitteln von Topologien

Wenn beim Übermitteln einer Topologie an HDInsight Fehler auftreten, stehen Ihnen Protokolle für die serverseitigen Komponenten zur Verfügung, die die Übermittlung von Topologien auf Ihren HDInsight-Cluster behandeln. Verwenden Sie zum Abrufen dieser Protokolle über die Befehlszeile den folgenden Befehl:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Ersetzen Sie __sshuser__ durch das SSH-Benutzerkonto für den Cluster. Ersetzen Sie __clustername__ durch den Namen des HDInsight-Clusters. Weitere Informationen zum Verwenden von `scp` und `ssh` mit HDInsight finden Sie unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Übermittlungen können aus mehreren Gründen fehlschlagen:

* JDK ist nicht installiert oder nicht im Pfad.
* Erforderliche Java-Abhängigkeiten sind nicht in der Übermittlung enthalten.
* Inkompatible Abhängigkeiten.
* Doppelte Topologienamen.

Wenn das Protokoll `hdinsight-scpwebapi.out` eine `FileNotFoundException` enthält, ist dies möglicherweise auf die folgenden Bedingungen zurückzuführen:

* Das JDK befindet sich nicht im Pfad in der Entwicklungsumgebung. Stellen Sie sicher, dass das JDK in der Entwicklungsumgebung installiert ist und dass `%JAVA_HOME%/bin` sich im Pfad befindet.
* Ihnen fehlt eine Java-Abhängigkeit. Stellen Sie sicher, dass Sie alle erforderlichen JAR-Dateien als Teil der Übermittlung einschließen.

## <a name="next-steps"></a>Nächste Schritte

Ein Beispiel der Verarbeitung von Daten von Event Hubs finden Sie unter [Verarbeitung von Ereignissen von Azure-Event-Hubs mit Storm auf HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Ein Beispiel einer C#-Topologie, die Streamdaten in mehrere Datenströme aufteilt, finden Sie unter [C# Storm example](https://github.com/Blackmist/csharp-storm-example)(in englischer Sprache).

Weitere Informationen zum Erstellen der C#-Topologien finden Sie unter [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Weitere Möglichkeiten zum Arbeiten mit HDInsight sowie weitere Beispiele für Storm in HDInsight finden Sie in folgenden Dokumenten:

**Microsoft SCP.NET**

* [SCP-Programmierleitfaden](apache-storm-scp-programming-guide.md)

**Apache Storm in HDInsight**

* [Bereitstellen und Überwachen von Topologien mit Apache Storm in HDInsight](apache-storm-deploy-monitor-topology.md)
* [Beispieltopologien für Apache Storm in HDInsight](apache-storm-example-topology.md)

**Apache Hadoop in HDInsight**

* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](../hadoop/hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit Apache Hadoop in HDInsight](../hadoop/hdinsight-use-pig.md)
* [Verwenden von Apache Hadoop MapReduce mit Apache Hadoop in HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase in HDInsight**

* [Erste Schritte mit Apache HBase in HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
