---
title: Seitenvorlagen in Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie den Inhalt von Entwicklerportalseiten mithilfe von verschiedenen Vorlagen in Azure API Management anpassen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: apimpm
ms.openlocfilehash: 1fbafcdab938a0f8653df48631d7733cc58a3668
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656794"
---
# <a name="page-templates-in-azure-api-management"></a>Seitenvorlagen in Azure API Management
Azure API Management bietet Ihnen die Möglichkeit, den Inhalt von Seiten des Entwicklerportals mit einem Satz von Vorlagen anzupassen, die den Inhalt konfigurieren. Unter Verwendung dieser Vorlagen können Sie die Seiteninhalte mithilfe von [DotLiquid](http://dotliquidmarkup.org/)-Syntax und dem Editor Ihrer Wahl (beispielsweise [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)) sowie verschiedenen lokalisierten [Zeichenfolgenressourcen](api-management-template-resources.md#strings), [Glyph-Ressourcen](api-management-template-resources.md#glyphs) und [Seitensteuerelementen](api-management-page-controls.md) an Ihre Bedürfnisse anpassen.  
  
 Mit den Vorlagen in diesem Abschnitt können Sie den Inhalt der Seiten für die Anmeldung, die Registrierung und eine nicht gefundene Seite im Entwicklerportal anpassen.  
  
-   [Anmelden](#SignIn)  
  
-   [Registrieren](#SignUp)  
  
-   [Seite nicht gefunden](#PageNotFound)  
  
> [!NOTE]
>  Beispielstandardvorlagen sind in der folgenden Dokumentation enthalten, können aber aufgrund von kontinuierlichen Verbesserungen geändert werden. Sie können die aktiven Standardvorlagen im Entwicklerportal anzeigen, indem Sie zu den gewünschten einzelnen Vorlagen navigieren. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a> Anmelden  
 Mit der Vorlage zum **Anmelden** können Sie die Anmeldeseite im Entwicklerportal anpassen.  
  
 ![Anmeldeseite](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM-Anmeldeseite, Vorlagen im Entwicklerportal")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Kontrollen  
 In dieser Vorlage können die folgenden [Seitensteuerelemente](api-management-page-controls.md) verwendet werden.  
  
-   [basic-signin](api-management-page-controls.md#basic-signin)  
  
-   [providers](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Datenmodell  
 Entität [Benutzeranmeldung](api-management-template-data-model-reference.md#UseSignIn).  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="SignUp"></a> Registrieren  
 Mit der Vorlage zum **Registrieren** können Sie die Registrierungsseite im Entwicklerportal anpassen.  
  
 ![Registrierungsseite](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM-Registrierungsseite, Vorlagen im Entwicklerportal")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Kontrollen  
 In dieser Vorlage können die folgenden [Seitensteuerelemente](api-management-page-controls.md) verwendet werden.  
  
-   [sign-up](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Datenmodell  
 Entität [Benutzerregistrierung](api-management-template-data-model-reference.md#UserSignUp).  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a> Seite nicht gefunden  
 Mit der Vorlage für **Seite nicht gefunden** können Sie die Seite für eine nicht gefundene Seite im Entwicklerportal anpassen.  
  
 ![Seite nicht gefunden](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM „Seite nicht gefunden“, Vorlagen im Entwicklerportal")  
  
### <a name="default-template"></a>Standardvorlage  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Kontrollen  
 Mit dieser Vorlage können Sie keine [Seitensteuerelemente](api-management-page-controls.md) verwenden.  
  
### <a name="data-model"></a>Datenmodell  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|referenceCode|Zeichenfolge|Generierter Code, wenn diese Seite aufgrund eines internen Fehlers angezeigt wurde.|  
|errorCode|Zeichenfolge|Generierter Code, wenn diese Seite aufgrund eines internen Fehlers angezeigt wurde.|  
|emailBody|Zeichenfolge|Generierter E-Mail-Text, wenn diese Seite aufgrund eines internen Fehlers angezeigt wurde.|  
|requestedUrl|Zeichenfolge|Die angeforderte URL, wenn die Seite nicht gefunden wurde.|  
|referrerUrl|Zeichenfolge|Die Verweis-URL der angeforderten URL.|  
  
### <a name="sample-template-data"></a>Vorlagenbeispieldaten  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).
