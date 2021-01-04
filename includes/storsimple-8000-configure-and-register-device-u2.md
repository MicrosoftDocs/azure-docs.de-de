---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ee92e145acd981bb8c84b43986b90306b994c262
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560050"
---
#### <a name="to-configure-and-register-the-device"></a>So konfigurieren und registrieren Sie das Gerät

1. Greifen Sie an der seriellen Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Anweisungen dazu finden Sie unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console) . **Achten Sie darauf, dass Sie die Vorgehensweise genau befolgen. Andernfalls sind Sie nicht in der Lage, auf die Konsole zuzugreifen.**

2. Drücken Sie in der Sitzung, die geöffnet wird, einmal die **EINGABETASTE**, um eine Eingabeaufforderung zu öffnen.

3. Sie werden aufgefordert, die Sprache auszuwählen, die für Ihr Gerät festgelegt werden soll. Geben Sie die Sprache an, und drücken Sie dann die **EINGABETASTE**.

4. Wählen Sie im angezeigten Menü der seriellen Konsole Option 1 aus, also die **Anmeldung mit Vollzugriff**.
     Führen Sie die Schritte 5 bis 12 aus, um die mindestens erforderlichen Netzwerkeinstellungen für Ihr Gerät zu konfigurieren. **Diese Konfigurationsschritte müssen für den aktiven Controller des Geräts ausgeführt werden.** Das Menü der seriellen Konsole gibt den Zustand des Controllers in der Bannermeldung an. Wenn Sie nicht mit dem aktiven Controller verbunden sind, trennen Sie die Verbindung, und stellen Sie dann eine Verbindung mit dem aktiven Controller her.

5. Geben Sie an der Eingabeaufforderung Ihr Kennwort ein. Das Standardkennwort für das Gerät lautet **Password1**.

6. Geben Sie den folgenden Befehl ein: `Invoke-HcsSetupWizard`

7. Es wird ein Installations-Assistent angezeigt, der Sie beim Konfigurieren der Netzwerkeinstellungen für das Gerät unterstützt. Geben Sie die folgenden Informationen an:
   
   * IP-Adresse für die Netzwerkschnittstelle DATA 0
   * Subnetzmaske
   * Gateway
   * IP-Adresse für den primären DNS-Server

   Unten ist eine Beispielausgabe dargestellt.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    In der obigen Beispielausgabe sehen Sie, dass vom System nach jedem Schritt des Prozesses eine Überprüfung der Netzwerkeinstellungen durchgeführt wird.

     > [!NOTE]
     > Sie müssen ggf. einige Minuten warten, bis die Subnetzmaske und die DNS-Einstellungen übernommen werden. Wenn die Fehlermeldung "Überprüfen Sie die Netzwerkverbindung mit DATA 0" angezeigt wird, überprüfen Sie die physische Netzwerkverbindung für die Netzwerkschnittstelle DATA 0 Ihres aktiven Controllers.

8. (Optional) Konfigurieren Sie Ihren Webproxyserver. Die Webproxykonfiguration ist optional. **Achten Sie jedoch bei Verwendung eines Webproxys darauf, dass dieser nur hier konfiguriert werden kann**. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
9. Konfigurieren Sie einen primären NTP-Server für Ihr Gerät NTP-Server sind für die Zeitsynchronisierung erforderlich, damit Ihr Gerät beim Clouddienstanbieter authentifiziert werden kann. Stellen Sie sicher, dass Ihr Netzwerk NTP-Datenverkehr vom Rechenzentrum ins Internet zulässt. Wenn dies nicht möglich ist, geben Sie einen internen NTP-Server an.

    Nachfolgend sehen Sie eine Beispielausgabe.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Aus Sicherheitsgründen läuft das Administratorkennwort für das Gerät nach der ersten Sitzung ab, und Sie müssen es jetzt ändern. Geben Sie, wenn Sie dazu aufgefordert werden, ein Administratorkennwort für das Gerät an. Ein gültiges Administratorkennwort für das Gerät muss zwischen 8 und 15 Zeichen lang sein. Das Kennwort muss aus drei der folgenden Zeichenkategorien bestehen: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. Der letzte Schritt im Installations-Assistenten besteht im Registrieren Ihres Geräts beim StorSimple-Geräte-Manager-Dienst. Zu diesem Zweck benötigen Sie den Dienstregistrierungsschlüssel, den Sie in Schritt 2 abgerufen haben. Nachdem Sie den Registrierungsschlüssel bereitgestellt haben, müssen Sie ggf. einige Minuten warten, bis das Gerät registriert wurde.
    
    > [!NOTE]
    > Sie können jederzeit STRG+C drücken, um den Installations-Assistenten zu beenden. Wenn Sie alle Netzwerkeinstellungen (IP-Adresse für DATA 0, Subnetzmaske und Gateway) angegeben haben, werden Ihre Einträge beibehalten.
    
    Nachfolgend sehen Sie eine Beispielausgabe.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Nachdem das Gerät registriert wurde, wird ein Schlüssel für die Dienstdatenverschlüsselung angezeigt. Kopieren Sie diesen Schlüssel, und bewahren Sie ihn an einem sicheren Ort auf. **Dieser Schlüssel ist mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte beim StorSimple-Geräte-Manager-Dienst erforderlich.** Weitere Informationen zu diesem Schlüssel finden Sie unter [StorSimple-Sicherheit](../articles/storsimple/storsimple-8000-security.md) .
    
    ![StorSimple – Registrieren des Geräts 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Markieren Sie zum Kopieren des Texts aus dem Fenster der seriellen Konsole den Text einfach. Sie sollten damit in der Lage sein, ihn in die Zwischenablage oder einen beliebigen Texteditor zu kopieren. Verwenden Sie NICHT STRG+C zum Kopieren des Schlüssels für die Dienstdatenverschlüsselung. STRG+C bewirkt, dass der Installations-Assistent beendet wird. Dies führt dazu, dass das Geräteadministratorkennwort nicht geändert und das Gerät auf das Standardkennwort zurückgesetzt wird.
    
13. Schließen Sie die serielle Konsole.
14. Kehren Sie zum Azure-Portal zurück, und führen Sie die folgenden Schritte aus:
    
    1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst.
    2. Klicken Sie auf **Geräte**.
    3. Vergewissern Sie sich in der Tabelle mit den Geräten, dass das Gerät erfolgreich mit dem Dienst verbunden wurde, indem Sie seinen Status überprüfen. Der Gerätestatus sollte **Zur Einrichtung bereit** lauten.
       
        ![StorSimple – Seite "Geräte"](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Unter Umständen müssen Sie einige Minuten warten, bis sich der Gerätestatus in **Zur Einrichtung bereit** ändert.
       
        Falls das Gerät in dieser Liste nicht angezeigt wird, sollten Sie sicherstellen, dass Ihr Firewallnetzwerk wie unter [Netzwerkanforderungen für Ihr StorSimple-Gerät](../articles/storsimple/storsimple-8000-system-requirements.md) beschrieben konfiguriert wurde. Stellen Sie sicher, dass Port 9354 für die ausgehende Kommunikation geöffnet ist, da dieser Port vom Service Bus für die Kommunikation zwischen dem StorSimple-Geräte-Manager-Dienst und Geräten verwendet wird.