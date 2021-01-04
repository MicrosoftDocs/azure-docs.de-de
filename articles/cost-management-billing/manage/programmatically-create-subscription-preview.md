---
title: Programmgesteuertes Erstellen von Azure-Abonnements mit Vorschau-APIs
description: Hier erfahren Sie, wie Sie mithilfe von Vorschauversionen der REST-API, der Azure CLI und von Azure PowerShell programmgesteuert zusätzliche Azure-Abonnements erstellen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 68d890386d53b4115c773b128f8678bac9579e53
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844334"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Programmgesteuertes Erstellen von Azure-Abonnements mit Vorschau-APIs

In diesem Artikel wird erläutert, wie Sie mithilfe der älteren Vorschau-API programmgesteuert Azure-Abonnements erstellen. Dieser Artikel enthält Informationen zum programmgesteuerten Erstellen von Abonnements mithilfe von Azure Resource Manager.

Es gibt neue Artikel für die aktuelle API-Version zur Verwendung mit verschiedenen Abonnementtypen für Azure-Vereinbarungen:

- [Programmgesteuertes Erstellen von EA-Abonnements mit der aktuellen API](programmatically-create-subscription-enterprise-agreement.md)
- [Programmgesteuertes Erstellen von MCA-Abonnements mit der aktuellen API](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Programmgesteuertes Erstellen von MPA-Abonnements mit der aktuellen API](Programmatically-create-subscription-microsoft-customer-agreement.md)

Wenn Sie nicht die neueste API-Version nutzen möchten, können Sie jedoch weiterhin die Informationen in diesem Artikel verwenden.

Azure-Kunden mit einem Abrechnungskonto für die folgenden Vereinbarungstypen können Abonnements programmgesteuert erstellen:

- Enterprise Agreement
- Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA)
- Microsoft-Partnervereinbarung (MPA)

