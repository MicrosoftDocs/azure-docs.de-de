---
title: Verwenden von Azure Functions zum Ausführen eines Datenbank-Bereinigungstasks| Microsoft-Dokumentation
description: Verwenden Sie Azure Functions, um eine Aufgabe zu planen, die eine Verbindung mit Azure SQL-Datenbank herstellt, um regelmäßig Zeilen zu bereinigen.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: 19a5fe4c087d477ff15d2237a36d1c4ecaa0e070
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908115"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Verwenden von Azure Functions zum Herstellen einer Verbindung mit einer Azure SQL-Datenbank-Instanz

In diesem Artikel wird erläutert, wie Sie mit Azure Functions einen geplanten Auftrag erstellen, über den eine Verbindung mit einer Azure SQL-Datenbank-Instanz hergestellt wird. Der Funktionscode bereinigt die Zeilen in einer Tabelle in der Datenbank. Die neue C#-Funktion wird basierend auf einer vordefinierten Vorlage für einen Zeitgebertrigger in Visual Studio 2019 erstellt. Zur Unterstützung dieses Szenarios müssen Sie auch eine Datenbank-Verbindungszeichenfolge als App-Einstellung in der Funktions-App festlegen. In diesem Szenario wird ein Massenvorgang auf die Datenbank angewendet. 

Wenn Sie C#-Funktionen zum ersten Mal verwenden, sollten Sie die [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md) lesen.

## <a name="prerequisites"></a>Voraussetzungen

+ Führen Sie die Schritte im Artikel [Erstellen Ihrer ersten Funktion mit Visual Studio](functions-create-your-first-function-visual-studio.md) aus, um eine lokale Funktions-App für Version 2.x der Runtime zu erstellen. Zudem müssen Sie Ihr Projekt in einer Funktions-App in Azure veröffentlicht haben.

+ In diesem Artikel wird ein Transact-SQL-Befehl veranschaulicht, der einen Massenbereinigungsvorgang in der Tabelle **SalesOrderHeader** in der Beispieldatenbank „AdventureWorksLT“ ausführt. Um die Beispieldatenbank „AdventureWorksLT“ zu erstellen, führen Sie die Schritte im Artikel [Erstellen einer Azure SQL-Datenbank-Instanz im Azure-Portal](../sql-database/sql-database-get-started-portal.md) aus.

+ Sie müssen eine [Firewallregel auf Serverebene](../sql-database/sql-database-get-started-portal-firewall.md) für die öffentliche IP-Adresse des Computers hinzufügen, den Sie für diesen Schnellstart verwenden. Diese Regel ist erforderlich, damit über den lokalen Computer auf die SQL-Datenbank-Instanz zugegriffen werden kann.  

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Sie müssen die Verbindungszeichenfolge für die Datenbank abrufen, die Sie in [Erstellen einer Azure SQL-Datenbank-Instanz im Azure-Portal](../sql-database/sql-database-get-started-portal.md) erstellt haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie im Menü auf der linken Seite die Option **SQL-Datenbanken** und anschließend auf der Seite **SQL-Datenbanken** Ihre Datenbank aus.

