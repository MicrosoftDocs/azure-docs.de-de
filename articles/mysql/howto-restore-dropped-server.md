---
title: Wiederherstellen eines getrennten Azure Database for MySQL-Servers
description: In diesem Artikel wird beschrieben, wie Sie mit dem Azure-Portal einen getrennten Server in Azure Database for MySQL wiederherstellen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 982747c1a7e093f84daeb63e75cfdf439d3fccf9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546720"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>Wiederherstellen eines getrennten Azure Database for MySQL-Servers

Wenn ein Server getrennt wird, kann die Datenbankserversicherung bis zu fünf Tage im Dienst aufbewahrt werden. Auf die Datenbanksicherung kann nur über das Azure-Abonnement zugegriffen werden, zu dem der Server ursprünglich gehörte. Und nur über dieses Abonnement kann die Datenbanksicherung auch wiederhergestellt werden. Die folgenden empfohlenen Schritte können ausgeführt werden, um eine gelöschte MySQL-Serverressource innerhalb von 5 Tagen ab dem Zeitpunkt des Löschens des Servers wiederherzustellen. Die empfohlenen Schritte funktionieren nur, wenn die Sicherung für den Server weiterhin verfügbar ist und nicht aus dem System gelöscht wurde. 

## <a name="pre-requisites"></a>Voraussetzungen
Zum Wiederherstellen eines getrennten Azure Database for MySQL-Servers benötigen Sie Folgendes:
- Name des Azure-Abonnements, das den ursprünglichen Server gehostet hat
- Speicherort, an dem der Server erstellt wurde

## <a name="steps-to-restore"></a>Schritte zum Wiederherstellen

1. Wechseln Sie im Azure-Portal auf dem Blatt „Überwachen“ zum [Aktivitätsprotokoll](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). 

2. Klicken Sie im „Aktivitätsprotokoll“ wie hier gezeigt auf **Filter hinzufügen** , und legen Sie die folgenden Filter fest für 

    - **Abonnement** = Ihr Abonnement, das den gelöschten Server hostet
    - **Ressourcentyp** = Azure Database for MySQL-Server (Microsoft.DBforMySQL/servers) 
    - **Vorgang** = MySQL-Server löschen (Microsoft.DBforMySQL/servers/delete) 
 
     [![Für Löschen des MySQL-Servers gefiltertes Aktivitätsprotokoll](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Doppelklicken Sie auf das „MySQL-Server löschen“-Ereignis, klicken Sie auf die Registerkarte „JSON“, und notieren Sie sich die Attribute „resourceId“ und „submissionTimestamp“ in der JSON-Ausgabe. Die „resourceId“ weist das folgende Format auf: „/Abonnements/ffffffff-ffff-ffff-ffff-ffffffffffff/Ressourcengruppe/Zielressourcengruppe/Anbieter/Microsoft.DBforMySQL/Server/gelöschterServer“.
 
 4. Wechseln Sie zur Seite [Server – Erstellen](/rest/api/mysql/servers/create), klicken Sie auf die grün hervorgehobene Registerkarte „Ausprobieren“, und melden Sie sich mit Ihrem Azure-Konto an.
 
 5. Geben Sie „resourceGroupName“, „serverName“ (gelöschter Servername) und „subscriptionId“ an, abgeleitet aus dem in Schritt 3 aufgezeichneten resourceId-Attribut, während „api-version“, wie im Bild gezeigt, bereits eingetragen ist.
 
     [![Erstellen eines Servers mit der REST-API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Scrollen Sie im Abschnitt „Anforderungstext“ nach unten, und fügen Sie das Folgende für „Speicherort des gelöschten Servers“, „submissionTimestamp“ und „resourceId“ ein. Geben Sie für „restorePointinTime“ den Wert „submissionTimestamp“ minus **15 Minuten** an, um sicherzustellen, dass der Befehl nicht fehlerhaft ist.
 
    ```json
        {
          "location": "Dropped Server Location",  
          "properties": 
              {
                  "restorePointInTime": "submissionTimestamp - 15 minutes",
                  "createMode": "PointInTimeRestore",
                  "sourceServerId": "resourceId"
            }
        }
    ```

7. Wenn Sie „Antwortcode 201“ oder 202 sehen, wurde die Wiederherstellungsanforderung erfolgreich übermittelt. 

8. Die Servererstellung kann abhängig von der Datenbankgröße und den Computeressourcen, die auf dem ursprünglichen Server bereitgestellt werden, eine Weile dauern. Der Wiederherstellungsstatus kann über das Aktivitätsprotokoll überwacht werden durch filtern nach 
   - **Abonnement** = Ihr Abonnement
   - **Ressourcentyp** = Azure Database for MySQL-Server (Microsoft.DBforMySQL/servers) 
   - **Vorgang**  = Update MySQL Server Create

## <a name="next-steps"></a>Nächste Schritte
- Wenn Sie versuchen, einen Server innerhalb von fünf Tagen wiederherzustellen, und nach dem genauen Ausführen der zuvor beschriebenen Schritte immer noch eine Fehlermeldung angezeigt wird, öffnen Sie einen Supportfall, um Unterstützung zu erhalten. Wenn Sie versuchen, einen getrennten Server nach fünf Tagen wiederherzustellen, wird ein Fehler erwartet, da die Sicherungsdatei nicht gefunden werden kann. Eröffnen Sie in diesem Szenario kein Supportticket. Das Supportteam kann keine Unterstützung bieten, wenn die Sicherung aus dem System gelöscht worden ist. 
- Um das versehentliche Löschen von Servern zu verhindern, sollten Sie unbedingt [Ressourcensperren](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222) verwenden.