---
title: 'Tutorial: Konfigurieren einer SQL Server-Always On-Verfügbarkeitsgruppe'
description: In diesem Tutorial erfahren Sie, wie Sie eine SQL Server Always On-Verfügbarkeitsgruppe in Azure Virtual Machines erstellen.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: feab48f32396bcc89621433930c9a9f4689d8286
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355442"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Tutorial: Manuelles Konfigurieren einer Verfügbarkeitsgruppe (SQL Server auf virtuellen Azure-Computern)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Tutorial erfahren Sie, wie Sie eine Always On-Verfügbarkeitsgruppe für SQL Server auf virtuellen Azure-Computern erstellen. Im Rahmen des vollständigen Tutorials wird eine Verfügbarkeitsgruppe mit einem Datenbankreplikat in zwei SQL Server-Instanzen erstellt.

In diesem Artikel wird die Umgebung der Verfügbarkeitsgruppen manuell konfiguriert. Diese Konfiguration kann aber auch über das [Azure-Portal](availability-group-azure-portal-configure.md), [PowerShell oder die Azure CLI](availability-group-az-commandline-configure.md) oder mithilfe von [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) erfolgen. 


**Geschätzter Zeitaufwand**: Etwa 30 Minuten nach Abschluss der [erforderlichen Vorbereitungen](availability-group-manually-configure-prerequisites-tutorial.md)


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial werden Grundkenntnisse über SQL Server Always On-Verfügbarkeitsgruppen vorausgesetzt. Weitere Informationen finden Sie in der [Übersicht über Always On-Verfügbarkeitsgruppen (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

Vor Beginn des Tutorials müssen die [Schritte zum Erfüllen der Voraussetzungen für die Erstellung von Always On-Verfügbarkeitsgruppen in Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md) ausgeführt werden. Falls die Voraussetzungen bereits erfüllt werden, können Sie direkt mit dem [Erstellen des Clusters](#CreateCluster) fortfahren.

Die folgende Tabelle gibt Aufschluss über die Voraussetzungen, die erfüllt sein müssen, bevor Sie mit dem Tutorial beginnen:

| Anforderung |BESCHREIBUNG |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Zwei SQL Server-Instanzen** | - In einer Azure-Verfügbarkeitsgruppe <br/> - In einer einzelnen Domäne <br/> - Mit installiertem Failoverclustering-Feature |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Dateifreigabe für Clusterzeuge |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server-Dienstkonto** | Domänenkonto |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server-Agent-Dienstkonto** | Domänenkonto |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Geöffnete Firewallports** | - SQL Server: **1433** für die Standardinstanz <br/> - Datenbankspiegelungs-Endpunkt: **5022** oder ein beliebiger verfügbarer Port <br/> - Integritätstest für IP-Adresse des Lastenausgleichs für Verfügbarkeitsgruppen: **59999** oder ein beliebiger verfügbarer Port <br/> - Integritätstest für IP-Adresse des Lastenausgleichs für Hauptressourcen des Clusters: **58888** oder ein beliebiger verfügbarer Port |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Hinzufügen des Failoverclustering-Features** | Dieses Feature wird von beiden SQL Server-Instanzen benötigt. |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Domänenkonto für die Installation** | - Lokaler Administrator in jeder SQL Server-Instanz <br/> - Mitglied der festen SQL Server-Serverrolle „SysAdmin“ für jede Instanz von SQL Server  |

>[!NOTE]
> Viele der in diesem Tutorial behandelten Schritte können jetzt mit dem [Azure-Portal](availability-group-azure-portal-configure.md), mit [PowerShell und der Azure CLI](./availability-group-az-commandline-configure.md) und mit [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) automatisiert werden.


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Erstellen Sie den Cluster.

Wenn die Voraussetzungen erfüllt sind, müssen Sie zunächst einen Windows Server-Failovercluster mit zwei SQL Server-Instanzen und einem Zeugenserver erstellen.

1. Verwenden Sie das Remotedesktopprotokoll (RDP) zum Herstellen einer Verbindung mit der ersten SQL Server-Instanz. Verwenden Sie dabei ein Domänenkonto, das in beiden SQL Server-Instanzen sowie auf dem Zeugenserver über Administratorberechtigungen verfügt.

   >[!TIP]
   >Wenn Sie sich an das [Dokument mit den Voraussetzungen](availability-group-manually-configure-prerequisites-tutorial.md) gehalten haben, haben Sie ein Konto namens **CORP\Install** erstellt. Verwenden Sie dieses Konto.

2. Wählen Sie im Dashboard **Server-Manager** die Option **Tools** aus, und klicken Sie dann auf **Failovercluster-Manager**.
3. Klicken Sie im linken Bereich mit der rechten Maustaste auf **Failovercluster-Manager**, und klicken Sie anschließend auf **Cluster erstellen**.

   ![Cluster erstellen](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. Erstellen Sie im Clustererstellungs-Assistenten einen Cluster mit einem einzelnen Knoten, indem Sie die Seiten durchlaufen und dabei die Einstellungen aus der folgenden Tabelle verwenden:

   | Seite | Einstellungen |
   | --- | --- |
   | Vorbereitungen |Standardwerte verwenden |
   | Server auswählen |Geben Sie unter **Servernamen eingeben** den Namen der ersten SQL Server-Instanz ein, und klicken Sie auf **Hinzufügen**. |
   | Validierungswarnung |Wählen Sie **Nein. Microsoft-Support für diesen Cluster nicht nötig. Validierungstests nicht durchführen. Klicken Sie auf „Weiter“, um das Erstellen des Clusters fortzusetzen**. |
   | Zugriffspunkt für die Clusterverwaltung |Geben Sie unter **Clustername** einen Clusternamen ein (beispielsweise **SQLAGCluster1**).|
   | Bestätigung |Standardeinstellungen verwenden, es sei denn, Sie verwenden Speicherplätze. Siehe den Hinweis im Anschluss an diese Tabelle. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Festlegen der IP-Adresse des Windows Server-Failoverclusters

  > [!NOTE]
  > Unter Windows Server 2019 wird für den Cluster ein **Name des verteilten Servers** anstelle des **Clusternetzwerknamens** erstellt. Wenn Sie mit Windows Server 2019 arbeiten, überspringen Sie alle Schritte, in denen in diesem Tutorial auf den Clusterhauptnamen verwiesen wird. Sie können einen Clusternetzwerknamen mit [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster) erstellen. Lesen Sie den Blog [Failover Cluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97), um weitere Informationen zu erhalten. 

1. Scrollen Sie im **Failovercluster-Manager** nach unten bis zu **Hauptressourcen des Clusters**, und erweitern Sie die Clusterdetails. Die Ressource **Name** und **IP-Adresse** befinden sich im Zustand **Fehler**. Die IP-Adressressource kann nicht online geschaltet werden, da dem Cluster die gleiche IP-Adresse zugewiesen ist wie dem Computer selbst. Es liegt also eine doppelte Adresse vor.

2. Klicken Sie mit der rechten Maustaste auf die fehlerhafte Ressource **IP-Adresse**, und klicken Sie dann auf **Eigenschaften**.

   ![Eigenschaften des Clusters](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Wählen Sie **Statische IP-Adresse** aus, und geben Sie eine verfügbare Adresse aus dem gleichen Subnetz an, das auch Ihre virtuellen Computer enthält.

4. Klicken Sie im Abschnitt **Hauptressourcen des Clusters** mit der rechten Maustaste auf den Clusternamen, und klicken Sie anschließend auf **Online schalten**. Warten Sie, bis beide Ressourcen online sind. Beim Onlineschalten der Clusternamenressource wird für den Domänencontrollerserver (DC) das Computerkonto in Active Directory (AD) aktualisiert. Verwenden Sie dieses AD-Konto zum späteren Ausführen des Clusterdiensts der Verfügbarkeitsgruppe.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Hinzufügen der anderen SQL Server-Instanz zum Cluster

Fügen Sie die andere SQL Server-Instanz dem Cluster hinzu.

1. Klicken Sie in der Browserstruktur mit der rechten Maustaste auf den Cluster, und klicken Sie dann auf **Knoten hinzufügen**.

    ![Hinzufügen eines Knotens zum Cluster](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. Klicken Sie im **Assistenten zum Hinzufügen von Knoten** auf **Weiter**. Fügen Sie auf der Seite **Server auswählen** die zweite SQL Server-Instanz hinzu. Geben Sie unter **Servernamen eingeben** den Namen des Servers ein, und klicken Sie auf **Hinzufügen**. Wenn Sie fertig sind, klicken Sie auf **Weiter**.

1. Klicken Sie auf der Seite **Validierungswarnung** auf **Nein** (in einem Produktionsszenario sollten Sie die Validierungstests ausführen). Klicken Sie anschließend auf **Weiter**.

8. Deaktivieren Sie bei Verwendung von Speicherplätzen das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden.** auf der Seite **Bestätigung**.

   ![Bestätigung der Knotenhinzufügung](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Wenn Sie Speicherplätze verwenden und das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden.** nicht deaktivieren, trennt Windows die virtuellen Datenträger während des Clusterprozesses. Sie werden daher erst im Datenträger-Manager oder -Explorer angezeigt, nachdem die Speicherplätze aus dem Cluster entfernt und mithilfe von PowerShell erneut angefügt wurden. Mit Speicherplätzen werden mehrere Datenträger in Speicherpools gruppiert. Weitere Informationen finden Sie unter [Speicherplätze](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)).
   >

1. Wählen Sie **Weiter** aus.

1. Wählen Sie **Fertig stellen** aus.

   Im Failovercluster-Manager sehen Sie nun, dass Ihr Cluster über einen neuen Knoten verfügt, und der Knoten wird im Container **Knoten** aufgelistet.

10. Melden Sie sich bei der Remotedesktopsitzung ab.

### <a name="add-a-cluster-quorum-file-share"></a>Hinzufügen einer Clusterquorum-Dateifreigabe

In diesem Beispiel erstellt der Windows-Cluster mithilfe einer Dateifreigabe ein Clusterquorum. In diesem Tutorial wird ein Quorum vom Typ „Knoten- und Dateifreigabemehrheit“ verwendet. Weitere Informationen finden Sie unter [Grundlegendes zu Quorumkonfigurationen in einem Failovercluster](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11)).

1. Stellen Sie über eine Remotedesktopsitzung eine Verbindung mit dem Dateifreigabenzeuge-Mitgliedsserver her.

1. Klicken Sie im **Server-Manager** auf **Tools**. Öffnen Sie **Computerverwaltung**.

1. Klicken Sie auf **Freigegebene Ordner**.

1. Klicken Sie mit der rechten Maustaste auf **Freigaben**, und klicken Sie anschließend auf **Neue Freigabe...** .

   ![Klicken mit der rechten Maustaste auf „Freigaben“ und Auswählen von „Neue Freigabe“](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Erstellen Sie mithilfe des **Assistenten zum Erstellen von Ordnerfreigaben** eine Freigabe.

1. Klicken Sie unter **Ordnerpfad** auf **Durchsuchen**, und navigieren Sie zu einem Pfad für den freigegebenen Ordner, oder erstellen Sie einen Pfad. Wählen Sie **Weiter** aus.

1. Überprüfen Sie unter **Name, Beschreibung und Einstellungen** den Freigabenamen und den Pfad. Wählen Sie **Weiter** aus.

1. Legen Sie unter **Berechtigungen für freigegebene Ordner** die Option **Berechtigungen anpassen** fest. Wählen Sie **Benutzerdefiniert...** aus.

1. Klicken Sie unter **Berechtigungen anpassen** auf **Hinzufügen...** .

1. Achten Sie darauf, dass das für die Clustererstellung verwendete Konto über Vollzugriff verfügt.

   ![Achten Sie darauf, dass das für die Clustererstellung verwendete Konto über Vollzugriff verfügt.](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Klicken Sie auf **OK**.

1. Klicken Sie unter **Berechtigungen für freigegebene Ordner** auf **Fertig stellen**. Wählen Sie erneut **Fertig stellen** aus.  

1. Melden Sie sich vom Server ab.

### <a name="configure-the-cluster-quorum"></a>Konfigurieren des Clusterquorums

Legen Sie als Nächstes das Clusterquorum fest.

1. Stellen Sie eine Remotedesktopverbindung mit dem ersten Clusterknoten her.

1. Klicken Sie im **Failovercluster-Manager** mit der rechten Maustaste auf den Cluster, zeigen Sie auf **Weitere Aktionen**, und klicken Sie auf **Clusterquorumeinstellungen konfigurieren**.

   ![Auswählen von „Clusterquorumeinstellungen konfigurieren“](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. Klicken Sie im **Assistenten zum Konfigurieren des Clusterquorums** auf **Weiter**.

1. Wählen Sie unter **Quorumkonfigurationsoption auswählen** die Option **Quorumzeugen auswählen** aus, und klicken Sie anschließend auf **Weiter**.

1. Klicken Sie unter **Quorumzeuge auswählen** auf **Dateifreigabenzeugen konfigurieren**.

   >[!TIP]
   >Windows Server 2016 unterstützt einen Cloudzeugen. Bei Verwendung dieser Art von Zeuge benötigen Sie keinen Dateifreigabenzeugen. Weitere Informationen finden Sie unter [Deploy a cloud witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness) (Bereitstellen eines Cloudzeugen für einen Failovercluster). In diesem Tutorial wird ein Dateifreigabenzeuge verwendet, der von älteren Betriebssystemen unterstützt wird.
   >

1. Geben Sie unter **Dateifreigabenzeugen konfigurieren** den Pfad für die erstellte Freigabe ein. Wählen Sie **Weiter** aus.

1. Überprüfen Sie unter **Bestätigung** die Einstellungen. Wählen Sie **Weiter** aus.

1. Wählen Sie **Fertig stellen** aus.

Die Hauptressourcen des Clusters werden mit einem Dateifreigabenzeugen konfiguriert.

## <a name="enable-availability-groups"></a>Aktivieren von Verfügbarkeitsgruppen

Aktivieren Sie als Nächstes das Feature **Always On-Verfügbarkeitsgruppen**. Führen Sie diese Schritte für beide SQL Server durch.

1. Starten Sie über den **Startbildschirm** den **SQL Server-Konfigurations-Manager**.
2. Klicken Sie in der Browserstruktur auf **SQL Server-Dienste**, klicken Sie dann mit der rechten Maustaste auf den Dienst **SQL Server (MSSQLSERVER)** , und klicken Sie auf **Eigenschaften**.
3. Wählen Sie die Registerkarte **Hochverfügbarkeit mit AlwaysOn** und dann **AlwaysOn-Verfügbarkeitsgruppen aktivieren** aus:

    ![AlwaysOn-Verfügbarkeitsgruppen aktivieren](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Wählen Sie **Übernehmen**. Klicken Sie im Popupdialogfenster auf **OK**.

5. Starten Sie den SQL Server-Dienst neu.

Wiederholen Sie diese Schritte für die andere SQL Server-Instanz.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Erstellen einer Datenbank in der ersten SQL Server-Instanz

1. Starten Sie die RDP-Datei für die erste SQL Server-Instanz mit einem Domänenkonto, das der festen SysAdmin-Serverrolle angehört.
1. Öffnen Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit der ersten SQL Server-Instanz her.
7. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf **Datenbanken**, und klicken Sie anschließend auf **Neue Datenbank**.
8. Geben Sie unter **Datenbankname** den Namen **MyDB1** ein, und klicken Sie dann auf **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Erstellen einer Sicherungsfreigabe

1. Klicken Sie auf dem ersten SQL Server unter **Server-Manager** auf **Tools**. Öffnen Sie **Computerverwaltung**.

1. Klicken Sie auf **Freigegebene Ordner**.

1. Klicken Sie mit der rechten Maustaste auf **Freigaben**, und klicken Sie anschließend auf **Neue Freigabe...** .

   ![Auswählen von „Neue Freigabe“](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Erstellen Sie mithilfe des **Assistenten zum Erstellen von Ordnerfreigaben** eine Freigabe.

1. Klicken Sie unter **Ordnerpfad** auf **Durchsuchen**, und navigieren Sie zu einem Pfad für den freigegebenen Ordner der Datenbanksicherung, oder erstellen Sie einen Pfad. Wählen Sie **Weiter** aus.

1. Überprüfen Sie unter **Name, Beschreibung und Einstellungen** den Freigabenamen und den Pfad. Wählen Sie **Weiter** aus.

1. Legen Sie unter **Berechtigungen für freigegebene Ordner** die Option **Berechtigungen anpassen** fest. Wählen Sie **Benutzerdefiniert...** aus.

1. Klicken Sie unter **Berechtigungen anpassen** auf **Hinzufügen...** .

1. Vergewissern Sie sich, dass das SQL Server-Dienstkonto und das SQL Server-Agent-Dienstkonto für beide Server über Vollzugriff verfügen.

   ![Vergewissern Sie sich, dass das SQL Server-Dienstkonto und das SQL Server-Agent-Dienstkonto für beide Server über Vollzugriff verfügen.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Klicken Sie auf **OK**.

1. Klicken Sie unter **Berechtigungen für freigegebene Ordner** auf **Fertig stellen**. Wählen Sie erneut **Fertig stellen** aus.  

### <a name="take-a-full-backup-of-the-database"></a>Erstellen einer vollständigen Sicherung der Datenbank

Die neue Datenbank muss gesichert werden, um die Protokollkette zu initiieren. Wenn Sie die neue Datenbank nicht sichern, kann sie keiner Verfügbarkeitsgruppe hinzugefügt werden.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die Datenbank, zeigen Sie auf **Aufgaben...** , und klicken Sie anschließend auf **Sichern**.

1. Klicken Sie auf **OK**, um eine vollständige Sicherung am Standardspeicherort für Sicherungen zu erstellen.

## <a name="create-the-availability-group"></a>Erstellen der Verfügbarkeitsgruppe

Nun können Sie eine Verfügbarkeitsgruppe konfigurieren. Gehen Sie dazu wie folgt vor:

* Erstellen Sie eine Datenbank in der ersten SQL Server-Instanz.
* Erstellen sowohl einer vollständigen Sicherung als auch einer Transaktionsprotokollsicherung der Datenbank
* Stellen Sie die vollständige Sicherung und die Protokollsicherung mit der **NORECOVERY** in der zweiten SQL Server-Instanz wieder her.
* Erstellen der Verfügbarkeitsgruppe (**AG1**) mit synchronem Commit, automatischem Failover und lesbaren, sekundären Replikaten

### <a name="create-the-availability-group"></a>Erstellen der Verfügbarkeitsgruppe:

1. Gehen Sie in einer Remotedesktopsitzung mit der ersten SQL Server-Instanz wie folgt vor: Klicken Sie in SSMS im **Objekt-Explorer** mit der rechten Maustaste auf **Hochverfügbarkeit mit AlwaysOn**, und wählen Sie anschließend **Assistent für neue Verfügbarkeitsgruppen** aus.

    ![Starten des Assistenten für neue Verfügbarkeitsgruppen](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. Wählen Sie auf der Seite **Einführung** die Option **Weiter** aus. Geben Sie auf der Seite **Namen der Verfügbarkeitsgruppe angeben** unter **Name der Verfügbarkeitsgruppe** einen Namen für die Verfügbarkeitsgruppe ein. Beispiel: **AG1**. Wählen Sie **Weiter** aus.

    ![Assistent für neue Verfügbarkeitsgruppen: Namen der Verfügbarkeitsgruppe angeben](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Wählen Sie auf der Seite **Datenbanken auswählen** Ihre Datenbank aus, und klicken Sie dann auf **Weiter**.

   >[!NOTE]
   >Die Datenbank erfüllt die Voraussetzungen für eine Verfügbarkeitsgruppe, weil Sie mindestens eine vollständige Sicherung auf dem vorgesehenen primären Replikat erstellt haben.
   >

   ![Assistent für neue Verfügbarkeitsgruppen: Datenbanken auswählen](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. Klicken Sie auf der Seite **Replikate angeben** auf **Replikat hinzufügen**.

   ![Assistent für neue Verfügbarkeitsgruppen: Replikate angeben](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. Das Dialogfeld **Verbindung mit Server herstellen** wird angezeigt. Geben Sie unter **Servername** den Namen des zweiten Servers ein. Wählen Sie **Verbinden**.

   Auf der Seite **Replikate angeben** wird nun unter **Verfügbarkeitsreplikate** der zweite Server aufgeführt. Konfigurieren Sie die Replikate wie folgt:

   ![Assistent für neue Verfügbarkeitsgruppen: Replikate angeben (vollständig)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Wählen Sie **Endpunkte** aus, um den Datenbankspiegelungs-Endpunkt für diese Verfügbarkeitsgruppe anzuzeigen. Verwenden den gleichen Port, den Sie auch beim Festlegen der [Firewallregel für Datenbankspiegelungs-Endpunkte](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall) verwendet haben.

    ![Assistent für neue Verfügbarkeitsgruppen: Anfängliche Datensynchronisierung auswählen](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. Wählen Sie auf der Seite **Anfängliche Datensynchronisierung auswählen** die Option **Vollständig** aus, und geben Sie einen freigegebenen Netzwerkpfad an. Verwenden Sie für den Speicherort die [zuvor erstellte Sicherungsfreigabe](#backupshare). In diesem Beispiel: **\\\\Erste SQL Server-Instanz\>\Backup\\** . Wählen Sie **Weiter** aus.

   >[!NOTE]
   >Zur vollständigen Synchronisierung wird die Datenbank der ersten SQL Server-Instanz vollständig gesichert und in der zweiten Instanz wiederhergestellt. Bei umfangreichen Datenbanken wird von einer vollständigen Synchronisierung abgeraten, da sie sehr lange dauern kann. Sie können den Vorgang beschleunigen, indem Sie die Datenbank manuell sichern und mit `NO RECOVERY` wiederherstellen. Falls die Datenbank bereits vor dem Konfigurieren der Verfügbarkeitsgruppe mit `NO RECOVERY` in der zweiten SQL Server-Instanz wiederhergestellt wurde, wählen Sie **Nur verknüpfen** aus. Wenn Sie die Sicherung nach dem Konfigurieren der Verfügbarkeitsgruppe erstellen möchten, wählen Sie **Anfängliche Datensynchronisierung überspringen** aus.
   >

   ![Auswählen von „Anfängliche Datensynchronisierung überspringen“](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Klicken Sie auf der Seite **Validierung** auf **Weiter**. Die Seite sollte in etwa der folgenden Abbildung entsprechen:

    ![Assistent für neue Verfügbarkeitsgruppen: Validierung](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Es liegt eine Warnung für die Listenerkonfiguration vor, weil Sie keinen Listener für die Verfügbarkeitsgruppe konfiguriert haben. Diese Warnung kann ignoriert werden, da Sie den Listener auf virtuellen Azure-Computern im Anschluss an Azure Load Balancer erstellen.

10. Wählen Sie auf der Seite **Zusammenfassung** die Option **Fertig stellen** aus, und warten Sie, bis der Assistent die neue Verfügbarkeitsgruppe konfiguriert hat. Auf der Seite **Status** können Sie auf **Weitere Details** klicken, um den detaillierten Status anzuzeigen. Sobald der Assistent abgeschlossen wurde, überprüfen Sie auf der Seite **Ergebnisse**, ob die Verfügbarkeitsgruppe erfolgreich erstellt wurde.

     ![Assistent für neue Verfügbarkeitsgruppen: Ergebnisse](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Klicken Sie auf **Schließen**, um den Assistenten zu beenden.

### <a name="check-the-availability-group"></a>Überprüfen der Verfügbarkeitsgruppe

1. Erweitern Sie im **Objekt-Explorer** den Eintrag **Hochverfügbarkeit mit Always On**, und erweitern Sie dann **Verfügbarkeitsgruppen**. Sie sollten jetzt die neue Verfügbarkeitsgruppe in diesem Container sehen können. Klicken Sie mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und wählen Sie **Dashboard anzeigen** aus.

   ![Anzeigen des Dashboards für Verfügbarkeitsgruppen](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   Ihr **Always On-Dashboard** sollte in etwa wie der folgende Screenshot aussehen:

   ![Dashboard „Verfügbarkeitsgruppe“](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Es werden die Replikate, der Failovermodus jedes Replikats und der Synchronisierungsstatus angezeigt.

2. Klicken Sie im **Failovercluster-Manager** auf Ihren Cluster. Wählen Sie **Rollen** aus. Bei dem verwendeten Verfügbarkeitsgruppennamen handelt es sich um eine Rolle im Cluster. Diese Verfügbarkeitsgruppe besitzt keine IP-Adresse für Clientverbindungen, da Sie keinen Listener konfiguriert haben. Der Listener wird nach der Erstellung einer Azure Load Balancer-Instanz konfiguriert.

   ![Verfügbarkeitsgruppe im Failovercluster-Manager](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Versuchen Sie nicht, ein Failover der Verfügbarkeitsgruppe aus dem Failovercluster-Manager heraus durchzuführen. Alle Failovervorgänge sollten aus dem **AlwaysOn-Dashboard** in SSMS ausgeführt werden. Weitere Informationen finden Sie unter [Failoverclustering und AlwaysOn-Verfügbarkeitsgruppen (SQL Server)](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server).
    >

Sie verfügen nun über eine Verfügbarkeitsgruppe mit Replikaten in zwei Instanzen von SQL Server. Sie können die Verfügbarkeitsgruppe zwischen Instanzen verschieben. Sie können noch keine Verbindung mit der Verfügbarkeitsgruppe herstellen, da Sie über keinen Listener verfügen. Auf virtuellen Azure-Computern wird für den Listener ein Lastenausgleich benötigt. Im nächsten Schritt wird der Lastenausgleich in Azure erstellt.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Erstellen einer Azure Load Balancer-Instanz

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Auf virtuellen Azure-Computern benötigt eine SQL Server-Verfügbarkeitsgruppe einen Lastenausgleich. Der Lastenausgleich speichert die IP-Adressen für die Verfügbarkeitsgruppenlistener und den Windows Server-Failovercluster. In diesem Abschnitt erfahren Sie, wie Sie den Lastenausgleich über das Azure-Portal erstellen.

Bei einem Load Balancer in Azure kann es sich um Load Balancer Standard oder Load Balancer Basic handeln. Load Balancer Standard verfügt über mehr Funktionen als Load Balancer Basic. Für eine Verfügbarkeitsgruppe ist Load Balancer Standard erforderlich, wenn Sie eine Verfügbarkeitszone (anstelle einer Verfügbarkeitsgruppe) verwenden. Ausführliche Informationen zu den Unterschieden zwischen den Load Balancer-SKUs finden Sie unter [Vergleich der Load Balancer-SKUs](../../../load-balancer/skus.md).

1. Navigieren Sie im Azure-Portal zu der Ressourcengruppe mit Ihren SQL Server-Instanzen, und klicken Sie auf **+ Hinzufügen**.
1. Suchen Sie nach **Load Balancer**. Wählen Sie den von Microsoft veröffentlichten Lastenausgleich aus.

   ![Auswählen des von Microsoft veröffentlichten Lastenausgleichs](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Klicken Sie auf **Erstellen**.
1. Konfigurieren Sie den Load Balancer mit folgenden Parametern:

   | Einstellung | Feld |
   | --- | --- |
   | **Name** |Verwenden Sie einen Textnamen für den Lastenausgleich (beispielsweise **sqlLB**). |
   | **Typ** |Intern |
   | **Virtuelles Netzwerk** |Verwenden Sie den Namen des virtuellen Azure-Netzwerks. |
   | **Subnetz** |Verwenden Sie den Namen des Subnetzes, in dem sich der virtuelle Computer befindet.  |
   | **IP-Adresszuweisung** |statischen |
   | **IP-Adresse** |Verwenden Sie eine verfügbare Adresse aus dem Subnetz. Verwenden Sie diese Adresse für den Verfügbarkeitsgruppenlistener. Beachten Sie, dass sich diese von Ihrer Cluster-IP-Adresse unterscheidet.  |
   | **Abonnement** |Verwenden Sie das gleiche Abonnement wie der virtuelle Computer. |
   | **Location** |Verwenden Sie den gleichen Standort wie der virtuelle Computer. |

   Das Blatt im Azure-Portal sollte wie folgt aussehen:

   ![Erstellen oder Aktualisieren eines Lastenausgleichs](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Wählen Sie **Erstellen** aus, um den Load Balancer zu erstellen.

Zum Konfigurieren des Lastenausgleichs müssen Sie einen Back-End-Pool und einen Test erstellen und die Lastenausgleichsregeln festlegen. Führen Sie diese Schritte im Azure-Portal aus.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Hinzufügen eines Back-End-Pools für den Verfügbarkeitsgruppenlistener

1. Navigieren Sie im Azure-Portal zu Ihrer Verfügbarkeitsgruppe. Unter Umständen müssen Sie die Ansicht aktualisieren, damit der neu erstellte Lastenausgleich angezeigt wird.

   ![Navigieren zum Lastenausgleich in der Ressourcengruppe](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Wählen Sie den Lastenausgleich aus, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.

1. Geben Sie einen Namen für den Back-End-Pool ein.

1. Ordnen Sie den Back-End-Pool der Verfügbarkeitsgruppe mit den virtuellen Computern zu.

1. Aktivieren Sie unter **Zielnetzwerk-IP-Konfigurationen** die Option **VIRTUELLER COMPUTER**, und wählen Sie die beiden virtuellen Computer aus, die als Hosts für Verfügbarkeitsgruppenreplikate fungieren. Schließen Sie den Dateifreigabenzeugen-Server nicht ein.

   >[!NOTE]
   >Ohne Angabe der beiden virtuellen Computer können nur Verbindungen mit dem primären Replikat hergestellt werden.

1. Wählen Sie **OK** aus, um den Back-End-Pool zu erstellen.

### <a name="set-the-probe"></a>Festlegen des Tests

1. Wählen Sie den Lastenausgleich aus, und wählen Sie dann **Integritätstests** und **+ Hinzufügen** aus.

1. Legen Sie den Integritätstest für den Listener wie folgt fest:

   | Einstellung | Beschreibung | Beispiel
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokoll** | Wählen Sie „TCP“ aus. | TCP |
   | **Port** | Ein beliebiger nicht verwendeter Port. | 59999 |
   | **Intervall**  | Der Zeitraum zwischen Testversuchen in Sekunden. |5 |
   | **Fehlerhafter Schwellenwert** | Die Anzahl aufeinander folgender Testfehler, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.  | 2 |

1. Klicken Sie auf **OK**, um den Integritätstest festzulegen.

### <a name="set-the-load-balancing-rules"></a>Festlegen der Lastenausgleichsregeln

1. Wählen Sie den Lastenausgleich aus, und wählen Sie dann **Lastenausgleichsregeln** und **+ Hinzufügen** aus.

1. Konfigurieren Sie die Lastenausgleichsregeln für den Listener wie folgt:

   | Einstellung | Beschreibung | Beispiel
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointListener |
   | **Frontend IP address** (Front-End-IP-Adresse) | Wählen Sie eine Adresse aus. |Verwenden Sie die Adresse, die Sie beim Erstellen des Lastenausgleichs erstellt haben. |
   | **Protokoll** | Wählen Sie „TCP“ aus. |TCP |
   | **Port** | Verwenden des Ports für den Verfügbarkeitsgruppenlistener | 1433 |
   | **Back-End-Port** | Dieses Feld wird nicht verwendet, wenn für Direct Server Return die Option „Floating IP“ festgelegt ist. | 1433 |
   | **Test** |Der Name, den Sie für den Test angegeben haben. | SQLAlwaysOnEndPointProbe |
   | **Session Persistence** (Sitzungspersistenz) | Dropdownliste | **None** |
   | **Leerlauftimeout** | Gibt an, wie viele Minuten eine TCP-Verbindung geöffnet bleiben soll. | 4 |
   | **Floating IP (Direct Server Return)** | |Aktiviert |

   > [!WARNING]
   > Direct Server Return wird bei der Erstellung festgelegt. Diese Einstellung kann nicht geändert werden.
   >

1. Klicken Sie auf **OK**, um die Lastenausgleichsregeln für den Listener festzulegen.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Fügen Sie die IP-Adresse der Hauptressourcen des Clusters für den Windows Server-Failovercluster (WSFC) hinzu.

Die WSFC IP-Adresse muss auf dem Lastenausgleich ebenfalls vorhanden sein.

1. Navigieren Sie im Azure-Portal zum gleichen Azure Load Balancer. Wählen Sie **Front-End-IP-Konfiguration** und dann **+ Hinzufügen** aus. Verwenden Sie die IP-Adresse, die Sie für den WSFC in den Hauptressourcen des Clusters konfiguriert haben. Legen Sie die IP-Adresse als statisch fest.

1. Wählen Sie für den Lastenausgleich **Integritätstests** und dann **+ Hinzufügen** aus.

1. Legen Sie den Integritätstest für die IP-Adresse der Hauptressourcen des WSFC-Clusters wie folgt fest:

   | Einstellung | Beschreibung | Beispiel
   | --- | --- |---
   | **Name** | Text | WSFCEndPointProbe |
   | **Protokoll** | Wählen Sie „TCP“ aus. | TCP |
   | **Port** | Ein beliebiger nicht verwendeter Port. | 58888 |
   | **Intervall**  | Der Zeitraum zwischen Testversuchen in Sekunden. |5 |
   | **Fehlerhafter Schwellenwert** | Die Anzahl aufeinander folgender Testfehler, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.  | 2 |

1. Klicken Sie auf **OK**, um den Integritätstest festzulegen.

1. Legen Sie die Lastenausgleichsregeln fest. Wählen Sie **Lastenausgleichsregeln** und dann **+ Hinzufügen** aus.

1. Konfigurieren Sie die Lastenausgleichsregeln für die IP-Adresse der Hauptressourcen des Clusters wie folgt:

   | Einstellung | Beschreibung | Beispiel
   | --- | --- |---
   | **Name** | Text | WSFCEndPoint |
   | **Frontend IP address** (Front-End-IP-Adresse) | Wählen Sie eine Adresse aus. |Verwenden Sie die Adresse, die Sie beim Konfigurieren der WSFC-IP-Adresse erstellt haben. Diese unterscheidet sich von der IP-Adresse des Listeners. |
   | **Protokoll** | Wählen Sie „TCP“ aus. |TCP |
   | **Port** | Verwenden Sie den Port für die Cluster-IP-Adresse. Dies ist ein verfügbarer Port, der nicht als Listenertestport verwendet wird. | 58888 |
   | **Back-End-Port** | Dieses Feld wird nicht verwendet, wenn für Direct Server Return die Option „Floating IP“ festgelegt ist. | 58888 |
   | **Test** |Der Name, den Sie für den Test angegeben haben. | WSFCEndPointProbe |
   | **Session Persistence** (Sitzungspersistenz) | Dropdownliste | **None** |
   | **Leerlauftimeout** | Gibt an, wie viele Minuten eine TCP-Verbindung geöffnet bleiben soll. | 4 |
   | **Floating IP (Direct Server Return)** | |Aktiviert |

   > [!WARNING]
   > Direct Server Return wird bei der Erstellung festgelegt. Diese Einstellung kann nicht geändert werden.
   >

1. Klicken Sie auf **OK**, um die Lastenausgleichsregeln festzulegen.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Konfigurieren des Listeners

Als Nächstes muss ein Verfügbarkeitsgruppenlistener für den Failovercluster konfiguriert werden.

> [!NOTE]
> In diesem Tutorial erfahren Sie, wie Sie einen einzelnen Listener mit einer einzelnen IP-Adresse eines internen Lastenausgleichs erstellen. Weitere Informationen zum Erstellen von einem oder mehreren Listenern mit einer oder mehreren IP-Adressen finden Sie unter [Erstellen eines Verfügbarkeitsgruppenlisteners und eines Load Balancers | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Festlegen des Listenerports

Legen Sie in SQL Server Management Studio den Listenerport fest.

1. Starten Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit dem primären Replikat her.

1. Navigieren Sie zu **Hochverfügbarkeit mit Always On** > **Verfügbarkeitsgruppen** > **Verfügbarkeitsgruppenlistener**.

1. Jetzt sollte der Listenername angezeigt werden, den Sie im Failovercluster-Manager erstellt haben. Klicken Sie mit der rechten Maustaste auf den Listenernamen, und wählen Sie **Eigenschaften** aus.

1. Geben Sie im Feld **Port** die Portnummer für den Verfügbarkeitsgruppenlistener an. 1433 ist der Standardwert. Klicken Sie auf **OK**.

Sie verfügen nun über eine SQL Server-Verfügbarkeitsgruppe auf virtuellen Azure-Computern im Azure Resource Manager-Modus.

## <a name="test-connection-to-listener"></a>Testen der Verbindung mit dem Listener

Gehen Sie wie folgt vor, um die Verbindung zu testen:

1. Stellen Sie eine RDP-Verbindung mit einer SQL Server-Instanz her, die sich im gleichen virtuellen Netzwerk befindet, aber nicht für das Replikat zuständig ist. Sie können die andere SQL Server-Instanz im Cluster verwenden.

1. Testen Sie die Verbindung mithilfe des Hilfsprogramms **sqlcmd**. Das folgende Skript stellt beispielsweise über den Listener eine **sqlcmd** -Verbindung mit Windows-Authentifizierung mit dem primären Replikat her:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Geben Sie den Port in der Verbindungszeichenfolge an, wenn der Listener einen anderen Port als den Standardport (1433) verwendet. Mit dem folgenden `sqlcmd`-Befehl wird beispielsweise eine Verbindung mit einem Listener über Port 1435 hergestellt:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Die sqlcmd-Verbindung wird automatisch mit der SQL Server-Instanz hergestellt, die das primäre Replikat hostet.

> [!TIP]
> Vergewissern Sie sich, dass der angegebene Port in der Firewall beider SQL Server geöffnet ist. Beide Server benötigen eine eingehende Regel für den TCP-Port, den Sie verwenden möchten. Weitere Informationen finden Sie unter [Hinzufügen oder Bearbeiten einer Firewallregel](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)).
>

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen einer IP-Adresse zu einem Lastenausgleich für eine zweite Verfügbarkeitsgruppe](availability-group-listener-powershell-configure.md#Add-IP)