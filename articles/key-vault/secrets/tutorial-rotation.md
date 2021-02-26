---
title: 'Tutorial: Rotation für Ressourcen mit einem in Azure Key Vault gespeicherten Satz mit Authentifizierungsanmeldeinformationen'
description: In diesem Tutorial erfahren Sie, wie Sie die Rotation eines Geheimnisses für Ressourcen automatisieren, bei denen ein Satz mit Anmeldeinformationen für die Authentifizierung verwendet wird.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: 526c3d2d85a3f2877f82b3b764f395c51f7c05c0
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805229"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatisieren der Rotation eines Geheimnisses für Ressourcen mit einem Satz mit Anmeldeinformationen für die Authentifizierung

Die beste Möglichkeit zur Authentifizierung bei Azure-Diensten ist die Verwendung einer [verwalteten Identität](../general/authentication.md). Es gibt jedoch einige Szenarien, in denen dies nicht möglich ist. In diesen Fällen werden Zugriffsschlüssel oder Geheimnisse verwendet. Sie sollten Zugriffsschlüssel und Geheimnisse regelmäßig rotieren.

In diesem Tutorial wird gezeigt, wie Sie die regelmäßige Rotation von Geheimnissen für Datenbanken und Dienste automatisieren, bei denen ein Satz mit Anmeldeinformationen für die Authentifizierung verwendet wird. Genauer gesagt werden in diesem Tutorial in Azure Key Vault gespeicherte SQL Server-Kennwörter mithilfe einer Funktion rotiert, die durch eine Azure Event Grid-Benachrichtigung ausgelöst wird:


:::image type="content" source="../media/rotate-1.png" alt-text="Diagramm der Rotationslösung":::

1. 30 Tage vor dem Ablaufdatum eines Geheimnisses veröffentlicht Key Vault das Ereignis „Läuft demnächst ab“ in Event Grid.
1. Event Grid überprüft die Ereignisabonnements und ruft mit HTTP POST den Funktions-App-Endpunkt auf, der dieses Ereignis abonniert hat.
1. Die Funktions-App empfängt die Geheimnisinformationen, generiert ein neues zufälliges Kennwort und erstellt für das Geheimnis eine neue Version mit dem neuen Kennwort in Key Vault.
1. Die Funktions-App aktualisiert SQL Server mit dem neuen Kennwort.

> [!NOTE]
> Zwischen den Schritten 3 und 4 kann es zu einer Verzögerung kommen. Während dieser Zeit kann das Geheimnis in Key Vault nicht bei SQL Server authentifiziert werden. Tritt in einem der Schritte ein Fehler auf, wiederholt Event Grid den Vorgang zwei Stunden lang.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Azure-Schlüsseltresor
* SQL Server

Sie können den unten angegebenen Bereitstellungslink verwenden, falls Sie nicht über vorhandene Key Vault- und SQL Server-Instanzen verfügen:

