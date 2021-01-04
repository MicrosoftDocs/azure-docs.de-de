---
title: TLS-Konfiguration – Azure-Portal – Azure Database for MySQL
description: Erfahren Sie, wie Sie die TLS-Konfiguration mit dem Azure-Portal für Ihr Azure Database for MySQL einstellen
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 290752c0e577e6c2cd58d83f77fea8a5406388e4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240629"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Konfigurieren von TLS-Einstellungen in Azure Database for MySQL mit dem Azure-Portal

In diesem Artikel wird beschrieben, wie Sie einen Azure Database for MySQL-Server so konfigurieren können, dass die TLS-Mindestversion erzwungen wird, bei der zulässige Verbindungen passieren können und alle Verbindungen mit einer niedrigeren TLS-Version als der konfigurierten TLS-Mindestversion abgelehnt werden, wodurch die Netzwerksicherheit erhöht wird.

Sie können die TLS-Version für die Verbindung mit ihrem Azure Database for MySQL erzwingen. Kunden haben jetzt die Möglichkeit, die TLS-Mindestversion für ihren Datenbankserver festzulegen. Wenn Sie z. B. diese TLS-Mindestversion auf 1.0 festlegen, bedeutet dies, dass Sie Clients, die sich über TLS 1.0, 1.1 und 1.2 verbinden, zulassen müssen. Alternativ bedeutet die Festlegung auf 1.2, dass Sie nur Verbindungen von Clients mithilfe von TLS 1.2+ zulassen und alle eingehenden Verbindungen mit TLS 1.0 und TLS 1.1 abgewiesen werden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

* Eine [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)-Instanz

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Festlegen von TLS-Konfigurationen für Azure Database for MySQL

Befolgen Sie diese Schritte, um die TLS-Mindestversion des MySQL-Servers festzulegen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen Azure Database for MySQL-Server aus.

1. Klicken Sie auf der Seite des MySQL-Servers unter **Einstellungen** auf **Verbindungssicherheit** , um die Seite zur Konfiguration der Verbindungssicherheit zu öffnen.

1. Wählen Sie in **TLS-Mindestversion** die Option **1.2** aus, um Verbindungen mit TLS-Versionen unter TLS 1.2 für Ihren MySQL-Server zurückzuweisen.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="TLS-Konfiguration für Azure Database for MySQL":::

1. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

1. Eine Benachrichtigung bestätigt, dass die Verbindungssicherheitseinstellung erfolgreich aktiviert wurde.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="TLS-Konfigurationserfolg für Azure Database for MySQL":::

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Warnungen zu Metriken erstellen](howto-alert-on-metric.md).