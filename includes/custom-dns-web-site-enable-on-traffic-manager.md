---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 67b9c0ba2566206b0e70db51844b21e5d5d3c261
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178668"
---
Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, sollten Sie den Browser verwenden können, um zu überprüfen, dass Ihr benutzerdefinierter Domänenname für den Zugriff auf die Web-App in Azure App Service verwendet werden kann.

> [!NOTE]
> Es kann einige Zeit dauern, bis Ihr CNAME über das DNS-System weitergegeben wurde. Mithilfe eines Diensts wie <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> können Sie überprüfen, ob der CNAME verfügbar ist.
> 
> 

Wenn Sie Ihre Web-App noch nicht als Traffic Manager-Endpunkt hinzugefügt haben, müssen Sie dies durchführen, bevor die Namensauflösung funktionieren kann, da der benutzerdefinierte Domänenname an Traffic Manager geleitet wird. Traffic Manager leitet dann zu Ihrer Web-App weiter. Verwenden Sie die Informationen unter [Hinzufügen und Löschen von Endpunkten](../articles/traffic-manager/traffic-manager-endpoints.md) , um dem Traffic Manager-Profil die Web-App als Endpunkt hinzuzufügen.

> [!NOTE]
> Wenn Ihre Web-App beim Hinzufügen eines Endpunkts nicht aufgeführt ist, überprüfen Sie, ob sie für den App Service-Planmodus **Standard** konfiguriert wurde. Sie müssen für Ihre Web-App den Modus **Standard** verwenden, damit sie mit Traffic Manager verwendet werden kann.
> 
> 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com)in Ihrem Browser.
2. Klicken Sie auf der Registerkarte **Web-Apps** auf den Namen der Web-App, wählen Sie **Einstellungen** aus, und wählen Sie anschließend **Benutzerdefinierte Domänen** aus.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Klicken Sie auf dem Blatt **Benutzerdefinierte Domänen** auf **Hostnamen hinzufügen**.
4. Verwenden Sie die Textfelder **Hostname** , um den Namen der Traffic Manager-Domäne einzugeben, die dieser Web-App zugeordnet werden soll.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Klicken Sie auf **Überprüfen** , um die Domänennamenkonfiguration zu speichern.
6. Nach dem Klicken auf **Überprüfen** startet Azure den Workflow zur Domänenüberprüfung. Dieser Workflow überprüft den Domänenbesitz sowie die Verfügbarkeit des Hostnamens und meldet die erfolgreiche Durchführung oder erstellt einen detaillierten Fehlerbericht mit Empfehlungen, wie der Fehler behoben werden kann.    
7. Nach erfolgreicher Überprüfung wird die Schaltfläche **Hostnamen hinzufügen** aktiv, und Sie können den Hostnamen zuweisen. Navigieren Sie jetzt in einem Browser zu Ihrer benutzerdefinierten Domäne. Sie sollten nun sehen, dass Ihre App mit Ihrem benutzerdefinierten Domänennamen ausgeführt wird. 
   
   Sobald die Konfiguration abgeschlossen ist, wird der benutzerdefinierte Domänenname im Abschnitt **Hostnamenbindungen** Ihrer Web-App aufgeführt.

Jetzt sollten Sie den Traffic Manager-Domänennamen in Ihren Browser eingeben können und auf diese Weise erfolgreich zu Ihrer Web-App gelangen.

