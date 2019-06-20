---
title: include file
description: include file
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: 7c682105113dac7c1d457489cf926210ead77993
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177805"
---
1. Führen Sie die Installationsdatei für das einheitliche Setup aus.
2. Wählen Sie unter **Vorbereitung** die Option **Install the configuration server and process server** (Konfigurationsserver und Prozessserver installieren) aus.

    ![Vorbereitung](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. Klicken Sie unter **Third-Party Software License** (Drittanbietersoftwarelizenz) auf **I Accept** (Ich stimme zu), um MySQL herunterzuladen und zu installieren.

    ![Drittanbietersoftware](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. Wählen Sie unter **Registration** (Registrierung) den Registrierungsschlüssel aus, den Sie aus dem Tresor heruntergeladen haben.

    ![Registrierung](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Geben Sie unter **Internet Settings** (Interneteinstellungen) an, wie der auf dem Konfigurationsserver ausgeführte Anbieter eine Internetverbindung mit Azure Site Recovery herstellen soll. Stellen Sie sicher, dass Sie die erforderlichen URLs zugelassen haben.

    - Wenn die Verbindung über den derzeit auf dem Computer eingerichteten Proxy hergestellt werden soll, wählen Sie **Unter Verwendung eines Proxyservers mit Azure Site Recovery verbinden**.
    - Wenn der Anbieter eine direkte Verbindung herstellen soll, wählen Sie **Direkt mit Azure Site Recovery verbinden (ohne Proxyserver)** .
    - Falls für den vorhandenen Proxy eine Authentifizierung erforderlich ist oder Sie für die Anbieterverbindung einen benutzerdefinierten Proxy verwenden möchten, müssen Sie **Mit benutzerdefinierten Proxyeinstellungen verbinden** auswählen und Adresse, Port und Anmeldeinformationen angeben.
     ![Firewall](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Bei der **Voraussetzungsüberprüfung** führt das Setup eine Überprüfung durch, um sicherzustellen, dass die Installation ausgeführt werden kann. Falls beim **Überprüfen der Synchronisierung der globalen Zeit** eine Warnung angezeigt wird, stellen Sie sicher, dass die Zeit der Systemuhr (Einstellungen für **Datum und Uhrzeit**) mit der Zeitzone übereinstimmt.

    ![Voraussetzungen](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. Erstellen Sie unter **MySQL Configuration** (MySQL-Konfiguration) Anmeldeinformationen für die Anmeldung bei der MySQL-Serverinstanz, die installiert wird.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. Wählen Sie unter **Umgebungsdetails** die Option „Nein“ aus, wenn Sie Azure Stack-VMs oder physische Server replizieren. 
9. Wählen Sie unter **Install Location** (Installationsspeicherort) aus, wo die Binärdateien installiert werden sollen und wo der Cache gespeichert werden soll. Auf dem ausgewählten Laufwerk müssen mindestens 5 GB an Speicherplatz verfügbar sind, wir raten Ihnen jedoch zu einem Cachelaufwerk mit mindestens 600 GB freiem Speicherplatz.

    ![Installationsspeicherort](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. Wählen Sie in **Netzwerkauswahl** zunächst den Netzwerkadapter aus, den der integrierte Prozessserver für die Ermittlung und die Pushinstallation des Mobilitätsdiensts auf Quellcomputern verwendet. Wählen Sie anschließend den Netzwerkadapter aus, den der Konfigurationsserver zum Herstellen einer Verbindung mit Azure nutzt. Port 9443 ist der Standardport zum Senden und Empfangen von Replikationsdatenverkehr. Sie können diese Portnummer jedoch ändern, um sie an die Anforderungen Ihrer Umgebung anzupassen. Zusätzlich zum Port 9443 öffnen wir auch den Port 443, der von einem Webserver zum Orchestrieren von Replikationsvorgängen verwendet wird. Verwenden Sie den Port 443 nicht, um Replikationsdatenverkehr zu senden oder zu empfangen.

    ![Netzwerkauswahl](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Lesen Sie die unter **Summary** (Zusammenfassung) angezeigten Informationen und klicken Sie auf **Install** (Installieren). Nach Abschluss der Installation wird eine Passphrase generiert. Diese benötigen Sie bei der Aktivierung der Replikation. Kopieren Sie sie deshalb, und bewahren Sie sie an einem sicheren Ort auf.

    ![Zusammenfassung](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Nach Abschluss der Registrierung wird der Server auf dem Blatt **Einstellungen** > **Server** im Tresor angezeigt.
