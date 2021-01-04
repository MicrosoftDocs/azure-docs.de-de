---
title: 'Azure Active Directory: Einlösen der Einladung in B2B Collaboration'
description: Beschreibt das Einlösen von Einladungen in Azure AD B2B-Zusammenarbeit für Endbenutzer (einschließlich der Zustimmung zu Datenschutzrichtlinien).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8520afdd05ecce8604ce72596bdf06053217cc2e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173089"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen

Dieser Artikel beschreibt die Möglichkeiten, wie Gastbenutzer auf Ihre Ressourcen zugreifen können, und den Einwilligungsprozess, auf den sie stoßen werden. Wenn Sie eine Einladungs-E-Mail an den Gast senden, enthält die Einladung einen Link, den der Gast einlösen kann, um Zugriff auf Ihre App oder Ihr Portal zu erhalten. Die Einladungs-E-Mail ist nur eine der Möglichkeiten, wie Gäste Zugriff auf Ihre Ressourcen erhalten können. Alternativ können Sie Ihrem Verzeichnis Gäste hinzufügen und ihnen einen direkten Link zu dem Portal oder der App zur Verfügung stellen, das bzw. die Sie freigeben möchten. Unabhängig von der verwendeten Methode werden Gäste schrittweise durch einen erstmaligen Einwilligungsprozess geführt. Dieser Prozess stellt sicher, dass Ihre Gäste den Datenschutzbestimmungen zustimmen und alle [Nutzungsbedingungen](../conditional-access/terms-of-use.md) akzeptieren, die Sie eingerichtet haben.

Wenn Sie Ihrem Verzeichnis einen Gastbenutzer hinzufügen, hat das Gastbenutzerkonto einen Zustimmungsstatus (in PowerShell einsehbar), der zunächst auf **PendingAcceptance** festgelegt ist. Diese Einstellung bleibt bestehen, bis der Gast Ihre Einladung annimmt und Ihrer Datenschutzerklärung und Ihren Nutzungsbedingungen zustimmt. Danach ändert sich der Zustimmungsstatus in **Accepted** (Akzeptiert), und die Zustimmungsseiten werden dem Gast nicht mehr angezeigt.

   > [!IMPORTANT]
   > **Ab dem 31. März 2021** wird das Einlösen von Einladungen durch die Erstellung von nicht verwalteten Azure AD-Konten und -Mandanten für B2B Collaboration-Szenarien von Microsoft nicht mehr unterstützt. Zur Vorbereitung hierauf raten wir Kunden, sich für die [Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md) zu entscheiden. Wir freuen uns über Ihr Feedback zu diesem Feature, das sich derzeit in der öffentlichen Vorschauphase befindet, und möchten noch mehr Möglichkeiten zur Zusammenarbeit schaffen.

## <a name="redemption-through-the-invitation-email"></a>Einlösung über die Einladungs-E-Mail

Wenn Sie Ihrem Verzeichnis [über das Azure-Portal](./b2b-quickstart-add-guest-users-portal.md) einen Gastbenutzer hinzufügen, wird dabei eine Einladungs-E-Mail an den Gast gesendet. Sie können auch Einladungs-E-Mails senden, wenn Sie Ihrem Verzeichnis [mit PowerShell](./b2b-quickstart-invite-powershell.md) Gastbenutzer hinzufügen. Hier ist eine Beschreibung der Erfahrungen des Gasts, wenn er den Link in der E-Mail einlöst.

