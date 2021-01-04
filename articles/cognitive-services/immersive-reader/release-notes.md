---
title: Versionshinweise zum SDK für den plastischen Reader
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die neuen Funktionen des JavaScript-SDK für den plastischen Reader.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135459"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Versionshinweise zum JavaScript-SDK für den plastischen Reader

## <a name="version-110"></a>Version 1.1.0

Diese Version enthält neue Features, Korrekturen von Sicherheitsrisiken, Fehlerbehebungen, Aktualisierungen von Codebeispielen sowie Konfigurationsoptionen.

#### <a name="new-features"></a>Neue Funktionen

* Das Speichern und Laden von Benutzereinstellungen über verschiedene Browser und Geräte wurde ermöglicht.
* Das Konfigurieren von Standardanzeigeoptionen wurde aktiviert.
* Eine Option zum Festlegen der Übersetzungssprache, zum Aktivieren der Übersetzung von Wörtern und zum Aktivieren der Dokumentübersetzung beim Starten des plastischen Readers wurde hinzugefügt.
* Die Unterstützung zum Konfigurieren des Vorlesens wurde über Optionen hinzugefügt.
* Die Möglichkeit zum Deaktivieren der zuerst ausgeführten Umgebung wurde hinzugefügt.
* „ImmersiveReaderView“ wurde für die universelle Windows-Plattform hinzugefügt.

#### <a name="improvements"></a>Verbesserungen

* Der HTML-Code des Android-Codebeispiels wurde aktualisiert, um besser mit dem neuesten SDK zusammenzuarbeiten.
* Die Startantwort wurde aktualisiert, um die Anzahl der verarbeiteten Zeichen zurückzugeben.
* Codebeispiele wurden für die Verwendung von Version 1.1.0 aktualisiert.
* Das Aufrufen von „launchAsync“ wird nicht zugelassen, wenn es bereits geladen wird.
* Es wird auf ungültige Inhalte geprüft, indem Nachrichten ignoriert werden, bei denen die Daten nicht aus einer Zeichenfolge bestehen.
* Der Aufruf eines Fensters wird in einer if-Klausel umschlossen, um die Browserunterstützung von Promise zu überprüfen.

#### <a name="fixes"></a>Fehlerbehebungen

* „Dependabot“ wurde durch Entfernen von „yarn.lock“ aus gitignore behoben.
* Das Sicherheitsrisiko durch ein Upgrade von „pug“ auf Version 3.0.0 im Codebeispiel „quickstart-nodejs“ wurde behoben.
* Es wurden mehrere Sicherheitsrisiken durch ein Upgrade der Jest- und TypeScript-Pakete behoben.
* Ein Sicherheitsrisiko wurde durch ein Upgrade von „Microsoft.IdentityModel.Clients.ActiveDirectory“ auf Version 5.2.0 behoben.

<br>

## <a name="version-100"></a>Version 1.0.0

Diese Version enthält Breaking Changes, neue Features, Verbesserungen an Codebeispielen und Fehlerbehebungen.

#### <a name="breaking-changes"></a>Aktuelle Änderungen

* Das Azure AD-Token und die Unterdomäne sind erforderlich und in früheren Versionen verwendete Token sind veraltet.
* „CookiePolicy“ wurde deaktiviert. Die Aufbewahrung von Benutzereinstellungen ist standardmäßig deaktiviert. Der Leser wird jedes Mal mit Standardeinstellungen gestartet, sofern „CookiePolicy“ nicht aktiviert wird.

#### <a name="new-features"></a>Neue Funktionen

* Die Unterstützung zum Aktivieren oder Deaktivieren von Cookies wurde hinzugefügt.
* Das Android Kotlin-Codebeispiel für den Schnellstart wurde hinzugefügt.
* Das Android Java-Codebeispiel für den Schnellstart wurde hinzugefügt.
* Das Node.js-Codebeispiel für den Schnellstart wurde hinzugefügt.

#### <a name="improvements"></a>Verbesserungen

* Die erweiterte „README.md“ von Node.js wurde aktualisiert.
* Das Python-Codebeispiel wurde von „Erweitert“ in „Schnellstart“ geändert.
* Das iOS Swift-Codebeispiel wurde nach „js/samples“ verschoben.
* Codebeispiele wurden für die Verwendung von Version 1.0.0 aktualisiert.

#### <a name="fixes"></a>Fehlerbehebungen

* Ein Fehler für das erweiterte Node.js-Codebeispiel wurde behoben.
* Es wurden fehlende Dateien für „advanced-csharp-multiple-resources“ hinzugefügt.
* Aus Links wurde „en-us“ entfernt.

<br>

## <a name="version-003"></a>Version 0.0.3

Diese Version enthält neue Features, Verbesserungen an Codebeispielen, Korrekturen für Sicherheitsrisiken und Fehlerbehebungen.

#### <a name="new-features"></a>Neue Funktionen

