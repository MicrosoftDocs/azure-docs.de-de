---
title: Verwalten von Kontoressourcen mit der Clientbibliothek für .NET – Azure Batch | Microsoft-Dokumentation
description: Erstellen, löschen und ändern Sie Azure-Batch-Kontoressourcen mit der Batch Management .NET-Bibliothek.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 301a3f9a500c41cf13dfa071d3526d2128b5e131
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775133"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Verwalten von Batch-Konten und -Kontingenten mit der Batch Management-Clientbibliothek für .NET

> [!div class="op_single_selector"]
> * [Azure-Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Sie können den Wartungsaufwand in Ihren Azure Batch-Anwendungen verringern, indem Sie die [Batch Management .NET][api_mgmt_net]-Bibliothek verwenden, um das Erstellen des Batch-Kontos, das Löschen, die Schlüsselverwaltung und die Kontingentermittlung zu automatisieren.

* **Erstellen und Löschen von Batch-Konten** in jeder Region. Wenn Sie als unabhängiger Softwareanbieter (ISV) beispielsweise Ihren Kunden einen Dienst anbieten, bei dem jedem ein separates Batch-Konto zu Abrechnungszwecken zugewiesen wird, können Sie dem Kundenportal Funktionen zum Erstellen und Löschen von Konten hinzufügen.
* **Programmgesteuertes Abrufen und erneutes Erstellen von Kontenschlüsseln** für alle Batch-Konten. Dies erleichtert Ihnen die Einhaltung von Sicherheitsrichtlinien, mit denen ein regelmäßiges Rollover oder der Ablauf von Kontoschlüsseln erzwungen wird. Bei mehreren Batch-Konten in verschiedenen Azure-Regionen, erhöht die Automatisierung dieses Rolloverprozesses die Lösungseffizienz.
* **Überprüfen von Kontokontingenten** und Beseitigen der Mutmaßungen des Trial-and-Error-Prinzips beim Festlegen der Einschränkungen für die jeweiligen Batch-Konten. Durch Überprüfen der Kontokontingente vor dem Starten von Aufträgen, Erstellen von Pools oder Hinzufügen von Computeknoten können Sie proaktiv anpassen, wo und wann diese Computeressourcen erstellt werden. Sie können bestimmen, für welche Konten die Kontingente erhöht werden müssen, bevor zusätzliche Ressourcen in diesen Konten zugewiesen werden.
* **Kombinieren Sie Funktionen von anderen Azure-Diensten**, um eine Verwaltung mit optimalem Funktionsumfang zu ermöglichen. Nutzen Sie dafür etwa Batch Management .NET, [Azure Active Directory][aad_about] und [Azure Resource Manager][resman_overview] gemeinsam in derselben Anwendung. Wenn Sie diese Funktionen und ihre APIs nutzen, können Sie ein reibungsloses Authentifizierungserlebnis, Möglichkeiten zum Erstellen und Löschen von Ressourcengruppen und die oben beschriebenen Funktionen für eine End-to-End-Verwaltungslösung bereitstellen.

> [!NOTE]
> Auch wenn sich dieser Artikel auf die programmgesteuerte Verwaltung der Batch-Konten, -Schlüssel und -Kontingente konzentriert, können Sie viele dieser Aktivitäten mithilfe des [Azure-Portals][azure_portal] ausführen. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Azure Batch-Kontos im Azure-Portal](batch-account-create-portal.md) und [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Erstellen und Löschen von Batch-Konten
Wie bereits erwähnt, ist eine der Hauptfunktionen der Batch Management-API das Erstellen und Löschen von Batch-Konten innerhalb einer Azure-Region. Zu diesem Zweck verwenden Sie [BatchManagementClient.Account.CreateAsync][net_create] und [DeleteAsync][net_delete] oder ihre synchronen Gegenstücke.

Mit dem folgenden Codeausschnitt wird ein Konto erstellt, und das neu erstellte Konto wird aus dem Batch-Dienst abgerufen und anschließend gelöscht. In diesem und anderen Codeausschnitten in diesem Artikel ist `batchManagementClient` eine vollständig initialisierte Instanz von [BatchManagementClient][net_mgmt_client].

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
> Anwendungen, die die Batch Management .NET-Bibliothek und die zugehörige „BatchManagementClient“-Klasse verwenden, benötigen einen Zugriff als **Dienstadministrator** oder **Co-Administrator** auf das Abonnement, das Besitzer des zu verwaltenden Batch-Kontos ist. Weitere Informationen finden Sie im Abschnitt zu Azure Active Directory und im [AccountManagement][acct_mgmt_sample]-Codebeispiel.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Programmgesteuertes Abrufen und erneutes Erstellen von Kontenschlüsseln
Rufen Sie primäre und sekundäre Kontoschlüssel aus jedem Batch-Konto innerhalb Ihres Abonnements mithilfe von [ListKeysAsync][net_list_keys] ab. Sie können diese Schlüssel mit [RegenerateKeyAsync][net_regenerate_keys] erneut generieren.

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
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
> Sie können einen optimierten Verbindungsworkflow  für Ihre Verwaltungsanwendungen erstellen. Rufen Sie zuerst mithilfe von [ListKeysAsync][net_list_keys] einen Kontoschlüssel für das zu verwaltende Batch-Konto ab. Verwenden Sie diesen Schlüssel anschließend beim Initialisieren der [BatchSharedKeyCredentials][net_sharedkeycred]-Klasse aus der Batch .NET-Bibliothek. Diese Klasse wird beim Initialisieren von [BatchClient][net_batch_client] verwendet.
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Überprüfen des Azure-Abonnements und der Batch-Kontokontingente
Azure-Abonnements und die einzelnen Azure-Dienste wie z. B. Batch verfügen über Standardkontingente, die die Anzahl von bestimmten darin enthaltenen Entitäten begrenzen. Die Standardkontingente für Azure-Abonnements finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md). Die Standardkontingente für den Batch-Dienst finden Sie unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md). Mithilfe der Batch Management .NET-Bibliothek können Sie diese Kontingente in Ihren Anwendungen überprüfen. Dadurch können Sie Zuordnungsentscheidungen treffen, bevor Sie Konten oder Computeressourcen hinzufügen (z. B. Pools und Computeknoten).

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Überprüfen des Azure-Abonnements für Batch-Kontokontingente
Vor dem Erstellen eines Batch-Kontos in einer Region können Sie Ihr Azure-Abonnement überprüfen, um festzustellen, ob Sie ein Konto in der betreffenden Region hinzufügen können.

