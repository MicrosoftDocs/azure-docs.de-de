---
title: Einrichten der SQL-Datensynchronisierung
description: In diesem Tutorial wird veranschaulicht, wie Sie die SQL-Datensynchronisierung für Azure einrichten.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: f5192176a6a0e174d5878c51defce70d949c1eb1
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922364"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Tutorial: Einrichten der SQL-Datensynchronisierung zwischen Datenbanken in Azure SQL-Datenbank und SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial erfahren Sie, wie Sie die SQL-Datensynchronisierung einrichten, indem Sie eine Synchronisierungsgruppe erstellen, die sowohl Azure SQL-Datenbank- als auch SQL Server-Instanzen enthält. Die Synchronisierungsgruppe ist speziell konfiguriert und verwendet den von Ihnen festgelegten Synchronisierungszeitplan.

In dem Tutorial wird davon ausgegangen, dass Sie über eine gewisse Erfahrung mit SQL-Datenbank und SQL Server verfügen.

Eine Übersicht über die SQL-Datensynchronisierung finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md).

PowerShell-Beispiele für die Konfiguration der SQL-Datensynchronisierung finden Sie im Artikel zum [Synchronisieren zwischen Datenbanken in SQL-Datenbank](scripts/sql-data-sync-sync-data-between-sql-databases.md) und zum [Synchronisieren zwischen Datenbanken in Azure SQL-Datenbank und SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md).

> [!IMPORTANT]
> SQL-Datensynchronisierung unterstützt derzeit **keine** verwalteten Azure SQL-Instanzen.

