---
title: Entfernen des Zugriffs eines Benutzers auf eine Anwendung in Azure Active Directory
description: Hier wird erläutert, wie Sie den Zugriff eines Benutzers auf eine Anwendung in Azure Active Directory entfernen
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 28b31d98f283dc957927ab2a35f0ab95bf066473
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654115"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Aufheben des Zugriffs eines Benutzers auf eine Anwendung

In diesem Artikel erhalten Sie Informationen zum Entfernen des Zugriffs eines Benutzers auf eine Anwendung.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ich möchte die Zuweisung eines bestimmten Benutzers oder einer bestimmten Gruppe zu einer Anwendung entfernen

Um eine Benutzer- oder Gruppenzuweisung zu einer Anwendung zu entfernen, führen Sie die im Artikel [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App in Azure Active Directory](./assign-user-or-group-access-portal.md) beschriebenen Schritte aus.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ich möchte den gesamten Zugriff auf eine Anwendung für alle Benutzer deaktivieren

Um alle Benutzeranmeldungen bei einer Anwendung zu deaktivieren, führen Sie die im Artikel [Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App in Azure Active Directory](./disable-user-sign-in-portal.md) beschriebenen Schritte aus.

## <a name="i-want-to-delete-an-application-entirely"></a>Ich möchte eine Anwendung vollständig löschen.

Die [Schnellstartreihe zur Anwendungsverwaltung](delete-application-portal.md) enthält Anleitungen zum Löschen einer Anwendung aus Ihrem Azure Active Directory-Mandanten.

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