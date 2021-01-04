---
title: 'Registrieren einer öffentlichen Client-App in Azure AD: Azure API for FHIR'
description: In diesem Artikel wird erläutert, wie Sie zur Vorbereitung auf die Bereitstellung der FHIR-API in Azure eine öffentliche Clientanwendung in Azure Active Directory registrieren.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: ff7bb6084839af56b5f6e874b39929021f23f8a1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398078"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrieren einer öffentlichen Clientanwendung in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie eine öffentliche Anwendung in Azure Active Directory registrieren.  

Clientanwendungsregistrierungen stellen Anwendungen in Azure Active Directory dar, die im Auftrag des Benutzers authentifiziert werden und API-Berechtigungen anfordern können. Öffentliche Clients sind Anwendungen wie beispielsweise mobile Anwendungen und JavaScript-Einzelseitenanwendungen, die Geheimnisse nicht vertraulich behandeln können. Das Verfahren ähnelt dem [Registrieren eines vertraulichen Clients](register-confidential-azure-ad-client-app.md). Öffentliche Clients erweisen sich jedoch beim Speichern eines Anwendungsgeheimnisses als nicht vertrauenswürdig, daher muss kein Geheimnis hinzugefügt werden.

In dieser Schnellstartanleitung erhalten Sie allgemeine Informationen, wie Sie [eine Anwendung bei der Microsoft Identity Platform registrieren](../active-directory/develop/quickstart-register-app.md).

## <a name="app-registrations-in-azure-portal"></a>App-Registrierungen im Azure-Portal

1. Klicken Sie im linken Navigationsbereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**.

2. Klicken Sie auf dem Blatt **Azure Active Directory** auf **App-Registrierungen** :

    ![Azure-Portal. Neue App-Registrierung.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicken Sie auf **Neue Registrierung**.

## <a name="application-registration-overview"></a>Übersicht über die Anwendungsregistrierung

1. Weisen Sie der Anwendung einen Anzeigenamen zu.

2. Stellen Sie eine Antwort-URL bereit. Unter der Antwort-URL werden Authentifizierungscodes an die Clientanwendung zurückgegeben. Sie können mehrere Antwort-URLs hinzufügen und vorhandene URLs später bearbeiten.

    ![Azure-Portal. Neue öffentliche App-Registrierung](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


So konfigurieren Sie Ihre [Desktop](../active-directory/develop/scenario-desktop-app-registration.md)-, [mobile](../active-directory/develop/scenario-mobile-app-registration.md) oder [Single-Page](../active-directory/develop/scenario-spa-app-registration.md)-Anwendung als öffentliche Anwendung:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) unter **App-Registrierungen** Ihre App aus, und wählen Sie dann **Authentifizierung** aus.

2. Wählen Sie **Erweiterte Einstellungen** > **Standardclienttyp** aus. Wählen Sie für **Anwendung als öffentlichen Client behandeln** die Option **Ja** aus.

3. Wählen Sie für eine Single-Page-Anwendung **Zugriffstoken** und **ID-Token** aus, um den impliziten Fluss zu aktivieren.

   - Wenn Ihre Anwendung Benutzer anmeldet, wählen Sie **ID-Token** aus.
   - Wenn Ihre Anwendung auch eine geschützte Web-API aufrufen muss, wählen Sie **Zugriffstoken** aus.

## <a name="api-permissions"></a>API-Berechtigungen

Sie müssen ähnlich wie bei [vertraulichen Clientanwendungen](register-confidential-azure-ad-client-app.md) festlegen, welche API-Berechtigungen im Auftrag der Benutzer von dieser Anwendung angefordert werden können:

1. Öffnen Sie die **API-Berechtigungen**.

    Wenn Sie die Azure API for FHIR verwenden, müssen Sie den Azure Healthcare APIs eine Berechtigung hinzufügen, indem Sie unter **Von meiner Organisation verwendete APIs** nach diesen APIs suchen. Diese APIs werden nur gefunden, wenn Sie die [Azure API for FHIR](fhir-paas-powershell-quickstart.md) bereits bereitgestellt haben.

    
    Wenn Sie auf eine andere Ressourcenanwendung verweisen, wählen Sie unter **Meine APIs** die zuvor erstellte [Ressourcenanwendungsregistrierung für die FHIR-API](register-resource-azure-ad-client-app.md) aus:

    ![Azure-Portal. Neue öffentliche API-Berechtigungen: Standardberechtigungen für Azure API for FHIR](media/public-client-app/api-permissions.png)


2. Wählen Sie die Berechtigungen aus, die von der Anwendung angefordert werden dürfen: ![Azure-Portal. App-Berechtigungen](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>Überprüfen der Autorität des FHIR-Servers
Wenn sich die Anwendung, die Sie in diesem Artikel registriert haben, und Ihr FHIR-Server im selben Azure AD-Mandanten befinden, müssen Sie mit den nächsten Schritten fortfahren.

Wenn Sie Ihre Clientanwendung in einem anderen Azure AD-Mandanten konfigurieren, müssen Sie die **Autorität** aktualisieren. In der Azure API for FHIR legen Sie die Autorität unter „Einstellungen“ -> „Authentifizierung“ fest. Legen Sie die Autorität auf **https://login.microsoftonline.com/\<TENANT-ID>** fest.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine öffentliche Clientanwendung in Azure Active Directory registrieren. Als Nächstes testen Sie den Zugriff auf Ihren FHIR-Server über Postman.
 
>[!div class="nextstepaction"]
>[Zugreifen auf Azure API for FHIR mit Postman](access-fhir-postman-tutorial.md)