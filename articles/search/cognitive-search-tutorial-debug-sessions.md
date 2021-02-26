---
title: 'Tutorial: Verwenden von Debugsitzungen zum Korrigieren von Skillsets'
titleSuffix: Azure Cognitive Search
description: „Debugsitzungen“ (Vorschau) ist ein Tool im Azure-Portal, das zum Suchen, Diagnostizieren und Beheben von Problemen in einem Skillset verwendet wird.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509149"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Tutorial: Debuggen eines Skillsets mithilfe von Debugsitzungen

Mit Skillsets wird eine Reihe von Aktionen zum Analysieren oder Transformieren von Inhalten koordiniert, wobei die Ausgabe eines Skills zur Eingabe eines anderen wird. Wenn Eingaben von Ausgaben abhängen, können Fehler in Skillsetdefinitionen und Feldzuordnungen zu fehlenden Vorgängen und Daten führen.

**Debugsitzungen** im Azure-Portal ermöglichen eine ganzheitliche Visualisierung eines Skillsets. Mithilfe dieses Tools können Sie einen Drilldown für bestimmte Schritte ausführen, um leicht zu ermitteln, wo eine Aktion möglicherweise fehlschlägt.

In diesem Artikel verwenden Sie **Debugsitzungen**, um folgende Elemente zu suchen und zu korrigieren: 1) eine fehlende Eingabe und 2) Ausgabefeldzuordnungen. Das Tutorial ist umfassend. Es stellt Daten für die Indizierung (Daten klinischer Studien), eine Postman-Sammlung, die Objekte erstellt, und Anweisungen für die Verwendung von **Debugsitzungen** bereit, um Probleme im Skillset zu ermitteln und zu beheben.

> [!Important]
> Bei Debugsitzungen handelt es sich um eine Previewfunktion, die ohne Vereinbarung zum Servicelevel bereitgestellt wird und nicht für Produktionsworkloads vorgesehen ist. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen können, müssen die folgenden Voraussetzungen erfüllt werden:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ Ein Azure Storage-Konto mit [Blobspeicher](../storage/blobs/index.yml), das zum Hosten von Beispieldaten und zum dauerhaften Speichern temporärer Daten verwendet wird, die während einer Debugsitzung erstellt werden

