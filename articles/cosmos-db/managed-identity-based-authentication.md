---
title: 'Gewusst wie: Verwenden der systemseitig zugewiesenen verwalteten Identität für den Zugriff auf Azure Cosmos DB'
description: Hier erfahren Sie, wie Sie eine vom System zugewiesene verwaltete Azure Active Directory (Azure AD)-Identität (verwaltete Dienstidentität) für den Zugriff auf Schlüssel aus Azure Cosmos DB konfigurieren.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: cfef6ce0fb38f074f854d5ceb77677843e44b91b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345728"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Verwenden von systemseitig zugewiesenen verwalteten Identitäten für den Zugriff auf Azure Cosmos DB-Daten
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel richten Sie eine *robuste, schlüsselrotationsunabhängige* Lösung für den Zugriff auf Azure Cosmos DB-Schlüssel durch Verwendung [verwalteter Identitäten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) ein. Im Beispiel in diesem Artikel wird Azure Functions verwendet, aber Sie können jeden beliebigen Dienst verwenden, der verwaltete Identitäten unterstützt. 

Sie erfahren, wie Sie eine Funktions-App erstellen, die auf Azure Cosmos DB-Daten zugreifen kann, ohne Azure Cosmos DB-Schlüssel kopieren zu müssen. Die Funktions-App wird jede Minute aktiviert und zeichnet die aktuelle Temperatur eines Aquariums auf. Informationen zum Einrichten einer per Timer ausgelösten Funktions-App finden Sie im Artikel [Erstellen einer Funktion in Azure, die von einem Timer ausgelöst wird](../azure-functions/functions-create-scheduled-function.md).

Zur Vereinfachung des Szenarios wurde die Einstellung [Gültigkeitsdauer](./time-to-live.md) bereits konfiguriert, um ältere Temperaturdokumente zu bereinigen. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Zuweisen einer vom System zugewiesenen verwalteten Identität zu einer Funktions-App

In diesem Schritt weisen Sie Ihrer Funktions-App eine vom System zugewiesene verwaltete Identität zu.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Bereich **Azure-Funktion**, und wechseln Sie zu Ihrer Funktions-App. 

1. Öffnen Sie die Registerkarte **Plattformfeatures** > **Identität**: 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Screenshot von Plattformfeatures und Identitätsoptionen für die Funktions-App.":::

1. Legen Sie auf der Registerkarte **Identität** den **Status** der Systemidentität auf **Ein** fest, und wählen Sie **Speichern** aus. Der Bereich **Identität** sollte so aussehen:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Screenshot, auf dem der „Status“ der Systemidentität auf „Ein“ festgelegt ist.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Gewähren des Zugriffs auf Ihr Azure Cosmos-Konto

In diesem Schritt weisen Sie der vom System zugewiesenen verwalteten Identität der Funktions-App eine Rolle zu. Azure Cosmos DB umfasst mehrere integrierte Rollen, die Sie der verwalteten Identität zuweisen können. Im Rahmen dieser Lösung verwenden Sie die beiden folgenden Rollen:

|Integrierte Rolle  |BESCHREIBUNG  |
|---------|---------|
|[Mitwirkender von DocumentDB-Konto](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kann Azure Cosmos DB-Konten verwalten. Ermöglicht das Abrufen von Lese-/Schreibschlüsseln. |
|[Cosmos DB-Rolle „Kontoleser“](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kann Azure Cosmos DB-Kontodaten lesen. Ermöglicht das Abrufen von Leseschlüsseln. |

> [!IMPORTANT]
> Die Unterstützung für rollenbasierte Zugriffssteuerung in Azure Cosmos DB gilt nur für Vorgänge auf Steuerungsebene. Vorgänge auf Datenebene werden über Primärschlüssel oder Ressourcentoken abgesichert. Weitere Informationen finden Sie im Artikel [Sicherer Zugriff auf Daten](secure-access-to-data.md).

> [!TIP] 
> Weisen Sie beim Zuweisen von Rollen nur den erforderlichen Zugriff zu. Wenn Ihr Dienst Daten nur lesen muss, weisen Sie der verwalteten Identität die Rolle **Cosmos DB-Kontoleser** zu. Weitere Informationen zur Bedeutung des Zugriffs mit geringsten Rechten finden Sie im Artikel [Senken der Gefährdung privilegierter Konten](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts).

In diesem Szenario liest die Funktions-App die Temperatur des Aquariums und schreibt diese Daten dann in Azure Cosmos DB in einen Container. Weil die Funktions-App die Daten schreiben muss, müssen Sie die Rolle **DocumentDB-Kontomitwirkender** zuweisen. 

### <a name="assign-the-role-using-azure-portal"></a>Zuweisen der Rolle über das Azure-Portal

1. Melden Sie sich beim Azure-Portal an, und wechseln Sie zu Ihrem Azure Cosmos DB-Konto. Öffnen Sie den Bereich **Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen**:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Screenshot des Bereichs „Zugriffssteuerung“ und der Registerkarte „Rollenzuweisungen“.":::

1. Wählen Sie **+Hinzufügen** > **Rollenzuweisung hinzufügen** aus.

1. Rechts wird der Bereich **Rollenzuweisung hinzufügen** geöffnet:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Screenshot des Bereichs „Rollenzuweisung hinzufügen“.":::

   * **Rolle**: Wählen Sie **DocumentDB-Kontomitwirkender** aus.
   * **Zugriff zuweisen zu**: Wählen Sie im Unterabschnitt **Systemseitig zugewiesene verwaltete Identität** die Option **Funktions-App** aus.
   * **Select**: Im Bereich werden alle Funktions-Apps in Ihrem Abonnement eingetragen, die über eine **verwaltete Systemidentität** verfügen. Wählen Sie in diesem Fall die Funktions-App **FishTankTemperatureService** aus: 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Screenshot des Bereichs „Rollenzuweisung hinzufügen“ mit Beispielen.":::

1. Nachdem Sie Ihre Funktions-App ausgewählt haben, wählen Sie **Speichern** aus.

### <a name="assign-the-role-using-azure-cli"></a>Zuweisen der Rolle über die Azure CLI

Öffnen Sie Azure Cloud Shell, und führen Sie die folgenden Befehle aus, um die Rolle mithilfe der Azure CLI zuzuweisen:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programmgesteuerter Zugriff auf die Azure Cosmos DB-Schlüssel

Nun haben Sie eine Funktions-App, die in den Azure Cosmos DB-Berechtigungen über eine vom System zugewiesene verwaltete Identität mit der Rolle **DocumentDB-Kontomitwirkender** verfügt. Der folgende Funktions-App-Code ruft die Azure Cosmos DB-Schlüssel ab, erstellt ein CosmosClient-Objekt, ruft die Temperatur des Aquariums ab und speichert sie dann in Azure Cosmos DB.

In diesem Beispiel wird die [List Keys-API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) verwendet, um auf die Azure Cosmos DB Kontoschlüssel zuzugreifen.

> [!IMPORTANT] 
> Wenn Sie die Rolle [„Cosmos DB-Kontoleser“ zuweisen](#grant-access-to-your-azure-cosmos-account) möchten, müssen Sie die [schreibgeschützte List Keys-API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys) verwenden. Damit werden nur die schreibgeschützten Schlüssel aufgefüllt.

Die List Keys-API gibt das `DatabaseAccountListKeysResult`-Objekt zurück. Dieser Typ ist in den C# Bibliotheken nicht definiert. Im folgenden Codebeispiel wird die Implementierung dieser Klasse veranschaulicht:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

Im Beispiel wird außerdem ein einfaches Dokument mit dem Namen „TemperatureRecord“ verwendet, das folgendermaßen definiert ist:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Sie verwenden die Bibliothek [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) zum Abrufen des vom System zugewiesenen verwalteten Identitätstokens. Weitere Möglichkeiten zum Abrufen des Tokens und weitere Informationen zur Bibliothek `Microsoft.Azure.Service.AppAuthentication` finden Sie im Artikel [Dienst-zu-Dienst-Authentifizierung](../key-vault/general/service-to-service-authentication.md).


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Jetzt können Sie [Ihre Funktions-App bereitstellen](../azure-functions/create-first-function-vs-code-csharp.md).

## <a name="next-steps"></a>Nächste Schritte

* [Zertifikatbasierte Authentifizierung mit Azure Cosmos DB und Azure Active Directory](certificate-based-authentication.md)
* [Sichern von Azure Cosmos DB-Schlüsseln mit Azure Key Vault](access-secrets-from-keyvault.md)
* [Sicherheitsbaseline für Azure Cosmos DB](security-baseline.md)