Wenn Sie ein Azure-Abonnement programmgesteuert erstellen, unterliegt es der Vereinbarung, auf deren Grundlage Sie Azure-Dienste von Microsoft oder einem autorisierten Handelspartner erhalten haben. Weitere Informationen finden Sie unter [Rechtliche Hinweise zu Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Erstellen von Abonnements für ein Abrechnungskonto vom Typ „EA“

Verwenden Sie die Informationen in den folgenden Abschnitten, um EA-Abonnements zu erstellen.

### <a name="prerequisites"></a>Voraussetzungen

Sie müssen über die Rolle „Besitzer“ für ein Registrierungskonto verfügen, um ein Abonnement zu erstellen. Es gibt zwei Möglichkeiten, wie Sie die Rolle erhalten:

* Der Enterprise-Administrator Ihrer Registrierung kann [Sie zum Kontobesitzer machen](https://ea.azure.com/helpdocs/addNewAccount) und die Anmeldung als erforderlich festlegen, damit Sie zum Besitzer des Registrierungskontos werden.
* Ein vorhandener Besitzer des Registrierungskontos kann [Ihnen Zugriff gewähren](grant-access-to-create-subscription.md). Wenn Sie einen Dienstprinzipal verwenden möchten, um ein EA-Abonnement zu erstellen, müssen Sie entsprechend [diesem Dienstprinzipal die Berechtigung zum Erstellen von Abonnements erteilen](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Ermitteln der Konten, auf die Sie Zugriff besitzen

Nachdem Sie einem Registrierungskonto hinzugefügt wurden, das einem Kontobesitzer zugeordnet ist, verwendet Azure die Beziehung zwischen dem Konto und der Registrierung für die Ermittlung, wo die Abonnementgebühren angerechnet werden sollen. Alle Abonnements, die unter dem Konto erstellt werden, werden über die EA-Registrierung abgerechnet, in der sich das Konto befindet. Um Abonnements zu erstellen, müssen Sie Werte zum Registrierungskonto und den Benutzerprinzipalen als Besitzer des Abonnements übergeben.

Um die folgenden Befehle ausführen zu können, müssen Sie beim *Basisverzeichnis* des Kontobesitzers angemeldet sein. Dies ist das Verzeichnis, in dem Abonnements standardmäßig erstellt werden.

### <a name="rest"></a>[REST](#tab/rest)

Fordern Sie eine Liste aller Registrierungskonten an, auf die Sie zugreifen können:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

In der API-Antwort sind alle Registrierungskonten aufgelistet, auf die Sie Zugriff haben:

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

Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, in dem Abonnements abgerechnet werden sollen. Kopieren Sie den `name`-Wert dieses Kontos. Wenn Sie beispielsweise Abonnements unter dem Registrierungskonto SignUpEngineering@contoso.com erstellen möchten, kopieren Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Der Bezeichner ist die Objekt-ID des Registrierungskontos. Halten Sie den Wert zur Verwendung als `enrollmentAccountObjectId` im nächsten Schritt bereit.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Öffnen Sie [Azure Cloud Shell](https://shell.azure.com/), und wählen Sie PowerShell aus.

Verwenden Sie das Cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) zum Auflisten aller Registrierungskonten, auf die Sie zugreifen können.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure gibt eine Liste aller Registrierungskonten zurück, auf die Sie zugreifen können:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, in dem Abonnements abgerechnet werden sollen. Kopieren Sie den `ObjectId`-Wert dieses Kontos. Wenn Sie beispielsweise Abonnements unter dem Registrierungskonto SignUpEngineering@contoso.com erstellen möchten, kopieren Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Halten Sie die Objekt-ID zur Verwendung als `enrollmentAccountObjectId` im nächsten Schritt bereit.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az billing enrollment-account list](/cli/azure/billing) zum Auflisten aller Registrierungskonten, auf die Sie zugreifen können.

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

Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, in dem Abonnements abgerechnet werden sollen. Kopieren Sie den `name`-Wert dieses Kontos. Wenn Sie beispielsweise Abonnements unter dem Registrierungskonto SignUpEngineering@contoso.com erstellen möchten, kopieren Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Der Bezeichner ist die Objekt-ID des Registrierungskontos. Halten Sie den Wert zur Verwendung als `enrollmentAccountObjectId` im nächsten Schritt bereit.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Erstellen von Abonnements unter einem bestimmten Registrierungskonto

Im folgenden Beispiel wird ein Abonnement namens *Dev Team Subscription* in Registrierungskonto erstellt, das im vorherigen Schritt ausgewählt wurde. Bei dem Abonnementangebot handelt es sich um *MS-AZR-0017P* (reguläres Microsoft Enterprise Agreement). Es werden optional auch zwei Benutzer als Azure RBAC-Besitzer für das Abonnement hinzugefügt.

### <a name="rest"></a>[REST](#tab/rest)

Führen Sie die folgende Anforderung aus, und ersetzen Sie dabei `<enrollmentAccountObjectId>` durch den `name`-Wert, den Sie im ersten Schritt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopiert haben. Informationen zum Festlegen von Besitzern finden Sie [hier](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Elementname  | Erforderlich | type   | BESCHREIBUNG                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nein      | String | Der Anzeigename des Abonnements. Wenn kein Anzeigename angegeben wird, wird er auf den Namen des Angebots festgelegt, z.B. auf „Microsoft Azure Enterprise“.                                 |
| `offerType`   | Ja      | String | Das Angebot des Abonnements. Die beiden Optionen für EA sind [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Verwendung in der Produktion) und [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muss [mit dem EA-Portal aktiviert werden](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nein       | String | Die Objekt-ID eines Benutzers, den Sie als Azure RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird  |

In der Antwort wird als Teil des Headers `Location` eine URL zurückgegeben, über die Sie den Status des Erstellens des Abonnements abfragen können. Nach Abschluss der Abonnementerstellung wird bei einem GET-Vorgang für die URL vom Typ `Location` ein Objekt vom Typ `subscriptionLink` zurückgegeben, das die Abonnement-ID enthält. Ausführlichere Informationen finden Sie in der [Dokumentation zur Abonnement-API](/rest/api/subscription/).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Führen Sie `Install-Module Az.Subscription` aus, um die neueste Version des Moduls zu installieren, das das Cmdlet `New-AzSubscription` enthält. Eine aktuelle Version von PowerShellGet finden Sie unter [Abrufen des PowerShellGet-Moduls](/powershell/scripting/gallery/installing-psget).

Führen Sie den folgenden [New-AzSubscription](/powershell/module/az.subscription)-Befehl aus, und ersetzen Sie dabei `<enrollmentAccountObjectId>` durch den `ObjectId`-Wert, den Sie im ersten Schritt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) abgerufen haben. Informationen zum Festlegen von Besitzern finden Sie [hier](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elementname  | Erforderlich | type   | BESCHREIBUNG |
|---------------|----------|--------|----|
| `Name` | Nein      | String | Der Anzeigename des Abonnements. Wenn kein Anzeigename angegeben wird, wird er auf den Namen des Angebots festgelegt, z. B. auf *Microsoft Azure Enterprise*. |
| `OfferType`   | Ja      | String | Das Abonnementangebot. Die beiden Optionen für EA sind [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Verwendung in der Produktion) und [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muss [mit dem EA-Portal aktiviert werden](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | String | Die Objekt-ID des Registrierungskontos, unter dem das Abonnement erstellt und abgerechnet wird. Der Wert ist eine GUID, die Sie von `Get-AzEnrollmentAccount` abrufen. |
| `OwnerObjectId`      | Nein       | String | Die Objekt-ID eines Benutzers, den Sie als Azure RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird  |
| `OwnerSignInName`    | Nein       | String | Die E-Mail-Adresse eines Benutzers, den Sie als Azure RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können den Parameter anstelle von `OwnerObjectId` verwenden.|
| `OwnerApplicationId` | Nein       | String | Die Anwendungs-ID eines Dienstprinzipals, den Sie als Azure RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können den Parameter anstelle von `OwnerObjectId` verwenden. Wenn Sie den Parameter verwenden, benötigt der Dienstprinzipal [Lesezugriff auf das Verzeichnis](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Wenn Sie eine vollständige Liste aller Parameter anzeigen möchten, finden Sie weitere Informationen unter [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installieren Sie zunächst die Vorschauerweiterung, indem Sie `az extension add --name subscription` ausführen.

Führen Sie den folgenden [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true)-Befehl aus, und ersetzen Sie dabei `<enrollmentAccountObjectId>` durch den `name`-Wert, den Sie im ersten Schritt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopiert haben. Informationen zum Festlegen von Besitzern finden Sie [hier](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elementname  | Erforderlich | type   | BESCHREIBUNG |
|---------------|----------|--------|------------|
| `display-name` | Nein      | String | Der Anzeigename des Abonnements. Wenn kein Anzeigename angegeben wird, wird er auf den Namen des Angebots festgelegt, z. B. auf *Microsoft Azure Enterprise*.|
| `offer-type`   | Ja      | String | Das Angebot des Abonnements. Die beiden Optionen für EA sind [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Verwendung in der Produktion) und [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muss [mit dem EA-Portal aktiviert werden](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | String | Die Objekt-ID des Registrierungskontos, unter dem das Abonnement erstellt und abgerechnet wird. Der Wert ist eine GUID, die Sie von `az billing enrollment-account list` abrufen. |
| `owner-object-id`      | Nein       | String | Die Objekt-ID eines Benutzers, den Sie als Azure RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird  |
| `owner-upn`    | Nein       | String | Die E-Mail-Adresse eines Benutzers, den Sie als Azure RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können den Parameter anstelle von `owner-object-id` verwenden.|
| `owner-spn` | Nein       | String | Die Anwendungs-ID eines Dienstprinzipals, den Sie als Azure RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können den Parameter anstelle von `owner-object-id` verwenden. Wenn Sie den Parameter verwenden, benötigt der Dienstprinzipal [Lesezugriff auf das Verzeichnis](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Wenn Sie eine vollständige Liste aller Parameter anzeigen möchten, finden Sie weitere Informationen unter [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Einschränkungen der Azure Enterprise-Abonnementerstellungs-API

- Nur Azure Enterprise-Abonnements können mithilfe der API erstellt werden.
- Pro Registrierungskonto sind maximal 2.000 Abonnements zulässig. Im Anschluss können weitere Abonnements für das Konto nur über das Azure-Portal erstellt werden. Wenn Sie über die API weitere Abonnements erstellen möchten, erstellen Sie ein weiteres Registrierungskonto. Abgebrochene, gelöschte und übertragene Abonnements werden auf den Grenzwert von 2.000 angerechnet.
- Benutzer, die keine Kontobesitzer sind, aber per Azure RBAC einem Registrierungskonto hinzugefügt wurden, können über das Azure-Portal keine Abonnements erstellen.
- Sie können den Mandanten nicht auswählen, in dem das Abonnement erstellt werden soll. Das Abonnement wird immer im Basismandanten des Kontobesitzers erstellt. Wenn Sie das Abonnement in einen anderen Mandanten verschieben möchten, finden Sie weitere Informationen unter [Ändern des Abonnementmandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Erstellen von Abonnements für ein Konto vom Typ „MCA“

Verwenden Sie die Informationen in den folgenden Abschnitten, um Abonnements für ein MCA-Konto zu erstellen.

### <a name="prerequisites"></a>Voraussetzungen

Sie müssen über die Rolle „Besitzer“, „Mitwirkender“ oder „Azure-Abonnementersteller“ für einen Rechnungsabschnitt oder die Rolle „Besitzer“ oder „Mitwirkender“ für ein Abrechnungsprofil oder -konto verfügen, um Abonnements erstellen zu können. Weitere Informationen finden Sie unter [Rollen und Aufgaben für die Abonnementabrechnung](understand-mca-roles.md#subscription-billing-roles-and-tasks).

In den folgenden Beispielen werden REST-APIs verwendet. PowerShell und Azure CLI werden derzeit nicht unterstützt.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Suchen nach Abrechnungskonten, auf die Sie Zugriff haben

Verwenden Sie die folgende Anforderung, um alle Abrechnungskonten aufzulisten:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
In der API-Antwort sind die Abrechnungskonten aufgeführt, auf die Sie Zugriff haben.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Verwenden Sie die `displayName`-Eigenschaft, um das Abrechnungskonto zu identifizieren, für das Sie Abonnements erstellen möchten. Vergewissern Sie sich, dass als „agreementType“ des Kontos die Option *MicrosoftCustomerAgreement* festgelegt ist. Kopieren Sie den Namen (`name`) des Kontos.  Wenn Sie beispielsweise ein Abonnement für das Abrechnungskonto `Contoso` erstellen möchten, kopieren Sie `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Halten Sie den Wert für den nächsten Schritt bereit.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Suchen nach Rechnungsabschnitten zum Erstellen von Abonnements

Die Gebühren für Ihr Abonnement werden in einem Abschnitt der Rechnung eines Abrechnungsprofils angezeigt. Verwenden Sie die folgende API, um die Liste mit den Rechnungsabschnitten und Abrechnungsprofilen abzurufen, für die Sie über die Berechtigung zum Erstellen von Azure-Abonnements verfügen.

Führen Sie die folgende Anforderung aus, und ersetzen Sie dabei `<billingAccountName>` durch den `name`-Wert, den Sie im ersten Schritt (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) kopiert haben.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

In der API-Antwort sind alle Rechnungsabschnitte und die zugehörigen Abrechnungsprofile aufgelistet, für die Sie über Zugriff zum Erstellen von Abonnements verfügen:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Verwenden Sie die `invoiceSectionDisplayName`-Eigenschaft, um den Rechnungsabschnitt zu identifizieren, für den Sie Abonnements erstellen möchten. Kopieren Sie die Elemente `invoiceSectionId` und `billingProfileId` sowie eines der `skuId`-Elemente für den Rechnungsabschnitt. Wenn Sie beispielsweise ein Abonnement vom Typ `Microsoft Azure plan` für den Rechnungsabschnitt `Development` erstellen möchten, kopieren Sie `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` und `0001`. Halten Sie die Werte für den nächsten Schritt bereit.

### <a name="create-a-subscription-for-an-invoice-section"></a>Erstellen eines Abonnements für einen Rechnungsabschnitt

Im folgenden Beispiel wird ein Abonnement mit dem Namen *Dev Team subscription* vom Typ *Microsoft Azure Plan* für den Rechnungsabschnitt *Development* erstellt. Die Kosten für das Abonnement werden dem Abrechnungsprofil von *Contoso finance* berechnet und im Abschnitt *Development* der Rechnung aufgeführt.

Führen Sie die folgende Anforderung aus, und ersetzen Sie dabei `<invoiceSectionId>` durch den `invoiceSectionId`-Wert, den Sie im zweiten Schritt kopiert haben (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Übergeben Sie die Elemente `billingProfileId` und `skuId`, die Sie im zweiten Schritt in den Anforderungsparametern der API kopiert haben. Informationen zum Festlegen von Besitzern finden Sie [hier](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Elementname  | Erforderlich | type   | BESCHREIBUNG                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | String | Der Anzeigename des Abonnements.|
| `billingProfileId`   | Ja      | String | Die ID des Abrechnungsprofils, dem die Gebühren für das Abonnement berechnet werden  |
| `skuId` | Ja      | String | Die SKU-ID, die den Typ des Azure-Plans bestimmt. |
| `owners`      | Nein       | String | Die Objekt-ID eines Benutzers oder Dienstprinzipals, den Sie als Azure RBAC-Besitzer für das Abonnement bei der Erstellung hinzufügen möchten  |
| `costCenter` | Nein      | String | Die Kostenstelle, die dem Abonnement zugeordnet ist. Sie wird in der CSV-Datei zur Nutzung angezeigt. |
| `managementGroupId` | Nein      | String | Die ID der Verwaltungsgruppe, der das Abonnement hinzugefügt wird. Informationen zum Erhalt der Liste mit den Verwaltungsgruppen finden Sie im API-Artikel [Verwaltungsgruppen – Liste](/rest/api/resources/managementgroups/list). Verwenden Sie die ID einer Verwaltungsgruppe über die API. |

In der Antwort wird ein `subscriptionCreationResult`-Objekt für die Überwachung zurückgegeben. Nach Abschluss der Erstellung des Abonnements gibt das `subscriptionCreationResult`-Objekt ein `subscriptionLink`-Objekt zurück, das die Abonnement-ID enthält.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Erstellen von Abonnements für ein Abrechnungskonto vom Typ „MPA“

Verwenden Sie die Informationen in den folgenden Abschnitten, um Abonnements für ein MPA-Konto zu erstellen.

### <a name="prerequisites"></a>Voraussetzungen

Sie müssen im Cloudlösungsanbieter-Konto Ihrer Organisation über die Rolle „Globaler Administrator“ oder „Administrator-Agent“ verfügen, um ein Abonnement für Ihr Abrechnungskonto erstellen zu können. Weitere Informationen finden Sie unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview).

In den folgenden Beispielen werden REST-APIs verwendet. PowerShell und Azure CLI werden derzeit nicht unterstützt.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Suchen nach Abrechnungskonten, auf die Sie Zugriff haben

Verwenden Sie die unten angegebene Anforderung, um alle Abrechnungskonten aufzulisten, auf die Sie Zugriff haben.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

In der API-Antwort sind die Abrechnungskonten aufgeführt.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Verwenden Sie die `displayName`-Eigenschaft, um das Abrechnungskonto zu identifizieren, für das Sie Abonnements erstellen möchten. Vergewissern Sie sich, dass als „agreementType“ des Kontos die Option *MicrosoftPartnerAgreement* festgelegt ist. Kopieren Sie den Namen (`name`) für das Konto. Wenn Sie beispielsweise ein Abonnement für das Abrechnungskonto `Contoso` erstellen möchten, kopieren Sie `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Halten Sie den Wert für den nächsten Schritt bereit.

### <a name="find-customers-that-have-azure-plans"></a>Suchen nach Kunden mit Azure-Plänen

Senden Sie die folgende Anforderung, indem Sie `<billingAccountName>` durch das `name`-Element aus dem ersten Schritt (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) ersetzen, um alle Kunden unter dem Abrechnungskonto aufzulisten, für die Sie Azure-Abonnements erstellen können.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
In der API-Antwort sind die Kunden des Abrechnungskontos aufgelistet, die über Azure-Pläne verfügen. Sie können Abonnements für die Kunden erstellen.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Verwenden Sie die `displayName`-Eigenschaft, um den Kunden zu identifizieren, für den Sie Abonnements erstellen möchten. Kopieren Sie die `id` für den Kunden. Wenn Sie beispielsweise ein Abonnement für `Fabrikam toys` erstellen möchten, kopieren Sie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Halten Sie diesen Wert für spätere Schritte bereit.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Optional für indirekte Anbieter: Abrufen der Handelspartner für einen Kunden

Falls Sie im Rahmen des zweistufigen Cloudlösungsanbieter-Modells als indirekter Anbieter fungieren, können Sie einen Handelspartner angeben, während Sie Abonnements für Kunden erstellen.

Senden Sie die folgende Anforderung, indem Sie `<customerId>` durch die `id` aus dem zweiten Schritt (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) ersetzen, um alle Handelspartner aufzulisten, die für einen Kunden verfügbar sind.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
In der API-Antwort sind die Handelspartner für den Kunden aufgeführt:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```

Verwenden Sie die `description`-Eigenschaft, um den Handelspartner zu identifizieren, der dem Abonnement zugeordnet werden soll. Kopieren Sie die `resellerId` für den Handelspartner. Kopieren Sie `3xxxxx`, um beispielsweise `Wingtip` zuzuordnen. Halten Sie den Wert für den nächsten Schritt bereit.

### <a name="create-a-subscription-for-a-customer"></a>Erstellen eines Abonnements für einen Kunden

Im folgenden Beispiel wird ein Abonnement mit dem Namen *Dev Team subscription* für *Fabrikam toys* erstellt, und anschließend wird dem Abonnement der Handelspartner *Wingtip* zugeordnet. 

Führen Sie die folgende Anforderung aus, und ersetzen Sie dabei `<customerId>` durch den `id`-Wert, den Sie im zweiten Schritt kopiert haben (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Übergeben Sie die optionale *resellerId*, die Sie im zweiten Schritt in den Anforderungsparametern der API kopiert haben.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Elementname  | Erforderlich | type   | BESCHREIBUNG                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | String | Der Anzeigename des Abonnements.|
| `skuId` | Ja      | String | Die SKU-ID des Azure-Plans. Verwenden Sie für Abonnements vom Typ „Microsoft Azure-Plan“ die ID *0001*. |
| `resellerId`      | Nein       | String | Die MPN-ID des Handelspartners, der dem Abonnement zugeordnet wird.  |

In der Antwort wird ein `subscriptionCreationResult`-Objekt für die Überwachung zurückgegeben. Nach Abschluss der Erstellung des Abonnements gibt das `subscriptionCreationResult`-Objekt ein `subscriptionLink`-Objekt zurück. Dieses Objekt enthält die Abonnement-ID.

## <a name="next-steps"></a>Nächste Schritte

- Ein Beispiel zur Erstellung eines EA-Abonnements (Enterprise Agreement) per .NET finden Sie auf [dieser GitHub-Seite mit Beispielcode](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Da Sie nun ein Abonnement erstellt haben, können Sie diese Möglichkeit auch für andere Benutzer und Dienstprinzipale eröffnen. Weitere Informationen finden Sie unter [Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements (Vorschau) ](grant-access-to-create-subscription.md).
* Weitere Informationen zum Verwalten einer großen Anzahl von Abonnements mithilfe von Verwaltungsgruppen finden Sie unter [Was sind Azure-Verwaltungsgruppen?](../../governance/management-groups/overview.md).
