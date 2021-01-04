---
title: Verwenden von Azure Data Lake Tools für Visual Studio Code
description: Erfahren Sie, wie Sie Azure Data Lake Tools für Visual Studio Code verwenden, um U-SQL-Skripts zu erstellen, zu testen und auszuführen.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751358"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Verwenden von Azure Data Lake Tools für Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Tools für Visual Studio Code (VS Code) verwenden, um U-SQL-Skripts zu erstellen, zu testen und auszuführen. Diese Informationen sind auch im folgenden Video enthalten:

[![Videoplayer: Azure Data Lake Tools für Visual Studio](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Voraussetzungen

Azure Data Lake Tools für VS Code unterstützt Windows, Linux und MacOS.  Das lokale Ausführung von U-SQL und lokales Debuggen funktioniert ausschließlich in Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Für MacOS und Linux:

- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installieren von Azure Data Lake-Tools

Nachdem Sie die erforderlichen Komponenten installiert haben, können Sie Azure Data Lake Tools für VS Code installieren.

### <a name="to-install-azure-data-lake-tools"></a>Installieren der Azure Data Lake Tools

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

### <a name="to-open-the-sample-script"></a>Öffnen des Beispielskripts

Öffnen Sie die Befehlspalette (STRG+UMSCHALT+P), und geben Sie **ADL: Open Sample Script** ein. Dadurch wird eine weitere Instanz dieses Beispiels geöffnet. In dieser Instanz können Sie ein Skript auch bearbeiten, konfigurieren und übermitteln.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Öffnen eines Ordners für das U-SQL-Projekt

1. Wählen Sie in Visual Studio Code im Menü **Datei** die Option **Ordner öffnen** aus.
2. Geben Sie einen Ordner an, und wählen Sie dann **Ordner auswählen** aus.
3. Wählen Sie Menü **Datei** die Option **Neu** aus. Eine Datei mit dem Namen „Unbenannt-1“ wird dem Projekt hinzugefügt.
4. Geben Sie den folgenden Code in die Datei „Unbenannt-1“ ein:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   OUTPUT @departments     TO "/Output/departments.csv" USING Outputters.Csv();

    Das Skript erstellt eine Datei mit dem Namen „departments.csv“ mit einigen Daten im Ordner „/Output“.

5. Speichern Sie die Datei als **myUSQL.usql** im geöffneten Ordner.

### <a name="to-compile-a-u-sql-script"></a>Kompilieren eines U-SQL-Skripts

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: Compile Script** ein. Die Kompilierungsergebnisse werden im Fenster **Ausgabe** angezeigt. Sie können auch mit der rechten Maustaste auf eine Skriptdatei klicken und dann **ADL: Compile Script** auswählen, um einen U-SQL-Auftrag zu kompilieren. Das Kompilierungsergebnis wird im Bereich **Ausgabe** angezeigt.

### <a name="to-submit-a-u-sql-script"></a>Übermitteln eines U-SQL-Skripts

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: Submit Job** auswählen. Sie können auch mit der rechten Maustaste auf eine Skriptdatei klicken und dann **ADL: Submit Job** auswählen.

Nach dem Übermitteln eines U-SQL-Auftrags werden die Übermittlungsprotokolle in Visual Studio Code im Fenster **Ausgabe** angezeigt. Die Auftragsansicht wird im rechten Bereich angezeigt. Wenn die Übermittlung erfolgreich war, wird auch die Auftrags-URL angezeigt. Sie können die Auftrags-URL in einem Webbrowser öffnen, um den Auftragsstatus in Echtzeit nachzuverfolgen.

Die Registerkarte **ZUSAMMENFASSUNG** der Auftragsansicht enthält die Auftragsdetails. Die Hauptfunktionen sind das erneute Übermitteln und Duplizieren des Skripts und das Öffnen im Portal. Auf der Registerkarte **DATEN** der Auftragsansicht finden Sie die Eingabedateien, Ausgabedateien und die Ressource. Dateien können auf den lokalen Computer heruntergeladen werden.

![Registerkarte „Zusammenfassung“ in der Auftragsansicht](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Registerkarte „Daten“ in der Auftragsansicht](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Festlegen des Standardkontexts

Sie können Standardkontext festlegen, um diese Einstellung auf alle Skriptdateien anzuwenden, wenn Sie die Parameter nicht für die Einzeldateien festgelegt haben.

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: Set Default Context** ein. Oder klicken Sie mit der rechten Maustaste auf den Skript-Editor, und geben Sie **ADL: Set Default Context** ein.
3. Wählen Sie das Konto, die Datenbank und das Schema, die bzw. das Sie möchten. Die Einstellung wird in der Konfigurationsdatei „xxx_settings.json“ gespeichert.

   ![Konto, Datenbank und Schema als Standardkontext festgelegt](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Festlegen der Skriptparameter

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

### <a name="to-set-git-ignore"></a>Festlegen von „Git ignore“

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: Set Git Ignore** ein.

   - Sollte Ihr VS Code-Arbeitsordner keine **.gitignore**-Datei enthalten, wird in Ihrem Ordner eine Datei mit der Bezeichnung **.gitignore** erstellt. Der Datei werden standardmäßig vier Elemente hinzugefügt: **usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**. Bei Bedarf können Sie weitere Aktualisierungen vornehmen.
   - Falls sich in Ihrem VS Code-Arbeitsordner bereits eine **.gitignore**-Datei befindet, fügt das Tool Ihrer **.gitignore**-Datei vier Elemente (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) hinzu, sofern diese noch nicht in der Datei enthalten sind.

   ![Elemente in der GITIGNORE-Datei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arbeiten Sie mit CodeBehind-Dateien: C Sharp, Python und R

Azure Data Lake Tools unterstützt mehrere benutzerdefinierte Codes. Anweisungen finden Sie in [Entwickeln von U-SQL mit Python, R und C# für Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Arbeiten mit Assemblys

Weitere Informationen zum Entwickeln von Assemblys finden Sie unter [Entwickeln von U-SQL-Assemblys für Azure Data Lake Analytics-Aufträge]().

Sie können mit Data Lake Tools benutzerdefinierte Codeassemblys im Data Lake Analytics-Katalog registrieren.

### <a name="to-register-an-assembly"></a>Registrieren einer Assembly

Sie können die Assembly mit dem Befehl **ADL: Register Assembly** oder **ADL: Register Assembly (Advanced)** registrieren.

### <a name="to-register-through-the-adl-register-assembly-command"></a>Registrieren über den Befehl „ADL: Register Assembly“

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: Register Assembly** ein.
3. Geben Sie den lokalen Assemblypfad an.
4. Wählen Sie ein Data Lake Analytics-Konto aus.
5. Wählen Sie eine Datenbank aus.

Das Portal wird in einem Browser geöffnet, und der Assemblyregistrierungsprozess wird angezeigt.  

Bequemer können Sie den Befehl **ADL: Register Assembly** auslösen, indem Sie im Datei-Explorer mit der rechten Maustaste auf die DLL-Datei klicken.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Registrieren über den Befehl „ADL: Register Assembly (Advanced)“

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: Register Assembly (Advanced)“** ein.
3. Geben Sie den lokalen Assemblypfad an.
4. Die JSON-Datei wird angezeigt. Überprüfen und bearbeiten Sie bei Bedarf die Assemblyabhängigkeiten und Ressourcenparameter. Die entsprechenden Anweisungen werden im Fenster **Ausgabe** angezeigt. Speichern Sie die JSON-Datei (STRG+S), um mit der Assemblyregistrierung fortzufahren.

   ![JSON-Datei mit Assemblyabhängigkeiten und Ressourcenparameter](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake Tools erkennt automatisch, ob die DLL Assemblyabhängigkeiten aufweist. Die erkannten Abhängigkeiten werden nach ihrer Erkennung in der JSON-Datei angezeigt.
>- Sie können Ihre DLL-Ressourcen (etwa TXT-, PNG- oder CSV-Dateien) als Teil der Assemblyregistrierung hochladen.

Alternativ können Sie den Befehl **ADL: Register Assembly (Advanced)** auch auslösen, indem Sie im Datei-Explorer mit der rechten Maustaste auf die DLL-Datei klicken.

Der folgende U-SQL-Code veranschaulicht, wie eine Assembly aufgerufen wird. In diesem Beispiel lautet der Name der Assembly *test*.

```usql
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
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Lokale Ausführung des U-SQL-Skripts und lokales Debuggen für Windows-Benutzer

Bei einer lokalen Ausführung des U-SQL-Skripts werden Ihre lokalen Daten und das Skript lokal überprüft, bevor Ihr Code in Data Lake Analytics veröffentlicht wird. Mit der lokalen Debugfunktion können Sie die folgenden Aufgaben ausführen, bevor Ihr Code an Data Lake Analytics übermittelt wird:

- Debuggen Ihres C#-CodeBehind
- Ausführen des Codes in einzelnen Schritten
- Lokales Überprüfen des Skripts

Das Feature zur lokalen Ausführung und zum lokalen Debuggen funktioniert nur in Windows-Umgebungen und wird auf MacOS- und Linux-basierten Betriebssystemen nicht unterstützt.

Informationen zum lokalen Ausführen und lokalen Debuggen finden Sie unter [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure

Damit Sie U-SQL-Skripts in Data Lake Analytics kompilieren und ausführen können, müssen Sie eine Verbindung mit Ihrem Azure-Konto herstellen.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Herstellen einer Azure-Verbindung per Befehl

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.

2. Geben Sie **ADL: Login** (HDInsight: Anmeldung). Die Anmeldeinformationen werden unten rechts angezeigt.

   ![Eingeben des Anmeldebefehls](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Benachrichtigung über die Anmeldung und Authentifizierung](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Klicken Sie auf **Kopieren und öffnen**, um die [Anmeldewebseite](https://aka.ms/devicelogin) zu öffnen. Fügen Sie den Code in das Textfeld ein, und wählen Sie anschließend **Weiter**.

    ![Anmeldewebseite](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Befolgen Sie die Anweisungen, um sich über die Webseite anzumelden. Wenn eine Verbindung hergestellt wurde, wird der Name Ihres Azure-Kontos auf der Statusleiste in der linken unteren Ecke des VS Code-Fensters angezeigt.

> [!NOTE]
>
> - Data Lake Tools meldet Sie beim nächsten Mal automatisch an, wenn Sie sich nicht abmelden.
> - Wenn für Ihr Konto zwei Faktoren aktiviert wurden, empfehlen wir anstelle einer PIN die Verwendung der Telefonauthentifizierung.

Um sich abzumelden, geben Sie den Befehl **ADL: Logout** (HDInsight: Abmelden) ein.

### <a name="to-connect-to-azure-from-the-explorer"></a>Herstellen einer Azure-Verbindung über Explorer

Erweitern Sie **AZURE DATALAKE**, wählen Sie **Bei Azure anmelden** aus, und führen Sie Schritt 3 und 4 von [So stellen Sie eine Verbindung mit Azure per Befehl her](#sign-in-by-command) aus.

![Auswahl „Bei Azure anmelden“ im Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Sie können sich nicht beim Explorer abmelden. Informationen zur Abmeldung finden Sie unter [So stellen Sie eine Verbindung mit Azure per Befehl her](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Erstellen eines Extraktionsskripts

Sie können ein Extraktionsskript für CSV-, TSV- und TXT-Dateien erstellen, indem Sie den Befehl **ADL: Create EXTRACT Script** oder den Azure Data Lake-Explorer verwenden.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Erstellen eines Extraktionsskripts per Befehl

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen, und geben Sie **ADL: Create EXTRACT Script** ein.
2. Geben Sie den vollständigen Pfad für eine Azure-Speicherdatei an, und drücken Sie die Eingabetaste.
3. Wählen Sie ein Konto aus.
4. Wählen Sie für eine TXT-Datei ein Trennzeichen zum Extrahieren der Datei aus.

![Prozess zum Erstellen eines Extraktionsskripts](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Das Extraktionsskript wird basierend auf Ihren Einträgen generiert. Wählen Sie für ein Skript, mit dem die Spalten nicht erkannt werden können, eine der beiden Optionen. Wenn nicht, wird nur ein Skript generiert.

![Ergebnis der Erstellung eines Extraktionsskripts](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Erstellen eines Extraktionsskripts über Explorer

Eine andere Möglichkeit zur Erstellung eines Extraktionsskripts ist die Verwendung des Kontextmenüs für die CSV-, TSV- oder TXT-Datei im Azure Data Lake Store oder Azure-Blobspeicher.

![Befehl „Create EXTRACT Script“ aus dem Kontextmenü](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln von U-SQL mit Python, R und C# für Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Erste Schritte mit Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Entwickeln von U-SQL-Skripts mit Data Lake Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)