---
title: Migrieren Ihrer Daten nach SQL Data Warehouse | Microsoft Docs
description: Tipps für die Migration von Daten in Azure SQL Data Warehouse zum Entwickeln von Lösungen.
services: sql-data-warehouse
author: TwoUnder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: mausher
ms.reviewer: igorstan
ms.openlocfilehash: 61506de6a8edd66a36148b8925d38490eb3b57ee
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873523"
---
# <a name="migrate-your-data"></a>Migrieren von Daten
Daten können aus unterschiedlichen Quellen und mithilfe verschiedener Tools in Ihr SQL Data Warehouse verschoben werden.  Zum Erreichen dieses Ziels können die ADF-Kopieraktivität, SSIS und bcp verwendet werden. Mit zunehmender Menge der Daten empfiehlt es sich jedoch, den Vorgang der Datenmigration in verschiedene Schritte aufzugliedern. Dies bietet Ihnen die Möglichkeit, die einzelnen Schritte im Hinblick auf Leistung und Belastbarkeit zu optimieren und so eine reibungslose Datenmigration zu gewährleisten.

In diesem Artikel werden zunächst einfache Migrationsszenarien der ADF-Kopieraktivität sowie von SSIS und bcp erörtert. Dann wird detaillierter erläutert, wie die Migration optimiert werden kann.

## <a name="azure-data-factory-adf-copy"></a>Azure Data Factory (ADF)-Kopieraktivität
Die [ADF-Kopieraktivität][ADF Copy] ist Teil von [Azure Data Factory][Azure Data Factory]. Mithilfe der ADF-Kopieraktivität können Sie Ihre Daten in Flatfiles im lokalen Speicher, in Remoteflatfiles in Azure Blob Storage oder direkt in SQL Data Warehouse exportieren.

Wenn sich Ihre Daten in Flatfiles befinden, müssen Sie sie zuerst in den Azure-Blobspeicher übertragen. Erst dann können Sie sie in SQL Data Warehouse laden. Nachdem die Daten in Azure Blob Storage übertragen wurden, können Sie sie dann wieder mithilfe der [ADF-Kopieraktivität][ADF Copy] in SQL Data Warehouse übertragen.

Auch PolyBase stellt eine leistungsstarke Möglichkeit zum Laden der Daten dar. Allerdings bedeutet dies, dass statt einem zwei Tools verwendet werden. Wenn Ihr Schwerpunkt auf der besten Leistung liegt, sollten Sie PolyBase verwenden. Wenn Sie nur ein Tool verwenden möchten (und die Daten nicht allzu umfangreich sind), ist ADF die beste Lösung für Sie.

Führen Sie [dieses Tutorial](../data-factory/load-azure-sql-data-warehouse.md) aus, um zu erfahren, wie ADF zum Laden von Daten in Ihr Data Warehouse verwendet wird.

## <a name="integration-services"></a>Integration Services
Integration Services (SSIS) ist ein leistungsfähiges und flexibles ETL-Tool (Extrahieren Transformieren und Laden), das komplexe Workflows, Datentransformation und verschiedene Optionen zum Laden von Daten unterstützt. Mit SSIS können Sie Daten in Azure oder als Teil einer größeren Migration übertragen.

> [!NOTE]
> Mit SSIS können Daten ohne die Bytereihenfolge-Marke in der Datei in das Format UTF-8 exportiert werden. Um dies zu konfigurieren, müssen Sie die Zeichendaten im Datenfluss mithilfe der abgeleiteten Spaltenkomponente zunächst so konvertieren, dass die UTF-8-Codepage 65001 verwendet wird. Nachdem die Spalten konvertiert wurden, schreiben Sie die Daten in den Flatfile-Zieladapter. Dabei müssen Sie sicherstellen, dass 65001 auch als Codepage für die Datei ausgewählt wurde.
> 
> 

SSIS stellt eine Verbindung mit SQL Data Warehouse her. Die Verbindungsherstellung entspricht der mit einer SQL Server-Bereitstellung. Für die Verbindungen muss jedoch ein ADO.NET-Verbindungs-Manager verwendet werden. Zudem sollten Sie darauf achten, dass die Einstellung "Sofern verfügbar, Masseneinfügung verwenden" konfiguriert ist, um den Durchsatz zu maximieren. Weitere Informationen zu dieser Eigenschaft finden Sie in dem Artikel zum [ADO.NET-Zieladapter][ADO.NET destination adapter].

> [!NOTE]
> Die Verbindungsherstellung mit Azure SQL Data Warehouse mithilfe von OLEDB wird nicht unterstützt.
> 
> 

Darüber hinaus besteht immer die Möglichkeit, dass ein Paket aufgrund von Drosselung oder Netzwerkproblemen fehlschlagen kann. Entwerfen Sie die Pakete so, dass sie zum Zeitpunkt des Fehlers fortgesetzt werden können, ohne dass die vor dem Fehler abgeschlossenen Arbeitsschritte erneut ausgeführt werden müssen.

Weitere Informationen finden Sie in der [SSIS-Dokumentation][SSIS documentation].

