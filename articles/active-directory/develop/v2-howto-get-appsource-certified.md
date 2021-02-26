---
title: Zertifizieren Ihrer AppSource-Anwendung für Azure Active Directory | Microsoft-Dokumentation
description: Hier erhalten Sie Detailinformationen zur Zertifizierung Ihrer AppSource-Anwendung für Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: d366664981894933f9afb177e9147ccae6695872
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222165"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Zertifizieren Ihrer AppSource-Anwendung für Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) ist ein Ziel für Geschäftsbenutzer zum Entdecken, Ausprobieren und Verwalten branchenspezifischer SaaS-Anwendungen (eigenständiges SaaS und Add-On zu vorhandenen SaaS-Produkten von Microsoft).

Zum Auflisten einer eigenständigen SaaS-Anwendung auf AppSource muss Ihre Anwendung einmaliges Anmelden von Geschäftskonten aus von allen Unternehmen oder Organisationen akzeptieren, die über Azure Active Directory (Azure AD) verfügen. Für den Anmeldevorgang muss das [OpenID Connect](v2-protocols-oidc.md)- oder [OAuth 2.0](v2-oauth2-auth-code-flow.md)-Protokoll verwendet werden. SAML-Integration wird für die AppSource-Zertifizierung nicht akzeptiert.

## <a name="multi-tenant-applications"></a>Mehrinstanzenfähige Anwendungen

Eine *mehrinstanzenfähige Anwendung* ist eine Anwendung, die Anmeldungen von Benutzern aus allen Unternehmen oder Organisationen akzeptiert, die über Azure AD verfügen, ohne dass eine separate Instanz, Konfiguration oder Bereitstellung erforderlich ist. AppSource empfiehlt, dass Anwendungen Mehrinstanzenfähigkeit implementieren, um die kostenlose Testbenutzeroberfläche *per Einfachklick* zu aktivieren.

