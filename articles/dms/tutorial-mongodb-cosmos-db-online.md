---
title: 'Tutorial: Onlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von einer lokalen MongoDB-Instanz zur Azure Cosmos DB-API für MongoDB durchführen.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-nov-2020
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 359f268f69918ccfd9fe34a28c3f8d1c79988393
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575613"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Tutorial: Onlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB mit DMS

Sie können Azure Database Migration Service zum Durchführen einer Onlinemigration (minimale Ausfallzeit) von Datenbanken aus einer lokalen oder cloudbasierten MongoDB-Instanz zur Azure Cosmos DB-API für MongoDB verwenden.

Dieses Tutorial veranschaulicht die Schritte, die mit der Verwendung von Azure Database Migration Service verbunden sind, um MongoDB-Daten zu Azure Cosmos DB zu migrieren:
> [!div class="checklist"]
> 
> * Erstellen einer Instanz von Azure Database Migration Service 
> * Erstellen Sie ein Migrationsprojekt. 
> * Angeben der Quelle 
> * Angeben des Ziels 
> * Zuordnen zu Zieldatenbanken 
> * Ausführen der Migration 
> * Überwachen der Migration 
> * Überprüfen der Daten in Azure Cosmos DB 
> * Fertigstellen der Migration, wenn Sie dazu bereit sind 

