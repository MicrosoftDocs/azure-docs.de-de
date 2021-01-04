---
title: Berichterstellung zur Dateierweiterung für Ihre Daten mithilfe von Purview-Erkenntnissen
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Berichterstellung zur Dateierweiterung mit Purview-Erkenntnissen für Ihre Daten anzeigen und verwenden können.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 222106f5838a1eb237987afaa93924f1efe4a1f4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746283"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Dateierweiterungserkenntnisse zu Ihren Daten aus Azure Purview 

In dieser Schrittanleitung wird beschrieben, wie Sie auf die in Ihren Daten gefundenen Dateierweiterungen bzw. Dateitypen zugreifen, sie anzeigen und filtern können.

Unterstützte Datenquellen: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2

In dieser Schrittanleitung lernen Sie Folgendes:
> [!div class="checklist"]
> * Starten Ihres Purview-Kontos über Azure 
> - Anzeigen von Dateierweiterungserkenntnissen zu Ihren Daten
> - Ausführen eines Drilldowns zum Abrufen weiterer Dateierweiterungsdetails zu Ihren Daten

## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie vor dem Beginn mit Purview-Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

- Einrichten Ihrer Azure-Ressourcen und Füllen der entsprechenden Konten mit Testdaten

- Einrichten und Abschließen einer Überprüfung der Testdaten in den einzelnen Datenquellen

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Verwenden von Purview-Erkenntnisse zu Dateierweiterungen

Beim Überprüfen Ihrer Ressourcen ist Azure Purview in der Lage, die in Ihrem Datenbestand gefundenen Dateitypen zu erkennen und Ihnen weitere Details zu jedem Dateityp bereitzustellen. Die Details umfassen, über wie viele Dateien der einzelnen Typen Sie verfügen, wo sich diese Dateien befinden und ob sie auf vertrauliche Daten durchsucht werden können.

> [!NOTE]
> Nachdem Sie Ihre Quelltypen überprüft haben, geben Sie den Erkenntnissen zur **Dateierweiterung** ein paar Stunden Zeit, um die neuen Ressourcen zu berücksichtigen.

**So zeigen Sie Erkenntnisse zu Dateierweiterungen an**

1. Wechseln Sie zum **Azure Purview**-[Instanzbildschirm im Azure-Portal](https://aka.ms/purviewportal), und wählen Sie Ihr Purview-Konto aus.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kontokachel **Purview starten** aus.

1. Wählen Sie in Purview das Menüelement **Erkenntnisse** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: auf der linken Seite aus, um auf Ihren **Erkenntnisse**-Bereich zuzugreifen.
    
1. Wählen Sie in **Erkenntnisse** die Registerkarte **Dateierweiterungen** aus.

    Der Bericht zeigt eine Zusammenfassung an, wie viele eindeutige Dateierweiterungen gefunden wurden, sowie einen Graphen der 10 am häufigsten gefundenen Erweiterungen über den ausgewählten Zeitraum (Standard: 30 Tage).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Dateierweiterungsbericht: Übersicht" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Gehen Sie folgendermaßen vor, um weitere Informationen zu erhalten:

    - Wählen Sie den Selektor **Zeit** am oberen Rand des Berichts aus, um die Zeitspanne zu ändern, für die die Dateierweiterungen gefunden wurden.
    
    - Wählen Sie **Mehr anzeigen** unterhalb des Graphen aus, um eine vollständige Liste der gefundenen Dateierweiterungen anzuzeigen. Weitere Informationen finden Sie unter [Drilldown zu den Dateierweiterungserkenntnissen](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Drilldown zu den Dateierweiterungserkenntnissen

Nachdem Sie die allgemeinen Informationen zu den Dateitypen in Ihrem Datenbestand angezeigt haben, können Sie weitere Details dazu anzeigen, wo sich die Dateien befinden und ob sie auf vertrauliche Daten überprüft werden können.

Beispiel:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Dateierweiterungsbericht: Drilldown" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

Das Raster zeigt Details für jede gefundene Dateierweiterung, einschließlich:

- **Dateianzahl** Die Anzahl der Dateien mit der angegebenen Erweiterung.
- **Inhaltsüberprüfung** Gibt an, ob die Dateierweiterung für das Überprüfen auf vertrauliche Daten unterstützt wird.
- **Abonnements** Die Anzahl der Abonnements, bei denen die angegebene Erweiterung gefunden wurde.
- **Quellen** Die Anzahl der gefundenen Quellen mit der angegebenen Dateierweiterung.



Verwenden Sie die Filter oberhalb des Rasters, um die angezeigten Daten zu filtern:

|Option  |BESCHREIBUNG  |
|---------|---------|
|**Nach Schlüsselwort filtern**     |    Geben Sie Text in das Feld **Nach Schlüsselwort filtern** ein, um Ihre Dateitypen nach Namen zu filtern. Geben Sie z. B. `PDF` ein, um nur PDF-Dateien anzuzeigen.     |
|**Time**        | Wählen Sie diese Option aus, um nach einer bestimmten Zeitspanne zu filtern, in der Ihre Daten erstellt wurden. <br>**Standardwert:** 30 Tage  |
|**Dateierweiterung**     |Wählen Sie diese Option aus, um das Raster nach einem oder mehreren Dateitypen zu filtern.        |
|**Sources**    |Wählen Sie diese Option aus, um das Raster nach bestimmten Datenquellen zu filtern. |
|**Inhaltsüberprüfung**     |Wählen Sie **Unterstützt** oder **Nicht unterstützt** aus, um nur Dateitypen anzuzeigen, die weiter auf vertrauliche Daten überprüft werden können, oder Daten, die nicht überprüft werden können, wie **CERT**- oder **JPG**-Dateien. |
| | |

Wählen Sie oberhalb der Filter die Option **Spalten bearbeiten** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: aus, um mehr oder weniger Spalten in Ihrem Raster anzuzeigen oder die Reihenfolge zu ändern. 

Wählen Sie einen Spaltenheader aus, um das Raster nach dieser Spalte zu sortieren.
## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Purview-Erkenntnisberichte
> [!div class="nextstepaction"]
> [Glossarerkenntnisse](glossary-insights.md)

> [!div class="nextstepaction"]
> [Überprüfungserkenntnisse](scan-insights.md)

> [!div class="nextstepaction"]
> [Klassifizierungs-Erkenntnisse](./classification-insights.md)

> [!div class="nextstepaction"]
> [Vertraulichkeitsbezeichnungs-Erkenntnisse](sensitivity-insights.md)
