---
title: Konfigurieren von Serverparametern – Azure-Portal – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie MySQL-Serverparameter in Azure Database for MySQL mithilfe des Azure-Portals konfigurieren können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 363be8b34f230b812bc24276e1f3925faf0cdc1c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540840"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Konfigurieren von Serverparametern in Azure Database for MySQL mit dem Azure-Portal

Azure Database für MySQL unterstützt das Konfigurieren einiger Serverparameter. In diesem Artikel wird beschrieben, wie diese Parameter mithilfe des Azure-Portals konfiguriert werden. Nicht alle Serverparameter können angepasst werden.

>[!Note]
> Serverparameter können global auf Serverebene aktualisiert werden. Verwenden Sie dazu die [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) oder das [Azure-Portal](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Konfigurieren von Serverparametern

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie dann nach Ihrem Azure Database for MySQL-Server.
2. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter**, um die Seite „Serverparameter“ für den Server mit Azure Database for MySQL zu öffnen.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Seite „Serverparameter“ im Azure-Portal":::
3. Suchen Sie die Einstellungen, die Sie anpassen möchten. Überprüfen Sie die Spalte **Beschreibung**, um den Zweck und die zulässigen Werte zu verstehen.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Dropdownliste für Enumerierung":::
4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Änderungen speichern oder verwerfen":::
5. Wenn Sie neue Werte für die Parameter gespeichert haben, können Sie jederzeit alles zurück auf die Standardwerte setzen, indem Sie die Option **Alle auf Standard zurücksetzen** wählen.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Alle auf Standard zurücksetzen":::

## <a name="setting-parameters-not-listed"></a>Nicht aufgeführte Einstellungsparameter

Wenn der Serverparameter, den Sie aktualisieren möchten, nicht im Azure-Portal aufgeführt ist, können Sie den Parameter optional mithilfe von `init_connect` auf Verbindungsebene festlegen. Damit werden die Serverparameter für jeden Client, der mit dem Server verbinden wird, festgelegt. 

1. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter**, um die Seite „Serverparameter“ für den Server mit Azure Database for MySQL zu öffnen.
2. Suchen Sie nach `init_connect`.
3. Fügen Sie die Serverparameter im folgenden Format hinzu: `SET parameter_name=YOUR_DESIRED_VALUE` als Wert der Wertspalte.

    Sie können z. B. den Zeichensatz Ihres Servers ändern, indem Sie `init_connect` auf `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;` festlegen.
4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

>[!Note]
> `init_connect` kann zum Ändern von Parametern verwendet werden, für die auf Sitzungsebene keine SUPER-Berechtigungen erforderlich sind. Führen Sie zum Überprüfen, ob Sie den Parameter mit `init_connect` festlegen können, den Befehl `set session parameter_name=YOUR_DESIRED_VALUE;` aus. Falls ein Fehler der Art **Zugriff verweigert; Sie benötigen SUPER-Berechtigungen** angezeigt wird, ist das Festlegen des Parameters mit „init_connect“ nicht möglich.

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

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Festlegen des Zeitzonenparameters":::

### <a name="setting-the-session-level-time-zone"></a>Festlegen der Sitzungszeitzone

Die Sitzungszeitzone kann durch Ausführen des Befehls `SET time_zone` in einem Tool wie der MySQL-Befehlszeile oder MySQL Workbench festgelegt werden. Im folgenden Beispiel wird die Zeitzone auf **US/Pacific** festgelegt.

```sql
SET time_zone = 'US/Pacific';
```

Informationen zu [Datums- und Uhrzeitfunktionen](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) finden Sie in der MySQL-Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

- [Datenverbindungsbibliotheken für Azure Database for MySQL](concepts-connection-libraries.md)
