---
title: Aktualisieren der Mongo-Version Ihrer Azure Cosmos DB-API für das MongoDB-Konto
description: Informationen, wie Sie die MongoDB Wire Protocol-Version für Ihre vorhandene Azure Cosmos DB-API für MongoDB-Konten nahtlos aktualisieren können
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: chrande
ms.openlocfilehash: 9ce444e41d19ece984071d0f62e705a09d5f23c9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356449"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Aktualisieren der MongoDB Wire Protocol-Version Ihrer Azure Cosmos DB-API für das MongoDB-Konto
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In diesem Artikel wird beschrieben, wie Sie die Wire Protocol-Version Ihrer Azure Cosmos DB-API für das MongoDB-Konto aktualisieren. Nachdem Sie die Wire Protocol-Version aktualisiert haben, können Sie die neueste Funktionalität in der Azure Cosmos DB-API für MongoDB verwenden. Der Upgradeprozess unterbricht die Verfügbarkeit Ihres Kontos nicht und verbraucht zu keinem Zeitpunkt RU/s oder verringert die Kapazität der Datenbank. Dieser Prozess wirkt sich nicht auf vorhandene Daten oder Indizes aus.

>[!Note]
> Zurzeit können nur qualifizierende Konten, die die Serverversion 3.2 verwenden, auf Version 3.6 aktualisiert werden. Wenn Ihr Konto die Upgradeoption nicht anzeigt, [erstellen Sie ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Upgrade von Version 3.2 auf 3.6

### <a name="benefits-of-upgrading-to-version-36"></a>Vorteile des Upgrades auf Version 3.6

Die folgenden neuen Features sind in Version 3.6 enthalten:
- Verbesserte Leistung und Stabilität
- Unterstützung für neue Datenbankbefehle
- Standardunterstützung für Aggregationspipeline und neue Aggregationsphasen
- Unterstützung für das Ändern von Datenströmen
- Unterstützung für zusammengesetzte Indizes
- Partitionsübergreifende Unterstützung für die folgenden Vorgänge: Aktualisieren, Löschen, Zählen und Sortieren
- Verbesserte Leistung für die folgenden zusammengesetzten Operationen: „$count“, „$skip“, „$limit“ und „$group“
- Platzhalterindizierung wird jetzt unterstützt

### <a name="changes-from-version-32"></a>Änderungen gegenüber Version 3.2

- **„RequestRateIsLarge“-Fehler wurden entfernt**. Anforderungen aus der Clientanwendung geben keine „16500“-Fehler mehr zurück. Stattdessen werden Anforderungen so lange fortgesetzt, bis sie abgeschlossen sind oder das Timeout erfüllen.
- Das Pro-Anforderungstimeout ist auf 60 Sekunden festgelegt.
- Bei MongoDB-Sammlungen, die in der neuen Wire Protocol-Version erstellt wurden, wird nur die Eigenschaft `_id` standardmäßig indiziert.

### <a name="action-required"></a>Erforderliche Aktion

Für das Upgrade auf Version 3.6 wird das Endpunktsuffix des Datenbankkontos auf das folgende Format aktualisiert:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Sie müssen den vorhandenen Endpunkt in Ihren Anwendungen und Treibern ersetzen, die eine Verbindung mit diesem Datenbankkonto herstellen. **Nur Verbindungen, die den neuen Endpunkt verwenden, können auf die Features in der MongoDB-Version 3.6 zugreifen**. Der vorherige Endpunkt sollte das Suffix `.documents.azure.com`haben.

>[!Note]
> Dieser Endpunkt weist möglicherweise geringfügige Unterschiede auf, wenn Ihr Konto in einer unabhängigen, behördlichen oder eingeschränkten Azure-Cloud erstellt wurde.

### <a name="how-to-upgrade"></a>Aktualisieren

1. Wechseln Sie zuerst zum Azure-Portal, und navigieren Sie zu Ihrer Azure Cosmos DB-API für das Übersichtsblatt zum MongoDB-Konto. Überprüfen Sie, ob die Serverversion `3.2` lautet. 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Azure-Portal mit MongoDB-Konto – Übersicht" border="false":::

2. Wählen Sie in den Optionen auf der linken Seite das Blatt `Features` aus. Dadurch werden die Features auf Kontoebene angezeigt, die für Ihr Datenbankkonto zur Verfügung stehen.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Übersicht über das Azure-Portal mit dem MongoDB-Konto; das Blatt „Features“ ist hervorgehoben" border="false":::

3. Klicken Sie auf die Zeile `Upgrade to Mongo server version 3.6`. Wenn diese Option nicht angezeigt wird, ist Ihr Konto für dieses Upgrade möglicherweise nicht qualifiziert. Wenn das zutrifft, erstellen Sie [ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Blatt „Features“ mit Optionen." border="false":::

4. Überprüfen Sie die angezeigten Informationen zu diesem spezifischen Upgrade. Beachten Sie, dass das Upgrade erst abgeschlossen wird, wenn Ihre Anwendungen den aktualisierten Endpunkt verwenden, wie in diesem Abschnitt beschrieben wird. Sobald Sie zum Starten des Prozesses bereit sind, klicken Sie auf `Enable`.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Erweiterte Upgradeanleitung." border="false":::

5. Nachdem der Prozess gestartet wurde, wird im Menü `Features` der Status des Upgrades angezeigt. Der Status wechselt von `Pending` über `In Progress` zu `Upgraded`. Dieser Prozess wirkt sich nicht auf die vorhandenen Funktionen oder Vorgänge des Datenbankkontos aus.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Upgradestatus nach dem Initiieren." border="false":::

6. Sobald das Upgrade abgeschlossen ist, wird der Status als `Upgraded` angezeigt. Klicken Sie darauf, um weitere Informationen zu den nächsten Schritten und Aktionen zu erhalten, die Sie zum Abschließen des Prozesses ausführen müssen. Wenn beim Verarbeiten Ihrer Anforderung ein Problem aufgetreten ist, [wenden Sie sich an den Support](https://azure.microsoft.com/en-us/support/create-ticket/).

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Kontostatus „Upgraded“." border="false":::

7. Wenn Sie mit der **Verwendung der aktualisierten Version Ihres Datenbankkontos beginnen** möchten, wechseln Sie zurück zum Blatt `Overview`, und kopieren Sie die neue Verbindungszeichenfolge für den Einsatz in Ihrer Anwendung. Die Anwendungen beginnen mit der Verwendung der aktualisierten Version, sobald sie eine Verbindung mit dem neuen Endpunkt herstellen. Vorhandene Verbindungen werden nicht unterbrochen und können jederzeit aktualisiert werden. Zur Gewährleistung eines konsistenten Verhaltens müssen alle Ihre Anwendungen den neuen Endpunkt verwenden.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Neues Übersichtsblatt." border="false":::

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die unterstützten und nicht unterstützten [Features von MongoDB, Version 3.6](mongodb-feature-support-36.md).
- Weitere Informationen finden Sie unter [Features der Mongo-Version 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