[![Bild einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus. Geben Sie einen Namen für die Gruppe ein. In diesem Tutorial wird **akvrotation** verwendet.
1. Geben Sie unter **SQL-Administratoranmeldung** den Anmeldenamen des SQL-Administrators ein. 
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**

:::image type="content" source="../media/rotate-2.png" alt-text="Erstellen einer Ressourcengruppe":::

Sie verfügen jetzt über eine Key Vault- und eine SQL Server-Instanz. Sie können dieses Setup in der Azure-Befehlszeilenschnittstelle überprüfen, indem Sie den folgenden Befehl ausführen:

```azurecli
az resource list -o table -g akvrotation
```

Das Ergebnis sieht in etwa wie die folgende Ausgabe aus:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Erstellen und Bereitstellen einer Funktion für die SQL Server-Kennwortrotation
> [!IMPORTANT]
> Für die folgende Vorlage müssen sich Key Vault, SQL Server und Azure-Funktion in der gleichen Ressourcengruppe befinden.

Erstellen Sie als Nächstes zusätzlich zu den anderen erforderlichen Komponenten eine Funktions-App mit einer systemseitig verwalteten Identität, und stellen Sie Funktionen für die Rotation von SQL Server-Kennwörtern bereit.

Eine Funktions-App benötigt folgende Komponenten:
- Einen Azure App Service-Plan
- Eine Funktions-App mit Funktionen für die SQL-Kennwortrotation mit Ereignisauslöser und HTTP-Trigger 
- Ein Speicherkonto, das für die Triggerverwaltung der Funktions-App benötigt wird
- Eine Zugriffsrichtlinie für die Identität der Funktions-App für den Zugriff auf Geheimnisse in Key Vault
- Event Grid-Ereignisabonnement für das Ereignis **SecretNearExpiry**

1. Wählen Sie den Link zur Bereitstellung der Vorlage in Azure aus: 

   [![Bild einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Wählen Sie in der Liste **Ressourcengruppe** die Option **akvrotation** aus.
1. Geben Sie unter **SQL Server-Name** den Namen der SQL Server-Instanz mit dem zu rotierenden Kennwort ein.
1. Geben Sie unter **Schlüsseltresorname** den Namen des Schlüsseltresors ein.
1. Geben Sie unter **Name der Funktions-App** den Namen der Funktions-App ein.
1. Geben Sie unter **Name des Geheimnisses** den Namen des Geheimnisses an, unter dem das Kennwort gespeichert wird.
1. Geben Sie unter **Repository-URL** den GitHub-Speicherort des Funktionscodes an ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** ).
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

:::image type="content" source="../media/rotate-3.png" alt-text="Auswählen von „Überprüfen + erstellen“":::
  

Nachdem Sie die obigen Schritte ausgeführt haben, verfügen Sie über ein Speicherkonto, eine Serverfarm und eine Funktions-App. Sie können dieses Setup in der Azure-Befehlszeilenschnittstelle überprüfen, indem Sie den folgenden Befehl ausführen:

```azurecli
az resource list -o table -g akvrotation
```

Das Ergebnis sieht in etwa wie die folgende Ausgabe aus:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Informationen zum Erstellen einer Funktions-App sowie zum Zugreifen auf Key Vault mit einer verwalteten Identität finden Sie unter [Erstellen einer Funktions-App im Azure-Portal](../../azure-functions/functions-create-function-app-portal.md), [Verwenden verwalteter Identitäten für App Service und Azure Functions](../../app-service/overview-managed-identity.md) und [Zuweisen einer Key Vault-Zugriffsrichtlinie im Azure-Portal](../general/assign-access-policy-portal.md).

### <a name="rotation-function"></a>Rotationsfunktion
Die im vorherigen Schritt bereitgestellte Funktion verwendet ein Ereignis, um die Rotation eines Geheimnisses durch Aktualisieren von Key Vault und SQL-Datenbank zu initiieren. 

#### <a name="function-trigger-event"></a>Funktionstriggerereignis

Diese Funktion liest Ereignisdaten und führt die Rotationslogik aus:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Logik für die Geheimnisrotation
Diese Rotationsmethode liest Datenbankinformationen aus dem Geheimnis, erstellt eine neue Version des Geheimnisses und aktualisiert die Datenbank mit dem neuen Geheimnis:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Den vollständigen Beispielcode finden Sie auf [GitHub](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Hinzufügen des Geheimnisses zu Key Vault
Erteilen Sie Benutzern in Ihrer Zugriffsrichtlinie Berechtigungen zum *Verwalten von Geheimnissen*:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Erstellen Sie ein neues Geheimnis mit Tags, das die SQL Server-Ressourcen-ID, den SQL Server-Anmeldenamen und die Gültigkeitsdauer für das Geheimnis in Tagen enthalten. Geben Sie den Namen des Geheimnisses und das erste Kennwort aus SQL-Datenbank (in unserem Beispiel „Simple123“) ein, und fügen Sie ein Ablaufdatum ein, das auf den morgigen Tag festgelegt ist

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Wenn Sie ein Geheimnis mit einem kurzen Ablaufdatum erstellen, wird innerhalb von 15 Minuten ein Ereignis vom Typ `SecretNearExpiry` veröffentlicht, wodurch wiederum die Funktion zum Rotieren des Geheimnisses ausgelöst wird.

## <a name="test-and-verify"></a>Testen und Überprüfen

Vergewissern Sie sich unter **Key Vault** > **Geheimnisse**, dass das Geheimnis rotiert wurde:

:::image type="content" source="../media/rotate-8.png" alt-text="Screenshot: Zugriff auf „Key Vault“ > „Geheimnisse“":::

Öffnen Sie das Geheimnis **sqlPassword**, und überprüfen Sie die ursprüngliche sowie die rotierte Version:

:::image type="content" source="../media/rotate-9.png" alt-text="Navigieren zu „Geheimnisse“":::

### <a name="create-a-web-app"></a>Erstellen einer Web-App

Erstellen Sie zum Überprüfen der SQL-Anmeldeinformationen eine Web-App. Diese Web-App ruft das Geheimnis aus Key Vault ab, extrahiert SQL-Datenbank- und Anmeldeinformationen aus dem Geheimnis und testet die Verbindung mit SQL Server.

Die Web-App benötigt folgende Komponenten:
- Eine Web-App mit systemseitig verwalteter Identität
- Eine Zugriffsrichtlinie für den Zugriff auf Geheimnisse in Key Vault mithilfe der verwalteten Identität der Web-App

1. Wählen Sie den Link zur Bereitstellung der Vorlage in Azure aus: 

   [![Bild einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Wählen Sie die Ressourcengruppe **akvrotation** aus.
1. Geben Sie unter **SQL Server-Name** den Namen der SQL Server-Instanz mit dem zu rotierenden Kennwort ein.
1. Geben Sie unter **Schlüsseltresorname** den Namen des Schlüsseltresors ein.
1. Geben Sie unter **Name des Geheimnisses** den Namen des Geheimnisses an, unter dem das Kennwort gespeichert ist.
1. Geben Sie unter **Repository-URL** den GitHub-Speicherort des Web-App-Codes an ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** ).
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.


### <a name="open-the-web-app"></a>Öffnen der Web-App

Wechseln Sie zur URL der bereitgestellten Anwendung:
 
'https://akvrotation-app.azurewebsites.net/'

Wenn die Anwendung im Browser geöffnet wird, wird für den Wert **Generated Secret Value** (Geheimniswert generiert) und **Database Connected** (Datenbank verbunden) der Wert *true* angezeigt.

## <a name="learn-more"></a>Weitere Informationen

- Tutorial: [Automatisieren der Rotation eines Geheimnisses für Ressourcen mit zwei Sätzen mit Anmeldeinformationen für die Authentifizierung](tutorial-rotation-dual.md)
- Übersicht: [Überwachen von Key Vault mit Azure Event Grid](../general/event-grid-overview.md)
- Gewusst wie: [Verwenden von Logic Apps zum Empfangen einer E-Mail bei Statusänderungen von Key Vault-Geheimnissen](../general/event-grid-logicapps.md)
- [Azure Event Grid-Ereignisschema für Azure Key Vault](../../event-grid/event-schema-key-vault.md)
