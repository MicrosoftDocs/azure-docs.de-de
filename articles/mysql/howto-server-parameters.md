---
title: Konfigurieren von Serverparametern in Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie MySQL-Serverparameter in Azure Database for MySQL mithilfe des Azure-Portals konfigurieren können.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 103e09a0e2b9dd409fa2ddaff1c5311ef9936d22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61422132"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Konfigurieren von Serverparametern in Azure Database for MySQL mit dem Azure-Portal

Azure Database für MySQL unterstützt das Konfigurieren einiger Serverparameter. In diesem Artikel wird beschrieben, wie diese Parameter mithilfe des Azure-Portals konfiguriert werden. Nicht alle Serverparameter können angepasst werden.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigieren Sie im Azure-Portal zu „Serverparameter“.

1. Melden Sie sich beim Azure-Portal an, und suchen Sie dann nach Ihrem Azure Database for MySQL-Server.
2. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter**, um die Seite „Serverparameter“ für den Server mit Azure Database for MySQL zu öffnen.
![Seite „Serverparameter“ im Azure-Portal](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Suchen Sie die Einstellungen, die Sie anpassen möchten. Überprüfen Sie die Spalte **Beschreibung**, um den Zweck und die zulässigen Werte zu verstehen.
![Dropdownliste für Enumerierung](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
![Änderungen speichern oder verwerfen](./media/howto-server-parameters/4-save_parameters.png)
5. Wenn Sie neue Werte für die Parameter gespeichert haben, können Sie jederzeit alles zurück auf die Standardwerte setzen, indem Sie die Option **Alle auf Standard zurücksetzen** wählen.
![Alle auf Standard zurücksetzen](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Liste der konfigurierbaren Serverparameter

Die Liste der unterstützten Serverparameter wächst ständig. Verwenden Sie die Registerkarte mit den Serverparametern im Azure-Portal, um die Definition abzurufen und Serverparameter anhand Ihrer Anwendungsanforderungen zu konfigurieren.

## <a name="non-configurable-server-parameters"></a>Nicht konfigurierbare Serverparameter

„InnoDB-Pufferpool“ und „Max. Anzahl von Verbindungen“ können nicht konfiguriert werden und sind an Ihren [Tarif](concepts-service-tiers.md) gebunden.

|**Tarif**| **Computegeneration**|**vCore(s)**|**InnoDB-Pufferpool (MB)**| **Max. Anzahl von Verbindungen**|
|---|---|---|---|--|
|Basic| Gen 4| 1| 960| 50|
|Basic| Gen 4| 2| 2\.560| 100|
|Basic| Gen 5| 1| 960| 50|
|Basic| Gen 5| 2| 2\.560| 100|
|Allgemeiner Zweck| Gen 4| 2| 3\.584| 300|
|Allgemeiner Zweck| Gen 4| 4| 7\.680| 625|
|Allgemeiner Zweck| Gen 4| 8| 15360| 1250|
|Allgemeiner Zweck| Gen 4| 16| 31\.232| 2500|
|Allgemeiner Zweck| Gen 4| 32| 62\.976| 5\.000|
|Allgemeiner Zweck| Gen 5| 2| 3\.584| 300|
|Allgemeiner Zweck| Gen 5| 4| 7\.680| 625|
|Allgemeiner Zweck| Gen 5| 8| 15360| 1250|
|Allgemeiner Zweck| Gen 5| 16| 31\.232| 2500|
|Allgemeiner Zweck| Gen 5| 32| 62\.976| 5\.000|
|Allgemeiner Zweck| Gen 5| 64| 125952| 10000|
|Arbeitsspeicheroptimiert| Gen 5| 2| 7168| 600|
|Arbeitsspeicheroptimiert| Gen 5| 4| 15360| 1250|
|Arbeitsspeicheroptimiert| Gen 5| 8| 30720| 2500|
|Arbeitsspeicheroptimiert| Gen 5| 16| 62464| 5\.000|
|Arbeitsspeicheroptimiert| Gen 5| 32| 125952| 10000|

Diese zusätzlichen Serverparameter sind im System nicht konfigurierbar:

|**Parameter**|**Fester Wert**|
| :------------------------ | :-------- |
|innodb_file_per_table (im Tarif „Basic“)|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Weitere Serverparameter, die hier nicht aufgeführt sind, werden für die Versionen [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) und [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) auf ihre vordefinierten MySQL-Standardwerte festgelegt.

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

Informationen zu [Datums- und Uhrzeitfunktionen](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) finden Sie in der MySQL-Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

- [Datenverbindungsbibliotheken für Azure Database for MySQL](concepts-connection-libraries.md)