Im folgenden Codeausschnitt verwenden wir zunächst [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts], um eine Auflistung aller Batch-Konten innerhalb eines Abonnements abzurufen. Nach dem Abrufen dieser Auflistung bestimmen wir, wie viele Konten sich in der Zielregion befinden. Anschließend rufen wir mithilfe von [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] das Batch-Kontokontingent ab und bestimmen, wie viele Konten (falls möglich) in dieser Region erstellt werden können.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Im obigen Codeausschnitt ist `creds` eine Instanz von [TokenCloudCredentials][azure_tokencreds]. Ein Beispiel zum Erstellen dieses Objekts finden Sie im [AccountManagement][acct_mgmt_sample]-Codebeispiel auf GitHub.

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
> Solange Standardkontingente für Azure-Abonnements und -Dienste vorliegen, können viele dieser Grenzen durch eine Anforderung im [Azure-Portal][azure_portal] erhöht werden. Anleitungen zum Erhöhen der Batch-Kontokontingente finden Sie z.B. unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md).
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Verwenden von Azure AD mit Batch Management .NET

Die Batch Management .NET-Bibliothek ist ein Azure-Ressourcenanbieterclient, der zusammen mit [Azure Resource Manager][resman_overview] zum programmgesteuerten Verwalten von Kontoressourcen verwendet wird. Azure AD ist erforderlich, um Anforderungen über beliebige Azure-Ressourcenanbieterclients, einschließlich der Batch Management .NET-Bibliothek, und über [Azure Resource Manager][resman_overview] zu authentifizieren. Informationen zum Verwenden von Azure AD mit der Batch Management .NET-Bibliothek finden Sie unter [Verwenden von Azure Active Directory zum Authentifizieren von Batch-Lösungen](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Beispielprojekt auf GitHub

Sehen Sie sich das Beispielprojekt [AccountManagement][acct_mgmt_sample] auf GitHub an, um Batch Management .NET in Aktion zu erleben. Die AccountManagement-Beispielanwendung veranschaulicht die folgenden Vorgänge:

1. Erwerben eines Sicherheitstokens von Azure Active AD mithilfe von [ADAL][aad_adal]. Wenn der Benutzer noch nicht angemeldet ist, wird er aufgefordert, die Azure-Anmeldeinformationen einzugeben.
2. Erstellen eines [SubscriptionClient][resman_subclient]-Elements mit einem von Azure AD abgerufenen Sicherheitstoken, um Azure nach einer Liste mit Abonnements abzufragen, die dem Konto zugeordnet sind. Der Benutzer kann ein Abonnement in der Liste auswählen, wenn diese mehrere Abonnements enthält.
3. Abrufen der Anmeldeinformationen, die dem ausgewählten Abonnement zugeordnet sind
4. Erstellen eines [ResourceManagementClient][resman_client]-Objekts mithilfe der Anmeldeinformationen
5. Erstellen einer Ressourcengruppe mit einem [ResourceManagementClient][resman_client]-Objekt
6. Verwenden Sie ein [BatchManagementClient][net_mgmt_client]-Objekt, um mehrere Batch-Kontovorgänge durchzuführen:
   * Erstellen eines Batch-Kontos in der neuen Ressourcengruppe.
   * Abrufen des neu erstellten Kontos aus dem Batch-Dienst.
   * Drucken der Kontoschlüssel für das neue Konto.
   * Erneutes Generieren eines neuen Primärschlüssels für das Konto.
   * Ausgeben der Kontingentinformationen für das Konto.
   * Ausgeben der Kontingentinformationen für das Abonnement.
   * Ausgeben aller Konten innerhalb des Abonnements.
   * Löschen des neu erstellten Kontos.
7. Löschen Sie die Ressourcengruppe.

Vor dem Löschen des neu erstellten Batch-Kontos und der Ressourcengruppe können Sie beides im [Azure-Portal][azure_portal] anzeigen:

Um die Beispielanwendung erfolgreich ausführen zu können, müssen Sie sie zunächst bei Ihrem Azure AD-Mandanten im Azure-Portal registrieren und der Azure Resource Manager-API Berechtigungen erteilen. Führen Sie die Schritte in [Authentifizieren von Batch Management-Lösungen mit Active Directory](batch-aad-auth-management.md) aus.


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Was ist Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Authentifizierungsszenarien für Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrieren von Anwendungen in Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
