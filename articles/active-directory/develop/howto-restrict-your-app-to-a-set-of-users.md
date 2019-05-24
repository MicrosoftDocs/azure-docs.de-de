---
title: Beschränken Ihrer für Azure Active Directory registrierten App auf eine Gruppe von Benutzern
description: Es wird beschrieben, wie Sie den Zugriff auf Ihre Apps, die für Azure AD registriert sind, auf eine Gruppe von ausgewählten Benutzern beschränken.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e07d9f0fa6ec6b4abc7ce96279b7b02faae298fa
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540199"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Gewusst wie: Beschränken Ihrer App auf eine Gruppe von Benutzern

Anwendungen, die für einen Azure AD-Mandanten (Azure Active Directory) registriert sind, sind standardmäßig für alle Benutzer des Mandanten verfügbar, deren Authentifizierung erfolgreich war.

Ähnlich ist es für eine [mehrinstanzenfähige](howto-convert-app-to-be-multi-tenant.md) App: Alle Benutzer des Azure AD-Mandanten, für den die App bereitgestellt wurde, können auf diese Anwendung zugreifen, nachdem sie sich erfolgreich am jeweiligen Mandanten angemeldet haben.

Für Mandantenadministratoren und Entwickler ist es häufig erforderlich, dass eine App auf eine bestimmte Gruppe von Benutzern beschränkt werden muss. Entwickler können dies erreichen, indem sie beliebte Autorisierungsmuster nutzen, z.B. die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC). Dieser Ansatz ist für Entwickler aber mit einem erheblichen Arbeitsaufwand verbunden.

Azure AD ermöglicht Mandantenadministratoren und Entwicklern die Beschränkung einer App auf eine bestimmte Gruppe von Benutzern oder Sicherheitsgruppen im Mandanten.

## <a name="supported-app-configurations"></a>Unterstützte App-Konfigurationen

Die Option zum Beschränken einer App auf eine bestimmte Gruppe von Benutzern oder Sicherheitsgruppen in einem Mandanten funktioniert für die folgenden Arten von Anwendungen:

- Anwendungen, die für die einmalige Verbundanmeldung mit SAML-basierter Authentifizierung konfiguriert sind
- Anwendungsproxyanwendungen mit Verwendung von Azure AD-Vorauthentifizierung
- Anwendungen, die direkt auf der Azure AD-Anwendungsplattform mit Verwendung der OAuth 2.0/OpenID Connect-Authentifizierung erstellt werden (nachdem ein Benutzer oder Administrator seine Zustimmung für die Anwendung erteilt hat).

     > [!NOTE]
     > Dieses Feature ist nur für Web-App/Web-API und Unternehmensanwendungen verfügbar. Apps, die als [nativ](quickstart-v1-integrate-apps-with-azure-ad.md) registriert wurden, können im Mandanten nicht auf eine Gruppe von Benutzern oder Sicherheitsgruppen beschränkt werden.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualisieren der App zum Aktivieren der Benutzerzuweisung

1. Navigieren Sie zum [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **globaler Administrator** an.
1. Wählen Sie in der obersten Leiste das angemeldete Konto aus. 
1. Wählen Sie unter **Verzeichnis** den Azure AD-Mandanten aus, für den die App registriert werden soll.
1. Wählen Sie im Navigationsmenü auf der linken Seite die Option **Azure Active Directory**. Führen Sie die folgenden Schritte aus, falls Azure Active Directory im Navigationsbereich nicht verfügbar ist:

    1. Wählen Sie oben im Hauptnavigationsmenü auf der linken Seite die Option **Alle Dienste**.
    1. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie in den Ergebnissen dann das Element **Azure Active Directory** aus.

1. Wählen Sie im Bereich **Azure Active Directory** im Navigationsmenü **Azure Active Directory** auf der linken Seite die Option **Unternehmensanwendungen**.
1. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

     Falls die gewünschte Anwendung hier nicht angezeigt wird, können Sie oben in der Liste **Alle Anwendungen** die verschiedenen Filter verwenden, um den Inhalt der Liste einzugrenzen. Sie können auch in der Liste nach unten scrollen, um nach Ihrer Anwendung zu suchen.

1. Wählen Sie in der Liste die Anwendung aus, der Sie einen Benutzer oder eine Sicherheitsgruppe zuweisen möchten.
1. Wählen Sie auf der Seite **Übersicht** im Navigationsmenü der Anwendung auf der linken Seite die Option **Eigenschaften**.
1. Suchen Sie nach der Einstellung **Benutzerzuweisung erforderlich?**, und legen Sie sie auf **Ja** fest. Wenn diese Option auf **Ja** festgelegt ist, müssen Benutzer zuerst dieser Anwendung zugewiesen werden, bevor sie darauf zugreifen können.
1. Wählen Sie **Speichern**, um diese Konfigurationsänderung zu speichern.

## <a name="assign-users-and-groups-to-the-app"></a>Zuweisen von Benutzern und Gruppen zur App

Nachdem Sie Ihre App für die Aktivierung der Benutzerzuweisung konfiguriert haben, können Sie damit beginnen, der App Benutzer und Gruppen zuzuweisen.

1. Wählen Sie in der Anwendung im Navigationsmenü auf der linken Seite den Bereich **Benutzer und Gruppen**.
1. Wählen Sie oben in der Liste **Benutzer und Gruppen** die Schaltfläche **Benutzer hinzufügen**, um den Bereich **Zuweisung hinzufügen** zu öffnen.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** den Selektor **Benutzer**. 

     Eine Liste mit Benutzern und Sicherheitsgruppen wird mit einem Textfeld angezeigt, mit dem bestimmte Benutzer oder Gruppen gesucht und ermittelt werden können. Auf diesem Bildschirm können Sie mehrere Benutzer und Gruppen auf einmal auswählen.

1. Nachdem Sie mit dem Auswählen der Benutzer und Gruppen fertig sind, können Sie unten die Schaltfläche **Auswählen** verwenden, um mit dem nächsten Teil fortzufahren.
1. Wählen Sie unten die Schaltfläche **Zuweisen**, um die Zuweisungen von Benutzern und Gruppen für die App abzuschließen. 
1. Vergewissern Sie sich, dass die hinzugefügten Benutzer und Gruppen in der aktualisierten Liste **Benutzer und Gruppen** angezeigt werden.

