---
title: Automatische Speichervergrößerung über das Azure-Portal in Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel ist beschrieben, wie Sie das automatische Vergrößern von Speicher über das Azure-Portal in Azure Database for PostgreSQL für einen Einzelserver aktivieren.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576648"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatische Speichervergrößerung über das Azure-Portal in Azure Database for PostgreSQL – Einzelserver
In diesem Artikel wird beschrieben, wie Sie die Speichervergrößerung für einen Azure Database for PostgreSQL-Server konfigurieren können, ohne die Workload zu beeinträchtigen.

Wenn ein Server das zugeordnete Speicherlimit erreicht, wird er als schreibgeschützt gekennzeichnet. Wenn Sie jedoch die automatische Speichervergrößerung aktivieren, wird der Speicher in Anpassung an die zunehmende Datenmenge erweitert. Bei Servern mit weniger als 100 GB bereitgestelltem Speicher wird die bereitgestellte Speichergröße um 5 GB erhöht, sobald der freie Speicher unter 1 GB oder zehn Prozent des bereitgestellten Speichers sinkt. Bei Servern mit mehr als 100 GB bereitgestelltem Speicher wird die bereitgestellte Speichergröße um fünf Prozent erhöht, sobald der freie Speicherplatz unter fünf Prozent der bereitgestellten Speichergröße sinkt. Dabei gelten die maximalen, [hier](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) beschriebenen Speicherlimits.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:
- Einen [Azure-Datenbank für PostgreSQL-Server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivieren der automatischen Speichervergrößerung 

Gehen Sie wie folgt vor, um die automatische Speichervergrößerung für PostgreSQL-Server zu aktivieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren Azure Database for PostgreSQL-Server aus.

2. Klicken Sie auf der Seite des PostgreSQL-Servers unter **Einstellungen** auf **Tarif**, um die Seite mit Tarifen zu öffnen.

3. Wählen Sie im Abschnitt **Automatische Vergrößerung** die Option **Ja** aus, um die automatische Speichervergrößerung zu aktivieren.

    ![Azure Database for PostgreSQL – Settings_Pricing_tier – Automatische Vergrößerung](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klicken Sie zum Speichern der Änderungen auf **OK**.

5. In einer Benachrichtigung wird bestätigt, dass die automatische Vergrößerung erfolgreich aktiviert wurde.

    ![Azure Database for PostgreSQL – erfolgreiche automatische Vergrößerung](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Warnungen zu Metriken erstellen](howto-alert-on-metric.md).
