---
title: Log Analytics in Azure Monitor umfasst eine Reihe von Beispielabfragen, die Sie unverändert ausführen oder als Vorlage für Ihre eigenen Abfragen verwenden können.
description: Abfragen, die Sie als Vorlage verwenden und nach Bedarf ändern können
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: e4b264d980eac50525e9d9013bc2841fe68065a3
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496424"
---
# <a name="example-queries-in-azure-monitor-log-analytics"></a>Beispielabfragen in Azure Monitor Log Analytics
Log Analytics umfasst eine Reihe von Beispielabfragen, die Sie unverändert ausführen oder als Vorlage für Ihre eigenen Abfragen verwenden können. In diesem Artikel werden diese Beispielabfragen und ihre Verwendung beschrieben.

Wenn Sie mit Log Analytics oder der Abfragesprache KQL nicht vertraut sind, eignen sich die Beispielabfragen gut für den Einstieg. Sie bieten einen direkten Einblick in eine Ressource und stellen eine gute Ausgangsbasis für den Einstieg in und die Verwendung von KQL dar, sodass Sie schneller mit der Verwendung von Log Analytics beginnen können. Wir haben mehr als 250 Beispielabfragen gesammelt und zusammengestellt, die Ihnen einen unmittelbaren Nutzen bringen sollen. Die Zahl der Beispielabfragen steigt darüber hinaus kontinuierlich.

## <a name="in-context-queries"></a>Kontextbezogene Abfragen

Diese neue Funktion filtert und schlägt Abfragen im Kontext vor. Mit anderen Worten: Im System werden automatisch nur Abfragen angezeigt, die für den ausgewählten Bereich relevant sind.

- Bei einer **einzelnen Ressource** werden Abfragen entsprechend dem Ressourcentyp gefiltert.
- Bei einer **Ressourcengruppe** werden Abfragen entsprechend den Ressourcen in der spezifischen Ressourcengruppe gefiltert.
- Bei einem **Arbeitsbereich** werden Abfragen entsprechend den im Arbeitsbereich installierten Lösungen gefiltert.

Dieses Verhalten ist bei allen Log Analytics-Bereichen einheitlich. Wenn für den gewünschten Ressourcentyp keine Beispielabfrage angezeigt wird, kann dies an den kontextbezogenen Filtern liegen. In einem späteren Abschnitt wird beschrieben, wie Sie kontextbezogene Bereiche entfernen, sodass alle möglichen Abfragen angezeigt werden.

> [!TIP]
> Je mehr Ressourcen in einem Bereich enthalten sind, desto länger dauert es, bis im Portal die Beispielabfragen gefiltert und im entsprechenden Dialogfeld angezeigt werden.

## <a name="example-query-user-interface"></a>Benutzeroberfläche für Beispielabfragen

Zu den Beispielabfragen können Sie an zwei verschiedenen Stellen navigieren.

### <a name="example-query-dialog"></a>Dialogfeld „Beispielabfragen“

Wenn Sie Log Analytics zum ersten Mal öffnen, wird automatisch das Dialogfeld *Beispielabfragen* angezeigt.  Auf dieses Dialogfeld können Sie auch zugreifen, indem Sie oben rechts auf dem Bildschirm auf **Beispielabfragen** klicken.

![Randleiste: Beispielabfragen](media/saved-queries/sidebar-2.png)

Das Dialogfeld „Beispielabfragen“ wird dann wie unten abgebildet angezeigt:  

![Bildschirm „Beispielabfragen“](media/saved-queries/example-query-start.png)

Im Screenshot oben ist der Protokollbildschirm für eine Azure Key Vault-Instanz zu sehen. Wie zuvor erwähnt, werden die Abfragen kontextbezogen angezeigt.  Somit sind im Screenshot nur die auf Key Vault bezogenen Abfragen zu sehen. Wenn Sie ein gesamtes Abonnement auswählen, werden Abfragen für alle Ressourcentypen in diesem Abonnement angezeigt.  

Jede Beispielabfrage wird durch eine Karte dargestellt. Sie können die Abfragen schnell durchsuchen, um die gewünschte Abfrage zu finden. Sie können die Abfrage direkt über das Dialogfeld ausführen oder in den Abfrage-Editor laden, um zusätzliche Abstimmungen und Anpassungen vorzunehmen.

### <a name="sidebar-query-experience"></a>Bereich „Abfragen“ auf der Seitenleiste

Auf alle Funktionen des Dialogfelds kann auch über den Bereich „Abfragen“ auf der linken Seitenleiste von Log Analytics zugegriffen werden. Sie können mit dem Mauszeiger auf den Namen einer Abfrage zeigen, um die Beschreibung und zusätzliche Funktionen der Abfrage anzuzeigen.

