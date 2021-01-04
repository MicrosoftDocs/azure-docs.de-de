---
title: include file
description: include file
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b32ff0926aa2b797bdb8dfcbb01cf845080a0ce9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376160"
---
1. Navigieren Sie zur [Google Developers Console](https://console.developers.google.com/cloud-resource-manager), und melden Sie sich mit den Anmeldeinformationen Ihres Google-Kontos an. 
2. Wählen Sie auf der Symbolleiste die Option **Create Project** (Projekt erstellen) aus. 
   
    ![Erstellen eines neuen Projekts](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. Geben Sie für **Project name** (Projektname) den Namen des Projekts ein, und klicken Sie auf **Create** (Erstellen).
4. Wählen Sie die Schaltfläche **Alerts** (Warnungen) auf der Symbolleiste aus, und wählen Sie das Projekt in der Liste aus. Das Dashboard für Ihr Projekt wird angezeigt. Sie können auch mit der URL `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>` direkt zum Dashboard navigieren.

    ![Auswählen Ihres Projekts in Warnungen](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Notieren Sie sich die **Project number** (Projektnummer) in der Kachel **Project info** (Projektinformationen) des Dashboards. 

    ![Projekt-ID](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. Wählen Sie im Dashboard auf der Kachel **APIs** die Option **Go to APIs overview** (Zur API-Übersicht wechseln) aus. 

    ![API-Übersichtslink](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Wählen Sie auf der **API** -Seite **ENABLE APIS AND SERVICES** (APIS UND DIENSTE AKTIVIEREN) aus. 

    ![Schaltfläche „Enable APIs and Services“ (APIs und Dienste aktivieren)](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Suchen Sie **Google Cloud Messaging** , und wählen Sie es aus. 

    ![Suchen Sie Google Cloud Messaging, und wählen Sie es aus.](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Um Google Cloud Messaging für das Projekt zu aktivieren, wählen Sie **AKTIVIEREN** aus.

    ![Aktivieren von Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Wählen Sie **Create credentials** (Anmeldeinformationen erstellen) auf der Symbolleiste aus. 

    ![Schaltfläche „Create credentials“ (Anmeldeinformationen erstellen)](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Wählen Sie auf der Seite **Add credentials to your project** (Anmeldeinformationen Ihrem Projekt hinzufügen) den Link **API key** (API-Schlüssel) aus. 

    ![Hinzufügen von Anmeldeinformationen](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Wählen Sie auf der Seite **API-Schlüssel** die Option **Create/Save** (Erstellen/speichern) aus. Im folgenden Beispiel ist die Option **IP addresses** (IP-Adressen) ausgewählt, und **0.0.0.0/0** wird für zulässige IP-Adressen eingegeben. Sie sollten Ihren API-Schlüssel entsprechend einschränken. 

    ![API-Schlüssel – Schaltfläche „Create“ (Erstellen)](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Kopieren Sie den **API-Schlüssel** in die Zwischenablage, und speichern Sie ihn an beliebiger Stelle. 

    ![Kopieren des API-Schlüssels](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Mit diesem API-Schlüsselwert geben Sie Azure die Möglichkeit, sich bei GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken. Navigieren Sie mithilfe der URL `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>` zurück zum Projektdashboard.