## <a name="create-sync-group"></a>Erstellen der Synchronisierungsgruppe

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Datenbank in SQL-Datenbank zu suchen. Suchen Sie nach **SQL-Datenbanken**, und wählen Sie diese Option aus.

    ![Suchen nach Datenbanken, Microsoft Azure-Portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Wählen Sie die Datenbank aus, die Sie als die Hub-Datenbank für die Datensynchronisierung verwenden möchten.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > Die Hub-Datenbank ist der zentrale Endpunkt einer Synchronisierungstopologie, bei der eine Synchronisierungsgruppe mehrere Datenbankendpunkte enthält. Alle anderen Mitgliedsdatenbanken mit Endpunkten in der gleichen Synchronisierungsgruppe werden mit der Hub-Datenbank synchronisiert.

1. Wählen Sie auf im Menü **SQL-Datenbank** für die ausgewählte Datenbank die Option **Mit anderen Datenbanken synchronisieren** aus.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. Wählen Sie auf der Seite **Mit anderen Datenbanken synchronisieren** die Option **Neue Synchronisierungsgruppe** aus. Die Seite **Neue Synchronisierungsgruppe** wird mit **Synchronisierungsgruppe erstellen** (Schritt 1) geöffnet.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   Ändern Sie auf der Seite **Datensynchronisierungsgruppe erstellen** die folgenden Einstellungen:

   | Einstellung                        | BESCHREIBUNG |
   | ------------------------------ | ------------------------------------------------- |
   | **Name der Synchronisierungsgruppe** | Geben Sie einen Namen für die neue Synchronisierungsgruppe ein. Dieser Name unterscheidet sich vom Namen der Datenbank. |
   | **Datenbank für Synchronisierungsmetadaten** | Erstellen Sie eine Datenbank (empfohlen), oder verwenden Sie eine vorhandene Datenbank.<br/><br/>Wenn Sie sich für **Neue Datenbank** entscheiden, wählen Sie **Neue Datenbank erstellen** aus. Benennen und konfigurieren Sie dann die neue Datenbank auf der Seite **SQL-Datenbank**, und wählen Sie **OK** aus.<br/><br/>Wenn Sie sich für **Vorhandene Datenbank verwenden** entscheiden, wählen Sie die Datenbank aus der Liste aus. |
   | **Automatische Synchronisierung** | Wählen Sie **Ein** oder **Aus** aus.<br/><br/>Geben Sie bei Verwendung von **Ein** im Abschnitt **Synchronisierungshäufigkeit** eine Zahl ein, und wählen Sie **Sekunden**, **Minuten**, **Stunden** oder **Tage** aus.<br/> Die erste Synchronisierung beginnt, wenn das ausgewählte Intervall nach dem Speichern der Konfiguration abgelaufen ist.|
   | **Konfliktlösung** | Wählen Sie **Hub hat Vorrang** oder **Mitglied hat Vorrang** aus.<br/><br/>**Hub hat Vorrang** bedeutet, dass bei einem Konflikt die in Konflikt stehenden Daten in der Mitgliedsdatenbank durch die Daten in der Hub-Datenbank überschrieben werden.<br/><br/>**Mitglied hat Vorrang** bedeutet, dass bei einem Konflikt die in Konflikt stehenden Daten in der Hub-Datenbank durch die Daten in der Mitgliedsdatenbank überschrieben werden. |
   | **Use private link** (Private Verbindung verwenden) | Wählen Sie einen dienstseitig verwalteten privaten Endpunkt aus, um eine sichere Verbindung zwischen dem Synchronisierungsdienst und der Hub-Datenbank einzurichten. |

   > [!NOTE]
   > Microsoft empfiehlt, eine neue, leere Datenbank als **Datenbank für Synchronisierungsmetadaten** zu erstellen. Durch die Datensynchronisierung werden Tabellen in Datenbanken erstellt und eine häufige Workload ausgeführt. Diese Datenbank wird als **Datenbank für Synchronisierungsmetadaten** für alle Synchronisierungsgruppen in einer ausgewählten Region und in einem ausgewählten Abonnement freigegeben. Sie können die Datenbank oder den Datenbanknamen nicht ändern, ohne alle Synchronisierungsgruppen und Synchronisierungs-Agents in der Region zu entfernen.

   Wählen Sie **OK** aus, und warten Sie, bis die Synchronisierungsgruppe erstellt und bereitgestellt wurde.
   
1. Wenn Sie auf der Seite **Neue Synchronisierungsgruppe** das Kontrollkästchen **Use private link** (Private Verbindung verwenden) aktiviert haben, müssen Sie die Verbindung mit dem privaten Endpunkt genehmigen. Über den Link in der Infomeldung gelangen Sie zur Umgebung für Verbindungen mit privaten Endpunkten, wo Sie die Verbindung genehmigen können. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>Hinzufügen von Synchronisierungsmitgliedern

Nach der Erstellung und Bereitstellung der neuen Synchronisierungsgruppe ist auf der Seite **Neue Synchronisierungsgruppe** die Option **Synchronisierungsmitglieder hinzufügen** (Schritt 2) hervorgehoben.

Geben Sie im Abschnitt **Hub-Datenbank** die vorhandenen Anmeldeinformationen für den Server ein, auf dem sich die Hub-Datenbank befindet. Geben Sie in diesem Abschnitt *keine* neuen Anmeldeinformationen ein.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>So fügen Sie eine Datenbank in Azure SQL-Datenbank hinzu

Im Abschnitt **Mitgliedsdatenbank** können Sie optional eine Datenbank in Azure SQL-Datenbank zur Synchronisierungsgruppe hinzufügen, indem Sie **Azure SQL-Datenbank hinzufügen** auswählen. Die Seite **Azure SQL-Datenbank konfigurieren** wird geöffnet.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  Ändern Sie auf der Seite **Azure SQL-Datenbank konfigurieren** die folgenden Einstellungen:

  | Einstellung                       | BESCHREIBUNG |
  | ----------------------------- | ------------------------------------------------- |
  | **Name des Synchronisierungsmitglieds** | Geben Sie einen Namen für das neue Synchronisierungsmitglied an. Dieser Name unterscheidet sich vom Namen der Datenbank. |
  | **Abonnement** | Wählen Sie das verknüpfte Azure-Abonnement für die Abrechnung aus. |
  | **Azure SQL Server** | Wählen Sie den vorhandenen Server aus. |
  | **Azure SQL-Datenbank** | Wählen Sie die vorhandene Datenbank in SQL-Datenbank aus. |
  | **Synchronisierungsrichtungen** | Wählen Sie **Bidirektionale Synchronisierung**, **Zum Hub** oder **Vom Hub** aus. |
  | **Benutzername** und **Kennwort** | Geben Sie die vorhandenen Anmeldeinformationen für den Server ein, auf dem sich die Mitgliedsdatenbank befindet. Geben Sie in diesem Abschnitt *keine* neuen Anmeldeinformationen ein. |
  | **Use private link** (Private Verbindung verwenden) | Wählen Sie einen dienstseitig verwalteten privaten Endpunkt aus, um eine sichere Verbindung zwischen dem Synchronisierungsdienst und der Mitgliedsdatenbank einzurichten. |

  Klicken Sie auf **OK**, und warten Sie, bis die neue Synchronisierungsmitglied erstellt und bereitgestellt wird.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Hinzufügen einer SQL Server-Datenbank

Im Abschnitt **Mitgliedsdatenbank** können Sie der Synchronisierungsgruppe optional eine SQL Server-Datenbank hinzufügen, indem Sie die Option **Lokale Datenbank hinzufügen** auswählen. Auf der daraufhin geöffneten Seite **Lokale Konfiguration** haben Sie folgende Möglichkeiten:

1. Klicken Sie auf **Synchronisierungs-Agent-Gateway auswählen**. Die Seite **Synchronisierungs-Agent auswählen** wird geöffnet.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. Wählen Sie auf der Seite **Synchronisierungs-Agent auswählen** aus, ob Sie einen vorhandenen Agent verwenden oder einen Agent erstellen möchten.

   Wenn Sie sich für **Vorhandene Agents** entscheiden, wählen Sie den vorhandenen Agent aus der Liste aus.

   Wenn Sie sich für **Neuen Agent erstellen** entscheiden, führen Sie folgende Schritte aus:

   1. Laden Sie über den bereitgestellten Link den Datensynchronisierungs-Agent herunter, und installieren Sie ihn auf dem Computer, auf dem sich der SQL-Server befindet. Sie können den Agent auch direkt unter [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693) herunterladen.

      > [!IMPORTANT]
      > Sie müssen den ausgehenden TCP-Port 1433 in der Firewall öffnen, damit der Client-Agent mit dem Server kommunizieren kann.

   1. Geben Sie einen Namen für den Agent ein.

   1. Wählen Sie **Schlüssel erstellen und generieren** aus, und kopieren Sie den Agent-Schlüssel in die Zwischenablage.

   1. Klicken Sie auf **OK**, um die Seite **Synchronisierungs-Agent auswählen** zu schließen.

1. Suchen Sie auf den SQL-Servercomputer nach der Anwendung des Clientsynchronisierungs-Agents, und führen Sie diese aus.

   ![Die Anwendung des Clientsynchronisierungs-Agents](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. Klicken Sie in der Synchronisierungs-Agent-Anwendung auf **Submit Agent Key** (Agent-Schlüssel übermitteln). Das Dialogfeld **Sync Metadata Database Configuration** (Konfiguration der Datenbank für Synchronisierungsmetadaten) wird geöffnet.

    1. Fügen Sie im Dialogfeld **Konfiguration der Datenbank für Synchronisierungsmetadaten** den Agent-Schlüssel ein, den Sie aus dem Azure-Portal kopiert haben. Geben Sie auch die vorhandenen Anmeldeinformationen für den Server an, auf dem sich die Datenbank für Metadaten befindet. (Wenn Sie eine Datenbank für Metadaten erstellt haben, befindet sich diese Datenbank auf dem gleichen Server wie die Hub-Datenbank.) Klicken Sie auf **OK**, und warten Sie, bis die Konfiguration abgeschlossen ist.

        ![Eingabe des Agent-Schlüssels und der Anmeldeinformationen des Servers](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Sollte ein Firewallfehler auftreten, erstellen Sie in Azure eine Firewallregel, um eingehenden Datenverkehr des SQL Server-Computers zuzulassen. Sie können die Regel manuell über das Portal oder in SQL Server Management Studio (SSMS) erstellen. Stellen Sie in SSMS eine Verbindung mit der Hub-Datenbank in Azure her, indem Sie ihren Namen im Format „<Name der Hub-Datenbank>.database.windows.net“ eingeben.

    1. Wählen Sie **Registrieren** aus, um eine SQL Server-Datenbank beim Agent zu registrieren. Das Dialogfeld **SQL Server-Konfiguration** wird geöffnet.

        ![Hinzufügen und Konfigurieren einer SQL Server-Datenbank](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. Wählen Sie im Dialogfeld **SQL Server-Konfiguration** aus, ob bei der Verbindungsherstellung die SQL Server-Authentifizierung oder die Windows-Authentifizierung verwendet werden soll. Wenn Sie sich für die SQL Server-Authentifizierung entscheiden, geben Sie die vorhandenen Anmeldeinformationen ein. Geben Sie den SQL Server-Namen und den Namen der Datenbank an, die Sie synchronisieren möchten, und wählen Sie **Verbindung testen** aus, um Ihre Einstellungen zu testen. Wählen Sie anschließend **Speichern** aus. Die registrierte Datenbank wird daraufhin in der Liste angezeigt.

        ![Die SQL Server-Datenbank ist nun registriert.](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Schließen Sie App „Clientsynchronisierungs-Agent“.

1. Wählen Sie im Portal auf der Seite **Lokale Konfiguration** die Option **Datenbank auswählen** aus.

1. Geben Sie auf der Seite **Datenbank auswählen** im Feld **Name des Synchronisierungsmitglieds** einen Namen für das neue Synchronisierungsmitglied an. Dieser Name unterscheidet sich vom Namen der Datenbank. Wählen Sie die Datenbank aus der Liste aus. Wählen Sie im Feld **Synchronisierungsrichtungen** die Option **Bidirektionale Synchronisierung**, **Zum Hub** oder **Vom Hub** aus.

    ![Wählen Sie die lokale Datenbank](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Klicken Sie auf **OK**, um die Seite **Datenbank auswählen** zu schließen. Klicken Sie dann auf **OK**, um die Seite **Lokale Konfiguration** zu schließen, und warten Sie, bis das neue Synchronisierungsmitglied erstellt und bereitgestellt wurde. Wählen abschließend **OK** aus, um die Seite **Synchronisierungsmitglieder auswählen** zu schließen.

> [!NOTE]
> Um eine Verbindung mit der SQL-Datensynchronisierung und dem lokalen Agent herzustellen, fügen Sie Ihren Benutzername der Rolle *DataSync_Executor* hinzu. Die Datensynchronisierung erstellt diese Rolle auf der SQL Server-Instanz.

## <a name="configure-sync-group"></a>Konfigurieren der Synchronisierungsgruppe

Nach der Erstellung und Bereitstellung der neuen Synchronisierungsgruppenmitglieder ist auf der Seite **Neue Synchronisierungsgruppe** die Option **Synchronisierung konfigurieren** (Schritt 3) hervorgehoben.

![Einstellungen für Schritt 3](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. Wählen Sie auf der Seite **Tabellen** eine Datenbank aus der Liste der Synchronisierungsgruppenmitglieder aus, und wählen Sie anschließend **Schema aktualisieren** aus.

1. Wählen Sie in der Liste die Tabellen aus, die Sie synchronisieren möchten. Standardmäßig sind alle Spalten ausgewählt. Deaktivieren Sie daher die Kontrollkästchen der Spalten, die Sie nicht synchronisieren möchten. Achten Sie darauf, dass die Primärschlüsselspalte ausgewählt bleibt.

1. Wählen Sie **Speichern** aus.

1. Standardmäßig werden Datenbanken erst synchronisiert, wenn dies per Zeitplan konfiguriert oder eine manuelle Synchronisierung ausgeführt wird. Navigieren Sie zum Ausführen einer manuellen Synchronisierung im Azure-Portal zu Ihrer Datenbank in SQL-Datenbank, wählen Sie **Mit anderen Datenbanken synchronisieren** aus, und wählen Sie dann die Synchronisierungsgruppe aus. Die Seite **Datensynchronisierung** wird geöffnet. Klicken Sie auf **Synchronisieren**.

    ![Manuelle Synchronisierung](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Häufig gestellte Fragen

**Wie häufig kann die Datensynchronisierung für meine Daten erfolgen?**

Der Mindestabstand zwischen Synchronisierungen beträgt fünf Minuten.

**Werden von der SQL-Datensynchronisierung Tabellen vollständig erstellt?**

Sollten in der Zieldatenbank Synchronisierungsschematabellen fehlen, werden sie von der SQL-Datensynchronisierung mit den von Ihnen ausgewählten Spalten erstellt. Allerdings wird dabei aus folgenden Gründen kein vollständig identisches Schema erstellt:

- In der Zieltabelle werden nur von Ihnen ausgewählte Spalten erstellt. Nicht ausgewählte Spalten werden ignoriert.
- In der Zieltabelle werden nur ausgewählte Spaltenindizes erstellt. Für nicht ausgewählte Spalten werden diese Indizes ignoriert.
- Es werden keine Indizes für XML-Spalten erstellt.
- Es werden keine CHECK-Einschränkungen erstellt.
- Es werden keine Trigger für die Quelltabellen erstellt.
- Es werden keine Sichten und gespeicherten Prozeduren erstellt.

Aufgrund dieser Einschränkungen wird Folgendes empfohlen:

- Erstellen Sie für Produktionsumgebungen das vollständig identische Schema selbst.
- Verwenden Sie die automatische Bereitstellung, wenn Sie mit dem Dienst experimentieren.

**Warum werden Tabellen angezeigt, die ich nicht erstellt habe?**

Die Datensynchronisierung erstellt in der Datenbank zusätzliche Tabellen für die Änderungsnachverfolgung. Löschen Sie diese Tabellen nicht, da ansonsten die Datensynchronisierung nicht mehr funktioniert.

**Sind meine Daten nach einer Synchronisierung konvergent?**

Nicht unbedingt. Stellen Sie sich beispielsweise eine Synchronisierungsgruppe mit einem Hub und drei Spokes (A, B und C) vor, bei der Synchronisierungen zwischen dem Hub und A, dem Hub und B und dem Hub und C erfolgen. Wenn die Datenbank A *nach* der Synchronisierung des Hubs mit A geändert wird, wird diese Änderung erst im Rahmen der nächsten Synchronisierungsaufgabe in die Datenbank B oder C geschrieben.

**Wie übertrage ich Schemaänderungen in eine Synchronisierungsgruppe?**

Alle Schemaänderungen müssen manuell vorgenommen und verteilt werden.

1. Replizieren Sie die Schemaänderungen manuell auf dem Hub und auf allen Synchronisierungsmitgliedern.
1. Aktualisieren Sie das Synchronisierungsschema.

Informationen zum Hinzufügen neuer Tabellen und Spalten:

Neue Tabellen und Spalten haben keine Auswirkungen auf die aktuelle Synchronisierung. Sie werden von der Datensynchronisierung ignoriert, bis sie dem Synchronisierungsschema hinzugefügt werden. Halten Sie sich beim Hinzufügen neuer Datenbankobjekte an die folgende Reihenfolge:

1. Fügen Sie neue Tabellen oder Spalten dem Hub und allen Synchronisierungsmitgliedern hinzu.
1. Fügen Sie neue Tabellen oder Spalten dem Synchronisierungsschema hinzu.
1. Beginnen Sie mit dem Einfügen von Werten in die neuen Tabellen und Spalten.

Informationen zum Ändern des Datentyps einer Spalte:

Wenn Sie den Datentyp einer vorhandenen Spalte ändern, funktioniert die Datensynchronisierung weiterhin – vorausgesetzt, die neuen Werte sind mit dem ursprünglich im Synchronisierungsschema definierten Datentyp kompatibel. Wenn Sie also beispielsweise in der Quelldatenbank den Typ von **int** in **bigint** ändern, funktioniert die Datensynchronisierung weiterhin, solange Sie keinen Wert einfügen, der für den Datentyp **int** zu groß ist. Replizieren Sie die Schemaänderung manuell auf dem Hub sowie auf allen Synchronisierungsmitgliedern, und aktualisieren Sie anschließend das Synchronisierungsschema, um die Änderung abzuschließen.

**Wie kann ich eine Datenbank mithilfe der Datensynchronisierung exportieren und importieren?**

Nachdem Sie eine Datenbank als *BACPAC-Datei* exportiert und die Datei zum Erstellen einer neuen Datenbank importiert haben, gehen Sie wie folgt vor, um die Datensynchronisierung in der neuen Datenbank zu verwenden:

1. Bereinigen Sie in der neuen Datenbank mithilfe [dieses Skripts](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql) die Datensynchronisierungsobjekte und die zusätzlichen Tabellen. Das Skript löscht alle erforderlichen Datensynchronisierungsobjekte aus der Datenbank.
1. Erstellen Sie die Synchronisierungsgruppe mit der neuen Datenbank neu. Wenn Sie die alte Synchronisierungsgruppe nicht mehr benötigen, löschen Sie sie.

**Wo finde ich Informationen zum Client-Agent?**

Häufig gestellte Fragen zum Client-Agent finden Sie unter [Agent – Häufig gestellte Fragen](sql-data-sync-agent-overview.md#agent-faq).

**Muss die private Verbindung manuell genehmigt werden, damit sie verwendet werden kann?**

Ja. Sie müssen den dienstseitig verwalteten privaten Endpunkt manuell genehmigen – entweder während der Synchronisierungsgruppenbereitstellung im Azure-Portal auf der Seite mit den Verbindungen mit privaten Endpunkten oder mithilfe von PowerShell.

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch. Sie haben nun eine Synchronisierungsgruppe erstellt, die sowohl eine SQL-Datenbank-Instanz als auch eine SQL Server-Datenbank enthält.

Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter:

- [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](sql-data-sync-agent-overview.md)
- [Bewährte Methoden für die SQL-Datensynchronisierung](sql-data-sync-best-practices.md) und [Behandeln von Problemen mit der SQL-Datensynchronisierung](sql-data-sync-troubleshoot.md)
- [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](./monitor-tune-overview.md)
- [Aktualisieren des Synchronisierungsschemas mit Transact-SQL](sql-data-sync-update-sync-schema.md) oder mit [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Weitere Informationen zu SQL-Datenbank finden Sie unter:

- [Übersicht über die SQL-Datenbank](sql-database-paas-overview.md)
- [Datenbank-Lebenszyklusverwaltung](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
