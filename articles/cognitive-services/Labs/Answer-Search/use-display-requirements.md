---
title: Verwendungs- und Anzeigeanforderungen – Project Answer Search
titlesuffix: Azure Cognitive Services
description: Hier finden Sie die Verwendungs- und Anzeigeanforderungen für den Project Answer Search-Endpunkt.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 085cb20e4dad92ed55b5ba0914c677aa50f3ac97
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345752"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Verwendungs- und Anzeigeanforderungen für Project Answer Search

Die Verwendungs- und Anzeigeanforderungen gelten für Ihre Implementierung der Inhalte und der dazugehörigen Informationen, wie etwa Beziehungen, Metadaten und andere Signale, die über Aufrufe der API für die Bing-Suche in Wissensdatenbankartikeln, der API für die benutzerdefinierten Bing-Suche, der Entitätssuche-API, Bildersuche-, News-Suche-, Videosuche-API, der API für die thematische Suche, Websuche-API, Rechtschreibprüfungs- und Vorschlagssuche-API verfügbar sind. Implementierungsdetails im Zusammenhang mit diesen Anforderungen finden Sie in der Dokumentation für bestimmte Features und Ergebnisse.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Bing-Rechtschreibprüfungs-API und Bing-Vorschlagssuche-API.

Vermeiden Sie Folgendes:

- Kopieren, Speichern oder Zwischenspeichern von Daten, die Sie von der Bing-Rechtschreibprüfungs-API oder von der Bing-Vorschlagssuche-API erhalten.
- Verwenden von Daten, die Sie von der Bing-Rechtschreibprüfungs-API oder Bing-Vorschlagssuche-API erhalten, im Rahmen einer Machine Learning-Aktivität oder einer ähnlichen algorithmischen Aktivität zum Trainieren, Auswerten oder Verbessern neuer oder vorhandener Dienste, die von Ihnen selbst oder von Dritten angeboten werden.

## <a name="2-definitions"></a>2. Definitionen

- „Antwort“ bezieht sich auf eine Kategorie von Ergebnissen, die in einer Reaktion zurückgegeben werden. Eine Reaktion der Bing-Websuche-API kann beispielsweise Antworten in den Kategorien „Webseitenergebnisse“, „Bild“, „Video“ und „News“ enthalten.
- Als „Reaktion“ werden sämtliche Antworten und dazugehörigen Daten bezeichnet, die infolge eines einzelnen Aufrufs einer Such-API empfangen werden.
- „Ergebnis“ bezieht sich auf ein Informationselement in einer Antwort. Die Daten in Verbindung mit einem einzelnen Nachrichtenartikel sind beispielsweise ein Ergebnis in einer Nachrichtenantwort.
- „Such-APIs“ stehen als Sammelbegriff für die APIs der benutzerdefinierten Bing-Suche, der Entitätssuche, der Bildersuche, der News-Suche, der Videosuche, der thematischen Suche und der Websuche. 


## <a name="3-search-apis"></a>3. Such-APIs

Die Anforderungen in diesem Abschnitt 3 gelten für die Such-APIs.

