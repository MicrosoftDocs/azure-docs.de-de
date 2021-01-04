---
title: Azure AD Multi-Factor Authentication-Aufforderungen und Sitzungslebensdauer
description: Erfahren Sie mehr über die empfohlene Konfiguration für Aufforderungen zur erneuten Authentifizierung mit Azure AD Multi-Factor Authentication und über die Anwendung der Sitzungslebensdauer.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14a65b83195bff48934668ff029f925e13884bad
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743699"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>Optimieren von Aufforderungen zur erneuten Authentifizierung und Grundlegendes zur Sitzungslebensdauer für Azure AD Multi-Factor Authentication

Azure Active Directory (Azure AD) verfügt über mehrere Einstellungen, mit denen bestimmt wird, wie oft Benutzer sich erneut authentifizieren müssen. Diese erneute Authentifizierung kann einstufig wie etwa mit einem Kennwort, mit FIDO oder mit Microsoft Authenticator (kennwortlos) oder mithilfe der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) erfolgen. Sie können diese Einstellungen für die erneute Authentifizierung nach Bedarf für Ihre Umgebung und die gewünschte Benutzererfahrung konfigurieren.

Die Standardkonfiguration von Azure AD sieht für die Anmeldehäufigkeit von Benutzern ein rollierendes Zeitfenster von 90 Tagen vor. Benutzer zur Angabe von Anmeldeinformationen aufzufordern, kann häufig sinnvoll erscheinen, sich aber nachteilig auswirken. Wenn Benutzer darin geschult werden, ihre Anmeldeinformationen ohne Nachdenken einzugeben, können sie diese versehentlich in einer böswilligen Eingabeaufforderung für Anmeldeinformationen angeben.

