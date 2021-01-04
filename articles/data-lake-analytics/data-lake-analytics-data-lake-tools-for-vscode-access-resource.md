---
title: Zugreifen auf Ressourcen mit Data Lake Tools
description: Erfahren Sie, wie Sie Azure Data Lake Tools für den Zugriff auf Azure Data Lake Analytics-Ressourcen verwenden.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754534"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Zugreifen auf Ressourcen mit Azure Data Lake Tools

Sie können mit Azure Data Tools-Befehlen oder -Aktionen in VS Code problemlos auf Azure Data Lake Analytics-Ressourcen zugreifen.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrieren mit Azure Data Lake Analytics per Befehl

Sie können auf Azure Data Lake Analytics-Ressourcen zugreifen, um Konten aufzulisten, auf Metadaten zuzugreifen und Analyseaufträgen anzuzeigen.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Auflisten der Azure Data Lake Analytics-Konten in Ihrem Azure-Abonnement

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen.
2. Geben Sie **ADL: List Accounts** ein. Die Konten werden im Bereich **Ausgabe** angezeigt.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Zugreifen auf Azure Data Lake Analytics-Metadaten

1. Drücken Sie STRG+UMSCHALT+P, und geben Sie dann **ADL: List Tables** ein.
2. Wählen Sie eines der Data Lake Analytics-Konten aus.
3. Wählen Sie eine der Data Lake Analytics-Datenbanken aus.
4. Wählen Sie eines der Schemas aus. Sie können die Liste der Tabellen anzeigen.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Anzeigen von Azure Data Lake Analytics-Aufträgen

1. Öffnen Sie die Befehlspalette (STRG+UMSCHALT+P), und wählen Sie **ADL: Show Jobs** aus.
2. Wählen Sie ein Data Lake Analytics- oder ein lokales Konto aus.
3. Warten Sie, bis die Auftragsliste für das Konto angezeigt wird.
4. Wählen Sie einen Auftrag aus der Liste aus. Data Lake Tools öffnet die Auftragsansicht im rechten Bereich und zeigt einige Informationen in VS Code an.

    ![Auftragsliste](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrieren mit Azure Data Lake Store per Befehl

Sie können auf Azure Data Lake Store bezogene Befehle für Folgendes verwenden:

- [Durchsuchen der Azure Data Lake Store-Ressourcen](#list-the-storage-path)
- [Anzeigen einer Vorschau der Azure Data Lake Store-Datei](#preview-the-storage-file)
- Hochladen der Datei direkt in Azure Data Lake Store in VS Code
- Herunterladen der Datei direkt aus Azure Data Lake Store in VS Code

### <a name="list-the-storage-path"></a>Auflisten des Speicherpfads

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Auflisten des Speicherpfads über die Befehlspalette

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie **ADL: List Path** aus.
2. Wählen Sie den Ordner in der Liste aus, oder wählen Sie **Pfad eingeben** oder **Vom Stamm durchsuchen** aus. (Als Beispiel wird **Pfad eingeben** verwendet.)
3. Wählen Sie Ihr Data Lake Analytics-Konto aus.
4. Navigieren Sie zum Speicherordnerpfad, oder geben Sie ihn ein (z.B. /output/).  

In der Befehlspalette werden die auf Ihren Eingaben basierenden Pfadinformationen aufgelistet.

![Speicherpfadergebnisse](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Alternativ können Sie den relativen Pfad auch über das Kontextmenü auflisten.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Auflisten des Speicherpfads über das Kontextmenü

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

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Herunterladen einer Datei mithilfe des Befehls „ADL: Download File (Advanced)“

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie anschließend **Download File (Advanced)** (Datei herunterladen (Erweitert)) aus.
2. In VS Code wird eine JSON-Datei angezeigt. Sie können Dateipfade eingeben und mehrere Dateien gleichzeitig herunterladen. Die entsprechenden Anweisungen werden im Fenster **Ausgabe** angezeigt. Speichern Sie die JSON-Datei (STRG+S), um mit dem Herunterladen der Datei(en) fortzufahren.

    ![JSON-Datei mit Pfaden für Dateidownload](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

Im Fenster **Ausgabe** wird der Dateidownloadstatus angezeigt.

![Fenster „Ausgabe“ mit Downloadstatus](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Sie können den [Downloadstatus überwachen](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Herunterladen einer Datei mithilfe des Befehls „ADL: Download File“

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, klicken Sie auf **Datei herunterladen**, und wählen sie dann im Dialogfeld **Ordner auswählen** den Zielordner aus.

1. Wählen Sie den Ordner in der Liste aus, oder wählen Sie **Pfad eingeben** oder **Vom Stamm durchsuchen** aus. (Als Beispiel wird **Pfad eingeben** verwendet.)

1. Wählen Sie Ihr Data Lake Analytics-Konto aus.

1. Navigieren Sie zum Speicherordnerpfad, oder geben Sie ihn ein (z.B. /output/), und wählen Sie eine Datei zum Herunterladen aus.

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

Erweitern Sie Ihr Azure-Abonnement. Unter dem Knoten **U-SQL Databases** können Sie Ihre U-SQL-Datenbank durchsuchen und Ordner anzeigen wie **Schemas**, **Credentials**, **Assemblies**, **Tables** und **Index**.

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

## <a name="additional-features"></a>Zusätzliche Features

Data Lake Tools für VS Code unterstützt die folgenden Features:

- **IntelliSense-AutoVervollständigen**: Vorschläge werden in Popupfenstern für Elemente wie Schlüsselwörter, Methoden und Variablen angezeigt. Die unterschiedlichen Objekttypen werden durch entsprechende Symbole dargestellt:

  - Scala-Datentyp
  - Komplexer Datentyp
  - Integrierte UDTs
  - .NET-Sammlung und -Klassen
  - C#-Ausdrücke
  - Integrierte C#-UDFs, -UDOs und -UDAAGs
  - U-SQL-Funktionen
  - U-SQL-Windowingfunktionen

    ![IntelliSense-Objekttypen](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **IntelliSense-AutoVervollständigen für Data Lake Analytics-Metadaten**: Data Lake Tools lädt die Data Lake Analytics-Metadateninformationen lokal herunter. Die IntelliSense-Funktion füllt automatisch die Objekte aus den Data Lake Analytics-Metadaten. Zu diesen Objekten gehören Datenbank, Schema, Tabelle, Ansicht, Tabellenwertfunktion, Prozeduren und C#-Assemblys.

  ![IntelliSense-Metadaten](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense-Fehlermarker**: Data Lake Tools unterstreicht Bearbeitungsfehler für U-SQL und C#.
- **Syntaxhervorhebungen**: Data Lake Tools verwendet Farben, um Elemente wie Variablen, Schlüsselwörter, Datentypen und Funktionen zu unterscheiden.

    ![Syntax mit verschiedenen Farben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Wir empfehlen Ihnen das Upgrade auf Azure Data Lake Tools für Visual Studio Code Version 2.3.3000.4 (oder höher). Ältere Versionen stehen nicht mehr als Download zur Verfügung und sind veraltet.  

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln von U-SQL mit Python, R und C# für Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Erste Schritte mit Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Entwickeln von U-SQL-Skripts mit Data Lake Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)