**A. Internetsuche.** Alle in einer Reaktion zurückgegebenen Daten dürfen ausschließlich im Rahmen von Internetsuchvorgängen verwendet werden. Internetsuche bedeutet, dass für den angezeigten Inhalt Folgendes gilt (sofern zutreffend): 
- Er ist für die direkte Abfrage des Endbenutzers oder für einen anderen Hinweis auf das Suchinteresse oder die Suchabsicht des Benutzers (beispielsweise eine vom Benutzer angegebene Suchabfrage) relevant und eine Reaktion darauf. 
- Unterstützt Benutzer beim Auffinden und Navigieren zu den Datenquellen (z.B. werden die bereitgestellten URLs als Hyperlinks implementiert, sodass der Inhalt oder die Zuordnung ein anklickbarer Link ist, der hervorgehoben mit den Daten angezeigt wird); oder gibt bei der Bing-Entitätssuche-API, einen sichtbaren Link zur in der Antwort angegebenen bing.com-URL an, die es dem Benutzer ermöglicht, zu den Suchergebnissen für die entsprechende Abfrage auf bing.com zu navigieren.
- Er enthält mehrere Ergebnisse, aus denen der Endbenutzer wählen kann. (Beispiel: Es werden mehrere Ergebnisse aus der Nachrichtenantwort angezeigt – oder alle, falls weniger Ergebnisse zurückgegeben werden.) 
- Er ist auf einen für den Zweck der Suche angemessenen Umfang beschränkt. (Beispiel: Miniaturansichten von Bildern haben relativ zum Display des Benutzers die Größe von Miniaturansichten.) 
- Der Inhalt ist für den Endbenutzer gut sichtbar als Internetsuchergebnis gekennzeichnet (etwa durch einen Hinweis wie „Aus dem Web“). Und:
- Er umfasst eine beliebige andere Kombination von geeigneten Maßnahmen, die sicherstellen, dass Ihre Verwendung von Daten, die von den Such-APIs empfangen wurden, nicht gegen geltende Gesetze verstößt oder die Rechte Dritter verletzt (z.B. wenn sie auf eine Creative Commons-Lizenz angewiesen sind, die geltenden Lizenzbedingungen entspricht). Wenden Sie sich gegebenenfalls an Ihre Rechtsberater, um zu klären, welche Maßnahmen hierfür geeignet sind.
Die einzige Ausnahme für die Anforderung bei der Internetsuche betrifft die URL-Ermittlung, wie unten in Abschnitt 3E (Ermittlung von Nicht-Anzeige-URLs) beschrieben. 

**B. Einschränkungen.** Vermeiden Sie Folgendes:

- Kopieren, Speichern oder Zwischenspeichern von Daten aus Reaktionen (mit Ausnahme der zulässigen Aufbewahrung gemäß dem Abschnitt zur Dienstkontinuität weiter unten). 
- Verwenden von Daten, die Sie von den Such-APIs erhalten, im Rahmen einer Machine Learning-Aktivität oder einer ähnlichen algorithmischen Aktivität zum Trainieren, Auswerten oder Verbessern neuer oder vorhandener Dienste, die von Ihnen selbst oder von Dritten angeboten werden.
- Ändern des Inhalts von Ergebnissen (mit Ausnahme der Anpassung der Formatierung unter Einhaltung sämtlicher anderer Anforderungen), sofern dies nicht gesetzlich erforderlich ist oder mit Microsoft abgestimmt wurde. 
- Entfernen der dem Ergebnisinhalt zugeordneten Namensnennung und URLs.
- Umsortierung, auch durch Auslassung, der Ergebnisse, die in einer Antwort angezeigt werden, wenn eine Reihenfolge oder ein Ranking angegeben ist (für die API für die benutzerdefinierte Bing-Suche gilt diese Regel nicht für die Umsortierung, die über das customsearch.ai-Portal implementiert wurde), es sei denn, dies ist gesetzlich vorgeschrieben oder von Microsoft genehmigt.
- Anzeigen anderer Inhalte innerhalb eines beliebigen Teils einer Reaktion auf eine Weise, die einem Endbenutzer vorspiegelt, der andere Inhalt sei Teil der Reaktion. 
- Anzeigen von Werbung, die nicht von Microsoft bereit gestellt wird, auf einer Seite, die beliebige Teile einer Antwort anzeigt. - Anzeigen von Werbung zusammen mit Antworten, die (i) von der Bildsuche-API, News-Suche-API oder Videosuche-API stammen oder die (ii) hauptsächlich (oder ausschließlich) nach Bild-, Nachrichten- und/oder Videoergebnissen gefiltert oder darauf beschränkt sind.

**C. Hinweise und Branding.** 

- Schließen Sie nahe jedem Punkt auf der Benutzeroberfläche, der einem Benutzer die Möglichkeit zum Eingeben einer Suchanfrage bietet, an hervorgehobener Stelle einen funktionsfähigen Link zur auf https://go.microsoft.com/fwlink/?LinkId=521839 verfügbaren Datenschutzerklärung von Microsoft ein. Bezeichnen Sie den Link als „Datenschutzerklärung von Microsoft“.
- Stellen Sie nahe jedem Punkt auf der Benutzeroberfläche, die einem Benutzer die Möglichkeit zum Eingeben einer Suchanfrage bietet, Bing-Branding an hervorgehobener Stelle in Übereinstimmung mit den unter https://go.microsoft.com/fwlink/?linkid=833278 verfügbaren Richtlinien dar.  Dieses Branding muss für den Benutzer eindeutig kennzeichnen, dass die Internetsuche von Microsoft bereitgestellt wird.
- Sie können jede angezeigte Antwort (oder einen Teil einer Antwort) der Bing-Websuche-, der Bing-Bildersuche-, der Bing-News-Suche- und der Bing-Videosuche-API Microsoft zuschreiben, sofern Microsoft in https://go.microsoft.com/fwlink/?linkid=833278 nicht schriftlich andere Angaben für Ihre Nutzung gemacht hat. 
- Schreiben Sie angezeigte Reaktionen (oder Teile von Reaktionen) der API für die benutzerdefinierte Bing-Suche nicht Microsoft zu, sofern Microsoft nicht schriftlich andere Angaben speziell für Ihre Nutzung gemacht hat.


