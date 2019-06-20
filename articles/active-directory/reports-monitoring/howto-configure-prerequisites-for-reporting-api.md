---
title: Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API | Microsoft-Dokumentation
description: Erfahren Sie, welche Voraussetzungen für den Zugriff auf die Azure AD-Berichterstellungs-API gelten.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab30dfeccc4ae1c7b09a8f48846ffb9e71cfc23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60439093"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API

Die [Berichtserstellungs-APIs von Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) bieten Ihnen über eine Gruppe von REST-basierten APIs programmgesteuerten Zugriff auf die Daten. Sie können diese APIs über verschiedene Programmiersprachen und Tools aufrufen.

Die Berichterstellungs-API verwendet [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) zum Autorisieren des Zugriffs auf die Web-APIs.

Um auf die Berichterstellungs-API zugreifen zu können, müssen Sie folgende Schritte ausführen:

1. [Zuweisen von Rollen](#assign-roles)
2. [Registrieren einer Anwendung](#register-an-application)
3. [Erteilen von Berechtigungen](#grant-permissions)
4. [Erfassen von Konfigurationseinstellungen](#gather-configuration-settings)

## <a name="assign-roles"></a>Zuweisen von Rollen

Um mithilfe der API auf die Berichtsdaten zuzugreifen, müssen Ihnen eine der folgenden Rollen zugewiesen sein:

- Sicherheitsleseberechtigter

- Sicherheitsadministrator

- Globaler Administrator


## <a name="register-an-application"></a>Registrieren einer Anwendung

Sie müssen Anwendungen auch dann registrieren, wenn Sie mithilfe eines Skripts auf die Berichterstellungs-API zugreifen. Hierdurch wird Ihnen eine **Anwendungs-ID** zugewiesen, die für die Autorisierungsaufrufe erforderlich ist und den Empfang von Token über Ihren Code ermöglicht.

Um Ihr Verzeichnis für den Zugriff auf die Azure AD-Berichterstellungs-API zu konfigurieren, müssen Sie sich beim [Azure-Portal](https://portal.azure.com) mit dem Konto eines Azure-Administrators anmelden, das auch der Verzeichnisrolle **Globaler Administrator** Ihres Azure AD-Mandanten angehört.

> [!IMPORTANT]
> Anwendungen, die unter Anmeldeinformationen mit Administratorberechtigungen ausgeführt werden, können äußerst einflussreich sein. Sorgen Sie also unbedingt dafür, dass die ID und geheimen Anmeldeinformationen der Anwendung an einem sicheren Ort aufbewahrt werden.
> 

**Registrieren Sie eine Azure AD-Anwendung wie folgt:**

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory**.
   
    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wählen Sie auf der Seite **Azure Active Directory** die Option **App-Registrierungen**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Wählen Sie auf der Seite **App-Registrierungen** die Option **Registrierung einer neuen Anwendung**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Führen Sie auf der Seite **Erstellen** die folgenden Schritte aus:

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Geben Sie im Textfeld **Name** Folgendes ein: `Reporting API application`.

    b. Wählen Sie als **Anwendungstyp** **Web-App/API** aus.

    c. Geben Sie im Textfeld **Anmelde-URL** die URL `https://localhost` ein.

    d. Klicken Sie auf **Erstellen**. 


## <a name="grant-permissions"></a>Erteilen von Berechtigungen 

Abhängig von der API, auf die Sie zugreifen möchten, müssen Sie Ihrer App die folgenden Berechtigungen erteilen:  

| API | Berechtigung |
| --- | --- |
| Microsoft Azure Active Directory | Verzeichnisdaten lesen |
| Microsoft Graph | Alle Überwachungsprotokolldaten lesen |


![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Im folgenden Abschnitt werden die Schritte für beide APIs aufgelistet. Wenn Sie nicht auf eine der APIs zugreifen möchten, können Sie die zugehörigen Schritte überspringen.

**So erteilen Sie Ihrer Anwendung die Berechtigung zur Verwendung der APIs**:

1. Wählen Sie Ihre Anwendung auf der Seite **App-Registrierungen** aus, und wählen Sie dann die Option **Einstellungen**. 

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Klicken Sie auf der Seite **Einstellungen** auf **Erforderliche Berechtigungen**. 

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Klicken Sie auf der Seite **Erforderliche Berechtigungen** in der **API**-Liste auf **Microsoft Azure Active Directory**. 

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Wählen Sie auf der Seite **Zugriff aktivieren** die Option **Verzeichnisdaten lesen** aus, und deaktivieren Sie **Anmelden und Benutzerprofil lesen**. 

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Klicken Sie auf der Symbolleiste am oberen Rand auf **Speichern**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Klicken Sie auf der Seite **Erforderliche Berechtigungen** auf der Symbolleiste am oberen Rand auf **Hinzufügen**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Klicken Sie auf der Seite **API-Zugriff hinzufügen** auf **API auswählen**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Klicken Sie auf der Seite **API auswählen** auf **Microsoft Graph** und dann auf **Auswählen**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. Wählen Sie auf der Seite **Zugriff aktivieren** die Option **Alle Überwachungsprotokolldaten lesen** aus, und klicken Sie dann auf **Auswählen**.  

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Klicken Sie auf der Seite **API-Zugriff hinzufügen** auf **Fertig**.  

11. Klicken Sie auf der Seite **Erforderliche Berechtigungen** auf der Symbolleiste am oberen Rand auf **Berechtigungen erteilen** und dann auf **Ja**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Erfassen von Konfigurationseinstellungen 

In diesem Abschnitt wird gezeigt, wie Sie die folgenden Einstellungen aus Ihrem Verzeichnis abrufen:

- Domänenname
- Client-ID
- Geheimer Clientschlüssel

Sie benötigen diese Werte, um Aufrufe an die Berichterstellungs-API zu konfigurieren. 

### <a name="get-your-domain-name"></a>Ermitteln des Domänennamens

**So ermitteln Sie den Domänennamen:**

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory**.
   
    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wählen Sie auf der Seite **Azure Active Directory** die Option **Benutzerdefinierte Domänennamen**.

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopieren Sie Ihren Domänennamen aus der Liste der Domänen.


### <a name="get-your-applications-client-id"></a>Abrufen der Client-ID der Anwendung

**So rufen Sie die Client-ID der Anwendung ab:**

1. Klicken Sie im linken Navigationsbereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**.
   
    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wählen Sie Ihre Anwendung auf der Seite **App-Registrierungen** aus.

3. Navigieren Sie auf der Anwendungsseite zu **Anwendungs-ID**, und wählen Sie die Option **Click to copy** (Zum Kopieren klicken).

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Abrufen des geheimen Clientschlüssels der Anwendung
Um den geheimen Clientschlüssel Ihrer Anwendung abzurufen, müssen Sie einen neuen Schlüssel erstellen und dessen Wert beim Speichern des neuen Schlüssels speichern, weil dieser Wert später nicht mehr abgerufen werden kann.

**So rufen Sie den geheimen Clientschlüssel der Anwendung ab:**

1. Klicken Sie im linken Navigationsbereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**.
   
    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Wählen Sie Ihre Anwendung auf der Seite **App-Registrierungen** aus.

3. Wählen Sie auf der Anwendungsseite oben in der Symbolleiste die Option **Einstellungen**. 

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Klicken Sie auf der Seite **Einstellungen** im Abschnitt **API-Zugriff** auf **Schlüssel**. 

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Führen Sie auf der Seite **Schlüssel** die folgenden Schritte durch:

    ![Register application (Anwendung registrieren)](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. Geben Sie im Textfeld **Beschreibung** Folgendes ein: `Reporting API`.

    b. Wählen Sie für **Läuft ab** die Option **In 2 Jahren** aus.

    c. Klicken Sie auf **Speichern**.

    d. Kopieren Sie den Schlüsselwert.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Beheben von Fehlern in der Berichterstellungs-API

In diesem Abschnitt werden die häufigsten Fehlermeldungen aufgeführt, die beim Zugreifen auf Aktivitätsberichte über die Microsoft Graph-API auftreten können, sowie Schritte zu deren Behebung.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP: Interner Serverfehler beim Zugriff auf den Microsoft Graph V2-Endpunkt.

Der Microsoft Graph v2-Endpunkt wird zurzeit nicht unterstützt. Stellen Sie sicher, dass Sie auf die Aktivitätsprotokolle mit dem Microsoft Graph v1-Endpunkt zugreifen.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Fehler Fehler beim Abrufen von Benutzerrollen aus AD Graph

Sie erhalten diese Fehlermeldung ggf. beim Versuch, auf Anmeldungen mithilfe des Graph-Explorers zuzugreifen. Stellen Sie sicher, dass Sie mit Ihrem Konto über beide Anmeldeschaltflächen in der Benutzeroberfläche des Graph-Explorers angemeldet sind, wie in der folgenden Abbildung gezeigt. 

![Graph-Tester](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Fehler Fehler beim Überprüfen der Premium-Lizenz aus AD Graph 

Wenn Sie diese Fehlermeldung beim Versuch erhalten, auf Anmeldungen mithilfe des Graph-Explorers zuzugreifen, wählen Sie **Berechtigungen ändern** unter Ihrem Konto im linken Navigationsbereich aus, und wählen Sie dann **Tasks.ReadWrite** und **Directory.Read.All** aus. 

![Benutzeroberfläche zum Ändern von Berechtigungen](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fehler Kein Mandant ist B2C, oder der Mandant besitzt keine Premium-Lizenz

Für den Zugriff auf Anmeldeberichte ist eine Azure Active Directory Premium 1-Lizenz (P1) erforderlich. Wenn diese Fehlermeldung beim Zugriff auf Anmeldungen angezeigt wird, stellen Sie sicher, dass Ihr Mandant mit einer Azure AD P1-Lizenz lizenziert ist.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fehler Der Benutzer gehört nicht den zulässigen Rollen an 

Wenn Sie diese Fehlermeldung sehen, während Sie versuchen, über die API auf Überwachungsprotokolle oder Anmeldungen zuzugreifen, stellen Sie sicher, dass Ihr Konto Teil der Rolle **Sicherheitsleseberechtigter** oder **Berichtsleser** in Ihrem Azure Active Directory-Mandanten ist. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fehler Der Anwendung fehlt die AAD-Berechtigung „Verzeichnisdaten lesen“ 

Führen Sie die Schritte unter [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md) aus, um sicherzustellen, dass die Anwendung mit dem richtigen Berechtigungssatz ausgeführt wird. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Fehler Der Anwendung fehlt die Berechtigung „Alle Überwachungsprotokolldaten lesen“ der MSGraph-API

Führen Sie die Schritte unter [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md) aus, um sicherzustellen, dass die Anwendung mit dem richtigen Berechtigungssatz ausgeführt wird. 

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen von Daten per Berichtserstellungs-API von Azure Active Directory mit Zertifikaten](tutorial-access-api-with-certificates.md)
* [Referenz zur Überwachungs-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referenz zur Anmeldeaktivitätsbericht-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