## <a name="bcp"></a>bcp
bcp ist ein Befehlszeilenprogramm, das für den Import und Export von Flatfiledaten entwickelt wurde. Einige Transformationen können während des Datenexports durchgeführt werden. Zum Durchführen einfacher Transformationen können Sie die Daten mit einer Abfrage auswählen und transformieren. Nach dem Export können die Flatfiles dann direkt in das Ziel in der SQL Data Warehouse-Datenbank geladen werden.

> [!NOTE]
> Häufig empfiehlt es sich, die während des Datenexports verwendeten Transformationen in einer Ansicht im Quellsystem zu kapseln. Dadurch wird sichergestellt, dass die Logik beibehalten wird und der Prozess wiederholbar ist.
> 
> 

bcp bietet folgende Vorteile:

* Einfachheit. bcp-Befehle lassen sich einfach erstellen und ausführen.
* Neustartfähiger Ladevorgang. Nach dem Exportieren kann der Ladevorgang beliebig oft ausgeführt werden.

bcp weist folgende Einschränkungen auf:

* bcp funktioniert nur mit tabellarischen Flatfiles. Für Dateien im XML- oder JSON-Format kann es z. B. nicht verwendet werden.
* Die Funktionen für die Datentransformation beschränken sich auf den Export und sind relativ einfach.
* bcp bietet keine Stabilität beim Laden von Daten über das Internet. Jede Instabilität im Netzwerk kann daher zu Fehlern beim Laden führen.
* bcp stützt sich auf das in der Zieldatenbank vor dem Laden vorhandene Schema.

