---
title: Überprüfen der Integrität eines Azure-Daten-Explorer-Clusters
description: In diesem Artikel werden die Schritte zum Überwachen der Integrität Ihres Azure Data Explorer-Clusters beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861317"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Überprüfen der Integrität eines Azure-Daten-Explorer-Clusters

Die Integrität eines Azure-Daten-Explorer-Clusters wird von verschiedenen Faktoren beeinflusst, einschließlich der CPU, dem Arbeitsspeicher und dem Datenträgersubsystem. In diesem Artikel werden einige grundlegende Schritte aufgeführt, die Sie ausführen können, um die Integrität eines Clusters zu bestimmen.

1. Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an.

1. Klicken Sie im linken Bereich auf Ihren Cluster, und führen Sie den folgenden Befehl aus.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Wenn 1 ausgegeben wird, ist der Cluster fehlerfrei. Die Ausgabe 0 deutet auf Fehler hin.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Cluster.

1. Klicken Sie unter **Überwachung** erst auf **Metriken** und anschließend wie im folgenden Bild gezeigt auf **Keep Alive**. Wenn die Ausgabe ungefähr 1 beträgt, ist der Cluster fehlerfrei.

    ![Keep-Alive-Metrik für den Cluster](media/check-cluster-health/portal-metrics.png)

1. Es können weitere Metriken zum Diagramm hinzugefügt werden. Wählen Sie das Diagramm aus, und klicken Sie auf **Metrik hinzufügen**. Auswählen einer weiteren Metrik – dieses Beispiel zeigt **CPU**.

    ![Hinzufügen von Metriken](media/check-cluster-health/add-metric.png)

1. Wenn Sie Hilfe bei der Diagnose von Problemen mit der Integrität eines Clusters benötigen, erstellen Sie eine Supportanfrage im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).