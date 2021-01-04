---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008474"
---
1. Richten Sie eine Remotedesktopverbindung mit dem Computer ein, auf dem der Prozessserver ausgeführt wird. 
2. Führen Sie „cspsconfigtool.exe“ aus, um das Tool für die Konfiguration des Azure Site Recovery-Prozessservers zu starten.
    - Das Tool wird automatisch gestartet, wenn Sie sich zum ersten Mal am Prozessserver anmelden.
    - Klicken Sie auf dem Desktop auf die Verknüpfung, falls das Öffnen nicht automatisch erfolgt.

3. Geben Sie unter **Configuration server FQDN or IP** (FQDN oder IP für Konfigurationsserver) den Namen oder die IP-Adresse des Konfigurationsservers an, über den der Prozessserver registriert werden soll.
4. Stellen Sie sicher, dass unter **Configuration Server Port** (Port des Konfigurationsservers) der Wert „443“ angegeben ist. Dies ist der Port, an dem der Konfigurationsserver auf Anforderungen lauscht.
5. Geben Sie unter **Connection Passphrase** (Passphrase für Verbindung) die Passphrase an, die Sie beim Einrichten des Konfigurationsservers festgelegt haben. Suchen Sie wie folgt nach der Passphrase:
    -  Navigieren Sie auf dem Konfigurationsserver zum Site Recovery-Installationsordner * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Führen Sie den folgenden Befehl aus, um die aktuelle Passphrase auszugeben:
    ```
    genpassphrase.exe -n
    ```

6. Übernehmen Sie unter **Data Transfer Port** (Port für Datenübertragung) den Standardwert, sofern Sie keinen benutzerdefinierten Port angegeben haben.

7. Klicke Sie auf **Save** (Speichern), und registrieren Sie den Prozessserver.

    
    ![Registrieren des Prozessservers](./media/site-recovery-vmware-register-process-server/register-ps.png)
