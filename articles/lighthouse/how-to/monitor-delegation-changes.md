---
title: Überwachen von Delegierungsänderungen in Ihrem Verwaltungsmandanten
description: Erfahren Sie, wie Sie Delegierungsaktivitäten von Kundenmandanten für Ihren Verwaltungsmandanten überwachen.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: 9fdf47df4ac37fec44cf53b565b7fe1411540793
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089415"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Überwachen von Delegierungsänderungen in Ihrem Verwaltungsmandanten

Als Dienstanbieter möchten Sie möglicherweise wissen, wenn Kundenabonnements oder Ressourcengruppen über [Azure Lighthouse](../overview.md) an Ihren Mandanten delegiert oder zuvor delegierte Ressourcen entfernt werden.

Im Verwaltungsmandanten werden Delegierungsaktivitäten auf Mandantenebene im [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/platform-logs-overview.md) erfasst. Diese protokollierten Aktivitäten umfassen alle hinzugefügten oder entfernten Delegierungen von allen Kundenmandanten.

In diesem Thema werden die erforderlichen Berechtigungen zum Überwachen von Delegierungsaktivitäten für Ihren Mandanten (übergreifend für alle Ihre Kunden) erläutert. Es enthält auch ein Beispielskript, das eine Methode zum Abfragen und zur Berichterstellung für diese Daten zeigt.

> [!IMPORTANT]
> Alle diese Schritte müssen in Ihrem Verwaltungsmandanten und nicht in einem Kundenmandanten ausgeführt werden.
>
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch können [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), denselben Prozess verwenden.

## <a name="enable-access-to-tenant-level-data"></a>Ermöglichen des Datenzugriffs auf Mandantenebene

