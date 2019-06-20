---
title: Includedatei
description: Includedatei
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4ceff7d59443fe78fb8cf7164e5f31cf1acc189a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420797"
---
1. Melden Sie sich bei der [Firebase-Konsole](https://firebase.google.com/console/) an. Erstellen Sie ein neues Firebase-Projekt, falls Sie noch keins besitzen.
2. Klicken Sie nach der Erstellung Ihres Projekts auf **Add Firebase to your Android app** (Firebase der Android-App hinzufügen). 

    ![Hinzufügen von Firebase zu Ihrer Android-App](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Führen Sie auf der Seite **Add Firebase to your Android app** (Firebase der Android-App hinzufügen) die folgenden Schritte aus: 
    1. Geben Sie für **Android-Paketname**, einen Namen für Ihr Paket ein. Beispiel: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Angeben des Paketnamens](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wählen Sie **Register app** (App registrieren) aus. 
4. Wählen Sie **Download google-services.json** (google-services.json herunterladen) aus, speichern Sie die Datei im Ordner **app** des Projekts, und klicken Sie dann auf **Next** (Weiter). 

    ![Herunterladen von „google-services.json“](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Klicken Sie auf der Seite auf **Next** (Weiter). 
7. Klicken Sie auf der Seite auf **Skip this step** (Diesen Schritt überspringen). 

    ![Den letzten Schritt überspringen](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Klicken Sie in der Firebase-Konsole auf das Zahnrad für Ihr Projekt. Klicken Sie dann auf **Projekteinstellungen**.

    ![Klicken auf „Projekteinstellungen“](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Wenn Sie die Datei **google-services.json** nicht heruntergeladen haben, können Sie dies auf dieser Seite tun. 
5. Wechseln Sie oben zur Registerkarte **Cloud Messaging**. 
6. Kopieren und speichern Sie den **Legacy-Serverschlüssel** zur späteren Verwendung. Dieser Wert dient zum Konfigurieren des Notification Hub.
