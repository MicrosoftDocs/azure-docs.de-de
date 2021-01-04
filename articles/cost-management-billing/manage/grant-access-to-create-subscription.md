---
title: Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements
description: Erfahren Sie, wie Sie einem Benutzer oder Dienstprinzipal ermöglichen können, programmgesteuert Azure Enterprise-Abonnements zu erstellen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: c13670df26e5d0f7774b5a2aac81f656de94c960
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844709"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements (Vorschau)

Als Azure-Kunde mit einem [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) können Sie einem anderen Benutzer oder Dienstprinzipal die Berechtigung zum Erstellen von Abonnements erteilen, die Ihrem Konto in Rechnung gestellt werden. In diesem Artikel erfahren Sie, wie Sie [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) verwenden, um anderen Benutzern das Erstellen von Abonnements zu ermöglichen, und wie Sie die Erstellung von Abonnements überwachen. Sie müssen für das Konto, das Sie freigeben möchten, über die Rolle „Besitzer“ verfügen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Gewähren von Zugriff

Benutzer müssen in einem Registrierungskonto über die [Azure RBAC-Rolle „Besitzer“](../../role-based-access-control/built-in-roles.md#owner) verfügen, um [Abonnements in diesem Konto zu erstellen](programmatically-create-subscription-enterprise-agreement.md). Sie können einem Benutzer oder einer Gruppe von Benutzern die Azure RBAC-Rolle „Besitzer“ in einem Registrierungskonto zuweisen, indem Sie folgende Schritte ausführen:

1. Abrufen der Objekt-ID des Registrierungskontos, dem Sie den Zugriff gewähren möchten

    Sie müssen entweder der Kontobesitzer sein oder in diesem Konto über die Azure RBAC-Rolle „Besitzer“ verfügen, um anderen in einem Registrierungskonto die Azure RBAC-Rolle „Besitzer“ zuweisen zu können.

    # <a name="rest"></a>[REST](#tab/rest)

    Fordern Sie eine Liste aller Registrierungskonten an, auf die Sie zugreifen können:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure gibt eine Liste aller Registrierungskonten zurück, auf die Sie zugreifen können:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, dem Sie die Azure RBAC-Rolle „Besitzer“ zuweisen möchten. Kopieren Sie den `name`-Wert dieses Kontos. Wenn Sie beispielsweise dem Registrierungskonto SignUpEngineering@contoso.com die Azure RBAC-Rolle „Besitzer“ zuweisen möchten, müssen Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` kopieren. Dies ist die Objekt-ID des Registrierungskontos. Kopieren Sie diesen Wert, um ihn im nächsten Schritt als `enrollmentAccountObjectId`-Wert zu verwenden.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Verwenden Sie das Cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) zum Auflisten aller Registrierungskonten, auf die Sie zugreifen können. Wählen Sie **Jetzt ausprobieren** aus, um [Azure Cloud Shell](https://shell.azure.com/) zu öffnen. Klicken Sie zum Einfügen des Codes mit der rechten Maustaste auf das Shell-Fenster, und wählen Sie dann **Einfügen** aus.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure gibt eine Liste aller Registrierungskonten zurück, auf die Sie zugreifen können:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, dem Sie die Azure RBAC-Rolle „Besitzer“ zuweisen möchten. Kopieren Sie den `ObjectId`-Wert dieses Kontos. Wenn Sie beispielsweise dem Registrierungskonto SignUpEngineering@contoso.com die Azure RBAC-Rolle „Besitzer“ zuweisen möchten, müssen Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` kopieren. Kopieren Sie diese Objekt-ID, um sie im nächsten Schritt als `enrollmentAccountObjectId`-Wert zu verwenden.

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    Verwenden Sie den Befehl [az billing enrollment-account list](/cli/azure/billing) zum Auflisten aller Registrierungskonten, auf die Sie zugreifen können. Wählen Sie **Jetzt ausprobieren** aus, um [Azure Cloud Shell](https://shell.azure.com/) zu öffnen. Klicken Sie zum Einfügen des Codes mit der rechten Maustaste auf das Shell-Fenster, und wählen Sie dann **Einfügen** aus.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure gibt eine Liste aller Registrierungskonten zurück, auf die Sie zugreifen können:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, dem Sie die Azure RBAC-Rolle „Besitzer“ zuweisen möchten. Kopieren Sie den `name`-Wert dieses Kontos. Wenn Sie beispielsweise dem Registrierungskonto SignUpEngineering@contoso.com die Azure RBAC-Rolle „Besitzer“ zuweisen möchten, müssen Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` kopieren. Dies ist die Objekt-ID des Registrierungskontos. Kopieren Sie diesen Wert, um ihn im nächsten Schritt als `enrollmentAccountObjectId`-Wert zu verwenden.

1. <a id="userObjectId"></a>Abrufen der Objekt-ID des Benutzers oder der Gruppe, dem bzw. der Sie die Azure RBAC-Rolle „Besitzer“ zuweisen möchten

    1. Suchen Sie im Azure-Portal nach **Azure Active Directory**.
    1. Wenn Sie einem Benutzer Zugriff gewähren möchten, klicken Sie im Menü auf der linken Seite auf **Benutzer**. Um einer Gruppe Zugriff zu erteilen, wählen Sie **Gruppen** aus.
    1. Wählen Sie den Benutzer oder die Gruppe aus, dem bzw. der Sie die Azure RBAC-Rolle „Besitzer“ zuweisen möchten.
    1. Wenn Sie einen Benutzer ausgewählt haben, finden Sie die Objekt-ID auf der Seite „Profil“. Wenn Sie eine Gruppe ausgewählt haben, befindet sich die Objekt-ID auf der Seite „Übersicht“. Kopieren Sie die **ObjectID**, indem Sie auf das Symbol rechts neben dem Textfeld klicken. Halten Sie diesen Wert bereit, damit Sie ihn im nächsten Schritt als `userObjectId` verwenden können.

1. Zuweisen der Azure RBAC-Rolle „Besitzer“ zu einem Benutzer oder einer Gruppe im Registrierungskonto

    Verwenden Sie die Werte, die Sie in den ersten beiden Schritten erfasst haben, um dem Benutzer oder der Gruppe die Azure RBAC-Rolle „Besitzer“ im Registrierungskonto zuzuweisen.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Führen Sie den folgenden Befehl aus, und ersetzen Sie dabei ```<enrollmentAccountObjectId>``` durch den `name`-Wert, den Sie im ersten Schritt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopiert haben. Ersetzen Sie ```<userObjectId>``` durch die Objekt-ID, die Sie im zweiten Schritt kopiert haben.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Wenn die Besitzerrolle im Bereich des Anmeldekontos erfolgreich zugewiesen wurde, antwortet Azure mit Informationen zur Rollenzuweisung:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Führen Sie den folgenden [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md)-Befehl aus, und ersetzen Sie ```<enrollmentAccountObjectId>``` durch den `ObjectId`-Wert, den Sie im ersten Schritt erfasst haben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersetzen Sie ```<userObjectId>``` durch die Objekt-ID, die Sie im zweiten Schritt erfasst haben.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli-2)

    Führen Sie den folgenden [az role assignment create](../../role-based-access-control/role-assignments-cli.md)-Befehl aus, und ersetzen Sie ```<enrollmentAccountObjectId>``` durch den `name`-Wert, den Sie im ersten Schritt kopiert haben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersetzen Sie ```<userObjectId>``` durch die Objekt-ID, die Sie im zweiten Schritt erfasst haben.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Sobald ein Benutzer ein Azure RBAC-Besitzer für Ihr Registrierungskonto ist, kann er darunter [programmgesteuert Abonnements erstellen](programmatically-create-subscription-enterprise-agreement.md). Ein Abonnement, das von einem delegierten Benutzer erstellt wurde, weist weiterhin den ursprünglichen Kontobesitzer als Dienstadministrator auf, verfügt aber standardmäßig auch über den delegierten Benutzer als Azure RBAC-Besitzer.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Überwachen mit Aktivitätsprotokollen, wer Abonnements erstellt hat

Verwenden Sie zum Nachverfolgen der über diese API erstellten Abonnements die [Mandantenaktivitätsprotokoll-API](/rest/api/monitor/tenantactivitylogs). Zurzeit ist es nicht möglich, PowerShell, die CLI oder das Azure-Portal zum Nachverfolgen der Abonnementerstellung zu verwenden.

1. Als Mandantenadministrator des Azure AD-Mandanten [erhöhen Sie die Zugriffsrechte](../../role-based-access-control/elevate-access-global-admin.md), und weisen Sie dann dem überwachenden Benutzer eine Leserrolle über den Bereich `/providers/microsoft.insights/eventtypes/management` zu. Dieser Zugriff ist in der Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader), in der Rolle [Mitwirkender an der Überwachung](../../role-based-access-control/built-in-roles.md#monitoring-contributor) oder in einer [benutzerdefinierten Rolle](../../role-based-access-control/custom-roles.md) verfügbar.
1. Rufen Sie als überwachender Benutzer die [Mandantenaktivitätsprotokoll-API](/rest/api/monitor/tenantactivitylogs) auf, um Aktivitäten zum Erstellen von Abonnements anzuzeigen. Beispiel:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Um diese API bequem über die Befehlszeile aufzurufen, verwenden Sie [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Nächste Schritte

* Da der Benutzer oder Dienstprinzipal nun über die Berechtigung verfügt, ein Abonnement zu erstellen, können Sie diese Identität verwenden, um [programmgesteuert Azure Enterprise-Abonnements zu erstellen](programmatically-create-subscription-enterprise-agreement.md).
* Ein Beispiel zum Erstellen von Abonnements mithilfe von .NET finden Sie im [Beispielcode auf GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Weitere Informationen zu Azure Resource Manager und den zugehörigen APIs finden Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/management/overview.md).
* Weitere Informationen zum Verwalten einer großen Anzahl von Abonnements mithilfe von Verwaltungsgruppen finden Sie unter [Organize your resources with Azure Management Groups](../../governance/management-groups/overview.md) (Organisieren von Ressourcen mit Azure-Verwaltungsgruppen).
* Einen umfassenden Leitfaden mit bewährten Methoden für große Organisationen zur Abonnementgovernance finden Sie unter [Azure Enterprise-Gerüst: Präskriptive Abonnementgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).