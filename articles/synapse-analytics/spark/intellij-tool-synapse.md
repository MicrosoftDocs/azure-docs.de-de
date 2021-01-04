---
title: 'Tutorial: Azure-Toolkit für IntelliJ (Spark-Anwendung)'
description: 'Tutorial: Verwenden des Azure-Toolkits für IntelliJ, um in Scala geschriebene Spark-Anwendungen zu entwickeln und an einen serverlosen Apache Spark-Pool zu übermitteln'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 75cc23df41741a95e87ad64d168aea4fa4f45733
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451047"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Tutorial: Erstellen einer Apache Spark-Anwendung mit IntelliJ mithilfe eines Synapse-Arbeitsbereichs

In diesem Tutorial wird gezeigt, wie Sie mit dem Plug-In „Azure-Toolkit für IntelliJ“ in [Scala](https://www.scala-lang.org/) geschriebene Apache Spark-Anwendungen entwickeln und anschließend direkt aus der IntelliJ-IDE (Integrated Development Environment, integrierte Entwicklungsumgebung) an einen serverlosen Apache Spark-Pool übermitteln. Sie können das Plug-In auf mehrere Arten verwenden:

- Entwickeln einer Scala Spark-Anwendung und Übermitteln der Anwendung an einen Spark-Pool
- Zugreifen auf die Ressourcen Ihres Spark-Pools
- Entwickeln und lokales Ausführen einer Scala Spark-Anwendung

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> - Verwenden des Plug-Ins „Azure-Toolkit für IntelliJ“
> - Entwickeln von Apache Spark-Anwendungen
> - Übermitteln einer Anwendung an Spark-Pools

## <a name="prerequisites"></a>Voraussetzungen

- [IntelliJ IDEA (Community-Version)](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC)
- Azure-Toolkit-Plug-In 3.27.0-2019.2 (kann über das [IntelliJ-Plug-In-Repository](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) installiert werden)
- [JDK (Version 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- Scala-Plug-In (kann über das [IntelliJ-Plug-In-Repository](/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea) installiert werden)
- Die folgende Voraussetzung gilt nur für Windows-Benutzer:

  Beim Ausführen der lokalen Spark Scala-Anwendung auf einem Windows-Computer wird unter Umständen eine Ausnahme wie unter [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) beschrieben ausgelöst. Diese Ausnahme tritt auf, weil die Datei „WinUtils.exe“ in Windows fehlt.
  Zur Behebung dieses Fehlers müssen Sie [die ausführbare WinUtils-Datei herunterladen](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) und an einem Speicherort wie **C:\WinUtils\bin** speichern. Fügen Sie anschließend die Umgebungsvariable **HADOOP_HOME** hinzu, und legen Sie den Wert der Variable auf **C:\WinUtils** fest.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Erstellen einer Spark Scala-Anwendung für einen Spark-Pool

1. Starten Sie IntelliJ IDEA, und wählen Sie **Create New Project** (Neues Projekt erstellen) aus, um das Fenster **New Project** (Neues Projekt) zu öffnen.
2. Wählen Sie im linken Bereich **Apache Spark/HDInsight** aus.
3. Wählen Sie im Hauptfenster **Spark Project with Samples (Scala)** (Spark-Projekt mit Beispielen (Scala)) aus.
4. Wählen Sie in der Dropdownliste **Build tool** (Buildtool) einen der folgenden Typen aus:

   - **Maven** für die Unterstützung des Scala-Projekterstellungs-Assistenten
   - **SBT** zum Verwalten von Abhängigkeiten und Erstellen für das Scala-Projekt

    ![IntelliJ IDEA: Dialogfeld „Neues Projekt“](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Wählen Sie **Weiter** aus.
6. Geben Sie im Fenster **New Project** (Neues Projekt) die folgenden Informationen an:

    | Eigenschaft | BESCHREIBUNG |
    | ----- | ----- |
    |Projektname| Geben Sie einen Namen ein. In diesem Tutorial wird `myApp` verwendet.|
    |Project&nbsp;location (Projektspeicherort)| Geben Sie den gewünschten Speicherort für Ihr Projekt ein.|
    |Project SDK (Projekt-SDK)| Dieses Feld ist bei der erstmaligen Verwendung von IDEA möglicherweise leer. Wählen Sie **New...** (Neu...) aus, und navigieren Sie zu Ihrem JDK.|
    |Spark-Version|Der Erstellungs-Assistent integriert die passende Version für das Spark-SDK und das Scala-SDK. Synapse unterstützt nur **Spark 2.4.0**.|
    |||

    ![Auswählen des Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Wählen Sie **Fertig stellen** aus. Es kann einige Minuten dauern, bis das Projekt verfügbar ist.
8. Das Spark-Projekt erstellt automatisch ein Artefakt. Führen Sie zum Anzeigen des Artefakts die folgenden Schritte aus:

   a. Navigieren Sie auf der Menüleiste zu **Datei** > **Projektstruktur**.

   b. Wählen Sie im Fenster **Projektstruktur** die Option **Artefakte** aus.

   c. Wählen Sie **Abbrechen** aus, nachdem Sie sich das Artefakt angesehen haben.

    ![Artefaktinformationen im Dialogfeld](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Navigieren Sie unter **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery** zu **LogQuery**. In diesem Tutorial wird **LogQuery** verwendet.

   ![Befehle zum Erstellen einer Scala-Klasse über Project](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Herstellen einer Verbindung mit Ihren Spark-Pools

Melden Sie sich beim Azure-Abonnement an, um eine Verbindung mit Ihren Spark-Pools herzustellen.

### <a name="sign-in-to-your-azure-subscription"></a>Melden Sie sich bei Ihrem Azure-Abonnement an.

1. Navigieren Sie in der Menüleiste zu **Ansicht** > **Toolfenster** > **Azure Explorer**.

   ![IntelliJ IDEA: Anzeige des Azure-Explorers](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Klicken Sie im Azure Explorer mit der rechten Maustaste auf den Knoten **Azure**, und wählen Sie dann **Anmelden** aus.

   ![IntelliJ IDEA-Explorer: Rechtsklick auf Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Wählen Sie im Dialogfeld **Azure Sign In** (Azure-Anmeldung) die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie dann auf **Sign in** (Anmelden).

    ![IntelliJ IDEA: Azure-Anmeldung](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Wählen Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) die Option **Copy&Open** (Kopieren und öffnen) aus.

   ![IntelliJ IDEA: Azure-Geräteanmeldung](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Fügen Sie den Code auf der Benutzeroberfläche des Browsers ein, und wählen Sie dann **Next** (Weiter) aus.

   ![Microsoft-Dialogfeld zur Eingabe des Codes für HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Geben Sie Ihre Azure-Anmeldeinformationen ein, und schließen Sie den Browser.

   ![Microsoft-Dialogfeld zur Eingabe der E-Mail-Adresse für HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Nachdem Sie sich angemeldet haben, werden im Dialogfeld **Abonnements auswählen** alle Azure-Abonnements aufgelistet, die den Anmeldeinformationen zugeordnet sind. Wählen Sie Ihr Abonnement und anschließend **Auswählen** aus.

    ![Dialogfeld zum Auswählen von Abonnements](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Erweitern Sie im **Azure-Explorer** die Option **Apache Spark on Synapse** (Apache Spark in Synapse), um die in Ihren Abonnements enthaltenen Arbeitsbereiche anzuzeigen.

    ![IntelliJ IDEA: Hauptansicht im Azure-Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Zum Anzeigen der Spark-Pools können Sie einen Arbeitsbereich noch weiter erweitern.

    ![Azure-Explorer: Speicherkonten](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Remoteausführung einer Spark Scala-Anwendung für einen Spark-Pool

Nachdem Sie eine Scala-Anwendung erstellt haben, können Sie sie remote ausführen.

1. Öffnen Sie das Fenster für die **Ausführungs-/Debugkonfiguration**, indem Sie das Symbol auswählen.

    ![Befehl „Submit Spark Application to HDInsight“ (Spark-Anwendung an HDInsight senden) 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. Wählen Sie im Dialogfeld für die **Ausführungs-/Debugkonfiguration** die Option **+** und anschließend **Apache Spark on Synapse** (Apache Spark in Synapse) aus.

    ![Befehl „Submit Spark Application to HDInsight“ (Spark-Anwendung an HDInsight senden) 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Geben Sie im Fenster für die **Ausführungs-/Debugkonfiguration** die folgenden Werte an, und wählen Sie anschließend **OK** aus:

    |Eigenschaft |Wert |
    |----|----|
    |Spark-Pools|Wählen Sie die Spark-Pools aus, in denen Sie Ihre Anwendung ausführen möchten.|
    |Select an Artifact to submit (Auswahl eines zu übermittelnden Artefakts)|Behalten Sie die Standardeinstellung bei.|
    |„Main class name“ (Name der Hauptklasse)|Der Standardwert ist die Hauptklasse der ausgewählten Datei. Sie können die Klasse ändern, indem Sie die Schaltfläche mit den Auslassungspunkten ( **…** ) und anschließend eine andere Klasse auswählen.|
    |Job configurations (Auftragskonfigurationen)|Sie können die Standardschlüssel und -werte ändern. Weitere Informationen finden Sie unter [Apache Livy-REST-API](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Befehlszeilenargumente|Sie können bei Bedarf durch Leerzeichen getrennte Argumente für die Hauptklasse eingeben.|
    |Referenced Jars and Referenced Files („Referenzierte JARs“ und „Referenzierte Dateien“)|Sie können bei Bedarf die Pfade für die JAR-Dateien und für die anderen Dateien eingeben, auf die verwiesen wird. Sie können auch Dateien im virtuellen Dateisystem von Azure durchsuchen, das derzeit nur ADLS Gen2-Cluster unterstützt. Weitere Informationen: [Apache Spark-Konfiguration]https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) und [Hochladen von Ressourcen in Cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Job Upload Storage (Speicher für Auftragsupload)|Erweitern Sie die Option, um zusätzliche Optionen anzuzeigen.|
    |Speichertyp|Wählen Sie in der Dropdownliste **Use Azure Blob to upload** (Azure-Blob für Upload verwenden) oder **Use cluster default storage account to upload** (Standardspeicherkonto des Clusters für Upload verwenden).|
    |Speicherkonto|Geben Sie Ihr Speicherkonto ein.|
    |Storage Key (Speicherschlüssel)|Geben Sie Ihren Speicherschlüssel ein.|
    |Speichercontainer|Wählen Sie Ihren Speichercontainer aus der Dropdownliste aus, nachdem Sie **Storage Account** (Speicherkonto) und **Storage Key** (Speicherschlüssel) eingegeben haben.|

    ![Dialogfeld für die Spark-Übermittlung 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Wählen Sie das Symbol **SparkJobRun** aus, um Ihr Projekt an den ausgewählten Spark-Pool zu übermitteln. Auf der Registerkarte **Remote Spark Job in Cluster** (Remote-Spark-Auftrag auf Cluster) wird im unteren Bereich der Fortschritt der Auftragsausführung angezeigt. Sie können die Anwendung anhalten, indem Sie die rote Schaltfläche auswählen.

    ![Fenster für die Apache Spark-Übermittlung](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Dialogfeld für die Spark-Übermittlung 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Lokales Ausführen/Debuggen von Apache Spark-Anwendungen

Im Anschluss erfahren Sie, wie Sie das lokale Ausführen und Debuggen für Ihren Apache Spark-Auftrag einrichten.

### <a name="scenario-1-do-local-run"></a>Szenario 1: Lokales Ausführen

1. Wählen Sie im Dialogfeld für die **Ausführungs-/Debugkonfiguration** das Plussymbol ( **+** ) aus. Wählen Sie anschließend die Option **Apache Spark on Synapse** (Apache Spark in Synapse) aus. Geben Sie zu speichernde Informationen für **Name** und **Main class name** (Name der main-Klasse) ein.

    ![Intellij – Konfigurationen debuggen/ausführen – lokale Ausführung 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Umgebungsvariablen und der Speicherort von „WinUtils.exe“ sind nur für Windows-Benutzer relevant.
    - Umgebungsvariablen: Die Systemumgebungsvariable kann automatisch erkannt werden, wenn Sie sie zuvor festgelegt haben. In diesem Fall muss sie nicht manuell hinzugefügt werden.
    - [WinUtils.exe Location](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) (Speicherort von „WinUtils.exe“): Zum Angeben des WinUtils-Speicherorts können Sie auf der rechten Seite das Ordnersymbol auswählen.

2. Wählen Sie anschließend die Schaltfläche für die lokale Wiedergabe aus.

    ![Intellij – Konfigurationen debuggen/ausführen – lokale Ausführung 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Nach Abschluss der lokalen Ausführung können Sie die Ausgabedatei unter **data** > **__default__** überprüfen, sofern das Skript eine Ausgabe enthält.

    ![Intellij – Ergebnis der lokalen Ausführung des Projekts 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Szenario 2: Lokales Debuggen

1. Öffnen Sie das Skript **LogQuery**, und legen Sie Breakpoints fest.
2. Wählen Sie das Symbol für **Lokales Debuggen** aus, um lokales Debuggen durchzuführen.

    ![Intellij – Ergebnis der lokalen Ausführung des Projekts 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Zugreifen auf und Verwalten von Synapse-Arbeitsbereichen

Im Azure-Explorer im Azure-Toolkit für IntelliJ können verschiedene Vorgänge ausgeführt werden. Navigieren Sie in der Menüleiste zu **Ansicht** > **Toolfenster** > **Azure Explorer**.

### <a name="launch-workspace"></a>Starten des Arbeitsbereichs

1. Navigieren Sie im Azure-Explorer zu **Apache Spark on Synapse** (Apache Spark in Synapse), und erweitern Sie den Knoten.

    ![IntelliJ IDEA: Hauptansicht im Azure-Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Klicken Sie mit der rechten Maustaste auf einen Arbeitsbereich, und wählen Sie **Arbeitsbereich starten** aus. Daraufhin wird eine Website geöffnet.

    ![Spark-Auftrag: Anzeigen von Anwendungsdetails 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark-Auftrag: Anzeigen von Anwendungsdetails 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark-Konsole

Sie können „Spark Local Console(Scala)“ (Lokale Spark-Konsole (Scala)) oder „Spark Livy Interactive Session Console(Scala)“ (Spark-Livy-Konsole für interaktive Sitzungen) ausführen.

### <a name="spark-local-console-scala"></a>Lokale Spark-Konsole (Scala)

Vergewissern Sie sich, dass die Voraussetzung für „WINUTILS.EXE“ erfüllt wurde.

1. Navigieren Sie in der Menüleiste zu **Run** (Ausführen) > **Edit Configurations...** (Konfigurationen bearbeiten…).
2. Navigieren Sie im Fenster für die **Ausführungs-/Debugkonfiguration** im linken Bereich zu **Apache Spark on Synapse** >  **[Spark on Synapse] myApp** (Apache Spark in Synapse > [Spark in Synapse] myApp).
3. Klicken Sie im Hauptfenster auf die Registerkarte **Locally Run** (Lokal ausführen).
4. Geben Sie die folgenden Werte an, und klicken Sie anschließend auf **OK**:

    |Eigenschaft |Wert |
    |----|----|
    |Umgebungsvariablen|Stellen Sie sicher, dass für HADOOP_HOME der richtige Wert festgelegt ist.|
    |„WINUTILS.exe location“ (Speicherort von „WINUTILS.exe“)|Stellen Sie sicher, dass der richtige Pfad festgelegt ist.|

    ![Konfigurieren der lokalen Konsole](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Navigieren Sie in „Project“ (Projekt) zu **myApp** > **src** > **main** > **scala** > **myApp**.
6. Navigieren Sie über die Menüleiste zu **Tools** > **Spark Console** > **Run Spark Local Console(Scala)** (Tools > Spark-Konsole > Lokale Spark-Konsole (Scala) ausführen).
7. Zwei Dialogfelder werden möglicherweise angezeigt, in denen Sie gefragt werden, ob Probleme mit Abhängigkeiten automatisch behoben werden sollen. Klicken Sie ggf. auf **Auto Fix** (Automatisch beheben).

    ![IntelliJ IDEA: Spark-Dialogfeld1 „Auto Fix“ (Automatisch korrigieren)](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA: Spark-Dialogfeld2 „Auto Fix“ (Automatisch korrigieren)](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Die Konsole sollte in etwa wie auf dem folgenden Screenshot aussehen. Geben Sie im Konsolenfenster `sc.appName` ein, und drücken Sie STRG+EINGABETASTE. Das Ergebnis wird angezeigt. Sie können die lokale Konsole beenden, indem Sie die rote Schaltfläche auswählen.

    ![IntelliJ IDEA Ergebnis der lokalen Konsole](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark-Konsole mit interaktiver Livy-Sitzung (Scala)

Wird nur für IntelliJ 2018.2 und 2018.3 unterstützt.

1. Navigieren Sie in der Menüleiste zu **Run** (Ausführen) > **Edit Configurations...** (Konfigurationen bearbeiten…).

2. Navigieren Sie im Fenster für die **Ausführungs-/Debugkonfiguration** im linken Bereich zu **Apache Spark on synapse** >  **[Spark on synapse] myApp** (Apache Spark in Synapse > [Spark in Synapse] myApp).

3. Klicken Sie im Hauptfenster auf die Registerkarte **Remotely Run in Cluster** (Remoteausführung in Cluster).

4. Geben Sie die folgenden Werte an, und klicken Sie anschließend auf **OK**:

    |Eigenschaft |Wert |
    |----|----|
    |„Main class name“ (Name der Hauptklasse)| Wählen Sie den Namen der Hauptklasse aus.| 
    |Spark-Pools|Wählen Sie die Spark-Pools aus, in denen Sie Ihre Anwendung ausführen möchten.|
    ||

    ![Konfigurieren der interaktiven Konsole](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Navigieren Sie in „Project“ (Projekt) zu **myApp** > **src** > **main** > **scala** > **myApp**.

6. Navigieren Sie über die Menüleiste zu **Tools** > **Spark console** > **Run Spark Livy Interactive Session Console(Scala)** (Tools > Spark-Konsole > Spark-Konsole mit interaktiver Livy-Sitzung (Scala) ausführen).
7. Die Konsole sollte in etwa wie auf dem folgenden Screenshot aussehen. Geben Sie im Konsolenfenster `sc.appName` ein, und drücken Sie STRG+EINGABETASTE. Das Ergebnis wird angezeigt. Sie können die lokale Konsole beenden, indem Sie die rote Schaltfläche auswählen.

    ![IntelliJ IDEA: Ergebnis der interaktiven Konsole](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Senden der Auswahl an die Spark-Konsole

Sie können sich die Ergebnisse eines Skripts ansehen, indem Sie Code an die lokale Konsole oder an die Livy-Konsole für interaktive Sitzungen (Scala) senden. Markieren Sie dazu Code in der Scala-Datei, und klicken Sie mit der rechten Maustaste auf **Send Selection To Spark Console** (Auswahl an Spark-Konsole senden). Der ausgewählte Code wird an die Konsole gesendet und ausgeführt. Das Ergebnis wird nach dem Code in der Konsole angezeigt. Die Konsole überprüft, ob Fehler vorliegen.

   ![Senden der Auswahl an die Spark-Konsole](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Nächste Schritte

- [Azure Synapse Analytics](../overview-what-is.md)
- [Erstellen eines neuen Apache Spark-Pools für einen Azure Synapse Analytics-Arbeitsbereich](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
