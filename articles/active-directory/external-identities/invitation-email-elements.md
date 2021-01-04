---
title: Elemente der B2B-Einladungs-E-Mail – Azure Active Directory | Microsoft-Dokumentation
description: Vorlage für die Einladungs-E-Mail von Azure Active Directory B2B-Zusammenarbeit
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb134a2fb784e02f5e00c9e88ab0df1794489e0c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860591"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Die Elemente der Einladungs-E-Mail für die B2B-Zusammenarbeit – Azure Active Directory

Einladungs-E-Mails sind eine wichtige Komponente zum Onboarding von Partnern als Benutzer von B2B-Zusammenarbeit in Azure AD. Auch wenn es [nicht erforderlich ist, eine E-Mail zu senden, um eine Person mit B2B Collaboration einzuladen](add-user-without-invite.md), erhalten die Benutzer auf diese Weise alle benötigten Informationen, um entscheiden zu können, ob sie Ihre Einladung akzeptieren. Außerdem erhalten sie einen Link, auf den sie in Zukunft zurückgreifen können, wenn sie zu Ihren Ressourcen zurückkehren möchten.

![Screenshot, der die B2B-Einladungs-E-Mail zeigt](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Erläuterungen zur E-Mail

Sehen wir uns einige Elemente der E-Mail an, damit Sie diese Funktionen optimal nutzen können.

### <a name="subject"></a>Subject

Der Betreff der E-Mail folgt diesem Muster:

&lt;Benutzername&gt; hat Sie zum Zugriff auf Anwendungen in seiner Organisation eingeladen.

### <a name="from-address"></a>Absenderadresse

Wir verwenden ein LinkedIn-ähnliches Muster für die Absenderadresse. Dieses Muster sollte deutlich machen, dass die E-Mail zwar von invites@microsoft.com stammt, die Einladung jedoch von einer anderen Organisation. Das Format lautet: Microsoft-Einladungen <invites@microsoft.com> oder Microsoft-Einladungen im Namen von &lt;Mandantenname&gt; <invites@microsoft.com>. 

### <a name="reply-to"></a>Antworten an

Die Antwort-E-Mail-Adresse wird auf die E-Mail-Adresse des einladenden Benutzers festgelegt (falls verfügbar). Wenn eine Antwort auf die E-Mail gesendet wird, gelangt diese zurück an den einladenden Benutzer.

### <a name="phishing-warning"></a>Phishingwarnung

Die E-Mail beginnt mit einer kurzen Warnung für den Benutzer über Phishing und benachrichtigt ihn, dass er nur erwartete Einladungen akzeptieren sollte. Sie sollten sicherstellen, dass die Partner, die Sie einladen, von Ihrer Einladung nicht überrascht werden, indem Sie sie im Vorfeld erwähnen.

![Abbildung der Phishingwarnung in der E-Mail](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>Informationen und Einladungsnachricht des einladenden Benutzers

Die E-Mail enthält den Namen und die primäre Domäne, die der Organisation zugeordnet sind, die Absender der Einladung ist. Diese Informationen sollen der eingeladenen Person eine fundierte Entscheidung ermöglichen, ob sie die Einladung akzeptieren soll. Wenn die einladende Person beim [Einladen eines Gastbenutzers in das Verzeichnis, die Gruppe oder die App](add-users-administrator.md) oder bei [Verwendung der Einladungs-API](customize-invitation-api.md) eine Nachricht einschließt, wird diese im Hauptabschnitt der E-Mail hervorgehoben. Außerdem sind der Name der einladenden Person und ihr Profilbild enthalten, sofern sie eines festgelegt hat. Die Meldung selbst ist ein Textbereich, daher werden aus Sicherheitsgründen keine HTML-Tags verarbeitet.

![Abbildung der Einladungsnachricht in der E-Mail](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>Schaltfläche „Akzeptieren“ und Umleitungs-URL

Der nächste Abschnitt der E-Mail enthält Informationen darüber, wohin die eingeladene Person nach dem Akzeptieren der Einladung geleitet wird, sowie eine Schaltfläche zu diesem Zweck.  In Zukunft kann die eingeladene Person diesen Link immer verwenden, um direkt zu Ihren Ressourcen zurückzukehren.

![Abbildung der Schaltfläche „Akzeptieren“ und der Umleitungs-URL in der E-Mail](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Fußzeilenabschnitt

Die Fußzeile enthält weitere Informationen zu der gesendeten Einladung. Es gibt immer eine Option, mit der die eingeladene Person zukünftige Einladungen blockieren kann. Wenn die Organisation [eine Datenschutzerklärung festgelegt hat](../fundamentals/active-directory-properties-area.md), wird hier der entsprechende Link angezeigt.  Andernfalls zeigt ein Hinweis an, dass die Organisation keine Datenschutzerklärung festgelegt hat.

![Abbildung des Footerabschnitts in der E-Mail](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Blockieren einer Organisation (Abonnement aufheben)

Die Einladung einer Organisation enthält in der Fußzeile eine Option, mit der Sie **zukünftige Einladungen blockieren** können. Ein Gastbenutzer kann diesen Link auswählen, um zukünftige Einladungen von dieser Organisation zu blockieren. Durch diese Aktion wird die Organisation ebenfalls der Liste der Abbestellungen des Benutzers bei [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) hinzugefügt.

### <a name="viewing-organizations-youve-blocked"></a>Anzeigen von Organisationen, die Sie blockiert haben

Sie können als Gastbenutzer die folgenden Schritte ausführen, um die von Ihnen blockierten Unternehmen anzuzeigen oder zu exportieren:

1. Wechseln Sie zu [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage).
2. Geben Sie Ihre E-Mail-Adresse ein, und befolgen Sie die Anmeldeschritte für die Authentifizierung mit Einmalkennung per E-Mail.
3. Zeigen Sie die von Ihnen blockierten Organisationen an, oder exportieren Sie die Namen durch Kopieren und Einfügen.
   > [!NOTE]
   > Wenn Sie ein von Ihnen blockiertes Unternehmen Sie erneut einladen soll, können Sie zuerst die Organisation und dann **Weiter** auswählen.

## <a name="how-the-language-is-determined"></a>So wird die Sprache bestimmt

Die den Gastbenutzern in der Einladungs-E-Mail angezeigte Sprache richtet sich nach den folgenden Einstellungen. Diese Einstellungen sind in der Reihenfolge ihres Auftretens aufgeführt. Wenn eine Einstellung nicht konfiguriert ist, bestimmt die nächste Einstellung in der Liste die Sprache.

- Die **messageLanguage**-Eigenschaft des [invitedUserMessageInfo](/graph/api/resources/invitedusermessageinfo)-Objekts, wenn die API zum Erstellen von Einladungen verwendet wird
-   Die **preferredLanguage**-Eigenschaft, die im [user-Objekt](/graph/api/resources/user) des Gasts angegeben ist
-   Die **Benachrichtigungssprache**, die in den Eigenschaften des Stammmandanten des Gastbenutzers (nur bei Azure AD-Mandanten) festgelegt wurde
-   Die **Benachrichtigungssprache**, die in den Eigenschaften des Ressourcenmandanten festgelegt ist

Wenn keine dieser Einstellungen konfiguriert wurde, wird als Standardsprache „Englisch (USA)“ verwendet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Wie fügen Azure Active Directory-Administratoren B2B-Zusammenarbeitsbenutzer hinzu?](add-users-administrator.md)
- [Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer hinzu?](add-users-information-worker.md)
- [B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md)
- [Hinzufügen von Benutzern der B2B-Zusammenarbeit ohne Einladung](add-user-without-invite.md)
