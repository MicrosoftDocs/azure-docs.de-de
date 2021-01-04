---
title: Wiederherstellen eines gelöschten Azure Database for PostgreSQL-Servers
description: In diesem Artikel wird beschrieben, wie Sie einen gelöschten Server in Azure Database for PostgreSQL über das Azure-Portal wiederherstellen.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 81764294cc29ad74d5a77f2055f10498d69b59e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342813"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Wiederherstellen eines gelöschten Azure Database for PostgreSQL-Servers

Wenn ein Server getrennt wird, kann die Datenbankserversicherung bis zu fünf Tage im Dienst aufbewahrt werden. Auf die Datenbanksicherung kann nur über das Azure-Abonnement zugegriffen werden, zu dem der Server ursprünglich gehörte. Und nur über dieses Abonnement kann die Datenbanksicherung auch wiederhergestellt werden. Die folgenden empfohlenen Schritte können ausgeführt werden, um eine gelöschte PostgreSQL-Serverressource innerhalb von fünf Tagen ab dem Zeitpunkt der Serverlöschung wiederherzustellen. Die empfohlenen Schritte funktionieren nur, wenn die Sicherung für den Server weiterhin verfügbar ist und nicht aus dem System gelöscht wurde. 

## <a name="pre-requisites"></a>Voraussetzungen
Zum Wiederherstellen eines gelöschten Azure Database for PostgreSQL-Servers benötigen Sie Folgendes:
- Name des Azure-Abonnements, das den ursprünglichen Server gehostet hat
- Speicherort, an dem der Server erstellt wurde

## <a name="steps-to-restore"></a>Schritte zum Wiederherstellen

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Wählen Sie den Dienst **Azure Monitor** und anschließend **Aktivitätsprotokoll** aus.

2. Klicken Sie im Aktivitätsprotokoll wie hier gezeigt auf **Filter hinzufügen** , und legen Sie die Filter wie folgt fest:

    - **Abonnement** = Ihr Abonnement, das den gelöschten Server hostet
    - **Ressourcentyp** : Azure Database for PostgreSQL-Server (Microsoft.DBforPostgreSQL/servers)
    - **Vorgang** : PostgreSQL-Server löschen (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![Nach Löschvorgang für PostgreSQL-Server gefiltertes Aktivitätsprotokoll](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Wählen Sie das Ereignis für **PostgreSQL-Server löschen** und anschließend die Registerkarte **JSON** aus. Kopieren Sie die Attribute `resourceId` und `submissionTimestamp` in der JSON-Ausgabe. Die Ressourcen-ID (resourceId) hat das folgende Format: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver`.


 4. Navigieren Sie zur [PostgreSQL-Seite der REST-API für die Servererstellung](/rest/api/PostgreSQL/servers/create), und wählen Sie die grün hervorgehobene Option **Jetzt testen** aus. Melden Sie sich mit Ihrem Azure-Konto an.

 5. Geben Sie Werte für die Eigenschaften **resourceGroupName** , **serverName** (Name des gelöschten Servers) und **subscriptionId** an (basierend auf dem JSON-Wert für das Attribut „resourceId“, das Sie zuvor in Schritt 3 erfasst haben). Die Eigenschaft „api-version“ wurde vorab aufgefüllt und kann unverändert bleiben, wie in der folgenden Abbildung zu sehen:

    ![Erstellen eines Servers mit der REST-API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
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
    Ein Beispiel: Wenn die aktuelle Zeit „2020-11-02T23:59:59.0000000Z“ lautet, empfiehlt es sich, einen Zeitpunkt zu wählen, der mindestens 15 Minuten vor dem Wiederherstellungszeitpunkt (2020-11-02T23:44:59.0000000Z) liegt.
    > [!Important]
    > Nach dem Löschen des Servers beginnt ein Zeitlimit von fünf Tagen. Nach fünf Tagen tritt erwartungsgemäß ein Fehler auf, da die Sicherungsdatei nicht gefunden wird.
    
7. Wenn Sie „Antwortcode 201“ oder 202 sehen, wurde die Wiederherstellungsanforderung erfolgreich übermittelt. 

    Die Servererstellung kann abhängig von der Datenbankgröße und den Computeressourcen, die auf dem ursprünglichen Server bereitgestellt werden, eine Weile dauern. Der Wiederherstellungsstatus kann über das Aktivitätsprotokoll überwacht werden durch filtern nach 
   - **Abonnement** = Ihr Abonnement
   - **Ressourcentyp** : Azure Database for PostgreSQL-Server (Microsoft.DBforPostgreSQL/servers) 
   - **Vorgang** : „Update PostgreSQL Server Create“ (PostgreSQL-Servererstellung aktualisieren)

## <a name="next-steps"></a>Nächste Schritte
- Wenn Sie versuchen, einen Server innerhalb von fünf Tagen wiederherzustellen, und nach dem genauen Ausführen der zuvor beschriebenen Schritte immer noch eine Fehlermeldung angezeigt wird, öffnen Sie einen Supportfall, um Unterstützung zu erhalten. Wenn Sie versuchen, einen getrennten Server nach fünf Tagen wiederherzustellen, wird ein Fehler erwartet, da die Sicherungsdatei nicht gefunden werden kann. Eröffnen Sie in diesem Szenario kein Supportticket. Das Supportteam kann keine Unterstützung bieten, wenn die Sicherung aus dem System gelöscht worden ist. 
- Um das versehentliche Löschen von Servern zu verhindern, sollten Sie unbedingt [Ressourcensperren](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222) verwenden.