* Das iOS Swift-Codebeispiel wurde hinzugefügt.
* Das erweiterte C#-Codebeispiel wurde hinzugefügt, das die Verwendung mehrerer Ressourcen veranschaulicht. 
* Die Unterstützung zum Deaktivieren des Features zum Umschalten der Vollbildanzeige wurde hinzugefügt.
* Die Unterstützung zum Ausblenden der Schaltfläche zum Beenden der Anwendung des plastischen Readers wurde hinzugefügt.
* Es wurde eine Rückruffunktion hinzugefügt, die von der Hostanwendung beim Beenden des plastischen Readers verwendet werden kann.
* Es wurden Codebeispiele aktualisiert, um die Azure Active Directory-Authentifizierung zu verwenden.

#### <a name="improvements"></a>Verbesserungen

* Das erweiterte C#-Codebeispiel wurde aktualisiert, um Word-Dokumente einzubeziehen.
* Codebeispiele wurden für die Verwendung von Version 0.0.3 aktualisiert.

#### <a name="fixes"></a>Fehlerbehebungen

* Es wurde ein Upgrade von „lodash“ auf Version 4.17.14 durchgeführt, um ein Sicherheitsrisiko zu beheben.
* Die C# MSAL-Bibliothek wurde aktualisiert, um ein Sicherheitsrisiko zu beheben.
* Es wurde ein Upgrade von „mixin-deep“ auf Version 1.3.2 durchgeführt, um ein Sicherheitsrisiko zu beheben.
* Es wurde ein Upgrade für „jest“, „webpack“ und „webpack-cli“ durchgeführt, die anfällige Versionen von „set-value“ und „mixin-deep“ verwendet haben, um ein Sicherheitsrisiko zu beheben.

<br>

## <a name="version-002"></a>Version 0.0.2

Diese Version enthält neue Features, Verbesserungen an Codebeispielen, Korrekturen für Sicherheitsrisiken und Fehlerbehebungen.

#### <a name="new-features"></a>Neue Funktionen

* Ein erweitertes Python-Codebeispiel wurde hinzugefügt.
* Ein Java-Codebeispiel für den Schnellstart wurde hinzugefügt.
* Ein einfaches Codebeispiel wurde hinzugefügt.

#### <a name="improvements"></a>Verbesserungen

* „ResourceName“ wurde in „cogSvcsSubdomain“ umbenannt.
* Geheimnisse wurden aus dem Code verschoben und es werden Umgebungsvariablen verwendet.
* Codebeispiele wurden für die Verwendung von Version 0.0.2 aktualisiert.

#### <a name="fixes"></a>Fehlerbehebungen

* Fehler bei der Barrierefreiheit für die Schaltfläche des plastischen Readers wurden behoben.
* Es wurden Fehler beim Scrollen behoben.
* Es wurde ein Upgrade des „handlebars“-Pakets auf Version 4.1.2 durchgeführt, um ein Sicherheitsrisiko zu beheben.
* Es wurden Fehler in den SDK-Komponententests behoben.
* Es wurden Fehler bei der Kompatibilität von JavaScript und Internet Explorer 11 behoben.
* Es wurden SDK-URLs aktualisiert.

<br>

## <a name="version-001"></a>Version 0.0.1

Das erste Release des JavaScript SDK des plastischen Readers.

* Das JavaScript SDK des plastischen Readers wurde hinzugefügt.
* Die Unterstützung zur Angabe der Sprache der Benutzeroberfläche wurde hinzugefügt.
* Es wurde ein Timeout zur Ermittlung hinzugefügt, wann bei der Funktion „launchAsync“ ein Timeoutfehler auftreten sollte.
* Die Unterstützung zur Angabe des Z-Index für das iframe-Element des plastischen Readers wurde hinzugefügt.
* Die Unterstützung zur Verwendung eines webview-Tags anstelle von „iframe“ wurde hinzugefügt, um die Kompatibilität mit Chrome-Apps sicherzustellen.
* Es wurden SDK-Komponententests hinzugefügt.
* Das erweiterte Node.js-Codebeispiel wurde hinzugefügt.
* Das erweiterte C#-Codebeispiel wurde hinzugefügt.
* Das C#-Codebeispiel für den Schnellstart wurde hinzugefügt.
* Die Paketkonfiguration, Yarn und andere Builddateien wurden hinzugefügt.
* Es wurden git-Konfigurationsdateien hinzugefügt.
* Es wurden README.md-Dateien zu Codebeispielen und zum SDK hinzugefügt.
* Die MIT-Lizenz wurde hinzugefügt.
* Anweisungen für Mitwirkende wurden hinzugefügt.
* Die statische Symbolschaltfläche für SVG-Ressourcen wurde hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit dem plastischen Reader:

* Lesen Sie die [Referenz zur Clientbibliothek für den plastischen Reader](./reference.md).
* Sehen Sie sich die [Clientbibliothek für den plastischen Reader auf GitHub](https://github.com/microsoft/immersive-reader-sdk) an.
