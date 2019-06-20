---
title: Debuggen von benutzerdefiniertem C#-Code für fehlerhafte Azure Data Lake-U-SQL-Aufträge
description: In diesem Artikel erfahren Sie, wie Sie U-SQL-Vertexfehler mit Azure Data Lake Tools für Visual Studio debuggen.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 5417f66696191cebadc2af9c6d634419a0eb8e5b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615385"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Debuggen von benutzerdefiniertem C#-Code für fehlerhafte U-SQL-Aufträge

Für U-SQL wird ein Erweiterbarkeitsmodell mit C# bereitgestellt. In U-SQL-Skripts ist es einfach, C#-Funktionen aufzurufen und Analysefunktionen durchzuführen, die von einer SQL-ähnlichen deklarativen Sprache nicht unterstützt werden. Weitere Informationen zur U-SQL-Erweiterbarkeit finden Sie im [U-SQL-Programmierbarkeitshandbuch](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

In der Praxis muss das Debuggen ggf. für jeden Code durchgeführt werden. Aber es ist schwierig, einen verteilten Auftrag mit benutzerdefiniertem Code in der Cloud zu debuggen, wenn nur eingeschränkte Protokolldateien vorhanden sind. [Azure Data Lake Tools für Visual Studio](https://aka.ms/adltoolsvs) enthalten ein Feature zum **Debuggen von Vertexfehlern**, mit dem das Debuggen von Fehlern in Ihrem benutzerdefinierten Code vereinfacht wird. Wenn für den U-SQL-Auftrag ein Fehler auftritt, behält der Dienst den Fehlerstatus bei, und das Tool unterstützt Sie beim Herunterladen der Cloudfehlerumgebung auf den lokalen Computer für das Debuggen. Der lokale Download umfasst die gesamte Cloudumgebung, einschließlich Eingabedaten und Benutzercode.

Das folgende Video zeigt die Verwendung des Features „Debuggen von Vertexfehlern“ in Azure Data Lake Tools für Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Für Visual Studio müssen zur Nutzung dieses Features die beiden folgenden Updates installiert sein: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) und [Universelle C-Runtime für Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Herunterladen eines fehlerhaften Vertex auf den lokalen Computer

Wenn Sie in Azure Data Lake Tools für Visual Studio einen fehlerhaften Auftrag öffnen, wird eine gelbe Warnleiste mit ausführlichen Fehlermeldungen auf der Fehlerregisterkarte angezeigt.

1. Klicken Sie auf **Herunterladen** , um alle erforderlichen Ressourcen und Eingabedatenströme herunterzuladen. Sollte der Downloadvorgang nicht abgeschlossen werden, klicken Sie auf **Wiederholen**.

2. Klicken Sie nach Abschluss des Downloadvorgangs auf **Öffnen**, um eine lokale Debugumgebung zu generieren. Eine neue Debugprojektmappe wird geöffnet, und wenn Sie die vorhandene Projektmappe in Visual Studio geöffnet haben, achten Sie bitte darauf, sie vor dem Debuggen zu speichern und zu schließen.

![Azure Data Lake Analytics, Debuggen von U-SQL-Aufträgen mit Visual Studio, Herunterladen von Vertexdaten](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurieren der Debugumgebung

> [!NOTE]
> Vergewissern Sie sich vor dem Debuggen, dass im Fenster mit den Ausnahmeeinstellungen(**STRG+ALT+E**) die Option **Common Language Runtime-Ausnahmen** aktiviert ist.

![Azure Data Lake Analytics, Debuggen von U-SQL-Aufträgen mit Visual Studio, Einstellung](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Es kann sein, dass Sie den benutzerdefinierten C#-Quellcode in der neu gestarteten Visual Studio-Instanz finden oder auch nicht:

1. [Ich kann meinen Quellcode in der Lösung finden](#source-code-is-included-in-debugging-solution)

2. [Ich kann meinen Quellcode nicht in der Lösung finden](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Quellcode ist in Debugprojektmappe enthalten

Es gibt zwei Fälle, in denen der C#-Quellcode erfasst wird:

1. Der Benutzercode wird in einer CodeBehind-Datei (in einem U-SQL-Projekt normalerweise `Script.usql.cs`) definiert.

2. Der Benutzercode wird in einem C#-Klassenbibliotheksprojekt für die U-SQL-Anwendung definiert und als Assembly mit **Debuginformationen** registriert.

Wenn der Quellcode in die Lösung importiert wird, können Sie die Visual Studio-Debugtools (Überwachung, Variablen usw.) nutzen, um die Problembehandlung durchzuführen:

1. Drücken Sie **F5**, um das Debuggen zu starten. Der Code wird ausgeführt, bis er durch eine Ausnahme beendet wird.

2. Öffnen Sie die Quellcodedatei, und legen Sie Breakpoints fest. Drücken Sie anschließend **F5**, um den Code schrittweise zu debuggen.

    ![Azure Data Lake Analytics-U-SQL-Debugausnahme](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Quellcode ist nicht in Debugprojektmappe enthalten

Wenn der Benutzercode nicht in der CodeBehind-Datei enthalten ist oder Sie die Assembly nicht mit **Debuginformationen** registriert haben, wird der Quellcode nicht automatisch in die Debugprojektmappe eingebunden. In diesem Fall müssen Sie zusätzliche Schritte ausführen, um den Quellcode hinzuzufügen:

1. Klicken Sie mit der rechten Maustaste auf die Projektmappe **VertexDebug**, und klicken Sie anschließend auf „Hinzufügen“ > „Vorhandenes Projekt...“, um nach dem Quellcode der Assembly zu suchen und das Projekt der Debugprojektmappe hinzuzufügen.

    ![Azure Data Lake Analytics-U-SQL-Debugprojekt hinzufügen](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Rufen Sie den Projektordnerpfad für das Projekt **FailedVertexDebugHost** ab. 

3. Klicken Sie mit der rechten Maustaste auf das hinzugefügte Projekt mit dem Assemblyquellcode, klicken Sie auf **Eigenschaften**, wählen Sie links die Registerkarte **Erstellen** aus, und fügen Sie den in Schritt 2 kopierten Pfad, der auf „\bin\debug“ endet, als **Ausgabe > Ausgabepfad** ein. Der endgültige Ausgabepfad lautet `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Azure Data Lake Analytics-U-SQL-Debugprojekt, PDB-Pfad einrichten](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Nachdem Sie diese Einstellungen vorgenommen haben, können Sie das Debuggen mit **F5** und Breakpoints starten. Sie können das Problem auch mithilfe der Visual Studio-Debugtools (Überwachung, Variablen usw.) behandeln.

> [!NOTE]
> Das Projekt mit dem Assemblyquellcode muss nach jeder Änderung des Codes neu erstellt werden, um aktualisierte PDB-Dateien zu generieren.

## <a name="resubmit-the-job"></a>Erneutes Übermitteln des Auftrags

Wenn das Projekt nach dem Debuggen erfolgreich abgeschlossen wird, erscheint im Ausgabefenster die folgende Meldung:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics-U-SQL-Debugging erfolgreich](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Gehen Sie wie folgt vor, um den fehlgeschlagenen Auftrag erneut zu senden:

1. Kopieren Sie bei Aufträgen mit CodeBehind-Projektmappen den C#-Code in die CodeBehind-Quelldatei (in der Regel `Script.usql.cs`).

2. Klicken Sie für Aufträge mit Assemblys in der Debugprojektmappe mit der rechten Maustaste auf das Assembly-Quellcodeprojekt, und registrieren Sie die aktualisierten DLL-Assemblys in Ihrem Azure Data Lake-Katalog.

3. Übermitteln Sie den U-SQL-Auftrag erneut.

## <a name="next-steps"></a>Nächste Schritte

- [U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
- [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Azure Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testen und Debuggen von U-SQL-Aufträgen mit lokalen Testläufen und dem Azure Data Lake-U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Behandeln von Problemen mit nicht normalen periodischen Aufträgen](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
