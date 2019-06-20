---
title: Seitensteuerelemente in Azure API Management | Microsoft-Dokumentation
description: Enthält Informationen zu den Seitensteuerelementen, die für die Verwendung in Entwicklerportalvorlagen in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: d87293d89e4009512494bf47f9742ea5901f909a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656924"
---
# <a name="azure-api-management-page-controls"></a>Seitensteuerelemente in Azure API Management
Azure API Management enthält die folgenden Seitensteuerelemente zur Verwendung in Entwicklerportalvorlagen.  
  
Um ein Steuerelement zu verwenden, platzieren Sie es an der gewünschten Stelle in der Entwicklerportalvorlage. Einige Steuerelemente, z.B. das Steuerelement [app-actions](#app-actions), verfügen über Parameter, wie im folgenden Beispiel gezeigt:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Die Werte für die Parameter werden als Teil des Datenmodells für die Vorlage übergeben. In den meisten Fällen können Sie einfach das bereitgestellte Beispiel für jedes Steuerelement einfügen, damit es ordnungsgemäß funktioniert. Weitere Informationen zu den Parameterwerten finden Sie im Datenmodellabschnitt für jede Vorlage, in der ein Steuerelement verwendet werden kann.  
  
 Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Seitensteuerelemente in Entwicklerportalvorlagen  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a> app-actions  
 Das Steuerelement `app-actions` bietet eine Benutzeroberfläche für die Interaktion mit Anwendungen auf der Benutzerprofilseite im Entwicklerportal.  
  
 ![Steuerelement „app&#45;actions“](./media/api-management-page-controls/APIM-app-actions-control.png "APIM-Steuerelement „app-actions“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parameter  
  
|Parameter|BESCHREIBUNG|  
|---------------|-----------------|  
|appId|Die ID der Anwendung.|  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `app-actions` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Anwendungen](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> basic-signin  
 Das Steuerelement `basic-signin` stellt ein Steuerelement zum Sammeln von Informationen zur Benutzeranmeldung auf der Anmeldeseite im Entwicklerportal bereit.  
  
 ![Steuerelement „basic&#45;signin“](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM-Steuerelement „basic-signin“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parameter  
 None (Keine):  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `basic-signin` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Anmelden](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> paging-control  
 `paging-control` stellt eine Seitennavigation für Entwicklerportalseiten bereit, auf denen eine Liste von Elementen angezeigt wird.  
  
 ![Steuerelement „paging“](./media/api-management-page-controls/APIM-paging-control.png "APIM-Steuerelement „paging“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parameter  
 None (Keine):  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `paging-control` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [API-Liste](api-management-api-templates.md#APIList)  
  
-   [Problemliste](api-management-issue-templates.md#IssueList)  
  
-   [Produktliste](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> providers  
 Das Steuerelement `providers` stellt ein Steuerelement zur Auswahl von Authentifizierungsanbietern auf der Anmeldeseite im Entwicklerportal bereit.  
  
 ![Steuerelement „providers“](./media/api-management-page-controls/APIM-providers-control.png "APIM-Steuerelement „providers“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parameter  
 None (Keine):  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `providers` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Anmelden](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> search-control  
 `search-control` stellt eine Suchfunktion für Entwicklerportalseiten bereit, auf denen eine Liste von Elementen angezeigt wird.  
  
 ![Steuerelement „search“](./media/api-management-page-controls/APIM-search-control.png "APIM-Steuerelement „search“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parameter  
 None (Keine):  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `search-control` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [API-Liste](api-management-api-templates.md#APIList)  
  
-   [Produktliste](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> sign-up  
 Das Steuerelement `sign-up` stellt ein Steuerelement zum Sammeln von Benutzerprofilinformationen auf der Registrierungsseite im Entwicklerportal bereit.  
  
 ![Steuerelement „sign&#45;up“](./media/api-management-page-controls/APIM-sign-up-control.png "APIM-Steuerelement „sign-up“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parameter  
 None (Keine):  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `sign-up` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Registrieren](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> subscribe-button  
 `subscribe-button` bietet ein Steuerelement, mit dem ein Benutzer ein Produkt abonnieren kann.  
  
 ![Steuerelement „subscribe&#45;button“](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM-Steuerelement „subscribe-button“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parameter  
 None (Keine):  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `subscribe-button` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Produkt](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> subscription-cancel  
 Das Steuerelement `subscription-cancel` stellt auf der Benutzerprofilseite im Entwicklerportal ein Steuerelement für das Kündigen des Abonnements für ein Produkt bereit.  
  
 ![Steuerelement „subscription&#45;cancel“](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-Steuerelement „subscription-cancel“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parameter  
  
|Parameter|BESCHREIBUNG|  
|---------------|-----------------|  
|subscriptionId|Die ID des Abonnements, das gekündigt werden soll.|  
|cancelUrl|Die URL zum Kündigen des Abonnements|  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `subscription-cancel` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Produkt](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).