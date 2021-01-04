---
author: hrasheed-msft
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 11/20/2020
ms.author: hrasheed
ms.openlocfilehash: 6207e5720f2b92a260a541ab3b8134ba73e1b720
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549949"
---
## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

> [!Note] 
> Die hier gezeigten Schritte und Screenshots veranschaulichen die allgemeine Verwaltung von Überprüfungen für verschiedene Datenquellentypen. Ihre Optionen können sich geringfügig unterscheiden (je nachdem, welche Arten von Datenquellen Sie verwenden).

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die von Ihnen registrierte Datenquelle aus.

1. Wählen Sie **+ New scan** (+ Neue Überprüfung) aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus. 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich Ihrer Überprüfung auf bestimmte Teile der Datenquelle (etwa auf Ordner, Sammlungen oder Schemas) festlegen, indem Sie in der Liste die entsprechenden Kontrollkästchen aktivieren.

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie anschließend einen Überprüfungsregelsatz für Ihre Überprüfung aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Optionen und der Inlineerstellung eines neuen Satzes wählen.

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

## <a name="viewing-your-scans-and-scan-runs"></a>Anzeigen Ihrer Überprüfungen und Überprüfungsausführungen

Gehen Sie zum Anzeigen vorhandener Überprüfungen wie folgt vor:

1. Navigieren Sie zum Verwaltungscenter. Wählen Sie im Abschnitt **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** aus. 

2. Wählen Sie die gewünschte Datenquelle aus. Daraufhin wird eine Liste mit vorhandenen Überprüfungen für diese Datenquelle angezeigt.

3. Wählen Sie die Überprüfung aus, für deren Ergebnisse Sie sich interessieren.

4. Auf dieser Seite werden alle vorherigen Überprüfungsausführungen sowie die Metriken und der Status der jeweiligen Überprüfungsausführung angezeigt. Außerdem sehen Sie hier, ob es sich um eine geplante oder um eine manuelle Überprüfung gehandelt hat, auf wie viele Ressourcen Klassifizierungen angewendet wurden, wie viele Ressourcen insgesamt ermittelt wurden, wann die Überprüfung gestartet und beendet wurde und wie lange die Überprüfung insgesamt gedauert hat.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Verwalten Ihrer Überprüfungen: Bearbeiten, Löschen oder Abbrechen

Gehen Sie zum Verwalten oder Löschen einer Überprüfung wie folgt vor:

1. Navigieren Sie zum Verwaltungscenter. Wählen Sie im Abschnitt **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** und anschließend die gewünschte Datenquelle aus.

2. Wählen Sie die Überprüfung aus, die Sie verwalten möchten. Wenn Sie die Überprüfung bearbeiten möchten, wählen Sie **Bearbeiten** aus.

3. Wenn Sie Ihre Überprüfung löschen möchten, wählen Sie **Löschen** aus. 
