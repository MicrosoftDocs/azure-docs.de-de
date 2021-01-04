---
title: Migrieren von Daten zum Azure Cosmos DB-Cassandra-API-Konto mithilfe von Striim
description: Hier erfahren Sie, wie Sie Daten mithilfe von Striim aus einer Oracle-Datenbank zu einem Azure Cosmos DB-Cassandra-API-Konto migrieren.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 64fb37fb4a57b3ee397ffe8814c76c7dca2fb9b3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334608"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migrieren von Daten zum Azure Cosmos DB-Cassandra-API-Konto mithilfe von Striim
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Das Striim-Image aus dem Azure Marketplace ermöglicht fortlaufende Echtzeitdatenverschiebungen aus Data Warehouses und Datenbanken zu Azure. Im Zuge der Datenverschiebung können Sie eine Inline-Denormalisierung und Datentransformationen durchführen sowie die Erstellung von Echtzeitanalysen und Datenberichten ermöglichen. Mit Striim lassen sich Unternehmensdaten ganz einfach fortlaufend zur Azure Cosmos DB-Cassandra-API verschieben. Über das im Azure Marketplace verfügbare Angebot können Sie Striim mühelos bereitstellen und Daten zu Azure Cosmos DB migrieren. 

In diesem Artikel wird gezeigt, wie Sie Daten mithilfe von Striim aus einer **Oracle-Datenbank** zu einem **Azure Cosmos DB-Cassandra-API-Konto** migrieren.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein [Azure-Abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.

* Eine lokal ausgeführte Oracle-Datenbank mit einigen Daten

## <a name="deploy-the-striim-marketplace-solution"></a>Bereitstellen der Striim-Lösung aus dem Marketplace

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** , und suchen Sie im Azure Marketplace nach **Striim**. Wählen Sie die erste Option und anschließend **Erstellen** aus.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Suchen nach dem Marketplace-Element „Striim“":::

1. Geben Sie als Nächstes die Konfigurationseigenschaften der Striim-Instanz ein. Die Striim-Umgebung wird auf einem virtuellen Computer bereitgestellt. Geben Sie im Bereich **Grundlagen** unter **Benutzername des virtuellen Computers** den VM-Benutzernamen und unter **Kennwort des virtuellen Computers** das VM-Kennwort ein. (Dieses Kennwort wird verwendet, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen.) Wählen Sie Ihr **Abonnement** , Ihre **Ressourcengruppe** und den **Standort** aus, an dem Striim bereitgestellt werden soll. Wählen Sie anschließend **OK** aus.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Konfigurieren der Grundeinstellungen für Striim":::


1. Wählen Sie im Bereich **Striim Cluster settings** (Einstellungen für den Striim-Cluster) die Art der Striim-Bereitstellung und die VM-Größe aus.

   |Einstellung | Wert | BESCHREIBUNG |
   | ---| ---| ---|
   |Art der Striim-Bereitstellung |Eigenständig | Mögliche Bereitstellungstypen für Striim sind **Eigenständig** und **Cluster**. Im eigenständigen Modus wird der Striim-Server auf einem einzelnen virtuellen Computer bereitgestellt, und Sie können die Größe der VMs abhängig von Ihrem Datenvolumen auswählen. Im Clustermodus wird der Striim-Server auf zwei oder mehr virtuellen Computern mit der gewählten Größe bereitgestellt. Clusterumgebungen mit mehr als zwei Knoten bieten automatische Hochverfügbarkeit und Failoverfunktionen.</br></br> In diesem Tutorial können Sie die eigenständige Option auswählen. Verwenden Sie die standardmäßige VM-Größe „Standard_F4s“. | 
   | Name des Striim-Clusters|    <Striim_cluster_Name>|  Der Name des Striim-Clusters.|
   | Kennwort des Striim-Clusters|   <Kennwort des Striim-Clusters>|  Das Kennwort für den Cluster.|

   Wenn Sie das Formular ausgefüllt haben, klicken Sie auf **OK**.

1. Konfigurieren Sie im Bereich **Striim access settings** (Striim-Zugriffseinstellungen) Werte für **Öffentliche IP-Adresse** (wählen Sie die Standardwerte aus), **Domain name for Striim** (Domänenname für Striim) und **Administratorkennwort** für die Anmeldung bei der Striim-Benutzeroberfläche. Konfigurieren Sie ein VNET und ein Subnetz. (Wählen Sie die Standardwerte aus.) Wählen Sie anschließend **OK** aus, um den Vorgang fortzusetzen.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Striim-Zugriffseinstellungen":::

1. Azure überprüft die Bereitstellung, um sicherzustellen, dass alles ordnungsgemäß konfiguriert wurde. Das dauert ein paar Minuten. Wählen Sie nach Abschluss der Validierung **OK** aus.
  
1. Lesen Sie abschließend die Nutzungsbedingungen, und wählen Sie **Erstellen** aus, um Ihre Striim-Instanz zu erstellen. 

## <a name="configure-the-source-database"></a>Konfigurieren der Quelldatenbank

In diesem Abschnitt konfigurieren Sie die Oracle-Datenbank als Quelle für die Datenverschiebung.  Für die Verbindungsherstellung mit Oracle benötigen Sie den [Oracle JDBC-Treiber](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html). Änderungen können entweder mit [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) oder mithilfe der [XStream-APIs](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) aus der Oracle-Quelldatenbank gelesen werden. Der Oracle JDBC-Treiber muss sich im Java-Klassenpfad von Striim befinden, um Daten aus der Oracle-Datenbank lesen, schreiben oder dauerhaft speichern zu können.

Laden Sie den Treiber [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) auf Ihren lokalen Computer herunter. Er wird später im Striim-Cluster installiert.

## <a name="configure-target-database"></a>Konfigurieren der Zieldatenbank

In diesem Abschnitt konfigurieren Sie das Azure Cosmos DB-Cassandra-API-Konto als Ziel für die Datenverschiebung.

1. Erstellen Sie über das Azure-Portal ein [Azure Cosmos DB-Cassandra-API-Konto](create-cassandra-dotnet.md#create-a-database-account).

1. Navigieren Sie in Ihrem Azure Cosmos-Konto zum Bereich **Daten-Explorer**. Wählen Sie **Neue Tabelle** aus, um einen neuen Container zu erstellen. Gehen Sie davon aus, dass Sie *Produkt-* und *Auftragsdaten* aus der Oracle-Datenbank zu Azure Cosmos DB migrieren. Erstellen Sie einen neuen Keyspace namens **striimdemo** mit dem Container „Orders“. Stellen Sie den Container mit **1.000 RUs** sowie mit **/ORDER_ID** als Primärschlüssel bereit. (In diesem Beispiel werden 1.000 RUs verwendet, Sie sollten allerdings den voraussichtlichen Durchsatz für Ihre Workload verwenden). Diese Werte unterscheiden sich abhängig von Ihren Quelldaten. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png" alt-text="Erstellen des Cassandra-API-Kontos":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurieren des Datenflusses von Oracle zu Azure Cosmos DB

1. Kehren wir nun zu Striim zurück. Installieren Sie vor der Interaktion mit Striim den zuvor heruntergeladenen Oracle JDBC-Treiber.

1. Navigieren Sie zu der Striim-Instanz, die Sie über das Azure-Portal bereitgestellt haben. Wählen Sie auf der oberen Menüleiste die Schaltfläche **Verbinden** aus, und kopieren Sie auf der Registerkarte **SSH** die URL im Feld **Mit lokalem VM-Konto anmelden**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Abrufen der SSH-URL":::

1. Öffnen Sie ein neues Terminalfenster, und führen Sie den aus dem Azure-Portal kopierten SSH-Befehl aus. In diesem Artikel wird ein macOS-Terminal verwendet. Die Vorgehensweise für PuTTY oder einen anderen SSH-Client auf einem Windows-Computer ist jedoch ähnlich. Geben Sie bei entsprechender Aufforderung **yes** (Ja) und anschließend das **Kennwort** ein, das Sie im vorherigen Schritt für den virtuellen Computer festgelegt haben.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Herstellen der Verbindung mit dem virtuellen Striim-Computer":::

1. Öffnen Sie eine neue Terminalregisterkarte, um die zuvor heruntergeladene Datei **ojdbc8.jar** zu kopieren. Verwenden Sie den folgenden SCP-Befehl, um die JAR-Datei von Ihrem lokalen Computer in den Ordner „tmp“ der in Azure ausgeführten Striim-Instanz zu kopieren:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Kopieren der JAR-Datei vom lokalen Computer in Striim":::

1. Kehren Sie wieder zu dem Fenster zurück, in dem Sie die SSH-Verbindung mit der Striim-Instanz hergestellt haben, und melden Sie sich als „sudo“ an. Verschieben Sie die Datei **ojdbc8.jar** mithilfe der folgenden Befehle aus dem Verzeichnis **/tmp** in das Verzeichnis **lib** Ihrer Striim-Instanz:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Verschieben der JAR-Datei in den Ordner „lib“":::


1. Starten Sie den Striim-Server über das gleiche Terminalfenster mithilfe der folgenden Befehle neu:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Der Startvorgang von Striim dauert etwas. Wenn Sie den Status anzeigen möchten, können Sie den folgenden Befehl ausführen: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Navigieren Sie nun wieder zu Azure, und kopieren Sie die öffentliche IP-Adresse Ihres virtuellen Striim-Computers. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Kopieren der IP-Adresse des virtuellen Striim-Computers":::

1. Öffnen Sie in einem Browser eine neue Registerkarte, kopieren Sie die öffentliche IP-Adresse, und hängen Sie Folgendes an, um zur Webbenutzeroberfläche von Striim zu navigieren: 9080. Melden Sie sich mit dem Benutzernamen **admin** und dem Administratorkennwort an, das Sie im Azure-Portal angegeben haben.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Anmelden bei Striim":::

1. Daraufhin wird die Startseite von Striim angezeigt. Dort stehen drei Bereiche zur Verfügung: **Dashboards** , **Apps** und **SourcePreview** (Quellvorschau). Im Bereich „Dashboards“ können Sie Daten in Echtzeit verschieben und visualisieren. Der Bereich „Apps“ enthält Ihre Streamingdatenpipelines (oder Datenflüsse). Rechts auf der Seite befindet sich „SourcePreview“ (Quellvorschau). In diesem Bereich können Sie vor dem Verschieben eine Vorschau Ihrer Daten anzeigen.

1. Wir beschäftigen uns zunächst mit dem Bereich **Apps**. Wählen Sie daher diesen Bereich aus. Es gibt eine Reihe von Beispiel-Apps, die Sie verwenden können, um sich mit Striim vertraut zu machen. In diesem Artikel erstellen wir jedoch eine eigene App. Wählen Sie in der rechten oberen Ecke die Schaltfläche **Add App** (App hinzufügen) aus.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Hinzufügen der Striim-App":::

1. Striim-Anwendungen können auf unterschiedliche Weise erstellt werden. Wählen Sie für dieses Szenario die Option **Start from Scratch** (Von Grund auf neu erstellen) aus.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png" alt-text="Erstellen einer neuen App ohne Vorlage":::

1. Geben Sie einen Anzeigenamen für Ihre Anwendung an (beispielsweise **oraToCosmosDB** ), und wählen Sie **Save** (Speichern) aus.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png" alt-text="Erstellen einer neuen Anwendung":::

1. Im daraufhin angezeigten Datenflussdesigner können Sie vorgefertigte Connectors per Drag & Drop platzieren, um Ihre Streaminganwendungen zu erstellen. Geben Sie auf der Suchleiste den Suchbegriff **Oracle** ein, und platzieren Sie die Quelle **Oracle CDC** per Drag & Drop auf der App-Canvas.  

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png" alt-text="Quelle „Oracle CDC“":::

1. Geben Sie die Quellkonfigurationseigenschaften Ihrer Oracle-Instanz ein. Der Quellname ist lediglich eine Benennungskonvention für die Striim-Anwendung. Sie können einen Namen wie etwa **src_onPremOracle** verwenden. Geben Sie auch andere Details wie Adaptertyp, Verbindungs-URL, Benutzername, Kennwort und Tabellenname ein. Wählen Sie **Save** (Speichern) aus, um den Vorgang fortzusetzen.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png" alt-text="Konfigurieren der Quellparameter":::

1. Klicken Sie nun auf das Wellensymbol des Streams, um die Verbindung mit der Azure Cosmos DB-Zielinstanz herzustellen. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png" alt-text="Mit Ziel verbinden":::

1. Stellen Sie vor dem Konfigurieren des Ziels sicher, dass Sie der [Java-Umgebung von Striim ein Baltimore-Stammzertifikat](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store) hinzugefügt haben.

1. Geben Sie die Konfigurationseigenschaften Ihrer Azure Cosmos DB-Zielinstanz ein, und wählen Sie **Save** (Speichern) aus, um den Vorgang fortzusetzen. Dies sind die wichtigsten Parameter:

   * **Adapter:** Verwenden Sie **DatabaseWriter**. Beim Schreiben in die Cassandra-API von Azure Cosmos DB ist DatabaseWriter erforderlich. Der Cassandra-Treiber 3.6.0 ist mit Striim gebündelt. Wenn DatabaseWriter die Anzahl von in Ihrem Azure Cosmos-Container bereitgestellten RUs überschreitet, stürzt die Anwendung ab.

   * **Verbindungs-URL:** Geben Sie Ihre JDBC-Verbindungs-URL für Azure Cosmos DB an. Das Format der URL ist `jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`.

   * **Username** (Benutzername): Geben Sie den Namen Ihres Azure Cosmos-Kontos an.
   
   * **Password** (Kennwort): Geben Sie den Primärschlüssel Ihres Azure Cosmos-Kontos an.

   * **Tables** (Tabellen): Zieltabellen müssen über Primärschlüssel verfügen. Primärschlüssel können nicht aktualisiert werden.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png" alt-text="Screenshot: Konfigurierbare Zieleigenschaften":::

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png" alt-text="Konfigurieren der Zieleigenschaften":::

1. Als Nächstes führen wir die Striim-Anwendung aus. Wählen Sie auf der oberen Menüleiste die Option **Created** (Erstellt) und anschließend **Deploy App** (App bereitstellen) aus. Im Bereitstellungsfenster können Sie angeben, ob bestimmte Teile der Anwendung auf bestimmten Komponenten Ihrer Bereitstellungstopologie ausgeführt werden sollen. Da wir hier eine einfache Azure-basierte Bereitstellungstopologie verwenden, behalten wir die Standardoption bei.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png" alt-text="Bereitstellen der App":::


1. Als Nächstes sehen wir uns eine Vorschau des Streams an, um den Datenfluss in Striim zu beobachten. Klicken Sie auf das Wellensymbol und anschließend auf das daneben angezeigte Augensymbol. Nach der Bereitstellung können Sie eine Vorschau des Streams anzeigen, um den Datenfluss zu beobachten. Wählen Sie das **Wellensymbol** und anschließend das daneben angezeigte **Augensymbol** aus. Wählen Sie auf der oberen Menüleiste die Schaltfläche **Deployed** (Bereitgestellt) und anschließend **Start App** (App starten) aus.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png" alt-text="Starten der App":::

1. Durch die Verwendung eines Readers vom Typ **CDC (Change Data Capture)** erfasst Striim nur neue Änderungen für die Datenbank. Daten, die Ihre Quelltabellen durchlaufen, werden angezeigt. Da es sich hierbei allerdings um eine Beispieltabelle handelt, ist die Quelle nicht mit einer Anwendung verbunden. Wenn Sie einen Beispieldatengenerator verwenden, können Sie eine Kette von Ereignissen in Ihre Oracle-Datenbank einfügen.

1. Daraufhin sehen Sie, wie Daten die Striim-Plattform durchlaufen. Striim erfasst auch alle Metadaten, die Ihrer Tabelle zugeordnet sind. Dies ist hilfreich, um die Daten zu überwachen und sicherzustellen, dass die Daten das richtige Ziel erreichen.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png" alt-text="Einrichten der CDC-Pipeline":::

1. Melden Sie sich zum Schluss bei Azure an, und navigieren Sie zu Ihrem Azure Cosmos-Konto. Aktualisieren Sie den Daten-Explorer. Daraufhin sehen Sie, dass Ihre Daten eingegangen sind. 

Durch Verwendung der Striim-Lösung in Azure können Sie Daten fortlaufend aus verschiedenen Quellen zu Azure Cosmos DB migrieren – etwa aus Oracle, Cassandra, MongoDB und aus verschiedenen anderen Quellen. Weitere Informationen finden Sie auf der [Striim-Website](https://www.striim.com/). [Laden Sie eine kostenlose 30-Tage-Testversion von Striim herunter](https://go2.striim.com/download-free-trial), und stellen Sie im Fall von Problemen bei der Einrichtung des Migrationspfads mit Striim eine [Supportanfrage](https://go2.striim.com/request-support-striim).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Migrieren von Daten zur Azure Cosmos DB-SQL-API finden Sie unter [Migrieren von Daten zum Azure Cosmos DB-SQL-API-Konto mithilfe von Striim](cosmosdb-sql-api-migrate-data-striim.md).

* [Überwachen und Debuggen mit Metriken in Azure Cosmos DB](use-metrics.md)