**D. Weiterleiten von Antworten.** Wenn Sie einem Benutzer die Weiterleitung einer Reaktion von einer Such-API an einen anderen Benutzer ermöglichen (etwa über eine Messaging-App oder über einen Beitrag in sozialen Medien), gilt Folgendes: 
- Weitergeleitete Reaktionen müssen folgende Anforderungen erfüllen:
  - Der Inhalt muss exakt dem Inhalt entsprechen, der dem weiterleitenden Benutzer angezeigt wurde. (Die Formatierung darf jedoch geändert werden.)
  - Es dürfen keine Daten in Metadatenform enthalten sein.
  - Für Antworten der Bing-Websuche-, der Bing-Bildersuche-, der Bing-News-Suche- und der Bing-Videosuche-API müssen darauf hinweisen, dass der Antwort eine Internetsuche mit Bing zugrunde liegt (beispielsweise „Unterstützt von Bing“, „Weitere Informationen zu diesem Bild auf Bing“ oder durch die Verwendung des Bing-Logos).
  - Bei Antworten der API für die benutzerdefinierte Bing-Suche muss ein Text angezeigt werden, der angibt, dass Antwort Reaktion über eine Internetsuche erhalten wurde (beispielsweise „Weitere Informationen zu diesem Suchergebnis“).
  - Sie müssen gut sichtbar die vollständige Abfrage anzeigen, die zum Generieren der Reaktion verwendet wurde. Und:
  - Sie müssen einen gut sichtbaren Link oder eine ähnliche Nennung der Quelle enthalten, die der Reaktion zugrunde liegt, entweder direkt oder über die Suchmaschine (bing.com, m.bing.com oder Ihre benutzerdefinierte Suchmaschine).
- Die Weiterleitung von Reaktionen darf nicht automatisiert werden. Eine Weiterleitung muss durch eine Benutzeraktion initiiert werden, die eindeutig auf die Absicht zurückzuführen ist, eine Reaktion weiterzuleiten.
- Einem Benutzer darf nur die Weiterleitung von Reaktionen ermöglicht werden, die als Antwort auf die Abfrage des weiterleitenden Benutzers angezeigt wurden.

**E. Dienstkontinuität.** Daten aus Reaktionen von Such-APIs dürfen nicht kopiert, gespeichert oder zwischengespeichert werden. Zur Gewährleistung der Kontinuität beim Dienstzugriff und beim Rendern von Daten ist es unter folgenden Bedingungen jedoch gestattet, Ergebnisse aufzubewahren:

**Gerät.** Sie können einem Endbenutzer die Aufbewahrung von Ergebnissen auf einem Gerät für den kürzeren der beiden folgenden Zeiträume ermöglichen: (i) 24 Stunden ab dem Zeitpunkt der Abfrage oder (ii) bis ein Endbenutzer eine weitere Abfrage für aktualisierte Ergebnisse übermittelt – vorausgesetzt, die aufbewahrten Ergebnisse werden ausschließlich zu folgenden Zwecken verwendet:

- Um dem Endbenutzer Zugriff auf Ergebnisse zu ermöglichen, die zuvor an diesen Endbenutzer auf diesem Gerät zurückgegeben wurden (etwa im Falle einer Dienstunterbrechung). Oder:
- Zur Speicherung der zurückgegebenen Ergebnisse für Ihre proaktive Abfrage, die in Erwartung des Endbenutzerbedarfs auf der Grundlage der Signale des Endbenutzers personalisiert wurde (etwa bei einer erwarteten Dienstunterbrechung)

