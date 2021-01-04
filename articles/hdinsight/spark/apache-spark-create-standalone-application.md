---
title: 'Tutorial: Scala Maven-App für Spark und IntelliJ: Azure HDInsight'
description: 'Tutorial: Erstellen einer in Scala geschriebenen Spark-Anwendung mit Apache Maven als Buildsystem sowie eines von IntelliJ IDEA bereitgestellten vorhandenen Maven-Archetyps für Scala'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: 5be4ce97e6087847f084f9b9ec2a6b8b0df84e14
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033509"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Tutorial: Erstellen einer Scala Maven-Anwendung für Apache Spark in HDInsight mithilfe von IntelliJ

In diesem Tutorial erfahren Sie, wie Sie eine in Scala geschriebene Apache Spark-Anwendung erstellen, die Apache Maven mit IntelliJ IDEA nutzt. In diesem Artikel wird Apache Maven als Buildsystem verwendet. Darüber hinaus wird ein von IntelliJ IDEA bereitgestellter vorhandener Maven-Archetyp für Scala genutzt.  Das Erstellen einer Scala-Anwendung in IntelliJ IDEA umfasst die folgenden Schritte:

* Verwenden von Maven als Buildsystem
* Aktualisieren der Projektobjektmodell-Datei (POM-Datei) zum Auflösen von Spark-Modulabhängigkeiten
* Schreiben der Anwendung in Scala
* Generieren einer JAR-Datei, die an HDInsight Spark-Cluster übermittelt werden kann
* Ausführen der Anwendung in einem Spark-Cluster mithilfe von Livy

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Installieren des Scala-Plug-Ins für IntelliJ IDEA
> * Verwenden von IntelliJ zum Entwickeln einer Scala Maven-Anwendung
> * Erstellen eines eigenständigen Scala-Projekts

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit.](https://www.azul.com/downloads/azure-only/zulu/)  In diesem Tutorial wird die Java-Version 8.0.202 verwendet.

* Eine Java-IDE. In diesem Artikel wird [IntelliJ IDEA Community  2018.3.4](https://www.jetbrains.com/idea/download/) verwendet.

* Azure-Toolkit für IntelliJ.  Weitere Informationen finden Sie unter [Installieren des Azure-Toolkits für IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installieren des Scala-Plug-Ins für IntelliJ IDEA

Führen Sie die folgenden Schritte aus, um das Scala-Plug-In zu installieren:

1. Öffnen Sie IntelliJ IDEA.

2. Navigieren Sie auf der Willkommensseite zu **Konfigurieren** > **Plug-Ins**, um das Fenster **Plug-Ins** zu öffnen.

    ![IntelliJ IDEA: Aktivieren des Scala-Plug-Ins](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Wählen Sie für das in dem neuen Fenster empfohlene Scala-Plug-In die Option **Installieren** aus.  

    ![IntelliJ IDEA: Installieren des Scala-Plug-Ins](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Nach erfolgreicher Plug-In-Installation müssen Sie die IDE neu starten.

## <a name="use-intellij-to-create-application"></a>Erstellen der Anwendung mit IntelliJ

1. Starten Sie IntelliJ IDEA, und wählen Sie **Create New Project** (Neues Projekt erstellen) aus, um das Fenster **New Project** (Neues Projekt) zu öffnen.

2. Wählen Sie im linken Bereich **Apache Spark/HDInsight** aus.

3. Wählen Sie im Hauptfenster **Spark Project (Scala)** (Spark-Projekt (Scala)) aus.

4. Wählen Sie in der Dropdownliste **Build tool** (Buildtool) einen der folgenden Werte aus:
      * **Maven** für die Unterstützung des Scala-Projekterstellungs-Assistenten
      * **SBT** zum Verwalten von Abhängigkeiten und Erstellen für das Scala-Projekt

   ![IntelliJ: Dialogfeld „Neues Projekt“](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. Wählen Sie **Weiter** aus.

6. Geben Sie im Fenster **New Project** (Neues Projekt) die folgenden Informationen an:  

  	|  Eigenschaft   | BESCHREIBUNG   |  
  	| ----- | ----- |  
  	|Projektname| Geben Sie einen Namen ein.|  
  	|Project&nbsp;location (Projektspeicherort)| Geben Sie den Speicherort für Ihr Projekt ein.|
  	|Project SDK (Projekt-SDK)| Dieses Feld ist bei der erstmaligen Verwendung von IDEA leer.  Wählen Sie **New...** (Neu...) aus, und navigieren Sie zu Ihrem JDK.|
  	|Spark-Version|Der Erstellungs-Assistent integriert die passende Version für das Spark-SDK und das Scala-SDK. Wenn Sie eine ältere Spark-Clusterversion als 2.0 verwenden, wählen Sie **Spark 1.x** aus. Wählen Sie andernfalls **Spark 2.x** aus. In diesem Beispiel wird **Spark 2.3.0 (Scala 2.11.8)** verwendet.|

    ![IntelliJ IDEA: Auswählen des Spark SDK](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Wählen Sie **Fertig stellen** aus.

## <a name="create-a-standalone-scala-project"></a>Erstellen eines eigenständigen Scala-Projekts

1. Starten Sie IntelliJ IDEA, und wählen Sie **Create New Project** (Neues Projekt erstellen) aus, um das Fenster **New Project** (Neues Projekt) zu öffnen.

2. Wählen Sie im linken Bereich die Option **Maven** aus.

3. Geben Sie ein **Projekt-SDK** an. Klicken Sie auf **New...** (Neu...), und navigieren Sie zum Installationsverzeichnis von Java.

4. Aktivieren Sie das Kontrollkästchen **Create from archetype** (Archetypbasierte Erstellung).  

5. Wählen Sie in der Liste der Archetypen **`org.scala-tools.archetypes:scala-archetype-simple`** aus. Dieser Archetyp erstellt die passende Verzeichnisstruktur und lädt die erforderlichen Standardabhängigkeiten zum Schreiben des Scala-Programms herunter.

    ![Screenshot, der den ausgewählten Archetyp im Fenster „Neues Projekt“ zeigt.](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. Wählen Sie **Weiter** aus.

7. Klappen Sie **Artifact Coordinates** (Artefaktkoordinaten) auf. Geben Sie passende Werte für **GroupId** und **ArtifactId** an. **Name** und **Ort** werden automatisch ausgefüllt. In diesem Tutorial werden die folgenden Werte verwendet:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

    ![Screenshot, der die Option „Artefaktkoordinaten“ im Fenster „Neues Projekt“ zeigt.](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. Wählen Sie **Weiter** aus.

9. Überprüfen Sie die Einstellungen, und wählen Sie **Weiter** aus.

10. Überprüfen Sie den Projektnamen und einen Speicherort, und wählen Sie anschließend **Fertig stellen** aus.  Das Importieren des Projekts dauert ein paar Minuten.

11. Navigieren Sie nach Abschluss des Projektimports im linken Bereich zu **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **microsoft** > **spark** > **example**.  Klicken Sie mit der rechten Maustaste auf **MySpec**, und wählen Sie **Delete...** (Löschen...) aus. Sie brauchen diese Datei für die Anwendung nicht.  Wählen Sie im Dialogfeld **OK** aus.
  
12. In späteren Schritten wird die Datei **pom.xml** aktualisiert, um die Abhängigkeiten für die Spark Scala-Anwendung zu definieren. Damit diese Abhängigkeiten automatisch heruntergeladen und aufgelöst werden, muss Maven konfiguriert sein.

13. Wählen Sie im Menü **File** (Datei) die Option **Settings** (Einstellungen) aus, um das Fenster **Settings** (Einstellungen) zu öffnen.

14. Navigieren Sie im Fenster **Settings** (Einstellungen) zu **Build, Execution, Deployment** > **Build Tools** > **Maven** > **Importing** („Erstellung, Ausführung, Bereitstellung“ > „Buildtools“ > „Maven“ > „Importieren“).

15. Aktivieren Sie das Kontrollkästchen **Import Maven projects automatically** (Maven-Projekte automatisch importieren).

16. Klicken Sie auf **Apply** (Anwenden) und dann auf **OK**.  Daraufhin wird wieder das Projektfenster angezeigt.

    ![Maven für automatische Downloads konfigurieren](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. Navigieren Sie im linken Bereich zu **src** > **main** > **scala** > **com.microsoft.spark.example**, und doppelklicken Sie dann auf **App**, um „App.scala“ zu öffnen.

18. Ersetzen Sie den vorhandenen Beispielcode durch den folgenden Code, und speichern Sie die Änderungen. Dieser Code liest die Daten aus der Datei „HVAC.csv“ (verfügbar in allen HDInsight Spark-Clustern), ruft die Zeilen ab, die nur eine Ziffer in der sechsten Spalte enthalten, und schreibt die Ausgabe in **/HVACOut** unter dem Standardspeichercontainer für den Cluster.

    ```scala
    package com.microsoft.spark.example

    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    /**
      * Test IO to wasb
      */
    object WasbIOTest {
        def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACout")
        }
    }
    ```

19. Doppelklicken Sie im linken Bereich auf **pom.xml**.  

20. Fügen Sie in `<project>\<properties>` folgende Segmente hinzu:

    ```xml
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11.8</scala.compat.version>
    <scala.binary.version>2.11</scala.binary.version>
    ```

21. Fügen Sie in `<project>\<dependencies>` folgende Segmente hinzu:

    ```xml
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_${scala.binary.version}</artifactId>
        <version>2.3.0</version>
    </dependency>
    ```

    Speichern Sie die Änderungen in „pom.xml“.

22. Erstellen Sie die JAR-Datei. IntelliJ IDEA ermöglicht die Erstellung von JAR als Projektartefakt. Führen Sie die folgenden Schritte aus:

    1. Wählen Sie im Menü **File** (Datei) die Option **Project Structure...** (Projektstruktur...) aus.

    2. Navigieren Sie im Fenster **Project Structure** (Projektstruktur) zu **Artifacts** >  **+**  > **JAR** > **From modules with dependencies...** („Artefakte“ > Plussymbol > „JAR“ > „Aus Modulen mit Abhängigkeiten...“).

        ![IntelliJ IDEA-Projektstruktur: Hinzufügen einer JAR-Datei](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. Wählen Sie im Fenster **Create JAR from Modules** (JAR aus Modulen erstellen) das Ordnersymbol im Textfeld **Main Class** (Hauptklasse) aus.

    4. Wählen Sie im Fenster **Select Main Class** (Hauptklasse auswählen) die standardmäßig angezeigte Klasse und anschließend **OK** aus.

        ![IntelliJ IDEA-Projektstruktur: Auswählen einer Klasse](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. Vergewissern Sie sich im Fenster **Create JAR from Modules** (JAR aus Modulen erstellen), dass die Option **extract to the target JAR** (Extrahieren in die JAR-Zieldatei) aktiviert ist, und wählen Sie anschließend **OK** aus.  Mit dieser Einstellung wird eine einzelne JAR-Datei mit allen Abhängigkeiten erstellt.

        ![IntelliJ IDEA-Projektstruktur: JAR aus Modul](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. Die Registerkarte **Output Layout** führt alle JAR-Dateien des Maven-Projekts auf. Sie können die Dateien auswählen und löschen, zu denen die Scala-Anwendung keine direkte Abhängigkeit hat. Bei der hier erstellten Anwendung können Sie alle bis auf die letzte (**SparkSimpleApp-Kompilierungsausgabe**) entfernen. Wählen Sie die zu löschenden JAR-Dateien und anschließend das Minussymbol **-** aus.

        ![IntelliJ IDEA-Projektstruktur: Löschen der Ausgabe](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Vergewissern Sie sich, dass das Kontrollkästchen **Include in project build** (In Projektbuild einbeziehen) aktiviert ist. Mit dieser Option wird sichergestellt, dass die JAR-Datei bei jeder Projekterstellung oder -aktualisierung erstellt wird. Wählen Sie **Apply** (Übernehmen) und anschließend **OK** aus.

    7. Navigieren Sie zum Erstellen der JAR-Datei zu **Build** > **Build Artifacts** > **Build** („Erstellen“ > „Artefakte erstellen“ > „Erstellen“). Das Projekt wird in ca. 30 Sekunden kompiliert.  Die JAR-Ausgabe wird unter **\out\artifacts** erstellt.

        ![IntelliJ IDEA-Projektartefakt: Ausgabe](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Ausführen der Anwendung im Apache Spark-Cluster

Sie können die folgenden Ansätze nutzen, um die Anwendung im Cluster auszuführen:

* **Kopieren Sie die JAR-Anwendungsdatei in das dem Cluster zugeordnete Azure Storage-Blob**. Hierzu können Sie das Befehlszeilenprogramm **AzCopy** verwenden. Daneben gibt es aber auch noch zahlreiche andere Clients, die Sie zum Hochladen von Daten verwenden können. Weitere Informationen finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](../hdinsight-upload-data.md).

* **Verwenden Sie Apache Livy, um einen Anwendungsauftrag remote an den Spark-Cluster zu übermitteln.** Spark-Cluster in HDInsight enthalten Livy, um REST-Endpunkte für die Remoteübermittlung von Spark-Aufträgen verfügbar zu machen. Weitere Informationen finden Sie unter [Übermitteln von Remoteaufträgen an einen HDInsight Spark-Cluster mithilfe der Apache Spark-REST-API](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr benötigen, gehen Sie wie folgt vor, um den erstellten Cluster zu löschen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Geben Sie oben im **Suchfeld** den Suchbegriff **HDInsight** ein.

1. Wählen Sie unter **Dienste** die Option **HDInsight-Cluster** aus.

1. Klicken Sie in der daraufhin angezeigten Liste mit den HDInsight-Clustern neben dem Cluster, den Sie für dieses Tutorial erstellt haben, auf die Auslassungspunkte ( **...** ).

1. Klicken Sie auf **Löschen**. Wählen Sie **Ja** aus.

![HDInsight im Azure-Portal: Löschen von Clustern](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Löschen eines HDInsight-Clusters")

## <a name="next-step"></a>Nächster Schritt

In diesem Artikel haben Sie gelernt, wie eine Apache Spark Scala-Anwendung erstellt wird. Im nächsten Artikel erfahren Sie, wie Sie diese Anwendung in einem HDInsight Spark-Cluster mit Livy ausführen.

> [!div class="nextstepaction"]
>[Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](./apache-spark-livy-rest-interface.md)