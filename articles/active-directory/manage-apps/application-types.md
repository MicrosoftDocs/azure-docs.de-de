---
title: Unerwartete Anwendung in der Liste meiner Anwendungen | Microsoft-Dokumentation
description: Anzeigen aller Anwendungen in Ihrem Mandanten und Informationen dazu, wie Anwendungen in der Liste aller Anwendungen unter „Unternehmensanwendungen“ angezeigt werden.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cb4eeb52d0680695bda266ad1a563b2ef5ee02
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65781094"
---
# <a name="unexpected-application-in-my-applications-list"></a>Unerwartete Anwendung in der Liste meiner Anwendungen

Dieser Artikel bietet Informationen dazu, wie Anwendungen in der Liste **Alle Anwendungen** unter **Unternehmensanwendungen** angezeigt werden. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Anzeigen aller Anwendungen im Mandanten

Um alle Anwendungen in Ihrem Mandanten anzuzeigen, müssen Sie das **Filter**-Steuerelement der Liste **Alle Anwendungen** auf **Alle Anwendungen** festlegen. Folgen Sie diesen Schritten:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

6.  Klicken Sie oberhalb der Liste **Alle Anwendungen** auf das Steuerelement **Filter**.

7.  Legen Sie im Bereich **Filter** die Option **Anzeigen** auf **Alle Anwendungen** fest.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Warum wird eine bestimmte Anwendung in der Liste mit all meinen Anwendungen angezeigt?

Bei einer Filterung nach **Alle Anwendungen** zeigt die **Liste** **Alle Anwendungen** jedes Dienstprinzipalobjekt in Ihrem Mandanten an. Dienstprinzipalobjekte können dieser Liste auf unterschiedliche Arten hinzugefügt werden:

1. Beim Hinzufügen einer beliebigen Anwendung aus dem Anwendungskatalog, wie z.B.:

   1. **Azure AD-Kataloganwendungen**: Anwendungen, die für das einmalige Anmelden mit Azure AD vorab integriert wurden.

   2. **Anwendungen für den Anwendungsproxy**: Anwendungen, die in Ihrer lokalen Umgebung ausgeführt werden und für die Sie das sichere einmalige Anmelden von externen Standorten bereitstellen möchten.

   3. **Benutzerdefiniert entwickelte Anwendungen**: Anwendungen, die Ihre Organisation auf der Azure AD-Anwendungsentwicklungsplattform entwickeln möchte, die aber möglicherweise noch nicht vorhanden sind.

   4. **Nicht im Katalog vorhandene Anwendungen**: Nutzen Sie Ihre eigenen Anwendungen (BYOD, Bring Your Own Applications). Hierbei kann es sich um beliebige Weblinks oder Anwendungen handeln, die Felder für Benutzername und Kennwort generieren, SAML oder OpenID Connect-Protokolle oder SCIM unterstützen, und die Sie für das einmalige Anmelden in Azure AD integrieren möchten.

2. Beim Registrieren für eine oder Anmelden bei einer Drittanbieteranwendung, die in Azure Active Directory integriert ist.<sup></sup> Beispiele hierfür sind [Smartsheet](https://app.smartsheet.com/b/home) oder [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Beim Registrieren für eine Erstanbieteranwendung wie z.B. [Microsoft Office 365](https://products.office.com/) oder beim Hinzufügen einer Lizenz für eine solche Anwendung zu einem Benutzer oder einer Gruppe.

4. Beim Hinzufügen einer neuen Anwendungsregistrierung durch Erstellen einer benutzerdefiniert entwickelten Anwendung über die [Anwendungsregistrierung](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration).

5. Beim Hinzufügen einer neuen Anwendungsregistrierung durch Erstellen einer benutzerdefiniert entwickelten Anwendung über das [Anwendungsregistrierungsportal v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration).

6. Beim Hinzufügen einer Anwendung, die Sie mithilfe von [ASP.NET-Authentifizierungsmethoden](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) oder [verbundenen Diensten](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) von Visual Studio entwickeln.

7. Beim Erstellen eines Dienstprinzipalobjekts mithilfe des [Azure AD-PowerShell-Moduls](/powershell/azure/install-adv2?view=azureadps-2.0).

8. Beim [Zustimmen zu einer Anwendung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) durch einen Administrator, damit in Ihrem Mandanten Daten verwendet werden können.

9. Beim [Zustimmen zu einer Anwendung durch einen Benutzer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview), damit in Ihrem Mandanten Daten verwendet werden können.

10. Beim Aktivieren bestimmter Dienste, die Daten in Ihrem Mandanten speichern. Ein Beispiel hierfür ist die Kennwortzurücksetzung, die als Dienstprinzipal modelliert ist, um Ihre Richtlinie für das Zurücksetzen von Kennwörtern sicher zu speichern.

Weitere Informationen zu der Art und Weise, in der Apps zu Ihrem Verzeichnis hinzugefügt werden, erhalten Sie unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ich möchte die Zuweisung eines bestimmten Benutzers oder einer bestimmten Gruppe zu einer Anwendung entfernen

Um eine Benutzer- oder Gruppenzuweisung zu einer Anwendung zu entfernen, führen Sie die im Artikel [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) beschriebenen Schritte aus.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ich möchte den gesamten Zugriff auf eine Anwendung für alle Benutzer deaktivieren

Um alle Benutzeranmeldungen bei einer Anwendung zu deaktivieren, führen Sie die im Artikel [Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) beschriebenen Schritte aus.

## <a name="i-want-to-delete-an-application-entirely"></a>Ich möchte eine Anwendung vollständig löschen.

Um eine **Anwendung zu löschen**, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, die Sie löschen möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im Bereich **Übersicht** der Anwendung auf das Symbol **Löschen**.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ich möchte alle zukünftigen Vorgänge für die Benutzerzustimmung für jede Anwendung deaktivieren.

Die Deaktivierung der Benutzerzustimmung für das gesamte Verzeichnis führt dazu, dass Endbenutzer keiner Anwendung zustimmen können. Administratoren können weiterhin im Namen von Benutzern zustimmen. Weitere Informationen zur Zustimmung zu Anwendungen sowie zum Aktivieren bzw. Deaktivieren der Zustimmung finden Sie unter [Grundlegendes zur Benutzer- und Administratorzustimmung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Führen Sie die folgenden Schritte aus, um **alle zukünftigen Vorgänge der Benutzerzustimmung in Ihrem gesamten Verzeichnis zu deaktivieren**:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Benutzereinstellungen**.

6.  Deaktivieren Sie alle zukünftigen Vorgänge der Benutzerzustimmung, indem Sie **Benutzer können Apps Zugriff auf ihre Daten gewähren** auf **Nein** festlegen und dann auf die Schaltfläche **Speichern** klicken.

## <a name="next-steps"></a>Nächste Schritte
[Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
