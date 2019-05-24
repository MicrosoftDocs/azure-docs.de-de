---
title: Konfigurieren der Benutzereinwilligung für eine Anwendung – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Art und Weise verwalten, in der Benutzer Anwendungsberechtigungen zustimmen. Sie können die Benutzerfreundlichkeit verbessern, indem Sie Administratoreinwilligung erteilen. Diese Methoden gelten für alle Endbenutzer in Ihrem Azure AD-Mandanten.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd84b44c89c856036640757b7760d7c10bd36173
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824845"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Konfigurieren der Art der Benutzereinwilligung für eine Anwendung in Azure Active Directory
Erfahren Sie, wie Sie die Art und Weise konfigurieren, in der Benutzer Anwendungsberechtigungen zustimmen. Sie können die Benutzerfreundlichkeit verbessern, indem Sie Administratoreinwilligung erteilen. Dieser Artikel erläutert die verschiedenen Möglichkeiten für die Konfiguration der Benutzereinwilligung. Diese Methoden gelten für alle Endbenutzer in Ihrem Azure AD-Mandanten. 

Weitere Informationen zur Einwilligung für Anwendungen finden Sie unter [Azure Active Directory-Zustimmungsframework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Voraussetzungen

Um eine Administratoreinwilligung zu erteilen, müssen Sie sich als globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator anmelden.

Um den Zugriff auf Anwendungen zu beschränken, benötigen Sie die Benutzerzuweisung, und weisen Sie dann Benutzer oder Gruppen der Anwendung zu.  Weitere Informationen finden Sie unter [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Erteilen der Administratoreinwilligung für Unternehmens-Apps im Azure-Portal

So erteilen Sie Administratoreinwilligung für eine Unternehmens-App:

1. Melden Sie sich globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie oben im Navigationsmenü auf der linken Seite auf **Alle Dienste**. Die **Azure Active Directory-Erweiterung** wird geöffnet.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4. Klicken Sie im Navigationsmenü auf **Unternehmensanwendungen**.
5. Klicken Sie auf **Administratoreinwilligung erteilen**. Sie werden aufgefordert, sich anzumelden, um die Anwendung zu verwalten.
6. Melden Sie sich mit einem Konto an, das über die Berechtigung verfügt, die Administratoreinwilligung für die Anwendung zu erteilen. 
7. Stimmen Sie den Anwendungsberechtigungen zu.

Diese Option funktioniert nur, wenn für die Anwendung Folgendes gilt: 

- Sie ist im Mandanten registriert, oder
- sie ist ein einem anderen Azure AD-Mandanten registriert und mindestens ein Endbenutzer hat seine Einwilligung erteilt. Sobald ein Endbenutzer seine Einwilligung für eine Anwendung erteilt hat, führt Azure AD die Anwendung im Azure-Portal unter **Unternehmens-Apps** auf.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Erteilen der Administratoreinwilligung beim Registrieren einer App im Azure-Portal

So erteilen Sie die Administratoreinwilligung beim Registrieren einer App: 

1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Blatt **App-Registrierungen**.
3. Wählen Sie die Anwendung aus, für die eine Einwilligung erteilt werden soll.
4. Wählen Sie **Erforderliche Berechtigungen** aus.
5. Klicken Sie oben auf dem Blatt auf **Berechtigungen erteilen**.


## <a name="grant-admin-consent-through-a-url-request"></a>Erteilen der Administratoreinwilligung über eine URL-Anforderung

So erteilen Sie die Administratoreinwilligung über eine URL-Anforderung:

1. Erstellen Sie eine Anforderung an *login.microsoftonline.com* mit Ihren App-Konfigurationen, und fügen Sie diese an `&prompt=admin_consent` an. 
2. Nach der Anmeldung als Administrator sollte die App-Zustimmung für alle Benutzer erteilt sein.


## <a name="force-user-consent-through-a-url-request"></a>Erzwingen der Benutzereinwilligung über eine URL-Anforderung

Um zu erzwingen, dass Endbenutzer bei jeder Authentifizierung ihre Einwilligung für eine Anwendung erteilen, fügen Sie `&prompt=consent` an die URL der Authentifizierungsanforderung an.

## <a name="next-steps"></a>Nächste Schritte

[Genehmigen und Integrieren von Apps in Azure AD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Genehmigen und Zuweisen von Berechtigungen für konvergierte Azure AD V2.0-Apps](../develop/active-directory-v2-scopes.md)

[Azure AD bei Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
