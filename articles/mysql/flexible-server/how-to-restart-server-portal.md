---
title: Neustarten eines Servers – Azure-Portal – Azure Database for MySQL – Flexibler Server
description: In diesem Artikel wird beschrieben, wie Sie einen flexiblen Azure Database for MySQL-Server über das Azure-Portal neu starten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 88a1524875f168b49f50f1684c650d5bc178bf38
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542659"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Neustarten eines flexiblen Azure Database for MySQL-Servers mit dem Azure-Portal
In diesem Thema wird erläutert, wie Sie einen flexiblen Azure Database for MySQL-Server neu starten. Es kann vorkommen, dass Sie Ihren Server zu Wartungszwecken neu starten müssen. In diesem Fall kommt es zu einem kurzen Ausfall, weil der Vorgang vom Server ausgeführt wird.

Der Neustart des Servers wird blockiert, wenn der Dienst ausgelastet ist. Beispielsweise kann der Dienst einen zuvor angeforderten Vorgang (z. B. das Skalieren von virtuellen Kernen) verarbeiten.

Die für einen Neustart benötigte Zeit hängt vom MySQL-Wiederherstellungsprozess ab. Um die Neustartzeit zu verkürzen, empfehlen wir, die Aktivitäten auf dem Server vor dem Neustart auf ein Minimum zu beschränken.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:
- Einen [flexiblen Azure Database for MySQL-Server](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Ausführen des Serverneustarts

Mit den folgenden Schritten wird der MySQL-Server neu gestartet:

1. Wählen Sie im Azure-Portal Ihren flexiblen Azure Database for MySQL-Server aus.

2. Klicken Sie auf der Seite **Übersicht** des Servers auf der Symbolleiste auf **Neu starten**.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL – Übersicht – Schaltfläche „Neu starten“":::

3. Klicken Sie auf **Ja**, um den Neustart des Servers zu bestätigen.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL – Neustart bestätigen":::

4. Beachten Sie, dass sich der Serverstatus in „Wird neu gestartet“ ändert.

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL – Status „Wird neu gestartet“":::

5. Vergewissern Sie sich, dass der Neustart des Servers erfolgreich war.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL – Erfolgreicher Neustart":::

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erstellen eines flexiblen Azure Database for MySQL-Servers mit dem Azure-Portal](quickstart-create-server-portal.md)
