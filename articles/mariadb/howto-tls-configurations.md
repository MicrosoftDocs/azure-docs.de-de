---
title: TLS-Konfiguration – Azure-Portal – Azure Database for MariaDB
description: Hier erfahren Sie, wie Sie die TLS-Konfiguration mit dem Azure-Portal für Ihr Azure Database for MariaDB festlegen.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241983"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Konfigurieren von TLS-Einstellungen in Azure Database for MariaDB mit dem Azure-Portal

In diesem Artikel wird beschrieben, wie Sie einen Azure Database for MariaDB-Server so konfigurieren können, dass die TLS-Mindestversion bei passierenden Verbindungen erzwungen wird und alle Verbindungen mit einer niedrigeren TLS-Version als der konfigurierten TLS-Mindestversion abgelehnt werden, wodurch die Netzwerksicherheit erhöht wird.

Sie können die TLS-Version zum Herstellen einer Verbindung mit Azure Database for MariaDB erzwingen, indem Sie die TLS-Mindestversion für den Datenbankserver festlegen. Wenn Sie diese Mindesteinstellung für die TLS-Version beispielsweise auf TLS 1.0 festlegen, bedeutet das, dass Ihr Server Verbindungen von Clients mit TLS 1.0, 1.1 und 1.2+ zulässt. Alternativ bedeutet die Festlegung auf 1.2, dass Sie nur Verbindungen von Clients mithilfe von TLS 1.2+ zulassen und alle Verbindungen mit TLS 1.0 und TLS 1.1 abgewiesen werden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

* Eine [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)-Instanz

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Festlegen von TLS-Konfigurationen für Azure Database for MariaDB

Befolgen Sie diese Schritte, um die TLS-Mindestversion des MariaDB-Servers festzulegen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen Azure Database for MariaDB-Server aus.

1. Klicken Sie auf der Seite des MariaDB-Servers unter **Einstellungen** auf **Verbindungssicherheit** , um die Seite zur Konfiguration der Verbindungssicherheit zu öffnen.

1. Wählen Sie in **TLS-Mindestversion** die Option **1.2** aus, um Verbindungen mit TLS-Versionen vor TLS 1.2 für Ihren MariaDB-Server abzulehnen.

    ![TLS-Konfiguration für Azure Database for MariaDB](./media/howto-tls-configurations/tls-configurations.png)

1. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

1. Eine Benachrichtigung bestätigt, dass die Verbindungssicherheitseinstellung erfolgreich aktiviert wurde.

    ![Erfolgreich abgeschlossene TLS-Konfiguration für Azure Database for MariaDB](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Warnungen zu Metriken erstellen](howto-alert-metric.md).