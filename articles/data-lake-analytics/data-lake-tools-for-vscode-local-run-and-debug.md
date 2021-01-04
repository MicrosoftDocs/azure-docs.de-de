---
title: Debuggen von U-SQL-Aufträgen – Azure Data Lake Tools für Visual Studio Code
description: Hier erfahren Sie, wie Sie U-SQL-Aufträge mithilfe von Azure Data Lake Tools für Visual Studio Code lokal ausführen und debuggen.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 07/14/2017
ms.openlocfilehash: ef32372cbaa70f7bca609c2ed1db118f33237e2b
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "92216197"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Lokales Ausführen und Debuggen von U-SQL in Visual Studio Code
In diesem Artikel erfahren Sie, wie Sie U-SQL-Aufträge auf einem lokalen Entwicklungscomputer ausführen, um frühe Programmierphasen zu beschleunigen oder Code lokal in Visual Studio Code zu debuggen. Weitere Informationen zur Verwendung von Azure Data Lake Tools für Visual Studio Code finden Sie unter [Verwenden von Azure Data Lake Tools für Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Nur Windows-Installationen der Azure Data Lake Tools für Visual Studio unterstützen die Aktion zum lokalen Ausführen von U-SQL und zum lokalen Debuggen von U-SQL. Installationen unter MacOS und auf Linux-basierten Betriebssystemen unterstützen dieses Feature nicht.

## <a name="set-up-the-u-sql-local-run-environment"></a>Einrichten der Umgebung zum lokalen Ausführen von U-SQL

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen, und geben Sie **ADL: Download Local Run Package** ein, um die Pakete herunterzuladen.  

   ![Herunterladen der ADL-Abhängigkeitspakete für die lokale Ausführung](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Suchen Sie die Abhängigkeitspakete unter dem im Bereich **Ausgabe** angezeigten Pfad, und installieren Sie dann BuildTools und Win10SDK 10240. Beispielpfad:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Suchen der Abhängigkeitspakete](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Klicken Sie zum Installieren von **BuildTools** auf „visualcppbuildtools_full.exe“ in dem Ordner „LocalRunDependency“, und folgen Sie anschließend den Anweisungen des Assistenten.   

    ![Installieren der BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Klicken Sie zum Installieren von **Win10SDK 10240** auf „sdksetup.exe“ in dem Ordner „LocalRunDependency/Win10SDK_10.0.10240_2“, und folgen Sie anschließend den Anweisungen des Assistenten.  

    ![Installieren von Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Legen Sie die Umgebungsvariable fest. Legen Sie die Umgebungsvariable **SCOPE_CPP_SDK** auf folgenden Wert fest:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Starten des Diensts für die lokale Ausführung und Übermitteln eines U-SQL-Auftrags an ein lokales Konto 
Anweisung für Erstbenutzer: Verwenden Sie **ADL: Download Local Run Package**, um lokal ausgeführte Pakete herunterzuladen, wenn [die Umgebung zum lokalen Ausführen von U-SQL noch eingerichtet werden muss](#set-up-the-u-sql-local-run-environment).

1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen, und geben Sie **ADL: Start Local Run Service** ein   
2. Wählen Sie **Accept** (Zustimmen) aus, um die Microsoft Software-Lizenzbedingungen erstmalig zu akzeptieren. 

   ![Annehmen der Microsoft-Software-Lizenzbedingungen](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Die Befehlskonsole wird geöffnet. Als Erstbenutzer müssen Sie **3** eingeben. Ermitteln Sie dann den lokalen Ordnerpfad für die Eingabe und Ausgabe Ihrer Daten. Falls es Ihnen nicht gelingt, den Pfad mit umgekehrten Schrägstrichen zu definieren, versuchen Sie es mit normalen Schrägstrichen. Für die anderen Optionen können Sie die Standardwerte verwenden.

   ![Data Lake Tools für Visual Studio Code – Befehlskonsole für lokale Ausführung](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen, geben Sie **ADL: Submit Job** ein, und wählen Sie **Local** aus, um den Auftrag an Ihr lokales Konto zu übermitteln.

   ![Data Lake Tools für Visual Studio Code – Auswählen von „local“](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Nach dem Übermitteln des Auftrags können Sie Übermittlungsdetails anzeigen. Wählen Sie zum Anzeigen der Übermittlungsdetails im Fenster **Ausgabe** die Option **jobUrl** aus. Sie können den Übermittlungsstatus des Auftrags auch über die Befehlskonsole anzeigen. Geben Sie an der Befehlskonsole **7** ein, wenn Sie weitere Auftragsdetails anzeigen möchten.

   ![Data Lake Tools für Visual Studio Code – Ausgabe für lokale Ausführung](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools für Visual Studio Code – Status für lokale Ausführung](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Starten des lokalen Debuggens für den U-SQL-Auftrag  
Anweisung für Erstbenutzer:

1. Verwenden Sie **ADL: Download Local Run Package**, um lokal ausgeführte Pakete herunterzuladen, wenn [die Umgebung zum lokalen Ausführen von U-SQL noch eingerichtet werden muss](#set-up-the-u-sql-local-run-environment).
2. Installieren Sie, falls noch nicht geschehen, wie empfohlen .NET Core SDK 2.0 im Nachrichtenfeld.
 
  ![Erinnerung installiert Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installieren Sie, falls noch nicht geschehen, wie empfohlen C# für Visual Studio Code im Nachrichtenfeld.  Klicken Sie auf **Installieren**, um fortzufahren, und starten Sie VSCode anschließend neu.

    ![Erinnerung an die Installation von C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Folgen Sie den untenstehenden Schritten, um lokal zu debuggen:
  
1. Drücken Sie STRG+UMSCHALT+P, um die Befehlspalette zu öffnen, und geben Sie **ADL: Start Local Run Service** ein Die Befehlskonsole wird geöffnet. Stellen Sie sicher, dass **DataRoot** festgelegt ist.
2. Legen Sie einen Breakpoint im zugrunde liegenden C#-Code fest.
3. Gehen Sie zurück zum Skript-Editor, klicken Sie mit der rechten Maustaste, und wählen Sie **ADL: Local Debug** aus.
    
   ![Data Lake Tools für Visual Studio Code – Ergebnis des lokales Debuggens](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Nächste Schritte
* [Verwenden der Azure Data Lake-Tools für Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Entwickeln von U-SQL mit Python, R und CSharp für Azure Data Lake Analytics in Visual Studio Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Erste Schritte mit Data Lake Analytics mithilfe von PowerShell](data-lake-analytics-get-started-powershell.md)
* [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
* [Verwenden von Data Lake-Tools für Visual Studio zum Entwickeln von U-SQL-Anwendungen](data-lake-analytics-data-lake-tools-get-started.md)
* [Verwenden des Azure Data Lake Analytics-Katalogs (U-SQL)](./data-lake-analytics-u-sql-get-started.md)