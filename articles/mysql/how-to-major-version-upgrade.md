---
title: Hauptversionsupgrade in Azure Database for MySQL Single Server
description: In diesem Artikel wird beschrieben, wie Sie ein Upgrade der Hauptversion für Azure Database for MySQL Single Server durchführen.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: ea2dc877c7bc6db387985e7b5cd1153e195ab4f1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509569"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Hauptversionsupgrade in Azure Database for MySQL Single Server

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff _Slave_, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.
>

> [!IMPORTANT]
> Das Hauptversionsupgrade für Azure Database for MySQL Single Server befindet sich in der öffentlichen Vorschauphase.

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal ein direktes Upgrade Ihrer MySQL-Hauptversion für Azure Database for MySQL Single Server durchführen.

Mit diesem Feature können Kunden einfach per Klick direkte Upgrades ihrer MySQL 5.6-Server auf MySQL-Version 5.7 durchführen, ohne dass Daten verschoben oder Verbindungszeichenfolgen für Anwendungen geändert werden müssen.

> [!Note]
> * Hauptversionsupgrade ist nur für das Upgrade von MySQL 5.6 auf MySQL 5.7 verfügbar.
> * Der Server ist während des gesamten Upgradevorgangs nicht verfügbar. Es wird daher empfohlen, Upgrades während Ihres geplanten Wartungsfensters durchzuführen. Sie können in Erwägung ziehen, [ein Hauptversionsupgrade von MySQL 5.6 auf MySQL 5.7 mit minimaler Ausfallzeit unter Verwendung von Lesereplikaten durchzuführen.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Durchführung des Hauptversionsupgrades von MySQL 5.6 auf MySQL 5.7 über das Azure-Portal

Führen Sie die folgenden Schritte aus, um ein Hauptversionsupgrade für Ihren Azure Database for MySQL-Server 5.6 über das Azure-Portal durchzuführen:

> [!IMPORTANT]
> Wir empfehlen, das Upgrade zunächst an einer wiederhergestellten Kopie des Servers durchzuführen, anstatt direkt ein Upgrade der Produktionsumgebung durchzuführen. Weitere Informationen finden Sie unter [Durchführen einer Zeitpunktwiederherstellung](howto-restore-server-portal.md#point-in-time-restore).

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen Azure Database for MySQL-Server 5.6 aus.

2. Klicken Sie auf der Seite **Übersicht** in der Symbolleiste auf die Schaltfläche **Upgrade**.

3. Wählen Sie im Abschnitt **Upgrade** die Option **OK** aus, um den Azure Database for MySQL-Server 5.6 auf Version 5.7 zu aktualisieren.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL: Übersicht über Upgrade":::

4. Mit einer Benachrichtigung wird bestätigt, dass der Upgradevorgang erfolgreich war.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Durchführung des Hauptversionsupgrades von MySQL 5.6 auf MySQL 5.7 über die Azure-Befehlszeilenschnittstelle

Führen Sie die folgenden Schritte aus, um ein Hauptversionsupgrade für Ihren Azure Database for MySQL-Server 5.6 über die Azure-Befehlszeilenschnittstelle durchzuführen:

> [!IMPORTANT]
> Wir empfehlen, das Upgrade zunächst an einer wiederhergestellten Kopie des Servers durchzuführen, anstatt direkt ein Upgrade der Produktionsumgebung durchzuführen. Weitere Informationen finden Sie unter [Durchführen einer Zeitpunktwiederherstellung](howto-restore-server-cli.md#server-point-in-time-restore).

1. Installieren Sie die [Azure-Befehlszeilenschnittstelle für Windows](/cli/azure/install-azure-cli) oder verwenden Sie die Azure-Befehlszeilenschnittstelle in [Azure Cloud Shell](../cloud-shell/overview.md), um die Upgradebefehle auszuführen. 
 
   Für dieses Upgrade ist mindestens Version 2.16.0 oder höher der Azure-Befehlszeilenschnittstelle erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. Führen Sie az version aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie az upgrade aus, um das Upgrade auf die aktuelle Version durchzuführen.

2. Nachdem Sie sich angemeldet haben, führen Sie den Befehl [az mysql server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) aus:

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   Die Eingabeaufforderung zeigt die Meldung „-Running“ (Wird ausgeführt) an. Nachdem diese Meldung nicht mehr angezeigt wird, ist das Versionsupgrade abgeschlossen.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Durchführen des Hauptversionsupgrades von MySQL 5.6 auf MySQL 5.7 mithilfe von Lesereplikaten über das Azure-Portal

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen Lesereplikatserver für Azure Database for MySQL 5.6 aus.

2. Klicken Sie auf der Seite **Übersicht** in der Symbolleiste auf die Schaltfläche **Upgrade**.

3. Wählen Sie im Abschnitt **Upgrade** die Option **OK** aus, um den Lesereplikatserver für Azure Database for MySQL 5.6 auf Version 5.7 zu aktualisieren.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL: Übersicht über Upgrade":::

4. Mit einer Benachrichtigung wird bestätigt, dass der Upgradevorgang erfolgreich war.

5. Vergewissern Sie sich auf der Seite **Übersicht**, dass Ihr Lesereplikatserver für Azure Database for MySQL die Version 5.7 hat.

6. Wechseln Sie nun zu Ihrem primären Server, und [führen Sie darauf das Hauptversionsupgrade aus](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal).

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Durchführen eines Hauptversionsupgrades von MySQL 5.6 auf MySQL 5.7 mit minimaler Ausfallzeit unter Verwendung von Lesereplikaten

Sie können ein Hauptversionsupgrade von MySQL 5.6 auf MySQL 5.7 unter Verwendung von Lesereplikaten mit minimaler Ausfallzeit durchführen. Sie sollten zunächst das Lesereplikat Ihres Servers auf 5.7 aktualisieren und später Ihre Anwendung per Failover auf das Lesereplikat verweisen lassen und es zum neuen primären Server machen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandene Instanz von Azure Database for MySQL 5.6 aus.

2. Erstellen Sie ein [Lesereplikat](https://docs.microsoft.com/azure/mysql/concepts-read-replicas#create-a-replica) des primären Servers.

3. [Aktualisieren Sie das Lesereplikat](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) auf Version 5.7.

4. Sobald Sie sich davon überzeugt haben, dass der Replikatserver mit Version 5.7 läuft, verhindern Sie, dass Ihre Anwendung sich mit dem primären Server verbindet.
 
5. Überprüfen Sie den Replikationsstatus, und stellen Sie sicher, dass das Replikat alle Daten mit dem primären Server synchronisiert hat und dass keine neuen Vorgänge im primären Server ausgeführt werden.

   Rufen Sie den Befehl [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) auf dem Replikatserver auf, um den Replikationsstatus anzuzeigen.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Wenn der Status von `Slave_IO_Running` und `Slave_SQL_Running` „yes“ lautet und der Wert von `Seconds_Behind_Master` „0“ ist, funktioniert die Replikation ordnungsgemäß. `Seconds_Behind_Master` gibt an, wie stark das Replikat verzögert ist. Wenn der Wert nicht „0“ ist, bedeutet dies, dass das Replikat Updates verarbeitet. Sobald Sie bestätigen, dass `Seconds_Behind_Master` den Wert 0 hat, kann die Replikation sicher beendet werden.

6. Stufen Sie Ihr Lesereplikat zum primären Replikat höher, indem Sie [die Replikation anhalten](https://docs.microsoft.com/azure/mysql/howto-read-replicas-portal#stop-replication-to-a-replica-server).

7. Zeigen Sie mit Ihrer Anwendung auf das neue primäre (bisherige Replikat), in dem Server 5.7 läuft. Jeder Server verfügt über eine eindeutige Verbindungszeichenfolge. Aktualisieren Sie Ihre Anwendung so, dass Sie auf das (ehemalige) Replikat und nicht auf die Quelle verweist.

> [!Note]
> In diesem Szenario kommt es nur während der Schritte 4, 5 und 6 zu Ausfallzeiten.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Wann wird dieses Upgrade-Feature allgemein verfügbar sein, da wir MySQL v5.6 in unserer Produktionsumgebung verwenden, das ein Upgrade erfordert?

Die allgemeine Verfügbarkeit dieses Features ist vor der Einstellung von MySQL v5.6 geplant. Das Feature ist jedoch bereit für die Produktionsumgebung und wird von Azure voll unterstützt, sodass Sie es mit Zuversicht in Ihrer Umgebung ausführen können sollten. Als empfohlene bewährte Methode empfehlen wir Ihnen dringend, es zuerst auf einer wiederhergestellten Kopie des Servers auszuführen und zu testen, sodass Sie die Downtime während des Upgrades abschätzen können, und einen Anwendungskompatibilitätstest durchzuführen, bevor Sie es in der Produktionsumgebung ausführen. Weitere Informationen zum Erstellen einer Zeitpunktkopie Ihres Servers finden Sie unter [Durchführen einer Zeitpunktwiederherstellung](howto-restore-server-portal.md#point-in-time-restore). 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Führt dies zu einer Downtime des Servers und wenn ja, wie lange dauert dies?

Ja, der Server wird während des Upgradeprozesses nicht verfügbar sein, sodass wir Ihnen empfehlen, diesen Vorgang während Ihres geplanten Wartungsfensters durchzuführen. Die geschätzte Downtime hängt von der Datenbankgröße, der bereitgestellten Speichergröße (bereitgestellte IOPs) und der Anzahl der Tabellen in der Datenbank ab. Die Upgradezeit verhält sich direkt proportional zur Anzahl der Tabellen auf dem Server. Die Upgrades von Basic-SKU-Servern werden voraussichtlich länger dauern, da es sich um eine Standardspeicherplattform handelt. Um die Downtime für Ihre Serverumgebung abzuschätzen, empfehlen wir, das Upgrade zunächst auf einer wiederhergestellten Kopie des Servers durchzuführen. Ziehen Sie es in Betracht, [ein Hauptversionsupgrade von MySQL 5.6 auf MySQL 5.7 mit minimaler Ausfallzeit unter Verwendung von Lesereplikaten durchzuführen.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Was passiert, wenn wir das Upgrade unseres MySQL v5.6-Servers nicht vor dem 5. Februar 2021 durchführen?

Sie können Ihren MySQL v5.6-Server weiterhin wie bisher betreiben. Azure **wird niemals** ein erzwungenes Upgrade auf Ihrem Server durchführen. Es gelten jedoch die in der Richtlinie [Azure Database for MySQL: Versionsrichtlinie](concepts-version-policy.md) dokumentierten Einschränkungen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Azure Database for MySQL: Versionsrichtlinie](concepts-version-policy.md).