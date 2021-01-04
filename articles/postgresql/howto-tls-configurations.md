---
title: TLS-Konfiguration – Azure-Portal – Azure Database for PostgreSQL (Einzelserver)
description: Erfahren Sie, wie Sie im Azure-Portal die TLS-Konfiguration für Ihren Azure Database for PostgreSQL-Einzelserver festlegen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 25be6b3c4e3172fc8ee14b97fd890b5948c284ba
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242363"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Konfigurieren von TLS-Einstellungen in Azure Database for PostgreSQL (Einzelserver) im Azure-Portal

In diesem Artikel wird beschrieben, wie Sie einen Azure Database for PostgreSQL-Server so konfigurieren können, dass die TLS-Mindestversion erzwungen wird, bei der zulässige Verbindungen passieren können und alle Verbindungen mit einer niedrigeren TLS-Version als der konfigurierten TLS-Mindestversion abgelehnt werden, wodurch die Netzwerksicherheit erhöht wird.

Sie können die TLS-Version für die Verbindung mit ihrem Azure Database for PostgreSQL erzwingen. Kunden haben jetzt die Möglichkeit, die TLS-Mindestversion für ihren Datenbankserver festzulegen. Wenn Sie diese Mindesteinstellung für die TLS-Version beispielsweise auf TLS 1.0 festlegen, bedeutet das, dass Ihr Server Verbindungen von Clients mit TLS 1.0, 1.1 und 1.2+ zulässt. Die Festlegung der TLS-Mindestversion auf 1.2+ bedeutet dagegen, dass Sie nur Verbindungen von Clients mithilfe von TLS 1.2 zulassen und alle Verbindungen mit TLS 1.0 und TLS 1.1 abgelehnt werden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

* Eine Instanz von [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Festlegen von TLS-Konfigurationen für Azure Database for PostgreSQL (Einzelserver)

Befolgen Sie diese Schritte, um die TLS-Mindestversion für PostgreSQL festzulegen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen Azure Database for PostgreSQL-Server aus.

1.  Klicken Sie auf der Seite „Azure Database for PostgreSQL (Einzelserver)“ unter **Einstellungen** auf **Verbindungssicherheit** , um die Seite zur Konfiguration der Verbindungssicherheit zu öffnen.

1. Wählen Sie in **TLS-Mindestversion** die Option **1.2** aus, um Verbindungen mit TLS-Versionen vor TLS 1.2 für Ihren PostgreSQL-Einzelserver abzulehnen.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="TLS-Konfigurationen für Azure Database for PostgreSQL (Einzelserver)":::

1. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

1. Eine Benachrichtigung bestätigt, dass die Verbindungssicherheitseinstellung erfolgreich aktiviert wurde.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Erfolgreiche TLS-Konfigurationen für Azure Database for PostgreSQL (Einzelserver)":::

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Warnungen zu Metriken erstellen](howto-alert-on-metric.md).