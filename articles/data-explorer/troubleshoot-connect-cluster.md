---
title: 'Problembehandlung: Fehler beim Herstellen einer Verbindung mit einem Cluster in Azure Data Explorer'
description: Dieser Artikel beschreibt die Schritte zur Problembehandlung beim Herstellen einer Verbindung mit einem Cluster in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827035"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Problembehandlung: Fehler beim Herstellen einer Verbindung mit einem Cluster im Azure-Daten-Explorer

Wenn Sie im Azure-Daten-Explorer keine Verbindung mit einem Cluster herstellen können, führen Sie die folgenden Schritte aus.

1. Stellen Sie sicher, dass die Verbindungszeichenfolge richtig ist. Sie sollte in folgendem Format vorliegen: `https://<ClusterName>.<Region>.kusto.windows.net`. Beispiel: `https://docscluster.westus.kusto.windows.net`.

1. Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Andernfalls erhalten Sie die Antwort, dass Sie *nicht autorisiert* sind.

    Informationen zu Berechtigungen finden Sie unter [Verwalten von Datenbankberechtigungen](manage-database-permissions.md). Arbeiten Sie ggf. mit Ihrem Clusteradministrator zusammen, damit dieser Sie zur entsprechenden Rolle hinzufügen kann.

1. Stellen Sie sicher, dass der Cluster nicht gelöscht wurde: Sehen Sie sich das Aktivitätsprotokoll in Ihrem Abonnement an.

1. Sehen Sie sich das [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) an. Suchen Sie nach dem Status des Azure-Daten-Explorers in der Region, in der Sie versuchen, eine Verbindung mit einem Cluster herzustellen.

    Wenn der Status nicht **Gut** lautet (grünes Häkchen), versuchen Sie die Verbindung mit dem Cluster herzustellen, wenn sich der Status verbessert hat.

1. Wenn Sie weitere Unterstützung bei der Lösung Ihres Problems benötigen, öffnen Sie eine Supportanfrage im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).