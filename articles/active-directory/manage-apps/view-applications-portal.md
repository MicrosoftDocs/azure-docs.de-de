---
title: 'Schnellstart: Anzeigen der Liste mit den Anwendungen, von denen Ihr Azure AD-Mandant (Azure Active Directory) für die Identitätsverwaltung genutzt wird'
description: Verwenden Sie in dieser Schnellstartanleitung das Azure-Portal zum Anzeigen der Liste mit den Anwendungen, die für die Nutzung Ihres Azure AD-Mandanten (Azure Active Directory) für die Identitätsverwaltung registriert sind.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: 281e408ca79697edbee308e15b70a2cf2055b212
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658892"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Schnellstart: Anzeigen der Liste mit den Anwendungen, von denen Ihr Azure AD-Mandant (Azure Active Directory) für die Identitätsverwaltung genutzt wird

Führen Sie die ersten Schritte aus, um Azure AD als IAM-System (Identity and Access Management) für die Anwendungen zu nutzen, die von Ihrer Organisation verwendet werden. In dieser Schnellstartanleitung zeigen Sie die Anwendungen (auch als Apps bezeichnet) an, für die die Nutzung des Azure AD-Mandanten als Identitätsanbieter (IdP) bereits eingerichtet ist.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um die für Ihren Azure AD-Mandanten registrierten Anwendungen anzuzeigen:

- Ein Azure-Konto. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Wir empfehlen Ihnen, zum Testen der Schritte in dieser Schnellstartanleitung keine Produktionsumgebung zu verwenden.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Suchen nach der Liste mit den Anwendungen in Ihrem Mandanten

Die Anwendungen, die unter Ihrem Azure AD-Mandanten registriert sind, können im Azure-Portal im Abschnitt **Unternehmens-Apps** angezeigt werden.

Gehen Sie wie folgt vor, um die unter dem Mandanten registrierten Anwendungen anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Navigationsbereich die Option **Azure Active Directory** aus.
3. Wählen Sie im Bereich **Azure Active Directory** die Option **Unternehmensanwendungen** aus.
4. Wählen Sie im Dropdownmenü **Anwendungstyp** die Option **Alle Anwendungen** und dann **Anwenden** aus. Eine nach dem Zufallsprinzip ausgewählte Gruppe Ihrer Mandantenanwendungen wird angezeigt.
5. Wenn Sie weitere Anwendungen anzeigen möchten, wählen Sie unten in der Liste **Mehr anzeigen** aus. Falls Ihr Mandant eine große Zahl von Anwendungen enthält, kann es einfacher sein, direkt nach einer bestimmten Anwendung zu suchen, als durch die Liste zu scrollen. Die Vorgehensweise beim Suchen nach einer bestimmten Anwendung wird später in dieser Schnellstartanleitung beschrieben.

## <a name="select-viewing-options"></a>Auswählen der Anzeigeoptionen

Wählen Sie entsprechende Optionen für die gesuchten Elemente aus.

1. Sie können die Anwendungen nach **Anwendungstyp**, **Anwendungsstatus** und **Anwendungssichtbarkeit** anzeigen.
2. Wählen Sie unter **Anwendungstyp** eine der folgenden Optionen aus:
    - Mit der Option **Unternehmensanwendungen** werden nicht von Microsoft stammende Anwendungen angezeigt.
    - Mit der Option **Microsoft-Anwendungen** werden Microsoft-Anwendungen angezeigt.
    - Mit der Option **Alle Anwendungen** werden nicht von Microsoft stammende Anwendungen und Microsoft-Anwendungen angezeigt.
3. Wählen Sie unter **Anwendungsstatus** die Option **Any** (Alle), **Deaktiviert** oder **Aktiviert** aus. Mit der Option **Any** (Alle) werden sowohl deaktivierte als auch aktivierte Anwendungen angezeigt.
4. Wählen Sie unter **Anwendungssichtbarkeit** die Option **Any** (Alle) oder **Ausgeblendet**. Mit der Option **Ausgeblendet** werden Anwendungen angezeigt, die sich zwar im Mandanten befinden, aber nicht für Benutzer sichtbar sind.
5. Klicken Sie nach dem Auswählen der gewünschten Optionen auf **Anwenden**.

## <a name="search-for-an-application"></a>Suchen einer Anwendung

So suchen Sie nach einer bestimmten Anwendung:

1. Wählen Sie im Menü **Anwendungstyp** die Option **Alle Anwendungen** und dann **Anwenden** aus.
2. Geben Sie den Namen der gewünschten Anwendung ein. Wenn die Anwendung zu Ihrem Azure AD-Mandanten hinzugefügt wurde, wird sie in den Suchergebnissen angezeigt. Dieses Beispiel zeigt, dass GitHub nicht zu den Mandantenanwendungen hinzugefügt wurde.
    ![Beispiel: Dem Mandanten wurde keine App hinzugefügt](media/view-applications-portal/search-for-tenant-application.png)
3. Geben Sie die ersten Buchstaben eines Anwendungsnamens ein. Dieses Beispiel zeigt alle Anwendungen, die mit **Sales** beginnen.
    ![Beispiel: Anzeige aller Apps, die mit „Sales“ beginnen](media/view-applications-portal/search-by-prefix.png)


> [!TIP]
> Sie können die App-Verwaltung mithilfe der Graph-API automatisieren. Informationen hierzu finden Sie unter [Automatisieren einer SAML-basierten SSO-App-Konfiguration mit Microsoft Graph-API](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In dieser Schnellstartanleitung haben Sie keine neuen Ressourcen erstellt, sodass keine Bereinigung ausgeführt werden muss.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie Azure AD als Identitätsanbieter für eine App verwenden:
> [!div class="nextstepaction"]
> [Hinzufügen einer App](add-application-portal.md)