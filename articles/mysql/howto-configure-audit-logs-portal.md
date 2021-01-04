---
title: Zugreifen auf Überwachungsprotokolle – Azure-Portal – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie die Überwachungsprotokolle in Azure Database for MySQL im Azure-Portal konfigurieren und darauf zugreifen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: a73a3c77310c0e19792758c0586975e14cfaebf8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541656"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurieren von und Zugreifen auf Überwachungsprotokolle für Azure Database for MySQL im Azure-Portal

Sie können im Azure-Portal die [Azure Database for MySQL-Überwachungsprotokolle](concepts-audit-logs.md) und -Diagnoseeinstellungen konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:

- [Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurieren der Überwachungsprotokollierung

>[!IMPORTANT]
> Es wird empfohlen, nur die Ereignistypen und Benutzer zu protokollieren, die für Ihre Überwachungszwecke erforderlich sind, um sicherzustellen, dass die Leistung Ihres Servers nicht stark beeinträchtigt wird.

Aktivieren und konfigurieren Sie die Überwachungsprotokollierung.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie Ihren Server für Azure Database for MySQL aus.

1. Wählen Sie auf der Randleiste im Abschnitt **Einstellungen** den Eintrag **Serverparameter** aus.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Serverparameter":::

1. Ändern Sie den Parameter **audit_log_enabled** in ON.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Aktivieren von Überwachungsprotokollen":::

1. Wählen Sie die zu protokollierenden [Ereignistypen](concepts-audit-logs.md#configure-audit-logging) aus, indem Sie den Parameter **audit_log_events** aktualisieren.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Überwachungsprotokollereignisse":::

1. Fügen Sie durch Aktualisieren der Parameter **audit_log_exclude_users** und **audit_log_include_users** alle MySQL-Benutzer hinzu, die in die Protokollierung einbezogen bzw. von ihr ausgeschlossen werden sollen. Geben Sie Benutzer an, indem Sie ihre MySQL-Benutzernamen bereitstellen.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Vom Überwachungsprotokoll auszuschließende Benutzer":::

1. Nachdem Sie die Parameter geändert haben, klicken Sie auf **Speichern**. Sie können Ihre Änderungen auch **verwerfen**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Speichern":::

## <a name="set-up-diagnostic-logs"></a>Einrichten von Diagnoseprotokollen

1. Wählen Sie auf der Randleiste im Abschnitt **Überwachung** die Option **Diagnoseeinstellungen** aus.

1. Klicken Sie auf „+ Diagnoseeinstellung hinzufügen“ :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Diagnoseeinstellung hinzufügen":::

1. Geben Sie einen Namen für die Diagnoseeinstellung ein.

1. Geben Sie an, an welche Datensenken die Überwachungsprotokolle gesendet werden sollen (Speicherkonto, Event Hub und/oder Log Analytics-Arbeitsbereich).

1. Wählen Sie als Protokolltyp „MySqlAuditLogs“ aus.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Konfigurieren der Diagnoseeinstellung":::

1. Nachdem Sie die Datensenken für die Überwachungsprotokolle konfiguriert haben, können Sie auf **Speichern** klicken.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Speichern der Diagnoseeinstellung":::

1. Greifen Sie auf die Überwachungsprotokolle zu, indem Sie sie in den von Ihnen konfigurierten Datensenken einsehen. Es kann bis zu 10 Minuten dauern, bis die Protokolle angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zu [Überwachungsprotokollen](concepts-audit-logs.md) in Azure Database for MySQL.
- Erfahren Sie mehr zum Konfigurieren von Überwachungsprotokollen über die [Azure-Befehlszeilenschnittstelle](howto-configure-audit-logs-cli.md).