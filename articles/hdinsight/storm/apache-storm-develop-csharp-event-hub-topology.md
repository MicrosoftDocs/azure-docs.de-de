---
title: Verarbeiten von Ereignissen aus Event Hubs mit Storm – Azure HDInsight
description: Erfahren Sie, wie Sie Azure Event Hubs-Daten mit einer in Visual Studio erstellten C#-Storm-Topologie mithilfe der HDInsight-Tools für Visual Studio verarbeiten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: dac56059455a75f4d64a698c416dc22793432bc8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545598"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Storm in HDInsight (C#)

Erfahren Sie mehr über die Verwendung von Azure Event Hubs aus [Apache Storm](https://storm.apache.org/) in HDInsight. In diesem Dokument wird eine C#-Storm-Topologie zum Lesen und Schreiben von Daten aus Event Hubs verwendet.

> [!NOTE]  
> Eine Java-Version dieses Projekts finden Sie unter [Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Storm in HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

In den Schritten in diesem Dokument wird SCP.NET verwendet, ein NuGet-Paket, das die Erstellung von C#-Topologien und -Komponenten zur Verwendung mit Storm in HDInsight vereinfacht.

Die HDInsight-Versionen 3.4 und höher verwenden Mono, um C#-Topologien auszuführen. Das in diesem Dokument verwendete Beispiel funktioniert mit HDInsight 3.6. Wenn Sie eigene .NET-Lösungen für HDInsight erstellen möchten, finden Sie im Dokument [Mono-Kompatibilität](https://www.mono-project.com/docs/about-mono/compatibility/) Informationen zu möglichen Inkompatibilitäten.

### <a name="cluster-versioning"></a>Clusterversionsverwaltung

Das Microsoft.SCP.Net.SDK-NuGet-Paket, das Sie für Ihr Projekt verwenden, muss der Hauptversion von Storm entsprechen, die auf HDInsight installiert ist. Die HDInsight-Versionen 3.5 und 3.6 verwenden Storm 1.x, daher müssen Sie mit diesen Clustern die SCP.NET-Version 1.0.x.x verwenden.

C#-Topologien müssen auch auf .NET 4.5 ausgerichtet sein.

## <a name="how-to-work-with-event-hubs"></a>Arbeiten mit Event Hubs

Microsoft stellt eine Reihe von Java-Komponenten bereit, die für die Kommunikation mit Event Hubs über eine Storm-Topologie verwendet werden können. Die Java-Archivdatei (JAR), die eine mit HDInsight 3.6 kompatible Version dieser Komponenten enthält, finden Sie unter [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]  
> Die Komponenten sind zwar in Java geschrieben, Sie können sie jedoch problemlos in einer C#-Topologie verwenden.

In diesem Beispiel werden die folgenden Komponenten verwendet:

* __EventHubSpout__ : Liest Daten aus Event Hubs.
* __EventHubBolt__ : Schreibt Daten in Event Hubs.
* __EventHubSpoutConfig__ : Wird zum Konfigurieren von EventHubSpout verwendet.
* __EventHubBoltConfig__ : Wird zum Konfigurieren von EventHubBolt verwendet.

### <a name="example-spout-usage"></a>Beispiel für die Verwendung von Spouts

SCP.NET stellt Methoden bereit, die zum Hinzufügen eines EventHubSpout zu Ihrer Topologie dienen. Mit diesen Methoden lassen sich Spouts einfach hinzufügen als mit den allgemeinen Methoden zum Hinzufügen einer Java-Komponente. Das folgende Beispiel zeigt, wie Sie mit dem __SetEventHubSpout__ und den von SCP.NET bereitgestellten **EventHubSpoutConfig** -Methoden einen Spout erstellen:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Das obige Beispiel erstellt eine neue Spoutkomponente namens __EventHubSpout__ und konfiguriert diese für die Kommunikation mit einem Event Hub. Der Parallelitätshinweis für die Komponente ist auf die Anzahl von Partitionen im Event Hub festgelegt. Durch diese Einstellung kann Storm eine Instanz der Komponente für jede Partition erstellen.

### <a name="example-bolt-usage"></a>Beispiel für die Verwendung von Bolts

Verwenden Sie die **JavaComponentConstructor** -Methode, um eine Instanz des Bolts zu erstellen. Das folgende Beispiel veranschaulicht, wie Sie eine neue Instanz von **EventHubBolt** erstellen und konfigurieren:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> Dieses Beispiel verwendet einen als Zeichenfolge übergebenen Clojure-Ausdruck anstelle von **JavaComponentConstructor** , um eine **EventHubBoltConfig** -Komponente zu erstellen, wie es das Spout-Beispiel getan hat. Beide Methoden funktionieren. Verwenden Sie die Methode, die für Sie am besten scheint.

## <a name="download-the-completed-project"></a>Herunterladen des abgeschlossenen Projekts

Sie können eine vollständige Version des in diesem Artikel erstellten Projekts von [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) herunterladen. Sie müssen jedoch weiterhin Konfigurationseinstellungen bereitstellen, indem Sie die Schritte in diesem Artikel befolgen.

### <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Storm-Cluster in HDInsight. Siehe [Erstellen von Apache Hadoop-Clustern im Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md), und wählen Sie **Storm** für **Clustertyp** aus.

* Einen [Azure Event Hub](../../event-hubs/event-hubs-create.md).

* Das [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* Die [HDInsight-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 oder höher in Ihrer Entwicklungsumgebung. JDK-Downloads stehen bei [Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support) zur Verfügung.

  * Die **JAVA_HOME** -Umgebungsvariable muss auf das Verzeichnis mit Java zeigen.
  * Das **%JAVA_HOME%/bin** -Verzeichnis muss im Pfad liegen.

## <a name="download-the-event-hubs-components"></a>Herunterladen der Event Hub-Komponenten

Herunterladen der Event Hubs-Spout- und -Bolt-Komponente von [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Erstellen Sie ein Verzeichnis mit dem Namen `eventhubspout`, und speichern Sie die Datei in dem Verzeichnis.

## <a name="configure-event-hubs"></a>Konfigurieren von Event Hubs

Event Hubs ist die Datenquelle für dieses Beispiel. Verwenden Sie die Informationen im Abschnitt „Erstellen eines Event Hubs“ in [Erste Schritte mit Event Hubs](../../event-hubs/event-hubs-create.md).

1. Nachdem der Event Hub erstellt wurde, zeigen Sie die **EventHub** -Einstellungen im Azure-Portal an, und wählen Sie **Freigegebene Zugriffsrichtlinien** aus. Wählen Sie **+ Hinzufügen** aus, um die folgenden Richtlinien zu erstellen:

   | Name | Berechtigungen |
   | --- | --- |
   | writer |Send |
   | reader |Lauschen |

    ![Screenshot des Fensters „Freigegebene Zugriffsrichtlinien“](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Wählen Sie die Richtlinien **reader** und **writer** aus. Kopieren und speichern Sie den Primärschlüsselwert für beide Richtlinien, da diese Werte später verwendet werden.

## <a name="configure-the-eventhubwriter"></a>Konfigurieren von EventHubWriter

1. Wenn Sie die neueste Version der HDInsight-Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter [Erste Schritte mit den HDInsight-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Laden Sie die Projektmappe von [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) herunter.

3. Öffnen Sie **EventHubExample.sln** . Öffnen Sie im Projekt **EventHubWriter** die Datei **App.config** . Verwenden Sie die Informationen aus dem Event Hub, den Sie zuvor konfiguriert haben, um den Wert für die folgenden Schlüssel anzugeben:

   | Key | value |
   | --- | --- |
   | EventHubPolicyName |writer (Wenn Sie einen anderen Namen für die Richtlinie mit der Berechtigung *Send* verwendet haben, verwenden sie ihn stattdessen.) |
   | EventHubPolicyKey |Der Schlüssel für die writer-Richtlinie. |
   | EventHubNamespace |Der Namespace, der den Event Hub enthält. |
   | EventHubName |Ihr Event Hub-Name. |
   | EventHubPartitionCount |Die Anzahl der Partitionen in Ihrem Event Hub. |

4. Speichern und schließen Sie die Datei **App.config** .

## <a name="configure-the-eventhubreader"></a>Konfigurieren von EventHubReader

1. Öffnen Sie das Projekt **EventHubReader** .

2. Öffnen Sie die Datei **App.config** für **EventHubReader** . Verwenden Sie die Informationen aus dem Event Hub, den Sie zuvor konfiguriert haben, um den Wert für die folgenden Schlüssel anzugeben:

   | Key | value |
   | --- | --- |
   | EventHubPolicyName |reader (Wenn Sie einen anderen Namen für die Richtlinie mit der Berechtigung *Listen* verwendet haben, verwenden sie ihn stattdessen.) |
   | EventHubPolicyKey |Der Schlüssel für die reader-Richtlinie. |
   | EventHubNamespace |Der Namespace, der den Event Hub enthält. |
   | EventHubName |Ihr Event Hub-Name. |
   | EventHubPartitionCount |Die Anzahl der Partitionen in Ihrem Event Hub. |

3. Speichern und schließen Sie die Datei **App.config** .

## <a name="deploy-the-topologies"></a>Bereitstellen der Topologien

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader** , und wählen Sie **An Storm in HDInsight übermitteln** aus.

    ![Screenshot des Projektmappen-Explorers, wobei „An Storm in HDInsight übermitteln“ hervorgehoben ist](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. Wählen Sie im Dialogfeld **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen** , wählen Sie **Java File Paths** und dann **...** aus, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene JAR-Datei enthält. Klicken Sie abschließend auf **Senden** .

    ![Screenshot des Dialogfelds „Topologie übermitteln“](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Sobald die Topologie übermittelt wurde, wird der **Storm Topologies Viewer** angezeigt. Wählen Sie im linken Bereich die Topologie **EventHubReader** aus, um Informationen über diese Topologie anzuzeigen.

    ![Screenshot des Storm Topologies Viewers](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubWriter** , und wählen Sie **An Storm in HDInsight übermitteln** aus.

5. Wählen Sie im Dialogfeld **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen** , wählen Sie **Java File Paths** und dann **...** aus, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene JAR-Datei enthält. Klicken Sie abschließend auf **Senden** .

6. Sobald die Topologie übermittelt wurde, aktualisieren Sie die Topologieliste im **Storm Topologies Viewer** , um sicherzustellen, dass beide Topologien auf dem Cluster ausgeführt werden.

7. Wählen Sie im **Storm Topologies Viewer** die Topologie **EventHubReader** aus.

8. Doppelklicken Sie auf die **LogBolt** -Komponente im Diagramm, um die Komponentenzusammenfassung für den Bolt zu öffnen.

9. Wählen Sie im Abschnitt **Ausführer** einen der Links in der Spalte **Port** aus. Dadurch werden die von der Komponente protokollierten Informationen angezeigt. Die protokollierten Informationen ähneln dem folgenden Text:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Beenden der Topologien

Um die Topologien zu beenden, wählen Sie jede Topologie im **Storm Topologies Viewer** aus, und klicken Sie dann auf **Beenden** .

![Screenshot des Storm Topologies Viewers mit hervorgehobener Schaltfläche „Kill“](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie den Java-Spout und den Java-Bolt für Event Hubs aus einer C#-Topologie verwenden, um mit Daten in Azure Event Hubs zu arbeiten. Weitere Informationen zum Erstellen von C#-Topologien finden Sie in folgenden Themen:

* [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP-Programmierleitfaden](apache-storm-scp-programming-guide.md)
* [Beispieltopologien für Apache Storm in HDInsight](apache-storm-example-topology.md)