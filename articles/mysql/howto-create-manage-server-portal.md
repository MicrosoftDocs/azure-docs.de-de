---
title: Verwalten eines Servers – Azure-Portal – Azure Database for MySQL
description: Erfahren Sie, wie Sie Azure Database for MySQL-Server aus dem Azure-Portal verwalten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: e29e823834ec813a8389cea220cffc7633aa7103
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541452"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Erstellen eines Servers für Azure Database for MySQL über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie Ihre Azure Database for MySQL-Server verwalten. Zu den Verwaltungsaufgaben gehören die Compute- und Speicherskalierung, das Zurücksetzen des Administratorkennworts und das Anzeigen von Serverdetails.

## <a name="sign-in"></a>Anmelden

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-server"></a>Erstellen eines Servers

Wechseln Sie zum [Schnellstart](quickstart-create-mysql-server-database-using-azure-portal.md), um zu erfahren, wie ein Azure Database for MySQL-Server erstellt wird, und wie Sie damit beginnen können.

## <a name="scale-compute-and-storage"></a>Skalieren von Compute und Speicher

Nach der Servererstellung können Sie zwischen den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ skalieren, wenn sich Ihre Anforderungen ändern. Sie können Compute und Speicher auch skalieren, indem Sie virtuelle Kerne vergrößern oder verkleinern. Der Speicher kann zentral hochskaliert werden (ein zentrales Herunterskalieren ist jedoch nicht möglich).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Skalieren zwischen den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“

Sie können von „Universell“ zu „Arbeitsspeicheroptimiert“ skalieren und umgekehrt. Ein Wechsel in den und aus dem Basic-Tarif nach der Servererstellung wird nicht unterstützt.

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Abschnitt **Einstellungen** die Option **Tarif** aus.

2. Wählen Sie je nach Skalierung **Universell** oder **Arbeitsspeicheroptimiert** aus.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Screenshot des Azure-Portals für die Auswahl der Dienstebenen „Basic“, „Universell“ oder „Arbeitsspeicheroptimiert“ in Azure Database for MySQL":::

   > [!NOTE]
   > Das Ändern der Tarife löst einen Neustart des Servers aus.

3. Wählen Sie **OK** aus, um die Änderungen zu speichern.

### <a name="scale-vcores-up-or-down"></a>Zentrales Hoch- oder Herunterskalieren von virtuellen Kernen

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Abschnitt **Einstellungen** die Option **Tarif** aus.

2. Ändern Sie die Einstellung **Virtuelle Kerne**, indem Sie den Schieberegler auf den gewünschten Wert ziehen.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Screenshot des Azure-Portals für die Auswahl der Option für die virtuellen Kerne in Azure Database for MySQL":::

    > [!NOTE]
    > Das Skalieren der virtuellen Kerne löst einen Neustart des Servers aus.

3. Wählen Sie **OK** aus, um die Änderungen zu speichern.

### <a name="scale-storage-up"></a>Zentrales Hochskalieren des Speichers

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Abschnitt **Einstellungen** die Option **Tarif** aus.

2. Ändern Sie die Einstellung **Speicher**, indem Sie den Schieberegler auf den gewünschten Wert ziehen.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Screenshot des Azure-Portal für die Auswahl der Speicherskalierung in Azure Database for MySQL":::

   > [!NOTE]
   > Der Speicher kann nicht herunterskaliert werden.

3. Wählen Sie **OK** aus, um die Änderungen zu speichern.

## <a name="update-admin-password"></a>Aktualisieren des Administratorkennworts

Sie können das Kennwort für die Administratorrolle über das Azure-Portal ändern.

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Fenster **Übersicht** die Option **Kennwort zurücksetzen** aus.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Screenshot des Azure-Portals für das Zurücksetzen des Kennworts in Azure Database for MySQL":::

2. Geben Sie ein neues Kennwort ein, und bestätigen Sie es. Im Textfeld werden Sie nach den Anforderungen an die Kennwortkomplexität gefragt.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Screenshot des Azure-Portals für das Zurücksetzen des Kennworts und das Speichern in Azure Database for MySQL":::

3. Wählen Sie **OK** aus, um das neue Kennwort zu speichern.

## <a name="delete-a-server"></a>Löschen eines Servers

Sie können Ihren Server löschen, wenn Sie ihn nicht mehr benötigen.

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Fenster **Übersicht** die Option **Löschen** aus.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Screenshot des Azure-Portals für das Löschen des Servers in Azure Database for MySQL":::

2. Geben Sie den Namen des Servers in das Eingabefeld ein, um zu bestätigen, dass dies der Server ist, den Sie löschen möchten.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Screenshot des Azure-Portals für das Bestätigen der Löschung des Servers in Azure Database for MySQL":::

   > [!NOTE]
   > Das Löschen eines Servers kann nicht mehr rückgängig gemacht werden.

3. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Sichern und Wiederherstellen eines Servers](howto-restore-server-portal.md).
- Erfahren Sie mehr über die [Überwachungs- und Optimierungsoptionen in Azure Database for MySQL](concepts-monitoring.md).
