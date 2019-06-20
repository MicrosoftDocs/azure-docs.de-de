---
title: Erste Schritte mit der Azure AD-Authentifizierung mithilfe des Azure-Portals| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Azure-Portal für den Zugriff auf die Azure Active Directory (Azure AD)-Authentifizierung verwenden, um die Azure Media Services-API zu nutzen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: b7962f42b4244121a67b88ef3bf789ce40f7b1e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719619"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Erste Schritte mit der Azure AD-Authentifizierung mithilfe des Azure-Portals

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Erfahren Sie, wie Sie das Azure-Portal für den Zugriff auf die Azure Active Directory (Azure AD)-Authentifizierung verwenden, um auf die Azure Media Services-API zuzugreifen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Beginnen Sie mit einer [kostenlosen Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/), falls Sie kein Konto haben. 
- Media Services-Konto. Weitere Informationen finden Sie unter [Erstellen eines Azure Media Services-Kontos mithilfe des Azure-Portals](media-services-portal-create-account.md).
- Lesen Sie zudem auch die Übersicht [Access the Azure Media Services API with Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md) (Zugriff auf die Azure Media Services-API mit Azure AD-Authentifizierung). 

Wenn Sie die Azure AD-Authentifizierung mit Azure Media Services nutzen, stehen Ihnen zwei Authentifizierungsoptionen zur Verfügung:

- **Benutzerauthentifizierung**. Dient zum Authentifizieren einer Person, die die App für die Interaktion mit Media Services-Ressourcen verwendet. Die interaktive Anwendung soll den Benutzer zuerst zur Eingabe seiner Anmeldeinformationen auffordern. Ein Beispiel hierfür ist eine Verwaltungskonsolen-App, die von autorisierten Benutzern zum Überwachen von Codierungsaufträgen oder Livestreaming verwendet wird. 
- **Dienstprinzipalauthentifizierung**. Dient zum Authentifizieren eines Diensts. Anwendungen, für die diese Authentifizierungsmethode normalerweise verwendet wird, sind Apps, die Daemondienste, Dienste der mittleren Ebene oder geplante Aufträge ausführen: Web-Apps, Funktions-Apps, Logic Apps, APIs oder Microservices.

> [!IMPORTANT]
> Für Media Services wird derzeit das Azure Access Control-Dienstauthentifizierungsmodell unterstützt. Die Access Control-Authentifizierung gilt aber ab dem 1. Juni 2018 als veraltet. Es wird empfohlen, möglichst bald zum Azure AD-Authentifizierungsmodell zu migrieren.