+ [Postman-Desktop-App](https://www.getpostman.com/) und eine [Postman-Sammlung](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) zum Erstellen von Objekten mithilfe der REST-APIs

+ [Beispieldaten (klinische Studien)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19)

> [!NOTE]
> In diesem Schnellstart wird außerdem [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) für die KI verwendet. Aufgrund der geringen Workloadgröße wird Cognitive Services im Hintergrund für die kostenlose Verarbeitung von bis zu 20 Transaktionen genutzt. Das bedeutet, dass Sie diese Übung durchführen können, ohne eine zusätzliche Cognitive Services-Ressource erstellen zu müssen.

## <a name="set-up-your-data"></a>Einrichten Ihrer Daten

In diesem Abschnitt wird das Beispieldadaset in Azure-Blobspeicher erstellt, sodass Indexer und Skillset Inhalte aufweisen, mit denen Sie arbeiten können.

1. [Laden Sie Beispieldaten (clinical-trials-pdf-19) herunter](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), die aus 19 Dateien bestehen.

1. [Erstellen Sie ein Azure Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-portal), oder [suchen Sie nach einem vorhandenen Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Es muss sich in der gleichen Region wie Azure Cognitive Search befinden, um Bandbreitengebühren zu vermeiden.

   + Es muss den Kontotyp „StorageV2 (allgemein, Version 2)“ besitzen.

1. Navigieren Sie im Portal zu den Seiten mit den Azure Storage-Diensten, und erstellen Sie einen Blobcontainer. Eine bewährte Methode besteht darin, die Zugriffsebene „Privat“ anzugeben. Nennen Sie den Container `clinicaltrialdataset`.

1. Klicken Sie im Container auf **Hochladen**, um die im ersten Schritt heruntergeladenen und entpackte Beispieldateien hochzuladen.

1. Rufen Sie im Portal die Verbindungszeichenfolge für Azure Storage ab, und speichern Sie sie. Sie benötigen sie für die REST-API-Aufrufe, mit denen Daten indiziert werden. Sie können die Verbindungszeichenfolge im Portal unter **Einstellungen** > **Zugriffsschlüssel** abrufen.

## <a name="get-a-key-and-url"></a>Abrufen eines Schlüssels und einer URL

Für REST-Aufrufe sind die Dienst-URL und ein Zugriffsschlüssel für jede Anforderung erforderlich. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels" border="false":::

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="create-data-source-skillset-index-and-indexer"></a>Erstellen von Datenquellen, Skillset, Index und Indexer

In diesem Abschnitt werden Postman und eine bereitgestellte Sammlung verwendet, um die Datenquelle, das Skillset, den Index und den Indexer von Cognitive Search zu erstellen. Wenn Sie mit Postman nicht vertraut sind, sehen Sie sich [diese Schnellstartanleitung](search-get-started-rest.md) an.

Sie benötigen die für dieses Tutorial erstellte [Postman-Sammlung](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions), um diese Aufgabe abzuschließen. 

1. Starten Sie Postman, und importieren Sie die Sammlung. Wählen Sie unter **Dateien** > **Neu** die Sammlung aus, die importiert werden soll.
1. Nachdem die Sammlung importiert wurde, erweitern Sie die Liste der Aktionen (...).
1. Klicken Sie auf **Bearbeiten**.
1. Geben Sie unter „Aktueller Wert“ den Namen Ihres Suchdiensts (`searchService`) ein. (Ist der Endpunkt etwa `https://mydemo.search.windows.net`, lautet der Dienstname `mydemo`.)
1. Geben Sie für `apiKey` den primären oder sekundären Schlüssel Ihres Suchdiensts ein.
1. Geben Sie die Speicher-Verbindungszeichenfolge (`storageConnectionString`) von der Schlüsselseite Ihres Azure Storage-Kontos ein.
1. Geben Sie den Containernamen (`containerName`) für den Container ein, den Sie im Speicherkonto erstellt haben, und klicken Sie auf **Aktualisieren**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Bearbeiten von Variablen in Postman":::

Die Sammlung enthält vier verschiedene REST-Aufrufe, die zum Erstellen der in diesem Tutorial verwendeten Objekte verwendet werden.

Der erste Befehl erstellt die Datenquelle: `clinical-trials-ds`. Der zweite Befehl erstellt das Skillset: `clinical-trials-ss`. Der dritte Befehl erstellt den Index: `clinical-trials`. Der vierte und letzte Befehl erstellt den indexer: `clinical-trials-idxr`.

+ Öffnen Sie nacheinander alle Anforderungen, und klicken Sie auf **Senden**, um jede Anforderung an den Suchdienst zu senden. 

Nachdem alle Aufrufe in der Sammlung abgeschlossen wurden, schließen Sie Postman, und kehren Sie zum Azure-Portal zurück.

## <a name="check-results-in-the-portal"></a>Überprüfen der Ergebnisse im Portal

Der Beispielcode erstellt als Folge der Probleme, die bei der Skillsetausführung aufgetreten sind, absichtlich einen fehlerhaften Index. Das Problem besteht darin, dass Daten fehlen. 

1. Wählen Sie im Azure-Portal auf der Seite „Übersicht“ des Suchdiensts die Registerkarte **Indizes** aus. 
1. Wählen Sie den Index `clinical-trials` aus.
1. Geben Sie die Abfragezeichenfolge `$select=metadata_storage_path, organizations, locations&$count=true` ein, um Felder für bestimmte Dokumente (identifiziert durch das eindeutige Feld `metadata_storage_path`) zurückzugeben.
1. Klicken Sie auf **Suche**, um die Abfrage auszuführen. Dabei werden alle 19 Dokumente zurückgegeben und leere Werte für „organizations“ und „locations“ angezeigt.

Diese Felder sollten durch den [Skill „Entitätserkennung“](cognitive-search-skill-entity-recognition.md) des Skillsets aufgefüllt worden sein, der zum Suchen von Organisationen und Standorten überall im Blobinhalt verwendet wurde. In der nächsten Übung verwenden Sie „Debugsitzung“, um zu ermitteln, welche Fehler aufgetreten sind.

Fehler und Warnungen können auch im Azure-Portal untersucht werden.

1. Öffnen Sie die Registerkarte **Indexer**, und wählen Sie `clinical-trials-idxr` aus.
1. Beachten Sie, dass der Indexerauftrag insgesamt zwar erfolgreich ausgeführt wurde, aber 57 Warnungen ausgegeben wurden.
1. Klicken Sie auf **Erfolg**, um die Warnungen anzuzeigen. (Liegen überwiegend Fehler vor, würde der Detaillink **Fehler** lauten.) Sie sehen eine lange Liste aller vom Indexer ausgegebenen Warnungen.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="Anzeigen von Warnungen":::

## <a name="start-your-debug-session"></a>Starten Sie Ihre Debugsitzung.

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Starten Sie eine neue Debugsitzung":::.

1. Klicken Sie auf der Seite „Übersicht“ der Suche auf die Registerkarte **Debugsitzungen**.
1. Wählen Sie **+ Neue Debugsitzung** aus.
1. Geben Sie der Sitzung einen Namen. 
1. Verbinden Sie die Sitzung mit Ihrem Speicherkonto. 
1. Geben Sie in der Indexervorlage den Indexernamen an. Der Indexer verweist auf die Datenquelle, das Skillset und den Index.
1. Akzeptieren Sie die Standarddokumentauswahl für das erste Dokument in der Sammlung. 
1. **Speichern** Sie die Sitzung. Durch das Speichern der Sitzung wird die KI-Anreicherungspipeline gestartet, wie durch das Skillset definiert.

> [!Important]
> Eine Debugsitzung funktioniert nur mit einem einzelnen Dokument. Sie können auswählen, für welches Dokument das Debugging durchgeführt werden soll, oder nur das erste verwenden.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Wenn die Initialisierung der Debugsitzung beendet ist, wird die Sitzung standardmäßig auf die Registerkarte **KI-Anreicherungen** gesetzt, wobei der **Skillgraph** hervorgehoben wird. Der Skillgraph bietet eine visuelle Hierarchie des Skillsets und seiner Ausführungsreihenfolge nacheinander und parallel.

## <a name="find-issues-with-the-skillset"></a>Ermitteln von Problemen mit dem Skillset

Alle vom Indexer gemeldeten Probleme finden Sie auf der danebenliegenden Registerkarte **Fehler/Warnungen**. 

Beachten Sie, dass die Registerkarte **Fehler/Warnungen** eine viel kleinere Liste als die zuvor angezeigte enthält, da diese Liste nur die Details zu den Fehlern für ein einzelnes Dokument aufführt. Wie bei der vom Indexer angezeigten Liste können Sie auf eine Warnmeldung klicken und Details zu dieser Warnung anzeigen.

Wählen Sie **Fehler/Warnungen** aus, um die Benachrichtigungen zu überprüfen. Drei sollten angezeigt werden:

   + „Could not map output field 'locations' to search index. Check the 'outputFieldMappings' property of your indexer.
Missing value '/document/merged_content/locations'.“ (Das Ausgabefeld 'locations' konnte dem Suchindex nicht zugeordnet werden. Überprüfen Sie die Eigenschaft 'outputFieldMappings' des Indexers. Fehlender Wert für '/document/merged_content/locations'.)

   + „Could not map output field 'organizations' to search index. Check the 'outputFieldMappings' property of your indexer.
Fehlender Wert für '/document/merged_content/organizations'.“ (Das Ausgabefeld 'organizations' konnte dem Suchindex nicht zugeordnet werden. Überprüfen Sie die Eigenschaft 'outputFieldMappings' des Indexers. Fehlender Wert für '/document/merged_content/organizations'.)

   + „Skill executed but may have unexpected results because one or more skill inputs was invalid.
Optional skill input is missing. Name: 'languageCode', Source: '/document/languageCode'. Expression language parsing issues: Missing value '/document/languageCode'.“ (Der Skill wurde ausgeführt, hat jedoch unter Umständen unerwartete Ergebnisse, da mindestens eine Skilleingabe ungültig war. Eine optionale Skilleingabe fehlt. Name: 'languageCode', Quelle: '/document/languageCode'. Probleme bei der Analyse der Ausdruckssprache. Fehlender Wert für '/document/languageCode'.)

   Viele Skills verfügen über den Parameter „languageCode“. Wenn Sie den Vorgang überprüfen, sehen Sie, dass diese Sprachcodeeingabe in `Enrichment.NerSkillV2.#1` fehlt. Dabei handelt es sich um denselben Skill für die Entitätserkennung, bei der Probleme mit der Ausgabe von „locations“ und „organizations“ auftreten. 

Da alle drei Benachrichtigungen zu diesem Skill gehören, besteht der nächste Schritt im Debuggen dieses Skills. Beginnen Sie nach Möglichkeit mit der Behebung von Eingabeproblemen, und fahren Sie dann mit Problemen vom Typ „outputFieldMapping“ fort.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Eine neue Debugsitzung wurde gestartet":::.

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Korrigieren fehlender Qualifikationseingabewerte

Auf der Registerkarte **Fehler/Warnungen** gibt es einen Fehler für einen Vorgang mit der Bezeichnung `Enrichment.NerSkillV2.#1`. Die Details zu diesem Fehler erläutern, dass ein Problem mit einem Qualifikationseingabewert „/document/languageCode“ vorliegt. 

1. Kehren Sie zur Registerkarte **KI-Anreicherungen** zurück.
1. Klicken Sie auf den **Skillgraphen**.
1. Klicken Sie auf den Skill mit der Bezeichnung **#1**, um seine Details im rechten Bereich anzuzeigen.
1. Suchen Sie die Eingabe für „languageCode“.
1. Wählen Sie das Symbol **</>** am Anfang der Zeile aus, und öffnen Sie die Ausdrucksauswertung.
1. Klicken Sie auf die Schaltfläche **Auswerten**, um zu überprüfen, ob dieser Ausdruck einen Fehler erzeugt. Es zeigt sich, dass die Eigenschaft „languageCode“ keine gültige Eingabe ist.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Ausdrucksauswertung":::

Es gibt zwei Möglichkeiten, diesen Fehler in der Sitzung zu recherchieren. Die erste besteht darin, zu untersuchen, woher die Eingabe stammt. Welche Qualifikation in der Hierarchie sollte dieses Ergebnis erzeugen? Auf der Registerkarte „Ausführungen“ sollte die Quelle der Eingabe im Detailbereich der Qualifikation angezeigt werden. Wenn keine Quelle vorhanden ist, deutet dies auf einen Feldzuordnungsfehler hin.

1. Klicken Sie auf die Registerkarte **Ausführungen**.
1. Schauen Sie sich die INPUTS an, und suchen Sie „languageCode“. Für diese Eingabe ist keine Quelle aufgeführt. 
1. Wechseln Sie in den linken Fensterbereich, um die angereicherte Datenstruktur anzuzeigen. Es gibt keinen zugeordneten Pfad, der „languageCode“ entspricht.

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Angereicherte Datenstruktur":::

Es gibt einen zugeordneten Pfad für „Sprache“. Es liegt also einen Tippfehler in den Skilleinstellungen vor. Um dies zu beheben, muss der Ausdruck im Skill „#1“ mit dem Ausdruck „/document/language“ aktualisiert werden.

1. Öffnen Sie die Ausdrucksauswertung **</>** für den Pfad „Sprache“.
1. Kopieren Sie den Ausdruck. Schließen Sie das Fenster.
1. Wechseln Sie zu den Skilleinstellungen für die Qualifikation „#1“, und öffnen Sie die Ausdrucksauswertung **</>** für die Eingabe „languageCode“.
1. Fügen Sie den neuen Wert „/document/language“ in das Feld „Ausdruck“ ein, und klicken Sie auf **Auswerten**.
1. Es sollte die korrekte Eingabe „en“ angezeigt werden. Klicken Sie auf „Übernehmen“, um den Ausdruck zu aktualisieren.
1. Klicken Sie auf **Speichern** im rechten Qualifikationsdetailbereich.
1. Klicken Sie im Sitzungsfenster im Menü auf **Ausführen**. Dadurch wird eine weitere Ausführung des Skillsets unter Verwendung des Dokuments gestartet. 

Sobald die Ausführung der Debugsitzung abgeschlossen ist, klicken Sie auf die Registerkarte „Fehler/Warnungen“, und Sie werden sehen, dass der Fehler mit der Bezeichnung „Enrichment.NerSkillV2.#1“ verschwunden ist. Es gibt jedoch immer noch zwei Warnungen, dass der Dienst dem Suchindex keine Ausgabefelder für Organisationen und Standorte zuordnen konnte. Es fehlen Werte: „/document/merged_content/organizations“ und „/document/merged_content/locations“.

## <a name="fix-missing-skill-output-values"></a>Korrigieren fehlender Qualifikationsausgabewerte

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Fehler und Warnungen":::

Es fehlen Ausgabewerte von einer Qualifikation. Um die Qualifikation mit dem Fehler zu identifizieren, wechseln Sie zur angereicherten Datenstruktur, suchen Sie den Wertnamen, und schauen Sie sich die Ursprungsquelle an. Im Falle der fehlenden Werte für Organisationen und Standorte handelt es sich um Ausgaben der Qualifikation „#1“. Wenn Sie die Ausdrucksauswertung </> für jeden Pfad öffnen, werden die Ausdrücke als „/document/content/organisations“ bzw. „/document/content/locations“ angezeigt.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Ausdrucksauswertung, Entität „Organisationen“":::

Die Ausgabe für diese Entitäten ist leer, darf aber nicht leer sein. Welches sind die Eingaben, die zu diesem Ergebnis führen?

1. Wechseln Sie zu **Skillgraph**, und wählen Sie Qualifikation „#1“ aus.
1. Wählen Sie die Registerkarte **Ausführungen** im rechten Qualifikationsdetailbereich aus.
1. Öffnen Sie die Ausdrucksauswertung **</>** für die Eingabe (INPUT) „Text“.

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Eingabe für die Qualifikation „Text“":::

Das angezeigte Ergebnis für diese Eingabe sieht nicht wie eine Texteingabe aus. Es sieht aus wie ein Bild, das von neuen Linien umgeben ist. Das Fehlen von Text bedeutet, dass keine Entitäten identifiziert werden können. Wenn Sie die Hierarchie des Skillsets betrachten, wird der Inhalt zunächst von der Qualifikation „#6“ (OCR) verarbeitet und dann an die Qualifikation „#5“ (Zusammenführung) weitergegeben. 

1. Wählen Sie die Qualifikation „#5“ (Zusammenführung) im **Skillgraph** aus.
1. Wählen Sie die Registerkarte **Ausführungen** im rechten Qualifikationsdetailbereich aus, und öffnen Sie die Ausdrucksauswertung **</>** für die Ausgaben (OUTPUTS) „mergedText“.

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Ausgabe für Qualifikation „Zusammenführung“":::

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

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Ausgabe für „Organisationen“-Entität":::

Die Auswertung des Ergebnisses des Ausdrucks ergibt das richtige Ergebnis. Die Qualifikation dient dazu, den richtigen Wert für die Entität „Organisationen“ zu ermitteln. Die Ausgabezuordnung im Pfad der Entität löst jedoch immer noch einen Fehler aus. Beim Vergleich des Ausgabepfads in der Qualifikation mit dem Ausgabepfad im Fehler, ist die Qualifikation den Ausgaben, Organisationen und Standorten unter dem Knoten „/document/content“ übergeordnet. Die Ausgabefeldzuordnung erwartet hingegen, dass die Ergebnisse dem Knoten „/document/merged_content“ untergeordnet werden. Im vorherigen Schritt wurde die Eingabe von „/document/content“ in „/document/merged_content“ geändert. Der Kontext in den Skilleinstellungen muss geändert werden, um sicherzustellen, dass die Ausgabe mit dem richtigen Kontext erzeugt wird.

1. Wählen Sie die Registerkarte **KI-Anreicherungen** aus.
1. Wählen Sie **Skillgraph** aus, und klicken Sie auf Qualifikation „#1“.
1. Navigieren Sie zu den **Skilleinstellungen**, um nach „Kontext“ zu suchen.
1. Doppelklicken Sie auf die Einstellung für „Kontext“, und bearbeiten Sie sie so, dass „/document/merged_content“ gelesen wird.
1. Klicken Sie auf **Speichern** im rechten Qualifikationsdetailbereich.
1. Klicken Sie im Sitzungsfenster im Menü auf **Ausführen**. Dadurch wird eine weitere Ausführung des Skillsets unter Verwendung des Dokuments gestartet.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Kontextkorrektur in Skilleinstellung":::

Alle Fehler wurden behoben.

## <a name="commit-changes-to-the-skillset"></a>Änderungen am Skillset committen

Beim Initiieren der Debugsitzung wurde vom Suchdienst eine Kopie des Skillsets erstellt. Dieser Schritt wurde ausgeführt, um das ursprüngliche Skillset für Ihren Suchdienst zu schützen. Nachdem Sie das Debugging Ihres Skillsets nun abgeschlossen haben, können Sie die Korrekturen committen (und das ursprüngliche Skillset überschreiben). 

Wenn Sie die Änderungen noch nicht committen möchten, können Sie alternativ die Debugsitzung speichern und später erneut öffnen.

1. Klicken Sie im Hauptmenü der Debugsitzungen auf **Änderungen committen**.
1. Klicken Sie auf **OK**, um zu bestätigen, dass Sie Ihr Skillset aktualisieren möchten.
1. Schließen Sie die Debugsitzung, und wählen Sie die Registerkarte **Indexer** aus.
1. Öffnen Sie „clinical-trials-idxr“.
1. Klicken Sie auf **Zurücksetzen**.
1. Klicken Sie auf **Ausführen**. Klicken Sie auf **OK** , um zu bestätigen.

Wenn die Ausführung des Indexers beendet ist, sollte auf der Registerkarte **Ausführungshistorie** ein grünes Häkchen und das Wort „Erfolg“ neben dem Zeitstempel der letzten Ausführung angezeigt werden. So stellen Sie sicher, dass die Änderungen übernommen wurden:

1. Wählen Sie auf der Seite „Übersicht“ für die Suche die Registerkarte **Index** aus.
1. Öffnen Sie den Index „clinical-trials“, und geben Sie auf der Registerkarte „Suchexplorer“ die Abfragezeichenfolge `$select=metadata_storage_path, organizations, locations&$count=true` ein, um Felder für bestimmte Dokumente (identifiziert durch das eindeutige Feld `metadata_storage_path`) zurückzugeben.
1. Klicken Sie auf **Suchen**.

In den Ergebnissen sollten Sie nun erkennen, dass Organisationen und Standorte mit den erwarteten Werten aufgefüllt wurden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden verschiedene Aspekte der Definition und Verarbeitung von Skillsets angesprochen. Weitere Informationen zu Konzepten und Workflows finden Sie in den folgenden Artikeln:

+ [Zuordnen von mit KI angereicherten Feldern zu einem durchsuchbaren Index](cognitive-search-output-field-mapping.md)

+ [Skillsetkonzepte in Azure Cognitive Search](cognitive-search-working-with-skillsets.md)

+ [Inkrementelle Anreicherung und Zwischenspeicherung in Azure Cognitive Search](cognitive-search-incremental-indexing-conceptual.md)