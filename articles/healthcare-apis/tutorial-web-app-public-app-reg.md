---
title: 'Web-App-Tutorial: Einrichten einer Clientanwendung'
description: Dieses Tutorial führt Sie Schritte für Schritt durch das Registrieren einer öffentlichen Anwendung als Vorbereitung für das Bereitstellen einer Webanwendung.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: f559c87a5c3285bf966f9389d623b2262e93cbca
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425812"
---
# <a name="client-application-registration"></a>Registrieren einer Clientanwendung
Im vorherigen Tutorial haben Sie Azure API for FHIR bereitgestellt und eingerichtet. Nachdem Sie Azure API for FHIR eingerichtet haben, registrieren Sie nun eine öffentliche Clientanwendung. Weitere Details und Informationen zur Problembehandlung finden Sie in der vollständigen Schrittanleitung zum [Registrieren einer öffentlichen Client-App](register-public-azure-ad-client-app.md). Die wichtigsten Schritte für dieses Tutorial sind jedoch nachfolgend aufgeführt.

1. Navigieren Sie zu Azure Active Directory.
1. Wählen Sie **App-Registrierung** --> **Neue Registrierung** aus.
1. Benennen Sie Ihre Anwendung.
1. Wählen Sie **Öffentlicher Client/nativ (mobil und Desktop)** aus, und legen Sie den Umleitungs-URI auf https://www.getpostman.com/oauth2/callback fest.

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Screenshot: Bereich „Anwendung registrieren“ und Name und Umleitungs-URL einer Beispielanwendung":::

## <a name="client-application-settings"></a>Clientanwendungseinstellungen

Kopieren Sie nach der Registrierung der Clientanwendung die Anwendungs-ID (Client) und die Mandanten-ID von der Übersichtsseite. Sie benötigen diese beiden Werte später beim Zugreifen auf den Client.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Screenshot: Bereich mit Clientanwendungseinstellungen mit hervorgehobenen Anwendungs- und Verzeichnis-IDs":::

### <a name="connect-with-web-app"></a>Herstellen einer Verbindung mit der Web-App

Wenn Sie Ihre [Web-App](tutorial-web-app-write-web-app.md) zum Herstellen einer Verbindung mit Azure API for FHIR geschrieben haben, müssen Sie auch die richtigen Authentifizierungsoptionen festlegen. 

1. Wählen Sie im linken Menü unter **Verwalten** die Option **Authentifizierung** aus. 

1. Wählen Sie zum Hinzufügen einer neuen Plattformkonfiguration **Web** aus.

1. Richten Sie den Umleitungs-URI ein. Dies dient als Vorbereitung für das Erstellen Ihrer Webanwendung im vierten Teil dieses Tutorials. Fügen Sie zu diesem Zweck `https://\<WEB-APP-NAME>.azurewebsites.net` der Liste der Umleitungs-URIs hinzu. Wenn Sie in dem Schritt, in dem Sie die [Web-App schreiben](tutorial-web-app-write-web-app.md), einen anderen Namen wählen, müssen Sie zurückkehren und diesen Eintrag aktualisieren.

1. Aktivieren Sie die Kontrollkästchen **Zugriffstoken** und **ID-Token**.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Screenshot: Blatt „App-Authentifizierungseinstellungen“, auf dem die Schritte zum Hinzufügen einer Plattform hervorgehoben sind":::

## <a name="add-api-permissions"></a>Hinzufügen von API-Berechtigungen

Nachdem Sie die richtige Authentifizierung eingerichtet haben, legen Sie die API-Berechtigungen fest:

1. Wählen Sie **API-Berechtigungen** aus, und klicken Sie auf **Berechtigung hinzufügen**.
1. Suchen Sie unter **Von meiner Organisation verwendete APIs** nach Azure-APIs für das Gesundheitswesen.
1. Wählen Sie **user_impersonation** aus, und klicken Sie auf **Berechtigungen hinzufügen**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Screenshot: Blatt zum Hinzufügen von API-Berechtigungen, auf dem die Schritte zum Hinzufügen von API-Berechtigungen hervorgehoben sind":::

## <a name="next-steps"></a>Nächste Schritte
Sie verfügen jetzt über eine öffentlichen Clientanwendung. Im nächsten Tutorial wird das Testen und der Zugriff auf diese Anwendung über Postman beschrieben.

>[!div class="nextstepaction"]
>[Testen der Clientanwendung in Postman](tutorial-web-app-test-postman.md)
