---
title: Verwalten von Lesereplikaten – Azure-Portal – Azure Database for PostgreSQL – Einzelserver
description: Erfahren Sie, wie Sie Lesereplikate für Azure Database for PostgreSQL verwalten – Einzelserver über das Azure-Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 9fdef187e9bdf77b29c548f767a4b4edfeb62f44
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422177"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Erstellen und Verwalten von Lesereplikaten in Azure Database for PostgreSQL – Einzelserver über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie Lesereplikate in Azure Database for PostgreSQL über das Azure-Portal erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-read-replicas.md).


## <a name="prerequisites"></a>Voraussetzungen
Ein [Azure Database for PostgreSQL-Server](quickstart-create-server-database-portal.md), der als primärer Server verwendet wird.

## <a name="azure-replication-support"></a>Azure-Replikationsunterstützung

[Lesereplikate](concepts-read-replicas.md) und [logische Decodierung](concepts-logical.md) sind beide vom Write-Ahead-Protokoll (WAL) von Postgres abhängig. Diese beiden Features erfordern unterschiedliche Ebenen der Protokollierung durch Postgres. Die logische Decodierung erfordert einen höheren Protokolliergrad als Lesereplikate.

Um den richtigen Protokolliergrad zu konfigurieren, verwenden Sie den Parameter für die Unterstützung der Azure-Replikation. Für die Unterstützung der Azure-Replikation gibt es drei Einstellungsoptionen:

* **Off**: Speichert am wenigsten Informationen im Write-Ahead-Protokoll. Diese Einstellung ist auf den meisten Azure Database for PostgreSQL-Servern nicht verfügbar.  
* **Replica**: Ausführlichere Informationen als bei **Off**. Dies ist der mindestens erforderliche Protokolliergrad, damit [Lesereplikate](concepts-read-replicas.md) funktionieren. Auf den meisten Servern ist dies die Standardeinstellung.
* **Logical**: Noch ausführlichere Informationen als bei **Replica**. Dies ist der mindestens erforderliche Protokolliergrad, damit die logische Decodierung funktioniert. Lesereplikate funktionieren bei dieser Einstellung ebenfalls.


> [!NOTE]
> Beim Bereitstellen von Lesereplikaten für persistente, sehr schreibintensive primäre Workloads kann sich die Replikation immer weiter verzögern, sodass der Stand des primären Servers möglicherweise gar nicht mehr erreicht wird. Dadurch kann auch die Speicherauslastung auf dem primären Server ansteigen, weil die WAL-Dateien erst gelöscht werden, wenn sie im Replikat empfangen wurden.

## <a name="prepare-the-primary-server"></a>Vorbereiten des primären Servers

1. Wählen Sie einen vorhandenen Azure Database for PostgreSQL-Server, den Sie als Masterserver verwenden möchten, im Azure-Portal aus.

2. Wählen Sie im Menü des Servers **Replikation** aus. Wenn die Azure-Replikationsunterstützung mindestens auf **Replica** festgelegt ist, können Sie Lesereplikate erstellen. 

3. Wenn die Azure-Replikationsunterstützung nicht auf mindestens **Replica** festgelegt ist, legen Sie sie entsprechend fest. Wählen Sie **Speichern** aus.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL – Replikation – Replikat festlegen und speichern":::

4. Starten Sie den Server neu, um die Änderung zu übernehmen, indem Sie **Ja** auswählen.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL – Replikation – Neustart bestätigen":::

5. Sie erhalten zwei Azure-Portalbenachrichtigungen, sobald der Vorgang abgeschlossen ist. Es gibt eine Benachrichtigung zur Aktualisierung des Serverparameters. Eine weitere Benachrichtigung bezieht sich auf den Neustart des Servers, der unmittelbar erfolgt.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Erfolgsbenachrichtigungen":::

6. Aktualisieren Sie die Azure-Portalseite zum Aktualisieren der Replikationssymbolleiste. Sie können jetzt schreibgeschützte Replikate für diesen Server erstellen.
   

## <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats
Führen Sie die folgenden Schritte aus, um ein Lesereplikat zu erstellen:

1. Wählen Sie einen vorhandenen Azure Database for PostgreSQL-Server aus, den Sie als primären Server verwenden möchten. 

2. Wählen Sie auf der Randleiste des Servers unter **EINSTELLUNGEN** die Option **Replikation** aus.

3. Wählen Sie **Replikat hinzufügen**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Hinzufügen eines Replikats":::

4. Geben Sie einen Namen für das Lesereplikat ein. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Benennen des Replikats":::

5. Wählen Sie einen Standort für das Replikat aus. Der Standardstandort ist mit dem des primären Servers identisch.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Standort auswählen":::

   > [!NOTE]
   > Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

6. Wählen Sie **OK**, um die Erstellung des Replikats zu bestätigen.

Nach der Erstellung des Lesereplikats kann dieses im Fenster **Replikation** angezeigt werden:

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Anzeigen des neuen Replikats im Fenster „Replikation“":::
 

