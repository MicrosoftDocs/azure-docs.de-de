---
title: 'Konfigurieren von Serverparametern – Azure-Portal – Azure Database for MySQL: Flexible Server'
description: In diesem Artikel wird beschrieben, wie Sie MySQL-Serverparameter auf flexiblen Servern für Azure Database for MySQL mithilfe des Azure-Portals konfigurieren können.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 7733a6211363b4f1c9e9006f757b4d152c7af7f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489555"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurieren von Serverparametern auf flexiblen Azure Database for MySQL-Servern über das Azure-Portal

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Sie können die Konfiguration flexibler Azure Database for MySQL-Server über Serverparameter verwalten. Die Serverparameter werden beim Erstellen des Servers mit einem Standardwert und einem empfohlenen Wert konfiguriert.  

In diesem Artikel wird beschrieben, wie diese Serverparameter mithilfe des Azure-Portals angezeigt und konfiguriert werden. Auf dem Blatt „Serverparameter“ im Azure-Portal werden sowohl änderbare als auch nicht änderbare Serverparameter angezeigt. Die nicht änderbaren Serverparameter sind ausgegraut.

>[!Note]
> Serverparameter können global auf Serverebene mithilfe der [Azure-Befehlszeilenschnittstelle](./how-to-configure-server-parameters-cli.md) oder das [Azure-Portal](./how-to-configure-server-parameters-portal.md) aktualisiert werden.

## <a name="configure-server-parameters"></a>Konfigurieren von Serverparametern

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie dann nach Ihrem flexiblen Azure Database for MySQL-Server.
2. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter** , um die Seite „Serverparameter“ für den flexiblen Azure Database for MySQL-Server zu öffnen.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Seite „Serverparameter“ im Azure-Portal":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Suchen Sie nach einem Serverparameter, den Sie anpassen möchten. Überprüfen Sie die Spalte **Beschreibung** , um den Zweck und die zulässigen Werte zu verstehen.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Dropdownliste für Enumerierung":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Änderungen speichern oder verwerfen":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. Bei statischen Parametern muss der Server neu gestartet werden, damit sie angewandt werden. Wenn Sie einen statischen Parameter ändern, erhalten Sie die Aufforderung **Jetzt neu starten** oder **Später neu starten**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Neustart beim Speichern statischer Parameter":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Wenn Sie neue Werte für die Parameter gespeichert haben, können Sie jederzeit alles zurück auf die Standardwerte setzen, indem Sie die Option **Alle auf Standard zurücksetzen** wählen.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Alle auf Standard zurücksetzen":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Festlegen nicht änderbarer Serverparameter

Wenn der Serverparameter, den Sie aktualisieren möchten, nicht geändert werden kann, können Sie den Parameter optional mithilfe von `init_connect` auf Verbindungsebene festlegen. Damit werden die Serverparameter für jeden Client, der mit dem Server verbinden wird, festgelegt. 

1. Klicken Sie im Abschnitt **EINSTELLUNGEN** auf **Serverparameter** , um die Seite „Serverparameter“ für den Server mit Azure Database for MySQL zu öffnen.
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
> Sie sollten den Server neu starten, um sicherzustellen, dass die Zeitzonentabellen ordnungsgemäß aufgefüllt werden.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Um die verfügbaren Zeitzonenwerte anzuzeigen, führen Sie den folgenden Befehl aus:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Festlegen der globalen Zeitzone

Die globale Zeitzone kann auf der Seite **Serverparameter** im Azure-Portal festgelegt werden. Im folgenden Beispiel wird die globale Zeitzone auf „US/Pacific“ festgelegt.

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Festlegen des Zeitzonenparameters":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Festlegen der Sitzungszeitzone

Die Sitzungszeitzone kann durch Ausführen des Befehls `SET time_zone` in einem Tool wie der MySQL-Befehlszeile oder MySQL Workbench festgelegt werden. Im folgenden Beispiel wird die Zeitzone auf **US/Pacific** festgelegt.

```sql
SET time_zone = 'US/Pacific';
```

Informationen zu [Datums- und Uhrzeitfunktionen](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) finden Sie in der MySQL-Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

- Konfigurieren von [Serverparametern über die Azure-Befehlszeilenschnittstelle](./how-to-configure-server-parameters-cli.md)
