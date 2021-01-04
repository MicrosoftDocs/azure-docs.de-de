---
title: Probleme beim Anmelden bei Microsoft-Anwendungen | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zur Behandlung von allgemeinen Problemen bei der Anmeldung in eigenen Microsoft-Anwendungen mithilfe von Azure AD (z. B. Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57deed9d7fb178ba1cdc8d6e954d751752532de4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654404"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Probleme beim Anmelden bei Microsoft-Anwendungen

Microsoft-Anwendungen (z. B. Exchange, SharePoint, Yammer usw.) werden nicht genau auf die gleiche Weise zugewiesen und verwaltet wie SaaS-Anwendungen von Drittanbietern oder andere Anwendungen, die Sie für das einmalige Anmelden mit Azure AD integrieren.

Es gibt drei Hauptmethoden, über die ein Benutzer Zugriff auf eine von Microsoft veröffentlichte Anwendung erhalten kann.

-   Für Anwendungen in Microsoft 365 oder anderen kostenpflichtigen Sammlungen erhalten Benutzer Zugriff über eine **Lizenzzuweisung** direkt in ihrem Benutzerkonto oder über eine Gruppe mithilfe der Funktion für gruppenbasierte Lizenzzuweisung.

-   Für Anwendungen, die Microsoft oder ein Drittanbieter kostenlos für alle Benutzer veröffentlicht, erhalten Benutzer möglicherweise Zugriff über eine **Benutzerzustimmung**. Dies bedeutet, dass sie sich mit ihrem Geschäfts-, Schul- oder Unikonto von Azure AD bei der Anwendung anmelden und dieser den Zugriff auf eine begrenzte Menge von Daten in ihrem Konto gewähren.

-   Für Anwendungen, die Microsoft oder ein Drittanbieter kostenlos für alle Benutzer veröffentlicht, können Benutzer auch Zugriff über eine **Administratorzustimmung** erhalten. Dies bedeutet, dass ein Administrator festgelegt hat, dass die Anwendung von allen Personen in seiner Organisation verwendet werden kann. Daher meldet er sich mit einem globalen Administratorkonto bei der Anwendung an und gewährt allen Benutzer in der Organisation Zugriff.

Um Ihr Problem zu beheben, beginnen Sie mit [Allgemeine zu berücksichtigende Problembereiche beim Anwendungszugriff](#general-problem-areas-with-application-access-to-consider). Lesen Sie dann „Exemplarische Vorgehensweise: Schritte zur Behandlung von Problemen beim Zugriff auf Microsoft-Anwendungen“, um ausführlichere Informationen zu erhalten.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Allgemeine zu berücksichtigende Problembereiche beim Anwendungszugriff

Im Anschluss finden Sie eine Liste der allgemeinen Problembereiche, die Sie untersuchen können, wenn Sie eine Vorstellung davon haben, wo Sie ansetzen können. Für einen schnellen Einstieg empfehlen wir jedoch den Artikel Exemplarische Vorgehensweise: Schritte zum Behandeln von Problemen beim Zugriff auf Microsoft-Anwendungen.

-   [Probleme mit dem Konto des Benutzers](#problems-with-the-users-account)

-   [Probleme mit Gruppen](#problems-with-groups)

-   [Probleme mit Richtlinien für bedingten Zugriff](#problems-with-conditional-access-policies)

-   [Probleme mit der Anwendungszustimmung](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Schritte zum Behandeln von Problemen beim Zugriff auf Microsoft-Anwendungen

Im Folgenden werden einige allgemeine Probleme beschrieben, die auftreten können, wenn Benutzer sich nicht bei einer Microsoft-Anwendung anmelden können.

- Allgemeine Probleme, die zuerst überprüft werden sollten

  * Stellen Sie sicher, dass der Benutzer sich bei der **richtigen URL** anmeldet und nicht bei der URL einer lokalen Anwendung.

  * Stellen Sie sicher, dass das Konto des Benutzers **nicht gesperrt ist**.

  * Stellen Sie sicher, dass das **Benutzerkonto** in Azure Active Directory vorhanden ist. [Überprüfen, ob ein Benutzerkonto in Azure Active Directory vorhanden ist](#problems-with-the-users-account)

  * Stellen Sie sicher, dass das Konto des Benutzers für Anmeldungen **aktiviert** ist. [Überprüfen des Status eines Benutzerkontos](#problems-with-the-users-account)

  * Stellen Sie sicher, dass **Kennwort des Benutzers nicht abgelaufen ist oder vergessen wurde**. [Zurücksetzen eines Benutzerkennworts](#reset-a-users-password) oder [Aktivieren von Self-Service-Kennwortzurücksetzung](../authentication/tutorial-enable-sspr.md)

  * Stellen Sie sicher, dass Benutzerzugriff nicht durch **Multi-Factor Authentication** blockiert wird. [Überprüfen des Multi-Factor Authentication-Status eines Benutzers](#check-a-users-multi-factor-authentication-status) oder [Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers](#check-a-users-authentication-contact-info)

  * Stellen Sie sicher, dass der Benutzerzugriff nicht durch eine **Richtlinie für bedingten Zugriff** oder eine **Identity Protection**-Richtlinie blockiert wird. [Überprüfen einer bestimmten Richtlinie für bedingten Zugriff](#problems-with-conditional-access-policies) oder [Überprüfen der Richtlinie für bedingten Zugriff einer bestimmten Anwendung](#check-a-specific-applications-conditional-access-policy) oder [Deaktivieren einer bestimmten Richtlinie für bedingten Zugriff](#disable-a-specific-conditional-access-policy)

  * Stellen Sie sicher, dass die **Kontaktinformationen für die Authentifizierung** des Benutzers aktuell sind, sodass Multi-Factor Authentication-Richtlinien oder Richtlinien für bedingten Zugriff erzwungen werden können. [Überprüfen des Multi-Factor Authentication-Status eines Benutzers](#check-a-users-multi-factor-authentication-status) oder [Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers](#check-a-users-authentication-contact-info)

- Im Anschluss werden einige spezifische Probleme für **Microsoft**-**Anwendungen, die eine Lizenz erfordern** (beispielsweise Office 365) aufgeführt, die Sie überprüfen können, nachdem Sie die oben genannten allgemeinen Probleme ausgeschlossen haben:

  * Stellen Sie sicher, dass dem Benutzer eine **Lizenz zugewiesen** ist. [Überprüfen der zugewiesenen Lizenzen eines Benutzers](#check-a-users-assigned-licenses) oder [Überprüfen der zugewiesenen Lizenzen einer Gruppe](#check-a-groups-assigned-licenses)

  * Wenn die Lizenz einer **statischen Gruppe** **zugewiesen ist**, stellen Sie sicher, dass der **Benutzer ein Mitglied dieser Gruppe ist**. [Überprüfen der Gruppenmitgliedschaften eines Benutzers](#check-a-users-group-memberships)

  * Wenn die Lizenz einer **dynamischen Gruppe** **zugewiesen ist**, stellen Sie sicher, dass die **Regel der dynamischen Gruppe richtig festgelegt wurde**. [Überprüfen der Mitgliedschaftskriterien einer dynamischen Gruppe](#check-a-dynamic-groups-membership-criteria)

  * Wenn die Lizenz einer **dynamischen Gruppe** **zugewiesen ist**, stellen Sie sicher, dass die dynamische Gruppe die **Verarbeitung der Mitgliedschaften abgeschlossen hat** und dass der **Benutzer ein Mitglied ist**. (Dies kann einige Zeit dauern.) [Überprüfen der Gruppenmitgliedschaften eines Benutzers](#check-a-users-group-memberships)

  *  Nachdem Sie sichergestellt haben, dass die Lizenz zugewiesen ist, stellen Sie sicher, dass die Lizenz **nicht abgelaufen** ist.

  *  Stellen Sie sicher, dass sie Lizenz **für die Anwendung**, auf die der Benutzer zugreifen möchte, gültig ist.

- Im Anschluss finden Sie einige weitere Aspekte, die Sie für **Microsoft**-**Anwendungen, die keine Lizenz erfordern**, überprüfen können:

  * Wenn die Anwendung **Berechtigungen auf Benutzerebene** (beispielsweise „Zugriff auf das Postfach dieses Benutzers“) anfordert, stellen Sie sicher, dass der Benutzer sich bei der Anwendung angemeldet und eine **Zustimmung auf Benutzerebene** durchgeführt hat, damit die Anwendung auf seine Daten zugreifen kann.

  * Wenn die Anwendung **Berechtigungen auf Administratorebene** (beispielsweise „Auf die Postfächer aller Benutzer zugreifen“) anfordert, stellen Sie sicher, dass ein globaler Administrator eine **Zustimmung auf Benutzerebene im Namen aller Benutzer** in der Organisation durchgeführt hat.

## <a name="problems-with-the-users-account"></a>Probleme mit dem Konto des Benutzers

Der Anwendungszugriff kann aufgrund eines Problems mit einem Benutzer blockiert werden, die der Anwendung zugewiesen ist. Nachfolgend finden Sie einige Möglichkeiten zum Lösen von Problemen mit Benutzerkonten und Kontoeinstellungen:

-   [Überprüfen, ob ein Benutzerkonto in Azure Active Directory vorhanden ist](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Überprüfen des Status eines Benutzerkontos](#check-a-users-account-status)

-   [Zurücksetzen des Kennworts für einen Benutzer](#reset-a-users-password)

-   [Aktivieren der Self-Service-Kennwortzurücksetzung](#enable-self-service-password-reset)

-   [Überprüfen des Multi-Factor Authentication-Status eines Benutzers](#check-a-users-multi-factor-authentication-status)

-   [Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers](#check-a-users-authentication-contact-info)

-   [Überprüfen der Gruppenmitgliedschaften eines Benutzers](#check-a-users-group-memberships)

-   [Überprüfen der zugewiesenen Lizenzen eines Benutzers](#check-a-users-assigned-licenses)

-   [Zuweisen einer Lizenz zu einem Benutzer](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Überprüfen, ob ein Benutzerkonto in Azure Active Directory vorhanden ist

Um zu überprüfen, ob das Konto eines Benutzers vorhanden ist, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Überprüfen Sie die Eigenschaften des Benutzerobjekts, um sicherzustellen, dass diese ordnungsgemäß festgelegt sind und keine Daten fehlen.

### <a name="check-a-users-account-status"></a>Überprüfen des Status eines Benutzerkontos

Um den Status eines Benutzerkontos zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Profil**.

8.  Stellen Sie unter **Einstellungen** sicher, dass die Option **Anmeldung blockieren** auf **Nein** festgelegt ist.

### <a name="reset-a-users-password"></a>Zurücksetzen des Kennworts für einen Benutzer

Um das Kennwort eines Benutzers zurückzusetzen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie oben im Benutzerbereich auf die Schaltfläche **Kennwort zurücksetzen**.

8.  Klicken Sie im jetzt angezeigten Bereich **Kennwort zurücksetzen** auf die Schaltfläche **Kennwort zurücksetzen**.

9.  Kopieren Sie das **temporäre Kennwort**, oder **geben Sie ein neues Kennwort ein**.

10. Teilen Sie dem Benutzer das neue Kennwort mit, da dieser das Kennwort bei der nächsten Anmeldung bei Azure Active Directory ändern muss.

### <a name="enable-self-service-password-reset"></a>Aktivieren der Self-Service-Kennwortzurücksetzung

Um die Self-Service-Kennwortzurücksetzung zu aktivieren, führen Sie die folgenden Bereitstellungsschritte aus:

-   [Aktivieren von Benutzern für das Zurücksetzen ihrer Azure Active Directory-Kennwörter](../authentication/tutorial-enable-sspr.md)

-   [Aktivieren von Benutzern für das Zurücksetzen oder Ändern ihrer lokalen Active Directory-Kennwörter](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Überprüfen des Multi-Factor Authentication-Status eines Benutzers

Um den Multi-Factor Authentication-Status eines Benutzers zu überprüfen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5. Klicken Sie auf **Alle Benutzer**.

6. Klicken Sie oben im Bereich auf die Schaltfläche **Multi-Factor Authentication**.

7. Nachdem das **Multi-Factor Authentication-Verwaltungsportal** geladen wurde, stellen Sie sicher, dass Sie sich auf der Registerkarte **Benutzer** befinden.

8. Suchen Sie den Benutzer, indem Sie die Liste der Benutzer durchsuchen, filtern oder sortieren.

9. Wählen Sie den Benutzer aus der Benutzerliste aus, und wählen Sie die gewünschte Option für die Multi-Factor Authentication: **Aktivieren**, **Deaktivieren** oder **Erzwingen**.

   * **Hinweis**: Wenn ein Benutzer sich im Status **Erzwungen** befindet, können Sie den Status vorübergehend auf **Deaktiviert** festlegen, damit der Benutzer sich wieder bei seinem Konto anmelden kann. Sobald der Benutzer wieder bei seinem Konto angemeldet ist, können Sie den Status zu **Aktiviert** ändern, damit der Benutzer bei der nächsten Anmeldung seine Kontaktinformationen erneut registrieren muss. Alternativ können Sie Schritte unter [Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers](#check-a-users-authentication-contact-info) ausführen, um diese Daten für den Benutzer zu überprüfen oder festzulegen.

### <a name="check-a-users-authentication-contact-info"></a>Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers

Um die Kontaktinformationen zur Authentifizierung des Benutzers zu überprüfen, die für Multi-Factor Authentication, bedingten Zugriff, Identity Protection und Kennwortzurücksetzung verwendet werden, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Profil**.

8.  Scrollen Sie nach unten zu **Kontaktinformationen für Authentifizierung**.

9.  **Überprüfen** Sie die für den Benutzer registrierten Daten, und aktualisieren Sie sie bei Bedarf.

### <a name="check-a-users-group-memberships"></a>Überprüfen der Gruppenmitgliedschaften eines Benutzers

Um die Gruppenmitgliedschaften eines Benutzers zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Gruppen**, um zu ermitteln, in welchen Gruppen der Benutzer Mitglied ist.

### <a name="check-a-users-assigned-licenses"></a>Überprüfen der zugewiesenen Lizenzen eines Benutzers

Um die einem Benutzer zugewiesenen Lizenzen zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen dem Benutzer derzeit zugewiesen sind.

### <a name="assign-a-user-a-license"></a>Zuweisen einer Lizenz zu einem Benutzer 

Um einem Benutzer eine Lizenz zuzuweisen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen dem Benutzer derzeit zugewiesen sind.

8.  Klicken Sie auf die Schaltfläche **Zuweisen**.

9.  Wählen Sie in der Liste der verfügbaren Produkte **ein oder mehrere Produkte** aus.

10. **Optional:** Klicken Sie auf die Option für **Zuweisungsoptionen**, um Produkte differenziert zuzuweisen. Klicken Sie auf **OK**, wenn Sie fertig sind.

11. Klicken Sie auf die Schaltfläche **Zuweisen**, um dem Benutzer diese Lizenzen zuzuweisen.

## <a name="problems-with-groups"></a>Probleme mit Gruppen

Der Anwendungszugriff kann aufgrund eines Problems mit einer Gruppe blockiert werden, die der Anwendung zugewiesen ist. Nachfolgend finden Sie einige Möglichkeiten zum Lösen von Problemen mit Gruppen und Gruppenmitgliedschaften:

-   [Überprüfen der Mitgliedschaft einer Gruppe](#check-a-groups-membership)

-   [Überprüfen der Mitgliedschaftskriterien einer dynamischen Gruppe](#check-a-dynamic-groups-membership-criteria)

-   [Überprüfen der zugewiesenen Lizenzen einer Gruppe](#check-a-groups-assigned-licenses)

-   [Erneutes Verarbeiten der Lizenzen einer Gruppe](#reprocess-a-groups-licenses)

-   [Zuweisen von Lizenzen zu einer Gruppe](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Überprüfen der Mitgliedschaft einer Gruppe

Um die Mitgliedschaften einer Gruppe zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Gruppen**.

6.  **Suchen** Sie nach der gewünschten Gruppe, und **klicken Sie auf die Zeile**, um sie auszuwählen.

7.  Klicken Sie auf **Mitglieder**, um die Liste der Benutzer, die dieser Gruppe zugewiesen sind, anzuzeigen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Überprüfen der Mitgliedschaftskriterien einer dynamischen Gruppe 

Um die Mitgliedschaftskriterien einer dynamischen Gruppe zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Gruppen**.

6.  **Suchen** Sie nach der gewünschten Gruppe, und **klicken Sie auf die Zeile**, um sie auszuwählen.

7.  Klicken Sie auf **Dynamische Mitgliedschaftsregeln**.

8.  Überprüfen Sie die **einfache** oder **erweiterte** definierte Regel für diese Gruppe, und stellen Sie sicher, dass der Benutzer, der Mitglied dieser Gruppe werden soll, diese Kriterien erfüllt.

### <a name="check-a-groups-assigned-licenses"></a>Überprüfen der zugewiesenen Lizenzen einer Gruppe

Um die einer Gruppe zugewiesenen Lizenzen zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Gruppen**.

6.  **Suchen** Sie nach der gewünschten Gruppe, und **klicken Sie auf die Zeile**, um sie auszuwählen.

7.  Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen der Gruppe derzeit zugewiesen sind.

### <a name="reprocess-a-groups-licenses"></a>Erneutes Verarbeiten der Lizenzen einer Gruppe

Um die einer Gruppe zugewiesenen Lizenzen erneut zu verarbeiten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5. Klicken Sie auf **Alle Gruppen**.

6. **Suchen** Sie nach der gewünschten Gruppe, und **klicken Sie auf die Zeile**, um sie auszuwählen.

7. Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen der Gruppe derzeit zugewiesen sind.

8. Klicken Sie auf die Schaltfläche **Erneut verarbeiten**, um sicherzustellen, dass die den Mitgliedern dieser Gruppe zugewiesenen Lizenzen auf dem neuesten Stand sind. Dies kann je nach der Größe und Komplexität der Gruppe sehr lange dauern.

   >[!NOTE]
   >Um die Zeit zu verkürzen, sollten Sie in Erwägung ziehen, dem Benutzer vorübergehend direkt eine Lizenz zuzuweisen. [Weisen Sie einem Benutzer eine Lizenz zu.](#problems-with-application-consent)
   >
   >

### <a name="assign-a-group-a-license"></a>Zuweisen von Lizenzen zu einer Gruppe

Um einer Gruppe eine Lizenz zuzuweisen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5. Klicken Sie auf **Alle Gruppen**.

6. **Suchen** Sie nach der gewünschten Gruppe, und **klicken Sie auf die Zeile**, um sie auszuwählen.

7. Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen der Gruppe derzeit zugewiesen sind.

8. Klicken Sie auf die Schaltfläche **Zuweisen**.

9. Wählen Sie in der Liste der verfügbaren Produkte **ein oder mehrere Produkte** aus.

10. **Optional:** Klicken Sie auf die Option für **Zuweisungsoptionen**, um Produkte differenziert zuzuweisen. Klicken Sie auf **OK**, wenn Sie fertig sind.

11. Klicken Sie auf die Schaltfläche **Zuweisen**, um der Gruppe diese Lizenzen zuzuweisen. Dies kann je nach der Größe und Komplexität der Gruppe sehr lange dauern.

    >[!NOTE]
    >Um die Zeit zu verkürzen, sollten Sie in Erwägung ziehen, dem Benutzer vorübergehend direkt eine Lizenz zuzuweisen. [Weisen Sie einem Benutzer eine Lizenz zu.](#problems-with-application-consent)
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Probleme mit Richtlinien für bedingten Zugriff

### <a name="check-a-specific-conditional-access-policy"></a>Überprüfen einer bestimmten Richtlinie für bedingten Zugriff

So überprüfen oder validieren Sie eine einzelne Richtlinie für bedingten Zugriff

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf das Navigationselement **Bedingter Zugriff**.

6. Klicken Sie auf die Richtlinie, die Sie untersuchen möchten.

7. Vergewissern Sie sich, dass keine spezifischen Bedingungen, Zuweisungen oder anderen Einstellungen den Benutzerzugriff blockieren.

   >[!NOTE]
   >Möglicherweise möchten Sie diese Richtlinie vorübergehend deaktivieren, um Auswirkungen auf Anmeldungen auszuschließen. Legen Sie hierzu den Umschalter **Richtlinie aktivieren** auf **Nein** fest, und klicken Sie auf die Schaltfläche **Speichern**.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Überprüfen der Richtlinie für bedingten Zugriff einer bestimmten Anwendung

So überprüfen oder validieren Sie die derzeit konfigurierte Richtlinie für bedingten Zugriff einer einzelnen Anwendung

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie auf **Alle Anwendungen**.

6.  Suchen Sie anhand des Anwendungsanzeigenamens oder der Anwendungs-ID nach der Anwendung, die Sie interessiert oder bei der sich der Benutzer anmelden möchte.

     >[!NOTE]
     >Wenn Sie die gewünschte Anwendung nicht sehen, klicken Sie auf die Schaltfläche **Filter**, und erweitern Sie den Bereich der Liste auf **Alle Anwendungen**. Wenn Sie weitere Spalten anzeigen möchten, klicken Sie auf die Schaltfläche **Spalten**, um weitere Details für Ihre Anwendungen hinzuzufügen.
     >
     >

7.  Klicken Sie auf das Navigationselement **Bedingter Zugriff**.

8.  Klicken Sie auf die Richtlinie, die Sie untersuchen möchten.

9.  Vergewissern Sie sich, dass keine spezifischen Bedingungen, Zuweisungen oder anderen Einstellungen den Benutzerzugriff blockieren können.

     >[!NOTE]
     >Möglicherweise möchten Sie diese Richtlinie vorübergehend deaktivieren, um Auswirkungen auf Anmeldungen auszuschließen. Legen Sie hierzu den Umschalter **Richtlinie aktivieren** auf **Nein** fest, und klicken Sie auf die Schaltfläche **Speichern**.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Deaktivieren einer bestimmten Richtlinie für bedingten Zugriff

So überprüfen oder validieren Sie eine einzelne Richtlinie für bedingten Zugriff

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie auf das Navigationselement **Bedingter Zugriff**.

6.  Klicken Sie auf die Richtlinie, die Sie untersuchen möchten.

7.  Deaktivieren Sie die Richtlinie, indem Sie den Umschalter **Richtlinie aktivieren** auf **Nein** festlegen, und klicken Sie auf die Schaltfläche **Speichern**.

## <a name="problems-with-application-consent"></a>Probleme mit der Anwendungszustimmung

Der Anwendungszugriff kann blockiert werden, wenn kein ordnungsgemäßer Berechtigungszustimmungsvorgang stattgefunden hat. Nachfolgend finden Sie einige Möglichkeiten zum Lösen von Problemen im Zusammenhang mit Anwendungsgenehmigungen:

-   [Durchführen einer Zustimmung auf Benutzerebene](#perform-a-user-level-consent-operation)

-   [Durchführen einer Zustimmung auf Administratorebene für alle Anwendungen](#perform-administrator-level-consent-operation-for-any-application)

-   [Durchführen einer Zustimmung auf Administratorebene für eine Anwendung mit einem Mandanten](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Durchführen einer Zustimmung auf Administratorebene für eine mehrinstanzenfähige Anwendung](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Durchführen einer Zustimmung auf Benutzerebene

-   Für jede Anwendung, die für Open ID Connect aktiviert ist und Berechtigungen anfordert, wird beim Navigieren zum Anmeldebildschirm für die Anwendung eine Zustimmung auf Benutzerebene für den angemeldeten Benutzer bei dieser Anmeldung ausgeführt.

-   Wenn Sie dies programmgesteuert erledigen möchten, finden Sie weitere Informationen unter [Anfordern der Zustimmung einzelner Benutzer](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Durchführen einer Zustimmung auf Administratorebene für alle Anwendungen

-   Für **ausschließlich Anwendungen, die mit der ersten Version des Anwendungsmodells entwickelt wurden**, können Sie diese Zustimmung auf Administratorebene erzwingen, indem Sie am Ende der Anmelde-URL einer Anwendung die Zeichenfolge **?prompt=admin\_consent** anfügen.

-   Für **Anwendungen, die mit der zweiten Version des Anwendungsmodells entwickelt wurden**, können Sie diese Zustimmung auf Administratorebene erzwingen, indem Sie die Anweisungen im Abschnitt **Anfordern der Berechtigungen von einem Verzeichnisadministrator** von [Verwenden des Endpunkts für die Administratorzustimmung](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) befolgen.

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Durchführen einer Zustimmung auf Administratorebene für eine Anwendung mit einem Mandanten

-   Für **Anwendungen mit einem Mandanten**, die Berechtigungen anfordern (z.B. Anwendungen, die Sie selbst entwickeln oder deren Besitzer in Ihrer Organisation Sie sind), können Sie eine **Zustimmung auf Administratorebene** im Namen aller Benutzer durchführen, indem Sie sich als globaler Administrator anmelden und oben im Bereich **Anwendungsregistrierung &gt; Alle Anwendungen &gt; App auswählen &gt; Erforderliche Berechtigungen** auf die Schaltfläche **Berechtigungen erteilen** klicken.

-   Für **Anwendungen, die mit der ersten oder zweiten Version des Anwendungsmodells entwickelt wurden**, können Sie diese Zustimmung auf Administratorebene erzwingen, indem Sie die Anweisungen im Abschnitt **Anfordern der Berechtigungen von einem Verzeichnisadministrator** von [Verwenden des Endpunkts für die Administratorzustimmung](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) befolgen.

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Durchführen einer Zustimmung auf Administratorebene für eine mehrinstanzenfähige Anwendung

-   Für **mehrinstanzenfähige Anwendungen**, die Berechtigungen anfordern (z.B. von Drittanbietern oder von Microsoft entwickelte Anwendungen), können Sie eine **Zustimmung auf Administratorebene** durchführen. Melden Sie sich als globaler Administrator an, und klicken Sie auf die Schaltfläche **Berechtigungen erteilen** im Bereich **Unternehmensanwendungen &gt; Alle Anwendungen &gt; App auswählen &gt; Berechtigungen** (demnächst verfügbar).

-   Sie können Sie diese Zustimmung auf Administratorebene auch erzwingen, indem Sie die Anweisungen im Abschnitt **Anfordern der Berechtigungen von einem Verzeichnisadministrator** von [Verwenden des Endpunkts für die Administratorzustimmung](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) befolgen.

## <a name="next-steps"></a>Nächste Schritte
[Verwenden des Endpunkts für die Administratorzustimmung](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)