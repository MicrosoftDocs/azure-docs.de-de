---
title: Anzeigen von Apps mithilfe Ihres Azure Active Directory-Mandanten für die Identitätsverwaltung
description: Hier erfahren Sie, wie Sie alle Anwendungen mithilfe Ihres Azure Active Directory-Mandanten für die Identitätsverwaltung anzeigen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 49dd8b65db771a182138f13218adda2f0e7f7396
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858228"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Anzeigen von Apps mithilfe Ihres Azure AD-Mandanten für die Identitätsverwaltung
Die [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md) enthält grundlegende Informationen. Darin erfahren Sie, wie Sie alle Apps anzeigen, die Ihren Azure AD-Mandanten für die Identitätsverwaltung verwenden. In diesem Artikel wird etwas ausführlicher auf die angezeigten App-Typen eingegangen.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Warum wird eine bestimmte Anwendung in der Liste mit all meinen Anwendungen angezeigt?
Bei einer Filterung nach **Alle Anwendungen** zeigt die **Liste** **Alle Anwendungen** jedes Dienstprinzipalobjekt in Ihrem Mandanten an. Dienstprinzipalobjekte können dieser Liste auf unterschiedliche Arten hinzugefügt werden:
- Beim Hinzufügen einer beliebigen Anwendung aus dem Anwendungskatalog, wie z.B.:
   - **Azure AD – Unternehmensanwendungen**: Apps, die Ihrem Mandanten mithilfe der Option **Unternehmensanwendungen** im Azure AD-Portal hinzugefügt werden. In der Regel handelt es sich dabei um Apps, die mit dem SAML-Standard integriert werden.
   - **Azure AD – App-Registrierungen**: Apps, die Ihrem Mandanten mithilfe der Option **App-Registrierungen** im Azure AD-Portal hinzugefügt werden. In der Regel sind dies benutzerdefinierte Apps, die den Open ID Connect- und den OAuth-Standard verwenden.
   - **Anwendungen für den Anwendungsproxy**: Anwendungen, die in Ihrer lokalen Umgebung ausgeführt werden und für die Sie das sichere einmalige Anmelden von externen Standorten bereitstellen möchten.
- Beim Registrieren für eine oder beim Anmelden bei einer Drittanbieteranwendung, die in Azure Active Directory integriert ist. Beispiele hierfür sind [Smartsheet](https://app.smartsheet.com/b/home) oder [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Microsoft-Apps, etwa Microsoft 365.
- Beim Hinzufügen einer neuen Anwendungsregistrierung durch Erstellen einer benutzerdefiniert entwickelten Anwendung über die [Anwendungsregistrierung](../develop/quickstart-register-app.md).
- Beim Hinzufügen einer neuen Anwendungsregistrierung durch Erstellen einer benutzerdefiniert entwickelten Anwendung über das [Anwendungsregistrierungsportal v2.0](../develop/quickstart-register-app.md).
- Beim Hinzufügen einer Anwendung, die Sie mithilfe von [ASP.NET-Authentifizierungsmethoden](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) oder [verbundenen Diensten](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) von Visual Studio entwickeln
- Beim Erstellen eines Dienstprinzipalobjekts mithilfe des [Azure AD-PowerShell-Moduls](/powershell/azure/active-directory/install-adv2).
- Beim [Zustimmen zu einer Anwendung](../develop/howto-convert-app-to-be-multi-tenant.md) durch einen Administrator, damit in Ihrem Mandanten Daten verwendet werden können.
- Beim [Zustimmen zu einer Anwendung durch einen Benutzer](../develop/howto-convert-app-to-be-multi-tenant.md), damit in Ihrem Mandanten Daten verwendet werden können.
- Beim Aktivieren bestimmter Dienste, die Daten in Ihrem Mandanten speichern. Ein Beispiel hierfür ist die Kennwortzurücksetzung, die als Dienstprinzipal modelliert ist, um Ihre Richtlinie für das Zurücksetzen von Kennwörtern sicher zu speichern.

Unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](../develop/active-directory-how-applications-are-added.md) erhalten Sie Informationen dazu, wie und warum Apps zu Ihrem Verzeichnis hinzugefügt werden.

## <a name="next-steps"></a>Nächste Schritte
[Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
