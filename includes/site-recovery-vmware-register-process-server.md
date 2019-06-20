---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178618"
---
1. Richten Sie eine Remotedesktopverbindung mit dem Computer ein, auf dem der Prozessserver ausgeführt wird. 
2. Führen Sie „cspsconfigtool.exe“ aus, um das Tool für die Konfiguration des Azure Site Recovery-Prozessservers zu starten.
    - Das Tool wird automatisch gestartet, wenn Sie sich zum ersten Mal am Prozessserver anmelden.
    - Klicken Sie auf dem Desktop auf die Verknüpfung, falls das Öffnen nicht automatisch erfolgt.

3. Geben Sie unter **Configuration server FQDN or IP** (FQDN oder IP für Konfigurationsserver) den Namen oder die IP-Adresse des Konfigurationsservers an, über den der Prozessserver registriert werden soll.
4. Stellen Sie sicher, dass unter **Configuration Server Port** (Port des Konfigurationsservers) der Wert „443“ angegeben ist. Dies ist der Port, an dem der Konfigurationsserver auf Anforderungen lauscht.
5. Geben Sie unter **Connection Passphrase** (Passphrase für Verbindung) die Passphrase an, die Sie beim Einrichten des Konfigurationsservers festgelegt haben. Suchen Sie wie folgt nach der Passphrase:
    -  Navigieren Sie auf dem Konfigurationsserver zum Site Recovery-Installationsordner * *\home\svssystems\bin\** . 
    - Führen Sie diesen Befehl aus: **genpassphrase.exe.n**. Der Speicherort der Passphrase wird angezeigt, damit Sie ihn sich notieren können.

6. Übernehmen Sie unter **Data Transfer Port** (Port für Datenübertragung) den Standardwert, sofern Sie keinen benutzerdefinierten Port angegeben haben.

7. Klicke Sie auf **Save** (Speichern), und registrieren Sie den Prozessserver.

    
    ![Registrieren des Prozessservers](./media/site-recovery-vmware-register-process-server/register-ps.png)
