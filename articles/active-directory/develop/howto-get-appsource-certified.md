---
title: Zertifizieren von AppSource für Azure Active Directory | Microsoft Docs
description: Detailinformationen zur Zertifizierung Ihrer AppSource-Anwendung für Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b820068f7bd59941346c8d2a3e8ede15252d58b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540264"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Zertifizieren von AppSource für Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) ist ein Ziel für Geschäftsbenutzer zum Entdecken, Ausprobieren und Verwalten branchenspezifischer SaaS-Anwendungen (eigenständiges SaaS und Add-On zu vorhandenen SaaS-Produkten von Microsoft).

Zum Auflisten einer eigenständigen SaaS-Anwendung auf AppSource muss Ihre Anwendung einmaliges Anmelden von Geschäftskonten aus von allen Unternehmen oder Organisationen akzeptieren, die über Azure Active Directory (Azure AD) verfügen. Für den Anmeldevorgang muss das [OpenID Connect](v1-protocols-openid-connect-code.md)- oder [OAuth 2.0](v1-protocols-oauth-code.md)-Protokoll verwendet werden. SAML-Integration wird für die AppSource-Zertifizierung nicht akzeptiert.

## <a name="guides-and-code-samples"></a>Anleitungen und Codebeispiele

Wenn Sie weitere Informationen über die Integration Ihrer Anwendung in Azure AD mithilfe der Open ID-Verbindung wünschen, befolgen Sie unsere Anleitungen und Codebeispiele in [Azure Active Directory für Entwickler](v1-overview.md#get-started "Erste Schritte").

## <a name="multi-tenant-applications"></a>Mehrinstanzenfähige Anwendungen

Eine *mehrinstanzenfähige Anwendung* ist eine Anwendung, die Anmeldungen von Benutzern aus allen Unternehmen oder Organisationen akzeptiert, die über Azure AD verfügen, ohne dass eine separate Instanz, Konfiguration oder Bereitstellung erforderlich ist. AppSource empfiehlt, dass Anwendungen Mehrinstanzenfähigkeit implementieren, um die kostenlose Testbenutzeroberfläche *per Einfachklick* zu aktivieren.

Führen Sie diese Schritte aus, um die Mehrinstanzenfähigkeit für Ihre Anwendung zu aktivieren:
1. Legen Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in den Registrierungsdaten Ihrer Anwendung die `Multi-Tenanted`-Eigenschaft auf `Yes` fest. Im Azure-Portal erstellte Anwendungen werden standardmäßig mit *[nur einem Mandanten](#single-tenant-applications)* konfiguriert.
1. Aktualisieren Sie Ihren Code, um Anforderungen an den Endpunkt `common` zu senden. Ändern Sie dazu den Endpunkt von `https://login.microsoftonline.com/{yourtenant}` in `https://login.microsoftonline.com/common*`.
1. Für einige Plattformen, z.B. ASP.NET, müssen Sie den Code auch so aktualisieren, dass er mehrere Aussteller akzeptiert.

Weitere Informationen zur Mehrinstanzenfähigkeit finden Sie unter [Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Anwendungen mit einem Mandanten

Eine *Anwendung mit einem Mandanten* ist eine Anwendung, die nur Anmeldungen von Benutzern einer definierten Azure Active Directory-Instanz akzeptiert. Externe Benutzer (einschließlich Geschäfts- oder Schulkonten aus anderen Organisationen oder persönliche Konten) können sich bei Anwendungen mit einem Mandanten anmelden, nachdem jeder Benutzer als Gastkonto der Azure AD-Instanz hinzugefügt worden ist, bei der die Anwendung registriert ist. 

Sie können Benutzer [programmgesteuert](../../active-directory-b2c/code-samples.md) über die [Azure AD B2B-Zusammenarbeit](../b2b/what-is-b2b.md) als Gastkonten zu Azure AD hinzufügen. Wenn Sie B2B verwenden, können Benutzer ein Self-Service-Portal erstellen, das keine Einladung für die Anmeldung erfordert. Weitere Informationen finden Sie unter [Self-Service-Anmeldungsportal für Azure AD B2B-Zusammenarbeit](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Anwendungen mit einem Mandanten können die *Kontaktformular*-Oberfläche aktivieren, aber wenn Sie die kostenlose Testbenutzeroberfläche per Einfachklick, die AppSource empfiehlt, aktivieren möchten, ermöglichen Sie stattdessen die Mehrinstanzenfähigkeit Ihrer Anwendung.

## <a name="appsource-trial-experiences"></a>AppSource – Testbenutzeroberflächen

### <a name="free-trial-customer-led-trial-experience"></a>Kostenlose Testversion (kundengeführte Testbenutzeroberfläche) 

Die kundengeführte Testversion ist die Benutzeroberfläche, die AppSource empfiehlt, da sie einen Einfachklickzugriff auf Ihre Anwendung bietet. Die folgende Abbildung zeigt, wie diese Benutzeroberfläche aussieht:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Benutzer findet Ihre Anwendung auf der AppSource-Website</li><li>Wählt die Option „Kostenlose Testversion“</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource leitet den Benutzer zu einer URL auf Ihrer Website um</li><li>Ihre Website startet automatisch (beim Laden der Seite) den Prozess des <i>einmaligen Anmeldens</i></li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Benutzer wird zur Microsoft-Anmeldeseite umgeleitet</li><li>Benutzer stellt Anmeldeinformationen zum Anmelden bereit</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Benutzer stimmt Ihrer Anwendung zu</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Anmeldung ist abgeschlossen und Benutzer wird wieder auf Ihre Website umgeleitet</li><li>Benutzer startet die kostenlose Testversion</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontaktformular (partnergeführte Testbenutzeroberfläche)

Sie können die partnergeführte Testbenutzeroberfläche verwenden, wenn ein manueller oder langfristiger Vorgang nötig ist, um den Benutzer/das Unternehmen bereitzustellen: Ihre Anwendung muss z.B. virtuelle Computer, Datenbankinstanzen oder Vorgänge bereitstellen, die viel Zeit in Anspruch nehmen. In diesem Fall sendet AppSource Ihnen Kontaktinformationen des Benutzers, nachdem der Benutzer die Schaltfläche **Testversion anfordern** ausgewählt und ein Formular ausgefüllt hat. Wenn Sie diese Informationen erhalten haben, stellen Sie die Umgebung bereit und senden die Anweisungen für den Zugriff auf die Testoberfläche an den Benutzer:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Benutzer findet Ihre Anwendung auf der AppSource-Website</li><li>Wählt die Option „Kontaktformular“</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Füllt ein Formular mit Kontaktinformationen aus</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Sie erhalten Benutzerinformationen</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Umgebung einrichten</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Wenden Sie sich mit Testversionsinformationen an den Benutzer</td>
        </tr>
        </table><br/><br/>
        <ul><li>Sie erhalten Informationen über den Benutzer und richten die Testversionsinstanz ein</li><li>Sie senden den Hyperlink, damit der Benutzer auf Ihre Anwendung zugreifen kann</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Benutzer greift auf Ihre Anwendung zu und schließt den Prozess des einmaligen Anmeldens ab</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Benutzer stimmt Ihrer Anwendung zu</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Anmeldung ist abgeschlossen und Benutzer wird wieder auf Ihre Website umgeleitet</li><li>Benutzer startet die kostenlose Testversion</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur AppSource-Testversionsbenutzeroberfläche finden Sie in [diesem Video](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen von Anwendungen, die Azure AD-Anmeldungen unterstützen, finden Sie unter [Authentifizierungsszenarien für Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Informationen zum Auflisten der SaaS-Anwendung in AppSource finden Sie unter [AppSource-Partnerinformationen](https://appsource.microsoft.com/partners).


## <a name="get-support"></a>Support

Für die Azure AD-Integration bieten wir Communitysupport über [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource). 

Sie sollten Ihre Fragen unbedingt zuerst auf Stack Overflow stellen und die vorhandenen Probleme durchsuchen, um festzustellen, ob bereits vor Ihnen jemand Ihre Frage gestellt hat. Stellen Sie sicher, dass Ihre Fragen und Kommentare mit [`[azure-active-directory]` und `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) markiert sind.

Verwenden Sie den folgenden Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started


<!--Image references-->