1. Wählen Sie unter **Einstellungen** die Option **Verbindungszeichenfolgen** aus, und kopieren Sie die vollständige **ADO.NET**-Verbindungszeichenfolge.

    ![Kopieren Sie die ADO.NET-Verbindungszeichenfolge.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Festlegen der Verbindungszeichenfolge

Eine Funktions-App hostet die Ausführung Ihrer Funktionen in Azure. Als bewährte Sicherheitsmethode empfiehlt es sich, Verbindungszeichenfolgen und andere Geheimnisse in den Einstellungen Ihrer Funktions-App zu speichern. Die Verwendung von Anwendungseinstellungen verhindert eine versehentliche Offenlegung der Verbindungszeichenfolge mit dem Code. Auf die App-Einstellungen für Ihre Funktions-App können Sie direkt in Visual Studio zugreifen.

Zuvor müssen Sie Ihre App in Azure veröffentlicht haben. Wenn dies noch nicht erfolgt ist, [veröffentlichen Sie die Funktions-App in Azure](functions-develop-vs.md#publish-to-azure).

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Funktions-App-Projekt, und wählen Sie **Veröffentlichen** > **Anwendungseinstellungen verwalten** aus. Wählen Sie **Einstellung hinzufügen** aus, geben Sie in **Name der neuen App-Einstellung** den Namen `sqldb_connection` ein, und wählen Sie **OK** aus.

    ![Anwendungseinstellungen für Funktions-App](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Fügen Sie in der neuen Einstellung **sqldb_connection** die Verbindungszeichenfolge, die Sie im vorherigen Abschnitt kopiert haben, im Feld **Lokal** ein, und ersetzen Sie die Platzhalter `{your_username}` und `{your_password}` durch die tatsächlichen Werte. Wählen Sie **Wert aus lokaler Quelle einfügen** aus, um den aktualisierten Wert in das Feld **Remote** zu kopieren, und wählen Sie dann **OK** aus.

    ![Einstellung „SQL-Verbindungszeichenfolge hinzufügen“](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Die Verbindungszeichenfolgen werden verschlüsselt in Azure gespeichert (**Remote**). Um zu verhindern, dass Geheimnisse offengelegt werden, sollte die Projektdatei „local.settings.json“ (**Lokal**) von der Quellcodeverwaltung ausgeschlossen werden, z.B. durch Verwendung einer GITIGNORE-Datei.

## <a name="add-the-sqlclient-package-to-the-project"></a>Hinzufügen des SqlClient-Pakets zum Projekt

Sie müssen das NuGet-Paket hinzufügen, das die SqlClient-Bibliothek enthält. Diese Datenzugriffsbibliothek wird zum Herstellen einer Verbindung mit einer SQL-Datenbank benötigt.

1. Öffnen Sie das lokale Funktions-App-Projekt in Visual Studio 2019.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Funktions-App-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Suchen Sie auf der Registerkarte **Durchsuchen** nach ```System.Data.SqlClient```, und wählen Sie das Element aus.

1. Wählen Sie auf der Seite **System.Data.SqlClient** die Version `4.5.1` aus, und klicken Sie dann auf **Installieren**.

1. Überprüfen Sie nach Abschluss der Installation die Änderungen, und klicken Sie anschließend auf **OK**, um das **Vorschaufenster** zu schließen.

1. Sollte das Fenster **Zustimmung zur Lizenz** angezeigt werden, klicken Sie auf **Ich stimme zu**.

Nun können Sie den C#-Funktionscode hinzufügen, der eine Verbindung mit Ihrer SQL-Datenbank herstellt.

## <a name="add-a-timer-triggered-function"></a>Hinzufügen einer Funktion mit Auslösung per Timer

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Funktions-App-Projekt, und wählen Sie **Hinzufügen** > **Neue Azure-Funktion** aus.

1. Wählen Sie die Vorlage **Azure Functions** aus, geben Sie dem neuen Element den Namen `DatabaseCleanup.cs`, und wählen Sie **Hinzufügen** aus.

1. Wählen Sie im Dialogfeld **Neue Azure-Funktion** die Option **Zeitgebertrigger** und dann **OK** aus. In diesem Dialogfeld wird eine Codedatei für die Funktion mit Auslösung per Zeitgeber erstellt.

1. Öffnen Sie die neue Codedatei, und fügen Sie am Anfang der Datei die folgenden using-Anweisungen ein:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Ersetzen Sie die vorhandene `Run`-Funktion durch den folgenden Code:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Diese Funktion wird alle 15 Sekunden ausgeführt, um die Spalte `Status` basierend auf dem Lieferdatum zu aktualisieren. Weitere Informationen zum Zeitgebertrigger finden Sie unter [Trigger mit Timer für Azure Functions](functions-bindings-timer.md).

1. Drücken Sie **F5**, um die Funktions-App zu starten. Das Ausführungsfenster [Azure Functions Core Tools](functions-develop-local.md) wird im Hintergrund von Visual Studio geöffnet.

1. Die Funktion wird 15 Sekunden nach dem Start ausgeführt. Sehen Sie sich die Ausgabe an, und beachten Sie die Anzahl der in der Tabelle **SalesOrderHeader** aktualisierten Zeilen.

    ![Zeigen Sie die Funktionsprotokolle an.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Bei der ersten Ausführung sollten 32 Datenzeilen aktualisiert werden. In nachfolgenden Ausführungen werden keine Datenzeilen aktualisiert, es sei denn, Sie nehmen Änderungen an den Daten der Tabelle „SalesOrderHeader“ vor, sodass mit der `UPDATE`-Anweisung weitere Zeilen ausgewählt werden.

Wenn Sie [die Funktion veröffentlichen](functions-develop-vs.md#publish-to-azure) möchten, vergessen Sie nicht, das Attribut `TimerTrigger` in einen geeigneteren [CRON-Zeitplan](functions-bindings-timer.md#cron-expressions) als alle 15 Sekunden zu ändern.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes erfahren Sie, wie Sie Functions mit Logic Apps für die Integration in andere Dienste verwenden.

> [!div class="nextstepaction"]
> [Erstellen einer Funktion, die in Logic Apps integriert ist](functions-twitter-email.md)

Weitere Informationen zu Functions finden Sie in den folgenden Artikeln:

+ [Entwicklerreferenz zu Azure Functions](functions-reference.md)  
  Referenz zum Programmieren von Funktionen sowie zum Festlegen von Triggern und Bindungen.
+ [Testing Azure Functions (Testen von Azure Functions) (Testen von Azure Functions)](functions-test-a-function.md)  
  Beschreibt verschiedene Tools und Techniken zum Testen Ihrer Funktionen  
