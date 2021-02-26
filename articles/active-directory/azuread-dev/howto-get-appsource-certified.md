---
title: Zertifizieren von AppSource für Azure Active Directory | Microsoft Docs
description: Detailinformationen zur Zertifizierung Ihrer AppSource-Anwendung für Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 15a4dd56b509571094ef202fbce781104bda9188
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052243"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Zertifizieren von AppSource für Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) ist ein Ziel für Geschäftsbenutzer zum Entdecken, Ausprobieren und Verwalten branchenspezifischer SaaS-Anwendungen (eigenständiges SaaS und Add-On zu vorhandenen SaaS-Produkten von Microsoft).

Zum Auflisten einer eigenständigen SaaS-Anwendung auf AppSource muss Ihre Anwendung einmaliges Anmelden von Geschäftskonten aus von allen Unternehmen oder Organisationen akzeptieren, die über Azure Active Directory (Azure AD) verfügen. Für den Anmeldevorgang muss das [OpenID Connect](v1-protocols-openid-connect-code.md)- oder [OAuth 2.0](v1-protocols-oauth-code.md)-Protokoll verwendet werden. SAML-Integration wird für die AppSource-Zertifizierung nicht akzeptiert.

## <a name="guides-and-code-samples"></a>Anleitungen und Codebeispiele