![Screenshot des Bereichs mit Abfragen](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Suchen und Filtern von Abfragen

Die Optionen in diesem Abschnitt sind im Dialogfeld sowie im Bereich „Abfragen“ der Seitenleiste verfügbar, jedoch mit einer etwas anderen Benutzeroberfläche.  

### <a name="use-favorites"></a>Verwenden von Favoriten

Für einen schnelleren Zugriff können Sie häufig verwendete Abfragen als Favoriten festlegen.

> [!TIP]
> Dabei empfiehlt es sich für den Einstieg, Abfragen zunächst zu sammeln und später anzuzeigen. Suchen Sie die gewünschten Abfragen, und klicken Sie auf den Stern neben den jeweiligen Abfragen, um sie den Favoriten hinzuzufügen. Wenn Sie später feststellen, dass eine Abfrage nicht nützlich ist, können Sie die Festlegung als Favorit aufheben.  

### <a name="filtering-and-group-by"></a>Filtern und Gruppieren

Im Dialogfeld „Beispielabfragen“ werden Abfragen nur kontextbezogen gefiltert und angezeigt. Sie können den Filter jedoch entfernen und alle Abfragen anzeigen.

### <a name="group-by"></a>Gruppieren nach

Ändern Sie die Gruppierung der Abfragen, indem Sie auf die Dropdownliste *Gruppieren nach* klicken:

![Bildschirm „Beispielabfragen“, „Gruppieren nach“](media/saved-queries/example-query-groupby.png)

Im Dialogfeld können Abfragen wie folgt gruppiert werden:

- **Ressourcentyp:** eine Ressource entsprechend der Definition in Azure, z. B. ein virtueller Computer. Eine vollständige Zuordnung der Azure Monitor-Protokolle und Log Analytics-Tabellen zum Ressourcentyp finden Sie in der [Referenz zu Azure Monitor-Tabellen](/azure/azure-monitor/reference/tables/tables-resourcetype).  
- **Kategorie:** ein Informationstyp, z. B. *Sicherheit* oder *Überprüfung*. Die Kategorien sind identisch mit den Kategorien, die im Bereich „Tabellen“ der Seitenleiste definiert sind. In der [Referenz zu Azure Monitor-Tabellen](/azure/azure-monitor/reference/tables/tables-category) finden Sie eine vollständige Liste der Kategorien.  
- **Lösung:** eine Azure Monitor-Lösung, die mit den Abfragen verknüpft ist.
- **Thema:** das Thema der Beispielabfrage, z. B. *Aktivitätsprotokolle* oder *App-Protokolle*. Die Themeneigenschaft ist spezifisch für Beispielabfragen und kann sich je nach Ressourcentyp unterscheiden.

Die Gruppierungswerte dienen außerdem als aktives Inhaltsverzeichnis. Durch Klicken auf einen der Werte links auf dem Bildschirm wird in der Ansicht der Abfragen direkt das entsprechende Element angezeigt.

### <a name="filter"></a>Filtern

Sie können die Abfragen auch nach den oben erwähnten Werten für **Gruppieren nach** filtern. Die Filter werden oben im Dialogfeld „Beispielabfragen“ angezeigt.

![Bildschirm „Beispielabfragen“, Filter](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Kombinieren von Gruppierung und Filtern

Die Filter- und Gruppierungsfunktionen können kombiniert werden. Dies bietet Flexibilität bei der Anordnung der Abfragen. Bei einer Ressourcengruppe mit mehreren Ressourcen können Sie die Abfragen beispielsweise nach einem bestimmten Ressourcentyp filtern und dann nach Thema anordnen.

## <a name="sample-query-dialog-appearance-behavior"></a>Anzeige des Dialogfelds „Beispielabfragen“

Wenn Sie mit KQL vertraut sind und den Abfrage-Editor direkt anzeigen möchten, können Sie das Dialogfeld „Beispielabfragen“ deaktivieren. Das Dialogfeld wird dann beim Laden des Log Analytics-Bildschirms nicht geladen.

![Dialogfeld „Beispielabfragen“ aktivieren/deaktivieren](media/saved-queries/examples-on-off.png)

Über die Schaltfläche *Beispielabfragen* in der oberen Leiste von Log Analytics können Sie jederzeit auf das Dialogfeld „Beispielabfragen“ zugreifen.

## <a name="query-explorer"></a>Abfrage-Explorer

Der Abfrage-Explorer zum Speichern und Freigeben von benutzergenerierten Abfragen bleibt vorerst unverändert.

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit KQL-Abfragen](get-started-queries.md)

