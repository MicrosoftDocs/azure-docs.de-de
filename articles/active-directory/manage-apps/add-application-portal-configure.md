---
title: 'Schnellstart: Konfigurieren von Eigenschaften für eine Anwendung auf Ihrem Azure AD-Mandanten (Azure Active Directory)'
description: In dieser Schnellstartanleitung wird das Azure-Portal verwendet, um eine Anwendung zu konfigurieren, die für Ihren Azure AD-Mandanten (Azure Active Directory) registriert wurde.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 18e40d0e62a69954b0e3c1e5770a71c8b0bcba8b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656682"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Schnellstart: Konfigurieren von Eigenschaften für eine Anwendung auf Ihrem Azure AD-Mandanten (Azure Active Directory)

In der vorherigen Schnellstartanleitung haben Sie Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten) eine Anwendung hinzugefügt. Beim Hinzufügen einer Anwendung teilen Sie Ihrem Azure AD-Mandanten mit, dass er als Identitätsanbieter für die App fungiert. Nun konfigurieren Sie einige Eigenschaften für die App.
 
## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um die Eigenschaften einer Anwendung auf Ihrem Azure AD-Mandanten zu konfigurieren:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Optional: Gehen Sie den Schnellstart [Anzeigen Ihrer Apps](view-applications-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Hinzufügen einer App](add-application-portal.md) durch.

>[!IMPORTANT]
>Verwenden Sie zum Testen der in dieser Schnellstartanleitung aufgeführten Schritte keine Produktionsumgebung.

## <a name="configure-app-properties"></a>Konfigurieren von App-Eigenschaften

Nachdem Sie Ihrem Azure AD-Mandanten eine Anwendung hinzugefügt haben, wird die Übersichtsseite angezeigt. Wenn Sie eine Anwendung konfigurieren, die bereits hinzugefügt wurde, lesen Sie die erste Schnellstartanleitung. Darin wird beschrieben, wie Sie die Ihrem Mandanten hinzugefügten Anwendungen anzeigen können. 

So bearbeiten Sie die Anwendungseigenschaften:

