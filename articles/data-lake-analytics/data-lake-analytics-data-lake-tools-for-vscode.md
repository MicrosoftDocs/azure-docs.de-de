---
title: Verwenden von Azure Data Lake Tools für Visual Studio Code
description: Erfahren Sie, wie Sie Azure Data Lake Tools für Visual Studio Code verwenden, um U-SQL-Skripts zu erstellen, zu testen und auszuführen.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509096"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Verwenden von Azure Data Lake Tools für Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Tools für Visual Studio Code (VS Code) verwenden, um U-SQL-Skripts zu erstellen, zu testen und auszuführen. Diese Informationen sind auch im folgenden Video enthalten:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Voraussetzungen

Azure Data Lake Tools für VS Code unterstützt Windows, Linux und MacOS. Das lokale Ausführung von U-SQL und lokales Debuggen funktioniert ausschließlich in Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Für MacOS und Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installieren von Azure Data Lake-Tools

Nachdem Sie die erforderlichen Komponenten installiert haben, können Sie Azure Data Lake Tools für VS Code installieren.

**Installieren von Azure Data Lake Tools**

1. Öffnen Sie Visual Studio Code.
2. Wählen Sie im linken Bereich **Erweiterungen** aus. Geben Sie in das Suchfeld **Azure Data Lake Tools** ein.
3. Wählen Sie neben **Azure Data Lake Tools** die Option **Installieren** aus. 

   ![Auswahl für die Installation von Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Nach wenigen Sekunden wird die Schaltfläche **Installieren** in **Neu laden** geändert.
4. Wählen Sie **Erneut laden**, um die Erweiterung **Azure Data Lake Tools** zu aktivieren.
5. Klicken Sie zur Bestätigung auf **Fenster erneut laden**. **Azure Data Lake Tools** wird im Bereich **Erweiterungen** angezeigt.

 
## <a name="activate-azure-data-lake-tools"></a>Aktivieren der Azure Data Lake Tools
Erstellen Sie eine USQL-Datei, oder öffnen Sie eine vorhandene USQL-Datei, um die Erweiterung zu aktivieren. 


## <a name="work-with-u-sql"></a>Arbeiten mit U-SQL

Sie müssen eine U-SQL-Datei oder einen entsprechenden Ordner öffnen, um mit U-SQL zu arbeiten.

**Öffnen des Beispielskripts**

Öffnen Sie die Befehlspalette (STRG+UMSCHALT+P), und geben Sie **ADL: Open Sample Script** ein. Dadurch wird eine weitere Instanz dieses Beispiels geöffnet. In dieser Instanz können Sie ein Skript auch bearbeiten, konfigurieren und übermitteln.

**So öffnen Sie einen Ordner für das U-SQL-Projekt**

1. Wählen Sie in Visual Studio Code im Menü **Datei** die Option **Ordner öffnen** aus.
2. Geben Sie einen Ordner an, und wählen Sie dann **Ordner auswählen** aus.
3. Wählen Sie Menü **Datei** die Option **Neu** aus. Eine Datei mit dem Namen „Unbenannt-1“ wird dem Projekt hinzugefügt.
4. Geben Sie den folgenden Code in die Datei „Unbenannt-1“ ein:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Das Skript erstellt eine Datei mit dem Namen „departments.csv“ mit einigen Daten im Ordner „/Output“.

5. Speichern Sie die Datei als **myUSQL.usql** im geöffneten Ordner.

**So kompilieren Sie ein U-SQL-Skript**

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen. 
2. Geben Sie **ADL: Compile Script** ein. Die Kompilierungsergebnisse werden im Fenster **Ausgabe** angezeigt. Sie können auch mit der rechten Maustaste auf eine Skriptdatei klicken und dann **ADL: Compile Script** auswählen, um einen U-SQL-Auftrag zu kompilieren. Das Kompilierungsergebnis wird im Bereich **Ausgabe** angezeigt.
 
**So übermitteln Sie ein U-SQL-Skript**

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen. 
2. Geben Sie **ADL: Submit Job** ein. Sie können auch mit der rechten Maustaste auf eine Skriptdatei klicken und dann **ADL: Submit Job** ein. 

Nach dem Übermitteln eines U-SQL-Auftrags werden die Übermittlungsprotokolle in Visual Studio Code im Fenster **Ausgabe** angezeigt. Die Auftragsansicht wird im rechten Bereich angezeigt. Wenn die Übermittlung erfolgreich war, wird auch die Auftrags-URL angezeigt. Sie können die Auftrags-URL in einem Webbrowser öffnen, um den Auftragsstatus in Echtzeit nachzuverfolgen. 

Die Registerkarte **ZUSAMMENFASSUNG** der Auftragsansicht enthält die Auftragsdetails. Die Hauptfunktionen sind das erneute Übermitteln und Duplizieren des Skripts und das Öffnen im Portal. Auf der Registerkarte **DATEN** der Auftragsansicht finden Sie die Eingabedateien, Ausgabedateien und die Ressource. Dateien können auf den lokalen Computer heruntergeladen werden.

![Registerkarte „Zusammenfassung“ in der Auftragsansicht](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Registerkarte „Daten“ in der Auftragsansicht](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**So legen Sie den Standardkontext fest**

Sie können Standardkontext festlegen, um diese Einstellung auf alle Skriptdateien anzuwenden, wenn Sie die Parameter nicht für die Einzeldateien festgelegt haben.

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen. 
2. Geben Sie **ADL: Set Default Context** ein. Oder klicken Sie mit der rechten Maustaste auf den Skript-Editor, und geben Sie **ADL: Set Default Context** ein.
3. Wählen Sie das Konto, die Datenbank und das Schema, die bzw. das Sie möchten. Die Einstellung wird in der Konfigurationsdatei „xxx_settings.json“ gespeichert.

   ![Konto, Datenbank und Schema als Standardkontext festgelegt](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**So legen Sie Skriptparameter fest**

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen. 
2. Geben Sie **ADL: Set Script Parameters** ein.
3. Die Datei „xxx_settings.json“ wird mit den folgenden Eigenschaften geöffnet:

   - **account**: Ein Azure Data Lake Analytics-Konto in Ihrem Azure-Abonnement, das zum Kompilieren und Ausführen von U-SQL-Aufträgen benötigt wird. Sie müssen das Computerkonto konfigurieren, bevor Sie U-SQL-Aufträge kompilieren und ausführen können.
   - **database**: Eine Datenbank unter Ihrem Konto. Der Standardwert ist **master**.
   - **schema**: Ein Schema unter Ihrer Datenbank. Der Standardwert ist **dbo**.
   - **optionalSettings**:
        - **priority**: Der Prioritätsbereich liegt zwischen 1 und 1000, wobei 1 die höchste Priorität ist. Der Standardwert ist **1000**.
        - **degreeOfParallelism**: Der Parallelitätsbereich liegt zwischen 1 und 150. Der Standardwert ist die maximal zulässige Parallelität in Ihrem Azure Data Lake Analytics-Konto. 

   ![Inhalt der JSON-Datei](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Nach dem Speichern der Konfiguration werden die Informationen zu Konto, Datenbank und Schema auf der Statusleiste links unten in der entsprechenden USQL-Datei angezeigt, wenn Sie keinen Standardkontext eingerichtet haben.

**Set Git ignore**

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen. 
2. Geben Sie **ADL: Set Git Ignore** ein.

   - Sollte Ihr VS Code-Arbeitsordner keine **.gitignore**-Datei enthalten, wird in Ihrem Ordner eine Datei mit der Bezeichnung **.gitignore** erstellt. Der Datei werden standardmäßig vier Elemente hinzugefügt: **usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**. Bei Bedarf können Sie weitere Aktualisierungen vornehmen.
   - Falls sich in Ihrem VS Code-Arbeitsordner bereits eine **.gitignore**-Datei befindet, fügt das Tool Ihrer **.gitignore**-Datei vier Elemente (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) hinzu, sofern diese noch nicht in der Datei enthalten sind.

   ![Elemente in der GITIGNORE-Datei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arbeiten Sie mit CodeBehind-Dateien: C Sharp, Python und R

Azure Data Lake Tools unterstützt mehrere benutzerdefinierte Codes. Anweisungen finden Sie in [Entwickeln von U-SQL mit Python, R und C# für Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Arbeiten mit Assemblys

Weitere Informationen zum Entwickeln von Assemblys finden Sie unter [Entwickeln von U-SQL-Assemblys für Azure Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-develop-assemblies.md).

Sie können mit Data Lake Tools benutzerdefinierte Codeassemblys im Data Lake Analytics-Katalog registrieren.

**So registrieren Sie eine Assembly**

Sie können die Assembly mit dem Befehl **ADL: Register Assembly** oder **ADL: Register Assembly (Advanced)** registrieren.

**So registrieren Sie über den Befehl „ADL: Register Assembly“**
1.  Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2.  Geben Sie **ADL: Register Assembly** ein. 
3.  Geben Sie den lokalen Assemblypfad an. 
4.  Wählen Sie ein Data Lake Analytics-Konto aus.
5.  Wählen Sie eine Datenbank aus.

Das Portal wird in einem Browser geöffnet, und der Assemblyregistrierungsprozess wird angezeigt.  

Bequemer können Sie den Befehl **ADL: Register Assembly** auslösen, indem Sie im Datei-Explorer mit der rechten Maustaste auf die DLL-Datei klicken. 

**So registrieren Sie über den Befehl „ADL: Register Assembly (Advanced)“**
1.  Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2.  Geben Sie **ADL: Register Assembly (Advanced)“** ein. 
3.  Geben Sie den lokalen Assemblypfad an. 
4.  Die JSON-Datei wird angezeigt. Überprüfen und bearbeiten Sie bei Bedarf die Assemblyabhängigkeiten und Ressourcenparameter. Die entsprechenden Anweisungen werden im Fenster **Ausgabe** angezeigt. Speichern Sie die JSON-Datei (STRG+S), um mit der Assemblyregistrierung fortzufahren.

    ![JSON-Datei mit Assemblyabhängigkeiten und Ressourcenparameter](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools erkennt automatisch, ob die DLL Assemblyabhängigkeiten aufweist. Die erkannten Abhängigkeiten werden nach ihrer Erkennung in der JSON-Datei angezeigt. 
>- Sie können Ihre DLL-Ressourcen (etwa TXT-, PNG- oder CSV-Dateien) als Teil der Assemblyregistrierung hochladen. 

Alternativ können Sie den Befehl **ADL: Register Assembly (Advanced)** auch auslösen, indem Sie im Datei-Explorer mit der rechten Maustaste auf die DLL-Datei klicken. 

Der folgende U-SQL-Code veranschaulicht, wie eine Assembly aufgerufen wird. In diesem Beispiel lautet der Name der Assembly *test*.


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Lokale Ausführung des U-SQL-Skripts und lokales Debuggen für Windows-Benutzer
Bei einer lokalen Ausführung des U-SQL-Skripts werden Ihre lokalen Daten und das Skript lokal überprüft, bevor Ihr Code in Data Lake Analytics veröffentlicht wird. Mit der lokalen Debugfunktion können Sie die folgenden Aufgaben ausführen, bevor Ihr Code an Data Lake Analytics übermittelt wird: 
- Debuggen Ihres C#-CodeBehind 
- Ausführen des Codes in einzelnen Schritten 
- Lokales Überprüfen des Skripts

Das Feature zur lokalen Ausführung und zum lokalen Debuggen funktioniert nur in Windows-Umgebungen und wird auf MacOS- und Linux-basierten Betriebssystemen nicht unterstützt.

Informationen zum lokalen Ausführen und lokalen Debuggen finden Sie unter [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure

Damit Sie U-SQL-Skripts in Data Lake Analytics kompilieren und ausführen können, müssen Sie eine Verbindung mit Ihrem Azure-Konto herstellen.

<b id="sign-in-by-command">So stellen Sie eine Verbindung mit Azure per Befehl her</b>

1.  Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen. 
2.  Geben Sie **ADL: Login** (HDInsight: Anmeldung). Die Anmeldeinformationen werden unten rechts angezeigt.

    ![Eingeben des Anmeldebefehls](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Benachrichtigung über die Anmeldung und Authentifizierung](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Klicken Sie auf **Kopieren und öffnen**, um die [Anmeldewebseite](https://aka.ms/devicelogin) zu öffnen. Fügen Sie den Code in das Textfeld ein, und wählen Sie anschließend **Weiter**.

    ![Anmeldewebseite](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Befolgen Sie die Anweisungen, um sich über die Webseite anzumelden. Wenn eine Verbindung hergestellt wurde, wird der Name Ihres Azure-Kontos auf der Statusleiste in der linken unteren Ecke des VS Code-Fensters angezeigt. 

> [!NOTE] 
>- Data Lake Tools meldet Sie beim nächsten Mal automatisch an, wenn Sie sich nicht abmelden.
>- Wenn für Ihr Konto zwei Faktoren aktiviert wurden, empfehlen wir anstelle einer PIN die Verwendung der Telefonauthentifizierung.


Um sich abzumelden, geben Sie den Befehl **ADL: Logout** (HDInsight: Abmelden) ein.

**So stellen Sie eine Verbindung mit Azure per Explorer her**

Erweitern Sie **AZURE DATALAKE**, wählen Sie **Bei Azure anmelden** aus, und führen Sie Schritt 3 und 4 von [So stellen Sie eine Verbindung mit Azure per Befehl her](#sign-in-by-command) aus.

![Auswahl „Bei Azure anmelden“ im Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Sie können sich nicht beim Explorer abmelden. Informationen zur Abmeldung finden Sie unter [So stellen Sie eine Verbindung mit Azure per Befehl her](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Erstellen eines Extraktionsskripts 
Sie können ein Extraktionsskript für CSV-, TSV- und TXT-Dateien erstellen, indem Sie den Befehl **ADL: Create EXTRACT Script** oder den Azure Data Lake-Explorer verwenden.

**So erstellen Sie ein Extraktionsskript per Befehl**

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen, und geben Sie **ADL: Create EXTRACT Script** ein.
2. Geben Sie den vollständigen Pfad für eine Azure-Speicherdatei an, und drücken Sie die Eingabetaste.
3. Wählen Sie ein Konto aus.
4. Wählen Sie für eine TXT-Datei ein Trennzeichen zum Extrahieren der Datei aus. 

![Prozess zum Erstellen eines Extraktionsskripts](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Das Extraktionsskript wird basierend auf Ihren Einträgen generiert. Wählen Sie für ein Skript, mit dem die Spalten nicht erkannt werden können, eine der beiden Optionen. Wenn nicht, wird nur ein Skript generiert.

![Ergebnis der Erstellung eines Extraktionsskripts](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**So erstellen Sie ein Extraktionsskript per Explorer**

Eine andere Möglichkeit zur Erstellung eines Extraktionsskripts ist die Verwendung des Kontextmenüs für die CSV-, TSV- oder TXT-Datei im Azure Data Lake Store oder Azure-Blobspeicher.

![Befehl „Create EXTRACT Script“ aus dem Kontextmenü](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrieren mit Azure Data Lake Analytics per Befehl

Sie können auf Azure Data Lake Analytics-Ressourcen zugreifen, um Konten aufzulisten, auf Metadaten zuzugreifen und Analyseaufträgen anzuzeigen. 

**So listen Sie die Azure Data Lake Analytics-Konten in Ihrem Azure-Abonnement auf**

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: List Accounts** ein. Die Konten werden im Bereich **Ausgabe** angezeigt.

**So greifen Sie auf Azure Data Lake Analytics-Metadaten zu**

1.  Drücken Sie STRG+UMSCHALT+P, und geben Sie dann **ADL: List Tables** ein.
2.  Wählen Sie eines der Data Lake Analytics-Konten aus.
3.  Wählen Sie eine der Data Lake Analytics-Datenbanken aus.
4.  Wählen Sie eines der Schemas aus. Sie können die Liste der Tabellen anzeigen.

**So zeigen Sie Azure Data Lake Analytics-Aufträge an**
1.  Öffnen Sie die Befehlspalette (STRG+UMSCHALT+P), und wählen Sie **ADL: Show Jobs** aus. 
2.  Wählen Sie ein Data Lake Analytics- oder ein lokales Konto aus. 
3.  Warten Sie, bis die Auftragsliste für das Konto angezeigt wird.
4.  Wählen Sie einen Auftrag aus der Liste aus. Data Lake Tools öffnet die Auftragsansicht im rechten Bereich und zeigt einige Informationen in VS Code an.

    ![Auftragsliste](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrieren mit Azure Data Lake Store per Befehl

Sie können auf Azure Data Lake Store bezogene Befehle für Folgendes verwenden:
 - [Durchsuchen der Azure Data Lake Store-Ressourcen](#list-the-storage-path) 
 - [Anzeigen einer Vorschau der Azure Data Lake Store-Datei](#preview-the-storage-file) 
 - Hochladen der Datei direkt in Azure Data Lake Store in VS Code
 - Herunterladen der Datei direkt aus Azure Data Lake Store in VS Code

### <a name="list-the-storage-path"></a>Auflisten des Speicherpfads 

**So listen Sie den Speicherpfad über die Befehlspalette auf**

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie **ADL: List Path** aus.
2. Wählen Sie den Ordner in der Liste aus, oder wählen Sie **Pfad eingeben** oder **Vom Stamm durchsuchen** aus. (Als Beispiel wird **Pfad eingeben** verwendet.) 
3. Wählen Sie Ihr Data Lake Analytics-Konto aus.
4. Navigieren Sie zum Speicherordnerpfad, oder geben Sie ihn ein (z.B. /output/).  

In der Befehlspalette werden die auf Ihren Eingaben basierenden Pfadinformationen aufgelistet.

![Speicherpfadergebnisse](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Alternativ können Sie den relativen Pfad auch über das Kontextmenü auflisten.

**So listen Sie den Speicherpfad über das Kontextmenü auf**

Klicken Sie mit der rechten Maustaste auf die Pfadzeichenfolge, und wählen Sie **Pfad auflisten**.

![„Pfad auflisten“ im Kontextmenü](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Anzeigen einer Vorschau der Speicherdatei

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie **ADL: Preview File** aus.
2. Wählen Sie Ihr Data Lake Analytics-Konto aus. 
3. Geben Sie einen Azure Storage-Dateipfad ein (z.B. /output/SearchLog.txt). 

Die Datei wird in VS Code geöffnet.

![Schritte und das Ergebnis für die Vorschau der Speicherdatei](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Eine andere Möglichkeit für die Anzeige einer Vorschau der Datei ist, im Skript-Editor im Kontextmenü auf den vollständigen oder relativen Pfad der Datei zu klicken. 

### <a name="upload-a-file-or-folder"></a>Hochladen von Dateien oder Ordnern

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie **Datei hochladen** oder **Ordner hochladen** aus.
2. Wenn Sie eine oder mehrere Datei ausgewählt haben, wählen Sie **Datei hochladen**, oder wählen Sie den gesamten Ordner, wenn Sie **Ordner hochladen** ausgewählt haben. Wählen Sie dann die Option **Hochladen**. 
3. Wählen Sie den Speicherordner in der Liste aus, oder wählen Sie **Pfad eingeben** oder **Vom Stamm durchsuchen** aus. (Als Beispiel wird **Pfad eingeben** verwendet.) 
4. Wählen Sie Ihr Data Lake Analytics-Konto aus. 
5. Navigieren Sie zum Speicherordnerpfad, oder geben Sie ihn ein (z.B. /output/). 
6. Wählen Sie **Aktuellen Ordner auswählen** aus, um Ihr Uploadziel anzugeben.

![Schritte und das Ergebnis für das Hochladen einer Datei oder eines Ordners](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Alternativ können Sie eine Datei in den Speicher hochladen, indem Sie im Skript-Editor im Kontextmenü auf den vollständigen oder relativen Pfad der Datei klicken.

Sie können den [Uploadstatus überwachen](#check-storage-tasks-status).


### <a name="download-a-file"></a>Herunterladen einer Datei 
Sie können eine Datei mithilfe des Befehls **ADL: Download File** oder **ADL: Download File (Advanced)** herunterladen.

**So laden Sie eine Datei mithilfe des Befehls „ADL: Download File (Advanced)“ herunter**
1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie anschließend **Download File (Advanced)** (Datei herunterladen (Erweitert)) aus.
2. In VS Code wird eine JSON-Datei angezeigt. Sie können Dateipfade eingeben und mehrere Dateien gleichzeitig herunterladen. Die entsprechenden Anweisungen werden im Fenster **Ausgabe** angezeigt. Speichern Sie die JSON-Datei (STRG+S), um mit dem Herunterladen der Datei(en) fortzufahren.

    ![JSON-Datei mit Pfaden für Dateidownload](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

Im Fenster **Ausgabe** wird der Dateidownloadstatus angezeigt.

![Fenster „Ausgabe“ mit Downloadstatus](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Sie können den [Downloadstatus überwachen](#check-storage-tasks-status).

**So laden Sie eine Datei mithilfe des Befehls „ADL: Download File“ herunter**

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, klicken Sie auf **Datei herunterladen**, und wählen sie dann im Dialogfeld **Ordner auswählen** den Zielordner aus.
2. Wählen Sie den Ordner in der Liste aus, oder wählen Sie **Pfad eingeben** oder **Vom Stamm durchsuchen** aus. (Als Beispiel wird **Pfad eingeben** verwendet.) 
3. Wählen Sie Ihr Data Lake Analytics-Konto aus. 
4. Navigieren Sie zum Speicherordnerpfad, oder geben Sie ihn ein (z.B. /output/), und wählen Sie eine Datei zum Herunterladen aus.

![Schritte und das Ergebnis für das Herunterladen einer Datei](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Alternativ können Sie Speicherdateien herunterladen, indem Sie im Skript-Editor im Kontextmenü auf den vollständigen oder relativen Pfad der Datei klicken.

Sie können den [Downloadstatus überwachen](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Überprüfen des Status der Speicheraufgabe
Der Upload- und Downloadstatus wird auf der Statusleiste angezeigt. Wählen Sie die Statusleiste. Anschließend wird der Status auf der Registerkarte **AUSGABE** angezeigt.

![Statusleiste und Ausgabedetails](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrieren mit Azure Data Lake Analytics per Explorer

Nach der Anmeldung werden im linken Bereich unter **AZURE DATALAKE** alle Abonnements unter Ihrem Azure-Konto angezeigt. 

![Data Lake-Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navigation in den Data Lake Analytics-Metadaten

Erweitern Sie Ihr Azure-Abonnement. Unter dem Knoten **U-SQL Databases** können Sie Ihre U-SQL-Datenbank durchsuchen und Ordner anzeigen wie**Schemas**, **Credentials**, **Assemblies**, **Tables** und **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Entitätsverwaltung in Azure Data Lake Analytics-Metadaten

Erweitern Sie **U-SQL-Datenbanken**. Sie können eine Datenbank, ein Schema, eine Tabelle, einen Tabellentyp, einen Index oder Statistiken erstellen, indem Sie mit der rechten Maustaste auf den entsprechenden Knoten klicken und im Kontextmenü **Skript zum Erstellen** auswählen. Bearbeiten Sie das Skript auf der geöffneten Skriptseite entsprechend Ihren Anforderungen. Übermitteln Sie dann den Auftrag, indem Sie mit der rechten Maustaste auf ihn klicken und anschließend **ADL: Submit Job** auswählen. 

Nachdem Sie das Element erstellt haben, klicken sie mit der rechten Maustaste auf den Knoten, und wählen Sie dann **Aktualisieren**, um das Element anzuzeigen. Sie können das Element auch löschen. Klicken Sie hierzu mit der rechten Maustaste, und wählen Sie **Löschen**.

![Befehl „Skript zum Erstellen“ im Kontextmenü im Data Lake-Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skriptseite für das neue Element](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics-Assemblyregistrierung

Sie können eine Assembly in der entsprechenden Datenbank registrieren, indem Sie mit der rechten Maustaste auf den Knoten **Assemblys** klicken und anschließend **Assembly registrieren** auswählen.

![Befehl „Assembly registrieren“ im Kontextmenü für den Knoten „Assemblys“](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrieren mit Azure Data Lake Store per Explorer

Navigieren Sie zu **Data Lake Store**:

- Klicken Sie mit rechten Maustaste auf den Ordnerknoten, und verwenden Sie im Kontextmenü die Befehle **Aktualisieren**, **Löschen**, **Hochladen**, **Ordner hochladen**, **Relativen Pfad kopieren** und **Vollständigen Pfad kopieren**.

   ![Kontextmenübefehle für einen Ordnerknoten im Data Lake-Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Klicken Sie mit der rechten Maustaste auf den Dateiknoten, und verwenden Sie die Befehle **Vorschau**, **Herunterladen**, **Löschen** und **EXTRACT-Skript erstellen** (nur für CSV-, TSV- und TXT-Dateien) sowie im Kontextmenü **Relativen Pfad kopieren** und **Vollständigen Pfad kopieren**.

   ![Kontextmenübefehle für einen Dateiknoten im Data Lake-Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrieren mit Azure Blob-Speicher per Explorer

Navigieren Sie zum Blob-Speicher:

- Klicken Sie mit der rechten Maustaste auf den Blobcontainerknoten, und verwenden Sie im Kontextmenü **Aktualisieren**, **Blobcontainer löschen** und **Blob hochladen**.

   ![Kontextmenübefehle für einen Blobcontainerknoten unter Blob-Speicher](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Klicken Sie mit der rechten Maustaste auf den Ordnerknoten, und verwenden Sie im Kontextmenü **Aktualisieren** und **Blob hochladen**.

   ![Kontextmenübefehle für einen Ordnerknoten unter Blob-Speicher](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Klicken Sie mit der rechten Maustaste auf den Dateiknoten, und verwenden Sie die Befehle **Vorschau/Bearbeiten**, **Herunterladen**, **Löschen** und **EXTRACT-Skript erstellen** (nur für CSV-, TSV- und TXT-Dateien) sowie im Kontextmenü **Relativen Pfad kopieren** und **Vollständigen Pfad kopieren**.

    ![Kontextmenübefehle für einen Dateiknoten unter Blob-Speicher](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Öffnen des Data Lake-Explorer im Portal
1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **Open Web Azure Storage Explorer** ein, oder klicken Sie im Skript-Editor mit der rechten Maustaste auf einen relativen oder vollständigen Pfad, und wählen Sie dann **Open Web Azure Storage Explorer** aus.
3. Wählen Sie ein Data Lake Analytics-Konto aus.

Data Lake Tools öffnet den Azure-Speicherpfad im Azure-Portal. Sie können den Pfad aus dem Web suchen und eine Vorschau der Datei anzeigen.

## <a name="additional-features"></a>Zusätzliche Funktionen

Data Lake Tools für VS Code unterstützt die folgenden Features:

-   **IntelliSense-AutoVervollständigen**: Vorschläge werden in Popupfenstern für Elemente wie Schlüsselwörter, Methoden und Variablen angezeigt. Die unterschiedlichen Objekttypen werden durch entsprechende Symbole dargestellt:

    - Scala-Datentyp
    - Komplexer Datentyp
    - Integrierte UDTs
    - .NET-Sammlung und -Klassen
    - C#-Ausdrücke
    - Integrierte C#-UDFs, -UDOs und -UDAAGs 
    - U-SQL-Funktionen
    - U-SQL-Windowingfunktionen
 
    ![IntelliSense-Objekttypen](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **IntelliSense-AutoVervollständigen für Data Lake Analytics-Metadaten**: Data Lake Tools lädt die Data Lake Analytics-Metadateninformationen lokal herunter. Die IntelliSense-Funktion füllt automatisch die Objekte aus den Data Lake Analytics-Metadaten. Zu diesen Objekten gehören Datenbank, Schema, Tabelle, Ansicht, Tabellenwertfunktion, Prozeduren und C#-Assemblys.
 
    ![IntelliSense-Metadaten](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **IntelliSense-Fehlermarker**: Data Lake Tools unterstreicht Bearbeitungsfehler für U-SQL und C#. 
-   **Syntaxhervorhebungen**: Data Lake Tools verwendet Farben, um Elemente wie Variablen, Schlüsselwörter, Datentypen und Funktionen zu unterscheiden. 

    ![Syntax mit verschiedenen Farben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Wir empfehlen Ihnen das Upgrade auf Azure Data Lake Tools für Visual Studio Code Version 2.3.3000.4 (oder höher). Ältere Versionen stehen nicht mehr als Download zur Verfügung und sind veraltet.  
   
## <a name="next-steps"></a>Nächste Schritte
- [Entwickeln von U-SQL mit Python, R und C# für Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Erste Schritte mit Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Entwickeln von U-SQL-Skripts mit Data Lake Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
