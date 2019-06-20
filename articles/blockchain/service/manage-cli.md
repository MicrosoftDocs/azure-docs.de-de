---
title: Verwalten des Azure Blockchain-Diensts mit der Azure CLI
description: Erstellen und Verwalten des Azure Blockchain-Diensts mit der Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154455"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Verwalten des Azure Blockchain-Diensts mit der Azure CLI

Sie können Blockchain-Mitglieder und Transaktionsknoten für Ihren Azure Blockchain-Dienst nicht nur über das Azure-Portal, sondern auch über die Azure CLI schnell erstellen und verwalten.

Achten Sie darauf, dass Sie die neueste Version der [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) installiert haben und mit `az login` bei einem Azure-Konto angemeldet sind.

Ersetzen Sie in den folgenden Beispielen die Beispiel-`<parameter names>` durch Ihre eigenen Werte.

## <a name="create-blockchain-member"></a>Erstellen eines Blockchainmitglieds

In folgenden Beispiel wird ein Blockchainmitglied im Azure Blockchain-Dienst erstellt, das das Quorum-Ledgerprotokoll in einem neuen Konsortium ausführt.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Name der Ressourcengruppe, in der die Ressourcen des Azure Blockchain-Diensts erstellt werden. |
| **name** | Ein eindeutiger Name, der Ihr Blockchainmitglied in Azure Blockchain identifiziert. Der Name wird für die Adresse eines öffentlichen Endpunkts verwendet. Beispiel: `myblockchainmember.blockchain.azure.com`. |
| **Speicherort** | Die Azure-Region, in der das Blockchainmitglied erstellt wird. Beispiel: `eastus`. Wählen Sie den Standort aus, der Ihren Benutzern oder anderen Azure-Anwendungen am nächsten liegt. |
| **password** | Das Kennwort des Mitgliedskontos. Das Mitgliedskontokennwort wird verwendet, um den öffentlichen Endpunkt des Blockchainmitglieds zu authentifizieren. Hierzu wird die Standardauthentifizierung verwendet. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)).|
| **protocol** | Die öffentliche Vorschauversion unterstützt Quorum. |
| **consortium** | Name des Konsortiums, dem beigetreten oder das erstellt werden soll. |
| **consortiumManagementAccountPassword** | Das Verwaltungskennwort des Konsortiums. Mit dem Kennwort kann einem Konsortium beigetreten werden. |
| **ruleName** | Regelname zum Hinzufügen eines IP-Adressbereichs zu einer Whitelist. Optionaler Parameter für Firewallregeln.|
| **startIpAddress** | Anfang des IP-Adressbereichs, der der Whitelist hinzugefügt werden soll. Optionaler Parameter für Firewallregeln. |
| **endIpAddress** | Ende des IP-Adressbereichs, der der Whitelist hinzugefügt werden soll. Optionaler Parameter für Firewallregeln. |
| **skuName** | Tarifart. Verwenden Sie S0 für Standard und B0 für Basic. |

## <a name="change-blockchain-member-password"></a>Ändern des Kennworts des Blockchainmitglieds

Im folgenden Beispiel wird das Kennwort eines Blockchainmitglieds geändert.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Name der Ressourcengruppe, in der die Ressourcen des Azure Blockchain-Diensts erstellt werden. |
| **name** | Der Name, der Ihr Azure Blockchain-Mitglied identifiziert. |
| **password** | Das Kennwort des Mitgliedskontos. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)). |


## <a name="create-transaction-node"></a>Erstellen eines Transaktionsknotens

Erstellen Sie einen Transaktionsknoten in einem vorhandenen Blockchainmitglied. Durch Hinzufügen von Transaktionsknoten können Sie die Sicherheitsisolation erhöhen oder die Last verteilen. Sie können beispielsweise über einen Transaktionsknotenendpunkt für verschiedene Clientanwendungen verfügen.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Name der Ressourcengruppe, in der die Ressourcen des Azure Blockchain-Diensts erstellt werden. |
| **name** | Der Name des Blockchainmitglieds von Azure Blockchain mit dem Namen des neuen Transaktionsknotens. |
| **Speicherort** | Die Azure-Region, in der das Blockchainmitglied erstellt wird. Beispiel: `eastus`. Wählen Sie den Standort aus, der Ihren Benutzern oder anderen Azure-Anwendungen am nächsten liegt. |
| **password** | Das Kennwort des Transaktionsknotens. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)). |
| **ruleName** | Regelname zum Hinzufügen eines IP-Adressbereichs zu einer Whitelist. Optionaler Parameter für Firewallregeln. |
| **startIpAddress** | Anfang des IP-Adressbereichs, der der Whitelist hinzugefügt werden soll. Optionaler Parameter für Firewallregeln. |
| **endIpAddress** | Ende des IP-Adressbereichs, der der Whitelist hinzugefügt werden soll. Optionaler Parameter für Firewallregeln.|

