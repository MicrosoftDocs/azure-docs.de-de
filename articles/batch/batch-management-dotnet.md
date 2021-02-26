---
title: Verwalten von Kontoressourcen mit der Batch Management .NET-Bibliothek
description: Erstellen, löschen und ändern Sie Azure-Batch-Kontoressourcen mit der Batch Management .NET-Bibliothek.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896730"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Verwalten von Batch-Konten und -Kontingenten mit der Batch Management-Clientbibliothek für .NET

Sie können den Wartungsaufwand in Ihren Azure Batch-Anwendungen verringern, indem Sie mithilfe der [Batch Management .NET](/dotnet/api/overview/azure/batch)-Bibliothek das Erstellen des Batch-Kontos, das Löschen, die Schlüsselverwaltung und die Kontingentermittlung automatisieren.

- **Erstellen und Löschen von Batch-Konten** in jeder Region. Wenn Sie als unabhängiger Softwareanbieter (ISV) beispielsweise Ihren Kunden einen Dienst anbieten, bei dem jedem ein separates Batch-Konto zu Abrechnungszwecken zugewiesen wird, können Sie dem Kundenportal Funktionen zum Erstellen und Löschen von Konten hinzufügen.
- **Programmgesteuertes Abrufen und erneutes Erstellen von Kontenschlüsseln** für alle Batch-Konten. Dies erleichtert Ihnen die Einhaltung von Sicherheitsrichtlinien, mit denen ein regelmäßiges Rollover oder der Ablauf von Kontoschlüsseln erzwungen wird. Bei mehreren Batch-Konten in verschiedenen Azure-Regionen, erhöht die Automatisierung dieses Rolloverprozesses die Lösungseffizienz.
- **Überprüfen von Kontokontingenten** und Beseitigen der Mutmaßungen des Trial-and-Error-Prinzips beim Festlegen der Einschränkungen für die jeweiligen Batch-Konten. Durch Überprüfen der Kontokontingente vor dem Starten von Aufträgen, Erstellen von Pools oder Hinzufügen von Computeknoten können Sie proaktiv anpassen, wo und wann diese Computeressourcen erstellt werden. Sie können bestimmen, für welche Konten die Kontingente erhöht werden müssen, bevor zusätzliche Ressourcen in diesen Konten zugewiesen werden.
- **Kombinieren Sie Funktionen von anderen Azure-Diensten**, um eine Verwaltung mit optimalem Funktionsumfang zu ermöglichen. Nutzen Sie dafür Batch Management .NET, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) und [Azure Resource Manager](../azure-resource-manager/management/overview.md) gemeinsam in derselben Anwendung. Wenn Sie diese Funktionen und ihre APIs nutzen, können Sie ein reibungsloses Authentifizierungserlebnis, Möglichkeiten zum Erstellen und Löschen von Ressourcengruppen und die oben beschriebenen Funktionen für eine End-to-End-Verwaltungslösung bereitstellen.

> [!NOTE]
> Auch wenn sich dieser Artikel auf die programmgesteuerte Verwaltung der Batch-Konten, -Schlüssel und -Kontingente konzentriert, können Sie viele dieser Aktivitäten auch mithilfe des [Azure-Portals](batch-account-create-portal.md) ausführen.

## <a name="create-and-delete-batch-accounts"></a>Erstellen und Löschen von Batch-Konten

Eine der Hauptfunktionen der Batch Management-API ist das Erstellen und Löschen von [Batch-Konten](accounts.md) innerhalb einer Azure-Region. Zu diesem Zweck verwenden Sie [BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) und [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync) oder ihre synchronen Gegenstücke.

Mit dem folgenden Codeausschnitt wird ein Konto erstellt, und das neu erstellte Konto wird aus dem Batch-Dienst abgerufen und anschließend gelöscht. In diesem und den anderen Codeausschnitten in diesem Artikel ist `batchManagementClient` eine vollständig initialisierte Instanz von [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Anwendungen, die die Batch Management .NET-Bibliothek und die zugehörige „BatchManagementClient“-Klasse verwenden, benötigen einen Zugriff als Dienstadministrator oder Co-Administrator auf das Abonnement, das Besitzer des zu verwaltenden Batch-Kontos ist. Weitere Informationen finden Sie im Abschnitt zu Azure Active Directory und im [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement)-Codebeispiel.

## <a name="retrieve-and-regenerate-account-keys"></a>Programmgesteuertes Abrufen und erneutes Erstellen von Kontenschlüsseln

Rufen Sie primäre und sekundäre Kontoschlüssel aus einem beliebigen Batch-Konto innerhalb Ihres Abonnements mithilfe von [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync) ab. Sie können diese Schlüssel mit [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync) erneut generieren.

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Sie können einen optimierten Verbindungsworkflow  für Ihre Verwaltungsanwendungen erstellen. Rufen Sie zuerst mithilfe von [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync) einen Kontoschlüssel für das zu verwaltende Batch-Konto ab. Verwenden Sie diesen Schlüssel anschließend beim Initialisieren der [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials)-Klasse aus der Batch .NET-Bibliothek. Diese Klasse wird beim Initialisieren von [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) verwendet.

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Überprüfen des Azure-Abonnements und der Batch-Kontokontingente

Azure-Abonnements und die einzelnen Azure-Dienste wie z. B. Batch verfügen über Standardkontingente, die die Anzahl von bestimmten darin enthaltenen Entitäten begrenzen. Die Standardkontingente für Azure-Abonnements finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md). Die Standardkontingente für den Batch-Dienst finden Sie unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md). Mithilfe der Batch Management .NET-Bibliothek können Sie diese Kontingente in Ihren Anwendungen überprüfen. Dadurch können Sie Zuordnungsentscheidungen treffen, bevor Sie Konten oder Computeressourcen hinzufügen (z. B. Pools und Computeknoten).

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Überprüfen des Azure-Abonnements für Batch-Kontokontingente

