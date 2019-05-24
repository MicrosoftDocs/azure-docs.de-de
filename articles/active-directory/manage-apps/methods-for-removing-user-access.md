---
title: Aufheben des Zugriffs eines Benutzers auf eine Anwendung | Microsoft-Dokumentation
description: Informationen zum Aufheben des Zugriffs eines Benutzers auf eine Anwendung
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
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826095"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Aufheben des Zugriffs eines Benutzers auf eine Anwendung

In diesem Artikel erhalten Sie Informationen zum Entfernen des Zugriffs eines Benutzers auf eine Anwendung.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ich möchte die Zuweisung eines bestimmten Benutzers oder einer bestimmten Gruppe zu einer Anwendung entfernen

Um eine Benutzer- oder Gruppenzuweisung zu einer Anwendung zu entfernen, führen Sie die im Artikel [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) beschriebenen Schritte aus.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ich möchte den gesamten Zugriff auf eine Anwendung für alle Benutzer deaktivieren

Um alle Benutzeranmeldungen bei einer Anwendung zu deaktivieren, führen Sie die im Artikel [Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) beschriebenen Schritte aus.

## <a name="i-want-to-delete-an-application-entirely"></a>Ich möchte eine Anwendung vollständig löschen.

Befolgen Sie die nachstehenden Anweisungen, um **eine Anwendung zu löschen**:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, die Sie löschen möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im Bereich **Übersicht** der Anwendung auf das Symbol **Löschen**.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ich möchte alle zukünftigen Vorgänge für die Benutzerzustimmung für jede Anwendung deaktivieren.

Die Deaktivierung der Benutzerzustimmung für das gesamte Verzeichnis führt dazu, dass Endbenutzer keiner Anwendung zustimmen können. Administratoren können weiterhin im Namen von Benutzern zustimmen. Weitere Informationen zur Einwilligung zu Anwendungen sowie Gründen für oder gegen Ihre Einwilligung finden Sie unter [Grundlegendes zur Benutzer- und Administratoreinwilligung](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Siehe auch [Berechtigungen und Einwilligung](../develop/v2-permissions-and-consent.md).

Befolgen Sie die nachstehenden Anweisungen, um **alle zukünftigen Vorgänge der Benutzerzustimmung in Ihrem gesamten Verzeichnis zu deaktivieren**:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**. 

3.  Klicken Sie im Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie auf **Benutzereinstellungen**.

6.  Legen Sie die Umschaltfläche **Benutzer können Apps Zugriff auf Unternehmensdaten in ihrem Namen gestatten** auf **Nein** fest, und klicken Sie auf die Schaltfläche „Speichern“.


## <a name="next-steps"></a>Nächste Schritte

[Verwalten des Zugriffs auf Apps](what-is-access-management.md)