## <a name="change-transaction-node-password"></a>Ändern des Kennworts des Transaktionsknotens

Im folgenden Beispiel wird das Kennwort des Transaktionsknotens geändert.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds von Azure Blockchain mit dem Namen des neuen Transaktionsknotens. |
| **password** | Das Kennwort des Transaktionsknotens. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)). |

## <a name="change-consortium-management-account-password"></a>Ändern des Verwaltungskontokennworts des Konsortiums

Das Verwaltungskonto des Konsortiums wird zur Verwaltung der Mitgliedschaften im Konsortium verwendet. Jedes Mitglied wird eindeutig durch ein Konsortiumverwaltungskonto identifiziert. Sie können das Kennwort des Kontos mit dem folgenden Befehl ändern:

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Name der Ressourcengruppe, in der die Ressourcen des Azure Blockchain-Diensts erstellt werden. |
| **name** | Der Name, der Ihr Azure Blockchain-Mitglied identifiziert. |
| **consortiumManagementAccountPassword** | Das Verwaltungskontokennwort. des Konsortiums. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)). |
  
## <a name="update-firewall-rules"></a>Aktualisieren von Firewallregeln

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds im Azure Blockchain-Dienst. |
| **ruleName** | Regelname zum Hinzufügen eines IP-Adressbereichs zu einer Whitelist. Optionaler Parameter für Firewallregeln.|
| **startIpAddress** | Anfang des IP-Adressbereichs, der der Whitelist hinzugefügt werden soll. Optionaler Parameter für Firewallregeln.|
| **endIpAddress** | Ende des IP-Adressbereichs, der der Whitelist hinzugefügt werden soll. Optionaler Parameter für Firewallregeln.|

## <a name="list-api-keys"></a>Auflisten von API-Schlüsseln

Mit API-Schlüsseln erhalten Sie Zugriff auf Knoten, ähnlich wie mit einem Benutzernamen und einem Kennwort. Es gibt zwei API-Schlüssel, die die Schlüsselrotation unterstützen. Verwenden Sie den folgenden Befehl, um Ihre API-Schlüssel aufzulisten:

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds von Azure Blockchain mit dem Namen des neuen Transaktionsknotens. |

## <a name="regenerate-api-keys"></a>Erneutes Generieren von API-Schlüsseln

Verwenden Sie den folgenden Befehl, um Ihre API-Schlüssel erneut zu generieren:

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds von Azure Blockchain mit dem Namen des neuen Transaktionsknotens. |
| **keyName** | Ersetzen Sie \<keyValue\> durch „key1“ oder „key2“. |

## <a name="delete-a-transaction-node"></a>Löschen eines Transaktionsknotens

Im folgenden Beispiel wird der Transaktionsknoten des Blockchainmitglieds gelöscht.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds von Azure Blockchain mit dem Namen des neuen Transaktionsknotens, der gelöscht werden soll. |

## <a name="delete-a-blockchain-member"></a>Löschen eines Blockchainmitglieds

Im folgenden Beispiel wird das Blockchainmitglied gelöscht.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds im Azure Blockchain-Dienst, das gelöscht werden soll. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Gewähren des Zugriffs für einen Azure AD-Benutzer

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **role** | Der Name der Azure AD-Rolle. |
| **assignee** | Die Azure AD-Benutzer-ID. Zum Beispiel, `user@contoso.com` |
| **scope** | Der Geltungsbereich der Rollenzuweisung. Dies kann entweder das Blockchainmitglied oder der Transaktionsknoten sein. |

**Beispiel:**

Gewähren Sie einem Azure AD-Benutzer Knotenzugriff auf ein **Blockchainmitglied**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Beispiel:**

Gewähren Sie einem Azure AD-Benutzer Knotenzugriff auf einen **Blockchaintransaktionsknoten**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Gewähren des Knotenzugriffs für Azure AD-Gruppen oder Anwendungsrollen

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parameter | BESCHREIBUNG |
|---------|-------------|
| **role** | Der Name der Azure AD-Rolle. |
| **assignee-object-id** | Die Azure AD-Gruppen-ID oder -Anwendungs-ID. |
| **scope** | Der Geltungsbereich der Rollenzuweisung. Dies kann entweder das Blockchainmitglied oder der Transaktionsknoten sein. |

**Beispiel:**

Gewähren des Knotenzugriffs für **Anwendungsrollen**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Entfernen des Azure AD-Knotenzugriffs

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **role** | Der Name der Azure AD-Rolle. |
| **assignee** | Die Azure AD-Benutzer-ID. Zum Beispiel, `user@contoso.com` |
| **scope** | Der Geltungsbereich der Rollenzuweisung. Dies kann entweder das Blockchainmitglied oder der Transaktionsknoten sein. |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren von Azure Blockchain-Transaktionsknoten mit dem Azure-Portal](configure-transaction-nodes.md)
