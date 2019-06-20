---
title: Was sind Bedingungen beim bedingten Zugriff in Azure Active Directory? | Microsoft-Dokumentation
description: Es wird beschrieben, wie Bedingungen beim bedingten Zugriff in Azure Active Directory zum Auslösen einer Richtlinie verwendet werden.
services: active-directory
keywords: bedingter Zugriff auf Apps, bedingter Zugriff mit Azure AD, sicherer Zugriff auf Unternehmensressourcen, Richtlinien für bedingten Zugriff
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/17/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2caedeb5cd4d0f920ffa49b33b0dca8bea9938cb
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861542"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Was sind Bedingungen beim bedingten Zugriff in Azure Active Directory?

Mit dem [bedingten Zugriff von Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) können Sie den Zugriff von Benutzern auf Ihre Cloud-Apps steuern. Bei einer Richtlinie für bedingten Zugriff definieren Sie die Reaktion („Then do this“) für den Grund der Auslösung Ihrer Richtlinie („When this happens“).

![Ursache und Reaktion](./media/conditions/10.png)


Im Kontext des bedingten Zugriffs ist **When this happens** eine **Bedingung**. **Then do this** ist eine **Zugriffssteuerung**. Die Kombination aus Ihren Bedingungen und Zugriffssteuerungen ergibt eine Richtlinie für bedingten Zugriff.

![Richtlinie für bedingten Zugriff](./media/conditions/61.png)


Bedingungen, die Sie nicht in einer Richtlinie für bedingten Zugriff konfigurieren, werden nicht angewendet. Einige Bedingungen sind [obligatorisch](best-practices.md) für die Anwendung einer Richtlinie für bedingten Zugriff auf eine Umgebung.

Dieser Artikel bietet eine Übersicht über die Bedingungen und ihre Verwendung in einer Richtlinie für bedingten Zugriff. 

## <a name="users-and-groups"></a>Benutzer und Gruppen

Die Bedingung für Benutzer und Gruppen ist in einer Richtlinie für bedingten Zugriff obligatorisch. In Ihrer Richtlinie können Sie entweder die Option **Alle Benutzer** oder bestimmte Benutzer und Gruppen auswählen.

![Benutzer und Gruppen](./media/conditions/111.png)

Wenn Sie **Alle Benutzer** auswählen, wird Ihre Richtlinie auf alle Benutzer (einschließlich Gastbenutzern) im Verzeichnis angewendet.

Wenn Sie **Benutzer und Gruppen auswählen** auswählen, können Sie die folgenden Optionen festlegen:

* Mit **Alle Gastbenutzer** können Sie eine Richtlinie auf B2B-Gastbenutzer anwenden. Diese Bedingung gilt für jedes Benutzerkonto, dessen Attribut **userType** **guest** entspricht. Verwenden Sie diese Einstellung, wenn eine Richtlinie angewendet werden muss, sobald das Konto in einem Einladungsfluss in Azure AD erstellt wurde.

* Mit **Verzeichnisrollen** können Sie eine Richtlinie basierend auf der zugewiesenen Benutzerrolle anwenden. Diese Bedingung unterstützt Verzeichnisrollen wie **Globaler Administrator** oder **Kennwortadministrator**.

* Mit **Benutzer und Gruppen** können Sie bestimmte Benutzergruppen adressieren. Beispielsweise können Sie eine Gruppe auswählen, die alle Mitglieder der HR-Abteilung enthält, wenn eine HR-App als Cloud-App ausgewählt wurde. Als Gruppe gilt eine beliebige Gruppe in Azure AD, einschließlich dynamischer oder zugewiesener Sicherheits- und Verteilungsgruppen.

Sie können bestimmte Benutzer und Gruppen auch aus einer Richtlinie ausschließen. Ein häufiger Anwendungsfall sind Dienstkonten, falls bei Ihrer Richtlinie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erzwungen wird.

