---
title: Rollenbasierte Azure-Zugriffssteuerung in Azure Cosmos DB
description: Erfahren Sie, wie Azure Cosmos DB Datenbankschutz mithilfe der Active Directory-Integration (Azure RBAC) bereitstellt.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: db6721754802a3eeda387339498dadb9d25b3685
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526940"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Rollenbasierte Azure-Zugriffssteuerung in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB bietet für gängige Verwaltungsszenarien eine integrierte rollenbasierte Azure-Zugriffssteuerung (Azure RBAC). Eine Person mit einem Profil in Azure Active Directory kann diese Azure-Rollen Benutzern, Gruppen, Dienstprinzipalen oder verwalteten Identitäten zuweisen, um den Zugriff auf Ressourcen und Vorgänge für Azure Cosmos DB-Ressourcen zu gewähren oder zu verweigern. Rollenzuweisungen sind auf den Zugriff auf Steuerungsebene beschränkt, was den Zugriff auf Konten, Datenbanken, Container und Angebote (Durchsatz) für Azure Cosmos umfasst.

## <a name="built-in-roles"></a>Integrierte Rollen

Die folgenden vordefinierten Rollen werden von Azure Cosmos DB unterstützt:

|**Integrierte Rolle**  |**Beschreibung**  |
|---------|---------|
|[Mitwirkender von DocumentDB-Konto](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kann Azure Cosmos DB-Konten verwalten.|
|[Cosmos DB-Kontoleser](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kann Azure Cosmos DB-Kontodaten lesen.|
|[Cosmos-Sicherungsoperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| Kann eine Wiederherstellungsanforderung für Azure-Portal für eine periodisch aktivierte Sicherungsdatenbank oder einen Container übermitteln. Kann das Sicherungsintervall und die Datenaufbewahrung im Azure-Portal ändern. Kann nicht auf Daten zugreifen oder Daten-Explorer verwenden.  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | Kann eine Wiederherstellungsaktion für ein Azure Cosmos DB-Konto mit fortlaufendem Sicherungsmodus ausführen.|
|[Cosmos DB-Operator](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Kann Azure Cosmos-Konten, -Datenbanken und -Container bereitstellen. Kann nicht auf Daten zugreifen oder Daten-Explorer verwenden.|

> [!IMPORTANT]
> Azure RBAC-Unterstützung in Azure Cosmos DB gilt nur für Vorgänge auf Steuerungsebene. Vorgänge auf Datenebene werden mithilfe von Primärschlüsseln oder Ressourcentoken abgesichert. Weitere Informationen finden Sie unter [Sicherer Zugriff auf Daten in Azure Cosmos DB](secure-access-to-data.md).

## <a name="identity-and-access-management-iam"></a>Identitäts- und Zugriffsverwaltung (IAM)

Der Bereich **Zugriffssteuerung (IAM)**  im Azure-Portal dient zum Konfigurieren der rollenbasierten Azure-Zugriffssteuerung für Azure Cosmos-Ressourcen. Die Rollen werden Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten in Active Directory zugewiesen. Sie können für Einzelbenutzer und Gruppen integrierte oder benutzerdefinierte Rollen verwenden. Der folgende Screenshot zeigt die Active Directory-Integration (Azure RBAC) mithilfe der Zugriffssteuerung (IAM) im Azure-Portal:

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Zugriffssteuerung (IAM) im Azure-Portal – Veranschaulichung der Datenbanksicherheit.":::

## <a name="custom-roles"></a>Benutzerdefinierte Rollen

Zusätzlich zu den integrierten Rollen können Benutzer in Azure auch [benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md) erstellen und diese Rollen Dienstprinzipalen in allen Abonnements innerhalb ihres Active Directory-Mandanten zuweisen. Benutzerdefinierte Rollen bieten Benutzern eine Möglichkeit, Azure-Rollendefinitionen mit einem benutzerdefinierten Satz von Ressourcenanbietervorgängen zu erstellen. Um zu erfahren, welche Vorgänge für die Erstellung benutzerdefinierter Rollen für Azure Cosmos DB verfügbar sind, siehe [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb).

> [!TIP]
> Benutzerdefinierte Rollen, die in der Lage sein müssen, auf in Cosmos-DB gespeicherte Daten zuzugreifen oder Daten-Explorer im Azure-Portal zu verwenden, müssen über die Aktion `Microsoft.DocumentDB/databaseAccounts/listKeys/*` verfügen.

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Verhindern von Änderungen aus den Azure Cosmos DB SDKs

Der Azure Cosmos DB-Ressourcenanbieter kann gesperrt werden, um Ressourcenänderungen von einem Client zu verhindern, der eine Verbindung unter Verwendung der Kontoschlüssel herstellt (also Anwendungen, die eine Verbindung über das Azure Cosmos SDK herstellen). Dies schließt auch Änderungen über das Azure-Portal ein. Dieses Feature ist möglicherweise für Benutzer vorteilhaft, die ein höheres Maß an Kontrolle und Governance für Produktionsumgebungen wünschen. Das Verhindern von Änderungen aus dem SDK ermöglicht außerdem Features wie Ressourcensperren und Diagnoseprotokolle für Vorgänge auf der Steuerungsebene. Von Clients, die eine Verbindung aus dem Azure Cosmos DB SDK herstellen, können keine Eigenschaften für Azure Cosmos-Konten, Datenbanken, Container und Durchsatz geändert werden. Die Vorgänge, bei denen Daten aus Cosmos-Containern gelesen oder in Cosmos-Container geschrieben werden, sind davon nicht betroffen.

Wenn dieses Feature aktiviert ist, können Änderungen an Ressourcen nur von einem Benutzer mit der richtigen Azure-Rolle und entsprechenden Azure Active Directory-Anmeldeinformationen vorgenommen werden. Dies schließt auch verwaltete Dienstidentitäten mit ein.

> [!WARNING]
> Die Aktivierung dieses Features kann Auswirkungen auf Ihre Anwendung haben. Stellen Sie sicher, dass Sie die Auswirkungen verstehen, bevor Sie das Feature aktivieren.

### <a name="check-list-before-enabling"></a>Prüfliste vor der Aktivierung

Durch diese Einstellung werden sämtliche Cosmos-Ressourcenänderungen durch Clients, die eine Verbindung unter Verwendung von Kontoschlüsseln herstellen (einschließlich aller Cosmos DB SDKs), durch Tools, die Kontoschlüssel für die Verbindungsherstellung verwenden, sowie über das Azure-Portal verhindert. Überprüfen Sie vor der Aktivierung dieses Features, ob Anwendungen oder Azure-Portalbenutzer eine der folgenden Aktionen ausführen, um Anwendungsprobleme oder -fehler nach der Aktivierung zu vermeiden:

- Beliebige Änderung des Cosmos-Kontos (einschließlich Eigenschaften oder Hinzufügen/Entfernen von Regionen)

- Erstellen oder Löschen untergeordneter Ressourcen wie Datenbanken und Container. Dies schließt auch Ressourcen für andere APIs wie Cassandra, MongoDB, Gremlin und Tabellenressourcen mit ein.

- Aktualisieren des Durchsatzes für Ressourcen auf der Datenbank- oder Containerebene

- Ändern von Containereigenschaften wie Indexrichtlinie, Gültigkeitsdauer und eindeutige Schlüssel

- Ändern von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen

Wenn Ihre Anwendungen (oder Benutzer über das Azure-Portal) eine dieser Aktionen ausführen, müssen sie für die Ausführung mithilfe von [ARM-Vorlagen](./manage-with-templates.md), per [PowerShell](manage-with-powershell.md), über die [Azure CLI](manage-with-cli.md), mithilfe von REST oder per [Azure-Verwaltungsbibliothek](https://github.com/Azure-Samples/cosmos-management-net) migriert werden. Beachten Sie, dass die Azure-Verwaltung in [mehreren Sprachen](/azure/?product=featured#languages-and-tools) verfügbar ist.

### <a name="set-via-arm-template"></a>Festlegen per ARM-Vorlage

Wenn Sie diese Eigenschaft mithilfe einer ARM-Vorlage festlegen möchten, aktualisieren Sie Ihre vorhandene Vorlage, oder exportieren Sie eine neue Vorlage für Ihre aktuelle Bereitstellung, und schließen Sie `"disableKeyBasedMetadataWriteAccess": true` in die Eigenschaften für die Ressourcen vom Typ `databaseAccounts` ein. Im Anschluss sehen Sie ein einfaches Beispiel für eine Azure Resource Manager-Vorlage mit dieser Eigenschaftseinstellung:

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Schließen Sie bei der erneuten Bereitstellung mit dieser Eigenschaft auch die anderen Eigenschaften für Ihr Konto und Ihre untergeordneten Ressourcen mit ein. Wenn Sie diese Vorlage unverändert bereitstellen, werden alle Ihre Kontoeigenschaften zurückgesetzt.

### <a name="set-via-azure-cli"></a>Festlegen über die Azure-Befehlszeilenschnittstelle

Verwenden Sie zur Aktivierung über die Azure CLI den folgenden Befehl:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Festlegen über PowerShell

Verwenden Sie zur Aktivierung über Azure PowerShell den folgenden Befehl:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md)
- [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md)
- [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
