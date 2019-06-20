---
title: Konfigurieren von Serverparametern in Azure Database for MariaDB
description: In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal MariaDB-Serverparameter in Azure Database for MariaDB konfigurieren.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c618a4035e9ec9b1ca1986e898ea1060ac05712d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60922525"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Konfigurieren von Serverparametern in Azure Database for MariaDB über das Azure-Portal

Azure Database for MariaDB unterstützt das Konfigurieren einiger Serverparameter. In diesem Artikel wird beschrieben, wie diese Parameter mithilfe des Azure-Portals konfiguriert werden. Nicht alle Serverparameter können angepasst werden.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigieren Sie im Azure-Portal zu „Serverparameter“.

1. Melden Sie sich beim Azure-Portal an, und suchen Sie dann nach Ihrem Azure Database for MariaDB-Server.
2. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter**, um die Seite „Serverparameter“ für den Server mit Azure Database for MariaDB zu öffnen.
![Seite „Serverparameter“ im Azure-Portal](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Suchen Sie die Einstellungen, die Sie anpassen möchten. Überprüfen Sie die Spalte **Beschreibung**, um den Zweck und die zulässigen Werte zu verstehen.
![Dropdownliste für Enumerierung](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
![Änderungen speichern oder verwerfen](./media/howto-server-parameters/4-save_parameters.png)
5. Wenn Sie neue Werte für die Parameter gespeichert haben, können Sie jederzeit alles zurück auf die Standardwerte setzen, indem Sie die Option **Alle auf Standard zurücksetzen** wählen.
![Alle auf Standard zurücksetzen](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Liste der konfigurierbaren Serverparameter

Die Liste der unterstützten Serverparameter wächst ständig. Verwenden Sie die Registerkarte mit den Serverparametern im Azure-Portal, um die Definition abzurufen und Serverparameter anhand Ihrer Anwendungsanforderungen zu konfigurieren.

## <a name="non-configurable-server-parameters"></a>Nicht konfigurierbare Serverparameter

„InnoDB-Pufferpool“ und „Max. Anzahl von Verbindungen“ können nicht konfiguriert werden und sind an Ihren [Tarif](concepts-pricing-tiers.md) gebunden.

|**Tarif**| **vCore(s)**|**InnoDB-Pufferpool (MB)**| **Max. Anzahl von Verbindungen**|
|---|---|---|---|
|Basic| 1| 1024| 50|
|Basic| 2| 2\.560| 100|
|Allgemeiner Zweck| 2| 3\.584| 300|
|Allgemeiner Zweck| 4| 7\.680| 625|
|Allgemeiner Zweck| 8| 15360| 1250|
|Allgemeiner Zweck| 16| 31\.232| 2500|
|Allgemeiner Zweck| 32| 62\.976| 5\.000|
|Allgemeiner Zweck| 64| 125952| 10000|
|Arbeitsspeicheroptimiert| 2| 7168| 600|
|Arbeitsspeicheroptimiert| 4| 15360| 1250|
|Arbeitsspeicheroptimiert| 8| 30720| 2500|
|Arbeitsspeicheroptimiert| 16| 62464| 5\.000|
|Arbeitsspeicheroptimiert| 32| 125952| 10000|

Diese zusätzlichen Serverparameter sind im System nicht konfigurierbar:

|**Parameter**|**Fester Wert**|
| :------------------------ | :-------- |
|innodb_file_per_table (im Tarif „Basic“)|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Weitere Serverparameter, die hier nicht aufgeführt sind, werden auf die vordefinierten Standardwerte für [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/) festgelegt.

## <a name="working-with-the-time-zone-parameter"></a>Arbeiten mit dem Zeitzonenparameter

### <a name="populating-the-time-zone-tables"></a>Auffüllen der Zeitzonentabellen

Die Zeitzonentabellen auf Ihrem Server können durch Aufrufen der gespeicherten Prozedur `az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden.

> [!NOTE]
> Wenn Sie den Befehl `az_load_timezone` in MySQL Workbench ausführen, müssen Sie möglicherweise zuerst den sicheren Aktualisierungsmodus mit `SET SQL_SAFE_UPDATES=0;` deaktivieren.

```sql
CALL mysql.az_load_timezone();
```

Um die verfügbaren Zeitzonenwerte anzuzeigen, führen Sie den folgenden Befehl aus:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Festlegen der globalen Zeitzone

Die globale Zeitzone kann auf der Seite **Serverparameter** im Azure-Portal festgelegt werden. Im folgenden Beispiel wird die globale Zeitzone auf „US/Pacific“ festgelegt.

![Festlegen des Zeitzonenparameters](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Festlegen der Sitzungszeitzone

Die Sitzungszeitzone kann durch Ausführen des Befehls `SET time_zone` in einem Tool wie der MySQL-Befehlszeile oder MySQL Workbench festgelegt werden. Im folgenden Beispiel wird die Zeitzone auf **US/Pacific** festgelegt.

```sql
SET time_zone = 'US/Pacific';
```

Informationen zu [Datums- und Uhrzeitfunktionen](https://mariadb.com/kb/en/library/convert_tz/) finden Sie in der MariaDB-Dokumentation.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