Die Ausrichtung auf bestimmte Gruppen von Benutzern ist für die Bereitstellung einer neuen Richtlinie nützlich. Bei einer neuen Richtlinie sollten Sie nur eine Ausrichtung auf eine Anfangsgruppe von Benutzern festlegen, um das Verhalten der Richtlinie zu überprüfen.

## <a name="cloud-apps-and-actions"></a>Cloud-Apps oder -aktionen

Eine Cloud-App ist eine Website, ein Dienst oder Endpunkt, die bzw. der durch den Azure AD-Anwendungsproxy geschützt wird. Eine ausführliche Beschreibung der unterstützten Cloud-Apps finden Sie unter [Zuweisungen von Cloud-Apps](technical-reference.md#cloud-apps-assignments). Die Bedingung **Cloud-Apps oder -aktionen** ist in einer Richtlinie für bedingten Zugriff obligatorisch. In Ihrer Richtlinie können Sie entweder **Alle Cloud-Apps** auswählen oder mithilfe von **Apps auswählen** bestimmte Apps angeben.

Organisationen können zwischen folgenden Optionen auswählen:

* **Alle Cloud-Apps**, wenn Basisrichtlinien angewendet werden, die für die gesamte Organisation gelten sollen. Verwenden Sie diese Auswahl für Richtlinien, die eine Multi-Factor Authentication erfordern, wenn für eine Cloud-App ein Anmelderisiko erkannt wird. Eine auf alle Cloud-Apps angewendete Richtlinie gilt für den Zugriff auf alle Websites und Dienste. Diese Einstellung ist nicht auf die Cloud-Apps in der Liste „Apps auswählen“ beschränkt.
* **Apps auswählen**, um bestimmte Dienste als Ziel der Richtlinie festzulegen. Sie können beispielsweise vorgeben, dass Benutzer über ein konformes Gerät verfügen müssen, um auf SharePoint Online zuzugreifen. Diese Richtlinie wird auch auf andere Dienste angewendet, wenn diese auf SharePoint-Inhalte zugreifen. Ein Beispiel ist Microsoft Teams.

> [!NOTE]
> Sie können bestimmte Apps von einer Richtlinie ausschließen. Diese Apps unterliegen jedoch weiterhin den Richtlinien, die für die Dienste gelten, auf die sie zugreifen.

**Benutzeraktionen** sind Aufgaben, die von einem Benutzer ausgeführt werden können. Die einzige derzeit unterstützte Aktion ist **Sicherheitsinformationen registrieren (Vorschau)** . Dadurch kann eine Richtlinie für bedingten Zugriff angewendet werden, wenn Benutzer, für die die kombinierte Registrierung aktiviert ist, versuchen, ihre Sicherheitsinformationen zu registrieren. Weitere Informationen finden Sie im Artikel [Aktivieren der kombinierten Registrierung von Sicherheitsinformationen (Vorschauversion)](../authentication/howto-registration-mfa-sspr-combined.md).

## <a name="sign-in-risk"></a>Anmelderisiko

Ein Anmelderisiko ist ein Indikator für die Wahrscheinlichkeit (hoch, mittel oder gering), dass eine Anmeldung nicht vom rechtmäßigen Besitzer eines Benutzerkontos durchgeführt wurde. Azure AD berechnet die Stufe des Anmelderisikos bei der Benutzeranmeldung. Sie können die berechnete Anmelderisikostufe als Bedingung in einer Richtlinie für den bedingten Zugriff verwenden.

![Anmelderisikostufen](./media/conditions/22.png)

Für die Nutzung dieser Bedingung muss [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) aktiviert sein.
 
Häufige Anwendungsfälle für diese Bedingung sind Richtlinien, für die folgende Schutzmaßnahmen gelten: 

- Blockieren von Benutzern mit einem hohen Anmelderisiko. Diese Schutzmaßnahme verhindert, dass potenziell unrechtmäßige Benutzer auf Ihre Cloud-Apps zugreifen. 
- Erzwingen der mehrstufigen Authentifizierung für Benutzer mit einem mittleren Anmelderisiko. Durch das Erzwingen der mehrstufigen Authentifizierung erhalten Sie zusätzliche Sicherheit, dass die Anmeldung vom rechtmäßigen Besitzer eines Kontos durchgeführt wird.

Weitere Informationen finden Sie unter [Blockieren des Zugriffs, wenn ein Sitzungsrisiko erkannt wird](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Geräteplattformen

Die Geräteplattform ist durch das Betriebssystem gekennzeichnet, das auf dem Gerät ausgeführt wird. Azure AD identifiziert die Plattform, indem vom Gerät, z.B. einem Benutzer-Agent, bereitgestellte Informationen verwendet werden. Diese Informationen wurden nicht überprüft. Es wird empfohlen, auf alle Plattformen eine Richtlinie anzuwenden. Die Richtlinie sollte den Zugriff blockieren, die Einhaltung der Microsoft Intune-Richtlinien erfordern oder festlegen, dass das Gerät in die Domäne eingebunden ist. Standardmäßig wird eine Richtlinie auf alle Geräteplattformen angewendet. 


![Konfigurieren von Geräteplattformen](./media/conditions/24.png)

Eine Liste der unterstützten Geräteplattformen finden Sie unter [Geräteplattformbedingung](technical-reference.md#device-platform-condition).


Ein häufiger Anwendungsfall für diese Bedingung ist eine Richtlinie, mit der der Zugriff auf Ihre Cloud-Apps auf [verwaltete Geräte](require-managed-devices.md) beschränkt wird. Informationen zu weiteren Szenarien, z.B. zur Geräteplattformbedingung, finden Sie unter [App-basierter bedingter Zugriff mit Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>Gerätestatus

Die Gerätestatusbedingung schließt in Hybrid-Azure AD eingebundene und als konform gekennzeichnete Geräte von einer Richtlinie für bedingten Zugriff aus. 


![Konfigurieren des Gerätstatus](./media/conditions/112.png)

Diese Bedingung ist hilfreich, wenn eine Richtlinie nur auf ein nicht verwaltetes Gerät angewendet werden soll, um zusätzliche Sitzungssicherheit zu bieten. Erzwingen Sie z.B. nur dann die Microsoft Cloud App Security-Sitzungssteuerung, wenn ein Gerät nicht verwaltet wird. 

## <a name="locations"></a>Standorte

Mit Standorten können Sie Bedingungen definieren, die darauf basieren, wo ein Verbindungsversuch stattgefunden hat. 

![Konfigurieren von Standorten](./media/conditions/25.png)

Häufige Anwendungsfälle für diese Bedingung sind Richtlinien mit den folgenden Schutzmaßnahmen:

- Erzwingen der mehrstufigen Authentifizierung für Benutzer beim Zugriff auf einen Dienst, wenn sie sich außerhalb des Unternehmensnetzwerks befinden  

- Blockieren des Zugriffs auf einen Dienst für Benutzer, die sich in bestimmten Ländern oder Regionen aufhalten 

Weitere Informationen finden Sie unter [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Client-Apps

Standardmäßig wird eine Richtlinie für bedingten Zugriff auf die folgenden Apps angewendet:

- **[Browser-Apps](technical-reference.md#supported-browsers)** : Hierzu zählen Websites mit den SAML-, WS-Verbund- oder OpenID Connect-Web-SSO-Protokollen. Dies umfasst auch alle Websites oder Webdienste, die als vertraulicher OAuth-Client registriert wurden. Ein Beispiel hierfür ist eine Office 365 SharePoint-Website. 

- **[Mobile Apps und Desktop-Apps mit moderner Authentifizierung](technical-reference.md#supported-mobile-applications-and-desktop-clients)** : Hierzu zählen die Office-Desktop-Apps und -Smartphone-Apps. 


Darüber hinaus können Sie eine Richtlinie auf bestimmte Client-Apps anwenden, die keine moderne Authentifizierung verwenden, beispielsweise:

- **[Exchange ActiveSync-Clients](conditions.md#exchange-activesync-clients)** : Wenn eine Richtlinie Exchange ActiveSync blockiert, erhalten betroffene Benutzer eine einzelne Quarantäne-E-Mail mit Informationen zum Grund für die Blockierung. Bei Bedarf enthält die E-Mail Anweisungen für die Registrierung des Geräts bei Intune.

- **[Andere Clients](block-legacy-authentication.md)** : Zu diesen Apps zählen Clients, die die Standardauthentifizierung mit E-Mail-Protokollen wie IMAP, MAPI, POP und SMTP nutzen, sowie ältere Office-Apps, die keine moderne Authentifizierung verwenden. Weitere Informationen finden Sie unter [So funktioniert die moderne Authentifizierung für Office 2013- und Office 2016-Client-Apps](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Client-Apps](./media/conditions/41.png)

Gängige Anwendungsfälle für diese Bedingung sind Richtlinien mit den folgenden Anforderungen:

- **[Erfordern eines verwalteten Geräts](require-managed-devices.md)** für mobile Apps und Desktopanwendungen, die Daten auf ein Gerät herunterladen. Der Browserzugriff ist dabei mit jedem Gerät zulässig. In diesem Szenario wird das Speichern und Synchronisieren von Dokumenten auf bzw. mit einem nicht verwalteten Gerät verhindert. Mit dieser Methode können Sie die Wahrscheinlichkeit von Datenverlusten verringern, wenn das Gerät verloren geht oder gestohlen wird.

- **[Erfordern eines verwalteten Geräts](require-managed-devices.md)** für Apps, die ActiveSync für den Zugriff auf Exchange Online verwenden.

- **[Blockieren der Legacyauthentifizierung](block-legacy-authentication.md)** bei Azure AD (andere Clients).

- Blockieren des Zugriffs über Webanwendungen und Zulassen des Zugriffs über mobile Apps und Desktopanwendungen.



### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-Clients

Sie können **Exchange ActiveSync-Clients** nur auswählen, wenn Folgendes zutrifft:


- Microsoft Office 365 Exchange Online ist die einzige Cloud-App, die Sie ausgewählt haben.

    ![Cloud-Apps](./media/conditions/32.png)

- Sie haben keine anderen Bedingungen in einer Richtlinie konfiguriert. Sie können den Geltungsbereich dieser Bedingung jedoch so eingrenzen, dass sie nur für [unterstützte Plattformen](technical-reference.md#device-platform-condition) gilt.
 
    ![Anwendend er Richtlinie nur auf unterstützte Plattformen](./media/conditions/33.png)


Wenn der Zugriff blockiert wird, weil ein [verwaltetes Gerät](require-managed-devices.md) erforderlich ist, erhalten die betroffenen Benutzer eine einzelne E-Mail mit Anweisungen zur Verwendung von Intune. 

Wenn eine genehmigte App erforderlich ist, erhalten die betroffenen Benutzer Anleitungen zum Installieren und Verwenden des mobilen Outlook-Clients.

In anderen Fällen, z. B. wenn eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist, werden die betroffenen Benutzer blockiert, weil Clients, die die Standardauthentifizierung verwenden, MFA nicht unterstützen.

Diese Einstellung kann nur auf Benutzer und Gruppen angewendet werden. Gäste und Rollen werden nicht unterstützt. Wenn eine Gast- oder Rollenbedingung konfiguriert ist, werden alle Benutzer blockiert, da der bedingte Zugriff nicht bestimmen kann, ob die Richtlinie für den Benutzer gelten soll oder nicht.


 Weitere Informationen finden Sie unter

- [Einrichten von SharePoint Online und Exchange Online für bedingten Zugriff mit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)
 
- [App-basierter bedingter Zugriff mit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 



## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Konfigurieren einer Richtlinie für bedingten Zugriff finden Sie unter [Schnellstart: Anfordern der mehrstufigen Authentifizierung für bestimmte Apps über den bedingten Zugriff von Azure Active Directory](app-based-mfa.md).

- Wie Sie Richtlinien für bedingten Zugriff für Ihre Umgebung konfigurieren, erfahren Sie unter [Best Practices für den bedingten Zugriff in Azure Active Directory](best-practices.md). 

