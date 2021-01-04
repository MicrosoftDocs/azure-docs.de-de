---
title: 'Herstellen einer Verbindung mit einer verwalteten Identität: Azure Database for PostgreSQL-Einzelserver'
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von verwalteten Identitäten eine Verbindung mit Azure Database for PostgreSQL herstellen und sich authentifizieren.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 444fbb08dfa535980c4012858b675e700ffa29d8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745099"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Herstellen einer Verbindung zu Azure Database for PostgreSQL mithilfe von verwalteten Identitäten

In diesem Artikel erfahren Sie, wie Sie mit einer benutzerseitig zugewiesenen Identität für einen virtuellen Windows-Computer (Virtual Machine, VM) auf einen Azure Database for PostgreSQL-Server zuzugreifen. Verwaltete Dienstidentitäten werden von Azure automatisch verwaltet und ermöglichen Ihnen die Authentifizierung für Dienste, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. 

Folgendes wird vermittelt:
- Erteilen des VM-Zugriffs auf einen Azure Database for PostgreSQL-Server
- Erstellen eines Benutzers in der Datenbank, der die benutzerseitig zugewiesene Identität der VM darstellt
- Abrufen eines Zugriffstokens mithilfe der VM-Identität und Abfragen eines Azure Database for PostgreSQL-Servers mithilfe dieses Tokens
- Implementieren des Tokenabrufs in einer C#-Beispielanwendung

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie mit der Funktion für verwaltete Identitäten für Azure-Ressourcen nicht vertraut sind, finden Sie hier eine [Übersicht](../../articles/active-directory/managed-identities-azure-resources/overview.md). Wenn Sie kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Ihr Konto muss für den entsprechenden Gültigkeitsbereich (Ihr Abonnement oder die Ressourcengruppe) über die Berechtigung „Besitzer“ verfügen, um die erforderliche Ressourcenerstellung und eine Rollenverwaltung durchführen zu können. Wenn Sie Unterstützung bei der Rollenzuweisung benötigen, finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../../articles/role-based-access-control/role-assignments-portal.md) weitere Informationen.
- Sie benötigen eine Azure-VM (z. B. unter Ubuntu Linux), mit der Sie mithilfe von verwalteten Identitäten auf Ihre Datenbank zugreifen möchten.
- Sie benötigen einen Azure Database for PostgreSQL-Datenbankserver, auf dem die [Azure AD-Authentifizierung](howto-configure-sign-in-aad-authentication.md) konfiguriert ist.
- Für das C#-Beispiel sollten Sie zuerst den Leitfaden zur [Verbindungsherstellung mit C#](connect-csharp.md) lesen.

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität für Ihre VM

Erstellen Sie mit dem Befehl [az identity create](/cli/azure/identity#az-identity-create) eine Identität in Ihrem Abonnement. Sie können dazu dieselbe Ressourcengruppe verwenden, in der Ihre VM ausgeführt wird, oder auch eine andere.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

In den folgenden Schritten werden Sie die Identität konfigurieren. Verwenden Sie den Befehl [az identity show](/cli/azure/identity#az-identity-show), um die Ressourcen- und die Client-ID der Identität in Variablen zu speichern.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Mit dem Befehl [az vm identity assign](/cli/azure/vm/identity#az-vm-identity-assign) können Sie nun die benutzerseitig zugewiesene Identität der VM zuweisen:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Zum Abschließen der Einrichtung müssen Sie den Wert der Client-ID abrufen, den Sie in den nächsten Schritten benötigen:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Erstellen eines PostgreSQL-Benutzers für Ihre verwaltete Identität

Nun stellen Sie als Azure AD-Administrator eine Verbindung mit Ihrer PostgreSQL-Datenbank her und führen die folgenden SQL-Anweisungen aus:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

Die verwaltete Identität hat jetzt Zugriff, wenn die Authentifizierung mit dem Benutzernamen `myuser` (durch einen Namen Ihrer Wahl ersetzen) durchgeführt wird.

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Abrufen des Zugriffstokens aus dem Dienst „Azure Instance Metadata“

Ihre Anwendung kann nun ein Zugriffstoken aus dem Dienst „Azure Instance Metadata“ abrufen und sich damit bei der Datenbank authentifizieren.

Dieses Token wird abgerufen, indem eine HTTP-Anforderung an `http://169.254.169.254/metadata/identity/oauth2/token` gesendet wird und die folgenden Parameter übergeben werden:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (die Sie zuvor abgerufen haben)

Es wird ein JSON-Ergebnis zurückgegeben, das ein `access_token`-Feld enthält. Dieser lange Textwert ist das Zugriffstoken der verwalteten Identität, das bei der Verbindungsherstellung zur Datenbank als Kennwort verwenden werden soll.

Zu Testzwecken können Sie die folgenden Befehle in Ihrer Shell ausführen. Beachten Sie, dass der `curl`-, `jq`- und der `psql`-Client installiert sein müssen.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Sie haben nun eine Verbindung zur Datenbank hergestellt, die Sie zuvor konfiguriert haben.

## <a name="connecting-using-managed-identity-in-c"></a>Herstellen einer Verbindung mithilfe einer verwalteten Identität in C#

In diesem Abschnitt wird gezeigt, wie Sie mithilfe der benutzerseitig zugewiesenen verwalteten Identität der VM ein Zugriffstoken abrufen und damit Azure Database for PostgreSQL aufrufen. Azure Database for PostgreSQL unterstützt die Azure AD-Authentifizierung nativ, sodass Zugriffstoken, die mit verwalteten Identitäten für Azure-Ressourcen abgerufen wurden, direkt angenommen werden können. Bei der Verbindungsherstellung mit PostgreSQL übergeben Sie das Zugriffstoken im Kennwortfeld.

Mit dem folgenden .NET-Codebeispiel wird eine Verbindung mit PostgreSQL mithilfe eines Zugriffstokens hergestellt. Dieser Code muss auf der VM ausgeführt werden, um auf den Endpunkt der benutzerseitig zugewiesenen verwalteten Identität der VM zugreifen zu können. Für die Verwendung von Zugriffstoken ist .NET Framework 4.6 oder höher bzw. .NET Core 2.2 oder höher erforderlich. Ersetzen Sie die Werte von HOST, USER, DATABASE und CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Wenn Sie diesen Befehl ausführen, wird eine Ausgabe wie die folgende zurückgegeben:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Nächste Schritte

* Überprüfen der Gesamtkonzepte für [Azure Active Directory-Authentifizierung mit Azure Database for PostgreSQL](concepts-aad-authentication.md)
