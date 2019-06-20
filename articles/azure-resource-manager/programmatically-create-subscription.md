---
title: Programmgesteuertes Erstellen von Azure Enterprise-Abonnements | Microsoft Docs
description: Erfahren Sie, wie Sie zusätzliche Azure Enterprise- oder Enterprise Dev/Test-Abonnements programmgesteuert erstellen können.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 7985451eb2bb5e9fd4fbcfb3d2fcf35149122c15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796076"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programmgesteuertes Erstellen von Azure Enterprise-Abonnements (Vorschau)

Als Azure-Kunde mit [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) können Sie EA- (MS-AZR-0017P) und EA Dev/Test-Abonnements (MS-AZR-0148P) programmgesteuert erstellen. Dieser Artikel enthält Informationen zum programmgesteuerten Erstellen von Abonnements mithilfe von Azure Resource Manager.

Wenn Sie über diese API Azure-Abonnements erstellen, unterliegen diese der Vereinbarung, nach der Sie Microsoft Azure-Dienste von Microsoft oder einem autorisierten Handelspartner erhalten haben. Weitere Informationen finden Sie unter [Rechtliche Informationen zu Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen über die Rolle „Besitzer“ in dem Registrierungskonto verfügen, unter dem Sie Abonnements erstellen möchten. Es gibt zwei Möglichkeiten, diese Rollen zu erhalten:

* Der Registrierungsadministrator kann [Sie zum Kontobesitzer machen](https://ea.azure.com/helpdocs/addNewAccount) (Anmeldung erforderlich), d.h. zum Besitzer des Registrierungskontos. Folgen Sie den Anweisungen in der Einladungs-E-Mail, die Sie erhalten, um manuell ein anfängliches Abonnement zu erstellen. Bestätigen Sie den Kontobesitz, und erstellen Sie manuell ein anfängliches EA-Abonnement, bevor Sie mit dem nächsten Schritt fortfahren. Es reicht nicht aus, das Konto einfach zur Registrierung hinzuzufügen.

* Ein vorhandener Besitzer des Registrierungskontos kann [Ihnen Zugriff gewähren](grant-access-to-create-subscription.md). Ebenso müssen Sie, wenn Sie einen Dienstprinzipal verwenden möchten, um das EA-Abonnement zu erstellen, [diesem Dienstprinzipal die Berechtigung zum Erstellen von Abonnements erteilen](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Ermitteln der Konten, auf die Sie Zugriff besitzen

Nachdem Sie einer Azure-EA-Registrierung als Kontobesitzer hinzugefügt wurden, verwendet Azure die Beziehung zwischen dem Konto und der Registrierung zum Ermitteln, wo die Abonnementgebühren angerechnet werden sollen. Alle Abonnements, die unter dem Konto erstellt werden, werden über die EA-Registrierung abgerechnet, in der sich das Konto befindet. Um Abonnements zu erstellen, müssen Sie Werte zum Registrierungskonto und den Benutzerprinzipalen als Besitzer des Abonnements übergeben. 

Um die folgenden Befehle ausführen zu können, müssen Sie im *Basisverzeichnis* des Kontobesitzers angemeldet sein. Dies ist das Verzeichnis, in dem Abonnements standardmäßig erstellt werden.

## <a name="resttabrest"></a>[REST](#tab/rest)

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

Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, in dem Abonnements abgerechnet werden sollen. Kopieren Sie den `name`-Wert dieses Kontos. Wenn Sie beispielsweise Abonnements unter dem Registrierungskonto SignUpEngineering@contoso.com erstellen wollten, müssten Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` kopieren. Dies ist die Objekt-ID des Registrierungskontos. Kopieren Sie diesen Wert, um ihn im nächsten Schritt als `enrollmentAccountObjectId`-Wert zu verwenden.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, in dem Abonnements abgerechnet werden sollen. Kopieren Sie den `ObjectId`-Wert dieses Kontos. Wenn Sie beispielsweise Abonnements unter dem Registrierungskonto SignUpEngineering@contoso.com erstellen wollten, müssten Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` kopieren. Kopieren Sie diese Objekt-ID, um sie im nächsten Schritt als `enrollmentAccountObjectId`-Wert zu verwenden.

## <a name="azure-clitabazure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) zum Auflisten aller Registrierungskonten, auf die Sie zugreifen können.

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

Verwenden Sie die `principalName`-Eigenschaft zum Identifizieren des Kontos, in dem Abonnements abgerechnet werden sollen. Kopieren Sie den `name`-Wert dieses Kontos. Wenn Sie beispielsweise Abonnements unter dem Registrierungskonto SignUpEngineering@contoso.com erstellen wollten, müssten Sie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` kopieren. Dies ist die Objekt-ID des Registrierungskontos. Kopieren Sie diesen Wert, um ihn im nächsten Schritt als `enrollmentAccountObjectId`-Wert zu verwenden.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Erstellen von Abonnements unter einem bestimmten Registrierungskonto

Im folgenden Beispiel wird ein Abonnement namens *Dev Team Subscription* in Registrierungskonto erstellt, das im vorherigen Schritt ausgewählt wurde. Bei dem Abonnementangebot handelt es sich um *MS-AZR-0017P* (reguläres Microsoft Enterprise Agreement). Es werden optional auch zwei Benutzer als RBAC-Besitzer für das Abonnement hinzugefügt.

# <a name="resttabrest"></a>[REST](#tab/rest)

Führen Sie die folgende Anforderung aus, und ersetzen Sie dabei `<enrollmentAccountObjectId>` durch den `name`-Wert, den Sie im ersten Schritt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopiert haben. Wie Sie Besitzer festlegen, erfahren Sie [hier](grant-access-to-create-subscription.md#userObjectId).

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

| Elementname  | Erforderlich | Typ   | BESCHREIBUNG                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nein      | string | Der Anzeigename des Abonnements. Wenn kein Anzeigename angegeben wird, wird er auf den Namen des Angebots festgelegt, z.B. auf „Microsoft Azure Enterprise“.                                 |
| `offerType`   | Ja      | string | Das Angebot des Abonnements. Die beiden Optionen für EA sind [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Verwendung in der Produktion) und [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muss [mit dem EA-Portal aktiviert werden](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nein       | string | Die Objekt-ID eines Benutzers, den Sie als RBAC Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird.  |

In der Antwort wird ein `subscriptionOperation`-Objekt für die Überwachung zurückgegeben. Nach Abschluss der Erstellung des Abonnements gibt das `subscriptionOperation`-Objekt ein `subscriptionLink`-Objekt zurück, das die Abonnement-ID enthält.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Installieren Sie zunächst das Vorschaumodul, indem Sie `Install-Module Az.Subscription -AllowPrerelease` ausführen. Um sicherzustellen, dass `-AllowPrerelease` funktioniert, installieren Sie eine aktuelle Version von PowerShellGet aus [PowerShellGet-Modul abrufen](/powershell/gallery/installing-psget).

Führen Sie den folgenden [New-AzSubscription](/powershell/module/az.subscription)-Befehl aus, und ersetzen Sie dabei `<enrollmentAccountObjectId>` durch den `ObjectId`-Wert, den Sie im ersten Schritt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) abgerufen haben. Wie Sie Besitzer festlegen, erfahren Sie [hier](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elementname  | Erforderlich | Typ   | BESCHREIBUNG                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nein      | string | Der Anzeigename des Abonnements. Wenn kein Anzeigename angegeben wird, wird er auf den Namen des Angebots festgelegt, z.B. auf „Microsoft Azure Enterprise“.                                 |
| `OfferType`   | Ja      | string | Das Angebot des Abonnements. Die beiden Optionen für EA sind [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Verwendung in der Produktion) und [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muss [mit dem EA-Portal aktiviert werden](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | string | Die Objekt-ID des Registrierungskontos, unter dem das Abonnement erstellt und abgerechnet wird. Dieser Wert ist eine GUID, die Sie von `Get-AzEnrollmentAccount` abrufen. |
| `OwnerObjectId`      | Nein       | string | Die Objekt-ID eines Benutzers, den Sie als RBAC Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird.  |
| `OwnerSignInName`    | Nein       | string | Die E-Mail-Adresse eines Benutzers, den Sie als RBAC Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können diesen Parameter anstelle von `OwnerObjectId` verwenden.|
| `OwnerApplicationId` | Nein       | string | Die Anwendungs-ID eines Dienstprinzipals, den Sie als RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können diesen Parameter anstelle von `OwnerObjectId` verwenden. Wenn Sie diesen Parameter verwenden zu können, benötigt das Dienstprinzipal [Lesezugriff auf das Verzeichnis](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Wenn Sie eine vollständige Liste aller Parameter anzeigen möchten, finden Sie weitere Informationen unter [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installieren Sie zunächst die Vorschauerweiterung, indem Sie `az extension add --name subscription` ausführen.

Führen Sie den folgenden [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)-Befehl aus, und ersetzen Sie dabei `<enrollmentAccountObjectId>` durch den `name`-Wert, den Sie im ersten Schritt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopiert haben. Wie Sie Besitzer festlegen, erfahren Sie [hier](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elementname  | Erforderlich | Typ   | BESCHREIBUNG                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nein      | string | Der Anzeigename des Abonnements. Wenn kein Anzeigename angegeben wird, wird er auf den Namen des Angebots festgelegt, z.B. auf „Microsoft Azure Enterprise“.                                 |
| `offer-type`   | Ja      | string | Das Angebot des Abonnements. Die beiden Optionen für EA sind [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Verwendung in der Produktion) und [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muss [mit dem EA-Portal aktiviert werden](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | string | Die Objekt-ID des Registrierungskontos, unter dem das Abonnement erstellt und abgerechnet wird. Dieser Wert ist eine GUID, die Sie von `az billing enrollment-account list` abrufen. |
| `owner-object-id`      | Nein       | string | Die Objekt-ID eines Benutzers, den Sie als RBAC Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird.  |
| `owner-upn`    | Nein       | string | Die E-Mail-Adresse eines Benutzers, den Sie als RBAC Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können diesen Parameter anstelle von `owner-object-id` verwenden.|
| `owner-spn` | Nein       | string | Die Anwendungs-ID eines Dienstprinzipals, den Sie als RBAC-Besitzer für das Abonnement hinzufügen möchten, wenn es erstellt wird. Sie können diesen Parameter anstelle von `owner-object-id` verwenden. Wenn Sie diesen Parameter verwenden zu können, benötigt das Dienstprinzipal [Lesezugriff auf das Verzeichnis](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Wenn Sie eine vollständige Liste aller Parameter anzeigen möchten, finden Sie weitere Informationen unter [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Einschränkungen der Azure Enterprise-Abonnementerstellungs-API

- Nur Azure Enterprise-Abonnements können mithilfe dieser API erstellt werden.
- Es gilt eine anfängliche Grenze von 50 Abonnements pro Registrierungskonto, jedoch können Sie [eine Supportanfrage erstellen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), um die Grenze auf 200 zu erhöhen. Weitere Abonnements können nur über das Kontocenter erstellt werden.
- Es muss mindestens ein EA- oder EA Dev/Test-Abonnement unter dem Konto vorhanden sein. Dies bedeutet, dass der Kontobesitzer den manuellen Registrierungsvorgang mindestens ein Mal durchlaufen hat.
- Benutzer, die keine Kontobesitzer sind, jedoch einem Registrierungskonto über RBAC hinzugefügt wurden, können keine Abonnements mithilfe des Kontocenters erstellen.
- Sie können den Mandanten nicht auswählen, in dem das Abonnement erstellt werden soll. Das Abonnement wird immer im Basismandanten des Kontobesitzers erstellt. Wenn Sie das Abonnement in einen anderen Mandanten verschieben möchten, finden Sie weitere Informationen unter [Ändern des Abonnementmandanten](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Nächste Schritte

* Ein Beispiel zum Erstellen von Abonnements mithilfe von .NET finden Sie im [Beispielcode auf GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Da Sie nun ein Abonnement erstellt haben, können Sie diese Möglichkeit auch für andere Benutzer und Dienstprinzipale eröffnen. Weitere Informationen finden Sie unter [Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements (Vorschau) ](grant-access-to-create-subscription.md).
* Weitere Informationen zum Verwalten einer großen Anzahl von Abonnements mithilfe von Verwaltungsgruppen finden Sie unter [Organize your resources with Azure Management Groups](management-groups-overview.md) (Organisieren von Ressourcen mit Azure-Verwaltungsgruppen).
