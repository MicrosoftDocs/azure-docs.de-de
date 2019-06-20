---
title: Zugreifen auf die Azure Media Services-API per Azure Active Directory-Authentifizierung | Microsoft-Dokumentation
description: Es werden die Konzepte und Schritte beschrieben, die erforderlich sind, um Azure Active Directory (Azure AD) zum Authentifizieren des Zugriffs auf die Azure Media Services-API zu verwenden.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: d80a58f1886ecc1ca2a735881fc5822f2fc0c53b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60826146"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung  

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Die Azure Media Services-API ist eine RESTful-API. Sie können sie nutzen, um Vorgänge für Medienressourcen durchzuführen, indem Sie eine REST-API oder verfügbare Client-SDKs verwenden. Azure Media Services stellt ein Media Services-Client-SDK für Microsoft .NET bereit. Sie müssen zuerst authentifiziert werden, um berechtigt zu sein, auf Media Services-Ressourcen und die Media Services-API zuzugreifen. 

Für Media Services wird die [Azure Active Directory-basierte Authentifizierung](../../active-directory/fundamentals/active-directory-whatis.md) unterstützt. Für den Azure Media-REST-Dienst ist Folgendes erforderlich: Der Benutzer oder die Anwendung, der bzw. die die REST-API-Anforderungen sendet, muss entweder über die Rolle **Mitwirkender** oder **Besitzer** verfügen, um auf die Ressourcen zuzugreifen. Weitere Informationen finden Sie unter [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](../../role-based-access-control/overview.md).  

Dieses Dokument enthält eine Übersicht darüber, wie Sie per REST- oder .NET-API auf die Media Services-API zugreifen.

> [!NOTE]
> Die Access Control-Authentifizierung gilt ab dem 1. Juni 2018 als veraltet.

## <a name="access-control"></a>Zugriffssteuerung

Damit die Azure Media Services-REST-Anforderung erfolgreich ist, muss der aufrufende Benutzer über die Rolle „Mitwirkender“ oder „Besitzer“ für das Media Services-Konto verfügen, auf das zugegriffen werden soll.  
Nur ein Benutzer mit der Rolle „Besitzer“ kann der Medienressource (Konto) Zugriff auf neue Benutzer oder Apps gewähren. Mit der Rolle „Mitwirkender“ ist nur der Zugriff auf die Medienressource möglich.
Für nicht autorisierte Anforderungen tritt ein Fehler mit dem Statuscode 401 auf. Wenn dieser Fehlercode angezeigt wird, sollten Sie überprüfen, ob Ihr Benutzer die Rolle „Mitwirkender“ oder „Besitzer“ aufweist, die für das Media Services-Konto des Benutzers zugewiesen ist. Dies können Sie im Azure-Portal überprüfen. Suchen Sie nach Ihrem Media-Konto, und klicken Sie anschließend auf die Registerkarte **Zugriffssteuerung**. 

