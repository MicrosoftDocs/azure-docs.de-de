---
title: Programmgesteuertes Erstellen von Azure Enterprise Agreement-Abonnements mit den neuesten APIs
description: Hier erfahren Sie, wie Sie mithilfe der neuesten Versionen der REST-API, der Azure CLI und von Azure PowerShell programmgesteuert Azure Enterprise Agreement-Abonnements erstellen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0cdd25b2937dd1fb2cc70ef7b1c5a9e9ddaef375
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780602"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Programmgesteuertes Erstellen von Azure Enterprise Agreement-Abonnements mit den neuesten APIs

In diesem Artikel wird erläutert, wie Sie mithilfe der aktuellen API-Versionen programmgesteuert Azure Enterprise Agreement-Abonnements für ein EA-Abrechnungskonto erstellen. Wenn Sie noch die ältere Vorschauversion verwenden, finden Sie weitere Informationen unter [Programmgesteuertes Erstellen von Azure-Abonnements mit Vorschau-APIs](programmatically-create-subscription-preview.md). 

Dieser Artikel enthält Informationen zum programmgesteuerten Erstellen von Abonnements mithilfe von Azure Resource Manager.

Wenn Sie ein Azure-Abonnement programmgesteuert erstellen, unterliegt es der Vereinbarung, auf deren Grundlage Sie Azure-Dienste von Microsoft oder einem autorisierten Handelspartner erhalten haben. Weitere Informationen finden Sie unter [Rechtliche Hinweise zu Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen über die Rolle „Besitzer“ für ein Registrierungskonto verfügen, um ein Abonnement zu erstellen. Es gibt zwei Möglichkeiten, wie Sie die Rolle erhalten:

* Der Enterprise-Administrator Ihrer Registrierung kann [Sie zum Kontobesitzer machen](https://ea.azure.com/helpdocs/addNewAccount) (Anmeldung erforderlich), damit Sie zum Besitzer des Registrierungskontos werden.
* Ein vorhandener Besitzer des Registrierungskontos kann [Ihnen Zugriff gewähren](grant-access-to-create-subscription.md). Wenn Sie einen Dienstprinzipal verwenden möchten, um ein EA-Abonnement zu erstellen, müssen Sie entsprechend [diesem Dienstprinzipal die Berechtigung zum Erstellen von Abonnements erteilen](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Ermitteln der Konten, auf die Sie Zugriff besitzen

Nachdem Sie einem Registrierungskonto hinzugefügt wurden, das einem Kontobesitzer zugeordnet ist, verwendet Azure die Beziehung zwischen dem Konto und der Registrierung für die Ermittlung, wo die Abonnementgebühren angerechnet werden sollen. Alle Abonnements, die unter dem Konto erstellt werden, werden über die EA-Registrierung abgerechnet, in der sich das Konto befindet. Um Abonnements zu erstellen, müssen Sie Werte zum Registrierungskonto und den Benutzerprinzipalen als Besitzer des Abonnements übergeben.

Um die folgenden Befehle ausführen zu können, müssen Sie im *Basisverzeichnis* des Kontobesitzers angemeldet sein. Dies ist das Verzeichnis, in dem Abonnements standardmäßig erstellt werden.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

Fordern Sie eine Liste aller Registrierungskonten an, auf die Sie zugreifen können:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

In der API-Antwort sind alle Registrierungskonten aufgelistet, auf die Sie Zugriff haben:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Der Wert für einen Abrechnungsbereich und `id` sind identisch. Die ID (`id`) für Ihr Registrierungskonto ist der Abrechnungsbereich, unter dem die Abonnementanforderung initiiert wird. Es ist wichtig, die ID zu kennen, da sie ein erforderlicher Parameter ist, den Sie später im Artikel zum Erstellen eines Abonnements verwenden.

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Erstellen von Abonnements unter einem bestimmten Registrierungskonto

Im folgenden Beispiel wird ein Abonnement namens *Dev Team Subscription* in Registrierungskonto erstellt, das im vorherigen Schritt ausgewählt wurde. 

### <a name="rest"></a>[REST](#tab/rest-EA)

Rufen Sie die PUT-API auf, um eine Anforderung bzw. einen Alias für die Abonnementerstellung zu generieren.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

Geben Sie im Anforderungstext die ID (`id`) eines Ihrer Registrierungskonten (`enrollmentAccounts`) als `billingScope` an.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Zulässige Werte für `Workload` sind `Production` und `DevTest`.

#### <a name="response"></a>Antwort

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

Sie können eine GET-Anforderung für die gleiche URL ausführen, um den Status der Anforderung zu erhalten.

### <a name="request"></a>Anforderung

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Antwort

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Der Status „In Bearbeitung“ wird unter `provisioningState` als Status `Accepted` zurückgegeben.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

Führen Sie `Install-Module Az.Subscription` aus, um die neueste Version des Moduls zu installieren, das das Cmdlet `New-AzSubscriptionAlias` enthält. Eine aktuelle Version von PowerShellGet finden Sie unter [Abrufen des PowerShellGet-Moduls](/powershell/scripting/gallery/installing-psget).

Führen Sie den folgenden Befehl [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) aus, und verwenden Sie dabei den Abrechnungsbereich `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
```

Sie erhalten die Abonnement-ID (subscriptionId) als Teil der Befehlsantwort.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli-EA)

Installieren Sie zunächst die Erweiterung, indem Sie `az extension add --name account` und `az extension add --name alias` ausführen.

Führen Sie den folgenden Befehl [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) aus, und geben Sie `billing-scope` und `id` von einem Ihrer Registrierungskonten (`enrollmentAccounts`) an. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

Sie erhalten die Abonnement-ID (subscriptionId) als Teil der Befehlsantwort.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Einschränkungen der Azure Enterprise-Abonnementerstellungs-API

- Nur Azure Enterprise-Abonnements werden mithilfe der API erstellt.
- Pro Registrierungskonto sind maximal 2.000 Abonnements zulässig. Im Anschluss können weitere Abonnements für das Konto nur über das Azure-Portal erstellt werden. Wenn Sie über die API weitere Abonnements erstellen möchten, erstellen Sie ein weiteres Registrierungskonto. Abgebrochene, gelöschte und übertragene Abonnements werden auf den Grenzwert von 2.000 angerechnet.
- Benutzer, die keine Kontobesitzer sind, aber per Azure RBAC einem Registrierungskonto hinzugefügt wurden, können über das Azure-Portal keine Abonnements erstellen.
- Sie können den Mandanten nicht auswählen, in dem das Abonnement erstellt werden soll. Das Abonnement wird immer im Basismandanten des Kontobesitzers erstellt. Wenn Sie das Abonnement in einen anderen Mandanten verschieben möchten, finden Sie weitere Informationen unter [Ändern des Abonnementmandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Nächste Schritte

* Da Sie nun ein Abonnement erstellt haben, können Sie diese Möglichkeit auch für andere Benutzer und Dienstprinzipale eröffnen. Weitere Informationen finden Sie unter [Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements (Vorschau) ](grant-access-to-create-subscription.md).
* Weitere Informationen zum Verwalten einer großen Anzahl von Abonnements mithilfe von Verwaltungsgruppen finden Sie unter [Was sind Azure-Verwaltungsgruppen?](../../governance/management-groups/overview.md).