**Server.** Spezifische Ergebnisse für einen einzelnen Endbenutzer dürfen sicher auf einem von Ihnen kontrollierten Server aufbewahrt werden, und die aufbewahrten Ergebnisse dürfen angezeigt werden, allerdings ausschließlich zu folgenden Zwecken:

- Um dem Benutzer den Zugriff auf einen Verlaufsbericht mit Ergebnissen zu ermöglichen, die diesem Benutzer in der Vergangenheit in Ihrer Lösung zurückgegebenen wurden – vorausgesetzt, die Ergebnisse werden (i) nicht länger als 21 Tage ab dem Zeitpunkt der ursprünglichen Abfrage des Endbenutzers aufbewahrt und (ii) nicht als Reaktion auf eine neue oder wiederholte Abfrage eines Endbenutzers angezeigt. Oder:
- Zur Speicherung von Ergebnissen, die für Ihre proaktive Abfrage zurückgegeben wurden, welche in Erwartung des Endbenutzerbedarfs auf der Grundlage der Signale des Endbenutzers personalisiert wurde – für den kürzeren der beiden folgenden Zeiträume: (i) 24 Stunden ab dem Zeitpunkt der Abfrage oder (ii) bis ein Endbenutzer eine weitere Abfrage für aktualisierte Ergebnisse übermittelt.

Aufbewahrte Ergebnisse für einen bestimmten Benutzer dürfen in keinem Fall mit Ergebnissen für einen anderen Benutzer vermischt werden. Mit anderen Worten: Die Ergebnisse einzelner Benutzer müssen jeweils separat aufbewahrt und bereitgestellt werden.

**Allgemeines.** Bei jeder Präsentation aufbewahrter Ergebnisse müssen folgende Anforderungen erfüllt sein:

- Der Zeitpunkt, zu dem die Abfrage gesendet wurde, muss eindeutig und gut sichtbar angegeben sein.
- Dem Benutzer muss eine Schaltfläche oder ein ähnliches Element zur Verfügung stehen, mit der bzw. mit dem er die Abfrage erneut ausführen und aktualisierte Ergebnisse abrufen kann. 
- Bei der Präsentation der Ergebnisse muss das Bing-Branding erhalten bleiben. Und:
- Die gespeicherten Ergebnisse müssen innerhalb der angegebenen Fristen gelöscht (und bei Bedarf mit einer neuen Abfrage aktualisiert) werden.

**F. Ermittlung von Nicht-Anzeige-URLs.** Suchreaktionen dürfen in nicht internetbasierten Suchen ausschließlich zur Ermittlung der URLs von Informationsquellen verwendet werden, bei denen es sich um eine Reaktion auf eine Abfrage Ihres Benutzers oder Kunden handelt. Sie dürfen solche URLs in einen Bericht oder in eine ähnliche Antwort kopieren, den bzw. die Sie (i) ausschließlich für diesen Benutzer oder Kunden als Antwort auf diese Abfrage bereitstellen und (ii) der bzw. die aussagekräftige, nützliche Zusatzinhalte enthält, die für die Abfrage relevant sind. Die Anforderungen in den Abschnitten 3A bis 3E dieser Verwendungs- und Anzeigeanforderungen gelten nicht für diese Nicht-Anzeige-Verwendung, allerdings mit folgenden Ausnahmen: 

- Mit Ausnahme des weiter oben beschriebenen eingeschränkten Kopierens von URLs dürfen keine Daten und keine Inhalte, die aus der Suchreaktion stammen oder daraus abgeleitet wurden, zwischengespeichert, kopiert oder gespeichert werden.
- Sie müssen sicherstellen, dass Ihre Verwendung von Daten (einschließlich URLs), die von den Such-APIs empfangen wurden, nicht gegen geltende Gesetze verstößt oder die Rechte Dritter verletz. Und:
- Sie dürfen die Daten (einschließlich URLs), die Sie von den Such-APIs erhalten, nicht im Rahmen einer Machine Learning-Aktivität oder einer ähnlichen algorithmischen Aktivität zum Trainieren, Auswerten oder Verbessern von Diensten verwenden, die von Ihnen selbst oder von Dritten angeboten werden.

## <a name="next-steps"></a>Nächste Schritte
[Übersicht über die Antwortsuche](overview.md)
