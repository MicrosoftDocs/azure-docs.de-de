---
title: 'Häufig gestellte Fragen (FAQ): LUIS'
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: e803fa393d85f7f0b4849aed96356b7a4e77d83f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96000129"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding: häufig gestellte Fragen (FAQ)

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Language Understanding (LUIS).

## <a name="whats-new"></a>Neues

[Erfahren Sie mehr über](whats-new.md) Neuerungen in Language Understanding (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Erstellen

### <a name="what-are-the-luis-best-practices"></a>Welche Best Practices gibt es für LUIS?
Lesen Sie zunächst [Authoring Cycle](luis-concept-app-iteration.md) (Erstellungszyklus) und anschließend [Best Practices](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Wie sollte ich bei der Erstellung einer LUIS-App vorgehen?

Bei der App-Erstellung sollte ein [inkrementeller Prozess](luis-concept-app-iteration.md) verwendet werden.

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Wie sollte ich Absichten in meiner App modellieren? Sind spezifische oder allgemeine Absichten zu bevorzugen?

Die Absichten sollten nicht so allgemein sein, dass Überlappungen auftreten. Sie sollten aber auch nicht so spezifisch sein, dass LUIS nicht mehr zwischen ähnlichen Absichten unterscheiden kann. Als Best Practice für die LUIS-Modellierung empfiehlt sich die Erstellung spezifischer, aber unterscheidbarer Absichten.

### <a name="is-it-important-to-train-the-none-intent"></a>Sollte die Absicht „None“ (Keine) trainiert werden?

Ja, beim Hinzufügen von weiteren Bezeichnungen für andere Absichten sollten Sie auch die Absicht **None** mit zusätzlichen Äußerungen trainieren. Für jede zehnte Absichtsbezeichnung sollten ein oder zwei Bezeichnungen zu **None** hinzugefügt werden. Durch dieses Verhältnis wird die Unterscheidungsfähigkeit von LUIS optimiert.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Wie lassen sich Rechtschreibfehler in Äußerungen korrigieren?

Informationen hierzu finden Sie im Tutorial zur [Bing-Rechtschreibprüfungs-API v7](luis-tutorial-bing-spellcheck.md). LUIS setzt die Einschränkungen um, die durch die Bing-Rechtschreibprüfungs-API v7 vorgegeben werden.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Wie kann ich eine LUIS-App programmgesteuert bearbeiten?
Sie können Ihre LUIS-App mit der [Erstellungs-API](https://go.microsoft.com/fwlink/?linkid=2092087) programmgesteuert bearbeiten. Unter [Aufrufen der Erstellungs-API für LUIS-Apps](./get-started-get-model-rest-apis.md) und [Build a LUIS app programmatically using Node.js (Programmgesteuertes Erstellen einer LUIS-App mit Node.js)](./luis-tutorial-node-import-utterances-csv.md) finden Sie Beispiele zum Aufrufen der Erstellungs-API. Die Erstellungs-API setzt voraus, dass Sie einen [Erstellungsschlüssel](luis-how-to-azure-subscription.md#azure-resources-for-luis) anstelle eines Endpunktschlüssels verwenden. Bei der programmgesteuerte Erstellung sind bis 1.000.000 Aufrufe pro Monat und fünf Transaktionen pro Sekunde möglich. Weitere Informationen zu den mit LUIS verwendeten Schlüsseln finden Sie unter [Verwalten von Schlüsseln](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Wo finde ich das Feature für Muster, mit den bisher nach regulären Ausdrücken gesucht werden konnte?
Das bisherige **Feature für Muster** ist als veraltet gekennzeichnet und wurde durch **[Muster](luis-concept-patterns.md)** ersetzt.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Wie extrahiere ich mithilfe von Entitäten bestimmte Daten?
Informationen hierzu finden Sie unter [Entitäten](luis-concept-entity-types.md) und [Datenextraktion](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Sollten Variationen einer Beispieläußerung Satzzeichen enthalten?
Verwenden Sie eine der folgenden Lösungen:
* Ignorieren Sie die [Interpunktion](luis-reference-application-settings.md#punctuation-normalization).
* Fügen Sie der Absicht die verschiedenen Variationen als Beispieläußerungen hinzu.
* Fügen Sie das Muster der Beispieläußerung mit der [Syntax zum Ignorieren](luis-concept-patterns.md#pattern-syntax) der Interpunktion hinzu.

### <a name="does-luis-currently-support-cortana"></a>Unterstützt LUIS derzeit Cortana?

Vorgefertigte Cortana-Apps wurden 2017 eingestellt. Sie werden nicht mehr unterstützt.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Wie übertrage ich eine LUIS-App an einen anderen Besitzer?
Wenn Sie eine LUIS-App an ein anderes Azure-Abonnement übertragen möchten, müssen Sie die App exportieren und mit einem neuen Konto importieren. Aktualisieren Sie die LUIS-App-ID in der Clientanwendung, die die ID aufruft. Die neue App gibt möglicherweise LUIS-Bewertungen zurück, die sich geringfügig von den Werten der ursprünglichen App unterscheiden.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Eine vordefinierte Entität wird in einer Beispieläußerung anstelle meiner benutzerdefinierten Entität markiert. Wie kann ich dies korrigieren?

Im LUIS-Portal können Sie Text für genau die Entität bezeichnen, die Sie extrahieren möchten. Sollte im LUIS-Portal nicht die richtige Entitätsvorhersage angezeigt werden, müssen Sie möglicherweise weitere Äußerungen hinzufügen und die Entität innerhalb des Texts beschriften oder ein Feature hinzufügen.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Ich habe versucht, eine App- oder Versionsdatei zu importieren, aber eine Fehlermeldung erhalten. Was ist passiert?

Erfahren Sie mehr über [Fehler beim Versionsimport](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Zusammenarbeiten und mitwirken

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Wie weise ich Mitarbeitern Zugriff auf LUIS mit Azure Active Directory (Azure AD) oder der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) zu?

Unter [Azure Active Directory-Ressourcen](luis-how-to-collaborate.md#azure-active-directory-resources) und [Azure Active Directory-Mandantenbenutzer](luis-how-to-collaborate.md#azure-active-directory-tenant-user) erfahren Sie, wie Sie Mitarbeitern den Zugriff gewähren.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Endpunkt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Ich habe einen Fehler mit dem Statuscode HTTP 403 erhalten. Wie behebe ich das Problem?

Sie erhalten Fehler mit dem Statuscode 403 und 429, wenn Sie für Ihren Tarif die Transaktionen pro Sekunde oder Monat überschreiten. Erhöhen Sie Ihren Tarif, oder verwenden Sie Language Understanding-[Container](luis-container-howto.md).

Wenn Sie die gesamten kostenlosen 1.000 Endpunktabfragen aufgebraucht haben oder das Kontingent der monatlichen Transaktionen Ihres Tarifs überschreiten, erhalten Sie einen Fehler mit dem Statuscode HTTP 403.

Zum Beheben dieses Fehlers müssen Sie entweder [einen höheren Tarif wählen](luis-how-to-azure-subscription.md#change-the-pricing-tier) oder [eine neue Ressource erstellen](get-started-portal-deploy-app.md#create-the-endpoint-resource) und [Ihrer App zuweisen](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Lösungen für diesen Fehler:

* Wählen Sie im [Azure-Portal](https://portal.azure.com) für Ihre Language Understanding-Ressource unter **Ressourcenverwaltung > Tarif** einen höheren TPS-Tarif. Sie müssen im Language Understanding-Portal nichts unternehmen, wenn Ihre Ressource bereits Ihrer Language Understanding-App zugewiesen ist.
*  Falls Ihre Nutzung den höchsten Tarif übersteigt, sollten Sie weitere Language Understanding-Ressourcen mit vorgeschaltetem Lastenausgleich hinzufügen. Hierbei kann der [Language Understanding-Container](luis-container-howto.md) mit Kubernetes oder Docker Compose hilfreich sein.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Ich habe einen Fehler mit dem Statuscode HTTP 429 erhalten. Wie behebe ich das Problem?

Sie erhalten Fehler mit dem Statuscode 403 und 429, wenn Sie für Ihren Tarif die Transaktionen pro Sekunde oder Monat überschreiten. Erhöhen Sie Ihren Tarif, oder verwenden Sie Language Understanding-[Container](luis-container-howto.md).

Dieser Statuscode wird zurückgegeben, wenn Ihre Transaktionen pro Sekunde Ihren Tarif überschreiten.

Beispiele für Lösungen sind:

* Sie können [Ihren Tarif erhöhen](luis-how-to-azure-subscription.md#change-the-pricing-tier), falls Sie noch nicht den höchsten Tarif verwenden.
* Falls Ihre Nutzung den höchsten Tarif übersteigt, sollten Sie weitere Language Understanding-Ressourcen mit vorgeschaltetem Lastenausgleich hinzufügen. Hierbei kann der [Language Understanding-Container](luis-container-howto.md) mit Kubernetes oder Docker Compose hilfreich sein.
* Sie können Ihre Anforderungen der Clientanwendung mit einer [Wiederholungsrichtlinie](/azure/architecture/best-practices/transient-faults#general-guidelines) versehen, die Sie selbst implementieren, wenn Sie diesen Statuscode erhalten.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Meine Endpunktabfrage hat unerwartete Ergebnisse zurückgegeben. Wie sollte ich vorgehen?

Unerwartete Abfragevorhersageergebnisse basieren auf dem Zustand des veröffentlichten Modells. Um das Modell zu korrigieren, müssen Sie ggf. das Modell ändern, trainieren und erneut veröffentlichen.

Korrigieren des Modells beginnt mit [aktivem Lernen](luis-how-to-review-endpoint-utterances.md).

Um das nicht deterministische Training zu entfernen, können Sie die [Anwendungsversionseinstellungen-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) aktualisieren, um alle Trainingsdaten zu verwenden.

Weitere Tipps finden Sie in den [Best Practices](luis-concept-best-practices.md).

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Warum fügt LUIS vor und nach Wörtern oder in der Mitte von Wörtern Leerzeichen in die Abfrage ein?
LUIS nutzt für die [Tokenisierung](luis-language-support.md#tokenization) der Äußerung deren [Kultur](luis-glossary.md#token). Sowohl der ursprüngliche Wert als auch der nach der Tokenisierung vorhandene Wert können [extrahiert](luis-concept-data-extraction.md#tokenized-entity-returned) werden.

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Wie lässt sich ein LUIS-Endpunkt erstellen und zuweisen?
Erstellen Sie zunächst für Ihren [Servicelevel](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) den [Endpunktschlüssel](luis-how-to-azure-subscription.md) in Azure. [Weisen Sie den Schlüssel](luis-how-to-azure-subscription.md) auf der Seite **[Azure-Ressourcen](luis-how-to-azure-subscription.md)** zu. Für diese Aktion steht keine API zur Verfügung. Danach müssen Sie die HTTP-Anforderung an den Endpunkt so ändern, dass der [neue Endpunktschlüssel](luis-how-to-azure-subscription.md) verwendet wird.

### <a name="how-do-i-interpret-luis-scores"></a>Wie lassen sich LUIS-Bewertungen interpretieren?
Ihr System sollte die am höchsten bewertete Absicht unabhängig vom Wert verwenden. Ein Wert unter 0,5 (weniger als 50 %) ist nicht zwangsläufig mit einer geringen Zuverlässigkeit von LUIS gleichzusetzen. Durch das Bereitstellen weiterer Trainingsdaten kann die [Bewertung](luis-concept-prediction-score.md) für die wahrscheinlichste Absicht verbessert werden.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Warum werden Endpunktabrufe nicht im App-Dashboard angezeigt?
Die Gesamtzahl der Endpunktabrufe in Ihrem App-Dashboard wird regelmäßig aktualisiert. Die Metriken, die dem LUIS-Endpunktschlüssel im Azure-Portal zugeordnet sind, werden jedoch häufiger aktualisiert.

Wenn die Endpunktabrufe im Dashboard nicht aktualisiert werden, melden Sie sich beim Azure-Portal an, suchen Sie nach der Ressource, die Ihrem LUIS-Endpunktschlüssel zugeordnet ist. Öffnen Sie **Metriken**, und wählen Sie die Metrik **Aufrufe gesamt** aus. Wenn der Endpunktschlüssel für mehr als eine LUIS-App verwendet wird, zeigt die Metrik im Azure-Portal die aggregierten Aufrufe durch alle LUIS-Apps an, die den Schlüssel verwenden.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Gibt es einen PowerShell-Befehl zum Abrufen des Endpunktkontingents?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sie können einen PowerShell-Befehl verwenden, um das Endpunktkontingent anzuzeigen:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Bisher hat meine LUIS-App problemlos funktioniert. Nun werden jedoch 403-Fehlermeldungen angezeigt. Ich habe die App nicht geändert. Wie behebe ich das Problem?
Befolgen Sie [diese Anweisungen](#how-do-i-create-and-assign-a-luis-endpoint-key), um einen LUIS-Endpunktschlüssel zu erstellen und ihn der App zuzuweisen. Danach müssen Sie die HTTP-Anforderung der Clientanwendung an den Endpunkt so ändern, dass der [neue Endpunktschlüssel](luis-how-to-azure-subscription.md) verwendet wird. Wenn Sie eine neue Ressource in einer anderen Region erstellt haben, ändern Sie auch die Region der HTTP-Clientanforderung.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Wie sichere ich meinen LUIS-Endpunkt?
Informationen hierzu finden Sie unter [Securing the endpoint (Schützen von Endpunkten)](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>LUIS-Grenzwerte

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Wie viele Absichten und Entitäten kann eine LUIS-App maximal unterstützen?
Informationen hierzu finden Sie in der Referenzdokumentation zu [Begrenzungen](luis-limits.md).

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Ich möchte eine LUIS-App erstellen, die mehr als die maximale Absichtsanzahl unterstützt. Wie sollte ich vorgehen?

Informationen hierzu finden Sie unter [Best Practices für Absichten](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Ich möchte eine LUIS-App erstellen, die mehr als die maximale Entitätszahl unterstützt. Wie sollte ich vorgehen?

Informationen hierzu finden Sie unter [Best Practices für Entitäten](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities).

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Welche Grenzwerte gelten für die Anzahl und den Umfang von Begriffslisten?
Informationen zur maximalen Länge einer [Begriffsliste](./luis-concept-feature.md) finden Sie in der Referenzdokumentation zu [Begrenzungen](luis-limits.md).

### <a name="what-are-the-limits-on-example-utterances"></a>Welche Grenzwerte gelten für Beispieläußerungen?
Informationen hierzu finden Sie in der Referenzdokumentation zu [Begrenzungen](luis-limits.md).

## <a name="testing-and-training"></a>Testen und Trainieren von LUIS

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>In meinem Batchtestbereich werden mehrere Fehler für Modelle in meiner App angezeigt. Wie kann ich dieses Problem beheben?

Diese Fehler deuten darauf hin, dass eine Diskrepanz zwischen den Bezeichnungen und den Modellvorhersagen besteht. Führen Sie einen oder beide Schritte aus, um das Problem zu beheben:
* Fügen Sie weitere Bezeichnungen hinzu, damit LUIS Absichten besser unterscheiden kann.
* Fügen Sie Begriffslistenfeatures mit Fachwortschatz hinzu, damit LUIS schneller lernt.

Informationen hierzu finden Sie im Tutorial [Batchtests](luis-tutorial-batch-testing.md).

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Wenn eine App exportiert und anschließend in eine neue App (mit einer neuen App-ID) reimportiert wird, unterscheiden sich die LUIS-Vorhersagebewertungen. Was ist dafür die Ursache?

Informationen hierzu finden Sie unter [Vorhersageunterschiede zwischen Kopien derselben App](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Einige Äußerungen gehen in die falsche Richtung, nachdem Änderungen an meiner App vorgenommen wurden. Das Problem scheint willkürlich zu verschwinden. Wie behebe ich das Problem?

Weitere Informationen finden Sie unter [Trainieren mit allen Daten](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Veröffentlichen von Apps

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Was ist die Mandanten-ID im Fenster „Add a key to your app“ (Schlüssel zur App hinzufügen)?
In Azure stellen Mandanten Clients oder Organisationen dar, die einem Dienst zugeordnet sind. Ihre Mandanten-ID finden Sie im Azure-Portal im Feld **Verzeichnis-ID**. Dieses rufen Sie über **Azure Active Directory** > **Verwalten** > **Eigenschaften** auf.

![Mandanten-ID im Azure-Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Warum sind meiner App mehr Endpunkte zugewiesen, als ich zugewiesen habe?
Jede LUIS-App enthält in der Endpunktliste zur Vereinfachung den Erstellungs-/Startschlüssel. Dieser Schlüssel lässt nur einige Endpunkttreffer zu, damit Sie LUIS ausprobieren können.

Wenn Ihre App bereits vor der allgemeinen Verfügbarkeit von LUIS bestand, wurden LUIS-Endpunktschlüssel in Ihrem Abonnement automatisch zugewiesen. Dadurch soll die GA-Migration erleichtert werden. Alle neuen LUIS-Endpunktschlüssel im Azure-Portal wurden LUIS _nicht_ automatisch zugewiesen.

## <a name="key-management"></a>Schlüsselverwaltung

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Wie erfahre ich, welchen Schlüssel ich benötige, wo ich diesen erhalte und wie ich ihn verwende?

Informationen zu den Unterschieden zwischen dem Erstellungsschlüssel und dem Vorhersage-Laufzeitschlüssel finden Sie unter [Erstellungsschlüssel und Endpunktschlüssel für Abfragevorhersagen in LUIS](luis-how-to-azure-subscription.md).

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Ich habe eine Fehlermeldung erhalten, dass nicht genügend Kontingent vorhanden ist. Wie behebe ich das Problem?

Weitere Informationen finden Sie unter „Beheben von Fehlern mit dem HTTP-Statuscode [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) und [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it)“.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Ich muss mehr Endpunktabfragen verarbeiten. Wie geht das?

Weitere Informationen finden Sie unter „Beheben von Fehlern mit dem HTTP-Statuscode [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) und [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it)“.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Ich habe einen Erstellungsschlüssel erstellt, der wird aber im LUIS-Portal nicht angezeigt. Was ist passiert?

Erstellungsschlüssel sind im LUIS-Portal nach der [Migration zur Erstellungsschlüssel-Benutzeroberfläche](luis-migration-authoring.md) verfügbar.

## <a name="app-management"></a>App-Verwaltung

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Wie lade ich ein Protokoll mit Benutzeräußerungen herunter?
Ihrer LUIS-App protokolliert standardmäßig Äußerungen von Benutzern. Wechseln Sie zu **Meine Apps** und wählen Sie die App aus, um ein Protokoll mit Äußerungen herunterzuladen, die Benutzer an Ihre LUIS-App senden. Wählen Sie in der kontextbezogenen Symbolleiste **Endpunktprotokolle exportieren** aus. Das Protokoll wird als durch Trennzeichen getrennte Datei (CSV) gespeichert.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Wie kann ich die Protokollierung von Äußerungen deaktivieren?
Sie können die Protokollierung von Benutzeräußerungen deaktivieren, indem Sie in der Endpunkt-URL, die Ihre Clientanwendung zur Abfrage von LUIS verwendet, `log=false` festlegen. Durch das Deaktivieren der Protokollierung kann die LUIS-App jedoch nicht mehr Äußerungen vorschlagen oder die auf dem [aktiven Lernen](luis-concept-review-endpoint-utterances.md#what-is-active-learning) basierende Leistung verbessern. Wenn Sie `log=false` aus Datenschutzgründen festlegen, können Sie keinen Datensatz mit diesen Benutzeräußerungen von LUIS herunterladen oder diese Äußerungen zur Verbesserung Ihrer App verwenden.

Äußerungen werden ausschließlich über die Protokollierungsfunktion gespeichert.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>In welchem Fall sollten nicht alle Endpunktäußerungen protokolliert werden?
Sie sollten keine Testäußerungen protokollieren, wenn Sie das Protokoll für die Vorhersageanalyse verwenden.

## <a name="data-management"></a>Datenverwaltung

### <a name="can-i-delete-data-from-luis"></a>Kann ich Daten aus LUIS löschen?

* Sie können Beispieläußerungen für das Trainieren von LUIS jederzeit löschen. Wenn Sie eine Beispieläußerung aus Ihrer LUIS-App löschen, wird sie vom LUIS-Webdienst entfernt und ist nicht für den Export verfügbar.
* Sie können Äußerungen aus der Liste der Benutzeräußerungen löschen, die LUIS auf der Seite **Review endpoint utterance** (Endpunktäußerungen überprüfen) vorschlägt. Das Löschen von Äußerungen aus dieser Liste verhindert, dass diese empfohlen werden. Sie werden aber nicht aus den Protokollen gelöscht.
* Wenn Sie ein Konto löschen, werden alle Apps zusammen mit ihren Beispieläußerungen und Protokollen gelöscht. Die Daten werden 60 Tage lang auf den Servern gespeichert und erst danach dauerhaft gelöscht.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Wie verwaltet Microsoft Daten, die ich an LUIS sende?

Im [Trust Center](https://www.microsoft.com/trustcenter) werden unsere Verpflichtungen sowie die Optionen für die Datenverwaltung und den Zugriff in Azure-Diensten erläutert.

## <a name="language-and-translation-support"></a>Sprach- und Übersetzungsunterstützung

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Ich habe eine App in einer bestimmten Sprache erstellt und möchte nun dieselbe App in einer anderen Sprache erstellen. Wie geht das am einfachsten?
1. Exportieren Sie zunächst Ihre App,
2. und übersetzen Sie anschließend die in der JSON-Datei der exportieren App vorhandenen Äußerungen mit Bezeichnungen in die Zielsprache.
3. Möglicherweise müssen Sie die Namen der Absichten und Entitäten ändern.
4. Wenn Sie die App abschließend importieren, steht Ihnen eine LUIS-App in der Zielsprache zur Verfügung.

## <a name="app-notification"></a>App-Benachrichtigungen

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Wieso habe ich eine E-Mail erhalten, in der darauf hingewiesen wird, dass mein Kontingent bald aufgebraucht ist?
Ihr Erstellungsschlüssel (auch als Startschlüssel bezeichnet) kann für maximal 1000 Endpunktabfragen pro Monat verwendet werden. Erstellen Sie einen kostenlosen oder kostenpflichtigen LUIS-Endpunktschlüssel, und verwenden Sie diesen für Endpunktabfragen. Wenn Sie Endpunktabfragen über einen Bot oder eine andere Clientanwendung ausführen, müssen Sie den LUIS-Endpunktschlüssel dort ändern.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Mein LUIS-Bot funktioniert nicht. Wie gehe ich vor?

Das erste Problem besteht in der Feststellung, ob das Problem mit LUIS zusammenhängt oder außerhalb der LUIS-Middleware auftritt.

#### <a name="resolve-issue-in-luis"></a>Beheben des Problems in LUIS
Übergeben Sie die gleiche Äußerung an LUIS aus dem [LUIS-Endpunkt](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Wenn Sie eine Fehlermeldung erhalten, beheben Sie das Problem in LUIS, bis der Fehler nicht mehr auftritt. Häufige Fehler sind z.B. folgende:

* `Out of call volume quota. Quota will be replenished in <time>.`: Dieses Problem deutet darauf hin, dass Sie entweder von einem Erstellungsschlüssel zu einem [Endpunktschlüssel](luis-how-to-azure-subscription.md) wechseln oder [Dienstebenen](luis-how-to-azure-subscription.md#change-the-pricing-tier) ändern müssen.

#### <a name="resolve-issue-in-azure-bot-service"></a>Beheben des Problems in Azure Bot Service

Wenn Sie Azure Bot Service verwenden und das Problem darin besteht, dass der **Test in Webchat**`Sorry, my bot code is having an issue` zurückgibt, überprüfen Sie Ihre Protokolle:

1. Wählen Sie im Azure-Portal für Ihren Bot im Abschnitt **Bot Management** (Botverwaltung) **Build** (Erstellen) aus.
1. Öffnen Sie den Onlinecode-Editor.
1. Wählen Sie auf der oberen blauen Navigationsleiste den Namen des Bots (das zweite Element von rechts) aus.
1. Wählen Sie in der resultierenden Dropdownliste **Open Kudu Console** (Kudu-Konsole öffnen) aus.
1. Wählen Sie **LogFiles** (Protokolldateien) und dann **Application** (Anwendung) aus. Überprüfen Sie alle Protokolldateien. Wenn Sie den Fehler im Anwendungsordner nicht sehen, überprüfen Sie alle Protokolldateien unter **LogFiles** (Protokolldateien).
1. Denken Sie daran, für Ihr Projekt einen neuen Build zu erstellen, wenn Sie eine kompilierte Sprache wie C# verwenden.

> [!Tip]
> Die Konsole kann auch zum Installieren von Paketen dienen.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Beheben Sie Probleme beim Debuggen auf dem lokalen Computer mit Bot Framework.

Weitere Informationen zum lokalen Debuggen eines Bots finden Sie unter [Debuggen eines Bots](/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrieren von LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Wo wird meine LUIS-App bei der Abonnementerstellung durch den Azure-Web-App-Bot erstellt?
Wenn Sie eine LUIS-Vorlage auswählen und im Vorlagenbereich auf **Select** (Auswählen) klicken, wird im linken Bereich der Vorlagentyp angezeigt, und Sie werden dazu aufgefordert, eine Region für die Erstellung der LUIS-Vorlage anzugeben. Der Web-App-Bot erstellt jedoch kein LUIS-Abonnement.

![Web-App-Bot-Region für LUIS-Vorlage](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Welche LUIS-Regionen unterstützen die Bot Framework-Sprachoptimierung?
Die [Sprachoptimierung ](/bot-framework/bot-service-manage-speech-priming) wird nur von LUIS-Apps in der Instanz für die mittlere USA unterstützt.

## <a name="api-programming-strategies"></a>Strategien für die API-Programmierung

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Wie kann ich programmgesteuert die LUIS-Region einer Ressource abrufen?

Verwenden Sie das LUIS-Beispiel für Regionsssuche ([find-region](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region)), um programmgesteuert mit C# oder Node.Js nach einer Region zu suchen.

## <a name="luis-service"></a>LUIS-Dienst

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Kann LUIS (Language Understanding) lokal oder in einer privaten Cloud genutzt werden?

Ja, Sie können die LUIS-[Container](luis-container-howto.md) für diese Szenarien verwenden, wenn Sie über die erforderliche Konnektivität zum Messen des Verbrauchs verfügen.

## <a name="migrating-to-the-next-version"></a>Migrieren zur nächsten Version

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Wie migriere ich zur API-Version 3 (Vorschau)?

Siehe die Anleitung zur [Migration von API-Version 2 zu API-Version 3 für LUIS-Apps](luis-migration-api-v3.md).

## <a name="build-2019-conference-announcements"></a>Ankündigungen zur Build 2019-Konferenz

Die folgenden Features wurden bei der Build 2019-Konferenz veröffentlicht:

* [Migrationsanleitung für die Vorschauversion der V3-API](luis-migration-api-v3.md)
* [Verbessertes Analytics-Dashboard](luis-how-to-use-dashboard.md)
* [Verbesserte vordefinierte Domänen](luis-reference-prebuilt-domains.md)
* [Entitäten vom Typ „dynamische Liste“](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe Entitäten](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videos:

* [How to use Azure Conversational AI to scale your business for the next generation (Bereiten Sie Ihr Unternehmen mit Azure Conversational AI auf die nächste Generation vor)](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu LUIS finden Sie in den folgenden Ressourcen:
* [Mit dem Tag „LUIS“ versehene Fragen auf Stack Overflow](https://stackoverflow.com/questions/tagged/luis)
* [Seite mit häufig gestellten Fragen von Microsoft für das MSDN-Forum für Language Understanding Intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)