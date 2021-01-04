---
title: Häufig gestellte Fragen zu B2B Collaboration – Azure Active Directory | Microsoft-Dokumentation
description: Erhalten Sie Antworten auf häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 09/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2825d3776f7b45f6726aaec05484900511ed1477
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172974"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit

Diese häufig gestellten Fragen (FAQs) zur Azure AD B2B-Zusammenarbeit (Azure Active Directory Business-to-Business) werden regelmäßig mit neuen Themen aktualisiert.

   > [!IMPORTANT]
   > **Ab dem 31. März 2021** wird das Einlösen von Einladungen durch Erstellen nicht verwalteter Azure AD-Konten und Mandanten für B2B-Zusammenarbeitsszenarien von Microsoft nicht mehr unterstützt. Zur Vorbereitung hierauf raten wir Kunden, sich für die [Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md) zu entscheiden. Wir freuen uns über Ihr Feedback zu diesem Feature, dass sich in der öffentlichen Vorschauphase befindet, und möchten noch mehr Möglichkeiten zur Zusammenarbeit schaffen.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Können wir unsere Anmeldeseite anpassen, damit sie für Gastbenutzer der B2B-Zusammenarbeit intuitiver ist?
Auf jeden Fall. Lesen Sie unseren [Blogbeitrag zu diesem Feature](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Weitere Informationen zum Anpassen der Anmeldeseite Ihrer Organisation finden Sie unter [Hinzufügen von Unternehmensbranding zu Anmelde- und Zugriffsbereichsseiten](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Können B2B-Zusammenarbeitsbenutzer auf SharePoint Online und OneDrive zugreifen?
Ja. Allerdings ist die Möglichkeit, in SharePoint Online mithilfe der Personenauswahl nach vorhandenen Gastbenutzern zu suchen, standardmäßig auf **Aus** gesetzt. Um die Option zum Suchen nach vorhandenen Gastbenutzern zu aktivieren, setzen Sie **ShowPeoplePickerSuggestionsForGuestUsers** auf **Ein**. Sie können diese Einstellung entweder auf Mandantenebene oder Websitesammlungsebene aktivieren. Sie können diese Einstellung mithilfe der Cmdlets „Set-SPOTenant“ und „Set-SPOSite“ ändern. Mit diesen Cmdlets können Mitglieder nach allen vorhandenen Gastbenutzern im Verzeichnis suchen. Änderungen im Mandantenbereich wirken sich nicht auf SharePoint Online-Websites aus, die bereits bereitgestellt wurden.

### <a name="is-the-csv-upload-feature-still-supported"></a>Wird die Funktion zum Hochladen von CSV-Dateien noch unterstützt?
Ja. Weitere Informationen zur Verwendung der Funktion zum Hochladen von CSV-Dateien finden Sie in [diesem PowerShell-Beispiel](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Wie kann ich meine Einladungs-E-Mails anpassen?
Mithilfe der [B2B-Einladungs-APIs](customize-invitation-api.md) können Sie nahezu jedes Element des Einladungsprozesses anpassen.

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Können Gastbenutzer ihre Multi-Factor Authentication-Methode zurücksetzen?
Ja. Gastbenutzer können ihre Multi-Factor Authentication-Methode auf dieselbe Weise zurücksetzen wie normale Benutzer.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Welche Organisation ist für Multi-Factor Authentication-Lizenzen verantwortlich?
Die einladende Organisation führt Multi-Factor Authentication durch. Die einladende Organisation muss sicherstellen, dass die Organisation genügend Lizenzen für B2B-Benutzer besitzt, die Multi-Factor Authentication verwenden.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Was geschieht, wenn eine Partnerorganisation bereits Multi-Factor Authentication eingerichtet hat? Können wir ihrer Multi-Factor Authentication vertrauen und auf die Verwendung unserer eigenen Multi-Factor Authentication verzichten?
Diese Funktion wird derzeit nicht unterstützt. Wenn für den Zugriff auf die Ressourcen Ihrer Organisation eine mehrstufige Authentifizierung erforderlich ist, muss sich die Partnerorganisation in Ihrer (der einladenden) Organisation für die mehrstufige Authentifizierung registrieren.

### <a name="how-can-i-use-delayed-invitations"></a>Wie kann ich verzögerte Einladungen senden?
Es kann vorkommen, dass eine Organisation Benutzer der B2B-Zusammenarbeit hinzufügen, diese bei Bedarf für Anwendungen bereitstellen und erst dann die Einladungen senden möchte. Sie können die Einladungs-API für die B2B-Zusammenarbeit verwenden, um den Onboardingworkflow benutzerdefiniert anzupassen.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kann ich Gastbenutzer in der globalen Adressliste von Exchange sichtbar machen?
Ja. Gastobjekte sind in der globalen Adressliste (GAL) Ihrer Organisation standardmäßig nicht sichtbar. Sie können jedoch Azure Active Directory PowerShell verwenden, um diese sichtbar zu machen. Weitere Informationen finden Sie unter [Kann ich Gastobjekte in der globalen Adressliste sichtbar machen?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Kann ich einen Gastbenutzer in einen eingeschränkten Administrator konvertieren?
Absolut. Weitere Informationen finden Sie unter [Zuweisen einer Rolle für Gastbenutzer](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Erlaubt die Azure AD B2B-Zusammenarbeit B2B-Benutzern den Zugriff auf das Azure-Portal?
B2B Collaboration-Benutzer benötigen keinen Zugriff auf das Azure-Portal, falls ihnen nicht die Rolle eines eingeschränkten Administrators zugewiesen ist. Wenn ihnen diese Rolle jedoch zugewiesen ist, können sie auf das Portal zugreifen. Auch wenn ein Gastbenutzer, dem keine dieser Administratorrollen zugewiesen ist, auf das Portal zugreift, kann der Benutzer möglicherweise auf bestimmte Funktionen zugreifen. Die Rolle des Gastbenutzers ist mit einigen Berechtigungen im Verzeichnis versehen.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Kann ich den Zugriff auf das Azure-Portal für Gastbenutzer sperren?

Ja. Sie können eine Richtlinie für bedingten Zugriff erstellen, durch die der Zugriff auf das Azure-Portal für alle Gast- und externen Benutzer blockiert wird. Gehen Sie beim Konfigurieren dieser Richtlinie umsichtig vor, um zu vermeiden, dass versehentlich der Zugriff für Mitglieder und Administratoren gesperrt wird.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
2. Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus. 
3. Wählen Sie unter **Verwalten** die Option **Sicherheit** aus.
4. Wählen Sie unter **Schützen** die Option **Bedingter Zugriff** aus. Wählen Sie **Neue Richtlinie**.
5. Geben Sie auf der Seite **Neu** im Textfeld **Name** einen Namen für die Richtlinie ein (z. B. „Blockieren des Zugriffs auf das Portal durch Gastbenutzer“).
6. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**.
7. Wählen Sie auf der Registerkarte **Einschließen** die Option **Benutzer und Gruppen auswählen** aus, und wählen Sie dann **Alle Gast- und externen Benutzer (Vorschau)** aus.
9. Wählen Sie **Fertig** aus.
10. Wählen Sie auf der Seite **Neu** im Abschnitt **Zuweisungen** die Option **Cloud-Apps oder -aktionen** aus.
11. Wählen Sie auf der Seite **Cloud-Apps oder -aktionen** die Option **Apps auswählen** aus, und wählen Sie dann **Auswählen** aus.
12. Wählen Sie auf der Seite **Auswählen** die Option **Microsoft Azure-Verwaltung** und dann **Auswählen** aus.
13. Wählen Sie auf der Seite **Cloud-Apps oder -aktionen** die Option **Fertig** aus.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Unterstützt die Azure AD B2B-Zusammenarbeit Multi-Factor Authentication und E-Mail-Konten für Consumer?
Ja. Sowohl Multi-Factor Authentication als auch E-Mail-Konten für Consumer werden für die Azure AD B2B-Zusammenarbeit unterstützt.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Wird die Kennwortzurücksetzung für Azure AD B2B-Zusammenarbeitsbenutzer unterstützt?
Wenn Ihr Azure AD-Mandant das Basisverzeichnis für einen Benutzer ist, können Sie [das Kennwort des Benutzers über das Azure-Portal zurücksetzen](../fundamentals/active-directory-users-reset-password-azure-portal.md). Aber Sie können das Kennwort für einen Gastbenutzer nicht direkt zurücksetzen, wenn er sich mit einem Konto anmeldet, das von einem anderen Azure AD-Verzeichnis oder einem externen Identitätsanbieter verwaltet wird. Nur der Gastbenutzer oder ein Administrator im Basisverzeichnis des Benutzers kann das Kennwort zurücksetzen. Hier sind einige Beispiele für die Funktionsweise der Kennwortzurücksetzung für Gastbenutzer:
 
* Gastbenutzer, die sich mit einem Microsoft-Konto anmelden (z.B. guestuser@live.com), können ihre eigenen Kennwörter über die Self-Service-Kennwortzurücksetzung (SSPR) für Microsoft-Konten zurücksetzen. Informationen finden Sie unter [Zurücksetzen des Kennworts Ihres Microsoft-Kontos](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Gastbenutzer, die sich mit einem Google-Konto oder einem anderen externen Identitätsanbieter anmelden, können ihre eigenen Kennwörter mit der SSPR-Methode ihres Identitätsanbieters zurücksetzen. Beispielsweise kann ein Gastbenutzer mit dem Google-Konto guestuser@gmail.com sein Kennwort mithilfe der Anweisungen in [Passwort ändern oder zurücksetzen](https://support.google.com/accounts/answer/41078) zurücksetzen.
* Wenn der Identitätsmandant ein Just-In-Time-Mandant (JIT) oder ein „viraler“ Mandant ist (es sich also um einen separaten, nicht verwalteten Azure-Mandanten handelt), kann nur der Gastbenutzer sein Kennwort zurücksetzen. In einigen Fällen [übernimmt die Organisation die Verwaltung der viralen Mandanten](../enterprise-users/domains-admin-takeover.md), die erstellt werden, wenn Mitarbeiter ihre geschäftliche E-Mail-Adresse für die Anmeldung bei Diensten verwenden. Wenn die Organisation einen viralen Mandanten übernommen hat, kann nur ein Administrator in dieser Organisation das Kennwort des Benutzers zurücksetzen oder SSPR aktivieren. Bei Bedarf können Sie als einladende Organisation das Gastkonto für den Benutzer aus dem Verzeichnis entfernen und erneut eine Einladung senden.

* Wenn das Basisverzeichnis des Gastbenutzers Ihr Azure AD-Mandant ist, können Sie das Kennwort des Benutzers zurücksetzen. Sie könnten beispielsweise einen Benutzer erstellen oder einen Benutzer über Ihr lokales Active Directory-Verzeichnis synchronisieren und „UserType“ auf „Gast“ festlegen. Da sich dieser Benutzer in Ihrem Verzeichnis befindet, können Sie sein Kennwort über das Azure-Portal zurücksetzen.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Bietet Microsoft Dynamics 365 Onlineunterstützung für die Azure AD B2B-Zusammenarbeit?
Ja, Dynamics 365 (online) unterstützt die Azure AD B2B-Zusammenarbeit. Weitere Informationen finden Sie im Dynamics 365-Artikel [Laden Sie Benutzer zu Dynamics 365 mit Azure Active Directory B2B Zusammenarbeit ein](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Wie lang ist ein anfängliches Kennwort für einen neu erstellten B2B-Zusammenarbeitsbenutzer gültig?
Azure AD verfügt über einen festen Satz von Anforderungen an Zeichen, Kennwortstärke und Kontosperre, die gleichermaßen für alle Azure AD-Cloudbenutzerkonten gelten. Cloudbenutzerkonten sind Konten, die nicht mit einem anderen Identitätsanbieter verbunden sind, z.B. 
* Microsoft-Konto
* Facebook
* Active Directory-Verbunddienste (AD FS)
* Ein anderer Cloudmandant (für B2B-Zusammenarbeit)

Bei Verbundkonten richtet sich die Kennwortrichtlinie nach der Richtlinie, die im lokalen Mandanten und den Einstellungen des Microsoft-Kontos des Benutzers angewendet wird.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Eine Organisation möchte ggf. verschiedene Funktionen in ihren Anwendungen für Mandanten- und Gastbenutzer bereitstellen. Gibt es dafür eine Standardanleitung? Ist das Vorhandensein des Identitätsanbieteranspruchs das richtige Modell dafür?
Ein Gastbenutzer kann einem beliebigen Identitätsanbieter zur Authentifizierung verwenden. Weitere Informationen finden Sie unter [Eigenschaften eines B2B-Zusammenarbeitsbenutzers](user-properties.md). Verwenden Sie die **UserType**-Eigenschaft, um die Benutzerfreundlichkeit zu bestimmen. Der **UserType**-Anspruch ist derzeit nicht im Token enthalten. Anwendungen sollten zur Abfrage des Verzeichnisses nach dem Benutzer und zum Abrufen seiner UserType-Eigenschaft die Microsoft Graph-API verwenden.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Wo finde ich eine Community für die B2B-Zusammenarbeit, um dort Lösungen und Ideen zu teilen?
Ihr Feedback zur Verbesserung der B2B-Zusammenarbeit nehmen wir sehr ernst. Teilen Sie uns Ihre Benutzerszenarien und bewährten Methoden mit, und lassen Sie uns wissen, was Ihnen an der Azure AD B2B-Zusammenarbeit gefällt. Werden Sie Teil der [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) und posten Sie Diskussionsbeiträge.
 
Senden Sie uns zudem über die Website [B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) Ihre Anregungen und Ideen, und stimmen Sie über zukünftige Features ab.

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Können eine automatisch eingelöste Einladung gesendet werden, damit der Benutzer sofort loslegen kann, oder muss sich der Benutzer stets bis zur Einlöse-URL durchklicken?
Sie können andere Benutzer aus der Partnerorganisation über die Benutzeroberfläche, über PowerShell-Skripts oder über APIs einladen. Dann können Sie dem Gastbenutzer einen direkten Link zu einer freigegebenen App senden. In den meisten Fällen muss der Eingeladene keine E-Mail-Einladung mehr öffnen und auf eine Einlösungs-URL klicken. Informationen finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Wie erfolgt die B2B-Zusammenarbeit, wenn der eingeladene Partner den Partnerverbund verwendet, um die eigene lokale Authentifizierung hinzuzufügen?
Wenn der Partner über einen Azure AD-Mandanten verfügt, der über einen Verbund in die lokale Authentifizierungsinfrastruktur eingebunden ist, wird automatisch lokales einmaliges Anmelden (Single Sign-On, SSO) ausgeführt. Wenn der Partner keinen Azure AD-Mandanten aufweist, wird für neue Benutzer ein Azure AD-Konto erstellt. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Stimmt es, dass Azure AD B2B keine E-Mail-Adressen von gmail.com und outlook.com akzeptiert und B2C für diese Arten von Konten verwendet wird?
Wir heben die Unterschiede zwischen der B2B- und B2C-Zusammenarbeit (Business-to-Consumer) im Hinblick auf die unterstützten Identitäten auf. Die verwendete Identität ist kein gutes Kriterium für die Entscheidung zwischen der Verwendung von B2B und B2C. Informationen zur Auswahl der Option für die Zusammenarbeit finden Sie unter [Vergleich der B2B- und B2C-Zusammenarbeit in Azure Active Directory](compare-with-b2c.md).

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Kann ein lokales Azure AD B2C-Konto zu einem Azure AD-Mandanten für die B2B-Zusammenarbeit eingeladen werden?
Nein. Ein lokales Azure AD B2C-Konto kann nur für die Anmeldung beim Azure AD B2C-Mandanten verwendet werden. Das Konto kann nicht zum Anmelden bei einem Azure AD-Mandanten verwendet werden. Das Einladen eines lokalen Azure AD B2C-Kontos zu einem Azure AD-Mandanten für die B2B-Zusammenarbeit wird nicht unterstützt.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Welche Anwendungen und Dienste unterstützen Azure B2B-Gastbenutzer?
Alle in Azure AD integrierten Anwendungen unterstützen Azure B2B-Gastbenutzer, aber sie müssen einen als Mandant eingerichteten Endpunkt verwenden, um Gastbenutzer zu authentifizieren. Sie müssen möglicherweise auch [die Ansprüche im SAML-Token anpassen](claims-mapping.md), das ausgegeben wird, wenn sich ein Gastbenutzer bei der App authentifiziert. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kann Multi-Factor Authentication für B2B-Gastbenutzer erzwungen werden, wenn unsere Partner nicht über Multi-Factor Authentication verfügen?
Ja. Weitere Informationen finden Sie unter [Bedingter Zugriff für Benutzer der B2B-Zusammenarbeit](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>In SharePoint können Listen für das Zulassen und Ablehnen des Zugriffs durch externe Benutzer definiert werden. Ist dies in Azure möglich?
Ja. Die Azure AD B2B-Zusammenarbeit unterstützt Zulassungs- und Verweigerungslisten. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Welche Lizenzen sind für die Verwendung von Azure AD B2B erforderlich?
Informationen darüber, welche Lizenzen Ihre Organisation für die Verwendung von Azure AD B2B benötigt, finden Sie unter [Preise für externe Identitäten](external-identities-pricing.md).

### <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)