Weitere Informationen zur Integration Ihrer Anwendung in Azure AD mithilfe von Open ID Connect finden Sie in den Anleitungen und Codebeispielen im [Entwicklerhandbuch zu Azure Active Directory](v1-overview.md#get-started "Erste Schritte mit Azure AD für Entwickler").

## <a name="multi-tenant-applications"></a>Mehrinstanzenfähige Anwendungen

Eine *mehrinstanzenfähige Anwendung* ist eine Anwendung, die Anmeldungen von Benutzern aus allen Unternehmen oder Organisationen akzeptiert, die über Azure AD verfügen, ohne dass eine separate Instanz, Konfiguration oder Bereitstellung erforderlich ist. AppSource empfiehlt, dass Anwendungen Mehrinstanzenfähigkeit implementieren, um die kostenlose Testbenutzeroberfläche *per Einfachklick* zu aktivieren.

Führen Sie diese Schritte aus, um die Mehrinstanzenfähigkeit für Ihre Anwendung zu aktivieren:
1. Legen Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in den Registrierungsdaten Ihrer Anwendung die `Multi-Tenanted`-Eigenschaft auf `Yes` fest. Im Azure-Portal erstellte Anwendungen werden standardmäßig mit *[nur einem Mandanten](#single-tenant-applications)* konfiguriert.
1. Aktualisieren Sie Ihren Code, um Anforderungen an den Endpunkt `common` zu senden. Ändern Sie dazu den Endpunkt von `https://login.microsoftonline.com/{yourtenant}` in `https://login.microsoftonline.com/common*`.
1. Für einige Plattformen, z.B. ASP.NET, müssen Sie den Code auch so aktualisieren, dass er mehrere Aussteller akzeptiert.

Weitere Informationen zur Mehrinstanzenfähigkeit finden Sie unter [Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Anwendungen mit einem Mandanten

Eine *Anwendung mit einem Mandanten* ist eine Anwendung, die nur Anmeldungen von Benutzern einer definierten Azure Active Directory-Instanz akzeptiert. Externe Benutzer (einschließlich Geschäfts- oder Schulkonten aus anderen Organisationen oder persönliche Konten) können sich bei Anwendungen mit einem Mandanten anmelden, nachdem jeder Benutzer als Gastkonto der Azure AD-Instanz hinzugefügt worden ist, bei der die Anwendung registriert ist. 

Sie können Benutzer [programmgesteuert](../../active-directory-b2c/code-samples.md) über die [Azure AD B2B-Zusammenarbeit](../external-identities/what-is-b2b.md) als Gastkonten zu Azure AD hinzufügen. Wenn Sie B2B verwenden, können Benutzer ein Self-Service-Portal erstellen, das keine Einladung für die Anmeldung erfordert. Weitere Informationen finden Sie unter [Self-Service-Anmeldungsportal für Azure AD B2B-Zusammenarbeit](../external-identities/self-service-portal.md).

Anwendungen mit einem Mandanten können die *Kontaktformular*-Oberfläche aktivieren, aber wenn Sie die kostenlose Testbenutzeroberfläche per Einfachklick, die AppSource empfiehlt, aktivieren möchten, ermöglichen Sie stattdessen die Mehrinstanzenfähigkeit Ihrer Anwendung.

## <a name="appsource-trial-experiences"></a>AppSource – Testbenutzeroberflächen

### <a name="free-trial-customer-led-trial-experience"></a>Kostenlose Testversion (kundengeführte Testbenutzeroberfläche)

Die kundengeführte Testversion ist die Benutzeroberfläche, die AppSource empfiehlt, da sie einen Einfachklickzugriff auf Ihre Anwendung bietet. Das folgende Beispiel zeigt, wie diese Benutzeroberfläche aussieht:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>Benutzer findet Ihre Anwendung auf der AppSource-Website</li><li>Wählt die Option „Kostenlose Testversion“ aus</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>AppSource leitet den Benutzer zu einer URL auf Ihrer Website um</li><li>Ihre Website startet automatisch (beim Laden der Seite) den Prozess des <i>einmaligen Anmeldens</i></li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>Benutzer wird zur Microsoft-Anmeldeseite umgeleitet</li><li>Benutzer stellt Anmeldeinformationen zum Anmelden bereit</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>Benutzer stimmt Ihrer Anwendung zu</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>Anmeldung ist abgeschlossen und Benutzer wird wieder auf Ihre Website umgeleitet</li><li>Benutzer startet die kostenlose Testversion</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontaktformular (partnergeführte Testbenutzeroberfläche)

Sie können die partnergeführte Testbenutzeroberfläche verwenden, wenn ein manueller oder langfristiger Vorgang nötig ist, um den Benutzer/das Unternehmen bereitzustellen: Ihre Anwendung muss z.B. virtuelle Computer, Datenbankinstanzen oder Vorgänge bereitstellen, die viel Zeit in Anspruch nehmen. In diesem Fall sendet AppSource Ihnen Kontaktinformationen des Benutzers, nachdem der Benutzer die Schaltfläche **Testversion anfordern** ausgewählt und ein Formular ausgefüllt hat. Wenn Sie diese Informationen erhalten haben, stellen Sie die Umgebung bereit und senden die Anweisungen für den Zugriff auf die Testoberfläche an den Benutzer:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>Benutzer findet Ihre Anwendung auf der AppSource-Website</li><li>Wählt die Option „Kontaktformular“ aus</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Füllt ein Formular mit Kontaktinformationen aus</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>Sie erhalten Benutzerinformationen</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>Umgebung einrichten</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Wenden Sie sich mit Testversionsinformationen an den Benutzer</td>
        </tr>
        </table><br/><br/>
        <ul><li>Sie erhalten Informationen über den Benutzer und richten die Testversionsinstanz ein</li><li>Sie senden den Hyperlink, damit der Benutzer auf Ihre Anwendung zugreifen kann</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>Benutzer greift auf Ihre Anwendung zu und schließt den Prozess des einmaligen Anmeldens ab</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>Benutzer stimmt Ihrer Anwendung zu</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>Anmeldung ist abgeschlossen und Benutzer wird wieder auf Ihre Website umgeleitet</li><li>Benutzer startet die kostenlose Testversion</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur AppSource-Testversionsbenutzeroberfläche finden Sie in [diesem Video](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen von Anwendungen, die Azure AD-Anmeldungen unterstützen, finden Sie unter [Authentifizierungsszenarien für Azure AD](./v1-authentication-scenarios.md).
- Informationen zum Auflisten der SaaS-Anwendung in AppSource finden Sie unter [AppSource-Partnerinformationen](https://appsource.microsoft.com/partners).

## <a name="get-support"></a>Support

Für die Azure AD-Integration bieten wir Communitysupport über [Microsoft Q&A](https://docs.microsoft.com/answers/products/).

Sie sollten Ihre Fragen unbedingt zuerst auf Microsoft Q&A stellen und die vorhandenen Probleme durchsuchen, um festzustellen, ob bereits vor Ihnen jemand Ihre Frage gestellt hat. Markieren Sie Ihre Fragen und Kommentare mit [`[azure-active-directory]`](https://docs.microsoft.com/answers/topics/azure-active-directory.html).

Verwenden Sie den folgenden Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->