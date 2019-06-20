---
title: Registrieren einer vertraulichen Clientanwendung in Azure Active Directory – Azure API for FHIR
description: In diesem Artikel wird erläutert, wie Sie eine vertrauliche Clientanwendung in Azure Active Directory registrieren.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 53ebce4151bfc9ef2324f0e75eb7d199d9479e83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60709952"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrieren einer vertraulichen Clientanwendung in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie eine vertrauliche Clientanwendung in Azure Active Directory registrieren. Die Registrierung einer Clientanwendung ist eine Azure Active Directory-Darstellung einer Anwendung, die zur Authentifizierung im Auftrag eines Benutzers und zur Anforderung des Zugriffs auf [Ressourcenanwendungen](register-resource-azure-ad-client-app.md) verwendet werden kann. Eine vertrauliche Clientanwendung ist eine Anwendung, die sich beim Speichern eines Geheimnisses und dem Anzeigen dieses Geheimnisses beim Anfordern von Zugriffstoken als vertrauenswürdig erweist. Beispiele für vertrauliche Anwendungen sind serverseitige Anwendungen.

Führen Sie die folgenden Schritte aus, um eine neue vertrauliche Anwendung im Portal zu registrieren.

## <a name="app-registrations-in-azure-portal"></a>App-Registrierungen im Azure-Portal

1. Klicken Sie im linken Navigationsbereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**.

2. Klicken Sie auf dem Blatt **Azure Active Directory** auf **App-Registrierungen (Vorschauversion)** :

    ![Azure-Portal. Neue App-Registrierung.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicken Sie auf **Neue Registrierung**.

## <a name="register-a-new-application"></a>Registrieren einer neuen Anwendung

1. Weisen Sie der Anwendung einen Anzeigenamen zu.

2. Stellen Sie eine Antwort-URL bereit. Diese Details können später geändert werden, aber wenn Sie die Antwort-URL Ihrer Anwendung kennen, geben Sie sie jetzt ein.

    ![Registrierung einer neuen vertraulichen Client-App.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API-Berechtigungen

Fügen Sie als Nächstes API-Berechtigungen hinzu:

1. Öffnen Sie **API-Berechtigungen**:

    ![Vertraulicher Client. API-Berechtigungen](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Klicken Sie auf **Berechtigung hinzufügen**.

3. Wählen Sie die entsprechende Ressourcen-API aus:

    Klicken Sie für Azure API for FHIR (verwalteter Dienst) auf **Von meiner Organisation verwendete APIs**, und suchen Sie nach „Azure-APIs für das Gesundheitswesen“. Wählen Sie für den Open Source FHIR-Server für Azure Folgendes aus: [FHIR API-Ressourcenanwendungsregistrierung](register-resource-azure-ad-client-app.md):

    ![Vertraulicher Client. Meine APIs](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Wählen Sie Bereiche (Berechtigungen) aus, nach denen die vertrauliche Anwendung im Auftrag eines Benutzers fragen können soll:

    ![Vertraulicher Client. Berechtigungen der Stellvertretung](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Anwendungsgeheimnis

1. Erstellen Sie ein Anwendungsgeheimnis (Clientgeheimnis):

    ![Vertraulicher Client. Anwendungsgeheimnis](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Geben Sie eine Beschreibung und die Dauer des Geheimnisses an.

3. Nach der Generierung wird es im Portal nur einmal angezeigt. Notieren Sie es sich, und bewahren Sie es sicher auf.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine vertrauliche Clientanwendung in Azure Active Directory registrieren. Als Nächstes stellen Sie eine FHIR-API in Azure bereit.
 
>[!div class="nextstepaction"]
>[Bereitstellen von Open Source-FHIR-Servern](fhir-oss-powershell-quickstart.md)