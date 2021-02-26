---
title: 'Schnellstart: Ihre erste .NET Core-Abfrage'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Aktivieren des Resource Graph-NuGet-Pakets für .NET Core und zum Ausführen Ihrer ersten Abfrage aus.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917570"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mit .NET Core

Wenn Sie Azure Resource Graph verwenden möchten, müssen Sie zuerst überprüfen, ob die erforderlichen Pakete für .NET Core installiert sind. In dieser Schnellstartanleitung wird der Prozess zum Hinzufügen der Pakete zu Ihrer .NET Core-Installation Schritt für Schritt beschrieben.

Am Ende dieses Prozesses haben Sie Ihrer .NET Core-Installation die Pakete hinzugefügt und Ihre erste Resource Graph-Abfrage ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Ein Azure-Dienstprinzipal, einschließlich _clientId_ und _clientSecret_. Falls Sie nicht über einen Dienstprinzipal für die Verwendung mit Resource Graph verfügen oder einen neuen erstellen möchten, helfen Ihnen die Informationen unter [Azure-Verwaltungsbibliotheken für die .NET-Authentifizierung](/dotnet/azure/sdk/authentication#mgmt-auth) weiter.
  Überspringen Sie den Schritt zum Installieren der .NET Core-Pakete, weil wir dies in den nächsten Schritten durchführen.

## <a name="create-the-resource-graph-project"></a>Erstellen des Resource Graph-Projekts

Erstellen Sie eine neue Konsolenanwendung, und installieren Sie die erforderlichen Pakete, um für .NET Core das Abfragen von Azure Resource Graph zu ermöglichen.

1. Überprüfen Sie, ob die aktuelle .NET Core-Version (mindestens **3.1.5**) installiert ist. Falls nicht, können Sie sie unter [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) herunterladen.

1. Initialisieren Sie eine neue .NET Core-Konsolenanwendung mit dem Namen „argQuery“:

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Ändern Sie die Verzeichnisse in den neuen Projektordner, und installieren Sie die erforderlichen Pakete für Azure Resource Graph:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Ersetzen Sie die Standarddatei `program.cs` durch den folgenden Code, und speichern Sie die aktualisierte Datei:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Kompilieren und veröffentlichen Sie die Konsolenanwendung `argQuery`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Ausführen Ihrer ersten Resource Graph-Abfrage

Nachdem die .NET Core-Konsolenanwendung kompiliert und veröffentlicht wurde, können Sie eine einfache Resource Graph-Abfrage ausprobieren. Die Abfrage gibt die ersten fünf Azure-Ressourcen mit dem **Namen** und **Ressourcentyp** der einzelnen Ressourcen zurück.

Jeder Aufruf von `argQuery` enthält Variablen, die Sie durch Ihre eigenen Werte ersetzen müssen:

- Ersetzen Sie `{tenantId}` durch Ihre Mandanten-ID.
- `{clientId}`: Ersetzen Sie diese ID durch die Client-ID Ihres Dienstprinzipals.
- `{clientSecret}`: Ersetzen Sie dieses Geheimnis durch den geheimen Clientschlüssel Ihres Dienstprinzipals.
- Ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID.

1. Ändern Sie die Verzeichnisse in den `{run-folder}`, den Sie mit dem vorherigen `dotnet publish`-Befehl definiert haben.

1. Führen Sie Ihre erste Azure Resource Graph-Abfrage mit der kompilierten .NET Core-Konsolenanwendung aus:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Da dieses Abfragebeispiel keinen Sortierungsmodifizierer wie z.B. `order by` umfasst, ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen.

1. Ändern Sie den letzten Parameter in `argQuery.exe`, und ändern Sie die Abfrage so, dass die Sortierung (`order by`) nach der Eigenschaft **Name** erfolgt:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Genau wie bei der ersten Abfrage ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen. Die Reihenfolge der Abfragebefehle ist wichtig. In diesem Beispiel kommt `order by` nach `limit`. Durch diese Befehlsreihenfolge werden die Abfrageergebnisse zuerst eingeschränkt und dann sortiert.

1. Ändern Sie den letzten Parameter in `argQuery.exe`, und ändern Sie die Abfrage so, dass zuerst die Sortierung (`order by`) nach der Eigenschaft **Name** und dann die Begrenzung (`limit`) auf die ersten fünf Ergebnisse erfolgt:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Wenn die letzte Abfrage mehrmals ausgeführt wird und in Ihrer Umgebung keine Änderungen vorgenommenen werden, sind die zurückgegebenen Ergebnisse konsistent und nach der Eigenschaft **Name** sortiert, aber immer noch auf die ersten fünf Ergebnisse begrenzt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie die .NET Core-Konsolenanwendung und die installierten Pakete entfernen möchten, können Sie dies erreichen, indem Sie den Projektordner `argQuery` löschen.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine .NET Core-Konsolenanwendung mit den erforderlichen Resource Graph-Paketen erstellt und Ihre erste Abfrage ausgeführt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).