---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos DB-Konto
description: Informationen zum Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos DB-Konto mit Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/05/2020
ms.author: thweiss
ms.openlocfilehash: e87f6f158265fd8ac210a0a071e35b0bb77df4d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338269"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos-Konto mit Azure Key Vault
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Die in Ihrem Azure Cosmos-Konto gespeicherten Daten werden automatisch und nahtlos mit von Microsoft verwalteten Schlüsseln (**vom Dienst verwaltete Schlüssel**) verschlüsselt. Sie können optional eine zweite Verschlüsselungsschicht mit von Ihnen verwalteten Schlüsseln (**vom Kunden verwaltete Schlüssel**) hinzufügen.

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Verschlüsselungsschicht um Kundendaten":::

Sie müssen vom Kunden verwaltete Schlüssel in [Azure Key Vault](../key-vault/general/overview.md) speichern und einen Schlüssel für jedes Azure Cosmos-Konto bereitstellen, für das vom Kunden verwalteten Schlüssel aktiviert sind. Dieser Schlüssel wird zum Verschlüsseln aller in diesem Konto gespeicherten Daten verwendet.

> [!NOTE]
> Derzeit sind von Kunden verwaltete Schlüssel nur für neue Azure Cosmos-Konten verfügbar. Sie sollten während der Kontoerstellung konfiguriert werden.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Registrieren des Azure Cosmos DB-Ressourcenanbieters für Ihr Azure-Abonnement

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, navigieren Sie zu Ihrem Azure-Abonnement, und wählen Sie auf der Registerkarte **Einstellungen** die Option **Ressourcenanbieter** aus:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Eintrag „Ressourcenanbieter“ im linken Menü":::

1. Suchen Sie nach dem Ressourcenanbieter **Microsoft.DocumentDB**. Überprüfen Sie, ob der Ressourcenanbieter bereits als registriert gekennzeichnet ist. Wenn dies nicht der Fall ist, wählen Sie den Ressourcenanbieter aus, und wählen Sie dann **Registrieren** aus:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Registrieren des Ressourcenanbieters „Microsoft.DocumentDB“":::

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurieren Ihrer Azure Key Vault-Instanz

Wenn Sie kundenseitig verwaltete Schlüssel mit Azure Cosmos DB verwenden, müssen Sie zwei Eigenschaften für die Azure Key Vault-Instanz festlegen, die Sie zum Hosten Ihrer Verschlüsselungsschlüssel verwenden möchten: **Vorläufiges Löschen** und **Löschschutz**.

Wenn Sie eine neue Azure Key Vault-Instanz erstellen, aktivieren Sie diese Eigenschaften während der Erstellung:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Aktivieren des vorläufigen Löschens und des Löschschutzes für eine neue Azure Key Vault-Instanz":::

Bei Verwendung einer vorhandenen Azure Key Vault-Instanz können Sie überprüfen, ob diese Eigenschaften aktiviert sind, indem Sie sich im Azure-Portal den Abschnitt **Eigenschaften** ansehen. Ist eine dieser Eigenschaften nicht aktiviert, finden Sie in den Abschnitten „Aktivieren des vorläufigen Löschens“ und „Aktivieren des Bereinigungsschutzes“ in einem der folgenden Artikel weitere Informationen:

- [Verwenden des vorläufigen Löschens mit PowerShell](../key-vault/general/key-vault-recovery.md)
- [Verwenden des vorläufigen Löschens mit Azure CLI](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Hinzufügen einer Zugriffsrichtlinie zu Ihrer Azure Key Vault-Instanz

1. Navigieren Sie im Azure-Portal zu der Azure Key Vault-Instanz, die Sie zum Hosten Ihrer Verschlüsselungsschlüssel verwenden möchten. Wählen Sie im linken Menü die Option **Zugriffsrichtlinien** aus.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Zugriffsrichtlinien im linken Menü":::

1. Wählen Sie **+ Zugriffsrichtlinie hinzufügen** aus.

1. Wählen Sie im Dropdownmenü **Schlüsselberechtigungen** die Berechtigungen **Abrufen**, **Schlüssel entpacken** und **Schlüssel packen** aus:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="Auswählen der richtigen Berechtigungen":::

1. Wählen Sie unter **Prinzipal auswählen** die Option **Nichts ausgewählt** aus. Suchen Sie dann nach dem **Azure Cosmos DB**-Prinzipal, und wählen Sie ihn aus (um ihn leichter zu finden, können Sie auch nach der Prinzipal-ID suchen: `a232010e-820c-4083-83bb-3ace5fc29d0b` für alle Azure-Regionen, mit Ausnahme der Azure Government-Regionen, bei denen die Prinzipal-ID `57506a73-e302-42a9-b869-6f12d9ec29e9` lautet). Wählen Sie schließlich im unteren Bereich **Auswählen** aus. Wenn der Prinzipal **Azure Cosmos DB** nicht in der Liste aufgeführt ist, müssen Sie möglicherweise den Ressourcenanbieter **Microsoft.DocumentDB** erneut registrieren (wie im Abschnitt [Registrieren des Ressourcenanbieters](#register-resource-provider) in diesem Artikel beschrieben).

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Auswählen des Azure Cosmos DB-Prinzipals":::

1. Wählen Sie **Hinzufügen** aus, um die neue Zugriffsrichtlinie hinzuzufügen.

1. Wählen Sie **Speichern** in der Key Vault-Instanz aus, um alle Änderungen zu speichern.

## <a name="generate-a-key-in-azure-key-vault"></a>Generieren eines Schlüssels in Azure Key Vault

1. Navigieren Sie im Azure-Portal zu der Azure Key Vault-Instanz, die Sie zum Hosten Ihrer Verschlüsselungsschlüssel verwenden möchten. Wählen Sie dann im linken Menü die Option **Schlüssel** aus:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Eintrag „Schlüssel“ im linken Menü":::

1. Wählen Sie **Generieren/Importieren** aus, geben Sie einen Namen für den neuen Schlüssel an, und wählen Sie eine RSA-Schlüsselgröße aus. Es wird eine Größe von mindestens 3072 empfohlen, da diese Größe die höchste Sicherheit bietet. Wählen Sie anschließend **Erstellen** aus:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Erstellen eines neuen Schlüssels":::

1. Wählen Sie nach der Schlüsselerstellung den neu erstellten Schlüssel und dann seine aktuelle Version aus.

1. Kopieren Sie den **Schlüsselbezeichner** des Schlüssels mit Ausnahme des Teils nach dem letzten Schrägstrich:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="Kopieren des Schlüsselbezeichners des Schlüssels":::

## <a name="create-a-new-azure-cosmos-account"></a>Erstellen eines neuen Azure Cosmos-Kontos

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Wählen Sie beim Erstellen eines neuen Azure Cosmos DB-Kontos im Azure-Portal im Schritt **Verschlüsselung** die Option **Vom Kunden verwalteter Schlüssel** aus. Fügen Sie in das Feld **Schlüssel-URI** den URI/Schlüsselbezeichner des Azure Key Vault-Schlüssels ein, den Sie im vorherigen Schritt kopiert haben:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Festlegen der CMK-Parameter im Azure-Portal":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Verwenden von Azure PowerShell

Berücksichtigen Sie Folgendes, wenn Sie ein neues Azure Cosmos DB-Konto mit PowerShell erstellen:

- Übergeben Sie den zuvor kopierten URI des Azure Key Vault-Schlüssels unter der Eigenschaft **keyVaultKeyUri** im **PropertyObject**.

- Verwenden Sie mindestens **2019-12-12** als API-Version.

> [!IMPORTANT]
> Sie müssen die Eigenschaft `locations` explizit festlegen, damit das Konto erfolgreich mit kundenseitig verwalteten Schlüsseln erstellt werden kann.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

Nach der Erstellung des Kontos können Sie überprüfen, ob kundenseitig verwaltete Schlüssel aktiviert wurden, indem Sie den URI des Azure Key Vault-Schlüssels abrufen:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Verwenden einer Azure Resource Manager-Vorlage

Berücksichtigen Sie Folgendes, wenn Sie ein neues Azure Cosmos-Konto anhand einer Azure Resource Manager-Vorlage erstellen:

- Übergeben Sie den zuvor kopierten URI des Azure Key Vault-Schlüssels unter der Eigenschaft **keyVaultKeyUri** im Objekt **properties**.

- Verwenden Sie mindestens **2019-12-12** als API-Version.

> [!IMPORTANT]
> Sie müssen die Eigenschaft `locations` explizit festlegen, damit das Konto erfolgreich mit kundenseitig verwalteten Schlüsseln erstellt werden kann.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Stellen Sie die Vorlage mithilfe des folgenden PowerShell-Skripts bereit:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Mithilfe der Azure CLI

Wenn Sie ein neues Azure Cosmos-Konto über die Azure-Befehlszeilenschnittstelle erstellen, übergeben Sie den URI des Azure Key Vault-Schlüssels, den Sie zuvor unter dem Parameter `--key-uri` kopiert haben.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

Nach der Erstellung des Kontos können Sie überprüfen, ob kundenseitig verwaltete Schlüssel aktiviert wurden, indem Sie den URI des Azure Key Vault-Schlüssels abrufen:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>Schlüsselrotation

Die Rotation des kundenseitig verwalteten Schlüssels, der von Ihrem Azure Cosmos-Konto verwendet wird, kann auf zwei Arten erfolgen.

- Erstellen Sie eine neue Version des Schlüssels, der derzeit von Azure Key Vault verwendet wird:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Erstellen einer neuen Schlüsselversion":::

- Tauschen Sie den derzeit verwendeten Schlüssel gegen einen völlig anderen aus, indem Sie den Schlüssel-URI in Ihrem Konto aktualisieren. Navigieren Sie im Azure-Portal zu Ihrem Azure Cosmos-Konto, und wählen Sie im linken Menü die Option **Datenverschlüsselung** aus:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Menüeintrag „Datenverschlüsselung“":::

    Ersetzen Sie dann den **Schlüssel-URI** durch den neuen Schlüssel, den Sie verwenden möchten, und wählen Sie **Speichern** aus:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="Aktualisieren des Schlüssel-URI":::

    Gehen Sie folgendermaßen vor, um das gleiche Ergebnis in PowerShell zu erzielen:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

Der vorherige Schlüssel oder die vorherige Schlüsselversion kann nach 24 Stunden oder dann deaktiviert werden, wenn in den [Azure Key Vault-Überwachungsprotokollen](../key-vault/general/logging.md) keine Aktivitäten mehr von Azure Cosmos DB für diesen Schlüssel oder diese Schlüsselversion angezeigt werden.
    
## <a name="error-handling"></a>Fehlerbehandlung

Wenn bei der Verwendung von kundenseitig verwalteten Schlüsseln (Customer-Managed Keys, CMK) in Azure Cosmos DB Fehler auftreten, gibt Azure Cosmos DB die Fehlerdetails zusammen mit einem HTTP-Unterstatuscode in der Antwort zurück. Mit diesem Unterstatuscode können Sie die Grundursache des Problems debuggen. Eine Liste der unterstützten HTTP-Unterstatuscodes finden Sie im Artikel [HTTP-Statuscodes für Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Fallen für die Aktivierung von kundenseitig verwalteten Schlüsseln zusätzliche Gebühren an?

Nein, es fallen keine Kosten für die Aktivierung dieses Features an.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Wie wirken sich kundenseitig verwaltete Schlüssel auf die Kapazitätsplanung aus?

Wenn Sie kundenseitig verwaltete Schlüssel verwenden, erhöht sich die Anzahl der von Ihren Datenbankvorgängen genutzten [Anforderungseinheiten](./request-units.md) entsprechend der zusätzlichen Verarbeitungsschritte, die zum Durchführen der Verschlüsselung und Entschlüsselung Ihrer Daten erforderlich sind. Dies kann zu einer geringfügig höheren Auslastung Ihrer bereitgestellten Kapazität führen. Nutzen Sie die folgende Tabelle als Orientierung:

| Vorgangsart | Erhöhung der Anforderungseinheiten |
|---|---|
| Punktlesevorgänge (Abrufen von Elementen nach ID) | 5 % pro Vorgang |
| Beliebiger Schreibvorgang | 6 % pro Vorgang<br/>etwa 0,06 RU pro indizierte Eigenschaft |
| Abfragen, Lesen des Änderungsfeeds oder Konfliktfeeds | 15 % pro Vorgang |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Welche Daten werden mit den vom Kunden verwalteten Schlüsseln verschlüsselt?

Mit Ausnahme der nachfolgend aufgeführten Metadaten werden alle in Ihrem Azure Cosmos-Konto gespeicherten Daten mit den vom Kunden verwalteten Schlüsseln verschlüsselt:

- Die Namen Ihrer Azure Cosmos DB-[Konten, -Datenbanken und -Container](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)

- Die Namen Ihrer [gespeicherten Prozeduren](./stored-procedures-triggers-udfs.md)

- Die in Ihren [Indizierungsrichtlinien](./index-policy.md) deklarierten Eigenschaftenpfade

- Die Werte der [Partitionsschlüssel](./partitioning-overview.md) Ihrer Container

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Werden vom Kunden verwaltete Schlüssel für vorhandene Azure Cosmos-Konten unterstützt?

Dieses Feature ist derzeit nur für neue Konten verfügbar.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Ist die Unterstützung einer feineren Granularität als bei Schlüsseln auf Kontoebene geplant?

Derzeit nicht. Es werden jedoch Schlüssel auf Containerebene in Erwägung gezogen.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Woran erkenne ich, dass kundenseitig verwaltete Schlüssel für mein Azure Cosmos-Konto aktiviert sind?

Navigieren Sie im Azure-Portal zu Ihrem Azure Cosmos-Konto, und suchen Sie im linken Menü nach dem Eintrag **Datenverschlüsselung**. Wenn dieser Eintrag vorhanden ist, sind kundenseitig verwaltete Schlüssel für Ihr Konto aktiviert:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Menüeintrag „Datenverschlüsselung“":::

Sie können die Details Ihres Azure Cosmos-Kontos auch programmgesteuert abrufen und überprüfen, ob die Eigenschaft `keyVaultKeyUri` vorhanden ist. Informationen zur Vorgehensweise [in PowerShell](#using-powershell) und [mithilfe der Azure CLI](#using-azure-cli) finden Sie weiter oben.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Wie wirken sich vom Kunden verwaltete Schlüssel auf eine Sicherung aus?

Azure Cosmos DB erstellt [regelmäßige und automatische Sicherungen](./online-backup-and-restore.md) der in Ihrem Konto gespeicherten Daten. Bei diesem Vorgang werden die verschlüsselten Daten gesichert. Um die wiederhergestellte Sicherung verwenden zu können, ist der zum Zeitpunkt der Sicherung verwendete Verschlüsselungsschlüssel erforderlich. Das bedeutet, dass keine Sperrung erfolgt ist und die zum Zeitpunkt der Sicherung verwendete Version des Schlüssels weiterhin aktiviert ist.

### <a name="how-do-i-revoke-an-encryption-key"></a>Wie sperre/widerrufe ich einen Verschlüsselungsschlüssel?

Die Schlüsselsperrung erfolgt durch Deaktivieren der aktuellen Version des Schlüssels:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Deaktivieren der Version eines Schlüssels":::

Alternativ können Sie zum Sperren aller Schlüssel in einer Azure Key Vault-Instanz die dem Azure Cosmos DB-Prinzipal erteilte Zugriffsrichtlinie löschen:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Löschen der Zugriffsrichtlinie für den Azure Cosmos DB-Prinzipal":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Welche Vorgänge sind nach dem Sperren/Widerrufen eines vom Kunden verwalteten Schlüssels verfügbar?

Das Löschen von Konten ist der einzige mögliche Vorgang, wenn der Verschlüsselungsschlüssel widerrufen wurde.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Datenverschlüsselung in Azure Cosmos DB](./database-encryption-at-rest.md).
- Überblick über [sicheren Zugriff auf Daten in Cosmos DB](secure-access-to-data.md).