Weitere Informationen finden Sie unter [Verwenden von bcp zum Laden von Daten in SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optimieren der Datenmigration
Ein SQLDW-Datenmigrationsprozess kann wirksam in drei separate Schritte unterteilt werden:

1. Exportieren der Quelldaten
2. Übertragen der Daten in Azure
3. Laden der Daten in die SQLDW-Zieldatenbank

Jeder Schritt kann einzeln optimiert werden, sodass ein stabiler, neustartfähiger und flexibler Migrationsprozess entsteht, der die Leistung in jedem Schritt maximiert.

## <a name="optimizing-data-load"></a>Optimieren des Ladens von Daten
Betrachten wir diese Schritte zunächst in der umgekehrten Reihenfolge: Daten können am schnellsten über PolyBase geladen werden. Für die Optimierung eines über PolyBase durchgeführten Ladevorgangs müssen in den vorhergehenden Schritten bestimmte Voraussetzungen gegeben sein. Machen Sie sich am besten im Voraus damit vertraut. Sie lauten wie folgt:

1. Codierung der Datendateien
2. Format der Datendateien
3. Speicherort der Datendateien

### <a name="encoding"></a>Codieren
Für PolyBase müssen Datendateien mit UTF-8 oder UTF-16FE codiert sein. 



### <a name="format-of-data-files"></a>Format der Datendateien
PolyBase erfordert das feste Zeilenabschlusszeichen "\n" oder einen Zeilenvorschub. Ihre Datendateien müssen diesem Standard entsprechen. Für Zeichenfolgen- oder Spaltenabschlusszeichen gibt es keine Einschränkungen.

Sie müssen jede Spalte in der Datei als Teil der externen Tabelle in PolyBase definieren. Stellen Sie sicher, dass alle exportierten Spalten erforderlich sind und die Datentypen den erforderlichen Standards entsprechen.

Ausführliche Informationen zu den unterstützten Datentypen finden Sie im Artikel [Migrieren Ihres Schemas].

### <a name="location-of-data-files"></a>Speicherort der Datendateien
SQL Data Warehouse verwendet PolyBase ausschließlich zum Laden von Daten aus Azure Blob Storage. Aus diesem Grund müssen die Daten zuerst in den Blob-Speicher übertragen werden.

## <a name="optimizing-data-transfer"></a>Optimieren der Datenübertragung
Einer der langsamsten Schritte bei der Datenmigration ist die Übertragung der Daten in Azure. Dabei kann nicht nur die Netzwerkbandbreite Probleme verursachen, sondern auch die Zuverlässigkeit des Netzwerks zu Beeinträchtigungen führen. Die Migration von Daten in Azure erfolgt standardmäßig über das Internet, sodass mit Übertragungsfehlern zu rechnen ist. Diese Fehler führen jedoch möglicherweise dazu, dass Daten vollständig oder teilweise erneut gesendet werden müssen.

Glücklicherweise stehen mehrere Optionen zur Verbesserung der Geschwindigkeit und Stabilität dieses Vorgangs zur Verfügung:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Beispielsweise können Sie [ExpressRoute][ExpressRoute] verwenden, um die Übertragung zu beschleunigen. [ExpressRoute][ExpressRoute] stellt Ihnen eine bereits vorhandene private Verbindung mit Azure zur Verfügung, d.h., die Verbindung erfolgt nicht über das öffentliche Internet. Dieser Schritt ist keineswegs unbedingt erforderlich. Dadurch verbessert sich jedoch der Durchsatz bei der Übertragung von Daten in Azure von einem Standort vor Ort oder in der Nähe.

Die Verwendung von [ExpressRoute][ExpressRoute] bietet folgende Vorteile:

1. Erhöhte Zuverlässigkeit
2. Höhere Netzwerkgeschwindigkeit
3. Niedrigere Netzwerklatenz
4. Höhere Netzwerksicherheit

[ExpressRoute][ExpressRoute] erweist sich nicht nur bei der Migration, sondern auch bei einer Reihe verschiedener Szenarien als vorteilhaft.

Interessiert? Weitere Informationen und Preise finden Sie in der [ExpressRoute-Dokumentation][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Azure Import/Export-Dienst
Der Azure Import/Export-Dienst ist ein Datenübertragungsprozess zur Übertragung umfangreicher (GB++) und sehr umfangreicher (TB++) Datenmengen in Azure. Dabei werden Ihre Daten auf Datenträger geschrieben und an ein Azure-Rechenzentrum versendet. Der Inhalt der Datenträger wird dann in Ihrem Auftrag in Azure-Blobspeicher geladen.

Allgemeine Übersicht über den Import- und Exportprozess:

1. Konfigurieren eines Azure Blob Storage-Containers zum Empfangen der Daten
2. Exportieren der Daten in den lokalen Speicher
3. Kopieren der Daten auf SATA II/III-Festplatten (3,5 Zoll) mit dem +++[Azure Import/Export-Tool]
4. Erstellen eines Importauftrags mithilfe des Azure Import/Export-Diensts mit den vom [Azure Import/Export-Tool] generierten Journaldateien
5. Versenden der Datenträger an das von Ihnen benannte Azure-Rechenzentrum
6. Übertragen der Daten in Ihren Azure Blob Storage-Container
7. Laden der Daten in SQLDW mithilfe von PolyBase

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy]-Hilfsprogramm
Das Hilfsprogramm [AZCopy][AZCopy] eignet sich hervorragend zum Übertragen Ihrer Daten in Azure-Speicherblobs. Es ist für die Übertragung kleiner (MB++) bis sehr umfangreicher (GB++) Datenmengen konzipiert. [AZCopy] bietet zudem einen hohen stabilen Durchsatz beim Übertragen von Daten in Azure und ist daher eine gute Wahl für die Datenübertragung. Nach dem Übertragen können Sie die Daten mithilfe von PolyBase in SQL Data Warehouse laden. Sie können AZCopy mit dem Task "Prozess ausführen" auch in Ihre SSIS-Pakete integrieren.

Zur Verwendung von AZCopy müssen Sie dieses Programm zunächst herunterladen und installieren. Es sind eine [Produktionsversion][production version] und eine [Vorschauversion][preview version] verfügbar.

Eine Datei können Sie mit einem Befehl wie dem folgenden aus Ihrem Dateisystem hochladen:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Allgemeine Übersicht über diesen Prozess:

1. Konfigurieren eines Azure-Blob-Speichercontainers zum Empfangen der Daten
2. Exportieren der Daten in den lokalen Speicher
3. Kopieren der Daten in den Azure Blob Storage-Container mithilfe von AZCopy
4. Laden der Daten in SQL Data Warehouse mithilfe von PolyBase

Die vollständige Dokumentation finden Sie unter: [AzCopy][AzCopy].

## <a name="optimizing-data-export"></a>Optimieren des Datenexports
Neben dem Sicherstellen, dass der Export den von PolyBase vorgegebenen Anforderungen entspricht, können Sie den gesamten Prozess durch die Optimierung des Datenexports weiter verbessern.



### <a name="data-compression"></a>Datenkomprimierung
PolyBase kann die mit GZip komprimierten Daten lesen. Wenn Sie Ihre Daten in GZip-Dateien komprimieren können, minimieren Sie die Menge der über das Netzwerk übertragenen Daten.

### <a name="multiple-files"></a>Mehrere Dateien
Die Unterteilung großer Tabellen in mehrere Dateien verbessert nicht nur die Exportgeschwindigkeit, sondern auch die Neustartfähigkeit bei der Übertragung und die gesamte Verwaltbarkeit der Daten, wenn sie sich in Azure Blob Storage befinden. Einer der vielen Vorteile von PolyBase ist, dass alle Dateien in einem Ordner gelesen und wie eine einzige Tabelle behandelt werden. Es empfiehlt sich daher, die Dateien für die einzelnen Tabellen jeweils in einem separaten Ordner zu speichern.

PolyBase unterstützt auch eine Funktion zum "rekursiven Ordnerdurchlauf". Mit dieser Funktion können Sie die Organisation der exportierten Daten weiter verbessern und so die Datenverwaltung optimieren.

Weitere Informationen zum Laden von Daten mit PolyBase finden Sie unter [Verwenden von PolyBase zum Laden von Daten in SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Migration finden Sie unter [Migrieren Ihrer Lösung zu SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/load-azure-sql-data-warehouse.md 
[ADF Copy examples]: ../data-factory/quickstart-create-data-factory-dot-net.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrieren Ihres Schemas]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: https://azure.microsoft.com/services/data-factory/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: https://azure.microsoft.com/documentation/services/expressroute/

[production version]: https://aka.ms/downloadazcopy/
[preview version]: https://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
