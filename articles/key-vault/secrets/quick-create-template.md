---
title: 'Azure-Schnellstart: Erstellen eines Azure-Schlüsseltresors und eines -Geheimnisses per Azure Resource Manager-Vorlage | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie Azure-Schlüsseltresore erstellen und den Tresoren Geheimnisse hinzufügen, indem Sie die Azure Resource Manager-Vorlage verwenden.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 68c10ba5581ffe404e8e3c1ac8ce92832cfcd6c4
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896428"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Schnellstart: Festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mit einer ARM-Vorlage

[Azure Key Vault](../general/overview.md) ist ein Clouddienst, der einen sicheren Speicher für Geheimnisse bereitstellt, z. B. für Schlüssel, Kennwörter, Zertifikate usw. In dieser Schnellstartanleitung geht es die Bereitstellung einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen eines Schlüsseltresors und eines Geheimnisses.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie für diesen Artikel die folgenden Schritte aus:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Ihre Azure AD-Benutzerobjekt-ID wird von der Vorlage zum Konfigurieren von Berechtigungen benötigt. Die folgende Prozedur ruft die Objekt-ID (GUID) ab.

    1. Führen Sie den folgenden Azure PowerShell- oder Azure CLI-Befehl aus, indem Sie die Option **Ausprobieren** wählen und das Skript anschließend in den Shell-Bereich einfügen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus.

        # <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Notieren Sie sich die Objekt-ID. Sie benötigen sie im nächsten Abschnitt dieser Schnellstartanleitung.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults) zum Erstellen eines Azure-Schlüsseltresors
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets) zum Erstellen eines Schlüsseltresorgeheimnisses.

Weitere Vorlagenbeispiele für Azure Key Vault finden Sie in [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt einen Schlüsseltresor und ein Geheimnis.

    [![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    ![ARM-Vorlage, Key Vault-Integration, Bereitstellen (Portal)](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Verwenden Sie den Standardwert, um den Schlüsseltresor und ein Geheimnis zu erstellen, sofern kein anderer Wert angegeben ist.

    * **Abonnement** : Wählen Sie ein Azure-Abonnement aus.
    * **Ressourcengruppe** : Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und klicken Sie dann auf **OK**.
    * **Standort** : Wählen Sie einen Standort aus. Beispiel: **USA, Mitte**.
    * **Schlüsseltresorname** : Geben Sie einen Namen für den Schlüsseltresor ein. Dieser muss im Namespace „.vault.azure.net“ global eindeutig sein. Sie benötigen den Namen im nächsten Abschnitt beim Überprüfen der Bereitstellung.
    * **Mandanten-ID** : Die Vorlagenfunktion ruft automatisch Ihre Mandanten-ID ab. Lassen Sie den Standardwert unverändert.
    * **Ad User Id** (AD-Benutzer-ID): Geben Sie Ihre Azure AD-Benutzerobjekt-ID ein, die Sie unter [Voraussetzungen](#prerequisites) abgerufen haben.
    * **Geheimnisname** : Geben Sie einen Namen für das Geheimnis ein, das Sie im Schlüsseltresor speichern. Beispiel: **adminpassword**.
    * **Geheimniswert** : Geben Sie den Geheimniswert ein. Beim Speichern eines Kennworts wird die Verwendung des generierten Kennworts empfohlen, das Sie unter „Voraussetzungen“ erstellt haben.
    * **Ich stimme den oben genannten Geschäftsbedingungen zu** : Aktivieren Sie dieses Kontrollkästchen.
3. Wählen Sie die Option **Kaufen**. Nach der erfolgreichen Bereitstellung des Schlüsseltresors erhalten Sie eine Benachrichtigung:

    ![ARM-Vorlage, Key Vault-Integration, Bereitstellen (Portalbenachrichtigung)](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Zum Bereitstellen der Vorlage wird das Azure-Portal verwendet. Neben dem Azure-Portal können Sie auch Azure PowerShell, die Azure-Befehlszeilenschnittstelle (Azure CLI) und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können entweder das Azure-Portal nutzen, um den Schlüsseltresor und das Geheimnis zu überprüfen, oder das unten angegebene Azure CLI- oder Azure PowerShell-Skript, um das erstellte Geheimnis aufzulisten.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Die Ausgabe sieht in etwa wie folgt aus:

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

![Screenshot, der die Ausgabe der Überprüfung der Portalbereitstellung zeigt.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM-Vorlage, Key Vault-Integration, Bereitstellen (Portal), Überprüfungsausgabe](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen und Tutorials für Key Vault bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.
Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Key Vault-Instanz und die dazugehörigen Ressourcen gelöscht. Die Ressourcengruppe kann über die Azure-Befehlszeilenschnittstelle oder mithilfe von Azure PowerShell gelöscht werden:

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe einer ARM-Vorlage einen Schlüsseltresor und ein Geheimnis erstellt und die Bereitstellung überprüft. Weitere Informationen zu Key Vault und Azure Resource Manager finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Lesen Sie weitere Informationen zu [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