![Registerkarte „Zugriffssteuerung“](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Authentifizierungstypen 
 
Wenn Sie die Azure AD-Authentifizierung mit Azure Media Services nutzen, stehen Ihnen zwei Authentifizierungsoptionen zur Verfügung:

- **Benutzerauthentifizierung**. Dient zum Authentifizieren einer Person, die die App zum Interagieren mit Media Services-Ressourcen verwendet. Die interaktive Anwendung sollte den Benutzer zuerst zur Eingabe seiner Anmeldeinformationen auffordern. Ein Beispiel hierfür ist eine Verwaltungskonsolen-App, die von autorisierten Benutzern zum Überwachen von Codierungsaufträgen oder Livestreaming verwendet wird. 
- **Dienstprinzipalauthentifizierung**. Dient zum Authentifizieren eines Diensts. Bei Anwendungen, die diese Authentifizierungsmethode normalerweise nutzen, handelt es sich um Apps, mit denen Daemondienste, Dienste der mittleren Ebene oder geplante Aufträge ausgeführt werden. Beispiele sind Web-Apps, Funktions-Apps, Logik-Apps, API und Microservices.

### <a name="user-authentication"></a>Benutzerauthentifizierung 

Anwendungen, für die die Benutzerauthentifizierung verwendet werden sollte, sind native Apps für die Verwaltung oder Überwachung: mobile Apps, Windows-Apps und Konsolenanwendungen. Diese Art von Lösung ist nützlich, wenn in einem der folgenden Szenarien die menschliche Interaktion mit dem Dienst gewünscht ist:

- Dashboard für die Überwachung Ihrer Codieraufträge
- Dashboard für die Überwachung Ihrer Livestreams
- Verwaltungsanwendung für Desktop- oder mobile Benutzer zum Verwalten von Ressourcen in einem Media Services-Konto

> [!NOTE]
> Diese Authentifizierungsmethode sollte nicht für kundenorientierte Anwendungen verwendet werden. 

Eine native Anwendung muss zuerst von Azure AD ein Zugriffstoken beschaffen und es anschließend verwenden, wenn Sie HTTP-Anforderungen an die Media Services-REST-API senden. Fügen Sie das Zugriffstoken dem Anforderungsheader hinzu. 

Im folgenden Diagramm ist der typische Authentifizierungsablauf für eine interaktive Anwendung dargestellt: 

![Diagramm: Native Apps](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Im obigen Diagramm stellen die Zahlen den Fluss der Anforderungen in chronologischer Reihenfolge dar.

> [!NOTE]
> Bei Verwendung der Benutzerauthentifizierungsmethode werden für alle Apps die gleiche Client-ID und der gleiche Umleitungs-URI (Standard) für native Anwendungen genutzt. 

1. Fordern Sie einen Benutzer zur Eingabe von Anmeldeinformationen auf.
2. Fordern Sie ein Azure AD-Zugriffstoken mit den folgenden Parametern an:  

   * Azure AD-Mandanten-Endpunkt

       Die Informationen zum Mandanten können über das Azure-Portal abgerufen werden. Platzieren Sie den Cursor in der oberen rechten Ecke über dem Namen des angemeldeten Benutzers.
   * Media Services-Ressourcen-URI. 

       Dieser URI ist für Media Services-Konten identisch, die sich in derselben Azure-Umgebung befinden (z.B. https://rest.media.azure.net).

   * Client-ID für (native) Media Services-Anwendungen.
   * Umleitungs-URI für Media Services-Anwendungen (nativ)
   * Ressourcen-URI für REST Media Services
        
       Der URI gibt den REST-API-Endpunkt an (z.B. https://test03.restv2.westus.media.azure.net/api/).

     Informationen zu den Werten dieser Parameter finden Sie unter [Get started with Azure AD authentication by using the Azure portal](media-services-portal-get-started-with-aad.md) (Erste Schritte mit der Azure AD-Authentifizierung mit dem Azure-Portal), indem Sie die Option für die Benutzerauthentifizierung wählen.

3. Das Azure AD-Zugriffstoken wird an den Client gesendet.
4. Der Client sendet eine Anforderung an die Azure Media-REST-API mit dem Azure AD-Zugriffstoken.
5. Der Client erhält die Daten von den Media Services zurück.

Informationen zur Verwendung der Azure AD-Authentifizierung zum Kommunizieren mit REST-Anforderungen über das Media Services-.NET-Client-SDK finden Sie unter [Use Azure AD authentication to access Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API mit .NET). 

Wenn Sie das Media Services-.NET-Client-SDK nicht verwenden, müssen Sie manuell eine Anforderung mit Azure AD-Zugriffstoken erstellen, indem Sie die in Schritt 2 beschriebenen Parameter verwenden. Weitere Informationen finden Sie unter [Azure Active Directory-Authentifizierungsbibliotheken](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

Anwendungen, für die diese Authentifizierungsmethode normalerweise verwendet wird, sind Apps, die Dienste der mittleren Ebene und geplante Aufträge ausführen: Web-Apps, Funktions-Apps, Logik-Apps, APIs und Microservices. Diese Authentifizierungsmethode eignet sich auch für interaktive Anwendungen, bei denen Sie unter Umständen ein Dienstkonto zum Verwalten von Ressourcen verwenden.

Wenn Sie die Dienstprinzipalauthentifizierung zum Erstellen von Verbraucherszenarien nutzen, wird die Authentifizierung normalerweise auf der mittleren Ebene (über eine API) und nicht direkt in einer mobilen Anwendung oder Desktopanwendung durchgeführt. 

Erstellen Sie zum Verwenden dieser Methode eine Azure AD-Anwendung und einen Dienstprinzipal im eigenen Mandanten. Nachdem Sie die Anwendung erstellt haben, können Sie für die App den Zugriff auf das Media Services-Konto über die Rolle „Mitwirkender“ oder „Besitzer“ gewähren. Hierfür können Sie das Azure-Portal, die Azure CLI oder ein PowerShell-Skript verwenden. Außerdem können Sie eine vorhandene Azure AD-Anwendung nutzen. Sie können Ihre Azure AD-App und den Dienstprinzipal [im Azure-Portal](media-services-portal-get-started-with-aad.md) registrieren und verwalten. Außerdem können Sie dazu die [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) oder [PowerShell](media-services-powershell-create-and-configure-aad-app.md) verwenden. 

![Apps der mittleren Ebene](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Nach dem Erstellen Ihrer Azure AD-Anwendung erhalten Sie Werte für die folgenden Einstellungen. Sie benötigen diese Werte für die Authentifizierung:

- Client-ID 
- Geheimer Clientschlüssel 

In der obigen Abbildung stellen die Zahlen den Fluss der Anforderungen in chronologischer Reihenfolge dar:
    
1. Eine App der mittleren Ebene (Web-API oder Webanwendung) fordert ein Azure AD-Zugriffstoken an, das die folgenden Parameter enthält:  

   * Azure AD-Mandantenendpunkt.

       Die Informationen zum Mandanten können über das Azure-Portal abgerufen werden. Platzieren Sie den Cursor in der oberen rechten Ecke über dem Namen des angemeldeten Benutzers.
   * Media Services-Ressourcen-URI. 

       Dieser URI ist für Media Services-Konten identisch, die sich in derselben Azure-Umgebung befinden (z.B. https://rest.media.azure.net).

   * Ressourcen-URI für REST Media Services

       Der URI gibt den REST-API-Endpunkt an (z.B. https://test03.restv2.westus.media.azure.net/api/).

   * Werte der Azure AD-Anwendung: Client-ID und geheimer Clientschlüssel.
    
     Informationen zu den Werten dieser Parameter finden Sie unter [Get started with Azure AD authentication by using the Azure portal](media-services-portal-get-started-with-aad.md) (Erste Schritte mit der Azure AD-Authentifizierung mit dem Azure-Portal), indem Sie die Option für die Dienstprinzipalauthentifizierung wählen.

2. Das Azure AD-Zugriffstoken wird an die mittlere Ebene gesendet.
4. Die mittlere Ebene sendet eine Anforderung mit dem Azure AD-Token an die Azure Media-REST-API.
5. Die mittlere Ebene erhält die Daten von den Media Services zurück.

Weitere Informationen zur Verwendung der Azure AD-Authentifizierung zum Kommunizieren mit REST-Anforderungen über das Media Services-.NET-Client-SDK finden Sie unter [Use Azure AD authentication to access Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API mit .NET). 

Wenn Sie das Media Services-.NET-Client-SDK nicht verwenden, müssen Sie manuell eine Azure AD-Tokenanforderung erstellen, indem Sie die in Schritt 1 beschriebenen Parameter verwenden. Weitere Informationen finden Sie unter [Azure Active Directory-Authentifizierungsbibliotheken](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Problembehandlung

Ausnahme: Der Remoteserver hat einen Fehler zurückgegeben: 401 – nicht autorisiert.

Lösung: Damit die Media Services-REST-Anforderung erfolgreich ist, muss der aufrufende Benutzer über die Rolle „Mitwirkender“ oder „Besitzer“ in dem Media Services-Konto verfügen, auf das zugegriffen werden soll. Weitere Informationen finden Sie im Abschnitt [Zugriffssteuerung](media-services-use-aad-auth-to-access-ams-api.md#access-control).

## <a name="resources"></a>Ressourcen

Die folgenden Artikel enthalten Übersichten über Azure AD-Authentifizierungskonzepte: 

- [Grundlagen der Authentifizierung in Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Integrieren von Anwendungen in Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Verwalten der rollenbasierten Zugriffssteuerung mit Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie das Azure-Portal zum [Zugreifen auf die Azure AD-Authentifizierung, um die Azure Media Services-API zu nutzen](media-services-portal-get-started-with-aad.md).
* Verwenden Sie die Azure AD-Authentifizierung zum [Zugreifen auf die Azure Media Services-API mit .NET](media-services-dotnet-get-started-with-aad.md).

