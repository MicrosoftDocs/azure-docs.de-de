---
title: Eigenschaften eines B2B-Gastbenutzers – Azure Active Directory | Microsoft-Dokumentation
description: Eigenschaften und Zustände von Azure Active Directory B2B-Gastbenutzern vor und nach dem Einlösen der Einladung
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c97958add682a4c49d7832843c0b95ffd2663bf
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859630"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Eigenschaften eines Azure Active Directory B2B-Zusammenarbeitsbenutzers

In diesem Artikel werden die Eigenschaften und Zustände beschrieben, die das B2B-Gastbenutzerobjekt in Azure Active Directory (Azure AD) vor und nach dem Einlösen der Einladung aufweisen kann. Ein Azure AD Business-to-Business (B2B) Collaboration-Benutzer ist ein Benutzer mit dem Benutzertyp „Gast“. Dieser Gastbenutzer ist ein der Regel ein Benutzer aus einer Partnerorganisation und verfügt standardmäßig über eingeschränkte Berechtigungen im einladenden Verzeichnis.

Je nach Anforderungen der einladenden Organisation kann ein Azure AD B2B-Zusammenarbeitsbenutzer einen der folgenden Kontozustände aufweisen:

- Zustand 1: Befindet sich in einer externen Instanz von Azure AD und wird als Gastbenutzer in der einladenden Organisation dargestellt. In diesem Fall meldet sich der B2B-Benutzer mit einem Azure AD-Konto an, das zum eingeladenen Mandanten gehört. Wenn die Partnerorganisation nicht Azure AD verwendet, werden die Gastbenutzer trotzdem in Azure AD erstellt. Voraussetzung ist, dass sie ihre Einladung einlösen und Azure AD ihre E-Mail-Adressen überprüft. Eine solche Vereinbarung wird auch als Just-in-Time-Mandant (JIT) oder „viraler“ Mandant bezeichnet.

   > [!IMPORTANT]
   > **Ab dem 31. März 2021** wird das Einlösen von Einladungen durch die Erstellung von nicht verwalteten Azure AD-Konten und -Mandanten für B2B Collaboration-Szenarien von Microsoft nicht mehr unterstützt. Zur Vorbereitung hierauf raten wir Kunden, sich für die [Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md) zu entscheiden. Wir freuen uns über Ihr Feedback zu diesem Feature, das sich derzeit in der öffentlichen Vorschauphase befindet, und möchten noch mehr Möglichkeiten zur Zusammenarbeit schaffen.

- Zustand 2: Befindet sich in einem Microsoft-Konto oder anderen Konto und wird als Gastbenutzer in der Hostorganisation dargestellt. In diesem Fall meldet sich der Gastbenutzer mit einem Microsoft-Konto oder einem Konto eines sozialen Netzwerks (z.B. google.com) an. Die Identität eines eingeladenen Benutzers wird während der Einlösung des Angebots im Verzeichnis der einladenden Organisation als Microsoft-Konto erstellt.

- Zustand 3: Befindet sich im lokalen Active Directory der Hostorganisation und wird mit der Azure AD-Instanz der Hostorganisation synchronisiert. Sie können mithilfe von Azure AD Connect die Partnerkonten als Azure AD B2B-Benutzer mit „UserType = Guest“ mit der Cloud synchronisieren. Siehe [Gewähren des Zugriffs auf Cloudressourcen für lokal verwaltete Partnerkonten](hybrid-on-premises-to-cloud.md).

- Zustand 4: Befindet sich im Azure AD der Hostorganisation mit dem Benutzertyp „Gast“, und die Anmeldeinformationen werden von der Hostorganisation verwaltet.

  ![Diagramm mit den vier Benutzerzuständen](media/user-properties/redemption-diagram.png)


Sehen wir uns nun an, wie ein Azure AD B2B-Kollaborationsbenutzer in Azure AD aussieht.

### <a name="before-invitation-redemption"></a>Vor dem Einlösen der Einladung

