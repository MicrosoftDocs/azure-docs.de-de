---
title: Entwurfsmuster für Azure-Tabellenspeicher | Microsoft-Dokumentation
description: Sehen Sie sich die Entwurfsmuster an, die für die Verwendung mit Tabellenspeicherdienst-Lösungen in Azure geeignet sind. Behandeln Sie die in anderen Artikeln erläuterten Probleme und Kompromisse.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 20e776e649d13e435a7bc9215802fcd89efe0867
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019224"
---
# <a name="table-design-patterns"></a>Entwurfsmuster für die Tabelle
Dieser Abschnitt beschreibt einige Muster, die zur Verwendung mit Tabellenspeicherdienstlösungen geeignet sind. Darüber hinaus wird gezeigt, wie Sie einige der in anderen Artikeln zum Tabellenspeicherentwurf angesprochenen Probleme und Kompromisse praktisch behandeln können. Das folgende Diagramm fasst die Beziehungen zwischen den verschiedenen Mustern zusammen:  

![Suchen nach verknüpften Daten](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


In der Muster-Karte oben werden einige Beziehungen zwischen Muster (Blau) und Antimuster (Orange) hervorgehoben, die in diesem Handbuch dokumentiert sind. Es gibt viele weitere Muster, die in Betracht gezogen werden können. Beispielsweise ist eines der Hauptszenarien für den Tabellenspeicherdienst die Verwendung des [Materialized View Pattern](/previous-versions/msp-n-p/dn589782(v=pandp.10)) (Muster für materialisierte Sichten) aus dem [Command Query Responsibility Segregation (CQRS)](/previous-versions/msp-n-p/jj554200(v=pandp.10))-Muster.  

## <a name="intra-partition-secondary-index-pattern"></a>Sekundäres Indexmuster für Intra-Partition
Speichern mehrerer Kopien jeder Entität mit unterschiedlichen **RowKey**-Werten (in der gleichen Partition) zur Ermöglichung schneller und effizienter Suchvorgänge und alternativer Sortierreihenfolgen mit unterschiedlichen **RowKey**-Werten. Updates zwischen Kopien können durch die Verwendung von EGT konsistent sein.  

### <a name="context-and-problem"></a>Kontext und Problem
Der Tabellenspeicherdienst indiziert automatisch Entitäten mit den **PartitionKey**- und **RowKey**-Werten. Dadurch wird eine Client-Anwendung in die Lage versetzt, eine Entität effizient unter Verwendung dieser Werte abzurufen. Beispiel: Durch Verwendung der unten gezeigten Tabellenstruktur kann eine Clientanwendung eine Punktabfrage verwenden, um eine einzelne Mitarbeiterentität mit dem Abteilungsnamen und der Mitarbeiter-ID abzurufen (Werte **PartitionKey** und **RowKey**). Ein Client kann auch Entitäten abrufen, die nach Mitarbeiter-ID in jeder Abteilung sortiert sind.

![Abbildung06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Wenn Sie auch eine Mitarbeiterentität finden möchten, die auf dem Wert einer anderen Eigenschaft basiert, wie z. B. die E-Mail-Adresse, müssen Sie einen weniger effizienten Partition-Scan verwenden, um eine Übereinstimmung zu finden. Der Grund ist, dass der Tabellenspeicherdienst keine sekundären Indizes bietet. Darüber hinaus steht keine Option zum Anfordern einer Liste der Mitarbeiter zur Verfügung, die in einer anderen Reihenfolge als in der **RowKey**-Reihenfolge sortiert ist.  

### <a name="solution"></a>Lösung
Um das Fehlen von sekundären Indizes zu umgehen, können Sie mehrere Kopien der einzelnen Entitäten speichern, wobei jede Kopie einen unterschiedlichen **RowKey** -Wert verwendet. Wenn Sie eine Entität mit den unten gezeigten Strukturen speichern, können Sie Mitarbeiterentitäten auf Grundlage der E-Mail-Adresse oder Mitarbeiter-ID effizient abrufen. Die Präfixwerte für **RowKey** („empid_“ und „email_“) ermöglichen es Ihnen, einen einzelnen Mitarbeiter oder einen Bereich von Mitarbeitern abzufragen, indem Sie einen Bereich von E-Mail-Adressen oder Mitarbeiter-IDs verwenden.  

![Mitarbeiterentitäten](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Die beiden folgenden Filterkriterien (eine Suche nach Mitarbeiter-ID und eine Suche nach E-Mail-Adresse) definieren jeweils eine Punktabfrage:  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'email_jonesj@contoso.com')  

Bei einer Abfrage nach einem Bereich von Mitarbeiterentitäten können Sie einen Bereich festlegen, der entweder nach Mitarbeiter-ID oder nach E-Mail-Adresse sortiert ist, indem Sie die Entitäten mit dem entsprechenden Präfix in **RowKey** abfragen.  

* Um alle Mitarbeiter in der Vertriebsabteilung mit einer Mitarbeiter-ID im Bereich von 000100 bis 000199 zu finden, verwenden Sie: $filter=(PartitionKey eq 'Vertrieb') und (RowKey ge 'empid_000100') und (RowKey le 'empid_000199')  
* Um alle Mitarbeiter in der Vertriebsabteilung mit einer E-Mail-Adresse zu finden, die mit dem Buchstaben "a" beginnt, verwenden Sie: $filter=(PartitionKey eq 'Sales') und (RowKey ge 'email_a') und (RowKey lt 'email_b')  
  
  Die in den obigen Beispielen verwendete Filtersyntax stammt aus der REST-API des Tabellenspeicherdiensts. Weitere Informationen finden Sie unter [Abfragen von Entitäten](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Der Tabellenspeicher ist relativ günstig zu verwenden, sodass der Kostenaufwand für das Speichern von redundanten Daten kein wichtiger Faktor sein sollte. Sie sollten jedoch immer die Kosten für den Entwurf bewerten, basierend auf dem angenommenen Speicherbedarf und nur doppelte Entitäten hinzufügen, um die Abfragen zu unterstützen, welche die Clientanwendung ausführen wird.  
* Da die sekundären Index-Entitäten in derselben Partition wie die ursprünglichen Entitäten gespeichert werden, sollten Sie sicherstellen, dass Sie die Skalierbarkeitsziele für eine einzelne Partition nicht überschreiten.  
* Sie können die doppelten Entitäten zueinander konsistent halten mithilfe von EGTs für die automatische Aktualisierung der beiden Kopien der Entität. Dies bedeutet, dass alle Kopien einer Entität in der gleichen Partition gespeichert werden sollten. Weitere Informationen finden Sie im Abschnitt [Verwenden von Entitätsgruppentransaktionen](table-storage-design.md#entity-group-transactions).  
* Der Wert, der für **RowKey** verwendet wird, muss für jede Entität eindeutig sein. Berücksichtigen Sie die Verwendung von zusammengesetzten Schlüsselwerten.  
* Das Auffüllen der numerischen Werte in **RowKey** (etwa bei der Mitarbeiter-ID „000223“) ermöglicht die korrekte Sortierung und Filterung anhand einer Ober- und Untergrenze.  
* Sie müssen nicht unbedingt alle Eigenschaften der Entität duplizieren. Beispiel: Wenn die Abfragen, mit denen die Entitäten anhand der E-Mail-Adresse in **RowKey** gesucht werden, nie das Alter des Mitarbeiters benötigen, könnten diese Entitäten die folgende Struktur aufweisen:

   ![Struktur für Mitarbeiterentitäten](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Es empfiehlt sich, in der Regel doppelte Daten zu speichern und sicherzustellen, dass Sie alle benötigten Daten mit einer einzigen Abfrage abrufen können, anstatt eine Abfrage zum Finden einer Entität und eine weitere zum Suchen nach den benötigten Daten durchzuführen.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Ihre Clientanwendung Entitäten abrufen muss, die eine Vielzahl verschiedener Schlüssel verwenden, wenn Ihr Client Entitäten in unterschiedlichen Sortierreihenfolgen abrufen muss, und wo Sie jede Entität unter Verwendung einer Vielzahl von eindeutigen Werten identifizieren können. Allerdings sollten Sie sicher sein, dass die Skalierungsgrenzen der Partition nicht überschritten werden, wenn Sie eine Suche nach Entitäten unter Verwendung der verschiedenen **RowKey** -Werte durchführen.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Sekundäres Indexmuster für Inter-Partition](#inter-partition-secondary-index-pattern)
* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)
* Entitätsgruppentransaktionen
* [Arbeiten mit heterogenen Entitätstypen](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Sekundäres Indexmuster für Inter-Partition
Das Speichern mehrerer Kopien der einzelnen Entitäten unter Verwendung verschiedener **RowKey**-Werte in separaten Partitionen oder in separaten Tabellen ermöglicht schnelle und effiziente Suchvorgänge und alternative Sortierreihenfolgen mit anderen **RowKey**-Werten.  

### <a name="context-and-problem"></a>Kontext und Problem
Der Tabellenspeicherdienst indiziert automatisch Entitäten mit den **PartitionKey**- und **RowKey**-Werten. Dadurch wird eine Client-Anwendung in die Lage versetzt, eine Entität effizient unter Verwendung dieser Werte abzurufen. Beispiel: Durch Verwendung der unten gezeigten Tabellenstruktur kann eine Clientanwendung eine Punktabfrage verwenden, um eine einzelne Mitarbeiterentität mit dem Abteilungsnamen und der Mitarbeiter-ID abzurufen (Werte **PartitionKey** und **RowKey**). Ein Client kann auch Entitäten abrufen, die nach Mitarbeiter-ID in jeder Abteilung sortiert sind.  

![Mitarbeiter-ID](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Wenn Sie auch eine Mitarbeiterentität finden möchten, die auf dem Wert einer anderen Eigenschaft basiert, wie z. B. die E-Mail-Adresse, müssen Sie einen weniger effizienten Partition-Scan verwenden, um eine Übereinstimmung zu finden. Der Grund ist, dass der Tabellenspeicherdienst keine sekundären Indizes bietet. Darüber hinaus steht keine Option zum Anfordern einer Liste der Mitarbeiter zur Verfügung, die in einer anderen Reihenfolge als in der **RowKey**-Reihenfolge sortiert ist.  

Sie werden eine große Anzahl von Transaktionen für diese Entitäten abschätzen und möchten das Risiko minimieren, dass der Tabellenspeicherdienst Ihren Client drosselt.  

### <a name="solution"></a>Lösung
Zur Umgehung fehlender sekundärer Indizes können Sie mehrere Kopien der einzelnen Entitäten mit jeder Kopie verwenden, indem Sie unterschiedliche **PartitionKey**- und **RowKey**-Werte verwenden. Wenn Sie eine Entität mit den unten gezeigten Strukturen speichern, können Sie Mitarbeiterentitäten auf Grundlage der E-Mail-Adresse oder Mitarbeiter-ID effizient abrufen. Mithilfe der Präfixwerte für **PartitionKey** („empid_“ und „email_“) können Sie den gewünschten Index für eine Abfrage identifizieren.  

![Primärer und sekundärer Index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Die beiden folgenden Filterkriterien (eine Suche nach Mitarbeiter-ID und eine Suche nach E-Mail-Adresse) definieren jeweils eine Punktabfrage:  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') and (RowKey eq 'jonesj@contoso.com')  

Bei einer Abfrage nach einem Bereich von Mitarbeiterentitäten können Sie einen Bereich festlegen, der entweder nach Mitarbeiter-ID oder nach E-Mail-Adresse sortiert ist, indem Sie die Entitäten mit dem entsprechenden Präfix in **RowKey** abfragen.  

* Verwenden Sie Folgendes, um alle Mitarbeiter in der Vertriebsabteilung mit einer Mitarbeiter-ID im Bereich von **000100** bis **000199** zu suchen und nach Mitarbeiter-ID zu sortieren: $filter=(PartitionKey eq 'empid_Vertrieb') und (RowKey ge '000100') und (RowKey le '000199').  
* Um alle Mitarbeiter in der Vertriebsabteilung mit einer E-Mail-Adresse zu finden, die mit dem Buchstaben "a" beginnt, und die in E-Mail-Adressen-Reihenfolge sortiert sind, verwenden Sie: $filter=(PartitionKey eq 'email_Sales') und (RowKey ge 'a') und (RowKey lt 'b')  

Die in den obigen Beispielen verwendete Filtersyntax stammt aus der REST-API des Tabellenspeicherdiensts. Weitere Informationen finden Sie unter [Abfragen von Entitäten](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Sie können die doppelten Entitäten "Eventually Consistent" halten, indem Sie das [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern) verwenden, um die primären und sekundären Indexentitäten zu verwalten.  
* Der Tabellenspeicher ist relativ günstig zu verwenden, sodass der Kostenaufwand für das Speichern von redundanten Daten kein wichtiger Faktor sein sollte. Sie sollten jedoch immer die Kosten für den Entwurf bewerten, basierend auf dem angenommenen Speicherbedarf und nur doppelte Entitäten hinzufügen, um die Abfragen zu unterstützen, welche die Clientanwendung ausführen wird.  
* Der Wert, der für **RowKey** verwendet wird, muss für jede Entität eindeutig sein. Berücksichtigen Sie die Verwendung von zusammengesetzten Schlüsselwerten.  
* Das Auffüllen der numerischen Werte in **RowKey** (etwa bei der Mitarbeiter-ID „000223“) ermöglicht die korrekte Sortierung und Filterung anhand einer Ober- und Untergrenze.  
* Sie müssen nicht unbedingt alle Eigenschaften der Entität duplizieren. Beispiel: Wenn die Abfragen, mit denen die Entitäten anhand der E-Mail-Adresse in **RowKey** gesucht werden, nie das Alter des Mitarbeiters benötigen, könnten diese Entitäten die folgende Struktur aufweisen:
  
   ![Mitarbeiterentität (sekundärer Index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Es ist in der Regel besser, doppelte Daten zu speichern und sicherzustellen, dass Sie alle benötigten Daten mit einer einzigen Abfrage abrufen können, anstatt eine Abfrage zum Finden einer Entität unter Verwendung des sekundären Indexes und eine weitere Abfrage zum Suchen nach den benötigten Daten im primären Index durchzuführen.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Ihre Clientanwendung Entitäten abrufen muss, die eine Vielzahl verschiedener Schlüssel verwenden, wenn Ihr Client Entitäten in unterschiedlichen Sortierreihenfolgen abrufen muss, und wo Sie jede Entität unter Verwendung einer Vielzahl von eindeutigen Werten identifizieren können. Verwenden Sie dieses Muster, wenn Sie ein Überschreiten der Skalierungsgrenzen der Partition verhindern möchten, wenn Sie Entitätssuchvorgänge mit den verschiedenen **RowKey** -Werten ausführen.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  
* [Sekundäres Indexmuster für Intra-Partition](#intra-partition-secondary-index-pattern)  
* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* Entitätsgruppentransaktionen  
* [Arbeiten mit heterogenen Entitätstypen](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Eventual Consistency-Transaktionsmuster
Aktivieren Sie Eventual Consistency über Partitions- oder Speichersystemgrenzen hinweg, indem Sie Azure-Warteschlangen verwenden.  

### <a name="context-and-problem"></a>Kontext und Problem
EGTs ermöglichen atomische Transaktionen über mehrere Entitäten, die den gleichen Partitionsschlüssel gemeinsam nutzen. Aus Gründen der Leistung und Skalierbarkeit möchten Sie Entitäten speichern, die Anforderungen an die Datenkonsistenz in separaten Partitionen oder in einem separaten Speichersystem haben. In diesem Fall können keine EGTs zur Gewährleistung der Konsistenz verwendet werden. Beispiel: Sie haben eine Anforderung, bei der in den folgenden Fällen Eventual Consistency beibehalten werden muss:  

* Entitäten, die in zwei verschiedenen Partitionen in derselben Tabelle, in verschiedenen Tabellen oder in verschiedenen Speicherkonten gespeichert sind.  
* Eine Entität, die im Tabellenspeicherdienst gespeichert ist und einen Blob, der im Blob-Dienst gespeichert ist.  
* Eine Entität, die im Tabellenspeicherdienst gespeichert ist und eine Datei in einem Dateisystem.  
* Speicherung einer Entität im Tabellenspeicherdienst, der jedoch mithilfe des Azure Cognitive Search-Dienstes indiziert ist.  

### <a name="solution"></a>Lösung
Mithilfe von Azure-Warteschlangen können Sie eine Lösung implementieren, die Eventual Consistency über zwei oder mehr Partitionen oder Speichersysteme bietet.
Zur Veranschaulichung dieses Ansatzes gehen Sie davon aus, dass Sie eine Anforderung haben, veraltete Mitarbeiterentitäten archivieren zu können. Alte Mitarbeiterentitäten werden selten abgefragt und sollten aus allen Aktivitäten ausgeschlossen werden, die sich mit den aktuellen Mitarbeitern beschäftigen. Um diese Anforderung zu implementieren, speichern Sie die aktiven Mitarbeiter in der Tabelle **Aktuell** und ehemalige Mitarbeiter in der Tabelle **Archiv**. Zum Archivieren eines Mitarbeiters löschen Sie die Entität aus der Tabelle **Aktuell** und fügen die Entität der Tabelle **Archiv** hinzu. Für diese beiden Vorgänge kann allerdings keine EGT verwendet werden. Um das Risiko zu vermeiden, das aufgrund eines Fehlers eine Entität in beiden oder in keiner Tabelle angezeigt wird, muss der Archivierungsvorgang letztendlich Eventual Consistency aufweisen. Das folgende Sequenzdiagramm beschreibt die Schritte bei diesem Vorgang. Mehr Details zu Ausnahmepfaden werden im folgenden Text zur Verfügung gestellt.  

![Lösung mit Azure-Warteschlangen](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Ein Client startet den Archiv-Vorgang, indem er eine Meldung auf einer Azure-Warteschlange ablegt. In diesem Beispiel wird Mitarbeiter #456 archiviert. Eine Workerrolle fragt die Warteschlange auf neue Meldungen ab. Wird eine gefunden, liest sie die Meldung und hinterlässt eine verborgene Kopie in der Warteschlange. Die Workerrolle ruft als Nächstes eine Kopie der Entität aus der Tabelle **Aktuell** ab, fügt eine Kopie in die Tabelle **Archiv** ein und löscht dann die ursprüngliche Entität aus der Tabelle **Aktuell**. Falls keine Fehler in den vorangegangenen Schritten aufgetreten sind, löscht die Workerrolle schlussendlich die verborgene Nachricht aus der Warteschlange.  

In diesem Beispiel fügt Schritt 4 den Mitarbeiter in die Tabelle **Archiv** ein. Es könnte den Mitarbeiter einem Blob im Blob-Dienst oder einer Datei in einem Dateisystem hinzufügen.  

### <a name="recovering-from-failures"></a>Wiederherstellung bei Fehlern
Für den Fall, dass die Workerrolle den Archivierungsvorgang neu starten muss, müssen die Vorgänge in den Schritten **4** und **5** *idempotent* sein. Bei Verwendung des Tabellenspeicherdiensts müssen Sie für Schritt **4** einen Vorgang vom Typ „Einfügen oder Ersetzen“ und für Schritt **5** in der verwendeten Clientbibliothek einen Vorgang vom Typ „Löschen, falls vorhanden“ ausführen. Wenn Sie ein anderes Speichersystem verwenden, müssen Sie einen entsprechenden idempotenten Vorgang verwenden.  

Wenn die Workerrolle Schritt **6** niemals abschließt, erscheint nach einem Timeout die Meldung wieder in der Warteschlange, damit die Workerrolle versuchen kann, sie erneut zu verarbeiten. Die Workerrolle kann überprüfen, wie oft eine Meldung in der Warteschlange gelesen wurde und bei Bedarf markieren, dass dies eine "unzustellbare" Meldung ist und sie zur Untersuchung an eine eigene Warteschlange senden. Weitere Informationen zum Lesen von Warteschlangenmeldungen und zur Überprüfung des Zählers für Entfernungsvorgänge aus der Warteschlange finden Sie unter [Get Messages](/rest/api/storageservices/Get-Messages).  

Einige Fehler aus der Tabelle und den Warteschlangendiensten sind vorübergehende Fehler. Ihre Clientanwendung sollte diese über eine geeignete Wiederholungslogik bedienen können.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Diese Lösung bietet keine Transaktionsisolation. Dadurch kann beispielsweise ein Client die Tabellen **Aktuell** und **Archiv** lesen, wenn sich die Workerrolle zwischen den Schritten **4** und **5** befindet, und eine inkonsistente Darstellung der Daten erhalten. Die Daten sind irgendwann konsistent.  
* Sie müssen darauf achten, dass die Schritte 4 und 5 idempotent sind, um Eventual Consistency zu gewährleisten.  
* Sie können die Lösung skalieren, indem Sie mehrerer Warteschlangen und Workerrollen-Instanzen verwenden.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, um Eventual Consistency zwischen Entitäten zu gewährleisten, die in verschiedenen Partitionen oder Tabellen vorhanden sind. Sie können dieses Muster zur Gewährleistung von Eventual Consistency für alle Vorgänge zum Tabellenspeicherdienst, Blob-Dienst und andere Nicht-Azure Tabellenspeicher-Datenquellen wie Datenbank oder Dateisystem verwenden.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* Entitätsgruppentransaktionen  
* [Zusammenführen oder ersetzen](#merge-or-replace)  

> [!NOTE]
> Falls Transaktionsisolation für Ihre Lösung wichtig ist, sollten Sie einen Neuentwurf Ihrer Tabellen erwägen, damit Sie EGTs verwenden können.  
> 
> 

## <a name="index-entities-pattern"></a>Muster für Indexentitäten
Verwalten von Index-Entitäten, um effiziente Suchvorgänge zu ermöglichen, die Listen mit Entitäten zurückgeben.  

### <a name="context-and-problem"></a>Kontext und Problem
Der Tabellenspeicherdienst indiziert automatisch Entitäten mit den **PartitionKey**- und **RowKey**-Werten. Dadurch kann eine Clientanwendung, eine Entität effizient mithilfe einer Punktabfrage abrufen. Beispiel: Durch Verwendung der unten gezeigten Tabellenstruktur kann eine Clientanwendung eine einzelne Mitarbeiterentität effizient abrufen, indem der Abteilungsname und die Mitarbeiter-ID verwendet werden (**PartitionKey** und **RowKey**).  

![Mitarbeiterentität](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Wenn Sie auch in der Lage sein möchten, eine Liste der Mitarbeiterentitäten abzurufen, die auf dem Wert einer anderen nicht eindeutigen Eigenschaft basiert, z. B. Nachname, müssen Sie einen weniger effizienten Partition-Scan verwenden, um Übereinstimmungen zu finden, anstatt einen Index zum direkt Nachschlagen zu verwenden. Der Grund ist, dass der Tabellenspeicherdienst keine sekundären Indizes bietet.  

### <a name="solution"></a>Lösung
Um die Suche nach dem Nachnamen mit der oben dargestellten Entitätsstruktur zu aktivieren, müssen Sie die Listen der Mitarbeiter-IDs verwalten. Wenn Sie die Mitarbeiterentitäten mit einem bestimmten Nachnamen aufrufen möchten, z.B. Jones, müssen Sie zuerst die Liste der Mitarbeiter-IDs für die Mitarbeiter finden, deren Nachname Jones ist und dann diese Mitarbeiterentitäten abrufen. Es gibt drei wichtige Optionen zur Speicherung der Listen mit den Mitarbeiter-IDs:  

* Verwenden von Blob-Speicher.  
* Erstellen von Index-Entitäten in der gleichen Partition wie für die Mitarbeiterentitäten.  
* Erstellen von Index-Entitäten in einer separaten Partition oder Tabelle.  

<u>Option 1: Verwenden von Blobspeicher</u>  

Für die erste Option erstellen Sie ein Blob für jeden eindeutigen Nachnamen und speichern in jedem Blob eine Liste mit den Werten **PartitionKey** (Abteilung) und **RowKey** (Mitarbeiter-ID) für die Mitarbeiter mit diesem Nachnamen. Beim Hinzufügen oder Löschen eines Mitarbeiters sollten Sie sicherstellen, dass der Inhalt des relevanten Blob schließlich konsistent mit den Mitarbeiterentitäten ist.  

<u>Option 2:</u> Erstellen von Indexentitäten in der gleichen Partition  

Verwenden Sie für die zweite Option die Index-Entitäten, die folgende Daten speichern:  

![Mitarbeiterindexentität](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Die Eigenschaft **EmployeeIDs** enthält eine Liste der Mitarbeiter-IDs für Mitarbeiter, deren Nachname in **RowKey** gespeichert ist.  

Wenn Sie die zweite Option verwenden, beschreiben die folgenden Schritte den Prozess, den Sie befolgen sollten, wenn Sie einen neuen Mitarbeiter hinzufügen. In diesem Beispiel werden wir einen Mitarbeiter mit der ID 000152 und einen Nachnamen Jones in der Vertriebsabteilung hinzufügen:  

1. Rufen Sie die Indexentität mit dem **PartitionKey**-Wert „Sales“ und dem **RowKey**-Wert „Jones“ ab. Speichern Sie das ETag der Entität, die in Schritt 2 verwendet wird.  
2. Erstellen Sie eine Entitätsgruppentransaktion (also einen Batchvorgang), mit der die neue Mitarbeiterentität eingefügt wird (**PartitionKey**= „Vertrieb“ und **RowKey** = „000152“), und aktualisieren Sie die Indexentität (**PartitionKey** = „Vertrieb“ und **RowKey** = „Jones“) durch Hinzufügen der neuen Mitarbeiter-ID zur Liste im Feld „EmployeeIDs“. Weitere Informationen zu Entitätsgruppentransaktionen finden Sie unter „Entitätsgruppentransaktionen“.  
3. Falls die Entitätsgruppentransaktion aufgrund eines Fehlers der vollständigen Nebenläufigkeit (jemand hat gerade die Indexentität geändert) nicht erfolgreich ist, müssen Sie erneut mit Schritt 1 beginnen.  

Wenn Sie die zweite Option verwenden, können Sie einen ähnlichen Ansatz beim Löschen eines Mitarbeiters wählen. Das Ändern des Nachnamen des Mitarbeiters ist etwas komplexer, da Sie eine Entitätsgruppentransaktion ausführen müssen, die drei Entitäten aktualisiert: die Mitarbeiterentität, die Indexentität für den alten Nachnamen und die Indexentität für den neuen Nachnamen. Sie müssen jede Entität abrufen, bevor Sie Änderungen vornehmen, um die ETag-Werte abzurufen, mit denen Sie dann die Updates unter Verwendung von optimistischer Nebenläufigkeit ausführen.  

Wenn Sie die zweite Option verwenden, beschreiben die folgenden Schritte den Prozess, den Sie befolgen sollten, wenn Sie alle Mitarbeiter mit einem bestimmten Nachnamen in einer Abteilung nachschlagen. In diesem Beispiel schlagen wir alle Mitarbeiter in der Vertriebsabteilung mit dem Nachnamen Jones nach:  

1. Rufen Sie die Indexentität mit dem **PartitionKey**-Wert „Sales“ und dem **RowKey**-Wert „Jones“ ab.  
2. Analysieren Sie die Liste der Mitarbeiter-IDs im Feld EmployeeIDs.  
3. Falls Sie zusätzliche Informationen zu den einzelnen Mitarbeitern benötigen (etwa ihre E-Mail-Adressen), rufen Sie die einzelnen Mitarbeiterentitäten mit dem **PartitionKey**-Wert „Sales“ und den **RowKey**-Werten aus der Liste der Mitarbeiter ab, die Sie in Schritt 2 abgerufen haben.  

<u>Option 3:</u> Erstellen von Indexentitäten in einer separaten Partition oder Tabelle  

Verwenden Sie für die dritte Option Index-Entitäten, die folgende Daten speichern:  

![Mitarbeiterindexentität in einer separaten Partition](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Die Eigenschaft **EmployeeIDs** enthält eine Liste der Mitarbeiter-IDs für Mitarbeiter, deren Nachname in **RowKey** gespeichert ist.  

Mit der dritten Option können Sie keine EGTs zur Aufrechterhaltung der Konsistenz verwenden, da sich die Index-Entitäten in einer anderen Partition wie die Mitarbeiterentitäten befinden. Stellen Sie sicher, dass die Index-Entitäten Eventual Consistency mit den Mitarbeiterentitäten aufweisen.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Diese Lösung erfordert mindestens zwei Abfragen zum Abrufen von übereinstimmenden Entitäten: eine zur Abfrage der Indexentitäten, um eine Liste mit **RowKey** -Werten zu erhalten, und dann Abfragen zum Abruf jeder Entität aus der Liste.  
* Angesichts der Tatsache, dass eine einzelne Entität eine Maximalgröße von 1 MB hat, wird bei den Optionen 2 und 3 der Lösungen angenommen, dass die Liste der Mitarbeiter-IDs für einen angegebenen Nachnamen nie größer als 1 MB ist. Wenn die Liste der Mitarbeiter-IDs aller Voraussicht nach größer als 1 MB wird, verwenden Sie die Option Nr. 1 und speichern Sie die Indexdaten im Blobspeicher.  
* Bei Verwendung der Option Nr. 2 (Verwendung von EGTs zum Hinzufügen und Löschen von Mitarbeitern und Ändern des Nachnamens eines Mitarbeiters) müssen Sie bewerten, ob die Transaktionsmenge sich den Skalierungsgrenzen in einer gegebenen Partition nähert. Wenn dies der Fall ist, sollten Sie eine Lösung mit Eventual Consistency (Option Nr. 1 oder Nr. 3) erwägen, die Warteschlangen zur Behandlung der Update-Anforderungen verwendet und Ihnen das Speichern Ihrer Index-Entitäten in einer von den Mitarbeiterentitäten separaten Partition ermöglicht.  
* In Option Nr. 2 in dieser Lösung wird davon ausgegangen, dass Sie innerhalb einer Abteilung nach Nachnamen nachschlagen möchten: z. B. eine Liste von Mitarbeitern abrufen mit einem Nachnamen Jones in der Vertriebsabteilung. Wenn Sie eine Suche aller Mitarbeiter mit einem Nachnamen Jones in der gesamten Organisation durchführen möchten, verwenden Sie entweder die Option Nr. 1 oder Nr. 3.
* Sie können eine auf einer Warteschlange basierende Lösung implementieren, die letztliche Konsistenz bietet (weitere Details finden Sie unter [Eventual Consistency-Transaktionsmuster](#eventually-consistent-transactions-pattern) ).  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine Reihe von Entitäten nachschlagen möchten, die einen gemeinsamen Eigenschaftswert haben, z.B. alle Mitarbeiter mit dem Nachnamen Jones.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  
* Entitätsgruppentransaktionen  
* [Arbeiten mit heterogenen Entitätstypen](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalisierungsmuster
Kombinieren Sie zugehörige Daten zusammen in eine einzelne Entität, mit der Sie alle Daten abrufen, die Sie mit einer einzelnen Abfrage benötigen.  

### <a name="context-and-problem"></a>Kontext und Problem
In einer relationalen Datenbank normalisieren Sie typischerweise Daten, um Duplizierungen zu entfernen, die zu Abfragen führen, die Daten aus mehreren Tabellen abrufen. Wenn Sie Ihre Daten in Azure-Tabellen normalisieren, müssen Sie mehrere Roundtrips vom Client zum Server durchlaufen, um Ihre zugeordneten Daten abzurufen. Beispiel: Bei der folgenden Tabellenstruktur benötigen Sie zwei Roundtrips zum Abrufen der Details für eine Abteilung. Mit dem ersten Roundtrip wird die Abteilungsentität mit der Manager-ID abgerufen. Mit dem zweiten werden die Details des Managers aus einer Mitarbeiterentität angefordert.  

![Abteilungsentität und Mitarbeiterentität](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Lösung
Anstatt die Daten in zwei separaten Entitäten zu speichern, denormalisieren Sie die Daten und bewahren eine Kopie der Details des Managers in der Abteilungsentität auf. Beispiel:  

![Abteilungsentität](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Sie können von Abteilungsentitäten, die mit diesen Eigenschaften gespeichert sind, jetzt alle benötigten Details zu einer Abteilung abrufen, indem Sie eine Punktabfrage verwenden.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Im Zusammenhang mit dem zweifachen Speichern von Daten fallen Mehrkosten an. Der Leistungsvorteil (der sich aus weniger Anforderungen an den Speicherdienst ergibt) überwiegt in der Regel die marginal höheren Speicherkosten (und diese Kosten werden teilweise durch eine Reduzierung der Anzahl der Transaktionen ausgeglichen, die Sie benötigen, um die Details einer Abteilung abrufen).  
* Sie müssen die Konsistenz der beiden Entitäten verwalten, die Informationen über Manager speichern. Sie können das Konsistenzproblem behandeln, indem Sie EGTs zum Aktualisieren von mehreren Entitäten in einer einzelnen atomischen Transaktion verwenden. In diesem Fall werden die Abteilungsentität und die Mitarbeiterentität für den Manager der Abteilung in der gleichen Partition gespeichert.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie häufig nach zugeordneten Informationen suchen müssen. Dieses Muster reduziert die Anzahl der Abfragen, die Ihr Client vornehmen muss, um die benötigten Daten abzurufen.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* Entitätsgruppentransaktionen  
* [Arbeiten mit heterogenen Entitätstypen](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Zusammengesetzte Schlüsselmuster
Verwenden Sie zusammengesetzte **RowKey** -Werte, damit der Client in der Lage ist, mit einer einzelnen Punktabfrage nach verknüpften Daten zu suchen.  

### <a name="context-and-problem"></a>Kontext und Problem
In einer relationalen Datenbank ist es natürlich, Verknüpfungen in Abfragen zu verwenden, um zugehörige Datenelemente in einer einzelnen Abfrage an den Client zurückzugeben. Beispiel: Sie können die Mitarbeiter-ID verwenden, um in einer Liste von verknüpften Entitäten zu suchen, die Leistungs- und Review-Daten für diesen Mitarbeiter enthalten.  

Angenommen, Sie speichern Mitarbeiterentitäten im Tabellenspeicherdienst mithilfe der folgenden Struktur:  

![Screenshot, der zeigt, wie Sie Mitarbeiterentitäten im Tabellenspeicherdienst speichern können](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Sie müssen auch Vergangenheitsdaten speichern, die im Zusammenhang mit Überprüfungen und Leistung für jedes Jahr, in dem der Mitarbeiter für Ihre Organisation gearbeitet hat, stehen und Sie müssen auf diese Informationen nach Jahr zugreifen können. Eine Möglichkeit ist, eine weitere Tabelle zu erstellen, in der Entitäten mit der folgenden Struktur gespeichert werden:  

![Alternative Struktur für Mitarbeiterentitäten](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Beachten Sie, dass Sie bei diesem Ansatz entscheiden könnten, dass Sie einige Informationen (z. B. Vorname und Nachname) in die neue Entität duplizieren, um zu ermöglichen, dass Sie Ihre Daten mit einer einzelnen Anforderung abrufen. Sie können jedoch keine starke Konsistenz beibehalten, da Sie keine EGT verwenden können, die beide Entitäten nicht trennbar aktualisiert.  

### <a name="solution"></a>Lösung
Speichern Sie einen neuen Entitätstyp in der ursprünglichen Tabelle unter Verwendung von Entitäten mit der folgenden Struktur ab:  

![Lösung für Struktur für Mitarbeiterentitäten](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Beachten Sie, dass **RowKey** jetzt ein Schlüssel ist, der sich aus der Mitarbeiter-ID und dem Jahr der Review-Daten zusammensetzt und der es Ihnen ermöglicht, die Leistungs- und Review-Daten des Mitarbeiters mit einer einzigen Anforderung für eine einzelne Entität abzurufen.  

Im folgende Beispiel wird erläutert, wie Sie alle Review-Daten für einen bestimmten Mitarbeiter (z. B. Mitarbeiter 000123 in der Vertriebsabteilung) abrufen können:  

$filter=(PartitionKey eq 'Sales') und (RowKey ge 'empid_000123') und (RowKey lt '000123_2012')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Sie sollten ein geeignetes Trennzeichen verwenden, um die Analyse des **RowKey**-Werts zu erleichtern (beispielsweise **000123_2012**).  
* Sie speichern diese Entität auch in der gleichen Partition wie andere Entitäten mit verknüpften Daten für denselben Mitarbeiter, was bedeutet, dass Sie EGTs verwenden können, um starke Konsistenz zu gewährleisten.
* Sie sollten berücksichtigen, wie oft Sie die Daten abfragen, um zu bestimmen, ob dieses Muster geeignet ist.  Beispiel: Wenn Sie auf die Review-Daten selten und auf die wichtigsten Mitarbeiterdaten häufig zugreifen, sollten Sie diese als separate Entitäten beibehalten.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine oder mehrere Entitäten, die Sie häufig abfragen, speichern müssen.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* Entitätsgruppentransaktionen  
* [Arbeiten mit heterogenen Entitätstypen](#working-with-heterogeneous-entity-types)  
* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Log Tail-Muster
Abrufen der *n* Entitäten, die zuletzt einer Partition hinzugefügt wurden, indem Sie einen **RowKey** -Wert verwenden, mit dem nach Datum und Uhrzeit in umgekehrter Reihenfolge sortiert wird.  

### <a name="context-and-problem"></a>Kontext und Problem
Eine gängige Anforderung ist, die zuletzt erstellten Entitäten abzurufen, z.B. die letzten 10 Kostenabrechnungen, die von einem Mitarbeiter übermittelt wurden. Tabellenabfragen unterstützen einen **$top**-Abfragevorgang, um die ersten *n* Entitäten einer Menge zurückzugeben. Es gibt keinen entsprechenden Abfragevorgang, mit dem die letzten n Entitäten einer Menge zurückgegeben werden können.  

### <a name="solution"></a>Lösung
Speichern von Entitäten unter Verwendung eines **RowKey**-Werts, der von Natur aus in umgekehrter Datum-/Zeit-Reihenfolge sortiert wird. Damit ist der neueste Eintrag immer der erste Eintrag in der Tabelle.  

Beispiel: Um die 10 neuesten Kostenabrechnungen, die von einem Mitarbeiter übermittelt wurden, abrufen zu können, können Sie einen umgekehrten Tick-Wert verwenden, der aus dem aktuellen Wert für Datum/Uhrzeit abgeleitet ist. Das folgende C#-Codebeispiel zeigt eine Möglichkeit, einen geeigneten "invertierten Ticks"-Wert für einen **RowKey** zu erstellen, der von der neuesten bis zur ältesten Information sortiert:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Sie können wieder zum Wert Datum/Uhrzeit mithilfe des folgenden Codes zurückkehren:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Die Tabellenabfrage sieht folgendermaßen aus:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Sie müssen den umgekehrten Tick-Wert mit führenden Nullen auffüllen, um sicherzustellen, dass der Zeichenfolgewert wie erwartet sortiert.  
* Sie müssen die Skalierbarkeitsziele auf der Ebene einer Partition kennen. Vorsicht, keine Hotspot-Partitionen erstellen.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie auf Entitäten in umgekehrter Datum/Uhrzeit-Reihenfolge oder auf die zuletzt hinzugefügten Entitäten zugreifen müssen.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Voranstellen / Anfügen Antimuster](#prepend-append-anti-pattern)  
* [Abrufen von Entitäten](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Muster für das Löschen hoher Volumen
Aktivieren Sie das Löschen einer hohen Anzahl von Entitäten, indem Sie alle Entitäten zum gleichzeitigen Löschen in ihrer eigenen separaten Tabelle speichern. Löschen Sie die Entitäten durch Löschen der Tabelle.  

### <a name="context-and-problem"></a>Kontext und Problem
Viele Anwendungen löschen alte Daten, die für eine Clientanwendung nicht mehr verfügbar sein müssen oder von der Anwendung auf einem anderen Speichermedium archiviert wurden. Sie kennzeichnen solche Daten in der Regel mit einem Datum. Sie haben z. B. eine Anforderung zum Löschen von Datensätzen aller Anmeldeanforderungen, die älter 60 Tage sind.  

Ein möglicher Entwurf ist die Verwendung von Datum und Uhrzeit der Anmeldeanforderung in **RowKey**:  

![Datum und Uhrzeit des Anmeldeversuchs](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Dieser Ansatz vermeidet Partition-Hotspots, da die Anwendung Anmeldeentitäten für jeden Benutzer in eine separate Partition einfügen und löschen kann. Allerdings kann dieser Ansatz teuer und zeitaufwendig sein, wenn die Anzahl Ihrer Entitäten groß ist, da Sie zunächst einen Tabellen-Scan ausführen müssen, um die zu löschenden Entitäten zu identifizieren. Danach müssen Sie jede alte Entität löschen. Sie können die Anzahl der Roundtrips zum Server reduzieren, die zum Löschen der alten Entitäten notwendig sind, indem Sie mehrere Löschanforderungen in EGTs stapeln.  

### <a name="solution"></a>Lösung
Verwenden Sie für Anmeldeversuche jeden Tag eine separate Tabelle. Sie können den oben genannten Entitätsentwurf verwenden, um beim Einfügen von Entitäten Hotspots zu vermeiden. Löschen von alten Entitäten ist jetzt einfach eine Frage des Löschens von einer Tabelle pro Tag (ein einzelner Speichervorgang), statt jeden Tag Hunderte und Tausende von individuellen Anmeldeentitäten zu suchen und zu löschen.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Unterstützt der Entwurf Ihrer Anwendung andere Möglichkeiten einer Datenverwendung, z. B. die Suche nach bestimmten Entitäten, die Verknüpfung mit anderen Daten oder das Generieren von zusammengefassten Informationen?  
* Vermeidet Ihren Entwurf Hotspots, wenn Sie neue Entitäten einfügen?  
* Rechnen Sie mit einer Verzögerungszeit, wenn Sie denselben Tabellennamen nach dem Löschen wiederverwenden möchten. Es empfiehlt sich, immer einen eindeutigen Tabellennamen zu verwenden.  
* Rechnen Sie mit einer Drosselung, wenn Sie zuerst eine neue Tabelle verwenden, während der Dienst die Zugriffsmuster lernt und die Partitionen auf den Knoten verteilt. Sie sollten berücksichtigen, wie häufig Sie neue Tabellen erstellen müssen.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine große Anzahl von Entitäten haben, die gleichzeitig gelöscht werden müssen.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* Entitätsgruppentransaktionen
* [Ändern von Entitäten](#modifying-entities)  

## <a name="data-series-pattern"></a>Datenreihenmuster
Speichern Sie vollständige Datenreihen in einer einzelnen Entität, um die Anzahl der von Ihnen vorgenommenen Anforderungen zu minimieren.  

### <a name="context-and-problem"></a>Kontext und Problem
Ein häufiges Szenario für eine Anwendung ist, eine Datenreihe zu speichern, die in der Regel auf einmal abgerufen werden muss. Beispiel: Ihre Anwendung erfasst, wie viele Chatnachrichten jeder Mitarbeiter pro Stunde sendet. Diese Informationen verwenden Sie dann, um zu zeichnen, wie viele Meldungen jeder Benutzer in den vergangenen 24 Stunden gesendet hat. Ein Entwurf könnte sein, für jeden Mitarbeiter 24 Entitäten zu speichern:  

![Speichern von 24 Entitäten für jeden Mitarbeiter](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Bei diesem Entwurf können Sie problemlos die Entität finden und aktualisieren, um sie für jeden Mitarbeiter immer dann zu aktualisieren, wenn die Anwendung den Meldungszählerwert aktualisieren muss. Allerdings müssen Sie zum Abrufen der Informationen, mit der ein Diagramm der Aktivität für die vergangenen 24 Stunden zu zeichnen ist, 24 Entitäten abrufen.  

### <a name="solution"></a>Lösung
Verwenden Sie den folgenden Entwurf mit einer separaten Eigenschaft,um den Meldungszähler für jede Stunde zu speichern:  

![Entität für Nachrichtenstatistik](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Bei diesem Entwurf können Sie mit einem Zusammenführungsvorgang den Meldungszähler für einen Mitarbeiter für eine bestimmte Stunde aktualisieren. Nun können Sie alle Informationen, die Sie zum Zeichnen des Diagramms benötigen, mit einer Anforderung für eine einzelne Entität abrufen.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Wenn eine vollständige Datenreihe nicht in eine einzelne Entität passt (eine Entität kann bis zu 252 Eigenschaften haben), verwenden Sie einen alternativen Datenspeicher, z. B einen Blob.  
* Wenn bei Ihnen mehrere Clients gleichzeitig eine Entität aktualisieren, müssen Sie **ETag** verwenden, um vollständige optimistische Nebenläufigkeit zu implementieren. Wenn Sie viele Clients haben, können eine Vielzahl von Konflikten auftreten.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine Datenreihe aktualisieren und abrufen müssen, die einer einzelnen Entität zugeordnet ist.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Muster für große Entitäten](#large-entities-pattern)  
* [Zusammenführen oder ersetzen](#merge-or-replace)  
* [Eventual Consistency-Transaktionsmuster](#eventually-consistent-transactions-pattern) (wenn Sie die Datenreihe in einem Blob speichern)  

## <a name="wide-entities-pattern"></a>Breite Entitätenmuster
Verwenden Sie mehrere physische Entitäten, um logische Entitäten mit mehr als 252 Eigenschaften zu speichern.  

### <a name="context-and-problem"></a>Kontext und Problem
Eine einzelne Entität kann nicht mehr als 252 Eigenschaften (mit Ausnahme der obligatorischen Systemeigenschaften) beinhalten und nicht mehr als 1 MB Daten insgesamt speichern. In einer relationalen Datenbank würden Sie in der Regel die Grenzen umgehen können bezüglich der Größe einer Zeile, indem Sie eine neue Tabelle hinzufügen und eine 1:1-Beziehung erzwingen.  

### <a name="solution"></a>Lösung
Durch Verwendung des Tabellenspeicherdienstes können Sie mehrere Entitäten speichern, um ein einzelnes großes Geschäftsobjekt mit mehr als 252 Eigenschaften darzustellen. Beispiel: Wenn Sie die Anzahl der Chatnachrichten, die von jedem Mitarbeiter in den letzten 365 Tagen gesendet wurde, speichern möchten, können Sie den folgenden Entwurf verwenden, der zwei Entitäten mit unterschiedlichen Schemas verwendet:  

![Mehrere Entitäten](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Falls Sie eine Änderung vornehmen müssen, die eine Aktualisierung beider Entitäten erfordert, um sie synchron zu halten, können Sie ein EGT verwenden. Ansonsten können Sie einen einzelnen Zusammenführungsvorgang verwenden, um den Meldungszähler für einen bestimmten Tag zu aktualisieren. Um alle Daten für einen einzelnen Mitarbeiter abzurufen, müssen Sie beide Entitäten abrufen, was Sie mit zwei effizienten Anforderungen durchführen können, die beide einen **PartitionKey**- und einen **RowKey**-Wert verwenden.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Der Abruf einer vollständigen logischen Entität umfasst mindestens zwei Speichertransaktionen, jeweils eine zum Abrufen der einzelnen physischen Entitäten.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie Entitäten speichern müssen, deren Größe oder Anzahl von Eigenschaften die Grenzwerte für eine einzelne Entität im Tabellenspeicherdienst überschreitet.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* Entitätsgruppentransaktionen
* [Zusammenführen oder ersetzen](#merge-or-replace)

## <a name="large-entities-pattern"></a>Muster für große Entitäten
Verwenden Sie Blob-Speicher zum Speichern großer Eigenschaftswerte.  

### <a name="context-and-problem"></a>Kontext und Problem
Eine einzelne Entität kann nicht mehr als 1 MB Daten insgesamt speichern. Wenn eine oder mehrere Ihrer Eigenschaften Werte speichern, die dazu führen, dass die Gesamtgröße der Entität diesen Wert überschreitet, können Sie nicht die gesamte Entität im Tabellenspeicherdienst speichern.  

### <a name="solution"></a>Lösung
Wenn Ihre Entität 1 MB Größe überschreitet, da eine oder mehrere Eigenschaften eine große Datenmenge enthalten, können Sie die Daten im Blob-Dienst und anschließend die Adresse des BLOBs in einer Eigenschaft der Entität speichern. Beispiel: Sie können das Foto des Mitarbeiters im Blob-Speicher und eine Verknüpfung zu dem Foto in der **Photo** -Eigenschaft der Mitarbeiterentität speichern:  

![Eigenschaft „Photo“](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Um "Eventual Consistency" zwischen der Entität im Tabellenspeicherdienst und den Daten im Blob-Dienst beizubehalten, verwenden Sie das [Eventual Consistency-Transaktionsmuster](#eventually-consistent-transactions-pattern) zur Verwaltung der Entitäten.
* Das Abrufen einer vollständigen Entität umfasst mindestens zwei Speichertransaktionen: eine zum Abrufen der Entität und eine zum Abrufen der Blob-Daten.  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie Entitäten speichern müssen, deren Größe die Grenzwerte für eine einzelne Entität im Tabellenspeicherdienst überschreitet.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  
* [Breite Entitätenmuster](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Antimuster voranstellen/anfügen
Erhöhen Sie die Skalierbarkeit, wenn Sie eine große Anzahl von Einfügungen vornehmen möchten, indem Sie die Einfügungen auf mehrere Partitionen verteilen.  

### <a name="context-and-problem"></a>Kontext und Problem
Voranstellen oder Anfügen von Entitäten an Ihre gespeicherten Entitäten führen in der Regel dazu, dass in der Anwendung neue Entitäten auf die erste oder letzte Partition aus einer Folge von Partitionen hinzugefügt werden. In diesem Fall finden alle Einfügungen zu einem beliebigen Zeitpunkt in der gleichen Partition statt und erzeugen einen Hotspot, der Lastenausgleich-Einfügungen durch den Tabellenspeicherdienst für mehrere Knoten verhindert und wahrscheinlich dazu führt, dass Ihre Anwendung die Skalierbarkeitsziele der Partition erreicht. Beispiel: Wenn Sie eine Anwendung haben, die Zugriffe auf Netzwerk und Ressourcen von Mitarbeitern protokolliert, könnte eine wie unten gezeigte Entitätsstruktur dazu führen, dass die Partition zur laufenden Stunde zum Hotspot wird, wenn das Transaktionsvolumen das Skalierbarkeitsziel für eine einzelne Partition erreicht:  

![Entitätsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Lösung
Die folgende alternative Entitätsstruktur vermeidet einen Hotspot auf einer bestimmten Partition, wenn die Anwendung Ereignisse protokolliert:  

![Alternative Entitätsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Beachten Sie, dass in diesem Beispiel sowohl der **PartitionKey**- als auch der **RowKey**-Wert ein Verbundschlüssel ist. **PartitionKey** verwendet sowohl die Abteilung als auch die Mitarbeiter-ID, um die Protokollierung auf mehrere Partitionen zu verteilen.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Unterstützt die alternative Schlüssel-Struktur, die das Erstellen von Hot-Partitionen bei Einfügungen vermeidet, die Abfragen effizient, die von der Clientanwendung vorgenommen werden?  
* Bedeutet die erwartete Anzahl von Transaktionen, dass Sie wahrscheinlich die Skalierbarkeitsziele für eine einzelne Partition erreichen und vom Speicherdienst gedrosselt werden?  

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Vermeiden Sie die Voranstellen/Anfügen-Antimuster, wenn die Anzahl an Transaktionen wahrscheinlich dazu führen wird, dass beim Zugriff auf eine Hot-Partition vom Speicherdienst eine Drosselung erfolgt.  

### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anleitungen können auch relevant sein, wenn dieses Muster implementiert wird:  

* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* [Log Tail-Muster](#log-tail-pattern)  
* [Ändern von Entitäten](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Log-Anti-Muster
Sie sollten in der Regel den Blob-Dienst anstelle des Tabellenspeicherdienstes zum Speichern von Protokolldaten verwenden.  

### <a name="context-and-problem"></a>Kontext und Problem
Eine allgemeiner Anwendungsfall für Protokolldaten ist, eine Auswahl von Protokolleinträgen für einen bestimmten Datum/Uhrzeitbereich abzurufen. Beispielsweise finden Sie alle Fehler und kritische Meldungen, die Ihre Anwendung zwischen 15:04 und 15:06 an einem bestimmten Datum protokolliert hat. Es empfiehlt sich nicht, das Datum und die Uhrzeit der Protokollmeldung zu verwenden, um die Partition zu bestimmen, in der die Protokolleinträge gespeichert werden sollen. Dies führt zu einer Hot-Partition, da alle Protokolleinträge jederzeit den gleichen **PartitionKey**-Wert gemeinsam nutzen. (Weitere Informationen finden Sie im Abschnitt [Antimuster voranstellen/anfügen](#prepend-append-anti-pattern).) Beispiel: Das folgende Entitätsschema für eine Protokollmeldung resultiert in einer Hot-Partition, da die Anwendung alle Protokollmeldungen in die Partition für das aktuelle Datum und Stunde schreibt:  

![Entität für Protokollmeldung](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

In diesem Beispiel beinhaltet **RowKey** Datum und Uhrzeit der Protokollmeldung, um sicherzustellen, dass die Protokollmeldungen nach Datum und Uhrzeit sortiert gespeichert werden, sowie eine Meldungs-ID für den Fall, dass mehrere Protokollmeldungen dasselbe Datum und dieselbe Uhrzeit aufweisen.  

Ein anderer Ansatz ist die Verwendung von **PartitionKey** , der sicherstellt, dass die Anwendung Meldungen über einen Bereich von Partitionen schreibt. Beispiel: Wenn die Quelle der Protokollmeldung einen Weg bietet, Meldungen über viele Partitionen zu verteilen, können Sie das folgende Entitätsschema verwenden:  

![Alternative Entität für Protokollmeldung](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Das Problem mit diesem Schema ist jedoch, dass Sie zum Abrufen der gesamten Protokollmeldungen für eine bestimmte Zeitspanne jede Partition in der Tabelle durchsuchen müssen.

### <a name="solution"></a>Lösung
Der vorherige Abschnitt hat das Problem bei dem Versuch hervorgehoben, den Tabellenspeicherdienst zum Speichern von Protokolleinträgen zu verwenden und schlug zwei nicht zufriedenstellende Entwürfe vor. Eine Lösung führte zu einer Hot-Partition mit dem Risiko einer schlechten Leistung beim Schreiben von Protokollmeldungen. Die andere Lösung führte zu einer schlechten Leistung bei Abfragen aufgrund der Anforderung, jede Partition in der Tabelle zum Abrufen von Protokollmeldungen für eine bestimmte Zeitspanne zu scannen. BLOB-Speicher bietet eine bessere Lösung für diese Art Szenario und dies ist die Funktionsweise, wie Azure Storage Analytics die gesammelten Protokolldaten speichert.  

In diesem Abschnitt wird erläutert, wie Storage Analytics Protokolldaten im Blob-Speicher speichert, als anschauliches Beispiel zu diesem Ansatz, um Daten zu speichern, die Sie in der Regel nach Bereich abfragen.  

Storage Analytics speichert Protokollmeldungen in einem Trennzeichen-getrennten Format in mehrere Blobs. Das Trennzeichen-getrennte Format erleichtert es einer Clientanwendung, die Daten in der Protokollmeldung zu analysieren.  

Storage Analytics verwendet eine Namenskonvention für Blobs, die es Ihnen ermöglicht, einen Blob (oder Blobs) zu finden, der die Protokollmeldungen enthält, nach denen Sie suchen. Beispiel: Ein Blob mit dem Namen "queue/2014/07/31/1800/000001.log" enthält die Protokollmeldungen, die dem Warteschlangendienst für die Stunde zugeordnet sind, beginnend um 18:00 Uhr am 31. Juli 2014. Die "000001" zeigt an, dass dies die erste Protokolldatei für diesen Zeitraum ist. Storage Analytics zeichnet auch die Zeitstempel der Protokollmeldungen von Vor- und Nachname an, die in der Datei als Teil der Blob-Metadaten gespeichert sind. Die API für Blobspeicher ermöglicht Ihnen, Blobs in einem Container zu finden, die auf einem Namenspräfix basieren. Um alle Blobs zu finden, die Protokolldaten für die Warteschlange für die Stunde beinhalten, die um 18:00 Uhr beginnt, können Sie das Präfix „queue/2014/07/31/1800“ verwenden.  

Storage Analytics puffert Protokollmeldungen intern, und aktualisiert dann den geeigneten Blob oder erstellt einen neuen Blob mit dem neuesten Satz Protokolleinträge. Dies reduziert die Anzahl der Schreibvorgänge, die für den Blob-Dienst ausgeführt werden müssen.  

Wenn Sie eine ähnliche Lösung in Ihre eigene Anwendung implementieren, müssen Sie berücksichtigen, wie Sie den Kompromiss zwischen Zuverlässigkeit (sofortiges Schreiben jedes Protokolleintrags in den Blob-Speicher) und Kosten und Skalierbarkeit (Pufferung der Aktualisierungen in Ihrer Anwendung und Schreiben im Stapel zum Blob-Speicher) verwalten.  

### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie Sie Protokolldaten speichern:  

* Wenn Sie einen Tabellenentwurf erstellen, der potenzielle Hot-Partitionen vermeidet, werden Sie feststellen, dass Sie auf Ihre Protokolldaten nicht effizient zugreifen können.  
* Zur Verarbeitung von Protokolldaten muss ein Client häufig viele Datensätze laden.  
* Obwohl Protokolldaten häufig strukturiert sind, ist Blob-Speicher möglicherweise eine bessere Lösung.  

## <a name="implementation-considerations"></a>Überlegungen zur Implementierung
Dieser Abschnitt beschreibt einige Überlegungen, die Sie berücksichtigen sollten, wenn Sie die in den vorherigen Abschnitten beschriebenen Muster implementieren. Im größten Teil dieses Abschnitts werden Beispiele verwendet, die in C# geschrieben sind und die Storage Client Library (Version 4.3.0 zum Redaktionszeitpunkt) verwendet.  

## <a name="retrieving-entities"></a>Abrufen von Entitäten
Wie im Abschnitt „Entwurf für Abfragen“ erläutert, ist die effizienteste Abfrage eine Punktabfrage. In einigen Szenarien müssen Sie jedoch mehrere Entitäten abrufen. Dieser Abschnitt beschreibt einige allgemeine Ansätze zum Abrufen von Entitäten mithilfe der Storage Client Library.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Ausführen einer Punktabfrage mithilfe der Storage Client Library
Die einfachste Möglichkeit zum Ausführen einer Punktabfrage ist die Verwendung des Tabellenvorgangs **Abrufen**, wie im folgenden C#-Codeausschnitt gezeigt, der eine Entität mit dem **PartitionKey**-Wert „Sales“ und dem **RowKey**-Wert „212“ abruft:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Beachten Sie, dass in diesem Beispiel erwartet wird, dass die abgerufene Entität den Typ **EmployeeEntity** hat.  

### <a name="retrieving-multiple-entities-using-linq"></a>Abrufen von mehreren Entitäten mithilfe von LINQ
Sie können LINQ zum Abrufen mehrerer Entitäten aus dem Tabellenspeicherdienst verwenden, wenn Sie die Standardbibliothek für Tabellen in Microsoft Azure Cosmos verwenden. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Geben Sie Namespaces an, damit die folgenden Beispiele funktionieren:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

Bei „employeeTable“ handelt es sich um ein „CloudTable“-Objekt, das eine „CreateQuery\<ITableEntity>()“-Methode implementiert, die eine „TableQuery\<ITableEntity>“ zurückgibt. Objekte dieses Typs implementieren eine „iQueryable“-Schnittstelle und ermöglichen die Verwendung von LINQ-Abfrageausdrücken und der Punktnotationssyntax.

Das Abrufen von mehreren Entitäten erfolgt durch das Angeben einer Abfrage mit einer **where**-Klausel. Um einen Tabellenscan zu vermeiden, sollten Sie immer den **PartitionKey**-Wert in die where-Klausel einschließen (und möglichst auch den **RowKey**-Wert, um Tabellen- und Partitionsscans zu vermeiden). Der Tabellenspeicherdienst unterstützt eine begrenzte Anzahl von Vergleichsoperatoren (größer als, größer als oder gleich, kleiner als, kleiner als oder gleich, gleich und ungleich) zur Verwendung in der WHERE-Klausel. 

Der folgende C#-Codeausschnitt sucht alle Mitarbeiter, deren Nachname mit „B“ beginnt (vorausgesetzt, in **RowKey** ist der Nachname gespeichert) und die der Vertriebsabteilung angehören (vorausgesetzt, in **PartitionKey** ist der Name der Abteilung gespeichert):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Beachten Sie, dass die Abfrage aus Effizienzgründen sowohl einen **RowKey**- als auch einen **PartitionKey**-Wert enthält.  

Das folgende Codebeispiel zeigt die entsprechende Funktion ohne Verwendung von LINQ-Syntax:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Das Beispiel schachtelt mehrere **CombineFilters** -Methoden, um die drei Filterbedingungen einschließen zu können.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Abrufen einer großen Anzahl von Entitäten aus einer Abfrage
Eine optimale Abfrage gibt auf der Grundlage eines **PartitionKey**- und eines **RowKey**-Werts eine einzelne Entität zurück. In einigen Szenarien haben Sie jedoch möglicherweise eine Anforderung, um viele Entitäten von der gleichen Partition oder sogar von vielen Partitionen zurückzugeben.  

In solchen Szenarien sollten Sie die Leistung Ihrer Anwendung immer vollständig testen.  

Eine Abfrage für den Tabellenspeicherdienst kann maximal 1000 Entitäten gleichzeitig zurückgeben und für maximal fünf Sekunden ausgeführt werden. Wenn der Ergebnissatz mehr als 1.000 Entitäten enthält, wenn die Abfrage nicht innerhalb von fünf Sekunden abgeschlossen wurde oder die Abfrage die Partitionsgrenzen überschreitet, gibt der Tabellenspeicherdienst ein Fortsetzungstoken zurück, das der Clientanwendung ermöglicht, den nächsten Satz Entitäten anzufordern. Weitere Informationen über die Funktionsweise von Fortsetzungstoken finden Sie unter [Abfragetimeout und Paginierung](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Bei Verwendung der Storage Client Library kann diese automatisch Fortsetzungstoken für Sie handhaben, da es Entitäten aus dem Tabellenspeicherdienst zurückgibt. Das folgende C#-Codebeispiel verwendet die Storage Client Library und handhabt automatisch Fortsetzungstoken, wenn er vom Tabellenspeicherdienst in der Antwort zurückgegeben wird:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Der folgende C#-Code handhabt Fortsetzungstoken explizit:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Durch die explizite Verwendung von Fortsetzungstoken können Sie steuern, wann Ihre Anwendung das nächste Datensegment abruft. Beispiel: Wenn Ihre Clientanwendung Benutzer in die Lage versetzt, durch die Entitäten zu blättern, die in einer Tabelle gespeichert sind, kann ein Benutzer sich entscheiden, nicht durch alle über die Abfrage abgerufenen Entitäten zu blättern. Somit würde Ihre Anwendung nur einen Fortsetzungstoken verwenden, um das nächste Segment abzurufen, wenn der Benutzer mit dem Blättern durch alle Entitäten im aktuellen Segment fertig ist. Dieser Ansatz hat mehrere Vorteile:  

* Er versetzt Sie in die Lage, die Datenmenge für den Abruf vom Tabellenspeicherdienst zu begrenzen, die sie über das Netzwerk verschieben.  
* Er versetzt Sie in die Lage, asynchrone I/O in .NET auszuführen.  
* Sie können den Fortsetzungstoken in einen permanenten Speicher serialisieren, damit bei einem Absturz der Anwendung fortgesetzt werden kann.  

> [!NOTE]
> Ein Fortsetzungstoken gibt in der Regel ein Segment mit 1.000 Entitäten oder weniger zurück. Dies ist auch der Fall, wenn Sie die Anzahl von Einträgen, die eine Abfrage zurückgibt, begrenzen, indem Sie **Take** verwenden, um die ersten n Entitäten zurückzugeben, die mit Ihren Suchkriterien übereinstimmen. Der Tabellenspeicherdienst kann ein Segment zurückgeben, das weniger als n Entitäten enthält, sowie ein Fortsetzungstoken, mit dem Sie die restlichen Entitäten abrufen können.  
> 
> 

Der folgende C#-Code zeigt, wie die Anzahl der in einem Segment zurückgegebenen Entitäten geändert werden kann:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Serverseitige Projektion
Eine einzelne Entität kann bis zu 255 Eigenschaften haben und bis zu 1 MB groß sein. Wenn Sie die Tabelle durchsuchen und Entitäten abrufen, benötigen Sie möglicherweise nicht alle Eigenschaften und Sie können unnötiges Übertragen von Daten vermeiden (trägt zum Verringern der Latenz und Kosten bei). Sie können auch eine serverseitige Projektion verwenden, um nur die benötigten Eigenschaften zu übertragen. Im folgenden Beispiel wird nur die **Email**-Eigenschaft (zusammen mit **PartitionKey**, **RowKey**, **Timestamp** und **ETag**) aus den Entitäten abgerufen, die durch die Abfrage ausgewählt werden.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Beachten Sie, dass der **RowKey** -Wert auch dann verfügbar ist, wenn er nicht in der Liste der abzurufenden Eigenschaften enthalten war.  

## <a name="modifying-entities"></a>Ändern von Entitäten
Die Storage Client Library versetzt Sie in die Lage, Ihre im Tabellenspeicherdienst gespeicherte Entitäten durch Einfügen, Löschen und Aktualisieren zu ändern. Sie können EGTs verwenden, um mehrere Einfüge-, Aktualisierungs- und Löschvorgänge zu stapeln und so die Anzahl der benötigten Roundtrips zu reduzieren und die Leistung Ihrer Lösung zu verbessern.  

Wenn die Storage Client Library eine EGT ausführt, werden Ausnahmen ausgelöst, da diese den Index der Entität beinhaltet, die einen Fehler bei der Stapelverarbeitung verursacht hat. Dies ist hilfreich, wenn Sie Code debuggen, der EGTs verwendet.  

Sie sollten auch berücksichtigen, wie Ihr Design beeinflusst wird und wie Ihre Anwendung Nebenläufigkeit und Aktualisierungsvorgänge handhabt.  

### <a name="managing-concurrency"></a>Verwalten von Nebenläufigkeit
Der Tabellenspeicherdienst implementiert standardmäßig Prüfungen der optimistischen Nebenläufigkeit auf der Ebene der einzelnen Entitäten für die Vorgänge **Einfügen**, **Zusammenführen** und **Löschen**, obwohl es für einen Client möglich ist, das Umgehen dieser Prüfungen durch den Tabellenspeicherdienst zu erzwingen. Weitere Informationen zur Verwaltung von Nebenläufigkeit durch den Tabellenspeicherdienst finden Sie unter [Verwalten von Nebenläufigkeit in Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Zusammenführen oder ersetzen
Die **Replace**-Methode der **TableOperation**-Klasse ersetzt immer die vollständige Entität im Tabellenspeicherdienst. Wenn Sie keine Eigenschaft in die Anforderung einschließen und diese Eigenschaft in der gespeicherten Entität existiert, entfernt die Anforderung diese Eigenschaft von der gespeicherten Entität. Sie müssen alle Eigenschaften in die Anforderung mit einschließen, wenn Sie nicht möchten, dass eine Eigenschaft aus einer gespeicherten Entität explizit entfernen wird.  

Sie können die **Merge**-Methode der **TableOperation**-Klasse verwenden, um die Datenmenge zu reduzieren, die Sie an den Tabellenspeicherdienst senden, wenn Sie eine Entität aktualisieren möchten. Die **Merge** -Methode ersetzt alle Eigenschaften in der gespeicherten Entität mit Eigenschaftswerten aus der Entität, die in der Anforderung enthalten ist, behält aber alle Eigenschaften in der gespeicherten Entität bei, die in der Anforderung nicht enthalten sind. Dies ist hilfreich, wenn Sie große Entitäten haben und nur eine kleine Anzahl von Eigenschaften in einer Anforderung aktualisieren müssen.  

> [!NOTE]
> Die **Replace**- und die **Merge**-Methode sind nicht erfolgreich, wenn die Entität nicht vorhanden ist. Als Alternative können Sie die **InsertOrReplace**-Methode und die **InsertOrMerge**-Methode verwenden, die eine neue Entität erstellen, falls keine vorhanden ist.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Arbeiten mit heterogenen Entitätstypen
Der Tabellenspeicherdienst ist ein *schemaloser* Tabellenspeicher. Das bedeutet, dass eine einzelne Tabelle Entitäten mehrerer Typen speichern kann, sodass Ihr Entwurf von einem hohen Maß an Flexibilität profitiert. Das folgende Beispiel veranschaulicht eine Tabelle, in der sowohl Mitarbeiter- als auch Abteilungsentitäten gespeichert sind:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Trotzdem muss jede Entität über **PartitionKey**-, **RowKey**- und **Timestamp**-Werte verfügen, kann aber einen beliebigen Satz von Eigenschaften besitzen. Darüber hinaus gibt es nichts, was den Typ einer Entität anzeigen kann, es sei denn, Sie entscheiden sich dafür, diese Information irgendwo zu speichern. Es gibt zwei Optionen für die Identifizierung des Entitätstyps:  

* Voranstellen des Entitätstyps vor den **RowKey**-Wert (oder möglicherweise den **PartitionKey**-Wert). Beispielsweise **EMPLOYEE_000123** oder **DEPARTMENT_SALES** als **RowKey**-Werte.  
* Verwenden Sie eine separate Eigenschaft zum Aufzeichnen des Entitätstyps, wie in der folgenden Tabelle dargestellt.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Die erste Option mit dem vorangestellten Entitätstyp vor den **RowKey** ist sinnvoll, wenn eine Möglichkeit besteht, dass zwei Entitäten mit unterschiedlichen Typen über denselben Schlüsselwert verfügen können. Hier werden auch Entitäten des gleichen Typs in der Partition zusammen gruppiert.  

Die in diesem Abschnitt beschriebenen Verfahren sind besonders wichtig für die Erörterung der [Vererbungsbeziehungen](table-storage-design-modeling.md#inheritance-relationships) weiter oben in diesem Handbuch (Artikel [Modellieren von Beziehungen](table-storage-design-modeling.md)).  

> [!NOTE]
> Sie sollten die Angabe einer Versionsnummer im Wert des Entitätstyps berücksichtigen, um der Clientanwendung zu ermöglichen, POCO-Objekte zu entwickeln und mit verschiedenen Versionen zu arbeiten.  
> 
> 

Im restlichen Teil dieses Abschnitts werden einige Funktionen der Storage Client Library beschrieben, mit denen die Arbeit mit mehreren Entitätstypen in derselben Tabelle erleichtert wird.  

### <a name="retrieving-heterogeneous-entity-types"></a>Abrufen von heterogenen Entitätstypen
Bei Verwendung der Storage Client Library stehen Ihnen drei Optionen für die Arbeit mit mehreren Entitätstypen zur Verfügung.  

Wenn Sie den Entitätstyp kennen, der mit einem bestimmten **RowKey**- und **PartitionKey**-Wert gespeichert ist, können Sie beim Abrufen der Entität den Entitätstyp angeben, wie in den beiden vorherigen Beispielen zum Abrufen von Entitäten des Typs **EmployeeEntity** dargestellt: [Ausführen einer Punktabfrage mithilfe der Speicherclientbibliothek](#executing-a-point-query-using-the-storage-client-library) und [Abrufen von mehreren Entitäten mithilfe von LINQ](#retrieving-multiple-entities-using-linq).  

Die zweite Option ist die Verwendung des **DynamicTableEntity** -Typs (ein Eigenschaftenbehälter) anstelle eines konkreten POCO-Entitätstyps (diese Option kann auch die Leistung verbessern, da keine Notwendigkeit zum Serialisieren und Deserialisieren der Entität in .NET-Typen besteht). Der folgende C#-Code ruft möglicherweise mehrere Entitäten mit unterschiedlichen Typen aus der Tabelle ab, gibt jedoch alle Entitäten als **DynamicTableEntity** -Instanzen zurück. Anschließend bestimmt er mithilfe der **EventType** -Eigenschaft den Typ von jeder Entität:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Zum Abrufen anderer Eigenschaften müssen Sie die **TryGetValue**-Methode für die **Properties**-Eigenschaft der **DynamicTableEntity**-Klasse verwenden.  

Eine dritte Möglichkeit wäre, den **DynamicTableEntity**-Typ mit einer **EntityResolver**-Instanz zu kombinieren. Dadurch können Sie mehrere POCO-Typen in der gleichen Abfrage auflösen. In diesem Beispiel verwendet der **EntityResolver**-Delegat die **EntityType**-Eigenschaft, um zwischen den beiden Typen der Entität zu unterscheiden, die von der Abfrage zurückgegeben werden. Die **Resolve**-Methode verwendet den **resolver**-Delegaten, um **DynamicTableEntity**-Instanzen zu **TableEntity**-Instanzen aufzulösen.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Ändern von heterogenen Entitätstypen
Zum Löschen einer Entität müssen Sie den Typ wissen. Sie wissen den Typ einer Entität immer, wenn sie ihn Einfügen. Sie können jedoch den **DynamicTableEntity**-Typ verwenden, um eine Entität zu aktualisieren, ohne deren Typ zu kennen und ohne eine POCO-Entitätsklasse zu verwenden. Im folgenden Codebeispiel wird eine einzelne Entität abgerufen und überprüft, ob die **EmployeeCount**-Eigenschaft vorhanden ist, bevor sie aktualisiert wird.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Steuern des Zugriffs mit Shared Access Signatures
Sie können Shared Access Signature (SAS)-Token verwenden, um Clientanwendungen zu ermöglichen, Tabellenentitäten zu ändern (und abzufragen) ohne die Notwendigkeit, Ihren Speicherkontoschlüssel in Ihren Code einzuschließen. Es gibt in der Regel drei Hauptvorteile, wenn Sie SAS in Ihrer Anwendung verwenden:  

* Sie müssen Ihren Speicherkontoschlüssel nicht auf eine unsichere Plattform (z. B. ein mobiles Gerät) verteilen, um auf das Gerät zuzugreifen und Entitäten im Tabellenspeicherdienst zu ändern.  
* Sie können einige der Aufgaben auslagern, die Web- und Workerrollen beim Verwalten von Entitäten in Client-Geräten durchführen, z. B. für Computer der Endbenutzer und mobile Geräte.  
* Sie können einen eingeschränkten und einen zeitbegrenzten Satz von Berechtigungen für einen Client zuweisen (z. B. den schreibgeschützten Zugriff auf bestimmte Ressourcen).  

Weitere Informationen zur Verwendung von SAS-Token mit dem Tabellenspeicherdienst finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md).  

Sie müssen jedoch weiterhin die SAS-Token generieren, die eine Clientanwendung für die Entitäten im Tabellenspeicherdienst gewähren. Sie sollten dies in einer Umgebung tun, die sicheren Zugriff auf Ihre Speicherkontoschlüssel gewährleistet. In der Regel verwenden Sie eine Web- oder Worker-Rolle zum Generieren der SAS-Token und übermittelt sie an die Client-Anwendungen, die Zugriff auf Ihre Entitäten benötigen. Da immer noch Aufwand zum Generieren und Bereitstellen von SAS-Token für Clients notwendig ist, sollten Sie erwägen, wie Sie diesen Aufwand am besten reduzieren, besonders in Szenarien mit großen Datenmengen.  

Es ist möglich, einen SAS-Token zu generieren, der Zugriff auf eine Teilmenge der Entitäten in einer Tabelle erteilt. Standardmäßig erstellen Sie ein SAS-Token für die gesamte Tabelle, aber Sie können auch angeben, dass dem SAS-Token entweder Zugriff auf einen Bereich von **PartitionKey**-Werten oder einen Bereich von **PartitionKey**- und **RowKey**-Werten gewährt wird. Sie können auswählen, ob Sie SAS-Token für einzelne Benutzer des Systems in einer Art generieren, das jedes Benutzer-SAS-Token nur Zugriff auf ihre eigenen Entitäten im Tabellenspeicherdienst ermöglicht.  

## <a name="asynchronous-and-parallel-operations"></a>Asynchrone und parallele Vorgänge
Angenommen, Sie verteilen Ihre Anfragen über mehrere Partitionen. In diesem Fall können Sie Durchsatz und Client-Reaktionsfähigkeit mithilfe von asynchronen oder parallelen Abfragen verbessern.
Beispiel: Sie haben möglicherweise zwei oder mehr Workerrollen-Instanzen, die auf die Tabellen parallel zugreifen. Sie können einzelne Worker-Rollen haben, die für bestimmte Gruppen von Partitionen zuständig sind oder einfach mehrere Workerrollen-Instanzen, von denen jede auf alle Partitionen in einer Tabelle zugreifen kann.  

Innerhalb einer Client-Instanz können Sie den Durchsatz verbessern, indem Sie Speichervorgänge asynchron ausführen. Die Storage Client Library erleichtert das Schreiben von asynchronen Abfragen und Änderungen. Beispiel: Sie können mit der synchronen Methode starten, mit der alle Entitäten in einer Partition wie im folgenden C#-Code gezeigt abgerufen werden:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Sie können diesen Code einfach ändern, damit die Abfrage wie folgt asynchron ausgeführt wird:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

In diesem Asynchron-Beispiel sehen Sie die folgenden Änderungen zur synchronen Version:  

* Die Methodensignatur enthält jetzt den **async**-Modifizierer und gibt eine **Task**-Instanz zurück.  
* Statt die **ExecuteSegmented**-Methode zum Abrufen von Ergebnissen aufzurufen, ruft die Methode jetzt die **ExecuteSegmentedAsync**-Methode auf und verwendet den **await**-Modifizierer, um Ergebnisse asynchron abzurufen.  

Die Clientanwendung kann diese Methode mehrmals aufrufen (mit unterschiedlichen Werten für den **department**-Parameter). Damit wird jede Abfrage in einem separaten Thread ausgeführt.  

In der **TableQuery**-Klasse gibt es keine asynchrone Version der **Execute**-Methode, da die **IEnumerable**-Schnittstelle keine asynchrone Enumeration unterstützt.  

Sie können Entitäten auch asynchron einfügen, aktualisieren und löschen. Das folgende C#-Beispiel zeigt eine einfache, synchrone Methode zum Einfügen oder Ersetzen einer Mitarbeiterentität:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Sie können diesen Code einfach ändern, damit das Update wie folgt asynchron ausgeführt wird:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In diesem Asynchron-Beispiel sehen Sie die folgenden Änderungen zur synchronen Version:  

* Die Methodensignatur enthält jetzt den **async**-Modifizierer und gibt eine **Task**-Instanz zurück.  
* Statt die **Execute**-Methode aufzurufen, um die Entität zu aktualisieren, ruft die Methode jetzt die **ExecuteAsync**-Methode auf und verwendet den **await**-Modifizierer, um Ergebnisse asynchron abzurufen.  

Die Clientanwendung kann mehrere asynchrone Methoden wie diese aufrufen und jeder Methodenaufruf wird auf einem separaten Thread ausgeführt.  

## <a name="next-steps"></a>Nächste Schritte

- [Modellieren von Beziehungen](table-storage-design-modeling.md)
- [Entwurf für Abfragen](table-storage-design-for-query.md)
- [Verschlüsseln von Tabellendaten](table-storage-design-encrypt-data.md)
- [Entwurf für die Datenänderung](table-storage-design-for-modification.md)