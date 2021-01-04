---
title: 'Schnellstart: Löschen einer Anwendung von Ihrem Azure AD-Mandanten (Azure Active Directory)'
description: In dieser Schnellstartanleitung wird das Azure-Portal verwendet, um eine Anwendung von Ihrem Azure AD-Mandanten (Azure Active Directory) zu löschen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 677c9c0123c174fdf2cf8e94f9c8110656d21306
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649397"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Schnellstart: Löschen einer Anwendung von Ihrem Azure AD-Mandanten (Azure Active Directory)

In dieser Schnellstartanleitung wird das Azure-Portal verwendet, um eine Anwendung zu löschen, die Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten) hinzugefügt wurde.

## <a name="prerequisites"></a>Voraussetzungen

Zum Löschen einer Anwendung von Ihrem Azure AD-Mandanten benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Optional: Gehen Sie den Schnellstart [Anzeigen Ihrer Apps](view-applications-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Hinzufügen einer App](add-application-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Konfigurieren einer App](add-application-portal-configure.md) durch.
- Optional: Arbeiten Sie den Artikel [Zuweisen von Benutzern zu einer App](add-application-portal-assign-users.md) durch.
- Optional: Gehen Sie den Schnellstart [Einrichten des einmaligen Anmeldens](add-application-portal-setup-sso.md) durch.

>[!IMPORTANT]
>Verwenden Sie zum Testen der in dieser Schnellstartanleitung aufgeführten Schritte keine Produktionsumgebung.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Löschen einer Anwendung von Ihrem Azure AD-Mandanten

Löschen Sie eine Anwendung wie folgt von Ihrem Azure AD-Mandanten:

1. Wählen Sie im Azure AD-Portal die Option **Unternehmensanwendungen** aus. Suchen Sie dann nach der zu löschenden Anwendung, und wählen Sie diese aus. In diesem Fall möchten wir die Anwendung **GitHub_test** löschen, die wir im vorherigen Schnellstart hinzugefügt haben.
1. Wählen Sie im linken Bereich im Abschnitt **Verwalten** die Option **Eigenschaften** aus.
1. Wählen Sie **Löschen** aus, und wählen Sie dann **Ja** aus, um zu bestätigen, dass die App aus Ihrem Azure AD-Mandanten gelöscht werden soll.

> [!TIP]
> Sie können die App-Verwaltung mithilfe der Graph-API automatisieren. Informationen hierzu finden Sie unter [Automatisieren einer SAML-basierten SSO-App-Konfiguration mit Microsoft Graph-API](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Schnellstartserie abgeschlossen haben, sollten Sie die App löschen, um den Testmandanten zu bereinigen. Das Löschen der App wurde in dieser Schnellstartanleitung behandelt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die Schnellstartserie abgeschlossen. Im nächsten Schritt erfahren Sie mehr über Best Practices bei der App-Verwaltung.
> [!div class="nextstepaction"]
> [Bewährte Methoden für die Anwendungsverwaltung](application-management-fundamentals.md)