---
title: Einrichten von Azure Attestation mithilfe der Azure CLI
description: Hier erfahren Sie, wie Sie einen Nachweisanbieter mithilfe der Azure CLI einrichten und konfigurieren.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020941"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Schnellstart: Einrichten von Azure Attestation mithilfe der Azure CLI

Führen Sie erste Schritte mit Azure Attestation aus, indem Sie die Azure CLI zum Einrichten des Nachweises verwenden.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Erste Schritte

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

1. Führen Sie den Befehl [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) aus, um einen Nachweisanbieter zu erstellen:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Mit dem Parameter **--certs-input-path** wird eine Reihe vertrauenswürdiger Signaturschlüssel erstellt. Wenn Sie einen Dateinamen für diesen Parameter angeben, muss der Nachweisanbieter nur mit Richtlinien im signierten JWT-Format konfiguriert werden. Andernfalls kann die Richtlinie als Text oder nicht signiertes JWT konfiguriert werden. Weitere Informationen zu JWT finden Sie unter [Grundlegende Konzepte](basic-concepts.md). Zertifikatbeispiele finden Sie unter [Beispiele für ein Signaturgeberzertifikat einer Nachweisrichtlinie](policy-signer-examples.md).

1. Führen Sie den Befehl [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) aus, um Nachweisanbietereigenschaften wie „status“ und „AttestURI“ abzurufen:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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

Sie können einen Nachweisanbieter mithilfe des Befehls [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) löschen:

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Richtlinienverwaltung

Für die Verwaltung von Richtlinien benötigt ein Azure AD-Benutzer die folgenden Berechtigungen für `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Diese Berechtigungen können einem Azure AD-Benutzer über eine Rolle wie `Owner` (Platzhalterberechtigungen), `Contributor` (Platzhalterberechtigungen) oder `Attestation Contributor` (spezifische Berechtigungen nur für Azure Attestation) zugewiesen werden.  

Für das Lesen von Richtlinien benötigt ein Azure AD-Benutzer die folgenden Berechtigungen für `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`

Diese Berechtigung kann einem Azure AD-Benutzer über eine Rolle wie `Reader` (Platzhalterberechtigungen) oder `Attestation Reader` (spezifische Berechtigungen nur für Azure Attestation) zugewiesen werden.

Verwenden Sie die hier beschriebenen Befehle, um die Richtlinienverwaltung für einen Nachweisanbieter (jeweils eine TEE) zu ermöglichen.

Der Befehl [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) gibt die aktuelle Richtlinie für die angegebene TEE zurück:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> Der Befehl zeigt die Richtlinie sowohl im Text- als auch im JWT-Format an.

Folgende TEE-Typen werden unterstützt:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Mit dem Befehl [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) wird eine neue Richtlinie für die angegebene TEE festgelegt.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

Die Nachweisrichtlinie im JWT-Format muss einen Anspruch namens `AttestationPolicy` enthalten. Eine signierte Richtlinie muss mit einem Schlüssel signiert werden, der einem der vorhandenen Richtlinien-Signaturgeberzertifikate entspricht.

Richtlinienbeispiele finden Sie unter [Beispiele für eine Nachweisrichtlinie](policy-examples.md).

Mit dem Befehl [az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) wird eine neue Richtlinie für die angegebene TEE festgelegt.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Verwaltung der Signaturgeberzertifikate einer Richtlinie

Verwenden Sie die folgenden Befehle, um die Richtlinien-Signaturgeberzertifikate für einen Nachweisanbieter zu verwalten:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Ein Richtlinien-Signaturgeberzertifikat ist ein signiertes JWT mit einem Anspruch namens `maa-policyCertificate`. Der Wert des Anspruchs ist ein JWK, der den vertrauenswürdigen Signaturschlüssel enthält, der hinzugefügt werden soll. Das JWT muss mit einem privaten Schlüssel signiert werden, der einem der vorhandenen Richtlinien-Signaturgeberzertifikate entspricht. Weitere Informationen zu JWT und JWK finden Sie unter [Grundlegende Konzepte](basic-concepts.md).

Die gesamte semantische Bearbeitung des Richtlinien-Signaturgeberzertifikats muss außerhalb der Azure CLI erfolgen. In der Azure CLI handelt es sich um eine einfache Zeichenfolge.

Zertifikatbeispiele finden Sie unter [Beispiele für ein Signaturgeberzertifikat einer Nachweisrichtlinie](policy-signer-examples.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Implementieren von Nachweisen mit einer SGX-Enclave mithilfe von Codebeispielen](/samples/browse/?expanded=azure&terms=attestation)