> [!IMPORTANT]
> Lesen Sie den [Abschnitt „Überlegungen“ in der Übersicht über Lesereplikate](concepts-read-replicas.md#considerations).
>
> Bevor eine Primärservereinstellung auf einen neuen Wert aktualisiert wird, aktualisieren Sie die Replikateinstellung auf den gleichen oder einen größeren Wert. Diese Aktion sorgt dafür, dass das Replikat mit allen Änderungen auf dem Masterserver Schritt halten kann.

## <a name="stop-replication"></a>Beenden der Replikation
Sie können die Replikation zwischen einem primären Server und einem Lesereplikat beenden.

> [!IMPORTANT]
> Das Beenden der Replikation zwischen einem primären Server und einem Lesereplikat kann nicht mehr rückgängig gemacht werden. Das Lesereplikat wird zu einem eigenständigen Server, der sowohl Lese- als auch Schreibvorgänge unterstützt. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

Führen Sie die folgenden Schritte aus, um die Replikation zwischen einem primären Server und einem Lesereplikat über das Azure-Portal zu beenden:

1. Wählen Sie im Azure-Portal Ihren primären Azure Database for PostgreSQL-Server aus.

2. Wählen Sie im Servermenü unter **EINSTELLUNGEN** die Option **Replikation** aus.

3. Wählen Sie den Replikatserver aus, für den Sie die Replikation beenden möchten.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Auswählen des Replikats":::
 
4. Wählen Sie **Replikation beenden** aus.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Auswählen von „Replikation beenden“":::
 
5. Wählen Sie **OK**, um die Replikation zu beenden.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Bestätigen der Beendigung der Replikation":::
 

## <a name="delete-a-primary-server"></a>Löschen eines primären Servers
Für das Löschen eines primären Servers führen Sie die gleichen Schritte wie für einen eigenständigen Azure Database for PostgreSQL-Server durch. 

> [!IMPORTANT]
> Wenn Sie einen primären Server löschen, wird die Replikation auf allen Lesereplikaten beendet. Die Lesereplikate werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Um einen Server über das Azure-Portal zu löschen, gehen Sie folgendermaßen vor:

1. Wählen Sie im Azure-Portal Ihren primären Azure Database for PostgreSQL-Server aus.

2. Öffnen Sie die Seite **Übersicht** für den Server. Klicken Sie auf **Löschen**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Auswählen des Löschvorgangs für den primären Server auf der Übersichtsseite des Servers":::
 
3. Geben Sie den Namen des zu löschenden primären Servers ein. Wählen Sie **Löschen** aus, um das Löschen des primären Servers zu bestätigen.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Bestätigen des Löschvorgangs für den primären Server":::
 

## <a name="delete-a-replica"></a>Löschen eines Replikats
Sie können ein Lesereplikat auf ähnliche Weise löschen wie einen primären Server.

- Öffnen Sie im Azure-Portal die Seite **Übersicht** für das Lesereplikat. Klicken Sie auf **Löschen**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Auswählen des Löschvorgangs für das Replikat auf der Übersichtsseite des Replikats":::
 
Sie können das Lesereplikat auch über das Fenster **Replikation** löschen, indem Sie folgendermaßen vorgehen:

1. Wählen Sie im Azure-Portal Ihren primären Azure Database for PostgreSQL-Server aus.

2. Wählen Sie im Servermenü unter **EINSTELLUNGEN** die Option **Replikation** aus.

3. Wählen Sie das zu löschende Lesereplikat aus.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Auswahl des zu löschenden Lesereplikats":::
 
4. Wählen Sie **Replikat löschen** aus.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Auswahl von „Replikat löschen“":::
 
5. Geben Sie den Namen des zu löschenden Replikats ein. Wählen Sie **Löschen**, um das Löschen des Replikats zu bestätigen.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Bestätigen des Löschvorgangs für das Replikat":::
 

## <a name="monitor-a-replica"></a>Überwachen eines Replikats
Für die Überwachung von Lesereplikaten stehen zwei Metriken zur Verfügung.

### <a name="max-lag-across-replicas-metric"></a>Metrik „Maximale Verzögerung zwischen Replikaten“
Die Metrik **Maximale Verzögerung zwischen Replikaten** zeigt die Verzögerung in Byte zwischen dem primären Server und dem Replikat mit der größten Verzögerung. 

1.  Wählen Sie im Azure-Portal den primären Azure Database for PostgreSQL-Server aus.

2.  Klicken Sie auf **Metriken**. Wählen Sie im Fenster **Metriken** die Option **Maximale Verzögerung zwischen Replikaten** aus.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Überwachen der maximalen Verzögerung zwischen Replikaten":::
 
3.  Wählen Sie für Ihre **Aggregation** den Wert **Max** aus.


### <a name="replica-lag-metric"></a>Metrik „Replikatverzögerung“
Die Metrik **Replikatverzögerung** zeigt die Zeit seit der letzten wiedergegebenen Transaktion in einem Replikat an. Wenn keine Transaktionen auf dem Masterserver stattfinden, gibt die Metrik diese Verzögerungszeit wieder.

1. Wählen Sie im Azure-Portal das Azure Database for PostgreSQL-Lesereplikat aus.

2. Klicken Sie auf **Metriken**. Wählen Sie im Fenster **Metriken** die Option **Replikatverzögerung** aus.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Überwachen der Replikatverzögerung":::
 
3. Wählen Sie für Ihre **Aggregation** den Wert **Max** aus. 
 
## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Lesereplikate in Azure Database for PostgreSQL](concepts-read-replicas.md).
* Erfahren Sie, wie Sie [Lesereplikate in der Azure-Befehlszeilenschnittstelle (Azure CLI) und der REST-API erstellen und verwalten](howto-read-replicas-cli.md).