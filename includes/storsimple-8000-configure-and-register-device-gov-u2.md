---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 338332eb90fda9afe7655f9f2ad19ed726419b6c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557273"
---
### <a name="to-configure-and-register-the-device"></a>So konfigurieren und registrieren Sie das Gerät
1. Greifen Sie an der seriellen Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Anweisungen dazu finden Sie unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) . **Achten Sie darauf, dass Sie die Vorgehensweise genau befolgen. Andernfalls sind Sie nicht in der Lage, auf die Konsole zuzugreifen.**
2. Drücken Sie in der Sitzung, die geöffnet wird, einmal die **EINGABETASTE**, um eine Eingabeaufforderung zu öffnen.
3. Sie werden aufgefordert, die Sprache auszuwählen, die für Ihr Gerät festgelegt werden soll. Geben Sie die Sprache an, und drücken Sie dann die **EINGABETASTE**.
   
    ![StorSimple – Konfigurieren und Registrieren des Geräts 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. Wählen Sie im angezeigten Menü der seriellen Konsole Option 1 aus, also die **Anmeldung mit Vollzugriff**.
   
    ![StorSimple – Registrieren des Geräts 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Führen Sie die folgenden Schritte aus, um die mindestens erforderlichen Netzwerkeinstellungen für Ihr Gerät zu konfigurieren.
   
   > [!IMPORTANT]
   > Diese Konfigurationsschritte müssen für den aktiven Controller des Geräts ausgeführt werden. Das Menü der seriellen Konsole gibt den Zustand des Controllers in der Bannermeldung an. Wenn Sie nicht mit dem aktiven Controller verbunden sind, trennen Sie die Verbindung, und stellen Sie dann eine Verbindung mit dem aktiven Controller her.
   
   1. Geben Sie an der Eingabeaufforderung Ihr Kennwort ein. Das Standardkennwort für das Gerät lautet **Password1**.
   2. Geben Sie folgenden Befehl ein:
      
        `Invoke-HcsSetupWizard`
   3. Es wird ein Installations-Assistent angezeigt, der Sie beim Konfigurieren der Netzwerkeinstellungen für das Gerät unterstützt. Geben Sie die folgenden Informationen an:
      
      * IP-Adresse für die Netzwerkschnittstelle DATA 0
      * Subnetzmaske
      * Gateway
      * IP-Adresse für den primären DNS-Server
      * IP-Adresse für den sekundären DNS-Server
      
      > [!NOTE]
      > Sie müssen ggf. einige Minuten warten, bis die Subnetzmaske und die DNS-Einstellungen übernommen werden.
    
   4. (Optional) Konfigurieren Sie Ihren Webproxyserver.
      
      > [!IMPORTANT]
      > Die Webproxykonfiguration ist optional. Achten Sie jedoch bei Verwendung eines Webproxys darauf, dass dieser nur hier konfiguriert werden kann. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
     
6. Drücken Sie STRG+C, um den Setup-Assistenten zu beenden.
8. Führen Sie das folgende Cmdlet aus, um das Gerät auf das Microsoft Azure-Portal für Behörden zu verweisen. (Es wird standardmäßig auf das öffentliche klassische Azure-Portal verwiesen.) Beide Controller werden dadurch neu gestartet. Es wird empfohlen, dass Sie zwei PuTTY-Sitzungen verwenden, um gleichzeitig eine Verbindung mit beiden Controllern herzustellen, sodass Sie sehen können, wenn jeder Controller neu gestartet wird.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Es wird eine Bestätigungsmeldung angezeigt. Übernehmen Sie den Standardwert (**Y**).
9. Führen Sie das folgende Cmdlet aus, um die Installation fortzusetzen:
   
    `Invoke-HcsSetupWizard`
   
    ![Fortsetzen des Installations-Assistenten](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Akzeptieren Sie die Netzwerkeinstellungen. Es wird eine Validierungsmeldung angezeigt, nachdem Sie alle Einstellungen akzeptiert haben.
11. Aus Sicherheitsgründen läuft das Administratorkennwort für das Gerät nach der ersten Sitzung ab, und Sie müssen es jetzt ändern. Geben Sie, wenn Sie dazu aufgefordert werden, ein Administratorkennwort für das Gerät an. Ein gültiges Administratorkennwort für das Gerät muss zwischen 8 und 15 Zeichen lang sein. Das Kennwort muss aus drei der folgenden Zeichenkategorien bestehen: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen.
    
    <br/>![StorSimple – Registrieren des Geräts 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. Der letzte Schritt im Installations-Assistenten besteht im Registrieren Ihres Geräts beim StorSimple-Geräte-Manager-Dienst. Zu diesem Zweck benötigen Sie den Dienstregistrierungsschlüssel, den Sie unter [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key) abgerufen haben. Nachdem Sie den Registrierungsschlüssel bereitgestellt haben, müssen Sie ggf. einige Minuten warten, bis das Gerät registriert wurde.
    
    > [!NOTE]
    > Sie können jederzeit STRG+C drücken, um den Installations-Assistenten zu beenden. Wenn Sie alle Netzwerkeinstellungen (IP-Adresse für DATA 0, Subnetzmaske und Gateway) angegeben haben, werden Ihre Einträge beibehalten.
    
    ![Fortschritt der StorSimple-Registrierung](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Nachdem das Gerät registriert wurde, wird ein Schlüssel für die Dienstdatenverschlüsselung angezeigt. Kopieren Sie diesen Schlüssel, und bewahren Sie ihn an einem sicheren Ort auf. **Dieser Schlüssel ist zusammen mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte beim StorSimple-Geräte-Manager-Dienst erforderlich.** Weitere Informationen zu diesem Schlüssel finden Sie unter [StorSimple-Sicherheit](../articles/storsimple/storsimple-8000-security.md) .
    
    ![StorSimple – Registrieren des Geräts 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Markieren Sie zum Kopieren des Texts aus dem Fenster der seriellen Konsole den Text einfach. Sie sollten damit in der Lage sein, ihn in die Zwischenablage oder einen beliebigen Texteditor zu kopieren.
    > 
    > Kopieren Sie den Schlüssel für die Dienstdatenverschlüsselung NICHT mithilfe von **STRG + C**. **STRG+C** bewirkt, dass der Installations-Assistent beendet wird. Dies führt dazu, dass das Geräteadministratorkennwort nicht geändert und das Gerät auf das Standardkennwort zurückgesetzt wird.
    
14. Schließen Sie die serielle Konsole.
15. Kehren Sie zum Azure-Portal für Behörden zurück, und führen Sie die folgenden Schritte aus:
    
    1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst.
    2. Klicken Sie auf **Geräte**. Suchen Sie in der Geräteliste das Gerät, das Sie bereitstellen. Vergewissern Sie sich, dass das Gerät erfolgreich mit dem Dienst verbunden wurde, indem Sie seinen Status überprüfen. Als Gerätestatus sollte **Online** angezeigt werden.
            
        Wenn der Gerätestatus **Offline** ist, warten Sie einige Minuten, bis das Gerät online geschaltet wird.
       
        Wenn das Gerät nach einigen Minuten immer noch offline ist, müssen Sie sicherstellen, dass Ihr Firewallnetzwerk wie unter [Netzwerkanforderungen für Ihr StorSimple-Gerät](../articles/storsimple/storsimple-8000-system-requirements.md) konfiguriert wurde.
       
        Stellen Sie sicher, dass Port 9354 für die ausgehende Kommunikation geöffnet ist, da dieser Port vom Service Bus für die Kommunikation zwischen dem StorSimple-Geräte-Manager-Dienst und Geräten verwendet wird.