## <a name="select-the-authentication-method"></a>Auswählen der Authentifizierungsmethode

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Media Services-Konto aus.
2. Wählen Sie, wie Sie eine Verbindung mit der Media Services-API herstellen möchten.

    ![Seite zum Auswählen der Verbindungsmethode](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Benutzerauthentifizierung

Zum Herstellen einer Verbindung mit der Media Services-API mithilfe der Benutzerauthentifizierung muss die Client-App ein Azure AD-Token mit den folgenden Parametern anfordern:  

* Azure AD-Mandantenendpunkt
* Media Services-Ressourcen-URI
* Client-ID für (native) Media Services-Anwendungen 
* Umleitungs-URI für (native) Media Services-Anwendungen 
* Ressourcen-URI für REST Media Services

Die Werte für diese Parameter können Sie auf der Seite **Verbindung mit Media Services-API über Benutzerauthentifizierung herstellen** abrufen. 

![Seite zum Herstellen einer Verbindung über die Benutzerauthentifizierung](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Wenn Sie mithilfe des Microsoft .NET SDK für Media Services eine Verbindung mit der Media Services-API herstellen, sind die erforderlichen Werte als Teil des SDK verfügbar. Weitere Informationen finden Sie unter [Use Azure AD authentication to access the Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Zugreifen auf die Azure Media Services-API mit .NET mithilfe der Azure AD-Authentifizierung).

Wenn Sie das Media Services-.NET-Client-SDK nicht verwenden, müssen Sie manuell eine Azure AD-Tokenanforderung erstellen, indem Sie die zuvor beschriebenen Parameter verwenden. Weitere Informationen finden Sie unter [Azure Active Directory-Authentifizierungsbibliotheken](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

Zum Herstellen einer Verbindung mit der Media Services-API mithilfe des Dienstprinzipals muss Ihre Anwendung der mittleren Ebene (Web-API oder Webanwendung) ein Azure AD-Token mit den folgenden Parametern anfordern:  

* Azure AD-Mandantenendpunkt
* Media Services-Ressourcen-URI 
* Ressourcen-URI für REST Media Services
* Werte der Azure AD-Anwendung: **Client-ID** und **geheimer Clientschlüssel**

Die Werte für diese Parameter können Sie auf der Seite **Verbindung mit Media Services-API über Dienstprinzipal herstellen** abrufen. Mithilfe dieser Seite können Sie eine neue Azure AD-Anwendung erstellen oder eine vorhandene Anwendung auswählen. Wenn Sie die Azure AD-App ausgewählt haben, können Sie die Client-ID (Anwendungs-ID) abrufen und die Werte für den geheimen Clientschlüssel (Schlüssel) erstellen. 

![Seite zum Herstellen einer Verbindung mit dem Dienstprinzipal](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Wenn sich das Blatt **Dienstprinzipal** öffnet, wird die erste Azure AD-Anwendung ausgewählt, die die folgenden Kriterien erfüllt:

- Sie ist eine registrierte Azure AD-Anwendung.
- Sie hat rollenbasierte Zugriffsberechtigungen als Mitwirkender oder Besitzer für das Konto.

Nachdem Sie eine Azure AD-App erstellt oder ausgewählt haben, können Sie einen geheimen Clientschlüssel (Schlüssel) und die Cient-ID (Anwendungs-ID) erstellen und kopieren. Der geheime Clientschlüssel und die Client-ID sind erforderlich, um das Zugriffstoken in diesem Szenario abzurufen.

Wenn Sie über keine Berechtigungen zum Erstellen von Azure AD-Apps in Ihrer Domäne verfügen, werden die Azure AD-App-Steuerelemente des Blatts nicht angezeigt, und eine Warnmeldung erscheint.

Weitere Informationen zum Herstellen einer Verbindung mit der Media Services-API mithilfe des Media Services .NET SDK finden Sie unter [Use Azure AD authentication to access Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Zugreifen auf die Azure Media Services-API mit .NET mithilfe der Azure AD-Authentifizierung).

Wenn Sie das Media Services-.NET-Client-SDK nicht verwenden, müssen Sie manuell eine Azure AD-Tokenanforderung erstellen, indem Sie die zuvor beschriebenen Parameter verwenden. Weitere Informationen finden Sie unter [Azure Active Directory-Authentifizierungsbibliotheken](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Abrufen der Client-ID und des geheimen Clientschlüssels

Wenn Sie eine vorhandene Azure AD-App ausgewählt oder eine neue erstellt haben, erscheinen die folgenden Schaltflächen:

![Schaltflächen „Berechtigungen verwalten“ und „Anwendung verwalten“](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Klicken Sie auf **Anwendung verwalten**, um das Azure AD-Anwendungsblatt zu öffnen. Auf dem Blatt **Anwendung verwalten** können Sie die Client-ID der App (Anwendungs-ID) abrufen. Wählen Sie **Schlüssel**, um einen geheimen Clientschlüssel (Schlüssel) zu erstellen.

![Blatt „Anwendung verwalten“ – Option „Schlüssel“](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Verwalten von Berechtigungen und der Anwendung

Wenn Sie die Azure AD-Anwendung ausgewählt haben, können Sie die Anwendung sowie die Berechtigungen verwalten. Klicken Sie auf **Berechtigungen verwalten**, um Ihre Azure AD-Anwendung für den Zugriff auf andere Anwendungen einzurichten. Klicken Sie auf **Anwendung verwalten**, um Verwaltungsaufgaben auszuführen. Dazu zählt z.B. das Ändern von Schlüsseln und Antwort-URLs oder das Bearbeiten des Anwendungsmanifests.

### <a name="edit-the-apps-settings-or-manifest"></a>Bearbeiten der App-Einstellungen oder des App-Manifests

Klicken Sie auf **Anwendung verwalten**, um die Einstellungen oder das Manifest der App zu bearbeiten.

![Seite zum Verwalten der Anwendung](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit dem [Hochladen von Dateien in Ihr Konto](media-services-portal-upload-files.md).