Bei Konten mit den Zuständen 1 und 2 werden Gastbenutzer unter Verwendung ihrer eigenen Anmeldeinformationen zur Kollaboration eingeladen. Beim ursprünglichen Senden der Einladung an den Gastbenutzer wird ein Konto in Ihrem Verzeichnis erstellt. Diesem Konto sind keine Anmeldeinformationen zugeordnet, da die Authentifizierung durch den Identitätsanbieter des Gastbenutzers erfolgt. Die Eigenschaft **Quelle** für das Gastbenutzerkonto in Ihrem Verzeichnis ist auf **Eingeladene Benutzer** festgelegt. 

![Screenshot mit Benutzereigenschaften vor dem Einlösen des Angebots](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Nach dem Einlösen der Einladung

Wenn der Gastbenutzer die Einladung annimmt, wird die Eigenschaft **Quelle** basierend auf dem Identitätsanbieter des Gastbenutzers aktualisiert.

Für Gastbenutzer mit dem Zustand 1 wird **Externes Azure Active Directory** als **Quelle** festgelegt.

![Gastbenutzer mit dem Zustand 1 nach dem Einlösen des Angebots](media/user-properties/after-redemption-state1.png)

Für Gastbenutzer mit dem Zustand 2 wird **Microsoft-Konto** als **Quelle** festgelegt.

![Gastbenutzer mit dem Zustand 2 nach dem Einlösen des Angebots](media/user-properties/after-redemption-state2.png)

Für Gastbenutzer mit einem der Zustände 3 oder 4 wird die Eigenschaft **Quelle** auf **Azure Active Directory** bzw. **Windows Server Active Directory** festgelegt (siehe Beschreibung im nächsten Abschnitt).

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Wichtige Eigenschaften von Azure AD B2B-Zusammenarbeitsbenutzern
### <a name="usertype"></a>UserType
Diese Eigenschaft gibt die Beziehung des Benutzers zum Hostmandanten an. Diese Eigenschaft kann zwei Werte aufweisen:
- Mitglied: Dieser Wert weist auf einen Mitarbeiter der Hostorganisation und einen Benutzer auf der Gehaltsliste der Organisation hin. Für diesen Benutzer sollte beispielsweise Zugriff auf rein interne Websites möglich sein. Dieser Benutzer wird nicht als externer Projektmitarbeiter betrachtet.

- Gast: Dieser Wert gibt einen Benutzer an, der im Unternehmen nicht als intern gilt, z.B. ein externer Mitarbeiter, Partner oder Kunde. Diese Benutzer sind z.B. nicht für den Empfang interner Memos vom CEO oder von Unternehmensvorteilen vorgesehen.

  > [!NOTE]
  > Die Eigenschaft „UserType“ steht in keinem Zusammenhang damit, wie sich der Benutzer anmeldet, welche Verzeichnisrolle er hat usw. Die Eigenschaft gibt einfach nur die Beziehung des Benutzers zur Hostorganisation an und ermöglicht der Organisation, Richtlinien basierend auf dieser Eigenschaft zu erzwingen.

Ausführliche Informationen zu Preisen finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory).

### <a name="source"></a>`Source`
Diese Eigenschaft gibt an, wie sich der Benutzer anmeldet.

- Eingeladener Benutzer: Dieser Benutzer wurde eingeladen, hat die Einladung aber noch nicht eingelöst.

- Externes Azure Active Directory: Dieser Benutzer befindet sich in einer externen Organisation und authentifiziert sich mit einem Azure AD-Konto, das zur anderen Organisation gehört. Diese Art der Anmeldung entspricht Zustand 1.

- Microsoft-Konto: Dieser Benutzer befindet sich in einem Microsoft-Konto und authentifiziert sich mit einem Microsoft-Konto. Diese Art der Anmeldung entspricht Zustand 2.

- Windows Server Active Directory: Dieser Benutzer wird über ein lokales Active Directory angemeldet, das zu dieser Organisation gehört. Diese Art der Anmeldung entspricht Zustand 3.

- Azure Active Directory: Dieser Benutzer authentifiziert sich mit einem Azure AD-Konto, das zu dieser Organisation gehört. Diese Art der Anmeldung entspricht Zustand 4.
  > [!NOTE]
  > „Source“ und „UserType“ sind unabhängige Eigenschaften. Ein Wert für „Source“ impliziert keinen bestimmten Wert für „UserType“.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Können Azure AD B2B-Benutzer als Mitglieder statt als Gäste hinzugefügt werden?
