---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 31e61069c95be9bd1c7a684bb83ebcd93bcb14be
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019136"
---
1. Starten von „UnifiedSetup.exe“ für Azure Site Recovery
2. Wählen Sie unter **Vorbereitung** die Option **Weitere Prozessserver zum Aufskalieren der Bereitstellung hinzufügen** aus.

   ![Prozessserver hinzufügen](./media/site-recovery-add-process-server/ps-page-1.png)

3. Geben Sie unter **Configuration Server Details** (Details zum Konfigurationsserver) die IP-Adresse des Konfigurationsservers und die Passphrase an.

   ![Prozessserver hinzufügen 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. Geben Sie unter **Internet Settings** (Interneteinstellungen) an, wie der auf dem Konfigurationsserver ausgeführte Anbieter eine Internetverbindung mit Azure Site Recovery herstellen soll.

   ![Prozessserver hinzufügen 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Wenn die Verbindung über den derzeit auf dem Computer eingerichteten Proxy hergestellt werden soll, wählen Sie **Connect with existing proxy settings** (Mit vorhandenen Proxyeinstellungen verbinden) aus.
   * Wenn der Anbieter eine direkte Verbindung herstellen soll, wählen Sie **Connect directly without a proxy** (Ohne Proxy direkt verbinden) aus.
   * Falls für den vorhandenen Proxy eine Authentifizierung erforderlich ist oder Sie für die Anbieterverbindung einen benutzerdefinierten Proxy verwenden möchten, wählen Sie **Mit benutzerdefinierten Proxyeinstellungen verbinden** aus.

     * Bei Verwendung eines benutzerdefinierten Proxys müssen Sie die Adresse, den Port und die Anmeldeinformationen eingeben.
     * Wenn Sie einen Proxy verwenden, sollten Sie den Zugriff auf die Dienst-URLs bereits zugelassen haben.

5. Bei der **Voraussetzungsüberprüfung** führt das Setup eine Überprüfung durch, um sicherzustellen, dass die Installation ausgeführt werden kann. Falls beim **Überprüfen der Synchronisierung der globalen Zeit** eine Warnung angezeigt wird, stellen Sie sicher, dass die Zeit der Systemuhr (Einstellungen für **Datum und Uhrzeit**) mit der Zeitzone übereinstimmt.

     ![Prozessserver hinzufügen 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. Wählen Sie unter **Umgebungsdetails** aus, ob Sie VMware-VMs replizieren werden. Wenn dies der Fall ist, überprüft das Setup, ob PowerCLI 6.0 installiert ist.

     ![Prozessserver hinzufügen 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. Wählen Sie unter **Install Location** (Installationsspeicherort) aus, wo die Binärdateien installiert werden sollen und wo der Cache gespeichert werden soll. Auf dem ausgewählten Laufwerk müssen mindestens 5 GB an Speicherplatz verfügbar sind, wir raten Ihnen jedoch zu einem Cachelaufwerk mit mindestens 600 GB freiem Speicherplatz.
     ![Der Screenshot zeigt den Installationsspeicherort für die Binärdateien und den Cachespeicher.](./media/site-recovery-add-process-server/ps-page-6.png)

8. Geben Sie unter **Network Selection** (Netzwerkauswahl) den Listener (Netzwerkadapter und SSL-Port) an, über den der Konfigurationsserver Replikationsdaten sendet und empfängt. Port 9443 ist der Standardport zum Senden und Empfangen von Replikationsdatenverkehr. Sie können diese Portnummer jedoch ändern, um sie an die Anforderungen Ihrer Umgebung anzupassen. Zusätzlich zum Port 9443 öffnen wir auch den Port 443, der von einem Webserver zum Orchestrieren von Replikationsvorgängen verwendet wird. Verwenden Sie den Port 443 nicht, um Replikationsdatenverkehr zu senden oder zu empfangen.

     ![Prozessserver hinzufügen 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. Lesen Sie die unter **Summary** (Zusammenfassung) angezeigten Informationen und klicken Sie auf **Install** (Installieren). Nach Abschluss der Installation wird eine Passphrase generiert. Diese benötigen Sie bei der Aktivierung der Replikation. Kopieren Sie sie deshalb, und bewahren Sie sie an einem sicheren Ort auf.

     ![Prozessserver hinzufügen 7](./media/site-recovery-add-process-server/ps-page-8.png)
