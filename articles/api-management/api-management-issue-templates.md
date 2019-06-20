---
title: Problemvorlagen in Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie den Inhalt der Problemseiten im Entwicklerportal in Azure API Management anpassen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c70e88a572bc261ad9d25f9a742b1987d0c19b44
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656684"
---
# <a name="issue-templates-in-azure-api-management"></a>Problemvorlagen in Azure API Management
Azure API Management bietet Ihnen die Möglichkeit, den Inhalt von Seiten des Entwicklerportals mit einem Satz von Vorlagen anzupassen, die den Inhalt konfigurieren. Unter Verwendung dieser Vorlagen können Sie die Seiteninhalte mithilfe von [DotLiquid](http://dotliquidmarkup.org/)-Syntax und dem Editor Ihrer Wahl (beispielsweise [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)) sowie verschiedenen lokalisierten [Zeichenfolgenressourcen](api-management-template-resources.md#strings), [Glyph-Ressourcen](api-management-template-resources.md#glyphs) und [Seitensteuerelementen](api-management-page-controls.md) an Ihre Bedürfnisse anpassen.  
  
 Mit den Vorlagen in diesem Abschnitt können Sie den Inhalt der Problemseiten im Entwicklerportal anpassen.  
  
-   [Problemliste](#IssueList)  
  
> [!NOTE]
>  Beispielstandardvorlagen sind in der folgenden Dokumentation enthalten, können aber aufgrund der kontinuierlichen Verbesserungen geändert werden. Sie können die aktiven Standardvorlagen im Entwicklerportal anzeigen, indem Sie zu den gewünschten einzelnen Vorlagen navigieren. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a> Problemliste  
 Mit der Vorlage für die **Problemliste** können Sie den Text der Problemlistenseite im Entwicklerportal anpassen.  
  
 ![Problemliste, Entwicklerportal](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APIM-Problemliste, Entwicklerportal")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Kontrollen  
 In der Vorlage `Issue list` können die folgenden [Seitensteuerelemente](api-management-page-controls.md) verwendet werden.  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Datenmodell  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Issues`|Sammlung von [Problementitäten](api-management-template-data-model-reference.md#Issue).|Die für den aktuellen Benutzer sichtbaren Probleme.|  
|`Paging`|Entität [Paging](api-management-template-data-model-reference.md#Paging).|Die Auslagerungsinformationen für die Anwendungssammlung.|  
|`IsAuthenticated`|boolean|Gibt an, ob der aktuelle Benutzer im Entwicklerportal angemeldet ist.|  
|`CanReportIssues`|boolean|Gibt an, ob der aktuelle Benutzer über Berechtigungen zum Melden eines Problems verfügt.|  
|`Search`|Zeichenfolge|Diese Eigenschaft ist veraltet und sollte nicht verwendet werden.|  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).