In diesem Tutorial migrieren Sie ein Dataset in MongoDB, das auf einem virtuellen Azure-Computer gehostet wird, mit Azure Database Migration Service zur Azure Cosmos DB-API für MongoDB. Hierbei kommt es nur zu minimaler Ausfallzeit. Wenn Sie noch keine MongoDB-Quelle eingerichtet haben, lesen Sie den Artikel [Installieren und Konfigurieren von MongoDB auf einem virtuellen Windows-Computer in Azure](../virtual-machines/windows/install-mongodb.md).

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs.

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird eine Onlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB beschrieben. Informationen zu einer Offlinemigration finden Sie unter [Offlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB mit DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Führen Sie die Schritte zur Migrationsvorbereitung](../cosmos-db/mongodb-pre-migration.md) aus. Schätzen Sie beispielsweise den Durchsatz, und wählen Sie einen Partitionsschlüssel und die Indizierungsrichtlinie aus.
* [Erstellen Sie eine Azure Cosmos DB-API für das MongoDB-Konto](https://ms.portal.azure.com/#create/Microsoft.DocumentDB), und stellen Sie sicher, dass [SSR](../cosmos-db/prevent-rate-limiting-errors.md) (Server-Side Retry, serverseitige Wiederholung) aktiviert ist.
* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > * Speicherendpunkt
    > * Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.

* Vergewissern Sie sich, dass die folgenden Kommunikationsports nicht durch Regeln für Netzwerksicherheitsgruppen (NSGs) des virtuellen Netzwerks blockiert werden: 53, 443, 445, 9354 und 10000-20000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf den MongoDB-Quellserver zugreifen kann (standardmäßig TCP-Port 27017).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Konfigurieren serverseitiger Wiederholungen für Azure Cosmos DB für eine effiziente Migration

Kunden, die von MongoDB zu Azure Cosmos DB migrieren, profitieren von Ressourcengovernancefunktionen, die die vollständige Nutzung Ihres bereitgestellten Durchsatzes (RU/s) garantieren. Von Azure Cosmos DB können im Laufe der Migration ggf. Data Migration Service-Anforderungen gedrosselt werden, wenn bei ihnen die vom Container bereitgestellte Menge an RU/s überschritten wird. In diesem Fall muss die Anforderung wiederholt werden. Data Migration Service kann zwar Wiederholungsversuche durchführen, die Roundtripzeit für den Netzwerkhop zwischen Data Migration Service und Azure Cosmos DB wirkt sich allerdings insgesamt nachteilig auf die Antwortzeit der Anforderung aus. Durch die Verbesserung der Antwortzeit für gedrosselte Anforderungen kann die Gesamtdauer der Migration verkürzt werden. Das Feature *Serverseitige Wiederholung* von Azure Cosmos DB ermöglicht es dem Dienst, drosselungsbedingte Fehlercodes abzufangen und eine Wiederholung mit wesentlich kürzerer Roundtripzeit durchzuführen, wodurch sich die Antwortzeiten von Anforderungen erheblich verbessern.

Die Funktion „Serverseitige Wiederholung“ (Server Side Retry, SSR) finden Sie im Azure Cosmos DB-Portal auf dem Blatt *Features*.

![Screenshot: Serverseitige Wiederholung von MongoDB](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-feature.png)

Ist das Feature *deaktiviert*, wird empfohlen, es wie im Anschluss gezeigt zu aktivieren:

![Screenshot: Aktivieren der serverseitigen Wiederholung von MongoDB](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie dann auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Erstellen einer Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten.

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

   Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die MongoDB-Quellinstanz und auf das Azure Cosmos DB-Zielkonto.

   Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

6. Wählen Sie eine SKU aus dem Premium-Tarif aus.

    > [!NOTE]
    > Onlinemigrationen werden nur bei Verwendung des Premium-Tarifs unterstützt. Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

    ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Suchen Sie auf dem Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

    Alternativ hierzu können Sie die Azure Database Migration Service-Instanz auch über den Suchbereich im Azure-Portal ermitteln.

    ![Verwenden des Suchbereichs im Azure-Portal](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Wählen Sie **+ Neues Migrationsprojekt** aus.

4. Geben Sie im Fenster **Neues Migrationsprojekt** einen Projektnamen ein, und wählen Sie im Textfeld **Quellservertyp** die Option **MongoDB** und im Textfeld **Zielservertyp** die Option **CosmosDB (MongoDB-API)** . Wählen Sie außerdem für **Aktivitätstyp auswählen** die Option **Onlinedatenmigration [Vorschauversion]** .

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Klicken Sie auf **Speichern**, und wählen Sie dann **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Source details** (Quelldetails) die Verbindungsdetails für die MongoDB-Server-Quellinstanz an.

   > [!IMPORTANT]
   > Azure Cosmos DB als Quelle wird von Azure Database Migration Service nicht unterstützt.

    Es gibt drei Modi, um eine Verbindung mit einer Quelle herzustellen:
   * **Standardmodus**, in dem ein vollqualifizierter Domänenname oder eine IP-Adresse, eine Portnummer und die Anmeldeinformationen für die Verbindung akzeptiert werden.
   * **Verbindungszeichenfolgen-Modus**, in dem eine MongoDB-Verbindungszeichenfolge akzeptiert wird. Dies ist im Artikel [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (URI-Format von Verbindungszeichenfolgen) beschrieben.
   * **Daten aus Azure-Speicher**, wobei eine Blobcontainer-SAS-URL akzeptiert wird. Aktivieren Sie die Option **Das Blob enthält BSON-Speicherabbilder**, wenn für den Blobcontainer vom MongoDB-Tool [bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) erstellte BSON-Speicherabbilder vorhanden sind. Deaktivieren Sie sie, wenn der Container JSON-Dateien enthält.

     Bei Verwendung dieser Option muss die Verbindungszeichenfolge für das Speicherkonto folgendes Format haben:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Beachten Sie außerdem den folgenden Aspekt (abhängig von der Art der Sicherungsinformationen in Azure Storage):

     * Bei BSON-Sicherungen müssen die Daten im Blobcontainer im bsondump-Format vorliegen, sodass Datendateien in Ordnern platziert werden, die nach den enthaltenden Datenbanken im Format „sammlung.bson“ benannt sind. Gegebenenfalls vorhandene Metadatendateien müssen im Format „*sammlung*.metadata.json“ benannt werden.

     * Bei JSON-Sicherungen müssen die Dateien im Blobcontainer in Ordnern platziert werden, die nach den enthaltenden Datenbanken benannt sind. Innerhalb der einzelnen Datenbankordner müssen Datendateien in einem Unterordner namens „data“ platziert und im Format „*sammlung*.json“ benannt werden. Gegebenenfalls vorhandene Metadaten müssen in einem Unterordner namens „metadata“ platziert und ebenfalls im Format „*sammlung*.json“ benannt werden. Die Metadatendateien müssen in dem Format vorliegen, das vom MongoDB-Tool „bsondump“ generiert wird.

    > [!IMPORTANT]
    > Es wird nicht empfohlen, ein selbstsigniertes Zertifikat auf dem Mongo-Server zu verwenden. Wenn dennoch eines verwendet wird, stellen Sie die Verbindung mit dem Server im **Verbindungszeichenfolgen-Modus** her, und stellen Sie sicher, dass Ihre Verbindungszeichenfolge in Anführungszeichen eingeschlossen ist.
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Sie können die IP-Adresse für Situationen verwenden, in denen eine DNS-Namensauflösung nicht möglich ist.

   ![Angeben von Quelldetails](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Wählen Sie **Speichern** aus.

   > [!NOTE]
   > Die Quellserveradresse sollte die Adresse des primären Replikats sein, wenn die Quelle eine Replikatgruppe ist, und die Adresse des Routers, wenn es sich bei der Quelle um einen MongoDB-Cluster mit Sharding handelt. Für einen MongoDB-Cluster mit Sharding muss Azure Database Migration Service eine Verbindung mit den einzelnen Shards im Cluster herstellen können, und hierzu kann es erforderlich sein, die Firewall auf weiteren Computern zu öffnen.

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Geben Sie auf dem Bildschirm **Zieldetails für die Migration** die Verbindungsdetails für das Azure Cosmos DB-Zielkonto an (also für die vorab bereitgestellte Azure Cosmos DB-API für das MongoDB-Konto, zu dem Sie die MongoDB-Daten migrieren).

    ![Angeben von Zieldetails](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Wählen Sie **Speichern** aus.

## <a name="map-to-target-databases"></a>Zuordnen zu Zieldatenbanken

1. Ordnen Sie im Bildschirm **Map to target databases** (Zu Zieldatenbanken zuordnen) die Quell- und die Zieldatenbank für die Migration einander zu.

   Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

   Wenn die Zeichenfolge **Erstellen** neben dem Datenbanknamen angezeigt wird, hat Azure Database Migration Service die Zieldatenbank nicht gefunden, und der Dienst erstellt die Datenbank für Sie.

   Geben Sie einen RU-Wert für den Durchsatz an, falls Sie an diesem Punkt der Migration den Durchsatz für die Datenbank freigeben möchten. In Cosmos DB können Sie den Durchsatz entweder auf Datenbankebene oder für jede Sammlung individuell bereitstellen. Der Durchsatz wird in [Anforderungseinheiten](../cosmos-db/request-units.md) (RUs) gemessen. Weitere Informationen: [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Zuordnen zu Zieldatenbanken](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Wählen Sie **Speichern** aus.

3. Erweitern Sie auf dem Einstellungsbildschirm **Sammlung** die Auflistung der Sammlungen, und überprüfen Sie dann die Liste der Sammlungen, die migriert werden.

   Azure Database Migration Service wählt automatisch alle Sammlungen aus, die in der MongoDB-Quellinstanz vorhanden sind, jedoch nicht im Azure Cosmos DB-Zielkonto. Wenn Sie Sammlungen, die bereits Daten enthalten, erneut migrieren möchten, müssen Sie die Sammlungen in diesem Fenster explizit auswählen.

   Sie können die Anzahl von RUs angeben, die die Sammlungen verwenden sollen. In den meisten Fällen ist ein Wert zwischen 500 (mindestens 1.000 für Sammlungen mit Sharding) und 4.000 ausreichend. Azure Database Migration Service schlägt intelligente Standardwerte basierend auf der Sammlungsgröße vor.

    > [!NOTE]
    > Führen Sie die Datenbankmigration und -sammlung parallel und ggf. unter Verwendung mehrerer Azure Database Migration Service-Instanzen aus, um die Ausführung zu beschleunigen.

   Sie können auch einen Shardschlüssel angeben, um die [Partitionierung in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) für eine optimale Skalierbarkeit zu nutzen. Überprüfen Sie unbedingt die [bewährten Methoden für die Auswahl eines Shard-/Partitionsschlüssels](../cosmos-db/partitioning-overview.md#choose-partitionkey). Wenn Sie nicht über einen Partitionsschlüssel verfügen, können Sie immer **_id** als Shardschlüssel verwenden, um den Durchsatz zu verbessern.

   ![Auswählen von Sammlungstabellen](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Wählen Sie **Speichern** aus.

5. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität an.

    ![Migrationszusammenfassung](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

   Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität wird angegeben.

   ![Aktivitätsstatus](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Überwachen der Migration

* Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Wird wiedergegeben** lautet.

   > [!NOTE]
   > Sie können die Aktivität zum Abrufen von Details der Migrationsmetriken auf Datenbank- und Sammlungsebene auswählen.

   ![Status „Wird wiedergegeben“ der Aktivität](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Überprüfen von Daten in Cosmos DB

1. Nehmen Sie Änderungen an Ihrer MongoDB-Quelldatenbank vor.
2. Stellen Sie eine Verbindung mit COSMOS DB her, um zu überprüfen, ob die Daten vom MongoDB-Quellserver repliziert werden.

    ![Der Screenshot zeigt, wo Sie überprüfen können, ob die Daten repliziert wurden.](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Fertigstellen der Migration

* Nachdem alle Dokumente von der Quelle auf dem COSMOS DB-Ziel verfügbar sind, können Sie im Kontextmenü der Migrationsaktivität die Option **Fertig stellen** wählen, um die Migration abzuschließen.

    Mit dieser Aktion wird die Wiedergabe aller ausstehenden Änderungen durchgeführt und die Migration fertiggestellt.

    ![Der Screenshot zeigt die Menüoption „Fertig stellen“.](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optimierung nach der Migration

Nach der Migration der in einer MongoDB-Datenbank gespeicherten Daten zur Azure Cosmos DB-API für MongoDB können Sie eine Verbindung mit Azure Cosmos DB herstellen und die Daten verwalten. Sie können nach der Migration auch andere Optimierungsschritte ausführen und beispielsweise die Indizierungsrichtlinie optimieren, die Standardkonsistenzebene aktualisieren oder die globale Verteilung für Ihr Azure Cosmos DB-Konto konfigurieren. Weitere Informationen finden Sie im Artikel zur [Optimierung nach der Migration](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cosmos DB-Dienstinformationen](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich in der Migrationsanleitung im [Leitfaden zur Datenbankmigration von Microsoft](https://datamigration.microsoft.com/) über weitere Szenarios.