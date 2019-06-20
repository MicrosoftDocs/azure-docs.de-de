---
title: Erstellen und Verwalten von Lesereplikaten in Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie im Portal Lesereplikate in Azure Database for MySQL einrichten und verwalten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b422718a1eaec483acdc2c8ab37442b9aea78aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510765"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for MySQL mithilfe des Azure-Portals

In diesem Artikel erfahren Sie, wie Sie Lesereplikate im Azure Database for MySQL-Dienst über das Azure-Portal erstellen und verwalten.

> [!IMPORTANT]
> Sie können ein Lesereplikat in derselben Region wie Ihren Masterserver oder in einer anderen beliebigen Azure-Region erstellen. Die regionsübergreifende Replikation befindet sich derzeit in der öffentlichen Vorschauversion.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-portal.md), der als Masterserver verwendet wird

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Masterserver einer dieser Tarife festgelegt ist.

## <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

Ein Lesereplikatserver kann mit den folgenden Schritten erstellt werden:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie den vorhandenen Azure Database for MySQL-Server aus, den Sie als Masterserver verwenden möchten. Mit dieser Aktion wird die Seite **Übersicht** geöffnet.

3. Wählen Sie im Menü unter **EINSTELLUNGEN** die Option **Replikation** aus.

4. Wählen Sie **Replikat hinzufügen**.

   ![Azure Database for MySQL: Replikation](./media/howto-read-replica-portal/add-replica.png)

5. Geben Sie einen Namen für den Replikatserver ein.

    ![Azure Database for MySQL – Replikatname](./media/howto-read-replica-portal/replica-name.png)

6. Wählen Sie den Standort für den Replikatserver aus. Sie können ein Replikat in einer beliebigen Azur-Region erstellen. Der Standardstandort ist derselbe wie beim Masterserver.

    ![Azure Database for MySQL – Replikatstandort](./media/howto-read-replica-portal/replica-location.png)

7. Wählen Sie **OK** aus, um die Erstellung des Replikats zu bestätigen.

> [!NOTE]
> Lesereplikate werden mit der gleichen Serverkonfiguration wie der Masterserver erstellt. Die Replikatserverkonfiguration kann nach der Erstellung geändert werden. Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Masterserver, damit das Replikat über genügend Kapazität verfügt.

Nach der Erstellung des Replikatservers kann dieser auf dem Blatt **Replikation** angezeigt werden.

   ![Azure Database for MySQL: Auflisten der Replikate](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver

> [!IMPORTANT]
> Das Beenden der Replikation auf einem Server kann nicht rückgängig gemacht werden. Wenn die Replikation zwischen einem Master und dem Replikat beendet wurde, kann dies nicht rückgängig gemacht werden. Der Replikatserver wird zu einem eigenständigen Server und unterstützt nun Lese- und Schreibvorgänge. Der Server kann nicht wieder in ein Replikat umgewandelt werden.

Führen Sie die folgenden Schritte aus, um die Replikation zwischen einem Masterserver und einem Replikatserver im Azure-Portal zu beenden:

1. Wählen Sie im Azure-Portal Ihren Azure Database for MySQL-Masterserver aus. 

2. Wählen Sie im Menü unter **EINSTELLUNGEN** die Option **Replikation** aus.

3. Wählen Sie den Replikatserver aus, für den Sie die Replikation beenden möchten.

   ![Azure Database for MySQL: Beenden der Replikation, Auswählen des Servers](./media/howto-read-replica-portal/stop-replication-select.png)

4. Wählen Sie **Replikation beenden** aus.

   ![Azure Database for MySQL: Replikation beenden](./media/howto-read-replica-portal/stop-replication.png)

5. Klicken Sie auf **OK**, um zu bestätigen, dass Sie die Replikation beenden möchten.

   ![Azure Database for MySQL: Bestätigen, dass die Replikation beendet werden soll](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Führen Sie die folgenden Schritte aus, um einen Lesereplikatserver im Azure-Portal zu löschen:

1. Wählen Sie im Azure-Portal Ihren Azure Database for MySQL-Masterserver aus.

2. Wählen Sie im Menü unter **EINSTELLUNGEN** die Option **Replikation** aus.

3. Wählen Sie den Replikatserver aus, den Sie löschen möchten.

   ![Azure Database for MySQL: Replikat löschen, Auswählen des Servers](./media/howto-read-replica-portal/delete-replica-select.png)

4. Wählen Sie **Replikat löschen** aus.

   ![Azure Database for MySQL: Replikat löschen](./media/howto-read-replica-portal/delete-replica.png)

5. Geben Sie den Namen des Replikats ein, und klicken Sie auf **Löschen**, um das Löschen des Replikats zu bestätigen.  

   ![Azure Database for MySQL: Bestätigen der Replikatlöschung](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Löschen eines Masterservers

> [!IMPORTANT]
> Wenn Sie einen Masterserver löschen, wird die Replikation auf allen Replikatservern beendet und der Masterserver selbst gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Führen Sie die folgenden Schritte aus, um einen Masterserver im Azure-Portal zu löschen:

1. Wählen Sie im Azure-Portal Ihren Azure Database for MySQL-Masterserver aus.

2. Wählen Sie unter **Übersicht** die Option **Löschen** aus.

   ![Azure Database for MySQL: Löschen eines Masterservers](./media/howto-read-replica-portal/delete-master-overview.png)

3. Geben Sie den Namen des Masterservers ein, und klicken Sie auf **Löschen**, um das Löschen des Masterservers zu bestätigen.  

   ![Azure Database for MySQL: Löschen eines Masterservers](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Überwachen der Replikation

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) den zu überwachenden Azure Database for MySQL-Replikatserver aus.

2. Wählen Sie auf der Seitenleiste im Abschnitt **Überwachung** die Option **Metriken** aus:

3. Wählen Sie in der Dropdownliste der verfügbaren Metriken die Option **Replication lag in seconds** (Replikationsverzögerung in Sekunden) aus.

   ![Auswählen der Replikationsverzögerung](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Wählen Sie den Zeitraum aus, den Sie anzeigen möchten. In der folgenden Abbildung wird ein Zeitraum von 30 Minuten ausgewählt.

   ![Auswählen eines Zeitbereichs](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zeigen Sie die Replikationsverzögerung für den ausgewählten Zeitraum an. Die folgende Abbildung zeigt die letzten 30 Minuten.

   ![Auswählen eines Zeitbereichs](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Lesereplikaten](concepts-read-replicas.md)