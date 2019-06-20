---
title: include file
description: include file
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 364d4a11772e6bb72e2e258503f3cce49dc61453
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178786"
---
## <a name="create-one-or-more-access-reviews"></a>Erstellen einer oder mehrerer Zugriffsüberprüfungen

1. Klicken Sie auf **Neu**, um eine neue Zugriffsüberprüfung zu erstellen.

1. Benennen Sie die Zugriffsüberprüfung. Wahlweise können Sie jeder Überprüfung eine Beschreibung hinzufügen. Den Prüfern werden Name und Beschreibung angezeigt.

    ![Erstellen einer Zugriffsüberprüfung – Name und Beschreibung der Überprüfung](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Legen Sie das **Startdatum** fest. Standardmäßig findet eine Zugriffsüberprüfung einmalig statt. Sie beginnt an dem Tag, an dem sie erstellt wird, und endet nach einem Monat. Sie können Start- und Enddatum so ändern, dass der Start der Zugriffsüberprüfung in der Zukunft liegt und sie so lange dauert, wie Sie wünschen.

    ![Erstellen einer Zugriffsüberprüfung – Start- und Enddatum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Wenn die Zugriffsüberprüfung wiederholt ausgeführt werden soll, ändern Sie die Einstellung **Häufigkeit** von **Einmal** in **Wöchentlich**, **Monatlich**,  **Vierteljährlich**, **Jährlich** oder **Halbjährlich**. Verwenden Sie den Schieberegler oder das Textfeld **Dauer**, um festzulegen, wie viele Tage Prüfer Eingaben für jede Überprüfung der Serie vornehmen können. Für eine monatliche Überprüfung kann beispielsweise eine maximale Dauer von 27 Tagen angegeben werden, um Überschneidungen zu vermeiden.

1. Geben Sie mithilfe der Einstellung **Ende** an, wie die wiederkehrende Zugriffsüberprüfungsreihe beendet werden soll. Die Reihe kann auf drei Arten enden: Die Serie wird unendlich ausgeführt, um Überprüfungen ohne zeitliche Beschränkung zu starten, die Serie wird bis zu einem bestimmten Datum ausgeführt, oder sie wird nach einer bestimmten Anzahl von Vorkommen beendet. Sie (oder ein anderer Benutzeradministrator oder globaler Administrator) können die Serie nach der Erstellung beenden, indem Sie unter **Einstellungen** das Datum ändern, sodass die Serie an diesem Datum endet.

1. Wählen Sie im Abschnitt **Benutzer** eine oder mehrere Rollen aus, deren Mitgliedschaft überprüft werden soll.

    ![Erstellen einer Zugriffsüberprüfung: Benutzer](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Bei der Auswahl mehrerer Rollen werden mehrere Zugriffsüberprüfungen erstellt. Bei der Auswahl von fünf Rollen werden z. B. fünf separate Zugriffsüberprüfungen erstellt.

    Wenn Sie eine Zugriffsüberprüfung für Azure AD-Rollen erstellen, sieht die Liste der zu überprüfenden Mitgliedschaften in etwa wie im folgenden Beispiel aus:

    ![Erstellen einer Zugriffsüberprüfung: Überprüfen der Rollenmitgliedschaft](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Wenn Sie eine Zugriffsüberprüfung für Azure-Ressourcenrollen erstellen, sieht die Liste der zu überprüfenden Mitgliedschaften in etwa wie im folgenden Beispiel aus:

    ![Erstellen einer Zugriffsüberprüfung: Überprüfen der Rollenmitgliedschaft](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Wählen Sie im Abschnitt **Prüfer** mindestens eine Person für die Überprüfung aller Benutzer aus. Alternativ können Sie auswählen, dass die Mitglieder ihren eigenen Zugriff überprüfen.

    ![Erstellen einer Zugriffsüberprüfung – Prüfer](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Ausgewählte Benutzer**: Verwenden Sie diese Option, wenn Sie nicht wissen, wer Zugriff benötigt. Mit dieser Option können Sie die Überprüfungsdurchführung einem Ressourcenbesitzer oder Gruppen-Manager zuweisen.
    - **Mitglieder (selbst)** : Mit dieser Option können Benutzer ihre Rollenzuweisungen selbst überprüfen.

### <a name="upon-completion-settings"></a>Einstellungen nach Abschluss

1. Erweitern Sie den Abschnitt **Einstellungen nach Abschluss**, um anzugeben, was nach Abschluss der Überprüfung geschehen soll.

    ![Einstellungen nach Abschluss](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Soll abgelehnten Benutzern automatisch der Zugriff entzogen werden, legen Sie **Ergebnisse automatisch auf Ressource anwenden** auf **Aktivieren** fest. Falls Sie die Ergebnisse nach Abschluss der Überprüfung manuell anwenden möchten, legen Sie die Einstellung auf **Deaktivieren** fest.

1. Geben Sie mithilfe der Liste **Falls der Prüfer nicht reagiert** an, was bei Benutzern geschehen soll, die vom Prüfer nicht innerhalb des vorgesehenen Zeitraums überprüft werden. Diese Einstellung hat keine Auswirkungen auf Benutzer, die von den Prüfern manuell überprüft wurden. Lautet die Entscheidung des Prüfers letztlich „Verweigern“, wird dem Benutzer der Zugriff entzogen.

    - **Keine Änderung**: Der Zugriff des Benutzers bleibt unverändert.
    - **Zugriff entfernen**: Dem Benutzer wird der Zugriff entzogen.
    - **Zugriff genehmigen**: Der Zugriff des Benutzers wird genehmigt.
    - **Empfehlungen annehmen**: Die Systemempfehlungen hinsichtlich der Ablehnung oder Gewährung des weiteren Benutzerzugriffs werden verwendet.

### <a name="advanced-settings"></a>Erweiterte Einstellungen

1. Erweitern Sie den Abschnitt **Erweiterte Einstellungen**, um weitere Einstellungen anzugeben.

    ![Erweiterte Einstellungen](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Legen Sie **Empfehlungen anzeigen** auf **Aktivieren** fest, damit den Prüfern die Systemempfehlungen auf der Grundlage der Zugriffsinformationen des Benutzers angezeigt werden.

1. Legen Sie **Bei Genehmigung Grund anfordern** auf **Aktivieren** fest, damit der Prüfer einen Grund für die Genehmigung angeben muss.

1. Legen Sie **E-Mail-Benachrichtigungen** auf **Aktivieren** fest, damit Azure AD beim Start einer Zugriffsüberprüfung E-Mail-Benachrichtigungen an die Prüfer und beim Abschluss einer Überprüfung Benachrichtigungen an Administratoren sendet.

1. Legen Sie **Erinnerungen** auf **Aktivieren** fest, damit Azure AD Erinnerungen zu laufenden Zugriffsüberprüfungen an Prüfer sendet, die ihre Überprüfung noch nicht abgeschlossen haben.
