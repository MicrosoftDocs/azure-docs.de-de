---
title: Konfigurieren von Serverparametern in Azure Database for MariaDB über das Azure-Portal
description: In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal MariaDB-Serverparameter in Azure Database for MariaDB konfigurieren.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 7081535bb709e6731a9a15436334e8742e7bdd08
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539455"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>Konfigurieren von Serverparametern in Azure Database for MariaDB mit dem Azure-Portal

Azure Database for MariaDB unterstützt das Konfigurieren einiger Serverparameter. In diesem Artikel wird beschrieben, wie diese Parameter mithilfe des Azure-Portals konfiguriert werden. Nicht alle Serverparameter können angepasst werden.

>[!Note]
> Serverparameter können global auf Serverebene aktualisiert werden. Verwenden Sie dazu die [Azure CLI](./howto-configure-server-parameters-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) oder das [Azure-Portal](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Konfigurieren von Serverparametern

1. Melden Sie sich beim Azure-Portal an, und suchen Sie dann nach Ihrem Azure Database for MariaDB-Server.
2. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter**, um die Seite „Serverparameter“ für den Server mit Azure Database for MariaDB zu öffnen.
![Seite „Serverparameter“ im Azure-Portal](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Suchen Sie die Einstellungen, die Sie anpassen möchten. Überprüfen Sie die Spalte **Beschreibung**, um den Zweck und die zulässigen Werte zu verstehen.
![Dropdownliste für Enumerierung](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
![Änderungen speichern oder verwerfen](./media/howto-server-parameters/4-save_parameters.png)
5. Wenn Sie neue Werte für die Parameter gespeichert haben, können Sie jederzeit alles zurück auf die Standardwerte setzen, indem Sie die Option **Alle auf Standard zurücksetzen** wählen.
![Alle auf Standard zurücksetzen](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Nicht aufgeführte Einstellungsparameter

Wenn der Serverparameter, den Sie aktualisieren möchten, nicht im Azure-Portal aufgeführt ist, können Sie den Parameter optional mithilfe von `init_connect` auf Verbindungsebene festlegen. Damit werden die Serverparameter für jeden Client, der mit dem Server verbinden wird, festgelegt. 

1. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter**, um die Seite „Serverparameter“ für den Server mit Azure Database for MariaDB zu öffnen.
2. Suchen Sie nach `init_connect`.
3. Fügen Sie die Serverparameter im folgenden Format hinzu: `SET parameter_name=YOUR_DESIRED_VALUE` als Wert der Wertspalte.

    Sie können z. B. den Zeichensatz Ihres Servers ändern, indem Sie `init_connect` auf `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;` festlegen.
4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

## <a name="working-with-the-time-zone-parameter"></a>Arbeiten mit dem Zeitzonenparameter

### <a name="populating-the-time-zone-tables"></a>Auffüllen der Zeitzonentabellen

Die Zeitzonentabellen auf Ihrem Server können durch Aufrufen der gespeicherten Prozedur `mysql.az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden.

> [!NOTE]
> Wenn Sie den Befehl `mysql.az_load_timezone` in MySQL Workbench ausführen, müssen Sie möglicherweise zuerst den sicheren Aktualisierungsmodus mit `SET SQL_SAFE_UPDATES=0;` deaktivieren.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Sie sollten den Server neu starten, um sicherzustellen, dass die Zeitzonentabellen ordnungsgemäß aufgefüllt werden. Um den Server neu zu starten, verwenden Sie das [Azure-Portal](howto-restart-server-portal.md) oder die [Befehlszeilenschnittstelle](howto-restart-server-cli.md).
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

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Serverparametern](concepts-server-parameters.md)
