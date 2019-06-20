---
title: Anwendungsvorlagen in Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie den Inhalt der Anwendungsseiten im Entwicklerportal in Azure API Management anpassen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 732fdf3f9210a1484895e0b43e061b4bbc586b43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60657935"
---
# <a name="application-templates-in-azure-api-management"></a>Anwendungsvorlagen in Azure API Management
Azure API Management bietet Ihnen die Möglichkeit, den Inhalt von Seiten des Entwicklerportals mit einem Satz von Vorlagen anzupassen, die den Inhalt konfigurieren. Unter Verwendung dieser Vorlagen können Sie die Seiteninhalte mithilfe von [DotLiquid](http://dotliquidmarkup.org/)-Syntax und dem Editor Ihrer Wahl (beispielsweise [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)) sowie verschiedenen lokalisierten [Zeichenfolgenressourcen](api-management-template-resources.md#strings), [Glyph-Ressourcen](api-management-template-resources.md#glyphs) und [Seitensteuerelementen](api-management-page-controls.md) an Ihre Bedürfnisse anpassen.  
  
 Mit den Vorlagen in diesem Abschnitt können Sie den Inhalt der Anwendungsseiten im Entwicklerportal anpassen.  
  
-   [Anwendungsliste](#ProductList)  
  
-   [Anwendung](#Application)  
  
> [!NOTE]
>  Beispielstandardvorlagen sind in der folgenden Dokumentation enthalten, können aber aufgrund von kontinuierlichen Verbesserungen geändert werden. Sie können die aktiven Standardvorlagen im Entwicklerportal anzeigen, indem Sie zu den gewünschten einzelnen Vorlagen navigieren. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [Anpassen des Azure API Management-Entwicklerportals mithilfe von Vorlagen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> Anwendungsliste  
 Mit der Vorlage für die **Anwendungsliste** können Sie den Text der Anwendungslistenseite im Entwicklerportal anpassen.  
  
 ![Anwendungslistenseite, Vorlagen im Entwicklerportal](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "APIM-Anwendungslistenseite, Vorlagen im Entwicklerportal")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
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
  
### <a name="data-model"></a>Datenmodell  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Paging`|Entität [Paging](api-management-template-data-model-reference.md#Paging).|Die Auslagerungsinformationen für die Anwendungssammlung.|  
|`Applications`|Sammlung von [Anwendungsentitäten](api-management-template-data-model-reference.md#Application).|Die für den aktuellen Benutzer sichtbaren Anwendungen.|  
|`CategoryName`|Zeichenfolge|Die Kategorie der Anwendung.|  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="Application"></a> Anwendung  
 Mit der Vorlage für die **Anwendung** können Sie den Text der Anwendungsseite im Entwicklerportal anpassen.  
  
 ![Anwendungsseite, Vorlagen im Entwicklerportal](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "APIM-Anwendungsseite, Vorlagen im Entwicklerportal")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>Kontrollen  
 Mit der Vorlage `Application` können keine [Seitensteuerelemente](api-management-page-controls.md) verwendet werden.  
  
### <a name="data-model"></a>Datenmodell  
 Entität [Anwendung](api-management-template-data-model-reference.md#Application).  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).