Vor dem Erstellen eines Batch-Kontos in einer Region können Sie Ihr Azure-Abonnement überprüfen, um festzustellen, ob Sie ein Konto in der betreffenden Region hinzufügen können.

Im folgenden Codeausschnitt verwenden wir zunächst **ListAsync**, um eine Sammlung aller Batch-Konten innerhalb eines Abonnements abzurufen. Nach dem Abrufen dieser Auflistung bestimmen wir, wie viele Konten sich in der Zielregion befinden. Anschließend rufen wir mithilfe von **GetQuotasAsync** das Batch-Kontokontingent ab und bestimmen, wie viele Konten (wenn überhaupt) in dieser Region erstellt werden können.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Im obigen Codeausschnitt ist `creds` eine Instanz von **TokenCredentials**. Ein Beispiel zum Erstellen dieses Objekts finden Sie im [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement)-Codebeispiel auf GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Überprüfen eines Batch-Kontos für Computeressourcenkontingente

Bevor Sie die Anzahl von Computeressourcen in Ihrer Batch-Lösung erhöhen, können Sie sich vergewissern, dass die Ressourcen, die Sie zuordnen möchten, nicht die Kontingente des Kontos überschreiten. Mit dem folgenden Codeausschnitt werden die Kontingentinformationen für das Batch-Konto `mybatchaccount` ausgegeben. In Ihrer eigenen Anwendung können Sie anhand solcher Informationen bestimmen, ob das Konto die zusätzlichen zu erstellenden Ressourcen überhaupt aufnehmen kann.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Solange Standardkontingente für Azure-Abonnements und -Dienste vorliegen, können viele dieser Grenzen durch [Anfordern einer Kontingenterhöhung im Azure-Portal](batch-quota-limit.md#increase-a-quota) heraufgesetzt werden.

## <a name="use-azure-ad-with-batch-management-net"></a>Verwenden von Azure AD mit Batch Management .NET

Die Batch Management .NET-Bibliothek ist ein Azure-Ressourcenanbieterclient, der zusammen mit [Azure Resource Manager](../azure-resource-manager/management/overview.md) zum programmgesteuerten Verwalten von Kontoressourcen verwendet wird. Azure AD ist erforderlich, um Anforderungen zu authentifizieren, die über Azure-Ressourcenanbieterclients wie z. B. die Batch Management .NET-Bibliothek und über Azure Resource Manager übermittelt werden. Informationen zum Verwenden von Azure AD mit der Batch Management .NET-Bibliothek finden Sie unter [Verwenden von Azure Active Directory zum Authentifizieren von Batch-Lösungen](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Beispielprojekt auf GitHub

Sehen Sie sich das Beispielprojekt [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) auf GitHub an, um Batch Management .NET in Aktion zu erleben. Die AccountManagement-Beispielanwendung veranschaulicht die folgenden Vorgänge:

1. Erwerben eines Sicherheitstokens von Azure AD mithilfe von [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Wenn der Benutzer noch nicht angemeldet ist, wird er aufgefordert, die Azure-Anmeldeinformationen einzugeben.
2. Erstellen eines [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient)-Elements mit dem von Azure AD abgerufenen Sicherheitstoken, um Azure nach einer Liste von Abonnements abzufragen, die dem Konto zugeordnet sind. Der Benutzer kann ein Abonnement in der Liste auswählen, wenn diese mehrere Abonnements enthält.
3. Abrufen der Anmeldeinformationen, die dem ausgewählten Abonnement zugeordnet sind
4. Erstellen eines [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient)-Objekts mithilfe der Anmeldeinformationen.
5. Erstellen einer Ressourcengruppe mithilfe eines [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient)-Objekts.
6. Verwenden Sie ein [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient)-Objekt, um mehrere Batch-Kontovorgänge durchzuführen:
   - Erstellen eines Batch-Kontos in der neuen Ressourcengruppe.
   - Abrufen des neu erstellten Kontos aus dem Batch-Dienst.
   - Drucken der Kontoschlüssel für das neue Konto.
   - Erneutes Generieren eines neuen Primärschlüssels für das Konto.
   - Ausgeben der Kontingentinformationen für das Konto.
   - Ausgeben der Kontingentinformationen für das Abonnement.
   - Ausgeben aller Konten innerhalb des Abonnements.
   - Löschen des neu erstellten Kontos.
7. Löschen Sie die Ressourcengruppe.

Um die Beispielanwendung erfolgreich ausführen zu können, müssen Sie sie zunächst bei Ihrem Azure AD-Mandanten im Azure-Portal registrieren und der Azure Resource Manager-API Berechtigungen erteilen. Führen Sie die Schritte in [Authentifizieren von Batch Management-Lösungen mit Active Directory](batch-aad-auth-management.md) aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Informieren Sie sich über die Grundlagen der Entwicklung einer Batch-fähigen Anwendung mit der [Batch-.NET-Clientbibliothek](quick-run-dotnet.md) oder mit [Python](quick-run-python.md). In diesen Schnellstarts werden Sie durch eine Beispielanwendung geführt, die den Azure Batch-Dienst zum Ausführen einer Workload auf mehreren Computeknoten verwendet und Azure Storage zum Bereitstellen und Abrufen von Workloaddateien nutzt.