1. Wählen Sie im Azure AD-Portal die Option **Unternehmensanwendungen** aus. Suchen Sie dann nach der zu konfigurierenden Anwendung, und wählen Sie diese aus.
2. Wählen Sie im Abschnitt **Verwalten** die Option **Eigenschaften** aus, um den Bereich **Eigenschaften** zur Bearbeitung zu öffnen.
3. Nehmen Sie sich einen Moment Zeit, um sich mit den zur Verfügung stehenden Optionen vertraut zu machen. Welche Optionen verfügbar sind, hängt davon ab, wie die App in Azure AD integriert ist. Eine App, für die SAML-basiertes einmaliges Anmelden verwendet wird, verfügt beispielsweise über Felder wie *URL für den Benutzerzugriff*. Bei einer App, für die OIDC-basiertes einmaliges Anmelden verwendet wird, ist dies dagegen nicht der Fall. Beachten Sie auch, dass es sich bei Apps, die über **Azure Active Directory > App-Registrierungen** hinzugefügt werden, standardmäßig um OIDC-basierte Apps handelt. Bei Apps, die über **Azure Active Directory > Unternehmensanwendungen** hinzugefügt werden, kann dagegen eine beliebige Anzahl von Standards für einmaliges Anmelden genutzt werden. Alle Apps verfügen über Felder zum Konfigurieren, wann eine App angezeigt wird und verwendet werden kann. Die Felder lauten:
    - Mit **Aktiviert für die Benutzeranmeldung?** wird festgelegt, ob sich Benutzer, die der Anwendung zugewiesen sind, anmelden können.
    - Mit **Benutzerzuweisung erforderlich?** wird festgelegt, ob sich Benutzer, die der Anwendung nicht zugewiesen sind, anmelden können.
    - Mit **Für Benutzer sichtbar?** wird festgelegt, ob den einer App zugewiesenen Benutzern die Anwendung unter [Meine Apps](https://myapps.microsoft.com) und im Microsoft 365-App-Startfeld angezeigt wird. (Siehe das Waffelmenü in der oberen linken Ecke einer Microsoft 365-Website.)
    
    > [!TIP]
    > Benutzer werden im Navigationsbereich **Benutzer und Gruppen** zugewiesen.

    Die drei Optionen können unabhängig voneinander aktiviert/deaktiviert werden, und das resultierende Verhalten ist nicht immer offensichtlich. Die folgende Tabelle ist ggf. hilfreich:
    
    | Aktiviert für die Benutzeranmeldung? | Benutzerzuweisung erforderlich? | Für Benutzer sichtbar? | Verhalten für Benutzer, die der App zugewiesen bzw. nicht zugewiesen wurden |
    |---|---|---|---|
    | Ja | Ja | Ja | Zugewiesenen Benutzern wird die App angezeigt, und sie können sich anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden. |
    | Ja | Ja | Nein  | Zugewiesenen Benutzern wird die App nicht angezeigt, aber sie können sich anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden. |
    | Ja | Nein  | Ja | Zugewiesenen Benutzern wird die App angezeigt, und sie können sich anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, aber sie können sich anmelden. |
    | Ja | Nein  | Nein  | Zugewiesenen Benutzern wird die App nicht angezeigt, aber sie können sich anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, aber sie können sich anmelden. |
    | Nein  | Ja | Ja | Zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden. |
    | Nein  | Ja | Nein  | Zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden. |
    | Nein  | Nein  | Ja | Zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden. |
    | Nein  | Nein  | Nein  | Zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden.<br>Nicht zugewiesenen Benutzern wird die App nicht angezeigt, und sie können sich nicht anmelden. |

4. Wählen Sie abschließend **Speichern** aus.

## <a name="use-a-custom-logo"></a>Verwenden eines benutzerdefinierten Logos

So verwenden Sie ein benutzerdefiniertes Logo:

1. Erstellen Sie ein Logo mit 215 x 215 Pixeln, und speichern Sie es im PNG-Format.
2. Wählen Sie im Azure AD-Portal die Option **Unternehmensanwendungen** aus. Suchen Sie dann nach der zu konfigurierenden Anwendung, und wählen Sie diese aus.
3. Wählen Sie im Abschnitt **Verwalten** die Option **Eigenschaften** aus, um den Bereich **Eigenschaften** zur Bearbeitung zu öffnen. 
4. Wählen Sie das Symbol zum Hochladen des Logos aus.
5. Wählen Sie abschließend **Speichern** aus.

    ![Screenshot des Bildschirms „Eigenschaften“, in dem zu sehen ist, wie das Logo geändert wird](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Die in diesem Bereich **Eigenschaften** angezeigte Miniaturansicht wird nicht sofort aktualisiert. Sie können den Bereich **Eigenschaften** schließen und erneut öffnen, um das aktualisierte Symbol anzuzeigen.


> [!TIP]
> Sie können die App-Verwaltung mithilfe der Graph-API automatisieren. Informationen hierzu finden Sie unter [Automatisieren einer SAML-basierten SSO-App-Konfiguration mit Microsoft Graph-API](/graph/application-saml-sso-configure-api).

## <a name="add-notes"></a>Hinzufügen von Notizen

Sie können das Notizfeld verwenden, um alle für die Verwaltung der Anwendung in Azure AD relevanten Informationen hinzuzufügen. „Notizen“ ist ein Feld für freien Text mit einer maximalen Größe von 1024 Zeichen.

1. Wählen Sie im Azure AD-Portal die Option **Unternehmensanwendungen** aus. Suchen Sie dann nach der zu konfigurierenden Anwendung, und wählen Sie diese aus.
2. Wählen Sie im Abschnitt **Verwalten** die Option **Eigenschaften** aus, um den Bereich **Eigenschaften** zur Bearbeitung zu öffnen.
3. Aktualisieren Sie das Notizfeld, und wählen Sie **Speichern** aus.

    ![Screenshot des Bildschirms „Eigenschaften“, in dem zu sehen ist, wie die Notizen geändert werden](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Schnellstartreihe nicht fortsetzen möchten, sollten Sie die App löschen, um den Testmandanten zu bereinigen. Das Löschen der App wird in der letzten Schnellstartanleitung dieser Reihe behandelt. Informationen finden Sie unter [Schnellstart: Löschen einer Anwendung von Ihrem Azure AD-Mandanten (Azure Active Directory)](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie der App Benutzer zuweisen:
> [!div class="nextstepaction"]
> [Zuweisen von Benutzern zu einer App](add-application-portal-assign-users.md)