1. Der Gast erhält eine [Einladungs-E-Mail](./invitation-email-elements.md), die über **Microsoft-Einladungen** gesendet wird.
2. Der Gast wählt **Einladung annehmen** in der E-Mail aus.
3. Der Gast verwendet seine eigenen Anmeldeinformationen, um sich bei Ihrem Verzeichnis anzumelden. Wenn der Gast kein Konto hat, das über einen Verbund in Ihrem Verzeichnis verwendet werden kann, und die Funktion für die [E-Mail-Einmalkennung](./one-time-passcode.md) (One-Time Passcode, OTP) nicht aktiviert ist, wird der Gast aufgefordert, ein persönliches [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) oder ein [Azure AD-Self-Service-Konto](../enterprise-users/directory-self-service-signup.md) zu erstellen. Ausführliche Informationen finden Sie unter [Flow beim Einlösen der Einladung](#invitation-redemption-flow).
4. Der Gast wird schrittweise durch die im Folgenden beschriebene [Zustimmungsbenutzeroberfläche](#consent-experience-for-the-guest) geführt.

## <a name="redemption-through-a-direct-link"></a>Einlösung über einen direkten Link

Alternativ zur Einladungs-E-Mail können Sie einem Gast einen direkten Link zu Ihrer App oder Ihrem Portal zur Verfügung stellen. Zunächst müssen Sie den Gastbenutzer über das [Azure-Portal](./b2b-quickstart-add-guest-users-portal.md) oder [PowerShell](./b2b-quickstart-invite-powershell.md) zu Ihrem Verzeichnis hinzufügen. Dann können Sie eine der [anpassbaren Möglichkeiten zum Bereitstellen von Anwendungen für Benutzer](../manage-apps/end-user-experiences.md) verwenden, einschließlich direkter Anmeldelinks. Wenn ein Gast anstelle der Einladungs-E-Mail einen direkten Link verwendet, wird er dennoch schrittweise durch die Benutzeroberfläche für die erste Zustimmung geführt.

> [!IMPORTANT]
> Der direkte Link muss mandantenspezifisch sein. Mit anderen Worten: Er muss eine Mandanten-ID oder eine überprüfte Domäne enthalten, damit der Gast in Ihrem Mandanten, in dem sich die freigegebene App befindet, authentifiziert werden kann. Eine allgemeine URL wie https://myapps.microsoft.com funktioniert für einen Gast nicht, da sie für die Authentifizierung an seinen Basismandanten weitergeleitet wird. Hier sind einige Beispiele für direkte Links mit Mandantenkontext:
 > - App-Zugriffsbereich: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - App-Zugriffsbereich für eine überprüfte Domäne: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure-Portal: `https://portal.azure.com/<tenant id>`
 > - Einzelne App: Lesen Sie, wie ein [direkter Anmeldelink](../manage-apps/end-user-experiences.md#direct-sign-on-links) verwendet wird.

Es gibt einige Fälle, in denen die Einladungs-E-Mail über einen direkten Link empfohlen wird. Wenn diese Sonderfälle für Ihre Organisation relevant sind, empfiehlt es sich, für die Einladung von Benutzern eine Methode zu verwenden, bei der weiterhin eine Einladungs-E-Mail gesendet wird:
 - Der Benutzer verfügt nicht über ein Azure AD-Konto, ein MSA oder ein E-Mail-Konto in einer Verbundorganisation. Sofern Sie nicht die Funktion für einen einmaligen Passcode verwenden, muss der Gast die Einladungs-E-Mail einlösen, um durch die Schritte zur Erstellung eines MSA geführt zu werden.
 - Manchmal verfügt das eingeladene Benutzerobjekt aufgrund eines Konflikts mit einem Kontaktobjekt (beispielsweise ein Outlook-Kontaktobjekt) möglicherweise über keine E-Mail-Adresse. In diesem Fall muss der Benutzer auf die Einlösungs-URL in der Einladungs-E-Mail klicken.
 - Der Benutzer meldet sich möglicherweise mit einem Alias für die eingeladene E-Mail-Adresse an. (Ein Alias ist eine zusätzliche E-Mail-Adresse, die einem E-Mail-Konto zugeordnet ist.) In diesem Fall muss der Benutzer auf die Einlösungs-URL in der Einladungs-E-Mail klicken.

## <a name="invitation-redemption-flow"></a>Flow beim Einlösen der Einladung

Wenn ein Benutzer in einer [Einladungs-E-Mail](invitation-email-elements.md) auf den Link **Einladung annehmen** klickt, löst Azure AD die Einladung automatisch ein, und zwar auf Grundlage des nachstehend gezeigten Einlösungsflows:

![Screenshot mit Diagramm des Einlösungsflows](media/redemption-experience/invitation-redemption-flow.png)

**Wenn der Benutzerprinzipalname (User Principal Name, UPN) des Benutzers mit einem vorhandenen Azure AD-Konto und einem persönlichen MSA-Konto übereinstimmt, wird der Benutzer aufgefordert, das Konto für das Einlösen auszuwählen.*

1. Azure AD führt eine benutzerbasierte Ermittlung durch, um festzustellen, ob der Benutzer in einem [bestehenden Azure AD-Mandanten](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal) vorhanden ist.

2. Wenn ein Administrator [Direktverbund](./direct-federation.md) aktiviert hat, prüft Azure AD, ob das Domänensuffix des Benutzers mit der Domäne eines konfigurierten SAML/WS-Fed-Identitätsanbieters übereinstimmt und leitet den Benutzer zum vorkonfigurierten Identitätsanbieter um.

3. Wenn ein Administrator [Verbund mit Google](./google-federation.md) aktiviert hat, prüft Azure AD, ob das Domänensuffix des Benutzers gmail.com oder googlemail.com ist und leitet den Benutzer zu Google um.

4. Der Einlösungsprozess prüft, ob der Benutzer bereits ein [Microsoft-Konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) hat.

5. Sobald das **Basisverzeichnis** des Benutzers bestimmt wurde, wird der Benutzer zur Anmeldung an den entsprechenden Identitätsanbieter weitergeleitet.  

6. Wenn in den Schritten 1 bis 4 kein Basisverzeichnis für den eingeladenen Benutzer gefunden wird, ermittelt Azure AD, ob der einladende Mandant das Feature [Einmalkennung per E-Mail für Gastbenutzer](./one-time-passcode.md) aktiviert hat.

7. Wenn [Einmalkennung per E-Mail für Gastbenutzer](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) aktiviert ist, wird in der Einladungs-E-Mail eine Kennung an den Benutzer gesendet. Der Benutzer ruft diese Kennung ab und gibt sie auf der Anmeldeseite von Azure AD ein.

8. Wenn „Einmalkennung per E-Mail für Gastbenutzer“ deaktiviert ist, überprüft Azure AD, ob das Domänensuffix zu einem Consumerkonto gehört. In diesem Fall wird der Benutzer aufgefordert, ein persönliches [Microsoft-Konto](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) zu erstellen. Andernfalls wird der Benutzer aufgefordert, ein [Azure AD-Self-Service-Konto](../enterprise-users/directory-self-service-signup.md) zu erstellen.

9. Azure AD versucht, ein [Azure AD-Self-Service-Konto](../enterprise-users/directory-self-service-signup.md) zu erstellen, indem der Zugriff auf die E-Mail-Adresse überprüft wird. Die Überprüfung des Kontos erfolgt, indem ein Code an die E-Mail-Adresse gesendet wird, den der Benutzer abruft und an Azure AD sendet. Wenn der Mandant des eingeladenen Benutzers jedoch zu einem Verbund gehört oder das Feld AllowEmailVerifiedUsers im Mandanten des eingeladenen Benutzers auf FALSE festgelegt ist, kann der Benutzer die Einlösung nicht abschließen, wodurch der Flow mit einem Fehler endet. Weitere Informationen finden Sie unter [Problembehandlung für die Azure Active Directory B2B Collaboration](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Der Benutzer wird aufgefordert, ein persönliches [Microsoft-Konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) zu erstellen.

11. Nach Authentifizierung beim richtigen Identitätsanbieter wird der Benutzer zu Azure AD umgeleitet, um den [Einwilligungsvorgang](#consent-experience-for-the-guest) abzuschließen.  

Für Just-in-Time-Einlösungen (JIT), bei denen die Einlösung über einen Link zur Mandantenanwendung erfolgt, sind die Schritte 8 bis 10 nicht verfügbar. Wenn ein Benutzer Schritt 6 erreicht und das Feature „Einmalkennung per E-Mail“ nicht aktiviert ist, erhält der Benutzer eine Fehlermeldung und kann die Einladung nicht einlösen. Um diesen Fehler zu vermeiden, müssen Administratoren entweder [Einmalkennung per E-Mail aktivieren](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) oder sicherstellen, dass der Benutzer auf einen Einladungslink klickt.

## <a name="consent-experience-for-the-guest"></a>Einwilligungsbenutzeroberfläche für den Gast

Wenn sich ein Gast zum ersten Mal anmeldet, um auf Ressourcen in einer Partnerorganisation zuzugreifen, wird er schrittweise durch die folgenden Seiten geführt. 

1. Der Gast überprüft die Seite **Berechtigungen überprüfen**, die die Datenschutzerklärung der einladenden Organisation beschreibt. Der Benutzer muss der Nutzung seiner Informationen gemäß den Datenschutzrichtlinien der Organisation **zustimmen**, um fortfahren zu können.

   ![Screenshot: Seite „Berechtigungen überprüfen“](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Informationen dazu, wie Sie als Mandantenadministrator einen Link zu den Datenschutzbestimmungen Ihrer Organisation einrichten, finden Sie unter [Vorgehensweise: Hinzufügen der Datenschutzinformationen mit Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Wenn Nutzungsbedingungen konfiguriert sind, öffnet und überprüft der Gast die Nutzungsbedingungen und wählt dann **Ich stimme zu** aus. 

   ![Screenshot mit neuen Nutzungsbedingungen](media/redemption-experience/terms-of-use-accept.png) 

   Sie können [Nutzungsbedingungen](../conditional-access/terms-of-use.md) unter **Externe Identitäten** > **Nutzungsbedingungen** konfigurieren.

3. Sofern nicht anders angegeben, wird der Gast zum Zugriffsbereich für Apps weitergeleitet, der die Anwendungen auflistet, auf die der Gast zugreifen kann.

   ![Screenshot: Zugriffsbereich für Apps](media/redemption-experience/myapps.png) 

In Ihrem Verzeichnis ändert sich der Wert **Invitation accepted** (Einladung angenommen) des Gasts in **Yes** (Ja). Wenn ein MSA erstellt wurde, wird als **Quelle** des Gasts **Microsoft-Konto** angezeigt. Weitere Informationen zu den Eigenschaften des Gastbenutzerkontos finden Sie unter [Eigenschaften eines Benutzers von Azure AD B2B-Zusammenarbeit](user-properties.md). 

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Add Azure Active Directory B2B collaboration users in the Azure portal](add-users-administrator.md) (Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal)
- [How do information workers add B2B collaboration users to Azure Active Directory?](add-users-information-worker.md) (Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer zu Azure Active Directory hinzu?)
- [Azure Active Directory B2B collaboration API and customization](customize-invitation-api.md#powershell) (Azure Active Directory B2B-Zusammenarbeit: API und Anpassung)
- [Leave an organization as a guest user](leave-the-organization.md) (Verlassen einer Organisation als Gastbenutzer)