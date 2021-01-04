---
title: 'Schnellstart: Herstellen einer Verbindung mit C#: Azure Database for PostgreSQL-Einzelserver'
description: Dieser Schnellstart enthält ein C#(.NET)-Codebeispiel, das Sie nutzen können, um eine Verbindung mit einem Azure Database for PostgreSQL-Einzelserver herzustellen und Daten daraus abzufragen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: a06d07a7d54b6399ab5f83c41284fb2fab7217fb
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360270"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Schnellstart: Verwenden von .NET (C#) zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver sowie zum Abfragen von Daten

Dieser Schnellstart zeigt, wie Sie mit einer C#-Anwendung eine Verbindung mit einer Azure-Datenbank für PostgreSQL herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie mit der C#-Entwicklung vertraut sind und noch keine Erfahrung mit Azure-Datenbank für PostgreSQL haben.

## <a name="prerequisites"></a>Voraussetzungen
Für diese Schnellstartanleitung benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free).
- Erstellen Sie einen Azure Database for PostgreSQL-Einzelserver unter Verwendung des [Azure-Portals](./quickstart-create-server-database-portal.md) <br/> oder der [Azure CLI](./quickstart-create-server-database-azure-cli.md), wenn Sie noch keinen besitzen.
- Je nachdem, ob Sie öffentlichen oder privaten Zugriff verwenden, führen Sie **EINE** der folgenden Aktionen aus, um Konnektivität herzustellen:

  |Action| Konnektivitätsmethode|Schrittanleitung|
  |:--------- |:--------- |:--------- |
  | **Konfigurieren von Firewallregeln** | Öffentlich | [Portal](./howto-manage-firewall-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-firewall-using-cli.md)|
  | **Konfigurieren des Dienstendpunkts** | Öffentlich | [Portal](./howto-manage-vnet-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-vnet-using-cli.md)|
  | **Konfigurieren von Private Link** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-configure-privatelink-cli.md) |

- Installieren Sie das [.NET Framework](https://www.microsoft.com/net/download) für Ihre Plattform (Windows, Ubuntu Linux oder macOS).
- Installieren Sie [Visual Studio](https://www.visualstudio.com/downloads/), um Ihr Projekt zu erstellen.
- Installieren Sie das NuGet-Paket [Npgsql](https://www.nuget.org/packages/Npgsql/) in Visual Studio.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure-Datenbank für PostgreSQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**, und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.
3. Klicken Sie auf den Servernamen.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-csharp/1-connection-string.png" alt-text="Azure Database for PostgreSQL-Servername":::

## <a name="step-1-connect-and-insert-data"></a>Schritt 1: Herstellen einer Verbindung und Einfügen von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten zu laden, indem Sie die SQL-Anweisungen **CREATE TABLE** und **INSERT INTO** verwenden. Im Code wird die NpgsqlCommand-Klasse mit einer der folgenden Methoden verwendet:
- [Open()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) zum Herstellen einer Verbindung mit der PostgreSQL-Datenbank
- [CreateCommand()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) zum Festlegen der Eigenschaft „CommandText“
- [ExecuteNonQuery()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) zum Ausführen der Datenbankbefehle

> [!IMPORTANT]
> Ersetzen Sie die Parameter „Host“, „DBName“, „User“ und „Password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresCreate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin@mydemoserver";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0};Username={1};Database={2};Port={3};Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);


            using (var conn = new NpgsqlConnection(connString))

            {
                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("DROP TABLE IF EXISTS inventory", conn))
                {
                    command.ExecuteNonQuery();
                    Console.Out.WriteLine("Finished dropping table (if existed)");

                }

                using (var command = new NpgsqlCommand("CREATE TABLE inventory(id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER)", conn))
                {
                    command.ExecuteNonQuery();
                    Console.Out.WriteLine("Finished creating table");
                }

                using (var command = new NpgsqlCommand("INSERT INTO inventory (name, quantity) VALUES (@n1, @q1), (@n2, @q2), (@n3, @q3)", conn))
                {
                    command.Parameters.AddWithValue("n1", "banana");
                    command.Parameters.AddWithValue("q1", 150);
                    command.Parameters.AddWithValue("n2", "orange");
                    command.Parameters.AddWithValue("q2", 154);
                    command.Parameters.AddWithValue("n3", "apple");
                    command.Parameters.AddWithValue("q3", 100);

                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Schritt 2: Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **SELECT**-SQL-Anweisung zu verbinden und zu lesen. Im Code wird die NpgsqlCommand-Klasse mit einer der folgenden Methoden verwendet:
- [Open()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) zum Herstellen einer Verbindung mit PostgreSQL
- [CreateCommand()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) und [ExecuteReader()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteReader) zum Ausführen der Datenbankbefehle
- [Read()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_Read) zum Zugreifen auf den Datensatz in den Ergebnissen
- [GetInt32()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetInt32_System_Int32_) und [GetString()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetString_System_Int32_) zum Analysieren der Werte im Datensatz

> [!IMPORTANT]
> Ersetzen Sie die Parameter „Host“, „DBName“, „User“ und „Password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresRead
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin@mydemoserver";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {

                Console.Out.WriteLine("Opening connection");
                conn.Open();


                using (var command = new NpgsqlCommand("SELECT * FROM inventory", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine(
                            string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0).ToString(),
                                reader.GetString(1),
                                reader.GetInt32(2).ToString()
                                )
                            );
                    }
                    reader.Close();
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Schritt 3: Aktualisieren von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten mit der SQL-Anweisung **UPDATE** zu aktualisieren. Im Code wird die NpgsqlCommand-Klasse mit einer der folgenden Methoden verwendet:
- [Open()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) zum Herstellen einer Verbindung mit PostgreSQL
- [CreateCommand()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) zum Festlegen der Eigenschaft „CommandText“
- [ExecuteNonQuery()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) zum Ausführen der Datenbankbefehle

> [!IMPORTANT]
> Ersetzen Sie die Parameter „Host“, „DBName“, „User“ und „Password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresUpdate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin@mydemoserver";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {

                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("UPDATE inventory SET quantity = @q WHERE name = @n", conn))
                {
                    command.Parameters.AddWithValue("n", "banana");
                    command.Parameters.AddWithValue("q", 200);
                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-4-delete-data"></a>Schritt 4: Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **DELETE**-SQL-Anweisung zu verbinden und zu löschen.

Im Code wird die NpgsqlCommand-Klasse mit der [Open()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open)-Methode verwendet, um eine Verbindung mit der PostgreSQL-Datenbank einzurichten. Anschließend wird im Code die [CreateCommand()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand)-Methode verwendet, die CommandText-Eigenschaft festgelegt und die [ExecuteNonQuery()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery)-Methode aufgerufen, um die Datenbankbefehle auszuführen.

> [!IMPORTANT]
> Ersetzen Sie die Parameter „Host“, „DBName“, „User“ und „Password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresDelete
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin@mydemoserver";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("DELETE FROM inventory WHERE name = @n", conn))
                {
                    command.Parameters.AddWithValue("n", "orange");

                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Verwalten eines Azure Database for MySQL-Servers mithilfe des Portals](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Verwalten eines Azure Database for MySQL-Servers mithilfe der CLI](./how-to-manage-server-cli.md)

[Können Sie die gesuchten Informationen nicht finden? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)
