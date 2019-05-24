---
title: Anzeige von Anwendungen im Zugriffsbereich | Microsoft-Dokumentation
description: Beheben des Problems, dass eine Anwendung im Zugriffsbereich angezeigt wird
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
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784411"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Anzeige von Anwendungen im Zugriffsbereich

Der Zugriffsbereich ist ein webbasiertes Portal, mit dem Benutzer mit einem Geschäfts-, Schul- oder Unikonto in Azure Active Directory (Azure AD) cloudbasierte Anwendungen anzeigen und starten können, für die der Azure AD-Administrator ihnen Zugriff gewährt hat. Diese Anwendungen werden im Namen des Benutzers im Azure AD-Portal konfiguriert. Der Administrator kann die Anwendung für den Benutzer direkt oder für eine Gruppe bereitstellen, zu der der Benutzer gehört. Beide Verfahren führen dazu, dass die Anwendung im Zugriffsbereich des Benutzers angezeigt wird.

## <a name="general-issues-to-check-first"></a>Allgemeine Probleme, die zuerst überprüft werden sollten

-   Wenn eine Anwendung für einen Benutzer oder eine Gruppe entfernt wurde, zu der der Benutzer gehört, sollte sich der Benutzer vom Zugriffsbereich abmelden und nach einigen Minuten wieder anmelden, um festzustellen, ob die Anwendung entfernt wurde.

-   Wenn eine Lizenz für einen Benutzer oder eine Gruppe entfernt wurde, zu der der Benutzer gehört, kann es je nach Größe und Komplexität der Gruppe lange dauern, bis die Änderungen durchgeführt wurden. Lassen Sie vor der Anmeldung beim Zugriffsbereich etwas Zeit verstreichen.

## <a name="problems-related-to-assigning-applications-to-users"></a>Probleme beim Zuweisen von Anwendungen zu Benutzern

Möglicherweise wird eine Anwendung im Zugriffsbereich eines Benutzers angezeigt, da der Benutzer der Anwendung zuvor zugewiesen war. Nachfolgend sind einige Möglichkeiten aufgeführt, dies zu überprüfen:

-   [Überprüfen, ob der Anwendung ein Benutzer zugewiesen ist](#check-if-a-user-is-assigned-to-the-application)

-   [Überprüfen, ob dem Benutzer eine Lizenz für die Anwendung zugewiesen ist](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Überprüfen, ob der Anwendung ein Benutzer zugewiesen ist

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob der Benutzer der Anwendung zugewiesen ist:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

6. **Suchen** Sie den Namen der betreffenden Anwendung.

7. Klicken Sie auf **Benutzer und Gruppen**.

8. Überprüfen Sie , ob der Benutzer der Anwendung zugewiesen ist.

   * Wenn Sie den Benutzer aus der Anwendung entfernen möchten, **klicken Sie auf die Zeile** des Benutzers, und wählen Sie **Löschen** aus.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Überprüfen, ob ein Benutzer einer Lizenz für die Anwendung zugewiesen ist

Um die einem Benutzer zugewiesenen Lizenzen zu überprüfen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5. Klicken Sie auf **Alle Benutzer**.

6. **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7. Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen dem Benutzer derzeit zugewiesen sind.

   * Wenn der Benutzer einer Office-Lizenz zugewiesen ist, können im Zugriffsbereich des Benutzers Erstanbieter-Office-Anwendungen angezeigt werden.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Probleme beim Zuweisen von Anwendungen zu Gruppen

Möglicherweise wird eine Anwendung im Zugriffsbereich eines Benutzers angezeigt, da der Benutzer Mitglied einer Gruppe ist, der die Anwendung zugewiesen ist. Nachfolgend sind einige Möglichkeiten aufgeführt, dies zu überprüfen:

-   [Überprüfen der Gruppenmitgliedschaften eines Benutzers](#check-a-users-group-memberships)

-   [Überprüfen, ob ein Benutzer Mitglied einer Gruppe ist, die einer Lizenz zugewiesen ist](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Überprüfen der Gruppenmitgliedschaften eines Benutzers

Um die Mitgliedschaften einer Gruppe zu überprüfen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5. Klicken Sie auf **Alle Benutzer**.

6. **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7. Klicken Sie auf **Gruppen**.

8. Überprüfen Sie, ob der Benutzer einer Gruppe angehört, die der Anwendung zugewiesen ist.

   * Wenn Sie den Benutzer aus der Gruppe entfernen möchten, **klicken Sie auf die Zeile** der Gruppe, und wählen Sie „Löschen“ aus.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Überprüfen, ob ein Benutzer Mitglied einer Gruppe ist, die einer Lizenz zugewiesen ist

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5. Klicken Sie auf **Alle Benutzer**.

6. **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7. Klicken Sie auf **Gruppen**.

8. Klicken Sie auf die Zeile für eine bestimmte Gruppe.

9. Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen der Gruppe zugewiesen sind.

   * Wenn die Gruppe einer Office-Lizenz zugewiesen ist, können im Zugriffsbereich des Benutzers bestimmte Erstanbieter-Office-Anwendungen angezeigt werden.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Wenn das Problem mit diesen Problembehandlungsschritten nicht behoben wird

Öffnen Sie ein Supportticket mit den folgenden Informationen, sofern verfügbar:

-   Fehlerkorrelations-ID

-   UPN (E-Mail-Adresse des Benutzers)

-   Mandanten-ID

-   Browsertyp

-   Zeitzone und Uhrzeit/Zeitraum des Auftretens des Fehlers

-   Fiddler-Ablaufverfolgungen

## <a name="next-steps"></a>Nächste Schritte
[Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
