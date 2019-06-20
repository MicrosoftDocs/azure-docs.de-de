---
title: Verwenden des Azure-Toolkits für IntelliJ mit Hortonworks Sandbox
description: Erfahren Sie, wie Sie HDInsight-Tools im Azure-Toolkit für IntelliJ mit Hortonworks Sandbox verwenden.
keywords: Hadoop-Tools,Hive-Abfrage,IntelliJ,Hortonworks Sandbox,Azure-Toolkit für IntelliJ
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 6ae271fc464e2a5735ef95a428b3070066058ddc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696144"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Verwenden von HDInsight-Tools für IntelliJ mit Hortonworks Sandbox

Erfahren Sie, wie Sie die HDInsight-Tools für IntelliJ verwenden, um Apache Scala-Anwendungen zu entwickeln und die Anwendungen in [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) auf Ihrem Computer zu testen. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) ist eine integrierte Java-Entwicklungsumgebung (Integrated Development Environment, IDE) zum Entwickeln von Computersoftware. Nachdem Sie Ihre Anwendungen in Hortonworks Sandbox entwickelt und getestet haben, können Sie sie in [Azure HDInsight](apache-hadoop-introduction.md) verschieben.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

- Ausführung von Hortonworks Data Platform 2.4 (HDP) in Hortonworks Sandbox auf Ihrem lokalen Computer. Informationen zur Einrichtung von HDP finden Sie unter [Erste Schritte im Apache Hadoop-Ökosystem mit einer Hadoop-Sandbox auf einem virtuellen Computer](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HDInsight-Tools für IntelliJ wurde nur mit HDP 2.4 getestet. Um HDP 2.4 zu erhalten, erweitern Sie auf der **Website für den Download von Hortonworks Sandbox** die Option [Hortonworks Sandbox Archive](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) Version 1.8 oder höher](https://aka.ms/azure-jdks). Azure Toolkit für IntelliJ erfordert JDK.

- [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download) mit dem [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala)-Plug-In und [Azure-Toolkit für IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij)-Plug-In. Die HDInsight-Tools für IntelliJ sind als Teil des Azure-Toolkits für IntelliJ verfügbar. 

So installieren Sie die Plug-Ins:

  1. Öffnen Sie IntelliJ IDEA.
  2. Wählen Sie auf der Seite **Welcome** (Willkommen) **Configure** (Konfigurieren) und dann auf **Plugins** (Plug-Ins).
  3. Wählen Sie links unten **Install JetBrains plugin** (JetBrains-Plug-In installieren).
  4. Verwenden Sie die Suchfunktion, um nach **Scala** zu suchen, und klicken Sie dann auf **Install** (Installieren).
  5. Wählen Sie **Restart IntelliJ IDEA** (IntelliJ IDEA neu starten), um die Installation abzuschließen.
  6. Wiederholen Sie die Schritte 4 und 5, um das **Azure-Toolkit für IntelliJ** zu installieren. Weitere Informationen finden Sie unter [Installieren des Azure-Toolkits für IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Erstellen einer Apache Spark Scala-Anwendung

In diesem Abschnitt erstellen Sie mit IntelliJ IDEA ein Scala-Beispielprojekt. Im nächsten Abschnitt verknüpfen Sie IntelliJ IDEA mit Hortonworks Sandbox (Emulator), bevor Sie das Projekt übermitteln.

1. Öffnen Sie IntelliJ IDEA auf Ihrem Computer. Führen Sie im Dialogfeld **Neues Projekt** folgende Schritte aus:

   1. Wählen Sie **HDInsight** > **Spark auf HDInsight (Scala)** aus.
   2. Wählen Sie in der Liste **Build-Tool** basierend auf Ihrem Szenario einen der folgenden Einträge aus:

      * **Maven**: für die Unterstützung des Scala-Projekterstellungs-Assistenten.
      * **SBT**: zum Verwalten von Abhängigkeiten und Erstellen für das Scala-Projekt.

   ![Dialogfeld „Neues Projekt“](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Klicken Sie auf **Weiter**.
3. Führen Sie im nächsten Dialogfeld **New Project** (Neues Projekt) folgende Schritte aus:

   1. Geben Sie in das Feld **Project name** einen Projektnamen ein.
   2. Geben Sie in das Feld **Project location** (Projektspeicherort) einen Projektspeicherort ein.
   3. Klicken Sie neben der Dropdownliste **Project SDK** (Projekt-SDK) auf **New** (Neu). Wählen Sie **JDK** aus, und geben Sie dann den Ordner der Java JDK-Version 1.7 oder höher an. Wählen Sie **Java 1.8** für den Spark 2.x-Cluster. Wählen Sie **Java 1.7** für den Spark 1.x-Cluster. Der Standardspeicherort ist „C:\Programme\Java\jdk1.8.x_xxx“.
   4. In der Dropdownliste **Spark Version** (Spark-Version) fügt der Scala-Projekterstellungs-Assistent die richtige Version für das Spark-SDK und das Scala-SDK ein. Wenn die Spark-Clusterversion niedriger als 2.0 ist, wählen Sie **Spark 1.x** aus. Wählen Sie andernfalls **Spark 2.x** aus. In diesem Beispiel wird Spark 1.6.2 (Scala 2.10.5) verwendet. Stellen Sie sicher, dass Sie das Repository **Scala 2.10.x** verwenden. Verwenden Sie nicht das Repository „Scala 2.11.x“.
    
      ![Erstellen von IntelliJ Scala-Projekteigenschaften](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Wählen Sie **Fertig stellen** aus.
5. Wenn die Ansicht **Project** (Projekt) nicht bereits geöffnet ist, drücken Sie **ALT+1**, um sie zu öffnen.
6. Erweitern Sie im **Project Explorer** (Projekt-Explorer) das Projekt, und wählen Sie anschließend **src** aus.
7. Klicken Sie mit der rechten Maustaste auf **src**, zeigen Sie auf **New** (Neu), und klicken Sie auf **Scala class** (Scala-Klasse).
8. Geben Sie im Feld **Name** einen Namen ein. Wählen Sie im Feld **Kind** (Art) die Option **Object** (Objekt). Wählen Sie anschließend **OK** aus.

    ![Das Dialogfeld „Create New Scala Class“ (Neue Scala-Klasse erstellen)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Fügen Sie den folgenden Code in die SCALA-Datei ein:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Klicken Sie im Menü **Build** auf **Build project** (Projekt erstellen). Stellen Sie sicher, dass die Kompilierung erfolgreich abgeschlossen wird.


## <a name="link-to-the-hortonworks-sandbox"></a>Link zu Hortonworks Sandbox

Sie müssen über eine vorhandene IntelliJ-Anwendung verfügen, bevor Sie einen Link zu Hortonworks Sandbox (Emulator) einrichten können.

So richten Sie einen Link zu einem Emulator ein:

1. Öffnen Sie das Projekt in IntelliJ.
2. Wählen Sie im Menü **View**  (Ansicht) die Option **Tool Windows** (Toolfenster) und dann **Azure Explorer** aus.
3. Erweitern Sie **Azure**, klicken Sie mit der rechten Maustaste auf **HDInsight**, und klicken Sie dann auf **Link an Emulator** (Mit einem Emulator verknüpfen).
4. Geben Sie im Dialogfeld **Link A New Emulator** (Verknüpfung mit neuem Emulator herstellen) das Kennwort ein, das Sie für das Stammkonto der Hortonworks Sandbox festgelegt haben. Geben Sie anschließend ähnliche Werte ein wie die im folgenden Screenshot verwendeten. Wählen Sie anschließend **OK** aus. 

   ![Das Dialogfeld „Link a New Emulator“ (Verknüpfung mit neuem Emulator herstellen)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Klicken Sie auf **Ja**, um den Emulator zu konfigurieren.

Wenn die Verbindung mit dem Emulator erfolgreich hergestellt wurde, sehen Sie, dass der Emulator (Hortonworks Sandbox) unter dem Knoten „HDInsight“ aufgeführt ist.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Übermitteln der Spark Scala-Anwendung an die Hortonworks Sandbox

Nachdem Sie IntelliJ IDEA mit dem Emulator verknüpft haben, können Sie Ihr Projekt übermitteln.

So übermitteln Sie ein Projekt an einen Emulator:

1. Klicken Sie im **Projektexplorer** mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Submit Spark Application to HDInsight** (Spark-Anwendung an HDInsight senden).
2. Führen Sie die folgenden Schritte aus:

    1. Wählen Sie in der Dropdownliste **Spark cluster (Linux only)** (Spark-Cluster [nur Linux]) Ihre lokale Hortonworks Sandbox-Instanz aus.
    2. Wählen Sie im Feld **Main class name** (Name der Hauptklasse) den Namen der Hauptklasse aus, oder geben Sie ihn ein. Für dieses Tutorial lautet der Name **GroupByTest**.

3. Klicken Sie auf **Submit** (Senden). Die Protokolle der Auftragsübermittlungen werden im Fenster des Spark-Übermittlungstools angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Verwenden der HDInsight-Tools im Azure-Toolkit für IntelliJ zum Erstellen von Apache Spark-Anwendungen für HDInsight Spark-Cluster unter Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Ein Video zu HDInsight-Tools für IntelliJ finden Sie unter [Introduce HDInsight Tools for IntelliJ for Apache Spark Development](https://www.youtube.com/watch?v=YTZzYVgut6c) (Einführung in HDInsight-Tools für IntelliJ für die Apache Spark-Entwicklung).

- Lernen Sie das [Remotedebuggen von Apache Spark-Anwendungen in einem HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ per SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md) kennen.

- Erfahren Sie mehr über das [Verwenden der HDInsight-Tools im Azure-Toolkit für IntelliJ zum Remotedebuggen von Apache Spark-Anwendungen im HDInsight Spark-Cluster unter Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Erfahren Sie mehr über das [Verwenden der HDInsight-Tools im Azure-Toolkit für Eclipse zum Erstellen von Apache Spark-Anwendungen](../spark/apache-spark-eclipse-tool-plugin.md).

- Ein Video zu HDInsight-Tools für Eclipse finden Sie unter [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Verwenden von HDInsight-Tools für Eclipse zum Erstellen von Spark-Anwendungen).
