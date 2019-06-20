---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178588"
---
1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Windows Store-App-Projekt. Wählen Sie dann **Store** > **App mit Store verknüpfen** aus.

    ![Zuordnen der App zu Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. Wählen Sie im Assistenten **Weiter** aus. Melden Sie sich dann mit Ihrem Microsoft-Konto an. Geben Sie unter **Neuen App-Namen reservieren** einen Namen für Ihre App ein, und wählen Sie dann **Reservieren** aus.
3. Nachdem die App-Registrierung erstellt wurde, wählen Sie den Namen der neuen App aus. Wählen Sie **Weiter** und anschließend **Zuordnen** aus. Durch diesen Vorgang werden dem Anwendungsmanifest die erforderlichen Windows Store-Registrierungsinformationen hinzugefügt.
4. Wiederholen Sie die Schritte 1 und 3 für das Windows Phone Store-App-Projekt, und verwenden Sie dabei die zuvor erstellte Registrierung für die Windows Store-App.  
5. Wechseln Sie zum [Windows Dev Center](https://dev.windows.com/en-us/overview), und melden Sie sich mit Ihrem Microsoft-Konto an. Wählen Sie unter **Meine Apps** die neue App-Registrierung aus. Erweitern Sie dann **Dienste** > **Pushbenachrichtigungen**.
6. Wählen Sie auf der Seite **Pushbenachrichtigungen** unter **Windows-Pushbenachrichtigungsdienste (WNS) und Microsoft Azure Mobile Apps** die Option **Live Services-Website** aus.  Notieren Sie sich die Werte für **Paket-SID** und den *aktuellen* Wert für **Geheimer Anwendungsschlüssel**. 

    ![App-Einstellung im Developer Center](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Der geheime Schlüssel der Anwendung und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer App.
   >
   >