Üblicherweise sind die Begriffe „Azure AD B2B-Benutzer“ und „Gastbenutzer“ synonym. Daher wird ein Azure AD B2B-Zusammenarbeitsbenutzer standardmäßig als Benutzer mit dem Benutzertyp „Gast“ hinzugefügt. In einigen Fällen ist eine Partnerorganisation jedoch Teil einer größeren Organisation, zu der auch die Hostorganisation gehört. Hierbei betrachtet die Hostorganisation die Benutzer der Partnerorganisation eher als Mitglieder, nicht als Gäste. Verwenden Sie die Azure AD B2B-Einladungs-Manager-APIs, um einen Benutzer aus der Partnerorganisation als Mitglied zur Hostorganisation einzuladen oder hinzuzufügen.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtern nach Gastbenutzern im Verzeichnis

![Screenshot mit dem Filter für Gastbenutzer](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertieren des Benutzertyps
Mithilfe von PowerShell kann die Eigenschaft „UserType“ von „Mitglied“ in „Gast“ geändert werden und umgekehrt. Die Eigenschaft „UserType“ gibt jedoch die Beziehung eines Benutzers zu der Organisation an. Daher sollten Sie diese Eigenschaft nur ändern, wenn sich die Beziehung des Benutzers zur Organisation ändert. Wenn sich die Beziehung des Benutzers ändert, muss dann der Benutzerprinzipalname (UPN) geändert werden? Soll der Benutzer weiterhin Zugriff auf die gleichen Ressourcen haben? Muss ein Postfach zugewiesen werden? Es empfiehlt sich nicht, die Eigenschaft „UserType“ in PowerShell als alleinige Aktivität zu ändern. Wenn diese Eigenschaft durch die Verwendung von PowerShell unveränderlich wird, empfiehlt es sich nicht, Abhängigkeiten von diesem Wert einzurichten.

## <a name="remove-guest-user-limitations"></a>Aufheben von Einschränkungen für Gastbenutzer
Es gibt eine Vielzahl von Situationen, in denen Sie Ihren Gastbenutzern höhere Berechtigungen einräumen möchten. Sie können Gastbenutzer beliebigen Rollen hinzufügen und sogar die standardmäßigen Einschränkungen für Gastbenutzer im Verzeichnis aufheben, um diesen die gleichen Berechtigungen zu gewähren wie Mitgliedern.

Sie können die standardmäßigen Einschränkungen aufheben, sodass Gastbenutzer im Unternehmensverzeichnis die gleichen Berechtigungen erhalten wie Mitgliedsbenutzer.

![Screenshot mit der Option für externe Benutzer in den Benutzereinstellungen](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kann ich Gastbenutzer in der globalen Adressliste von Exchange sichtbar machen?
Ja. Gastobjekte sind in der globalen Adressliste Ihres Unternehmens standardmäßig nicht sichtbar. Sie können jedoch Azure Active Directory PowerShell verwenden, um diese sichtbar zu machen. Weitere Informationen finden Sie unter **Kann ich Gast-Objekte in der globalen Adressliste sichtbar machen?** im Artikel [Verwalten des Zugriffs von Gast in Microsoft 365-Gruppen](/office365/admin/create-groups/manage-guest-access-in-groups).

## <a name="can-i-update-a-guest-users-email-address"></a>Kann ich die E-Mail-Adresse eines Gastbenutzers aktualisieren?

Wenn ein Gastbenutzer Ihre Einladung annimmt und danach seine E-Mail-Adresse ändert, wird die neue Adresse nicht automatisch im Gastbenutzerobjekt in Ihrem Verzeichnis synchronisiert. Die E-Mail-Eigenschaft wird über die [Microsoft Graph-API](/graph/api/resources/user) erstellt. Sie können die Maileigenschaft über die Microsoft Graph-API, das Admin Center von Exchange oder [Exchange Online-PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser) aktualisieren. Die Änderung wird im Azure AD-Gastbenutzerobjekt widergespiegelt.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
* [Benutzertoken für die B2B-Zusammenarbeit](user-token.md)
* [Zuordnen von Benutzeransprüchen für die B2B-Zusammenarbeit](claims-mapping.md)
