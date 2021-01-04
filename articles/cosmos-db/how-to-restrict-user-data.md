---
title: Beschränken des Benutzerzugriffs ausschließlich auf Datenvorgänge mit Azure Cosmos DB
description: Erfahren Sie, wie der Benutzerzugriff ausschließlich auf Datenvorgänge mit Azure Cosmos DB beschränkt wird.
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 61e8ee2cbcd1252704b2d1e220fcfaaff17c2f28
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085988"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Beschränken des Benutzerzugriffs auf Datenvorgänge in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In Azure Cosmos DB gibt es zwei Möglichkeiten, Ihre Interaktionen mit dem Datenbankdienst zu authentifizieren:

- Verwenden Ihrer Azure Active Directory-Identität bei der Interaktion mit dem Azure-Portal.
- Verwenden von Azure Cosmos DB-[Schlüsseln](database-security.md#primary-keys) oder -[Ressourcentoken](secure-access-to-data.md#resource-tokens) beim Ausgeben von Aufrufen aus APIs und SDKs.

Jede Authentifizierungsmethode ermöglicht den Zugriff auf unterschiedliche Sätze von Vorgängen mit einer gewissen Überschneidung:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Aufteilung von Vorgängen pro Authentifizierungstyp" border="false":::

In einigen Szenarien möchten Sie möglicherweise einige Benutzer Ihrer Organisation darauf beschränken, Datenvorgänge (also CRUD-Anforderungen und Abfragen) auszuführen. Dies ist in der Regel der Fall bei Entwicklern, die keine Ressourcen erstellen oder löschen bzw. den bereitgestellten Durchsatz der Container nicht ändern müssen, an denen sie arbeiten.

Sie können den Zugriff einschränken, indem Sie die folgenden Schritte ausführen:
1. Erstellen einer benutzerdefinierten Azure Active Directory-Rolle für die Benutzer, für die Sie den Zugriff einschränken möchten. Die benutzerdefinierte Active Directory-Rolle sollte über eine differenzierte Zugriffsebene für Vorgänge verfügen, indem [präzise Aktionen](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) von Azure Cosmos DB verwendet werden.
1. Verhindern der Ausführung von Nicht-Datenvorgängen mit Schlüsseln. Dies können Sie erreichen, indem Sie diese Vorgänge ausschließlich auf Azure Resource Manager-Aufrufe beschränken.

In den nächsten Abschnitten dieses Artikels wird erläutert, wie Sie diese Schritte ausführen.

> [!NOTE]
> Um die Befehle in den nächsten Abschnitten ausführen zu können, müssen Sie das Azure PowerShell-Modul 3.0.0 oder höher installieren sowie die [Azure-Rolle „Besitzer“](../role-based-access-control/built-in-roles.md#owner) für das Abonnement, das Sie ändern möchten.

Ersetzen Sie in den PowerShell-Skripts in den nächsten Abschnitten die folgenden Platzhalter durch Werte, die für Ihre Umgebung spezifisch sind:
- `$MySubscriptionId`: Die Abonnement-ID, die das Azure Cosmos-Konto enthält, für das Sie die Berechtigungen einschränken möchten. Beispiel: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`: Die Ressourcengruppe, die das Azure Cosmos-Konto enthält. Beispiel: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`: Der Name Ihres Azure Cosmos-Kontos. Beispiel: `mycosmosdbsaccount`.
- `$MyUserName`: Der Anmeldename (username@domain) des Benutzers, für den Sie den Zugriff einschränken möchten. Beispiel: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Auswählen des Azure-Abonnements

Azure PowerShell-Befehle erfordern, dass Sie sich anmelden und das Abonnement auswählen, um die Befehle auszuführen zu können:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Erstellen der benutzerdefinierten Azure Active Directory-Rolle

Mit dem folgenden Skript wird eine Azure Active Directory-Rollenzuweisung mit Zugriff des Typs „Nur Schlüssel“ für Azure Cosmos-Konten erstellt. Die Rolle basiert auf [benutzerdefinierten Azure-Rollen](../role-based-access-control/custom-roles.md) und [präzisen Aktionen für Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Diese Rollen und Aktionen sind Teil des Azure Active Directory-Namespaces `Microsoft.DocumentDB`.

1. Erstellen Sie zunächst ein JSON-Dokument mit dem Namen `AzureCosmosKeyOnlyAccess.json` und dem folgenden Inhalt:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Führen Sie die folgenden Befehle aus, um die Rollenzuweisung zu erstellen und dem Benutzer zuzuweisen:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Verhindern der Ausführung von Nicht-Datenvorgängen

Mit den folgenden Befehlen wird die Möglichkeit entfernt, Schlüssel für folgende Zwecke zu verwenden:
- Erstellen, Ändern oder Löschen von Ressourcen
- Aktualisieren von Containereinstellungen (einschließlich Indizierungsrichtlinien, Durchsatz usw.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [rollenbasierten Zugriffssteuerung von Cosmos DB](role-based-access-control.md)
- Überblick über [sicheren Zugriff auf Daten in Cosmos DB](secure-access-to-data.md)
