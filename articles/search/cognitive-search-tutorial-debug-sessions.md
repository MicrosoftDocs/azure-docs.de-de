---
title: 'Tutorial: Verwenden von Debugsitzungen zum diagnostizieren, Korrigieren und Committen von Änderungen an Ihrem Skillset'
titleSuffix: Azure Cognitive Search
description: Debugsitzungen (Vorschau) bieten eine portalbasierte Oberfläche, um Probleme/Fehler in ihren Skillsets zu bewerten und zu beheben.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8ec39c4616f5a34f8326b56d4f0ba6e15cdad91c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699116"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Tutorial: Diagnostizieren, Reparieren und Committen von Änderungen an Ihrem Skillset

In diesem Artikel verwenden Sie das Azure-Portal, um auf Debugsitzungen zuzugreifen und Probleme mit dem bereitgestellten Skillset zu beheben. Das Skillset weist einige Fehler auf, die behoben werden müssen. Dieses Tutorial führt Sie durch eine Debugsitzung, um Probleme mit Qualifikationseingaben und -ausgaben zu identifizieren und zu beheben.

> [!Important]
> Bei Debugsitzungen handelt es sich um eine Previewfunktion, die ohne Vereinbarung zum Servicelevel bereitgestellt wird und nicht für Produktionsworkloads vorgesehen ist. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Ein Azure-Abonnement. Erstellen eines [kostenlosen Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oder Verwenden des aktuellen Abonnements
> * Eine Azure Cognitive Search-Dienstinstanz
> * Ein Azure-Speicherkonto
> * [Postman-Desktop-App](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Erstellen von Diensten und Laden von Daten

In diesem Tutorial werden Azure Cognitive Search und Azure Storage-Dienste verwendet.

* [Laden Sie Beispieldaten herunter](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), die aus 19 Dateien bestehen.

* [Erstellen Sie ein Azure Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-portal), oder [suchen Sie nach einem vorhandenen Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Es muss sich in der gleichen Region wie Azure Cognitive Search befinden, um Bandbreitengebühren zu vermeiden.
   
   Es muss den Kontotyp „StorageV2 (allgemein, Version 2)“ besitzen.

* Öffnen Sie die Seiten für Speicherdienste, und erstellen Sie einen Container. Eine bewährte Methode besteht darin, die Zugriffsebene „Privat“ anzugeben. Nennen Sie den Container `clinicaltrialdataset`.

* Klicken Sie im Container auf **Hochladen**, um die im ersten Schritt heruntergeladenen und entpackte Beispieldateien hochzuladen.

* [Erstellen Sie einen Azure Cognitive Search-Dienst](search-create-service-portal.md), oder [suchen Sie nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden.

## <a name="get-a-key-and-url"></a>Abrufen eines Schlüssels und einer URL

Für REST-Aufrufe sind die Dienst-URL und ein Zugriffsschlüssel für jede Anforderung erforderlich. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels" border="false":::

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="create-data-source-skillset-index-and-indexer"></a>Erstellen von Datenquellen, Skillset, Index und Indexer

In diesem Abschnitt werden Postman und eine bereitgestellte Sammlung verwendet, um die Datenquelle, das Skillset, den Index und den Indexer des Suchdiensts zu erstellen.

1. Wenn Sie nicht über Postman verfügen, können Sie die [Postman-Desktop-App hier herunterladen](https://www.getpostman.com/).
1. [Herunterladen der Postman-Sammlung für Debugsitzungen](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Starten von Postman
1. Wählen Sie unter **Dateien** > **Neu** die Sammlung aus, die importiert werden soll.
1. Nachdem die Sammlung importiert wurde, erweitern Sie die Liste der Aktionen (...).
1. Klicken Sie auf **Bearbeiten**.
1. Geben Sie den Namen Ihres Suchdiensts (searchService) ein. (Ist der Endpunkt etwa `https://mydemo.search.windows.net`, lautet der Dienstname `mydemo`.)
1. Geben Sie den API-Schlüssel (apiKey) entweder mit dem primären oder sekundären Schlüssel Ihres Suchdiensts ein.
1. Geben Sie Speicher-Verbindungszeichenfolge (storageConnectionString) von der Schlüsselseite Ihres Azure Storage Kontos ein.
1. Geben Sie den Containernamen (containerName) für den Container ein, den Sie im Speicherkonto erstellt haben.

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Bearbeiten von Variablen in Postman":::

Die Sammlung enthält vier verschiedene REST-Aufrufe, die zum Fertigstellen dieses Abschnitts verwendet werden.

Der erste Befehl erstellt die Datenquelle: `clinical-trials-ds`. Der zweite Befehl erstellt das Skillset: `clinical-trials-ss`. Der dritte Befehl erstellt den Index: `clinical-trials`. Der vierte und letzte Befehl erstellt den indexer: `clinical-trials-idxr`. Nachdem alle Aufrufe in der Sammlung abgeschlossen wurden, schließen Sie Postman, und kehren Sie zum Azure-Portal zurück.

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="Verwenden von Postman zum Erstellen einer Datenquelle":::

## <a name="check-the-results"></a>Prüfen der Ergebnisse

Das Skillset enthält einige häufige Fehler. In diesem Abschnitt werden bei der Ausführung einer leeren Abfrage zum Zurückgeben aller Dokumente mehrere Fehler angezeigt. In den nachfolgenden Schritten werden die Probleme mithilfe einer Debugsitzung gelöst.

1. Wechseln Sie im Azure-Portal zu Ihrem Suchdienst. 
1. Wählen Sie die Registerkarte **Indizes** aus. 
1. Wählen Sie den Index `clinical-trials` aus.
1. Klicken Sie auf **Suchen**, um eine leere Abfrage auszuführen. 

Nach Abschluss der Suche werden im Fenster zwei Felder ohne Daten aufgeführt: „Organisationen“ und „Standorte“. Führen Sie die Schritte aus, um alle vom Skillset erzeugten Probleme zu ermitteln.

1. Kehren Sie zur Seite „Übersicht“ Ihres Suchdiensts zurück.
1. Wählen Sie die Registerkarte **Indexer** aus. 
1. Klicken Sie auf `clinical-trials-idxr`, und wählen Sie die Warnungsbenachrichtigung aus. 

Es liegen zahlreiche Probleme mit Projektionsausgabe-Feldzuordnungen vor, und auf Seite drei werden Warnungen angezeigt, weil mindestens eine Qualifikationseingabe ungültig ist.

Kehren Sie zum Bildschirm „Übersicht“ Ihres Suchdiensts zurück.

## <a name="start-your-debug-session"></a>Starten Sie Ihre Debugsitzung.

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Starten Sie eine neue Debugsitzung":::.

1. Klicken Sie auf die Registerkarte „Debugsitzungen“ (Vorschau).
1. „+NewDebugSession“ auswählen
1. Geben Sie der Sitzung einen Namen. 
1. Verbinden Sie die Sitzung mit Ihrem Speicherkonto. 
1. Geben Sie den Indexernamen an. Der Indexer verweist auf die Datenquelle, das Skillset und den Index.
1. Akzeptieren Sie die Standarddokumentauswahl für das erste Dokument in der Sammlung. 
1. **Speichern** Sie die Sitzung. Durch das Speichern der Sitzung wird die KI-Anreicherungspipeline gestartet, wie durch das Skillset definiert.

> [!Important]
> Eine Debugsitzung funktioniert nur mit einem einzelnen Dokument. Ein bestimmtes Dokument im Dataset kann > ausgewählt werden, oder die Sitzung wird standardmäßig auf das erste Dokument gesetzt.

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="Eine neue Debugsitzung wurde gestartet":::.

Wenn die Ausführung der Debugsitzung beendet ist, wird die Sitzung standardmäßig auf die Registerkarte „KI-Anreicherungen“ gesetzt, wobei der Skillgraph hervorgehoben wird.

+ Der Skillgraph bietet eine visuelle Hierarchie des Skillsets und seiner Ausführungsreihenfolge von oben nach unten. Qualifikationen, die im Graphen nebeneinander stehen, werden parallel ausgeführt. Die Farbcodierung der Qualifikationen im Graphen verdeutlicht die Typen der im Skillset ausgeführten Qualifikationen. In dem Beispiel sind die grünen Qualifikationen vom typ „Text“ und die rote Qualifikation ist vom Typ „Sehen“. Wenn Sie auf eine einzelne Qualifikation im Graphen klicken, werden die Details dieser Instanz der Qualifikation im rechten Bereich des Sitzungsfensters angezeigt. Die Skilleinstellungen, ein JSON-Editor, Ausführungsdetails sowie Fehler/Warnungen sind alle zur Überprüfung und Bearbeitung verfügbar.
+ Die angereicherte Datenstruktur enthält Einzelheiten zu den Knoten in der Anreicherungsstruktur, die durch die Qualifikationen aus dem Inhalt des Quelldokuments erzeugt wurden.

Die Registerkarte „Fehler/Warnungen“ enthält eine viel kleinere Liste als die zuvor angezeigte, da diese Liste nur die Details zu den Fehlern für ein einzelnes Dokument aufführt. Wie bei der vom Indexer angezeigten Liste können Sie auf eine Warnmeldung klicken und Details zu dieser Warnung anzeigen.

## <a name="fix-missing-skill-input-value"></a>Korrigieren fehlender Qualifikationseingabewerte

Auf der Registerkarte „Fehler/Warnungen“ gibt es einen Fehler für eine Operation mit der Bezeichnung `Enrichment.NerSkillV2.#1`. Die Details zu diesem Fehler erläutern, dass ein Problem mit einem Qualifikationseingabewert „/document/languageCode“ vorliegt. 

1. Kehren Sie zur Registerkarte „KI-Anreicherungen“ zurück.
1. Klicken Sie auf den **Skillgraphen**.
1. Klicken Sie auf die Qualifikation mit der Bezeichnung „#1“, um ihre Details im rechten Fensterbereich anzuzeigen.
1. Suchen Sie die Eingabe für „languageCode“.
1. Wählen Sie das Symbol **</>** am Anfang der Zeile aus, und öffnen Sie die Ausdrucksauswertung.
1. Klicken Sie auf die Schaltfläche **Auswerten**, um zu überprüfen, ob dieser Ausdruck einen Fehler erzeugt. Es zeigt sich, dass die Eigenschaft „languageCode“ keine gültige Eingabe ist.

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Ausdrucksauswertung":::

Es gibt zwei Möglichkeiten, diesen Fehler in der Sitzung zu recherchieren. Die erste besteht darin, zu untersuchen, woher die Eingabe stammt. Welche Qualifikation in der Hierarchie sollte dieses Ergebnis erzeugen? Auf der Registerkarte „Ausführungen“ sollte die Quelle der Eingabe im Detailbereich der Qualifikation angezeigt werden. Wenn keine Quelle vorhanden ist, deutet dies auf einen Feldzuordnungsfehler hin.

1. Klicken Sie auf die Registerkarte **Ausführungen**.
1. Schauen Sie sich die INPUTS an, und suchen Sie „languageCode“. Für diese Eingabe ist keine Quelle aufgeführt. 
1. Wechseln Sie in den linken Fensterbereich, um die angereicherte Datenstruktur anzuzeigen. Es gibt keinen zugeordneten Pfad, der „languageCode“ entspricht.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Angereicherte Datenstruktur":::

Es gibt einen zugeordneten Pfad für „Sprache“. Es liegt also einen Tippfehler in den Skilleinstellungen vor. Um dies zu beheben, muss der Ausdruck in der Qualifikation „#1“ mit dem Ausdruck „/document/language“ aktualisiert werden.

1. Öffnen Sie die Ausdrucksauswertung **</>** für den Pfad „Sprache“.
1. Kopieren Sie den Ausdruck. Schließen Sie das Fenster.
1. Wechseln Sie zu den Skilleinstellungen für die Qualifikation „#1“, und öffnen Sie die Ausdrucksauswertung **</>** für die Eingabe „languageCode“.
1. Fügen Sie den neuen Wert „/document/language“ in das Feld „Ausdruck“ ein, und klicken Sie auf **Auswerten**.
1. Es sollte die korrekte Eingabe „en“ angezeigt werden. Klicken Sie auf „Übernehmen“, um den Ausdruck zu aktualisieren.
1. Klicken Sie auf **Speichern** im rechten Qualifikationsdetailbereich.
1. Klicken Sie im Sitzungsfenster im Menü auf **Ausführen**. Dadurch wird eine weitere Ausführung des Skillsets unter Verwendung des Dokuments gestartet. 

Sobald die Ausführung der Debugsitzung abgeschlossen ist, klicken Sie auf die Registerkarte „Fehler/Warnungen“, und Sie werden sehen, dass der Fehler mit der Bezeichnung „Enrichment.NerSkillV2.#1“ verschwunden ist. Es gibt jedoch immer noch zwei Warnungen, dass der Dienst dem Suchindex keine Ausgabefelder für Organisationen und Standorte zuordnen konnte. Es fehlen Werte: „/document/merged_content/organizations“ und „/document/merged_content/locations“.

## <a name="fix-missing-skill-output-values"></a>Korrigieren fehlender Qualifikationsausgabewerte

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Fehler und Warnungen":::

Es fehlen Ausgabewerte von einer Qualifikation. Um die Qualifikation mit dem Fehler zu identifizieren, wechseln Sie zur angereicherten Datenstruktur, suchen Sie den Wertnamen, und schauen Sie sich die Ursprungsquelle an. Im Falle der fehlenden Werte für Organisationen und Standorte handelt es sich um Ausgaben der Qualifikation „#1“. Wenn Sie die Ausdrucksauswertung </> für jeden Pfad öffnen, werden die Ausdrücke als „/document/content/organisations“ bzw. „/document/content/locations“ angezeigt.

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Ausdrucksauswertung, Entität „Organisationen“":::

Die Ausgabe für diese Entitäten ist leer, darf aber nicht leer sein. Welches sind die Eingaben, die zu diesem Ergebnis führen?

1. Wechseln Sie zu **Skillgraph**, und wählen Sie Qualifikation „#1“ aus.
1. Wählen Sie die Registerkarte **Ausführungen** im rechten Qualifikationsdetailbereich aus.
1. Öffnen Sie die Ausdrucksauswertung **</>** für die Eingabe (INPUT) „Text“.

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Eingabe für die Qualifikation „Text“":::

Das angezeigte Ergebnis für diese Eingabe sieht nicht wie eine Texteingabe aus. Es sieht aus wie ein Bild, das von neuen Linien umgeben ist. Das Fehlen von Text bedeutet, dass keine Entitäten identifiziert werden können. Wenn Sie die Hierarchie des Skillsets betrachten, wird der Inhalt zunächst von der Qualifikation „#6“ (OCR) verarbeitet und dann an die Qualifikation „#5“ (Zusammenführung) weitergegeben. 

1. Wählen Sie die Qualifikation „#5“ (Zusammenführung) im **Skillgraph** aus.
1. Wählen Sie die Registerkarte **Ausführungen** im rechten Qualifikationsdetailbereich aus, und öffnen Sie die Ausdrucksauswertung **</>** für die Ausgaben (OUTPUTS) „mergedText“.

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Ausgabe für Qualifikation „Zusammenführung“":::

Hier wird der Text mit dem Bild gekoppelt. Wenn Sie den Ausdruck „/document/merged_content“ betrachten, ist der Fehler in den Pfaden „Unternehmen“ und „Standorte“ für die Qualifikation „#1“ sichtbar. Anstatt „/document/content“ sollte „/document/merged_content“ für die „Text“-Eingaben verwendet werden.

1. Kopieren Sie den Ausdruck für die Ausgabe „mergedText“, und schließen Sie das Fenster der Ausdrucksauswertung.
1. Wählen Sie im **Skillgraphen** die Qualifikation „#1“ aus.
1. Wählen Sie die Registerkarte **Skilleinstellungen** im rechten Qualifikationsdetailbereich aus.
1. Öffnen Sie die Ausdrucksauswertung **</>** für die „Text“-Eingabe.
1. Fügen Sie den neuen Ausdruck in das Feld ein. Klicken Sie auf **Auswerten**.
1. Die richtige Eingabe mit dem hinzugefügten Text sollte angezeigt werden. Klicken Sie auf **Übernehmen**, um die Skilleinstellungen zu aktualisieren.
1. Klicken Sie auf **Speichern** im rechten Qualifikationsdetailbereich.
1. Klicken Sie im Sitzungsfenster im Menü auf **Ausführen**. Dadurch wird eine weitere Ausführung des Skillsets unter Verwendung des Dokuments gestartet.

Nachdem die Ausführung des Indexers abgeschlossen ist, sind die Fehler weiterhin vorhanden. Wechseln Sie zurück zu Qualifikation „#1“, und untersuchen Sie sie. Die Eingabe für die Qualifikation wurde von „content“ zu „merged_content“ korrigiert. Welches sind die Ergebnisse für diese Entitäten in der Qualifikation?

1. Wählen Sie die Registerkarte **KI-Anreicherungen** aus.
1. Wählen Sie **Skillgraph** aus, und klicken Sie auf Qualifikation „#1“.
1. Navigieren Sie zu den **Skilleinstellungen**, um nach „Ausgaben“ zu suchen.
1. Öffnen Sie die Ausdrucksauswertung **</>** für die „Organisationen“-Entität.

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Ausgabe für „Organisationen“-Entität":::

Die Auswertung des Ergebnisses des Ausdrucks ergibt das richtige Ergebnis. Die Qualifikation dient dazu, den richtigen Wert für die Entität „Organisationen“ zu ermitteln. Die Ausgabezuordnung im Pfad der Entität löst jedoch immer noch einen Fehler aus. Beim Vergleich des Ausgabepfads in der Qualifikation mit dem Ausgabepfad im Fehler, ist die Qualifikation den Ausgaben, Organisationen und Standorten unter dem Knoten „/document/content“ übergeordnet. Die Ausgabefeldzuordnung erwartet hingegen, dass die Ergebnisse dem Knoten „/document/merged_content“ untergeordnet werden. Im vorherigen Schritt wurde die Eingabe von „/document/content“ in „/document/merged_content“ geändert. Der Kontext in den Skilleinstellungen muss geändert werden, um sicherzustellen, dass die Ausgabe mit dem richtigen Kontext erzeugt wird.

1. Wählen Sie die Registerkarte **KI-Anreicherungen** aus.
1. Wählen Sie **Skillgraph** aus, und klicken Sie auf Qualifikation „#1“.
1. Navigieren Sie zu den **Skilleinstellungen**, um nach „Kontext“ zu suchen.
1. Doppelklicken Sie auf die Einstellung für „Kontext“, und bearbeiten Sie sie so, dass „/document/merged_content“ gelesen wird.
1. Klicken Sie auf **Speichern** im rechten Qualifikationsdetailbereich.
1. Klicken Sie im Sitzungsfenster im Menü auf **Ausführen**. Dadurch wird eine weitere Ausführung des Skillsets unter Verwendung des Dokuments gestartet.

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Kontextkorrektur in Skilleinstellung":::

Alle Fehler wurden behoben.

## <a name="commit-changes-to-the-skillset"></a>Änderungen am Skillset committen

Beim Initiieren der Debugsitzung wurde vom Suchdienst eine Kopie des Skillsets erstellt. Dies wurde durchgeführt, damit sich vorgenommene Änderungen nicht auf das Produktionssystem auswirken. Nachdem Sie das Debugging Ihres Skillsets nun abgeschlossen haben, können Sie die Korrekturen in das Produktivsystem committen (und das ursprüngliche Skillset überschreiben). Wenn Sie weitere Änderungen am Skillset vornehmen möchten, ohne das sich diese auf das Produktionssystem auswirken, können Sie die Debugsitzung speichern und später wieder öffnen.

1. Klicken Sie im Hauptmenü der Debugsitzungen auf **Änderungen committen**.
1. Klicken Sie auf **OK**, um zu bestätigen, dass Sie Ihr Skillset aktualisieren möchten.
1. Schließen Sie die Debugsitzung, und wählen Sie die Registerkarte **Indexer** aus.
1. Öffnen Sie „clinical-trials-idxr“.
1. Klicken Sie auf **Zurücksetzen**.
1. Klicken Sie auf **Ausführen**. Klicken Sie auf **OK** , um zu bestätigen.

Wenn die Ausführung des Indexers beendet ist, sollte auf der Registerkarte „Ausführungshistorie“ ein grünes Häkchen und das Wort „Erfolg“ neben dem Zeitstempel der letzten Ausführung angezeigt werden. So stellen Sie sicher, dass die Änderungen übernommen wurden:

1. Beenden Sie den **Indexer**, und wählen Sie die Registerkarte **Index** aus.
1. Öffnen Sie den Index „clinical-trials“, und klicken Sie auf der Registerkarte „Suchexplorer“ auf **Suchen**.
1. Im Ergebnisfenster sollten Sie nun erkennen, dass die Entitäten „Organisationen“ und „Standorte“ nun mit den erwarteten Werten aufgefüllt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Skillsets](./cognitive-search-working-with-skillsets.md)
> [Weitere Informationen zu inkrementellen Anreicherungen und Caching](./cognitive-search-incremental-indexing-conceptual.md)