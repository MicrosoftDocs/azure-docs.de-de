---
title: Suchen und Filtern von Gruppenmitgliedern und -besitzern (Vorschau) – Azure Active Directory | Microsoft-Dokumentation
description: Suchen und Filtern von Gruppenmitgliedern und -besitzern im Azure-Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547371"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Suchen von Gruppen und Mitgliedern (Vorschau) in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie nach Mitgliedern und Besitzern einer Gruppe suchen, und wie Sie Suchfilter im Rahmen der Vorschau der Verbesserung für Gruppen im Azure Active Directory-Portal (Azure AD) verwenden. Es gibt viele Verbesserungen in den Gruppenbenutzeroberflächen, mit denen Sie Ihre Gruppen, einschließlich der Mitglieder und Besitzer, schnell und unkompliziert verwalten können. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zu den Änderungen in dieser Vorschau gehören:

- Neue Gruppensuchfunktionen wie z. B. Suchen von Teilzeichenfolgen in Gruppennamen
- Neu Filter- und Sortieroptionen für Mitglieds- und Besitzerlisten
- Neue Suchfunktionen für Mitglieds- und Besitzerlisten
- Genauere Gruppenanzahl für große Gruppen

## <a name="enabling-and-managing-the-preview"></a>Aktivieren und Verwalten der Vorschau

Wir haben die Teilnahme an der Vorschau erleichtert:

  1. Melden Sie sich beim [Azure AD-Portal](https://portal.azure.com) an, und wählen Sie **Gruppen** aus.
  2. Wählen Sie auf der Seite „Gruppen – Alle Gruppen“ das Banner am oberen Rand der Seite aus, um an der Vorschau teilzunehmen.

Um die neuesten Features und Verbesserungen kennenzulernen, können Sie auch **Vorschauinfo** auf der Seite **Alle Gruppen** auswählen. Wenn Sie an der Vorschau teilnehmen, sehen Sie das Vorschautag auf allen Gruppenseiten, die Verbesserungen aufweisen und Teil der Vorschau sind. Nicht jede Gruppenseite wurde im Rahmen dieser Vorschauversion aktualisiert.

Wenn Probleme auftreten, können Sie wieder zur Legacyoberfläche zurückkehren, indem Sie das Banner am oberen Rand der Seite **Alle Gruppen** auswählen. Wir freuen uns über Ihr Feedback, denn es gibt uns wertvolle Anregungen zur Verbesserung unserer Benutzeroberflächen.

## <a name="group-search-and-sorting"></a>Suchen und Sortieren bei Gruppen

Die Suche in der Gruppenliste wurde verbessert. Sobald Sie mit der Eingabe einer Suchzeichenfolge beginnen, führt die Suchfunktion automatisch eine `startswith`- und Teilzeichenfolgesuche in der Liste der Gruppennamen durch. Die Teilzeichenfolgesuche wird nur bei ganzen Wörtern durchgeführt und berücksichtigt keine Sonderzeichen. Bei der Teilzeichenfolgesuche wird zwischen Groß- und Kleinschreibung unterschieden.

![Neue Teilzeichenfolgesuchen auf der Seite „Alle Gruppen“](./media/groups-members-owners-search/groups-search-preview.png)

Eine Suche nach „Policy“ gibt nun z. B. sowohl „MDM policy – West“ als auch „Policy group“ zurück. Eine Gruppe mit dem Namen „New_policy“ würde nicht zurückgegeben.

- Sie können dieselbe Suche auch in Gruppenmitgliedschaftslisten durchführen.
- Jetzt können Sie die Gruppenliste mithilfe der Pfeile rechts neben der Namenspaltenüberschrift nach Namen sortieren, um die Liste in aufsteigender oder absteigender Reihenfolge zu sortieren.

## <a name="group-member-search-and-filtering"></a>Suchen und Filtern von Gruppenmitgliedern

### <a name="search-group-member-and-owner-lists"></a>Suchen in Gruppenmitglieder- und Besitzerlisten

Jetzt können Sie die Mitglieder einer bestimmten Gruppe nach Namen suchen und die gleiche Suche auch in der Liste der Gruppenbesitzer durchführen. Wenn Sie in der neuen Benutzeroberfläche eine Zeichenfolge in das Suchfeld eingeben, wird automatisch eine „Beginnt mit“-Suche durchgeführt. Beispielsweise gibt eine Suche nach „Scott“ „Scott Wilkinson“ zurück.

![Neue Teilzeichenfolgen-Suchvorgänge in Gruppenmitglieder- und Besitzerlisten](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Filtern von Mitglieder- und Besitzerlisten

Neben der Suche können Sie nun die Mitglieder- und Besitzerlisten nach dem Benutzertyp filtern. Diese Information enthält die Spalte „Benutzertyp“ der Liste. Daher können Sie die Liste nach Mitgliedern und Gästen filtern, um zu ermitteln, ob in der Gruppe Gäste vorhanden sind.

### <a name="view-and-manage-membership"></a>Anzeigen und Verwalten der Mitgliedschaft

Zusätzlich zum Anzeigen der direkten Mitglieder einer bestimmten Gruppe können Sie nun die Liste aller Mitglieder der Gruppe auf der Seite „Mitglieder“ anzeigen. Die Mitgliederliste enthält alle eindeutigen Mitglieder der Gruppe einschließlich transitiver Mitglieder.

Sie können auch die Liste der direkten Mitglieder und die Liste alle Mitglieder einzeln durchsuchen und filtern. Das Filtern der Liste alle Mitglieder wirkt sich nicht auf die Filter aus, die auf die Liste der direkten Mitglieder angewendet werden.

## <a name="improved-group-member-counts"></a>Verbesserte Anzahl der Gruppenmitglieder

Wir haben die Seite **Übersicht** für Gruppen so verbessert, dass die Gruppenmitgliederzahlen von Gruppen aller Größen berücksichtigt werden. Sie sehen auch die Mitgliederzahlen für Gruppen mit mehr als 1.000 Mitgliedern. Jetzt können Sie die Gesamtzahl der direkten Mitglieder einer Gruppe und die Gesamtzahl der Mitgliedschaften (alle eindeutigen Mitglieder der Gruppe einschließlich transitiver Mitglieder) auf der Seite **Übersicht** sehen.

![Höhere Genauigkeit bei der Anzahl der Gruppenmitgliedschaften](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel bieten zusätzliche Informationen zur Arbeit mit Gruppen in Azure AD.

- [Anzeigen Ihrer Gruppen und Mitglieder](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Verwalten der Gruppenmitgliedschaft](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-create-rule.md)
- [Bearbeiten Ihrer Gruppeneinstellungen](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Verwalten des Zugriffs auf Ressourcen mittels Gruppen](../fundamentals/active-directory-manage-groups.md)
- [Verwalten des Zugriffs auf SaaS-Apps mittels Gruppen](groups-saasapps.md)
- [Verwalten von Gruppen mithilfe von PowerShell-Befehlen](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Hinzufügen eines Azure-Abonnements zu Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
