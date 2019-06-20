---
title: Zentrales Hochskalieren von Azure Data Explorer-Clustern zur Anpassung an sich änderndem Bedarf
description: Dieser Artikel beschreibt Schritte zum zentralen Hoch- und Herunterskalieren eines Azure Data Explorer-Clusters basierend auf sich änderndem Bedarf.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60758694"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Verwalten der Clusterskalierung bei sich änderndem Bedarf

Es gibt zwei Workflows für die Skalierung Azure Data Explorer-Clusters: zentrale Skalierung und [horizontale Skalierung](manage-cluster-scale-out.md). In diesem Artikel wird veranschaulicht, wie Sie einen Cluster zentral hochskalieren.

Die richtige Größe eines Clusters ist entscheidend für die Leistung von Azure-Daten-Explorer. Aber der Bedarf in einem Cluster kann nicht mit absoluter Genauigkeit vorhergesagt werden. Eine statische Clustergröße kann zu einer Unter- oder Überauslastung führen, was beides nicht ideal ist. Ein besserer Ansatz ist die *Skalierung* eines Clusters, wobei je nach Bedarf Kapazität und CPU-Ressourcen hinzugefügt und entfernt werden. 

## <a name="steps-to-scale-up"></a>Schritte zum zentralen Hochskalieren
1. Zum Custer wechseln Wählen Sie unter **Einstellungen** die Option **Zentral hochskalieren** aus.

    Ihnen wird eine Liste der verfügbaren SKUs angezeigt. In der folgenden Abbildung sind z. B. nur vier SKUs verfügbar.

    ![Zentrales Hochskalieren](media/manage-cluster-scale-up/scale-up.png)

    SKUs sind deaktiviert, weil sie entweder die aktuelle SKU sind, oder weil sie in der Region nicht verfügbar sind, in der sich der Cluster befindet.

1. Zum Ändern Ihrer SKU wählen Sie die gewünschte SKU aus, und wählen Sie **Auswählen** aus.

> [!NOTE]
> Die zentrale Hochskalierung kann einige Minuten dauern, und während dieser Zeit wird Ihr Cluster angehalten. Beachten Sie, dass eine Herunterskalierung die Clusterleistung beeinträchtigen kann.

Sie haben nun einen zentralen Hoch- oder Herunterskalierungsvorgang für Ihren Azure Data Explorer-Cluster durchgeführt.

## <a name="next-steps"></a>Nächste Schritte
Sie können auch die [horizontale Skalierung des Clusters verwalten](manage-cluster-scale-out.md), um die Instanzanzahl basierend auf den von Ihnen angegebenen Metriken dynamisch zu skalieren.

Wenn Sie Hilfe bei Clusterskalierungsproblemen benötigen, erstellen Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eine Supportanfrage.

Überwachen Sie Ihre Ressourcenverwendung gemäß diesem Artikel: [Überwachen der Azure Data Explorer-Leistung, -Integrität und -Nutzung mit Metriken](using-metrics.md).