Führen Sie diese Schritte aus, um die Mehrinstanzenfähigkeit für Ihre Anwendung zu aktivieren:
1. Legen Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in den Registrierungsdaten Ihrer Anwendung die `Multi-Tenanted`-Eigenschaft auf `Yes` fest. Im Azure-Portal erstellte Anwendungen werden standardmäßig mit *[nur einem Mandanten](#single-tenant-applications)* konfiguriert.
1. Aktualisieren Sie Ihren Code, um Anforderungen an den Endpunkt `common` zu senden. Ändern Sie dazu den Endpunkt von `https://login.microsoftonline.com/{yourtenant}` in `https://login.microsoftonline.com/common*`.
1. Bei einigen Plattformen (wie z. B. ASP.NET) müssen Sie den Code auch so aktualisieren, dass mehrere Aussteller akzeptiert werden.

Weitere Informationen zur Mehrinstanzenfähigkeit finden Sie unter [Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Anwendungen mit einem Mandanten

Eine *Anwendung mit einem Mandanten* ist eine Anwendung, die nur Anmeldungen von Benutzern einer definierten Azure Active Directory-Instanz akzeptiert. Externe Benutzer (einschließlich Geschäfts- oder Schulkonten aus anderen Organisationen oder persönliche Konten) können sich bei Anwendungen mit einem Mandanten anmelden, nachdem jeder Benutzer als Gastkonto der Azure AD-Instanz hinzugefügt worden ist, bei der die Anwendung registriert ist. 

Sie können Benutzer [programmgesteuert](../../active-directory-b2c/code-samples.md) über die [Azure AD B2B-Zusammenarbeit](../external-identities/what-is-b2b.md) als Gastkonten zu Azure AD hinzufügen. Wenn Sie B2B verwenden, können Benutzer ein Self-Service-Portal erstellen, das keine Einladung für die Anmeldung erfordert. Weitere Informationen finden Sie unter [Self-Service-Anmeldungsportal für Azure AD B2B-Zusammenarbeit](../external-identities/self-service-portal.md).

Anwendungen mit einem Mandanten können die *Kontaktformular*-Oberfläche aktivieren, aber wenn Sie die kostenlose Testbenutzeroberfläche per Einfachklick, die AppSource empfiehlt, aktivieren möchten, ermöglichen Sie stattdessen die Mehrinstanzenfähigkeit Ihrer Anwendung.

## <a name="appsource-trial-experiences"></a>AppSource – Testbenutzeroberflächen

### <a name="free-trial-customer-led-trial-experience"></a>Kostenlose Testversion (kundengeführte Testbenutzeroberfläche)

Die kundengeführte Testversion ist die Benutzeroberfläche, die AppSource empfiehlt, da sie einen Einfachklickzugriff auf Ihre Anwendung bietet. Das folgende Beispiel zeigt, wie diese Benutzeroberfläche aussieht:

1.  Ein Benutzer findet Ihre Anwendung auf der AppSource-Website und wählt dann die Option **Kostenlose Testversion** aus.

    ![Screenshot der Option „Kostenlose Testversion“ für die kundengeführte Testbenutzeroberfläche](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource leitet den Benutzer zu einer URL auf Ihrer Website um. Ihre Website startet automatisch (beim Laden der Seite) den Prozess des *einmaligen Anmeldens*.

    ![Screenshot der Umleitung des Benutzers zu einer URL auf Ihrer Website](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  Der Benutzer wird zur Microsoft-Anmeldeseite umgeleitet, und der Benutzer gibt die Anmeldeinformationen ein, um sich anzumelden.

    ![Screenshot der Microsoft-Anmeldeseite](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. Der Benutzer stimmt Ihrer Anwendung zu.

    ![Beispiel: Zustimmungsseite für eine Anwendung](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  Die Anmeldung ist abgeschlossen, und Benutzer wird wieder auf Ihre Website umgeleitet.  Der Benutzer startet die kostenlose Testversion.

    ![Screenshot der Benutzeroberfläche, die dem Benutzer angezeigt wird, wenn er wieder auf Ihre Website umgeleitet wird](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Kontaktformular (partnergeführte Testbenutzeroberfläche)

Sie können die partnergeführte Testbenutzeroberfläche verwenden, wenn ein manueller oder langfristiger Vorgang nötig ist, um den Benutzer/das Unternehmen bereitzustellen: Ihre Anwendung muss z.B. virtuelle Computer, Datenbankinstanzen oder Vorgänge bereitstellen, die viel Zeit in Anspruch nehmen. In diesem Fall sendet AppSource Ihnen Kontaktinformationen des Benutzers, nachdem der Benutzer die Schaltfläche **Testversion anfordern** ausgewählt und ein Formular ausgefüllt hat. Wenn Sie diese Informationen erhalten haben, stellen Sie die Umgebung bereit und senden die Anweisungen für den Zugriff auf die Testoberfläche an den Benutzer:<br/><br/>

1. Ein Benutzer findet Ihre Anwendung auf der AppSource-Website und wählt dann die Option **Kontakt mit mir aufnehmen** aus.

    ![Screenshot der Option „Kontakt mit mir aufnehmen“ für die partnergeführte Testbenutzeroberfläche](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. Der Benutzer füllt ein Formular mit Kontaktinformationen aus.

    ![Screenshot eines Beispielformulars mit Kontaktinformationen](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Sie erhalten die Informationen des Benutzers, richten eine Testinstanz ein und senden dem Benutzer den Hyperlink zum Zugreifen auf Ihre Anwendung.

    ![Platzhaltersymbol für Benutzerinformationen](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. Der Benutzer greift auf Ihre Anwendung zu und schließt den Prozess des einmaligen Anmeldens ab.

    ![Screenshot des Anmeldebildschirms für die Anwendung](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. Der Benutzer stimmt Ihrer Anwendung zu.

    ![Screenshot einer Beispielzustimmungsseite für eine Anwendung](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. Die Anmeldung ist abgeschlossen, und Benutzer wird wieder auf Ihre Website umgeleitet. Der Benutzer startet die kostenlose Testversion.

    ![Screenshot der Benutzeroberfläche, die dem Benutzer angezeigt wird, wenn er wieder auf Ihre Website umgeleitet wird](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur AppSource-Testversionsbenutzeroberfläche finden Sie in [diesem Video](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Support

Für die Azure AD-Integration bieten wir Communitysupport über [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html).

Sie sollten Ihre Fragen unbedingt zuerst auf [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html) stellen und die vorhandenen Probleme durchsuchen, um festzustellen, ob bereits vor Ihnen jemand dieselbe Frage gestellt hat. Markieren Sie Ihre Fragen oder Kommentare mit [[`[azure-active-directory]`](https://docs.microsoft.com/answers/topics/azure-active-directory.html)].

Verwenden Sie den folgenden Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen von Anwendungen, die Azure AD-Anmeldungen unterstützen, finden Sie unter [Authentifizierungsszenarien für Azure AD](authentication-flows-app-scenarios.md).
- Informationen zum Auflisten der SaaS-Anwendung in AppSource finden Sie unter [AppSource-Partnerinformationen](https://appsource.microsoft.com/partners).