Für den Zugriff auf Aktivitätsprotokolldaten auf Mandantenebene muss einem Konto die integrierte Azure-Rolle [Überwachungsleser](../../role-based-access-control/built-in-roles.md#monitoring-reader) im Stammbereich (/) zugewiesen werden. Diese Zuweisung muss von einem Benutzer durchgeführt werden, der über die globale Administratorrolle mit zusätzlichen erhöhten Zugriffsrechten verfügt.

### <a name="elevate-access-for-a-global-administrator-account"></a>Erhöhen der Zugriffsrechte für ein globales Administratorkonto

Zum Zuweisen einer Rolle im Stammbereich (/) benötigen Sie die globale Administratorrolle mit erhöhten Zugriffsrechten. Diese erhöhten Zugriffsrechte sollten nur dann hinzugefügt werden, wenn Sie die Rollenzuweisung vornehmen müssen, und anschließend wieder entfernt werden.

Ausführliche Anweisungen zum Hinzufügen und Entfernen der Erhöhung von Rechten finden Sie unter [Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen](../../role-based-access-control/elevate-access-global-admin.md).

Nachdem Sie Ihre Zugriffsrechte erhöht haben, weist Ihr Konto die Rolle „Benutzerzugriffsadministrator“ in Azure für den Stammbereich auf. Durch diese Rollenzuweisung können Sie alle Ressourcen anzeigen und den Zugriff in jeder Abonnement- oder Verwaltungsgruppe im Verzeichnis zuweisen sowie Rollenzuweisungen im Stammbereich vornehmen.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Zuweisen der Rolle „Überwachungsleser“ im Stammbereich

Nachdem Sie die Zugriffsrechte erhöht haben, können Sie einem Konto die entsprechenden Berechtigungen zuweisen, damit es Aktivitätsprotokolldaten auf Mandantenebene abfragen kann. Diesem Konto muss die integrierte Azure-Rolle [Überwachungsleser](../../role-based-access-control/built-in-roles.md#monitoring-reader) im Stammbereich Ihres Verwaltungsmandanten zugewiesen sein.

> [!IMPORTANT]
> Das Erteilen einer Rollenzuweisung im Stammbereich bedeutet, dass die gleichen Berechtigungen für jede Ressource im Mandanten gelten. Da dies einen sehr weitgefassten Zugriff ermöglicht, sollten Sie [diese Rolle möglicherweise einem Dienstprinzipalkonto zuweisen und dieses Konto für das Abfragen von Daten verwenden](#use-a-service-principal-account-to-query-the-activity-log). Sie können auch einzelnen Benutzern oder Benutzergruppen die Rolle „Überwachungsleser“ im Stammbereich zuweisen, damit sie die [Delegierungsinformationen direkt im Azure-Portal anzeigen](#view-delegation-changes-in-the-azure-portal) können. Beachten Sie, dass es sich hierbei um einen umfassenden Zugriff handelt, der auf eine möglichst geringe Anzahl von Benutzern begrenzt sein sollte.

Verwenden Sie eine der folgenden Methoden für die Stammbereichszuweisung.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Entfernen der erhöhten Zugriffsrechte für das globale Administratorkonto

Nachdem Sie dem gewünschten Konto die Rolle „Überwachungsleser“ im Stammbereich zugewiesen haben, müssen Sie die [erhöhten Zugriffsrechte für das globale Administratorkonto entfernen](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access), da diese Zugriffsebene nicht mehr benötigt wird.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Anzeigen von Delegierungsänderungen im Azure-Portal

Benutzer, denen die Rolle „Überwachungsleser“ im Stammbereich zugewiesen wurde, können Delegierungsänderungen direkt im Azure-Portal anzeigen.

1. Navigieren Sie zur Seite **Meine Kunden**, und wählen Sie dann im linken Navigationsmenü die Option **Aktivitätsprotokoll** aus.
1. Stellen Sie sicher, dass **Verzeichnisaktivität** im Filter am oberen Rand des Bildschirms ausgewählt ist.

Eine Liste der Delegierungsänderungen wird angezeigt. Sie können **Spalten bearbeiten** auswählen, um die Werte für **Status**, **Ereigniskategorie**, **Zeit**, **Zeitstempel**, **Abonnement**, **Ereignis initiiert von**, **Ressourcengruppe**, **Ressourcentyp** und **Ressource** ein- oder auszublenden.

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Screenshot von Delegierungsänderungen im Azure-Portal":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Verwenden eines Dienstprinzipalkontos zum Abfragen des Aktivitätsprotokolls

Da die Rolle „Überwachungsleser“ im Stammbereich einen so weitreichenden Zugriff erlaubt, sollten Sie die Rolle einem Dienstprinzipalkonto zuweisen und Daten über dieses Konto abfragen. Verwenden Sie dazu das folgende Skript.

> [!IMPORTANT]
> Derzeit treten bei Mandanten mit einer großen Menge an Delegierungsaktivitäten möglicherweise Fehler auf, wenn diese Daten abgefragt werden.

Wenn Sie zum Abfragen des Aktivitätsprotokolls ein Dienstprinzipalkonto verwenden, werden die folgenden bewährten Methoden empfohlen:

- [Erstellen Sie ein neues Dienstprinzipalkonto](../../active-directory/develop/howto-create-service-principal-portal.md), das nur für diese Funktion verwendet wird, statt diese Rolle einem vorhandenen Dienstprinzipal zuzuweisen, der für andere Automatisierungen verwendet wird.
- Stellen Sie sicher, dass dieser Dienstprinzipal keinen Zugriff auf delegierte Kundenressourcen hat.
- [Verwenden Sie ein Zertifikat zum Authentifizieren](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options), und [speichern Sie es sicher in Azure Key Vault](../../key-vault/general/security-overview.md).
- Beschränken Sie die Anzahl der Benutzer, die entsprechenden Zugriff zum Handeln im Namen des Dienstprinzipals haben.

Nachdem Sie ein neues Dienstprinzipalkonto mit dem Zugriffsrecht der Rolle „Überwachungsleser“ im Stammbereich Ihres Verwaltungsmandanten erstellt haben, können Sie damit Delegierungsaktivitäten in Ihrem Mandanten abfragen und Berichte erstellen.

[Dieses Azure PowerShell-Skript](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) kann verwendet werden, um die Aktivitäten des letzten Tages abzufragen und einen Bericht über alle hinzugefügten oder entfernten Delegierungen (bzw. nicht erfolgreiche Versuche) zu erstellen. Es werden die Daten des [Mandantenaktivitätsprotokolls](/rest/api/monitor/TenantActivityLogs/List) abgefragt und dann die folgenden Werte erstellt, um über hinzugefügte oder entfernte Delegierungen zu berichten:

- **DelegatedResourceId**: Die ID des delegierten Abonnements oder der delegierten Ressourcengruppe.
- **CustomerTenantId**: Die ID des Kundenmandanten.
- **CustomerSubscriptionId**: Die Abonnement-ID, die delegiert wurde oder die Ressourcengruppe enthält, die delegiert wurde.
- **CustomerDelegationStatus**: Die Statusänderung für die delegierte Ressource (erfolgreich oder fehlerhaft).
- **EventTimeStamp**: Das Datum und die Uhrzeit, zu der die Delegierungsänderung protokolliert wurde.

Beim Abfragen dieser Daten ist Folgendes zu beachten:

- Wenn mehrere Ressourcengruppen in einer einzelnen Bereitstellung delegiert werden, werden für jede Ressourcengruppe separate Einträge zurückgegeben.
- An einer vorherigen Delegierung vorgenommene Änderungen (z. B. Aktualisierung der Berechtigungsstruktur) werden als hinzugefügte Delegierung protokolliert.
- Wie bereits erwähnt, muss ein Konto über die integrierte Azure-Rolle „Überwachungsleser“ im Stammbereich (/) verfügen, um auf diese Daten auf Mandantenebene zuzugreifen.
- Sie können diese Daten in Ihren eigenen Workflows und der eigenen Berichterstellung verwenden. Beispielsweise können Sie mit der [HTTP-Datensammler-API (Public Preview)](../../azure-monitor/platform/data-collector-api.md) Daten von einem REST-API-Client in Azure Monitor protokollieren und anschließend [Aktionsgruppen](../../azure-monitor/platform/action-groups.md) verwenden, um Benachrichtigungen oder Warnungen zu erstellen.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Onboarding von Kunden in [Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Erfahren Sie mehr über [Azure Monitor](../../azure-monitor/index.yml) und das [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/platform-logs-overview.md).
- Mithilfe der Beispielarbeitsmappe [Aktivitätsprotokolle nach Domäne](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) können Sie lernen, wie Sie Azure-Aktivitätsprotokolle über mehrere Abonnements hinweg anzeigen und nach Domänenname filtern können.
