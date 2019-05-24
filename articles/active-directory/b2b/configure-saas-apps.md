---
title: Konfigurieren von SaaS-Apps für die B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Code- und PowerShell-Beispiele für die Azure Active Directory B2B-Zusammenarbeit
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35dad420aa004e27ec974c494dc66e9b8e13c733
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65811945"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurieren von SaaS-Apps für die B2B-Zusammenarbeit

Die Azure Active Directory (Azure AD) B2B-Zusammenarbeit funktioniert mit den meisten Apps, die sich in Azure AD integrieren lassen. In diesem Abschnitt wird erläutert, wie Sie einige beliebte SaaS-Apps für die Verwendung mit Azure AD B2B konfigurieren.

Bevor wir die App-spezifischen Anweisungen betrachten, finden Sie im Folgenden einige Faustregeln:

* Die Benutzereinrichtung muss bei den meisten Apps manuell erfolgen. Benutzer müssen also auch in der App manuell erstellt werden.

* Für Apps, die eine automatische Einrichtung unterstützen (wie z.B. Dropbox), werden in den Apps separate Einladungen erstellt. Die Benutzer müssen jede Einladung annehmen.

* Legen Sie in den Benutzerattributen für **User Identifier** (Benutzerbezeichner) immer **user.mail** fest, um mögliche Probleme mit fehlerhaften Benutzerprofil-Datenträgern (UPD) bei Gastbenutzern zu minimieren.


## <a name="dropbox-business"></a>Dropbox Business

Damit sich die Benutzer mit ihrem Organisationskonto anmelden können, müssen Sie Dropbox Business so konfigurieren, dass es Azure AD als Security Assertion Markup Language (SAML)-Identitätsanbieter verwendet. Wenn Dropbox Business dafür nicht konfiguriert wurde, kann der Benutzer nicht zum Anmelden über Azure AD aufgefordert oder die Anmeldung mithilfe von Azure AD anderweitig ermöglicht werden.

1. Wählen Sie im linken Bereich **Unternehmensanwendungen**, und klicken Sie auf **Hinzufügen**, um die Dropbox Business-App zu Azure AD hinzuzufügen.

   ![Schaltfläche „Hinzufügen“ auf der Seite „Unternehmensanwendungen“](media/configure-saas-apps/add-dropbox.png)

2. Geben Sie im Fenster **Eine Anwendung hinzufügen** im Suchfeld den Text **dropbox** ein, und wählen Sie dann in der Ergebnisliste **Dropbox für Unternehmen** aus.

   ![Suchen nach „dropbox“ auf der Seite „Eine Anwendung hinzufügen“](media/configure-saas-apps/add-app-dialog.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden** im linken Bereich **Einmaliges Anmelden** aus, und geben Sie im Feld **User Identifier** (Benutzerbezeichner) den Text **user.mail** ein. (Standardmäßig ist UPN festgelegt.)

   ![Konfigurieren des einmaligen Anmeldens für die App](media/configure-saas-apps/configure-app-sso.png)

4. Wenn Sie das Zertifikat zur Verwendung bei der Dropbox-Konfiguration verwenden möchten, wählen Sie **Configure DropBox** (DropBox konfigurieren). Wählen Sie anschließend in der Liste die Option **SAML Single Sign On Service URL** (Dienst-URL für einmaliges Anmelden für SAML) aus.

   ![Herunterladen des Zertifikats für die Dropbox-Konfiguration](media/configure-saas-apps/download-certificate.png)

5. Melden Sie sich mit der Anmelde-URL von der Seite **Einmaliges Anmelden** bei Dropbox an.

   ![Screenshot der Dropbox-Anmeldeseite](media/configure-saas-apps/sign-in-to-dropbox.png)

6. Wählen Sie im Menü die Option **Admin Console** (Verwaltungskonsole) aus.

   ![Der Link „Admin Console“ (Verwaltungskonsole) im Dropbox-Menü](media/configure-saas-apps/dropbox-menu.png)

7. Wählen Sie im Dialogfeld **Authentication** (Authentifizierung) die Option **More** (Mehr) aus, laden Sie das Zertifikat hoch, und geben Sie anschließend im Feld **Sign in URL** (Anmelde-URL) die URL für einmaliges Anmelden für SAML ein.

   ![Der Link „More“ (Mehr) im reduzierten Dialogfeld „Authentication“ (Authentifizierung)](media/configure-saas-apps/dropbox-auth-01.png)

   ![Die „Sign in URL“ (Anmelde-URL) im erweiterten Dialogfeld „Authentication“ (Authentifizierung)](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Wählen Sie im linken Bereich **Bereitstellung** aus. Wählen Sie dann im Feld **Bereitstellungsmodus** die Option **Automatisch** aus, und klicken Sie anschließend auf **Autorisieren**, um die automatische Benutzereinrichtung im Azure-Portal zu konfigurieren.

   ![Konfigurieren der automatischen Benutzerbereitstellung im Azure-Portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Wenn Gastbenutzer oder Mitglieder in der Dropbox-App eingerichtet wurden, erhalten diese eine separate Einladung von Dropbox. Sie müssen auf einen Link klicken und die Einladung annehmen, damit sie das einmalige Anmelden für Dropbox verwenden können.

## <a name="box"></a>Box
Sie können Benutzern die Authentifizierung von Box-Gastbenutzern mit ihrem Azure AD-Konto ermöglichen, indem Sie einen Verbund nutzen, der auf dem SAML-Protokoll basiert. Bei diesem Verfahren werden Metadaten auf Box.com hochgeladen.

1. Fügen Sie die Box-App aus den Unternehmens-Apps hinzu.

2. Konfigurieren Sie einmaliges Anmelden in der folgenden Reihenfolge:

   ![Screenshot mit den Konfigurationseinstellungen für einmaliges Anmelden](media/configure-saas-apps/configure-box-sso.png)

   a. Vergewissern Sie sich zunächst im Feld **Anmelde-URL**, dass die Anmelde-URL für Box im Azure-Portal richtig eingerichtet ist. Diese URL entspricht der URL Ihres Box.com-Mandanten. Hierfür sollte die Benennungskonvention *https://.box.com* eingehalten werden.  
   Der **Bezeichner** gilt nicht für diese App, er erscheint aber nach wie vor als ein Pflichtfeld.

   b. Geben Sie im Feld **User identifier** (Benutzerbezeichner) den Text **user.mail** ein (für SSO-Gastkonten).

   c. Klicken Sie unter **SAML-Signaturzertifikat** auf **Neues Zertifikat erstellen**.

   d. Laden Sie die Metadatendatei herunter, und speichern Sie sie auf Ihrem lokalen Laufwerk, um Ihren Box.com-Mandanten so zu konfigurieren, dass er Azure AD als Identitätsanbieter verwendet werden kann.

   e. Leiten Sie die Metadatendatei an das Supportteam von Box weiter, das das einmalige Anmelden für Sie konfiguriert.

3. Wählen Sie für die automatische Azure AD-Benutzereinrichtung im linken Bereich **Bereitstellung** und anschließend **Autorisieren**.

   ![Autorisieren von Azure AD zum Herstellen einer Verbindung mit Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Wie auch bei Dropbox müssen von Box Eingeladene ihre Einladung von der Box-Anwendung einlösen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Dynamische Gruppen und B2B-Zusammenarbeit](use-dynamic-groups.md)
- [Zuordnen von Benutzeransprüchen für die B2B-Zusammenarbeit](claims-mapping.md)
- [Externe Office 365-Freigaben](o365-external-user.md)

