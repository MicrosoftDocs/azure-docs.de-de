---
title: Verwenden und Anzeigen von Anforderungen für die Bing-Suche-APIs
titleSuffix: Azure Cognitive Services
description: Anforderungen für die Anzeige von Suchergebnissen von den Bing-Suche-APIs in Ihren Anwendungen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 4300860039b76bd076f19acfc252f59ef10d462b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351843"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Anforderungen für die Verwendung und Anzeige der Bing-Suche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Diese Verwendungs- und Anzeigeanforderungen gelten für jede Implementierung von Inhalten und zugehörigen Informationen aus den folgenden Bing-Suche-APIs, einschließlich der Beziehungen, Metadaten und anderer Signale.

- Benutzerdefinierte Bing-Suche
- Bing-Entitätssuche
- Bing-Bildersuche
- Bing News-Suche
- Bing-Videosuche
- Visuelle Bing-Suche
- Bing-Websuche
- Bing-Rechtschreibprüfung
- Bing-Vorschlagssuche

## <a name="definitions"></a>Definitionen


|Begriff  |Beschreibung  |
|---------|---------|
|Antwort     | Eine Kategorie von Ergebnissen, die in einer Antwort zurückgegeben werden. Eine Reaktion der Bing-Websuche-API kann beispielsweise Antworten in den Kategorien „Webseitenergebnisse“, „Bild“, „Video“ „Thema“ und „News“ enthalten. |
|Antwort     | Sämtliche Antworten und die zugehörigen Daten, die infolge eines einzelnen Aufrufs einer Suche-API empfangen werden. |
|Ergebnis    | Ein Informationselement in einer Antwort. Die Daten in Verbindung mit einem einzelnen Nachrichtenartikel sind beispielsweise ein Ergebnis in einer Nachrichtenantwort. |
|Such-APIs    | Sammelbegriff für die APIs der benutzerdefinierten Bing-Suche, der Entitätssuche, der Bildersuche, der News-Suche, der Videosuche, der visuellen Suche, der Suche nach lokalen Unternehmen und der Websuche. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Einschränkungen der Bing-Rechtschreibprüfungs-API und der Bing-Vorschlagssuche-API

Vermeiden Sie Folgendes:

- Kopieren, Speichern oder Zwischenspeichern von Daten, die Sie von der Bing-Rechtschreibprüfungs-API oder von der Bing-Vorschlagssuche-API erhalten.
- Verwenden der Daten, die Sie von der Bing-Rechtschreibprüfungs-API oder der Bing-Vorschlagssuche-API erhalten, im Rahmen einer Aktivität mit maschinellem Lernen oder einer ähnlichen algorithmischen Aktivität Verwenden Sie diese Daten nicht zum Trainieren, Auswerten oder Verbessern neuer oder vorhandener Dienste, die Sie oder Dritte möglicherweise anbieten

## <a name="bing-search-apis"></a>Bing-Suche-APIs

> [!NOTE]
> Die Anforderungen in diesem Abschnitt gelten nur für die Suche-APIs, die weder die Bing-Rechtschreibprüfung noch die Bing-Vorschlagssuche enthalten. 

### <a name="internet-search-experience-requirements"></a>Anforderungen der Internetsuche

Alle in einer Reaktion zurückgegebenen Daten dürfen ausschließlich im Rahmen von Internetsuchvorgängen verwendet werden. Internetsuche bedeutet, dass für den angezeigten Inhalt Folgendes gilt: 

- Er ist für die direkte Abfrage des Endbenutzers oder für einen anderen Hinweis auf sein Suchinteresse oder die Suchabsicht des Benutzers (beispielsweise eine vom Benutzer angegebene Suchabfrage) relevant und eine Reaktion darauf. 

- Er hilft Benutzern beim Suchen der Datenquellen der Antwort und beim Navigieren zu diesen. Beispiel: klickbare Links aus Hyperlinks in der Antwort.

- Er enthält mehrere Ergebnisse, zwischen denen der Benutzer auswählen kann. 

- Die Anordnung ermöglicht Benutzern die Suche.

- Er enthält einen sichtbaren Hinweis darauf, dass der Inhalt das Ergebnis einer Internetsuche ist. Ein Beispiel wäre die Anmerkung, dass der Inhalt „aus dem Web“ stammt.

