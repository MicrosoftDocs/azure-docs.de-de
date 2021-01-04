---
title: Voraussetzungen für die Azure Active Directory-Berichterstellungs-API
description: Erfahren Sie, welche Voraussetzungen für den Zugriff auf die Azure AD-Berichterstellungs-API gelten.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca618dc234ff4383083ff5eb21299fb1e3e7128
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348737"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API

Die [Berichtserstellungs-APIs von Azure Active Directory (Azure AD)](./concept-reporting-api.md) bieten Ihnen über eine Gruppe von REST-basierten APIs programmgesteuerten Zugriff auf die Daten. Sie können diese APIs über Programmiersprachen und Tools aufrufen.

Die Berichterstellungs-API verwendet [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) zum Autorisieren des Zugriffs auf die Web-APIs.

Um auf die Berichterstellungs-API zugreifen zu können, müssen Sie folgende Schritte ausführen:

1. [Zuweisen von Rollen](#assign-roles)
2. [Lizenzanforderungen](#license-requirements)
3. [Registrieren einer Anwendung](#register-an-application)
4. [Erteilen von Berechtigungen](#grant-permissions)
5. [Erfassen von Konfigurationseinstellungen](#gather-configuration-settings)

## <a name="assign-roles"></a>Zuweisen von Rollen

Um mithilfe der API auf die Berichtsdaten zuzugreifen, müssen Ihnen eine der folgenden Rollen zugewiesen sein:

- Sicherheitsleseberechtigter

- Sicherheitsadministrator

- Globaler Administrator

## <a name="license-requirements"></a>Lizenzanforderungen

Um auf die Anmeldungsberichte eines Mandanten zugreifen zu können, muss einem Azure AD-Mandanten eine Azure AD Premium-Lizenz zugeordnet sein. Eine Azure AD Premium-Lizenz ab P1 ist erforderlich, um auf die Anmeldeberichte beliebiger Azure AD-Mandanten zugreifen zu können. Alternativ dazu ist der Zugriff auf die Anmeldeberichte über die API ohne zusätzliche Lizenzanforderungen möglich, wenn der Verzeichnistyp „Azure AD B2C“ lautet. 


## <a name="register-an-application"></a>Registrieren einer Anwendung

Eine Registrierung ist auch dann erforderlich, wenn Sie mithilfe eines Skripts auf die Berichterstellungs-API zugreifen. Bei der Registrierung wird Ihnen eine **Anwendungs-ID** zugewiesen, die für die Autorisierungsaufrufe erforderlich ist und den Empfang von Token über Ihren Code ermöglicht.

Um Ihr Verzeichnis für den Zugriff auf die Azure AD-Berichterstellungs-API zu konfigurieren, müssen Sie sich beim [Azure-Portal](https://portal.azure.com) mit dem Konto eines Azure-Administrators anmelden, das auch der Verzeichnisrolle **Globaler Administrator** Ihres Azure AD-Mandanten angehört.

> [!IMPORTANT]
> Anwendungen, die unter Anmeldeinformationen mit Administratorberechtigungen ausgeführt werden, können äußerst einflussreich sein. Sorgen Sie also unbedingt dafür, dass die ID und geheimen Anmeldeinformationen der Anwendung an einem sicheren Ort aufbewahrt werden.
> 

**Registrieren Sie eine Azure AD-Anwendung wie folgt:**

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory**.
   
    ![Screenshot: Azure-Portalmenü mit ausgewählter Option „Azure Active Directory“](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wählen Sie auf der Seite **Azure Active Directory** die Option **App-Registrierungen**.

    ![Screenshot: Menü „Verwalten“ mit ausgewählter Option „App-Registrierungen“](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Wählen Sie auf der Seite **App-Registrierungen** die Option **Registrierung einer neuen Anwendung** aus.

    ![Screenshot: Ausgewählte Option „Neue Registrierung“](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Die Seite **Anwendung registrieren**:

    ![Screenshot: Seite „Anwendung registrieren“, auf der Sie die Werte in diesem Schritt eingeben können](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Geben Sie im Textfeld **Name** Folgendes ein: `Reporting API application`.

    b. Wählen Sie für **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.

    c. Wählen Sie unter **Umleitungs-URL** das Textfeld **Web** aus, und geben Sie `https://localhost` ein.

    d. Wählen Sie **Registrieren**. 


## <a name="grant-permissions"></a>Erteilen von Berechtigungen 

Abhängig von der API, auf die Sie zugreifen möchten, müssen Sie Ihrer App die folgenden Berechtigungen erteilen:  

| API | Berechtigung |
| --- | --- |
| Microsoft Azure Active Directory | Verzeichnisdaten lesen |
| Microsoft Graph | Alle Überwachungsprotokolldaten lesen |

![Screenshot: Zeigt, wo Sie im Bereich „API-Berechtigungen“ die Option „Berechtigung hinzufügen“ auswählen können](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Im folgenden Abschnitt werden die Schritte für beide APIs aufgelistet. Wenn Sie nicht auf eine der APIs zugreifen möchten, können Sie die zugehörigen Schritte überspringen.

**So erteilen Sie Ihrer Anwendung die Berechtigung zur Verwendung der APIs**:


1. Wählen Sie **API-Berechtigungen** und dann **Berechtigung hinzufügen** aus. 

    ![Screenshot: Seite „API-Berechtigungen“, auf der Sie „Berechtigung hinzufügen“ auswählen können](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Suchen Sie auf der Seite **API-Berechtigungen anfordern** unter **Unterstützte Legacy-APIs** nach **Azure Active Directory Graph**. 

    ![Screenshot: Seite „API-Berechtigungen anfordern“, auf der Sie „Azure Active Directory Graph“ auswählen können](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Wählen Sie auf der Seite **Erforderliche Berechtigungen** die Option **Anwendungsberechtigungen** aus, erweitern Sie **Verzeichnis**, und aktivieren Sie dann das Kontrollkästchen **Directory.ReadAll**.  Wählen Sie **Berechtigungen hinzufügen** aus.

    ![Screenshot: Seite „API-Berechtigungen anfordern“, auf der Sie Anwendungsberechtigungen auswählen können](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Wählen Sie auf der Seite **Berichterstellungs-API-Anwendung – API-Berechtigungen** die Option **Administratoreinwilligung erteilen** aus. 

    ![Screenshot: Seite „Berichterstellungs-API-Anwendung – API-Berechtigungen“, auf der Sie „Administratoreinwilligung erteilen“ auswählen können](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Hinweis: **Microsoft Graph** wird bei der API-Registrierung standardmäßig hinzugefügt.

    ![Screenshot: Seite „API-Berechtigungen“, auf der Sie „Berechtigung hinzufügen“ auswählen können](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Erfassen von Konfigurationseinstellungen 

In diesem Abschnitt wird gezeigt, wie Sie die folgenden Einstellungen aus Ihrem Verzeichnis abrufen:

- Domänenname
- Client-ID
- Geheimer Clientschlüssel

Sie benötigen diese Werte, um Aufrufe an die Berichterstellungs-API zu konfigurieren. 

### <a name="get-your-domain-name"></a>Ermitteln des Domänennamens

**So ermitteln Sie den Domänennamen:**

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory**.
   
    ![Screenshot: Azure-Portalmenü mit ausgewählter Option „Azure Active Directory“](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wählen Sie auf der Seite **Azure Active Directory** die Option **Benutzerdefinierte Domänennamen**.

    ![Screenshot: Ausgewählte Option „Benutzerdefinierte Domänennamen“ auf der Seite „Azure Active Directory“](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopieren Sie Ihren Domänennamen aus der Liste der Domänen.


### <a name="get-your-applications-client-id"></a>Abrufen der Client-ID der Anwendung

**So rufen Sie die Client-ID der Anwendung ab:**

1. Klicken Sie im linken Navigationsbereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**.
   
    ![Screenshot: Azure-Portalmenü mit ausgewählter Option „Azure Active Directory“](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wählen Sie Ihre Anwendung auf der Seite **App-Registrierungen** aus.

3. Navigieren Sie auf der Anwendungsseite zu **Anwendungs-ID**, und wählen Sie die Option **Click to copy** (Zum Kopieren klicken).

    ![Screenshot: Seite „Berichterstellungs-API-Anwendung“, auf der Sie die Anwendung-ID kopieren können](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Abrufen des geheimen Clientschlüssels der Anwendung
 Vermeiden Sie Fehler beim Zugreifen auf Überwachungsprotokolle oder beim Anmelden mithilfe der API.

**So rufen Sie den geheimen Clientschlüssel der Anwendung ab:**

1. Klicken Sie im linken Navigationsbereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**.
   
    ![Screenshot: Azure-Portalmenü mit ausgewählter Option „Azure Active Directory“](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Wählen Sie Ihre Anwendung auf der Seite **App-Registrierungen** aus.

3.  Wählen Sie auf der Seite **API-Anwendung** die Option **Zertifikate und Geheimnisse** aus, und klicken Sie im Abschnitt **Geheime Clientschlüssel** auf **+ Neuer geheimer Clientschlüssel**. 

    ![Screenshot: Seite „Zertifikate und Geheimnisse“, auf der Sie einen geheimen Clientschlüssel hinzufügen können](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Führen Sie auf der Seite **Geheimen Clientschlüssel hinzufügen** die folgenden Aktionen aus:

    a. Geben Sie im Textfeld **Beschreibung** Folgendes ein: `Reporting API`.

    b. Wählen Sie für **Läuft ab** die Option **In 2 Jahren** aus.

    c. Klicken Sie auf **Speichern**.

    d. Kopieren Sie den Schlüsselwert.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Beheben von Fehlern in der Berichterstellungs-API

In diesem Abschnitt werden die häufigsten Fehlermeldungen, die beim Zugreifen auf Aktivitätsberichte über die Microsoft Graph-API auftreten können, sowie Schritte zu deren Behebung aufgeführt.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Error: Fehler beim Abrufen von Benutzerrollen aus Microsoft Graph

 Melden Sie sich auf der Graph-Tester-Benutzeroberfläche über beide Anmeldeschaltflächen bei Ihrem Konto an, um zu vermeiden, dass bei der Anmeldung mit dem Graph-Tester ein Fehler ausgegeben wird. 

![Graph-Tester](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Error: Fehler beim Überprüfen der Premium-Lizenz aus Microsoft Graph 

Wenn Sie diese Fehlermeldung beim Versuch erhalten, auf Anmeldungen mithilfe des Graph-Explorers zuzugreifen, wählen Sie **Berechtigungen ändern** unter Ihrem Konto im linken Navigationsbereich aus, und wählen Sie dann **Tasks.ReadWrite** und **Directory.Read.All** aus. 

![Benutzeroberfläche zum Ändern von Berechtigungen](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Error: Der Mandant ist nicht B2C, oder der Mandant besitzt keine Premium-Lizenz

Für den Zugriff auf Anmeldeberichte ist eine Azure Active Directory Premium 1-Lizenz (P1) erforderlich. Wenn diese Fehlermeldung beim Zugriff auf Anmeldungen angezeigt wird, stellen Sie sicher, dass Ihr Mandant mit einer Azure AD P1-Lizenz lizenziert ist.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Error: Die zulässigen Rollen enthalten nicht „Benutzer“. 

 Vermeiden Sie Fehler beim Zugreifen auf Überwachungsprotokolle oder beim Anmelden mithilfe der API. Stellen Sie sicher, dass Ihr Konto zu der Rolle **Sicherheitsleseberechtigter** oder **Berichtsleser** in Ihrem Azure Active Directory-Mandanten gehört.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error: Der Anwendung fehlt die AAD-Berechtigung „Verzeichnisdaten lesen“ 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Error: Der Anwendung fehlt die Berechtigung „Alle Überwachungsprotokolldaten lesen“ der Microsoft Graph-API

Führen Sie die unter [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md) aufgeführten Schritte aus, um sicherzustellen, dass Ihre Anwendung mit dem richtigen Berechtigungssatz ausgeführt wird. 

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen von Daten per Berichtserstellungs-API von Azure Active Directory mit Zertifikaten](tutorial-access-api-with-certificates.md)
* [Referenz zur Überwachungs-API](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Referenz zur Anmeldeaktivitätsbericht-API](/graph/api/resources/signin?view=graph-rest-beta)