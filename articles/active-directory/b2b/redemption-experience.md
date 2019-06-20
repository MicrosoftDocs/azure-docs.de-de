---
title: Einlösen von Einladungen in B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Beschreibt das Einlösen von Einladungen in Azure AD B2B-Zusammenarbeit für Endbenutzer (einschließlich der Zustimmung zu Datenschutzrichtlinien).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99fe386ec75e60a16f3095f22dccc3852883f8aa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304510"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen

Wenn Sie über Azure Active Directory (Azure AD) B2B-Zusammenarbeit mit Benutzern aus Partnerorganisationen zusammenarbeiten möchten, können Sie Gastbenutzer einladen, um ihnen Zugriff auf freigegebene Apps zu gewähren. Nachdem ein Gastbenutzer über die Benutzeroberfläche dem Verzeichnis hinzugefügt oder über PowerShell eingeladen wurde, muss er zunächst einen Zustimmungsprozess durchlaufen und den [Datenschutzrichtlinien](#privacy-policy-agreement) zustimmen. Für diesen Prozess gibt es mehrere Möglichkeiten:

- Der Einladende sendet einen direkten Link zu einer freigegebenen App. Der Eingeladene klickt auf den Link, um sich anzumelden, akzeptiert die Datenschutzrichtlinien und greift nahtlos auf die freigegebene Ressource zu. (Der Gastbenutzer erhält weiterhin eine E-Mail-Einladung mit einer Einlösungs-URL, diese wird jedoch nur noch in einigen Sonderfällen benötigt.)  
- Der Gastbenutzer erhält eine Einladungs-E-Mail und klickt auf die Einlösungs-URL. Im Rahmen der erstmaligen Anmeldung wird er zur Akzeptierung der Datenschutzrichtlinien aufgefordert.

## <a name="redemption-through-a-direct-link"></a>Einlösung über einen direkten Link

Ein Einladender kann einem Gastbenutzer als Einladung einen [direkten Link zu einer freigegebenen App](../manage-apps/end-user-experiences.md#direct-sign-on-links) senden. Der Gastbenutzer muss sich lediglich bei der App anmelden, die für ihn freigegeben wurde. Er kann auf einen Link zu der App klicken, die Datenschutzrichtlinien prüfen und akzeptieren und anschließend nahtlos auf die App zugreifen. In den meisten Fällen müssen Gastbenutzer nicht mehr auf eine Einlösungs-URL in einer Einladungs-E-Mail klicken.

Wenn Sie einen Gastbenutzer über die Benutzeroberfläche eingeladen oder ihm im Rahmen der PowerShell-Einladung eine E-Mail-Einladung gesendet haben, erhält der Eingeladene weiterhin eine Einladungs-E-Mail. Diese E-Mail ist in folgenden Sonderfällen hilfreich:

- Der Benutzer besitzt kein Azure AD-Konto und kein Microsoft-Konto (Microsoft Account, MSA). In diesem Fall muss der Benutzer vor dem Klicken auf den Link ein MSA erstellen oder die Einlösungs-URL aus der Einladungs-E-Mail verwenden. Im Rahmen des Einlösungsprozesses wird der Benutzer automatisch zur Erstellung eines MSA aufgefordert.
- Manchmal verfügt das eingeladene Benutzerobjekt aufgrund eines Konflikts mit einem Kontaktobjekt (beispielsweise ein Outlook-Kontaktobjekt) möglicherweise über keine E-Mail-Adresse. In diesem Fall muss der Benutzer auf die Einlösungs-URL in der Einladungs-E-Mail klicken.
- Der Benutzer meldet sich möglicherweise mit einem Alias für die eingeladene E-Mail-Adresse an. (Ein Alias ist eine zusätzliche E-Mail-Adresse, die einem E-Mail-Konto zugeordnet ist.) In diesem Fall muss der Benutzer auf die Einlösungs-URL in der Einladungs-E-Mail klicken.

Wenn diese Sonderfälle für Ihre Organisation relevant sind, empfiehlt es sich, für die Einladung von Benutzern eine Methode zu verwenden, bei der weiterhin eine Einladungs-E-Mail gesendet wird. Auch Benutzer, für die diese Sonderfälle nicht gelten, können auf die URL in einer Einladungs-E-Mail klicken, um Zugriff zu erhalten.

## <a name="redemption-through-the-invitation-email"></a>Einlösung über die Einladungs-E-Mail

Benutzer, die über eine Methode mit Einladungs-E-Mail eingeladen wurden, können eine Einladung auch über die Einladungs-E-Mail einlösen. Ein eingeladener Benutzer kann auf die Einlösungs-URL in der E-Mail klicken und anschließend die Datenschutzrichtlinien prüfen und akzeptieren. Eine ausführlichere Beschreibung dieses Prozesses finden Sie hier:

1.  Der Eingeladene erhält von **Microsoft Invitations** eine Einladungs-E-Mail.
2.  Der Eingeladene klickt in der E-Mail auf **Erste Schritte**.
3.  Falls der Eingeladene über kein Azure AD-Konto oder MSA verfügt, wird er aufgefordert, ein MSA zu erstellen.
4.  Dem Eingeladenen wird der**Bildschirm zum Überprüfen** der Berechtigungen angezeigt. Hier kann er die Datenschutzbestimmungen der einladenden Organisation prüfen und die Bedingungen akzeptieren.

## <a name="privacy-policy-agreement"></a>Zustimmung zur Datenschutzrichtlinie

Wenn sich ein Gastbenutzer zum ersten Mal anmeldet, um auf Ressourcen in einer Partnerorganisation zuzugreifen, wird ein**Bildschirm zum Überprüfen**der Berechtigungen angezeigt. Hier kann der Benutzer die Datenschutzbestimmungen der einladenden Organisation prüfen. Der Benutzer muss der Nutzung seiner Informationen gemäß den Datenschutzrichtlinien der Organisation zustimmen, um fortfahren zu können.

![Screenshot mit Benutzereinstellungen im Zugriffsbereich](media/redemption-experience/ConsentScreen.png) 

Informationen dazu, wie Sie als Mandantenadministrator einen Link zu den Datenschutzbestimmungen Ihrer Organisation einrichten, finden Sie unter [Vorgehensweise: Hinzufügen der Datenschutzinformationen mit Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Nutzungsbedingungen

Mithilfe des Nutzungsbedingungsfeatures von Azure AD können Sie dem Gastbenutzer im Rahmen der erstmaligen Einlösung Nutzungsbedingungen anzeigen. Dieses Feature befindet sich in Azure Active Directory unter **Verwalten** > **Organisationsbeziehungen** > **Nutzungsbedingungen** oder unter **Sicherheit** > **Bedingter Zugriff** > **Nutzungsbedingungen**. Ausführliche Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure Active Directory](../conditional-access/terms-of-use.md).

![Screenshot mit neuen Nutzungsbedingungen](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Add Azure Active Directory B2B collaboration users in the Azure portal](add-users-administrator.md) (Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal)
- [How do information workers add B2B collaboration users to Azure Active Directory?](add-users-information-worker.md) (Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer zu Azure Active Directory hinzu?)
- [Azure Active Directory B2B collaboration API and customization](customize-invitation-api.md#powershell) (Azure Active Directory B2B-Zusammenarbeit: API und Anpassung)
- [Leave an organization as a guest user](leave-the-organization.md) (Verlassen einer Organisation als Gastbenutzer)