- Er umfasst beliebige weitere geeignete Maßnahmen, die sicherstellen, dass Ihre Daten von der Bing-Suche-API nicht gegen geltende Gesetze verstoßen oder die Rechte Dritter verletzen. Wenden Sie sich gegebenenfalls an Ihre Rechtsberater, um zu klären, welche Maßnahmen hierfür geeignet sind.

Die einzige Ausnahme für diese Anforderung bei der Internetsuche betrifft die URL-Ermittlung, wie weiter unten in diesem Artikel beschrieben. 

### <a name="restrictions"></a>Beschränkungen

Vermeiden Sie Folgendes:

- Kopieren, Speichern oder Zwischenspeichern von Daten aus Antworten (mit Ausnahme der zulässigen Aufbewahrung gemäß dem Abschnitt zur [Dienstkontinuität](#continuity-of-service)). 

- Verwenden der Daten, die Sie von den Such-APIs erhalten, im Rahmen einer Aktivität mit maschinellem Lernen oder einer ähnlichen algorithmischen Aktivität Verwenden Sie diese Daten nicht zum Trainieren, Auswerten oder Verbessern neuer oder vorhandener Dienste, die Sie oder Dritte möglicherweise anbieten

- Ändern des Inhalts von Ergebnissen (mit Ausnahme der Anpassung der Formatierung unter Einhaltung sämtlicher anderer Anforderungen), sofern dies nicht gesetzlich erforderlich ist oder mit Microsoft abgestimmt wurde 

- Entfernen der dem Ergebnisinhalt zugeordneten Namensnennung und URLs

- Ändern der Reihenfolge (einschließlich Auslassung) von Ergebnissen in einer Antwort, wenn eine Reihen- oder Rangfolge bereitgestellt wird, sofern dies nicht gesetzlich erforderlich ist oder mit Microsoft abgestimmt wurde 

    > [!NOTE]
    > Diese Regel gilt nicht für Änderungen der Reihenfolge, die durch das Portal für die API der benutzerdefinierten Bing-Suche implementiert wurden.

- Anzeigen anderer Inhalte innerhalb eines beliebigen Teils einer Reaktion auf eine Weise, die einem Benutzer vorspiegelt, der andere Inhalt sei Teil der Reaktion 

- Anzeigen von Werbung, die nicht von Microsoft bereitgestellt wird, auf einer Seite, auf der ein Teil einer Reaktion angezeigt wird 

- Anzeigen von Werbung auf Seiten mit Antworten:
    - Von den APIs der Bing-Bildersuche, News-Suche, Videosuche oder visuellen Suche
    - Die gefiltert oder eingeschränkt werden, um hauptsächlich (oder ausschließlich) Bild-, Nachrichten- und/oder Videoergebnisse oder visuelle Suchergebnisse anzuzeigen

### <a name="notices-and-branding"></a>Hinweise und Branding 
Führen Sie Folgendes aus:

- Schließen Sie nahe jedem Punkt auf der Benutzeroberfläche, der einem Benutzer die Möglichkeit zum Eingen einer Suchanfrage bietet, an hervorgehobener Stelle einen funktionsfähigen Link zur [Datenschutzerklärung von Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839) ein. Bezeichnen Sie den Link als **Datenschutzerklärung von Microsoft**.

- Stellen Sie nahe jedem Punkt auf der Benutzeroberfläche, die einem Benutzer die Möglichkeit zum Einengen einer Suchanfrage bietet, Bing-Branding an hervorgehobener Stelle in Übereinstimmung mit den [Richtlinien für die Bing-Markennutzung](https://go.microsoft.com/fwlink/?linkid=833278) dar. Dieses Branding muss für den Benutzer eindeutig kennzeichnen, dass die Internetsuche von Microsoft bereitgestellt wird.

- Sie können jede angezeigte Reaktion (oder einen Teil einer Reaktion) der Bing-Websuche-API, der Bing-Bildersuche-API, der Bing-News-Suche-API, der Bing-Videosuche-API und der Bing-API für thematische Suche Microsoft zuschreiben, sofern Microsoft nicht schriftlich andere Angaben für Ihre Nutzung gemacht hat. Dies ist in den [Richtlinien für die Bing-Markennutzung](https://go.microsoft.com/fwlink/?linkid=833278) beschrieben. 

Vermeiden Sie Folgendes:

- Schreiben Sie angezeigte Reaktionen (oder Teile von Reaktionen) der API für die benutzerdefinierte Bing-Suche Microsoft zu, sofern Microsoft nicht schriftlich andere Angaben speziell für Ihre Nutzung gemacht hat.

### <a name="transferring-responses"></a>Weiterleiten von Reaktionen

Wenn Sie einem Benutzer die Weiterleitung einer Reaktion von einer Such-API an einen anderen Benutzer ermöglichen (etwa über eine Messaging-App oder über einen Beitrag in sozialen Medien), gilt Folgendes: 

- Weitergeleitete Reaktionen müssen folgende Anforderungen erfüllen:
  - Der Inhalt muss exakt dem Inhalt entsprechen, der dem weiterleitenden Benutzer angezeigt wurde. Die Formatierung darf geändert werden.
  - Es dürfen keine Daten in Metadatenform enthalten sein.
  - Bei Reaktionen von der Bing-Websuche-API, der Bing-Bildersuche-API, der Bing-News-Suche-API, der Bing-Videosuche-API und der Bing-API für thematische Suche muss ein Text angezeigt werden, der angibt, dass die Reaktion über eine durch Bing bereitgestellte Internetsuche erhalten wurde. Beispielsweise können Sie einen Text wie „Unterstützt durch Bing“ oder „Weitere Informationen über dieses Bild bei Bing“ anzeigen, oder Sie können das Bing-Logo verwenden.
  - Bei Reaktionen der API für die benutzerdefinierte Bing-Suche muss ein Text angezeigt werden, der angibt, dass die Reaktion über eine Internetsuche erhalten wurde. Beispielsweise können Sie einen Text wie „Weitere Informationen über dieses Suchergebnis“ anzeigen.
  - Sie müssen gut sichtbar die vollständige Abfrage anzeigen, die zum Generieren der Reaktion verwendet wurde.
  - Sie müssen einen gut sichtbaren Link oder eine ähnliche Nennung der Quelle enthalten, die der Reaktion zugrunde liegt, entweder direkt oder über die Suchmaschine (bing.com, m.bing.com oder Ihre benutzerdefinierte Suchmaschine).
- Die Weiterleitung von Reaktionen darf nicht automatisiert werden. Eine Weiterleitung muss durch eine Benutzeraktion initiiert werden, die eindeutig auf die Absicht zurückzuführen ist, eine Reaktion weiterzuleiten.
- Einem Benutzer darf nur die Weiterleitung von Reaktionen ermöglicht werden, die als Antwort auf die Abfrage des weiterleitenden Benutzers angezeigt wurden.

### <a name="continuity-of-service"></a>Dienstkontinuität 

Daten aus Reaktionen von Such-APIs dürfen nicht kopiert, gespeichert oder zwischengespeichert werden. Zur Gewährleistung der Kontinuität beim Dienstzugriff und beim Rendern von Daten ist es unter folgenden Bedingungen jedoch gestattet, Ergebnisse aufzubewahren:

#### <a name="device"></a>Sicherungsmedium

Sie können einem Benutzer die Aufbewahrung von Ergebnissen auf einem Gerät für den kürzeren der beiden folgenden Zeiträume ermöglichen: (i) 24 Stunden ab dem Zeitpunkt der Abfrage oder (ii) bis ein Benutzer eine weitere Abfrage für aktualisierte Ergebnisse übermittelt – vorausgesetzt, die aufbewahrten Ergebnisse werden ausschließlich zu folgenden Zwecken verwendet:

- Um dem Benutzer Zugriff auf Ergebnisse zu ermöglichen, die zuvor an diesen Benutzer auf diesem Gerät zurückgegeben wurden (etwa im Fall einer Dienstunterbrechung)
- Zur Speicherung der zurückgegebenen Ergebnisse für Ihre proaktive Abfrage, die in Erwartung des Benutzerbedarfs auf der Grundlage der Signale des Benutzers personalisiert wurde (etwa bei einer erwarteten Dienstunterbrechung)

#### <a name="server"></a>Server

Spezifische Ergebnisse für einen einzelnen Benutzer dürfen sicher auf einem von Ihnen kontrollierten Server aufbewahrt werden, und die aufbewahrten Ergebnisse dürfen angezeigt werden, allerdings ausschließlich zu folgenden Zwecken:

- Um dem Benutzer einen Verlaufsbericht der dem Benutzer in Ihrer Lösung zuvor zurückgegebenen Ergebnisse zur Verfügung zu stellen. Die Ergebnisse dürfen nicht (i) für mehr als 21 Tage ab dem Zeitpunkt der ursprünglichen Abfrage des Endbenutzers beibehalten und (ii) als Antwort auf eine neue oder wiederholte Abfrage eines Benutzers angezeigt werden.
- Zur Speicherung der zurückgegebenen Ergebnisse für Ihre proaktive Abfrage, die in Erwartung des Benutzerbedarfs auf der Grundlage der Signale des Benutzers personalisiert wurde. Sie können diese Ergebnisse für den kürzeren der beiden folgenden Zeiträume ermöglichen: (i) 24 Stunden ab dem Zeitpunkt der Abfrage oder (ii) bis ein Benutzer eine weitere Abfrage für aktualisierte Ergebnisse übermittelt.

Wenn die Ergebnisse für einen bestimmten Benutzer beibehalten werden, dürfen sie nicht mit Ergebnissen für einen anderen Benutzer vermischt werden. Die Ergebnisse müssen also für jeden Benutzer separat beibehalten und übermittelt werden.

### <a name="general"></a>Allgemein 

Bei jeder Präsentation aufbewahrter Ergebnisse müssen folgende Anforderungen erfüllt sein:

- Der Zeitpunkt, zu dem die Abfrage gesendet wurde, muss eindeutig und gut sichtbar angegeben sein.
- Dem Benutzer muss eine Schaltfläche oder ein ähnliches Element zur Verfügung stehen, mit der bzw. dem er die Abfrage erneut ausführen und aktualisierte Ergebnisse abrufen kann. 
- Bei der Präsentation der Ergebnisse muss das Bing-Branding erhalten bleiben.
- Die gespeicherten Ergebnisse müssen innerhalb der angegebenen Fristen gelöscht (und bei Bedarf mit einer neuen Abfrage aktualisiert) werden.

### <a name="non-display-url-discovery"></a>Ermittlung von Nicht-Anzeige-URLs 

Suchreaktionen dürfen in nicht internetbasierten Suchen ausschließlich zur Ermittlung der URLs von Informationsquellen verwendet werden, bei denen es sich um eine Reaktion auf eine Abfrage Ihres Benutzers oder Kunden handelt. Sie dürfen solche URLs in einem Bericht oder einer ähnlichen Antwort kopieren, die Sie unter folgenden Umständen bereitstellen:

- Nur für diesen Benutzer oder Kunden und als Antwort auf diese Abfrage
- Nur, wenn sie einen erheblichen zusätzlichen wertvollen Inhalt enthält, der relevant für die Abfrage ist

Die vorhergehenden Abschnitte dieser Nutzungs- und Anzeigeanforderungen für Such-APIs gelten nicht für diese Nicht-Anzeige-Verwendung, jedoch mit folgenden Ausnahmen: 

- Mit Ausnahme des weiter oben beschriebenen eingeschränkten Kopierens von URLs dürfen keine Daten und keine Inhalte, die aus der Suchreaktion stammen oder daraus abgeleitet wurden, zwischengespeichert, kopiert oder gespeichert werden.
- Sie müssen sicherstellen, dass Ihre Verwendung von Daten (einschließlich URLs), die von den Such-APIs empfangen wurden, nicht gegen geltende Gesetze verstößt oder die Rechte Dritter verletzt.
- Sie dürfen die Daten (einschließlich URLs), die Sie von den Such-APIs erhalten, nicht im Rahmen einer Suchindizierung, einer Aktivität des maschinellen Lernens oder einer ähnlichen algorithmischen Aktivität verwenden. Verwenden Sie diese Daten nicht zum Erstellen, Trainieren, Auswerten oder Verbessern von Diensten, die Sie oder Dritte möglicherweise anbieten.

## <a name="gdpr-compliance"></a>Einhaltung der DSGVO  

Betreffend personenbezogenen Daten, die der Datenschutz-Grundverordnung (DSGVO) der Europäischen Union unterliegen und die im Zusammenhang mit Aufrufen an die Such-APIs, die Bing-Rechtschreibprüfungs-API oder die Bing-Vorschlagssuche-API verarbeitet werden, bestätigen Sie, dass Sie und Microsoft unabhängige Verantwortliche gemäß der DSGVO sind. Sie sind unabhängig voneinander für die Einhaltung der DSGVO verantwortlich.