Es klingt vielleicht beunruhigend, keine erneute Anmeldung von einem Benutzer zu fordern, tatsächlich wird die Sitzung bei einer Verletzung der IT-Richtlinien jedoch widerrufen. Zu den Beispielen zählen eine Kennwortänderung, ein nicht konformes Gerät oder eine Kontodeaktivierung. Sie können [Benutzersitzungen auch explizit mit PowerShell widerrufen](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

In diesem Artikel werden die empfohlenen Konfigurationen und die Funktionsweise der verschiedenen Einstellungen sowie deren Interaktion erläutert.

## <a name="recommended-settings"></a>Empfohlene Einstellungen

Damit Sie Ihren Benutzern die richtige Balance zwischen Sicherheit und Benutzerfreundlichkeit bieten können, indem sie aufgefordert werden, sich mit der richtigen Häufigkeit anzumelden, empfehlen wir die folgenden Konfigurationen:

* Wenn Sie Azure AD Premium haben:
    * Aktivieren Sie das einmalige Anmelden (Single Sign-on, SSO) über mehrere Anwendungen hinweg mithilfe von [verwalteten Geräten](../devices/overview.md) oder [nahtlosem einmaligem Anmelden](../hybrid/how-to-connect-sso.md).
    * Wenn eine erneute Authentifizierung erforderlich ist, verwenden Sie eine Richtlinie für bedingten Zugriff für die [Anmeldehäufigkeit](../conditional-access/howto-conditional-access-session-lifetime.md).
    * Verwenden Sie für Benutzer, die sich über nicht verwaltete oder mobile Geräte anmelden, den bedingten Zugriff, um persistente Browsersitzungen und Richtlinien für die Anmeldehäufigkeit zu aktivieren.
* Wenn Sie über Lizenzen für Microsoft 365-Apps oder den kostenlosen Azure AD-Tarif verfügen:
    * Aktivieren Sie das einmalige Anmelden (Single Sign-on, SSO) über mehrere Anwendungen hinweg mithilfe von [verwalteten Geräten](../devices/overview.md) oder [nahtlosem einmaligem Anmelden](../hybrid/how-to-connect-sso.md).
    * Lassen Sie die Option *Remain signed-in* (Angemeldet bleiben) aktiviert, und weisen Sie Ihre Benutzer an, sie zu akzeptieren.
* Stellen Sie in Szenarien mit mobilen Geräten sicher, dass Ihre Benutzer die Microsoft Authenticator-App verwenden. Diese App wird als Broker für andere Azure AD-Verbund-Apps verwendet und reduziert die Authentifizierungsaufforderungen auf dem Gerät.

Unsere Untersuchungen zeigen, dass diese Einstellungen für die meisten Mandanten geeignet sind. Einige Kombinationen dieser Einstellungen, wie z. B. *Remember MFA* (MFA speichern) und *Remain signed-in* (Angemeldet bleiben), können dazu führen, dass Ihre Benutzer zu oft aufgefordert werden, sich zu authentifizieren. Reguläre Aufforderungen für die erneute Authentifizierung sind schlecht für die Produktivität der Benutzer und machen diese unter Umständen anfälliger für Angriffe.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Konfigurationseinstellungen für die Azure AD-Sitzungslebensdauer

Sie können Optionen für die Azure AD-Sitzungslebensdauer konfigurieren, um die Häufigkeit der Authentifizierungsaufforderungen für Ihre Benutzer zu optimieren. Machen Sie sich mit den Anforderungen Ihres Unternehmens und Ihrer Benutzer vertraut, und konfigurieren Sie die Einstellungen, die für Ihre Umgebung die beste Kombination bieten.

### <a name="evaluate-session-lifetime-policies"></a>Evaluieren der Richtlinien für die Sitzungslebensdauer

Ohne Einstellungen für die Sitzungslebensdauer sind in der Browsersitzung keine persistenten Cookies vorhanden. Jedes Mal, wenn ein Benutzer den Browser schließt und öffnet, wird eine Aufforderung zur erneuten Authentifizierung angezeigt. In Office-Clients ist der Standardzeitraum ein rollierendes Fenster von 90 Tagen. Wenn ein Benutzer mit dieser Office-Standardkonfiguration sein Kennwort zurücksetzt oder 90 Tage lang inaktiv war, wird er aufgefordert, sich mit allen erforderlichen Stufen (der ersten und der zweiten Stufe) erneut zu authentifizieren.

Einem Benutzer werden möglicherweise mehrere MFA-Aufforderungen auf einem Gerät angezeigt, das in Azure AD nicht über eine Identität verfügt. Mehrere Aufforderungen treten auf, wenn jede Anwendung über ein eigenes OAuth-Aktualisierungstoken verfügt, das nicht mit anderen Client-Apps gemeinsam verwendet wird. In diesem Szenario erfolgen mehrere MFA-Aufforderungen, da jede Anwendung ein OAuth-Aktualisierungstoken für die Validierung per MFA anfordert.

In Azure AD legt die restriktivste Richtlinie für die Sitzungslebensdauer fest, wann sich der Benutzer erneut authentifizieren muss. Nehmen Sie das folgende Szenario als Beispiel:

* Aktivieren Sie die Option *Remain signed-in* (Angemeldet bleiben), die ein persistentes Browsercookie verwendet, und
* aktivieren Sie außerdem die Option *Remember MFA for 14 days* (MFA 14 Tage lang speichern).

In diesem Beispielszenario muss sich der Benutzer alle 14 Tage erneut authentifizieren. Dieses Verhalten folgt der restriktivsten Richtlinie, auch wenn die Option *Angemeldet bleiben* selbst es nicht erfordert, dass sich der Benutzer im Browser erneut authentifiziert.

### <a name="managed-devices"></a>Verwaltete Geräte

Geräte, die mithilfe von Azure AD Join oder Azure AD Hybrid Join in Azure AD eingebunden wurden, erhalten ein [primäres Aktualisierungstoken (Primary Refresh Token, PRT)](../devices/concept-primary-refresh-token.md), um das einmalige Anmelden (Single Sign-on, SSO) für verschiedene Anwendungen zu verwenden. Dieses PRT ermöglicht es einem Benutzer, sich einmal auf dem Gerät anzumelden, und erlaubt es IT-Mitarbeitern, sicherzustellen, dass die Sicherheits- und Compliancestandards erfüllt sind. Wenn ein Benutzer aufgefordert werden muss, sich für einige Apps oder Szenarien auf einem verbundenen Gerät häufiger anzumelden, ist dies mithilfe der [Anmeldehäufigkeit mit bedingtem Zugriff](../conditional-access/howto-conditional-access-session-lifetime.md) möglich.

### <a name="show-option-to-remain-signed-in"></a>Anzeigen der Option, angemeldet zu bleiben

Wenn ein Benutzer **Ja** für die Option *Stay signed in?* (Angemeldet bleiben?) während der Anmeldung auswählt, wird im Browser ein persistentes Cookie festgelegt. Dieses persistente Cookie speichert sowohl die erste als auch die zweite Stufe und gilt nur für Authentifizierungsanforderungen im Browser.

![Screenshot der Beispielaufforderung, um angemeldet zu bleiben](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Wenn Sie über eine Azure AD Premium 1-Lizenz verfügen, empfehlen wir die Verwendung der Richtlinie für den bedingten Zugriff für eine *persistente Browsersitzung*. Diese Richtlinie überschreibt die Einstellung *Stay signed in?* (Angemeldet bleiben?) und bietet eine verbesserte Benutzererfahrung. Wenn Sie über keine Azure AD Premium 1-Lizenz verfügen, empfehlen wir Ihnen, für Ihre Benutzer die Einstellung „Angemeldet bleiben“ zu aktivieren.

Weitere Informationen zum Konfigurieren der Option, mit der Benutzer angemeldet bleiben können, finden Sie unter [Anpassen Ihrer Azure AD-Anmeldeseite](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Speichern der Multi-Factor Authentication  

Mit dieser Einstellung können Sie Werte zwischen 1 und 365 Tagen konfigurieren und ein persistentes Cookie im Browser festlegen, wenn ein Benutzer die Option **Die nächsten X Tage nicht erneut fragen** auswählt.

![Screenshot der Beispielaufforderung zum Genehmigen einer Anmeldeanforderung](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Mit dieser Einstellung wird die Anzahl der Authentifizierungen für Web-Apps verringert, und die Anzahl der Authentifizierungen für moderne Authentifizierungsclients wie etwa Office-Clients wird erhöht. Diese Clients geben normalerweise nur nach einer Kennwortzurücksetzung oder nach einer 90-tägigen Inaktivität eine Anforderung aus. Wenn Sie diesen Wert jedoch auf weniger als 90 Tage festlegen, werden die MFA-Standardaufforderungen für Office-Clients verkürzt und die Häufigkeit für erneute Authentifizierungen erhöht. In Verbindung mit **Remain signed-in** (Angemeldet bleiben) oder Richtlinien für den bedingten Zugriff kann sich dadurch die Anzahl der Authentifizierungsanforderungen erhöhen.

Wenn Sie *Remember MFA* (MFA speichern) verwenden und über Azure AD Premium 1-Lizenzen verfügen, empfiehlt sich ggf. eine Migration dieser Einstellungen zur Anmeldehäufigkeit mit bedingtem Zugriff. Oder verwenden Sie stattdessen die Option *Keep me signed in?* (Angemeldet bleiben?).

Weitere Informationen finden Sie unter [Speichern der Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Verwalten von Authentifizierungssitzungen mit bedingtem Zugriff

Die **Anmeldehäufigkeit** ermöglicht es dem Administrator, die Anmeldehäufigkeit auszuwählen, die sowohl für die erste als auch für die zweite Stufe sowohl im Client als auch im Browser gilt. Die Verwendung dieser Einstellungen zusammen mit verwalteten Geräten empfiehlt sich in Szenarien, in denen Sie die Authentifizierungssitzung beschränken müssen, wie etwa für kritische Geschäftsanwendungen.

Bei einer **persistenten Browsersitzung** können Benutzer angemeldet bleiben, nachdem sie ihr Browserfenster geschlossen und erneut geöffnet haben. So ähnlich wie bei der Einstellung *Remain signed-in* (Angemeldet bleiben) wird im Browser ein persistentes Cookie festgelegt. Da dies jedoch vom Administrator konfiguriert ist, ist es nicht erforderlich, dass der Benutzer **Ja** für die Option *Stay signed-in?* (Angemeldet bleiben?) auswählt, was wiederum eine bessere Benutzererfahrung bietet. Wenn Sie die Option *Remain signed-in?* (Angemeldet bleiben?) verwenden, empfehlen wir Ihnen, dass Sie stattdessen die Richtlinie **Persistente Browsersitzung** aktivieren.

Weitere Informationen. Siehe [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../conditional-access/howto-conditional-access-session-lifetime.md)

### <a name="configurable-token-lifetimes"></a>Konfigurierbare Tokengültigkeitsdauer

Diese Einstellung ermöglicht die Konfiguration der Gültigkeitsdauer für das von Azure Active Directory ausgestellte Token. Diese Richtlinie wird ersetzt durch die *Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff*. Wenn Sie aktuell eine *konfigurierbare Tokengültigkeitsdauer* verwenden, empfiehlt es sich, die Migration zu den Richtlinien für bedingten Zugriff zu starten.

## <a name="review-your-tenant-configuration"></a>Überprüfen Ihrer Mandantenkonfiguration  

Nachdem Sie nun wissen, wie unterschiedliche Einstellungen und die empfohlene Konfiguration funktionieren, ist es an der Zeit, die Mandantenkonfiguration zu überprüfen und entsprechende Änderungen vorzunehmen:

Führen Sie die folgenden Schritte aus, um die Option *Remain signed-in* (Angemeldet bleiben) zu konfigurieren oder zu überprüfen:

1. Suchen Sie im Azure AD-Portal nach *Azure Active Directory*, und wählen Sie es aus.
1. Wählen Sie **Unternehmensbranding** aus, und wählen Sie anschließend für jedes Gebietsschema **Option „Angemeldet bleiben“ anzeigen** aus.
1. Wählen Sie *Ja* und anschließend **Speichern** aus.

Führen Sie die folgenden Schritte aus, um die Einstellungen für Multi-Factor Authentication zu speichern:

1. Suchen Sie im Azure AD-Portal nach *Azure Active Directory*, und wählen Sie es aus.
1. Wählen Sie **Sicherheit** und anschließend **MFA** aus.
1. Wählen Sie unter **Konfigurieren** die Option **Zusätzliche cloudbasierte MFA-Einstellungen** aus.
1. Scrollen Sie auf der Seite *Multi-factor authentication service settings* (Diensteinstellungen für Multi-Factor Authentication) zu **remember multi-factor authentication settings** (Multi-Factor Authentication-Einstellungen speichern). Deaktivieren Sie die Einstellung, indem Sie das Kontrollkästchen deaktivieren.

Führen Sie die folgenden Schritte aus, um Richtlinien für bedingten Zugriff für die Anmeldehäufigkeit und eine persistente Browsersitzung zu konfigurieren:

1. Suchen Sie im Azure AD-Portal nach *Azure Active Directory*, und wählen Sie es aus.
1. Wählen Sie **Sicherheit** und anschließend **Bedingter Zugriff** aus.
1. Konfigurieren Sie eine Richtlinie mithilfe der in diesem Artikel beschriebenen empfohlenen Optionen für die Sitzungsverwaltung.

Zum Überprüfen der Tokengültigkeitsdauer [verwenden Sie Azure AD PowerShell, um Azure AD-Richtlinien abzufragen](../develop/configure-token-lifetimes.md#prerequisites). Deaktivieren Sie alle Richtlinien, die Sie eingerichtet haben.

Wenn mehr als eine Einstellung in Ihrem Mandanten aktiviert ist, empfehlen wir Ihnen, die Einstellungen ausgehend von der für Sie verfügbaren Lizenzierung zu aktualisieren. Wenn Sie beispielsweise über Azure AD Premium-Lizenzen verfügen, sollten Sie nur die Richtlinie für bedingten Zugriff für die *Anmeldehäufigkeit* und die *persistente Browsersitzung* verwenden. Wenn Sie über Microsoft 365-Apps oder Azure AD Free-Lizenzen verfügen, verwenden Sie die Konfiguration *Remain signed-in?* (Angemeldet bleiben?).

Wenn Sie konfigurierbare Tokengültigkeitsdauern aktiviert haben, wird diese Funktion bald entfernt. Planen Sie eine Migration zu einer Richtlinie für bedingten Zugriff.

In der folgenden Tabelle werden die Empfehlungen auf Grundlage von Lizenzen zusammengefasst:

|              | Azure AD Free und Microsoft 365-Apps | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [Azure AD Join](../devices/concept-azure-ad-join.md), [Azure AD Hybrid Join](../devices/concept-azure-ad-join-hybrid.md) oder [Nahtlose einmalige Anmeldung](../hybrid/how-to-connect-sso.md) für nicht verwaltete Geräte | Azure AD-Einbindung<br />Azure AD-Hybrideinbindung |
| **Einstellungen für die erneute Authentifizierung** | Angemeldet bleiben                  | Verwenden Sie Richtlinien für bedingten Zugriff für die Anmeldehäufigkeit und eine persistente Browsersitzung. |

## <a name="next-steps"></a>Nächste Schritte

Führen Sie als Erstes die Tutorials [Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md) oder [Verwenden von Risikoerkennungen für Benutzeranmeldungen, um Azure AD Multi-Factor Authentication oder Kennwortänderungen auszulösen](tutorial-risk-based-sspr-mfa.md) durch.
