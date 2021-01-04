---
title: Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Apps – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Gruppen in Azure Active Directory den Zugriff auf SaaS-Anwendungen zuweisen, die in Azure Active Directory integriert sind.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: df36cd334dbb455cf1717bf18fc6c8337d0ee6d2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547881"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen

Wenn Sie Azure Active Directory (Azure AD) mit einem Azure AD Premium-Lizenzplan nutzen, können Sie Gruppen verwenden, um den Zugriff auf eine in Azure AD integrierte SaaS-Anwendung zuzuweisen. Wenn Sie beispielsweise der Marketingabteilung Zugriff auf fünf verschiedene SaaS-Anwendungen erteilen möchten, können Sie eine Gruppe erstellen, die die Benutzer der Marketingabteilung enthält, und diese Gruppe dann diesen fünf SaaS-Anwendungen zuweisen, die von der Marketingabteilung benötigt werden. Auf diese Weise sparen Sie Zeit, da Sie die Mitgliedschaft für die Marketingabteilung zentral verwalten können. Benutzer werden der Anwendung zugewiesen, wenn sie als Mitglieder der Marketinggruppe hinzugefügt werden. Ebenso wird die Zuweisung der Benutzer zur Anwendung entfernt, wenn sie als Mitglieder aus der Marketinggruppe entfernt werden. Diese Funktion kann für Hunderte von Anwendungen verwendet werden, die Sie aus dem Azure AD-Anwendungskatalog hinzufügen können.

> [!IMPORTANT]
> Sie können diese Funktion erst verwenden, nachdem Sie eine Azure AD Premium-Testversion gestartet oder einen Azure AD Premium-Lizenzplan erworben haben.
> Die gruppenbasierte Zuweisung wird nur für Sicherheitsgruppen unterstützt.
> Geschachtelte Gruppenmitgliedschaften werden für die gruppenbasierte Zuordnung zu Anwendungen derzeit nicht unterstützt.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>So weisen Sie einem Benutzer oder einer Gruppe den Zugriff auf eine SaaS-Anwendung zu

1. Wählen Sie im [Azure AD Admin Center](https://aad.portal.azure.com) die Option **Unternehmensanwendungen**.
2. Wählen Sie eine der aus dem Anwendungskatalog hinzugefügten Anwendungen aus, um diese zu öffnen.
3. Wählen Sie **Benutzer und Gruppen** und dann **Alle Benutzer**.
4. Wählen Sie unter **Zuweisung hinzufügen** die Option **Benutzer und Gruppen**, um die Auswahlliste **Benutzer und Gruppen** zu öffnen.
6. Wählen Sie beliebig viele Gruppen oder Benutzer aus, und klicken oder tippen Sie dann auf **Auswählen**, um diese der Liste **Zuweisung hinzufügen** hinzuzufügen. An dieser Stelle können Sie auch einem Benutzer eine Rolle zuweisen.
7. Wählen Sie **Zuweisen**, um die Benutzer oder Gruppen der ausgewählten Unternehmensanwendung zuzuweisen.

## <a name="next-steps"></a>Nächste Schritte
Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md)
* [Anwendungsverwaltung in Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../enterprise-users/groups-settings-cmdlets.md)
* [Was ist Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
