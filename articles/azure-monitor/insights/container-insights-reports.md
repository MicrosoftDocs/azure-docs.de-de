---
title: Berichte in Azure Monitor für Container
description: Hier werden die Berichte beschrieben, die zum Analysieren der von Azure Monitor für Container gesammelten Daten bereitstehen.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 3cc2f8fb9bfaa278ce06b4a8cd6d379397b7129a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907402"
---
# <a name="reports-in-azure-monitor-for-containers"></a>Berichte in Azure Monitor für Container
Als Berichte in Azure Monitor für Container werden standardmäßige [Azure-Arbeitsmappen](../platform/workbooks-overview.md) empfohlen. In diesem Artikel werden die verschiedenen verfügbaren Berichte und der Zugriff darauf beschrieben.

## <a name="viewing-reports"></a>Anzeigen von Berichten
Wählen Sie im Azure-Portal im **Azure Monitor**-Menü die Option **Container** aus. Wählen Sie im Abschnitt **Überwachung** die Option **Einblicke** aus. Wählen Sie dann einen bestimmten Cluster und anschließend die Seite **Berichte (Vorschau)** aus. 

[![Seite „Berichte“](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Benutzerdefinierte Arbeitsmappe erstellen
Wenn Sie eine benutzerdefinierte Arbeitsmappe auf Grundlage einer dieser Arbeitsmappen erstellen möchten, wählen Sie die Dropdownliste **Arbeitsmappen anzeigen** und dann am Ende dieser Liste den Eintrag **Zum AKS-Katalog wechseln** aus. Weitere Informationen zu Arbeitsmappen und zur Verwendung von Arbeitsmappenvorlagen finden Sie unter [Azure Monitor-Arbeitsmappen](../platform/workbooks-overview.md).

[![AKS-Katalog](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Arbeitsmappen für Knoten

- **Datenträgerkapazität**: Interaktive Diagramme zur Datenträgerauslastung für jeden Datenträger, der dem Knoten in einem Container bereitgestellt wird, anhand der folgenden Gesichtspunkte:

    - Prozentuale Datenträgerauslastung für alle Datenträger.
    - Freier Speicherplatz für alle Datenträger.
    - Ein Raster, in dem der Datenträger jedes Knotens, der prozentual genutzte Speicherplatz mit dem zugehörigen Trend sowie der freie Speicherplatz auf dem Datenträger (GiB) mit dem Trend (GiB) angezeigt werden. Wenn in der Tabelle eine Zeile ausgewählt wird, wird der Prozentsatz des belegten und freien Speicherplatzes auf dem Datenträger unterhalb der Zeile angezeigt (in GiB).

- **Datenträger-E/A**: Interaktive Diagramme zur Datenträgerauslastung für jeden Datenträger, der dem Knoten in einem Container bereitgestellt wird, anhand der folgenden Gesichtspunkte:

    - Zusammenfassung der Datenträger-E/A auf allen Datenträgern nach gelesenen Bytes/Sek., geschriebenen Bytes/Sek. und Trends der gelesenen und geschriebenen Bytes/Sek.
    - In acht Leistungsdiagrammen werden wichtige Leistungsindikatoren angezeigt, um Datenträger-E/A-Engpässe zu messen und zu identifizieren.

- **GPU**: Interaktive Diagramme zur GPU-Auslastung für jeden GPU-fähigen Kubernetes-Clusterknoten.

## <a name="resource-monitoring-workbooks"></a>Arbeitsmappen für Ressourcenüberwachung

- **Bereitstellungen**: Status Ihrer Bereitstellungen und der horizontalen automatischen Podskalierung (HPA) einschließlich benutzerdefinierter HPA. 
  
- **Workloaddetails**: Interaktive Diagramme zu Leistungsstatistiken der Workloads für einen Namespace. Umfasst mehrere Registerkarten:

  - Eine Übersicht über die CPU- und Arbeitsspeicherauslastung nach POD.
  - „POD-/Containerstatus“ zeigt den Trend für POD-Neustarts, den Trend für Containerneustarts und den Containerstatus für PODs.
  - „Kubernetes-Ereignisse“ zeigt eine Zusammenfassung der Ereignisse für den Controller.

- **Kubelet**: Enthält zwei Raster, die die Hauptknoten-Betriebsstatistiken anzeigen:

    - „Übersicht nach Knotenraster“ fasst alle Vorgänge, alle Fehler und die erfolgreichen Vorgänge für jeden Knoten in Prozent und Trend zusammen.
    - „Übersicht nach Vorgangstyp“ fasst die Vorgänge für jeden Typ, alle Fehler und die erfolgreichen Vorgänge in Prozent und Trend zusammen.
## <a name="billing-workbooks"></a>Arbeitsmappen für Abrechnung

- **Datennutzung**: Hiermit können Sie die Datenquellen visualisieren, ohne selbst eine Bibliothek mit Abfragen basierend auf den in der Dokumentation freigegebenen Informationen erstellen zu müssen. Die Arbeitsmappe enthält Diagramme, mit denen Sie abrechenbare Daten nach folgenden Gesichtspunkten anzeigen können:

  - Gesamtmenge der erfassten abrechenbaren Daten (in GB) pro Lösung
  - in Containerprotokollen (Anwendungsprotokollen) erfasste abrechenbare Daten
  - in einem Kubernetes-Namespace erfasste abrechenbare Containerprotokolldaten
  - unter einem Clusternamen erfasste abrechenbare Containerprotokolldaten
  - in einem Protokollquelleintrag erfasste abrechenbare Containerprotokolldaten
  - in Diagnosemasterknoten-Protokollen erfasste abrechenbare Diagnosedaten

## <a name="networking-workbooks"></a>Arbeitsmappen für Netzwerk

- **NPM-Konfiguration**:  Überwachung Ihrer Netzwerkkonfigurationen, die über den Netzwerkrichtlinien-Manager (NPM) konfiguriert werden.

  - Zusammenfassungsinformationen zur Gesamtkomplexität der Konfiguration.
  - Die Anzahlen von Richtlinien, Regeln und Gruppen über einen Zeitraum, die Einblicke in die Beziehung zwischen den drei ermöglichen, und Hinzufügen einer Zeitdimension zum Debuggen einer Konfiguration.
  - Anzahl der Einträge in allen IPSets und den einzelnen IPSets
  - Schlechteste und durchschnittliche Leistung pro Knoten zum Hinzufügen von Komponenten zu Ihrer Netzwerkkonfiguration.

- **Netzwerk:** Interaktive Diagramme zur Netzwerkauslastung für die Netzwerkadapter der einzelnen Knoten und ein Raster, in dem die wichtigsten Leistungsindikatoren zum Messen der Leistung der Netzwerkadapter angezeigt werden.



## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zu Arbeitsmappen in Azure Monitor finden Sie unter [Azure Monitor-Arbeitsmappen](../platform/workbooks-overview.md).
