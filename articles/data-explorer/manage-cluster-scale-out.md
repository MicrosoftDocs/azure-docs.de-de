---
title: Horizontales Hochskalieren eines Azure Data Explorer-Clusters
description: Dieser Artikel beschreibt Schritte zum horizontalen Hoch- und Herunterskalieren eines Azure Data Explorer-Clusters basierend auf sich änderndem Bedarf.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 24bbddd28943adc929fbaea456eeae8165db290c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60758663"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Verwalten der Clusterskalierung bei sich änderndem Bedarf

Die richtige Größe eines Clusters ist entscheidend für die Leistung von Azure-Daten-Explorer. Aber der Bedarf in einem Cluster kann nicht mit absoluter Genauigkeit vorhergesagt werden. Eine statische Clustergröße kann zu einer Unter- oder Überauslastung führen, was beides nicht ideal ist.

Ein besserer Ansatz ist die *Skalierung* eines Clusters, wobei je nach Bedarf Kapazitäten hinzugefügt und entfernt werden. Es gibt zwei Workflows für die Skalierung: zentrale Skalierung und horizontale Skalierung. In diesem Artikel wird der Workflow für die horizontale Skalierung beschrieben.

In diesem Artikel wird veranschaulicht, wie Sie die horizontale Skalierung eines Clusters verwalten, die auch als Autoskalierung bezeichnet wird. Die automatische Skalierung ermöglicht Ihnen das horizontale Skalieren der Instanzenanzahl basierend auf vordefinierten Regeln und Zeitplänen. Sie legen die Autoskalierungseinstellungen für Ihren Cluster im Azure-Portal fest, wie in diesem Artikel beschrieben.

## <a name="steps-to-configure-autoscale"></a>Schritte zum Konfigurieren der Autoskalierung

Navigieren Sie im Azure-Portal zu Ihrer Data Explorer-Clusterressource. Wählen Sie unter der Überschrift **Einstellungen** die Option **Horizontal hochskalieren** aus. Wählen Sie auf der Registerkarte **Konfigurieren** die Option **Autoskalierung aktivieren** aus.

   ![Aktivieren der automatischen Skalierung](media/manage-cluster-scaling/enable-autoscale.png)

Die folgende Grafik zeigt die Abfolge der nächsten Schritte. Weitere Details finden Sie in der Grafik.

1. Geben Sie im Feld **Name der Einstellung für die Autoskalierung** einen Namen ein, z. B. *Horizontale Skalierung: Cacheauslastung*. 

   ![Skalierungsregel](media/manage-cluster-scaling/scale-rule.png)

2. Wählen Sie unter **Skalierungsmodus** die Option **Basierend auf einer Metrik skalieren** aus. Dieser Modus ermöglicht die dynamische Skalierung. Sie können auch **Auf eine bestimmte Anzahl von Instanzen skalieren** auswählen.

3. Wählen Sie **+ Regel hinzufügen** aus.

4. Geben Sie im Abschnitt **Skalierungsregel** auf der rechten Seite Werte für jede Einstellung an.

    **Kriterien**

    | Einstellung | Beschreibung und Wert |
    | --- | --- |
    | **Zeitaggregation** | Wählen Sie ein Aggregationskriterium aus, z. B. **Durchschnitt**. |
    | **Metrikname** | Wählen Sie die Metrik aus, auf der der Skalierungsvorgang basieren soll, z. B. **Cacheauslastung**. |
    | **Statistik zum Aggregationsintervall** | Wählen Sie zwischen **Durchschnitt**, **Minimum**, **Maximum** und **Summe** aus. |
    | **Operator** | Wählen Sie die entsprechende Option aus, z. B. **Größer als oder gleich**. |
    | **Schwellenwert** | Wählen Sie einen passenden Wert aus. Für die Cacheauslastung ist beispielsweise ist 80 % ein guter Ausgangspunkt. |
    | **Dauer (in Minuten)** | Wählen Sie eine angemessene Zeitspanne, die das System bei der Berechnung von Metriken berücksichtigt. Beginnen Sie mit dem Standardwert von 10 Minuten. |
    |  |  |

    **Aktion**

    | Einstellung | Beschreibung und Wert |
    | --- | --- |
    | **Vorgang** | Wählen Sie die entsprechende Option zum horizontalen Hoch- oder Herunterskalieren aus. |
    | **Anzahl der Instanzen** | Wählen Sie die Anzahl von Knoten oder Instanzen aus, die hinzugefügt oder entfernt werden sollen, wenn eine Metrikbedingung erfüllt ist. |
    | **Abkühlen (Minuten)** | Wählen Sie ein geeignetes Intervall für die Zeit zwischen den einzelnen Skalierungsvorgängen. Beginnen Sie mit dem Standardwert von 5 Minuten. |
    |  |  |

5. Wählen Sie **Hinzufügen**.

6. Geben Sie im Abschnitt **Instanzgrenzwerte** auf der linken Seite Werte für jede Einstellung an.

    | Einstellung | Beschreibung und Wert |
    | --- | --- |
    | **Mindestanforderungen** | Die Anzahl der Instanzen, unter die Ihr Cluster unabhängig von der Auslastung nicht herunterskaliert wird. |
    | **Maximum** | Die Anzahl der Instanzen, über die Ihr Cluster unabhängig von der Auslastung nicht hochskaliert wird. |
    | **Standard** | Die Standardanzahl von Instanzen. Diese Einstellung wird verwendet, wenn Probleme beim Lesen der Ressourcenmetriken auftreten. |
    |  |  |

7. Wählen Sie **Speichern** aus.

Sie haben nun einen horizontalen Skalierungsvorgang für Ihren Azure-Daten-Explorer-Cluster konfiguriert. Fügen Sie eine weitere Regel für einen Vorgang zum horizontalen Herunterskalieren hinzu. Mit dieser Konfiguration kann Ihr Cluster basierend auf den von Ihnen angegebenen Metriken dynamisch skaliert werden.

Sie können auch das [zentrale Hochskalieren von Clustern verwalten](manage-cluster-scale-up.md), um eine geeignete Größenanpassung vorzunehmen.

Wenn Sie Hilfe bei Clusterskalierungsproblemen benötigen, erstellen Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eine Supportanfrage.

## <a name="next-steps"></a>Nächste Schritte

[Überwachen der Azure Data Explorer-Leistung, -Integrität und -Auslastung mit Metriken](using-metrics.md)
