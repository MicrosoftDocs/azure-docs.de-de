---
title: Was ist die Azure Active Directory B2B-Zusammenarbeit? – Azure Active Directory | Microsoft-Dokumentation
description: Die Azure Active Directory B2B-Zusammenarbeit unterstützt einen Gastbenutzerzugriff, um auf sichere Weise Ressourcen freizugeben und mit externen Partnern zusammenzuarbeiten.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 09/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ab73b501fc9dbe6209a92fff6f6e19de03fcdc1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65768078"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Was ist der Gastzugriff in Azure Active Directory-B2B?

Mit der Azure Active Directory-B2B-Zusammenarbeit (Business-to-Business) können Sie die Anwendungen und Dienste Ihres Unternehmens für Gastbenutzer von beliebigen Organisationen sicher freigeben und dabei die Kontrolle über Ihre eigenen Unternehmensdaten behalten. Arbeiten Sie sicher und geschützt mit externen Partnern zusammen, ob groß oder klein, auch wenn sie keine Azure AD oder eine IT-Abteilung haben. Ein einfacher Prozess zum Senden und Einlösen von Einladungen ermöglicht es Partnern, ihre eigenen Zugangsdaten für den Zugriff auf die Ressourcen Ihres Unternehmens zu verwenden. Entwickler können Azure AD Business-to-Business-APIs verwenden, um den Einladungsprozess anzupassen oder Anwendungen wie Self-Service-Anmeldeportale zu schreiben.

Sehen Sie sich das Video an und erfahren Sie, wie Sie sicher mit Gastbenutzern zusammenarbeiten können, indem Sie sie einladen, sich mit ihren eigenen Identitäten bei den Anwendungen und Diensten Ihres Unternehmens anzumelden.

Im folgenden Video wird eine nützliche Übersicht gezeigt.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="collaborate-with-any-partner-using-their-identities"></a>Zusammenarbeit mit jedem Partner über seine Identitäten
Mit Azure AD-B2B verwendet der Partner seine eigene Identitätsverwaltungslösung, sodass es keinen externen Verwaltungsaufwand für Ihr Unternehmen gibt. 
- Der Partner verwendet seine eigenen Identitäten und Anmeldeinformationen; Azure AD ist nicht erforderlich. 
- Sie müssen keine externen Konten oder Kennwörter verwalten. 
- Sie müssen keine Konten synchronisieren oder Kontolebenszyklen verwalten.  

![Screenshot: Seite „Mitglieder hinzufügen“](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Einladen von Gastbenutzern mit einem einfachen Prozess zum Senden und Einlösen von Einladungen
Gastbenutzer können sich mit ihrem Geschäfts-, Schul- oder Unikonto bzw. mit ihrer Identität bei Ihren Anwendungen und Diensten anmelden. Falls der Gastbenutzer über kein Microsoft- oder Azure AD-Konto verfügt, wird zum Zeitpunkt der Einlösung ein Konto für ihn erstellt. 
- Laden Sie Gastbenutzer mit der E-Mail-Adresse ihrer Wahl ein.
- Senden Sie einen direkten Link zu einer Anwendung oder eine Einladung an den eigenen Zugriffsbereich des Gastbenutzers. 
- Gastbenutzer können die Einladung in wenigen Schritten einlösen, um sich anzumelden.

![Screenshot: Seite „Berechtigungen überprüfen“](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Verwenden von Richtlinien zum sicheren Freigeben von Anwendungen und Diensten
Sie können Autorisierungsrichtlinien verwenden, um Ihre Unternehmensinhalte zu schützen. Richtlinien für bedingten Zugriff (beispielsweise Multi-Factor Authentication) können erzwungen werden:
- Auf Mandantenebene
- Auf Anwendungsebene
- Für bestimmte Gastbenutzer, um Anwendungen und Daten des Unternehmens zu schützen

![Screenshot: Option „Bedingter Zugriff“](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Einfaches Hinzufügen von Gastbenutzern im Azure AD-Portal

Als Administrator können Sie im Azure-Portal problemlos Gastbenutzer zu Ihrer Organisation hinzufügen.
- Erstellen Sie einen neuen Gastbenutzer in Azure AD auf ähnliche Weise, wie Sie einen neuen Benutzer hinzufügen würden.
- Der Gastbenutzer erhält sofort eine anpassbare Einladung, mit der er sich in seinem Zugangsbereich anmelden kann.
- Gastbenutzer im Verzeichnis können Anwendungen oder Gruppen zugeordnet werden.  

![Screenshot: Seite zur Eingabe einer Einladung an einen neuen Gastbenutzer](media/what-is-b2b/adding-b2b-users-admin.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Anwendungs- und Gruppenbesitzer können ihre eigenen Gastbenutzer verwalten

Sie können die Gastbenutzerverwaltung an Anwendungsbesitzer delegieren, sodass sie Gastbenutzer direkt zu jeder Anwendung hinzufügen können, die sie freigeben möchten, unabhängig davon, ob es sich um eine Microsoft-Anwendung handelt oder nicht. 
 - Administratoren richten die Self-Service-Anwendungs- und Gruppenverwaltung ein.
 - Benutzer, die keine Administratoren sind, verwenden ihren [Zugriffsbereich](https://myapps.microsoft.com), um Gastbenutzer zu Anwendungen oder Gruppen hinzuzufügen.

![Screenshot: Zugriffsbereich für einen Gastbenutzer](media/what-is-b2b/access-panel-manage-app.png)

## <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Mit APIs und einem Beispielcode können Sie einfach Anwendungen erstellen, die integriert werden sollen

Integrieren Sie Ihre externen Partner entsprechend den Anforderungen Ihrer Organisation.
- Mithilfe der [Einladungs-APIs der B2B-Zusammenarbeit](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) können Sie Ihre Onboardingprozesse anpassen und unter anderem Self-Service-Registrierungsportale erstellen. 
- Verwenden Sie den von uns bereitgestellten Beispielcode für ein Self-Service-Portal auf [GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![Screenshot: Beispiel für Anmeldeportal](media/what-is-b2b/sign-up-portal.png)

## <a name="next-steps"></a>Nächste Schritte

- [Lizenzierungsanleitung für Azure AD-B2B-Zusammenarbeit](licensing-guidance.md)
- [Hinzufügen von Gastbenutzern für B2B-Zusammenarbeit im Portal](add-users-administrator.md)
- [Verstehen, wie Einladungen eingelöst werden](redemption-experience.md)
- Und wie immer empfehlen wir, sich über unsere [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) mit dem Produktteam in Verbindung zu setzen, um Feedback zu geben, Fragen zu erörtern und Vorschläge zu unterbreiten.
