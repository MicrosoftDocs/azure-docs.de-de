---
title: Produktvorlagen in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Inhalt der Produktseiten im Entwicklerportal von Azure API Management anpassen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 14090e21fb7c6ca07fe63220ffd1d44d483ac869
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61088172"
---
# <a name="product-templates-in-azure-api-management"></a>Produktvorlagen in Azure API Management

Azure API Management bietet Ihnen die Möglichkeit, den Inhalt von Seiten des Entwicklerportals mit einem Satz von Vorlagen anzupassen, die den Inhalt konfigurieren. Unter Verwendung dieser Vorlagen können Sie die Seiteninhalte mithilfe von [DotLiquid](http://dotliquidmarkup.org/)-Syntax und dem Editor Ihrer Wahl (beispielsweise [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)) sowie verschiedenen lokalisierten [Zeichenfolgenressourcen](api-management-template-resources.md#strings), [Glyph-Ressourcen](api-management-template-resources.md#glyphs) und [Seitensteuerelementen](api-management-page-controls.md) an Ihre Bedürfnisse anpassen.  
  
 Mit den Vorlagen in diesem Abschnitt können Sie den Inhalt der Produktseiten im Entwicklerportal anpassen.  
  
-   [Produktliste](#ProductList)  
  
-   [Produkt](#Product)  
  
> [!NOTE]
>  Beispielstandardvorlagen sind in der folgenden Dokumentation enthalten, können aber aufgrund von kontinuierlichen Verbesserungen geändert werden. Sie können die aktiven Standardvorlagen im Entwicklerportal anzeigen, indem Sie zu den gewünschten einzelnen Vorlagen navigieren. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> Produktliste  
 Mit der Vorlage für die **Produktliste** können Sie den Text der Produktlistenseite im Entwicklerportal anpassen.  
  
 ![Produktliste](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Kontrollen  
 In der Vorlage `Product list` können die folgenden [Seitensteuerelemente](api-management-page-controls.md) verwendet werden.  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [search-control](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Datenmodell  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|Paging|Entität [Paging](api-management-template-data-model-reference.md#Paging).|Die Seiteninformationen für die Produktsammlung.|  
|Filterung|Entität [Filtering](api-management-template-data-model-reference.md#Filtering).|Die Filterinformationen für die Produktlistenseite.|  
|Produkte|Sammlung von [Product](api-management-template-data-model-reference.md#Product)-Entitäten.|Die für den aktuellen Benutzer sichtbaren Produkte.|  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="Product"></a> Produkt  
 Mit der Vorlage **Produkt** können Sie den Text der Produktseite im Entwicklerportal anpassen.  
  
 ![Produktseite im Entwicklerportal](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Kontrollen  
 In der Vorlage `Product list` können die folgenden [Seitensteuerelemente](api-management-page-controls.md) verwendet werden.  
  
-   [subscribe-button](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Datenmodell  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|Produkt|[Produkt](api-management-template-data-model-reference.md#Product)|Das angegebene Produkt.|  
|IsDeveloperSubscribed|boolean|Gibt an, ob der aktuelle Benutzer dieses Produkt abonniert hat.|  
|SubscriptionState|number|Der Status des Abonnements. Mögliche Statusangaben:<br /><br /> -   `0 - suspended`: Das Abonnement ist blockiert, und der Abonnent kann keine APIs des Produkts aufrufen.<br />-   `1 - active`: Das Abonnement ist aktiv.<br />-   `2 - expired`: Das Abonnement hat das Ablaufdatum erreicht und wurde deaktiviert.<br />-   `3 - submitted`: Die Abonnementanforderung wurde vom Entwickler gesendet, aber noch nicht genehmigt oder abgelehnt.<br />-   `4 - rejected`: Die Abonnementanforderung wurde von einem Administrator abgelehnt.<br />-   `5 - cancelled`: Das Abonnement wurde vom Entwickler oder Administrator abgebrochen.|  
|Einschränkungen|Array|Diese Eigenschaft ist veraltet und sollte nicht verwendet werden.|  
|DelegatedSubscriptionEnabled|boolean|Gibt an, ob für dieses Abonnement die [Delegierung](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) aktiviert ist.|  
|DelegatedSubscriptionUrl|Zeichenfolge|Gibt bei aktivierter Delegierung die URL des delegierten Abonnements an.|  
|IsAgreed|boolean|Wenn für das Produkt Bedingungen gelten, gibt dieser Wert an, ob der aktuelle Benutzer den Nutzungsbedingungen zugestimmt hat.|  
|Abonnements|Sammlung von [Subscription summary](api-management-template-data-model-reference.md#SubscriptionSummary)-Entitäten.|Die Abonnements für das Produkt.|  
|Apis|Sammlung von [API](api-management-template-data-model-reference.md#API)-Entitäten.|Die APIs in dieses Produkt.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Gibt an, ob der aktuelle Benutzer im Hinblick auf das Abonnementlimit zum Abonnieren dieses Produkts berechtigt ist.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Gibt an, ob der aktuelle Benutzer zum Abonnieren dieses Produkts berechtigt ist, je nachdem, ob mehrere Abonnements zulässig sind oder nicht.|  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).