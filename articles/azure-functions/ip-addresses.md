---
title: IP-Adressen in Azure Functions
description: Erfahren Sie, wie Sie eingehende und ausgehende IP-Adressen für Funktionen-Apps finden und wodurch diese geändert werden.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: 83e5a15d8a7f9c01f6a180ebceb715600b8a39db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035859"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-Adressen in Azure Functions

In diesem Artikel erfahren Sie mehr über die folgenden Themen im Zusammenhang mit IP-Adressen von Funktionen-Apps:

* Wie Sie die IP-Adressen finden, die derzeit von einer Funktionen-App verwendet werden.
* Wodurch die IP-Adressen einer Funktionen-App geändert werden.
* Wie Sie die IP-Adressen einschränken, die auf eine Funktionen-App zugreifen können.
* Wie Sie dedizierte IP-Adressen für eine Funktionen-App erhalten.

IP-Adressen werden Funktionen-Apps zugeordnet, nicht einzelnen Funktionen. Eingehende HTTP-Anforderungen können die eingehende IP-Adresse nicht verwenden, um einzelne Funktionen aufzurufen. Sie müssen den Standarddomänennamen (functionappname.azurewebsites.net) oder einen benutzerdefinierten Domänennamen verwenden.

## <a name="function-app-inbound-ip-address"></a>Eingehende IP-Adresse einer Funktionen-App

Jede Funktionen-App verfügt über eine einzelne eingehende IP-Adresse. So finden Sie diese IP-Adresse:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zur Funktionen-App.
3. Wählen Sie **Plattformfeatures**.
4. Wenn Sie **Eigenschaften** wählen, wird die eingehende IP-Adresse unter **Virtuelle IP-Adresse** angezeigt.

## <a name="find-outbound-ip-addresses"></a>Ausgehende IP-Adressen einer Funktions-App

Jede Funktionen-App verfügt über einen Satz von verfügbaren ausgehenden IP-Adressen. Jede ausgehende Verbindung von einer Funktion, beispielsweise mit einer Back-End-Datenbank, verwendet eine der verfügbaren ausgehenden IP-Adressen als IP-Ursprungsadresse. Im Vorfeld ist nicht klar, welche IP-Adresse eine bestimmte Verbindung verwendet. Daher muss Ihr Back-End-Dienst seine Firewall für alle ausgehenden IP-Adressen der App öffnen.

So finden Sie die verfügbaren ausgehenden IP-Adressen einer Funktionen-App:

1. Melden Sie sich beim [Azure-Ressourcen-Explorer](https://resources.azure.com) an.
2. Wählen Sie **Abonnements > {Ihr Abonnement} > Anbieter > Microsoft.Web > Websites**.
3. Suchen Sie im JSON-Panel die Site mit einer `id`-Eigenschaft, die auf den Namen Ihrer Funktionen-App endet.
4. Prüfen Sie `outboundIpAddresses` und `possibleOutboundIpAddresses`. 

Der Satz der `outboundIpAddresses` steht derzeit für die Funktionen-App zur Verfügung. Der Satz der `possibleOutboundIpAddresses` enthält IP-Adressen, die nur zur Verfügung stehen, wenn für die Funktionen-App [eine Skalierung auf andere Tarife](#outbound-ip-address-changes) möglich ist.

Eine Alternative zum Ermitteln der verfügbaren ausgehenden IP-Adressen ist die Verwendung der [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Bei der Skalierung einer nach dem [Verbrauchsplan](functions-scale.md#consumption-plan) ausgeführten Funktions-App wird möglicherweise ein neuer Bereich von ausgehenden IP-Adressen zugewiesen. Bei der Ausführung nach dem Verbrauchsplan müssen Sie möglicherweise das gesamte Rechenzentrum auf die Whitelist setzen.

## <a name="data-center-outbound-ip-addresses"></a>Ausgehende IP-Adressen eines Rechenzentrums

Wenn Sie eine Whitelist der von Ihrer Funktionen-Apps verwendeten ausgehenden IP-Adressen benötigen, haben Sie die Möglichkeit, das Rechenzentrum der Funktionen-App auf die Whitelist zu setzen (Azure-Region). Sie können [eine JSON-Datei herunterladen, die IP-Adressen für alle Azure-Rechenzentren auflistet](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Suchen Sie dann das JSON-Fragment, das der Region entspricht, in der Ihre Funktions-App ausgeführt wird.

Das JSON-Fragment für Westeuropa könnte beispielsweise folgendermaßen aussehen:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Informationen dazu, wann diese Datei aktualisiert wird und wann die IP-Adresse geändert wird, finden Sie im Abschnitt **Details** auf der [Download Center-Seite](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a>Änderungen der eingehenden IP-Adresse

Die eingehende IP-Adresse **kann** geändert werden, wenn Sie:

- eine Funktionen App löschen und in einer anderen Ressourcengruppe neu erstellen.
- die letzte Funktionen-App in einer Kombination aus Ressourcengruppe und Region löschen und neu erstellen.
- eine SSL-Bindung löschen, z.B. während der [Zertifikaterneuerung](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates)).

Wenn Ihre Funktions-App in einem [Verbrauchstarif](functions-scale.md#consumption-plan) ausgeführt wird, kann sich die eingehende IP-Adresse auch ändern, wenn Sie noch keine der oben aufgeführten Aktionen ausgeführt haben.

## <a name="outbound-ip-address-changes"></a>Änderungen der ausgehenden IP-Adresse

Der Satz der verfügbaren ausgehenden IP-Adressen für eine Funktionen-App kann geändert werden, wenn Sie:

* Maßnahmen ergreifen, durch die sich die eingehende IP-Adresse ändern kann.
* den Tarif für Ihren App Service-Plan wechseln. Alle möglichen ausgehenden IP-Adressen, die Ihre App in allen Tarifen verwenden kann, sind in der `possibleOutboundIPAddresses`-Eigenschaft aufgelistet. Siehe [Ermitteln der ausgehenden IP-Adressen](#find-outbound-ip-addresses).

Wenn Ihre Funktions-App in einem [Verbrauchstarif](functions-scale.md#consumption-plan) ausgeführt wird, kann sich die ausgehende IP-Adresse auch ändern, wenn Sie noch keine der oben aufgeführten Aktionen ausgeführt haben.

So erzwingen Sie eine Änderung der ausgehenden IP-Adresse:

1. Skalieren Sie Ihren App Service-Plan zwischen Standard- und Premium v2-Tarifen hoch oder herunter.
2. Warten Sie 10 Minuten.
3. Skalieren Sie zur Ausgangssituation zurück.

## <a name="ip-address-restrictions"></a>Einschränkungen für IP-Adressen

Sie können eine Liste der IP-Adressen konfigurieren, denen Sie den Zugriff auf die Funktionen-App gestatten oder verweigern möchten. Weitere Informationen finden Sie unter [Statische Azure App Service-IP-Einschränkungen](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedizierte IP-Adressen

Wenn Sie statische, dedizierte IP-Adressen benötigen, empfehlen wir [App Service Environments](../app-service/environment/intro.md) (der [isolierte Tarif](https://azure.microsoft.com/pricing/details/app-service/) von App Service-Plänen). Weitere Informationen finden Sie unter [ASE-IP-Adressen](../app-service/environment/network-info.md#ase-ip-addresses) und [Steuern von eingehendem Datenverkehr in eine App Service-Umgebung](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

So finden Sie heraus, ob Ihre Funktionen-App in einer App Service-Umgebung ausgeführt wird:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zur Funktionen-App.
3. Klicken Sie auf die Registerkarte **Übersicht**.
4. Die App Service-Plantarif wird unter **App Service-Plan/Tarif** angezeigt. Der App Service Environment-Tarif ist **isoliert**.
 
Alternativ können Sie die [Cloud Shell](../cloud-shell/quickstart.md) verwenden:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Die `sku` der App Service-Umgebung ist `Isolated`.

## <a name="next-steps"></a>Nächste Schritte

IP-Änderungen sind häufig durch die Skalierung Ihrer Funktionen-App begründet. [Erfahren Sie mehr über die Skalierung Ihrer Funktionen-App](functions-scale.md).
