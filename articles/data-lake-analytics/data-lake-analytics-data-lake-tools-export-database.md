---
title: Exportieren einer U-SQL-Datenbank mithilfe von Azure Data Lake Tools für Visual Studio
description: Erfahren Sie, wie Sie mithilfe von Azure Data Lake Tools für Visual Studio eine U-SQL-Datenbank exportieren und automatisch in ein lokales Konto importieren.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: fe28aa8b88f557d4bbcdabf1de1c4bc6491743ce
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60628539"
---
# <a name="export-a-u-sql-database"></a>Exportieren einer U-SQL-Datenbank

In diesem Artikel erfahren Sie, wie Sie mithilfe von [Azure Data Lake Tools für Visual Studio](https://aka.ms/adltoolsvs) eine U-SQL-Datenbank als einzelnes U-SQL-Skript und heruntergeladene Ressourcen exportieren. Sie können die exportierte Datenbank im gleichen Vorgang in ein lokales Konto importieren.

Kunden verwalten in der Regel mehrere Umgebungen für die Entwicklung, zum Testen und für die Produktion. Diese Umgebungen werden sowohl in einem lokalen Konto auf dem lokalen Computer des Entwicklers als auch im Azure Data Lake Analytics-Konto in Azure gehostet. 

Wenn Sie U-SQL-Abfragen in Entwicklungs- und Testumgebungen entwickeln und optimieren, müssen Entwickler häufig ihre Arbeit in einer Produktionsdatenbank neu erstellen. Mit dem Datenbankexport-Assistenten kann dieser Vorgang beschleunigt werden. Entwickler können mithilfe des Assistenten die vorhandene Datenbankumgebung und die Beispieldaten in anderen Data Lake Analytics-Konten klonen.

## <a name="export-steps"></a>Exportschritte

### <a name="step-1-export-the-database-in-server-explorer"></a>Schritt 1: Exportieren der Datenbank im Server-Explorer

Im Server-Explorer sind alle Data Lake Analytics-Konten aufgeführt, für die Sie über Berechtigungen verfügen. So exportieren Sie die Datenbank

1. Erweitern Sie im Server-Explorer das Konto, das die zu exportierende Datenbank enthält.
2. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie dann **Exportieren** aus. 
   
    ![Server-Explorer – Exportieren einer Datenbank](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Wenn die Menüoption **Exportieren** nicht verfügbar ist, müssen Sie das [Tool auf die neueste Version aktualisieren](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Schritt 2: Konfigurieren der zu exportierenden Objekte

Wenn Sie nur einen kleinen Teil einer großen Datenbank benötigen, können Sie im Export-Assistenten eine Teilmenge von Objekten konfigurieren, die Sie exportieren möchten. 

Die Exportaktion wird durch die Ausführung eines U-SQL-Auftrags abgeschlossen. Daher verursacht der Export aus einem Azure-Konto einige Kosten.

![Datenbankexport-Assistent – Auswählen von Exportobjekten](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Schritt 3: Überprüfen der Objektliste und weiterer Konfigurationen

In diesem Schritt können Sie die ausgewählten Objekte im Feld **Objektliste exportieren** überprüfen. Wenn Fehler aufgetreten sind, wählen Sie **Zurück** aus, um zum vorherigen Schritt zurückzukehren und die zu exportierenden Objekte ordnungsgemäß zu konfigurieren.

Sie können auch andere Einstellungen für das Exportziel konfigurieren. Konfigurationsbeschreibungen sind in der folgenden Tabelle aufgeführt:

|Konfiguration|BESCHREIBUNG|
|-------------|-----------|
|Zielname|Dieser Name gibt an, wo die exportierten Datenbankressourcen gespeichert werden sollen. Beispiele sind Assemblys, zusätzliche Dateien und Beispieldaten. Unter dem Stammordner für Ihre lokalen Daten wird ein Ordner mit diesem Namen erstellt.|
|Projektverzeichnis|Dieser Pfad legt fest, wo das exportierte U-SQL-Skript gespeichert werden soll. Alle Datenbankobjektdefinitionen werden an dieser Stelle gespeichert.|
|Nur Schema|Wenn Sie diese Option auswählen, werden nur Datenbankdefinitionen und Ressourcen (z. B. Assemblys und zusätzliche Dateien) exportiert.|
|Schema und Daten|Wenn Sie diese Option auswählen, werden Datenbankdefinitionen, Ressourcen und Daten exportiert. Die oberen N Tabellenzeilen werden exportiert.|
|Automatisch in lokale Datenbank importieren|Wenn Sie diese Option auswählen, wird die exportierte Datenbank automatisch in Ihre lokale Datenbank importiert, wenn der Exportvorgang abgeschlossen ist.|

![Datenbankexport-Assistent – Liste der Exportobjekte und andere Konfigurationen](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Schritt 4: Überprüfen der Exportergebnisse

Wenn der Export beendet ist, können Sie die exportierten Ergebnisse im Protokollfenster des Assistenten anzeigen. Das folgende Beispiel zeigt, wie exportierte U-SQL-Skript- und Datenbankressourcen, einschließlich Assemblys, zusätzliche Dateien und Beispieldaten, gefunden werden können:

![Datenbankexport-Assistent – Exportergebnisse](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importieren der exportierten Datenbank in ein lokales Konto

Am einfachsten importieren Sie die exportierte Datenbank, indem Sie während des Exportvorgangs in Schritt 3 das Kontrollkästchen **Automatisch in lokale Datenbank importieren** aktivieren. Wenn Sie dieses Kontrollkästchen nicht aktiviert haben, suchen Sie zuerst das exportierte U-SQL-Skript im Exportprotokoll. Führen Sie dann das U-SQL-Skript lokal aus, um die Datenbank in Ihr lokales Konto zu importieren.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importieren der exportierten Datenbank in ein Data Lake Analytics-Konto

So importieren Sie die Datenbank in ein anderes Data Lake Analytics-Konto

1. Laden Sie die exportierten Ressourcen (einschließlich Assemblys, zusätzlicher Dateien und Beispieldaten) in das Azure Data Lake Store-Standardkonto des Data Lake Analytics-Kontos hoch, in das die Ressourcen importiert werden sollen. Sie finden den exportierten Ressourcenordner unter dem lokalen Datenstammordner. Laden Sie den gesamten Ordner in das Stammverzeichnis des standardmäßigen Data Lake Store-Kontos hoch.
2. Wenn der Upload abgeschlossen ist, senden Sie das exportierte U-SQL-Skript an das Data Lake Analytics-Konto, in das Sie die Datenbank importieren möchten.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Wenn die Option **Schema und Data** in Schritt 3 ausgewählt haben, führt das Tool derzeit einen U-SQL-Auftrag aus, um die in Tabellen gespeicherten Daten zu exportieren. Aus diesem Grund kann der Prozess des Datenexports sehr langsam sein und es können Kosten für Sie anfallen. 

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zu U-SQL-Datenbanken](/u-sql/data-definition-language-ddl-statements) 
* [Testen und Debuggen von U-SQL-Aufträgen mit lokalen Testläufen und dem Azure Data Lake-U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)


