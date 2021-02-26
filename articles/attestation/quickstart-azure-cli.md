---
title: Einrichten von Azure Attestation mithilfe der Azure CLI
description: Hier erfahren Sie, wie Sie einen Nachweisanbieter mithilfe der Azure CLI einrichten und konfigurieren.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 4fc799c1c6dcaaa3ed4bc41c93bd6b786f51591c
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429244"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Schnellstart: Einrichten von Azure Attestation mithilfe der Azure CLI

Führen Sie erste Schritte mit [Azure Attestation unter Verwendung der Azure CLI](/cli/azure/ext/attestation/attestation?view=azure-cli-latest) aus.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="get-started"></a>Erste Schritte

1. Installieren Sie diese Erweiterung mit dem folgenden CLI-Befehl.

   ```azurecli
   az extension add --name attestation
   ```
   
1. Version überprüfen

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

   ```azurecli
   az login
   ```

1. Wechseln Sie bei Bedarf zu dem Abonnement für Azure Attestation:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registrieren Sie mit dem Befehl [az provider register](/cli/azure/provider#az_provider_register) den Ressourcenanbieter „Microsoft.Attestation“ im Abonnement:

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Weitere Informationen zu Azure-Ressourcenanbietern sowie zu ihrer Konfiguration und Verwaltung finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Sie müssen einen Ressourcenanbieter nur einmal für ein Abonnement registrieren.

1. Erstellen Sie eine Ressourcengruppe für den Nachweisanbieter. Sie können andere Azure-Ressourcen (einschließlich eines virtuellen Computers mit einer Clientanwendungsinstanz) in derselben Ressourcengruppe platzieren. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen, oder verwenden Sie eine vorhandene Ressourcengruppe:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Erstellen und Verwalten eines Nachweisanbieters

Die folgenden Befehle können Sie zum Erstellen und Verwalten des Nachweisanbieters verwenden:

1. Führen Sie den Befehl [az attestation create](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_create) aus, um einen Nachweisanbieter ohne Richtliniensignierungsanforderung zu erstellen:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Führen Sie den Befehl [az attestation show](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_show) aus, um Nachweisanbietereigenschaften wie „status“ und „AttestURI“ abzurufen:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Mit diesem Befehl werden Werte wie in der folgenden Ausgabe angezeigt:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Sie können einen Nachweisanbieter mithilfe des Befehls [az attestation delete](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_delete) löschen:

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Richtlinienverwaltung

Verwenden Sie die hier beschriebenen Befehle, um die Richtlinienverwaltung für einen Nachweisanbieter (jeweils ein Nachweistyp) zu ermöglichen.

Der Befehl [az attestation policy show](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_show) gibt die aktuelle Richtlinie für die angegebene TEE zurück:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> Der Befehl zeigt die Richtlinie sowohl im Text- als auch im JWT-Format an.

Folgende TEE-Typen werden unterstützt:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Mit dem Befehl [az attestation policy set](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_set) wird eine neue Richtlinie für den angegebenen Nachweistyp festgelegt.

So legen Sie die Richtlinie im Textformat für eine bestimmte Art von Nachweistyp mithilfe des Dateipfads fest:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

So legen Sie die Richtlinie im JWT-Format für eine bestimmte Art von Nachweistyp mithilfe des Dateipfads fest:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Implementieren von Nachweisen mit einer SGX-Enclave mithilfe von Codebeispielen](/samples/browse/?expanded=azure&terms=attestation)
