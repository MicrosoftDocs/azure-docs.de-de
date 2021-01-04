---
title: Sprach-Assistenten unter Windows – Entwurfsrichtlinien
titleSuffix: Azure Cognitive Services
description: Richtlinien für bewährte Methoden beim Entwerfen eines Sprach-Assistenten
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165144"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Entwerfen von Assistenten für Windows 10

Unter Windows 10 entwickelte Sprach-Assistenten müssen die unten aufgeführten Richtlinien für das Benutzererlebnis implementieren, um die bestmögliche Benutzerfreundlichkeit für die Sprachaktivierung unter Windows 10 zu bieten. In diesem Dokument erhalten Entwickler Erläuterungen zu den wichtigsten Aufgaben für die Integration eines Sprach-Assistenten in die Windows 10-Shell.

## <a name="contents"></a>Contents

- [Zusammenfassung der in Windows 10 unterstützten Sprachaktivierungsansichten](#summary-of-voice-activation-views-supported-in-windows-10)
- [Zusammenfassung der Anforderungen](#requirements-summary)
- [Bewährte Methoden für gutes Hören durch den Assistenten](#best-practices-for-good-listening-experiences)
- [Entwurfsleitfaden für die In-App-Sprachaktivierung](#design-guidance-for-in-app-voice-activation)
- [Entwurfsleitfaden für die Sprachaktivierung auf dem Sperrbildschirm](#design-guidance-for-voice-activation-above-lock)
- [Entwurfsleitfaden für die Sprachaktivierungsvorschau](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Zusammenfassung der in Windows 10 unterstützten Sprachaktivierungsansichten

Unter Windows 10 wird die Aktivierung im Kundenkontext vom Gerätekontext abgeleitet. Die folgende Zusammenfassungstabelle bietet eine allgemeine Übersicht über die verschiedenen Ansichten, wenn der Bildschirm aktiviert ist.

| Ansicht (Verfügbarkeit) | Gerätekontext | Kundenziel | Bedingung für Anzeige | Entwurfsanforderungen |
| --- | --- | --- | --- | --- |
| **In-App (19H1)** | Nach Entsperrung des Bildschirms, der Assistent hat den Fokus | Interagieren mit der Assistenten-App | Der Assistent verarbeitet die Anforderung in der App. | Hörfunktion in der Hauptansicht der App |
| **Auf Sperrbildschirm (19H2)** | Auf Sperrbildschirm, nicht authentifiziert | Interagieren mit dem Assistenten, aber aus einer Entfernung | Das System ist gesperrt, und der Assistent fordert die Aktivierung an. | Visuelle Vollbildelemente für Fernfeld-UI. Implementieren von Schließrichtlinien, um das Entsperren nicht zu blockieren. |
| **Sprachaktivierungsvorschau (20h1)** | Nach Entsperrung des Bildschirms, der Assistent hat nicht den Fokus | Interagieren mit dem Assistenten, aber weniger intrusiv | Das System ist entsperrt, und der Assistent fordert Hintergrundaktivierung an. | Minimale Canvas. Je nach Bedarf Größenanpassung oder Übergabe an Hauptansicht der App. |

## <a name="requirements-summary"></a>Zusammenfassung der Anforderungen

Für den Zugriff auf die verschiedenen Benutzererlebnisse ist ein minimaler Aufwand erforderlich. Allerdings müssen die Assistenten für jede Ansicht den richtigen Entwurfsleitfaden implementieren. Die folgende Tabelle enthält eine Checkliste der zu erfüllenden Anforderungen.

| **Sprachaktivierungsansicht** | **Zusammenfassung der Anforderungen an den Assistenten** |
| --- | --- |
| **In-App** | <ul><li>Verarbeiten der Anforderung in der App</li><li>Indikatoren auf der Benutzeroberfläche für die Hörstatus</li><li>Anpassung der Benutzeroberfläche bei Änderung der Fenstergröße</li></ul> |
| **Auf Sperrbildschirm** | <ul><li>Erkennen des Sperrzustands und Anfordern der Aktivierung</li><li>Kein Bereitstellen einer stets persistenten UX, die den Zugriff auf den Windows-Sperrbildschirm blockiert</li><li>Bereitstellen visueller Vollbildelemente und von Voice-First-Verhalten</li><li>Beachten der unten beschriebenen Schließrichtlinien</li><li>Beachten der unten beschriebenen Überlegungen zu Datenschutz und Sicherheit</li></ul> |
| **Sprachaktivierungsvorschau** | <ul><li>Erkennen des Entsperrungszustands und Anfordern der Hintergrundaktivierung</li><li>Zeichnen einer minimalen Hör-UX im Vorschaubereich</li><li>Zeichnen eines Schließsymbols (X) in der oberen rechten Ecke und Beenden des Audiostreamings, wenn auf das Symbol geklickt wird</li><li>Nach Bedarf Ändern der Größe oder Übergabe an die Hauptansicht der Assistent-App, um Antworten bereitzustellen</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Bewährte Methoden für gutes Hören durch den Assistenten

Die Assistenten sollten eine Hörfunktion bieten, um wichtiges Feedback bereitzustellen, damit der Kunde den Status des Assistenten erkennen kann. Im Folgenden finden Sie einige mögliche Zustände, die bei der Erstellung einer Assistenten-UX berücksichtigt werden sollten. Dies sind nur Vorschläge, keine obligatorischen Anweisungen.

- Der Assistent ist für die Spracheingabe verfügbar.
- Der Assistent wird gerade aktiviert (entweder durch ein Schlüsselwort oder durch Klicken auf die Mikrofonschaltfläche).
- Der Assistent streamt aktiv Audiodaten in die Assistenten-Cloud.
- Der Assistent ist bereit für die Spracheingabe durch den Kunden.
- Der Assistent hört, dass Wörter gesprochen werden.
- Der Assistent erkennt, dass die Spracheingabe des Kunden beendet ist.
- Der Assistent verarbeitet die Eingabe und bereitet eine Antwort vor.
- Der Assistent antwortet.

Auch wenn sich die Zustände schnell ändern, empfiehlt sich die Bereitstellung einer UX für einzelne Zustände, da die Dauer im Windows-Ökosystem variiert. Sowohl visuelles Feedback als auch kurze Glockentöne oder kurzes Zirpen, auch als „Earcons“ bezeichnet, können Teil der Lösung sein. Visuelle Karten in Verbindung mit Audiobeschreibungen sind ebenfalls gute Antwortoptionen.

## <a name="design-guidance-for-in-app-voice-activation"></a>Entwurfsleitfaden für die In-App-Sprachaktivierung

Wenn die Assistenten-App den Fokus hat, bedeutet dies, dass der Kunde mit der App interagieren möchte. Dann müssen alle Sprachaktivierungsfunktionen in der Hauptansicht der App behandelt werden. Die Größe dieser Ansicht kann vom Kunden geändert werden. Im Rest dieses Dokuments werden anhand des konkreten Beispiels eines Finanzdienst-Assistenten mit dem Namen „Contoso“ die Interaktionen mit der Assistenten-Shell erläutert. In diesem und den folgenden Diagrammen wird die Spracheingabe des Kunden in Sprechblasen auf der linken Seite angezeigt, und die Antworten des Assistenten werden in Sprechblasen auf der rechten Seite angezeigt.

**In-App-Ansicht. Anfangszustand bei Beginn der Sprachaktivierung:** 
![ Der Screenshot zeigt die App des Contoso-Finanz-Assistenten, die im Standardcanvas geöffnet ist. Eine Cartoonsprechblase auf der rechten Seite sagt „Contoso“.](media/voice-assistants/windows_voice_assistant/initial_state.png)

**In-App-Ansicht. Nach erfolgreicher Sprachaktivierung wird die Hörfunktion gestartet:** ![Screenshot des Sprach-Assistenten unter Windows während des Hörens durch den Sprach-Assistenten](media/voice-assistants/windows_voice_assistant/listening.png)

**In-App-Ansicht. Alle Antworten verbleiben in der App.** ![Screenshot des Sprach-Assistenten unter Windows während des Antwortens des Assistenten](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Entwurfsleitfaden für die Sprachaktivierung auf dem Sperrbildschirm

Ab 19H2 können auf der Windows-Sprachaktivierungsplattform basierende Assistenten auf dem Sperrbildschirm antworten.

### <a name="customer-opt-in"></a>Aktivierung durch Kunden

Die Sprachaktivierung auf dem Sperrbildschirm ist standardmäßig immer deaktiviert. Die Aktivierung durch den Kunden erfolgt über Windows-Einstellungen > „Datenschutz“ > „Stimmaktivierungsmodus aktivieren“. Ausführliche Informationen zur Überwachung und Eingabeaufforderung für diese Einstellung finden Sie in der [obigen Anleitung zur Implementierung der Sperrbildschirmfunktion](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Kein Ersatz für den Sperrbildschirm

Obwohl Benachrichtigungen oder andere Standardfunktionen der Integration der App in den Sperrbildschirm für den Assistenten verfügbar bleiben, definiert der Windows-Sperrbildschirm immer das anfängliche Kundenerlebnis, bis eine Sprachaktivierung erfolgt. Nachdem die Sprachaktivierung erkannt wurde, wird die Assistenten-App vorübergehend auf dem Sperrbildschirm angezeigt. Um Verwirrung bei den Kunden zu vermeiden, darf die auf dem Sperrbildschirm aktivierte Assistent-App nie eine Benutzeroberfläche anzeigen, auf der zur Eingabe von Anmeldeinformationen oder zum Anmelden aufgefordert wird.

![Screenshot eines Windows-Sperrbildschirms](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Auf dem Sperrbildschirm nach Sprachaktivierung

Wenn der Bildschirm aktiviert ist, wird die Assistenten-App im Vollbildmodus ohne Titelleiste auf dem Sperrbildschirm angezeigt. Größere visuelle Elemente und deutliche akustische Beschreibungen mit entsprechender primärer Sprachschnittstelle unterstützen Fälle, in denen der Abstand zum Benutzer zu groß ist, um die Benutzeroberfläche zu lesen, oder in denen er mit einer anderen Aufgabe (nicht am PC) beschäftigt ist.

Wenn der Bildschirm deaktiviert bleibt, kann die Assistenten-App mit einem Earcon angeben, dass der Assistent aktiviert wird, und ein sprachbasiertes Erlebnis bieten.

![Screenshot des Sprach-Assistenten auf dem Sperrbildschirm](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Schließrichtlinien

Der Assistent muss die Schließrichtlinien in diesem Abschnitt implementieren, damit sich Kunden leichter anmelden können, wenn sie ihren Windows-PCs wieder verwenden möchten. Der Assistent muss folgende Anforderungen implementieren:

- **Alle Assistenten-Canvas, in denen die obige Sperre angezeigt wird, müssen in der oberen rechten Ecke ein X enthalten** , mit dem der Assistent geschlossen wird.
- **Die Assistenten-App muss auch durch Drücken einer beliebigen Taste geschlossen werden** . Tastatureingaben sind ein typisches Signal für Sperrbildschirm-Apps, dass sich der Kunde anmelden möchte. Daher sollten Tastatur-/Texteingaben nicht an die App weitergeleitet werden. Stattdessen sollte sich die App selbst schließen, wenn Tastatureingaben erkannt werden, damit sich der Kunde problemlos bei seinem Gerät anmelden kann.
- **Wenn der Bildschirm deaktiviert wird, muss sich die App selbst schließen.** Dadurch wird sichergestellt, dass der Anmeldebildschirm beim nächsten Mal, wenn der Kunde den PC verwendet, für die Anmeldung bereit ist.
- Wenn die App &quot;gerade verwendet wird&quot;, kann ihre Ausführung auf dem Sperrbildschirm fortgesetzt werden. &quot;gerade verwendet&quot; bezeichnet eine beliebige Eingabe oder Ausgabe. Beispielsweise kann die App beim Streaming von Musik oder Video auf dem Sperrbildschirm fortgesetzt werden. &quot;Folgen Sie uns&quot; und andere Dialogfeldschritte mit mehreren Durchläufen sind zulässig, damit die App auf dem Sperrbildschirm aktiviert bleibt.
- **Implementierungsdetails zum Schließen der Anwendung** finden Sie [in der obigen Anleitung zur Implementierung der Sperrbildschirmfunktion](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Der Screenshot zeigt die obige Sperransicht der App des Contoso-Finanz-Assistenten.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Screenshot eines Desktops, der den Windows-Sperrbildschirm anzeigt.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>Überlegungen zu Datenschutz und Sicherheit auf dem Sperrbildschirm

Viele PCs sind portierbar, jedoch nicht immer in Reichweite des Kunden. Sie werden möglicherweise kurz in Hotelzimmern, auf Flugzeugsitzen oder in Arbeitsbereichen gelassen, wo andere Personen physischen Zugriff haben. Wenn für auf dem Sperrbildschirm aktivierte Assistenten keine Vorkehrungen getroffen wurden, können sie sogenannten &quot;[Evil-Maid](https://en.wikipedia.org/wiki/Evil_maid_attack)&quot;-Angriffen ausgesetzt sein.

Daher sollten für die Assistenten die Anweisungen in diesem Abschnitt befolgt werden, um die Sicherheit zu gewährleisten. Interaktionen auf dem Sperrbildschirm erfolgen, wenn der Windows-Benutzer nicht authentifiziert ist. Dies bedeutet, dass im Allgemeinen **Eingaben in den Assistenten ebenfalls als nicht authentifiziert** behandelt werden sollen.

- Assistenten sollten **eine Zulassungsliste für Funktionen implementieren, um Funktionen zu erkennen, auf die ohne Risiko auf dem Sperrbildschirm zugegriffen werden kann** .
- Technologien der Sprecheridentifikation können bei der Minderung einiger Risiken genutzt werden, die Sprecheridentifikation ersetzt jedoch nicht die Windows-Authentifizierung.
- Die Zulassungsliste für Funktionen sollte drei Klassen von Aktionen oder Funktionen beinhalten:

| **Aktionsklasse** | **Beschreibung** | **Beispiele (keine vollständige Liste)** |
| --- | --- | --- |
| Ohne Authentifizierung sicher | Allgemeine Informationen oder grundlegende App-Befehle und -Steuerelemente | &quot;Wie spät ist es?&quot;, &quot;Nächsten Track abspielen&quot; |
| Ohne Sprecheridentifikation sicher | Identitätswechselrisiko, Offenlegen personenbezogener Informationen | &quot;Was ist mein nächster Termin?&quot;&quot;Meine Einkaufsliste überprüfen&quot;, &quot;Anruf annehmen&quot; |
| Nur sicher nach der Windows-Authentifizierung | Aktionen mit hohem Risiko, die ein Angreifer verwenden kann, um dem Kunden Schaden zuzufügen | &quot;Weitere Lebensmittel kaufen&quot;, &quot;Meinen (wichtigen) Termin löschen&quot;, &quot;Eine (niederträchtige) Textnachricht senden&quot;, &quot;Eine (verwerfliche) Webseite öffnen&quot; |

Im Fall von Contoso sind allgemeine öffentliche Börseninformationen ohne Authentifizierung sicher. Kundenspezifische Informationen, z. B. die Anzahl der im Besitz befindlichen Aktien, sind mit Sprecheridentifikation wahrscheinlich sicher. Das Kaufen oder Verkaufen von Aktien sollte jedoch nie ohne Windows-Authentifizierung zugelassen werden.

Für zusätzliche Sicherheit **werden Weblinks und andere Arten des Startens einer App durch eine andere App immer von Windows blockiert, bis sich der Kunde anmeldet** . Als letztes Mittel der Risikominderung behält sich Microsoft das Recht vor, eine Anwendung aus der Zulassungsliste der aktivierten Assistenten zu entfernen, wenn ein schwerwiegendes Sicherheitsproblem nicht rechtzeitig behoben wird.

## <a name="design-guidance-for-voice-activation-preview"></a>Entwurfsleitfaden für die Sprachaktivierungsvorschau

Wenn die Assistenten-App nach Entsperrung des Sperrbildschirms _nicht_ den Fokus hat, bietet Windows für nahtlose Kundeninteraktionen eine weniger intrusive Sprachaktivierungs-Benutzeroberfläche. Dies gilt insbesondere für Fälle falscher Aktivierungen, die stark störend wirken, wenn durch sie die App vollständig gestartet wird. Der Grundgedanke ist, dass jeder Assistent auch in der Shell, dem Taskleistensymbol des Assistenten, verfügbar ist. Wenn die Hintergrundaktivierung angefordert wird, wird über dem Taskleistensymbol des Assistenten eine kleine Ansicht angezeigt. Die Assistenten sollten in dieser Canvas eine kleine Hörbenutzeroberfläche bereitstellen. Nach der Verarbeitung der Anforderungen können Assistenten die Größe dieser Ansicht ändern, um eine kontextbasierte Antwort anzuzeigen oder in der Hauptansicht der App größere, ausführlichere visuelle Elemente anzuzeigen.

- Damit die Vorschau minimal bleibt, verfügt sie über keine Titelleiste. Daher **muss der Assistent oben rechts ein X zeichnen, damit Kunden die Ansicht schließen können** . Informationen zu den APIs, die beim Klicken auf die Schaltfläche zum Schließen aufgerufen werden müssen, finden Sie unter [Schließen der Anwendung](windows-voice-assistants-implementation-guide.md#closing-the-application).
- Zur Unterstützung der Sprachaktivierungsvorschau können Assistenten Kunden während der ersten Ausführung auffordern, den Assistenten an die Taskleiste anzuheften.

**Sprachaktivierungsvorschau: Anfangszustand**

Der Assistent „Contoso“ wird auf der Taskleiste angezeigt: das kreisförmige Wirbelsymbol.

![Screenshot des Sprach-Assistenten unter Windows als Taskleistensymbol vor der Aktivierung](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**Während der Aktivierung** fordert der Assistent die Hintergrundaktivierung an. Der Assistent erhält einen kleinen Vorschaubereich (Standardbreite 408 und Höhe 248). Wenn die serverseitige Sprachaktivierung bestimmt, dass das Signal falsch positiv war, kann diese Ansicht geschlossen werden, um die Unterbrechung möglichst gering zu halten.

![Screenshot des Sprach-Assistenten unter Windows in Kompaktansicht beim Überprüfen der Aktivierung](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Wenn die endgültige Aktivierung bestätigt wurde** , präsentiert der Assistent die Hör-UX. Der Assistent muss immer rechts oben in der Sprachaktivierungsvorschau ein X zum Schließen zeichnen.

![Screenshot des hörenden Sprach-Assistenten unter Windows in der Kompaktansicht](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

In der Sprachaktivierungsvorschau können **schnelle Antworten** angezeigt werden. Mit „TryResizeView“ können Assistenten verschiedene Größen anfordern.

![Screenshot des antwortenden Sprach-Assistenten unter Windows in der Kompaktansicht](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Wechsel der Ansicht** . Der Assistent kann jederzeit zur Hauptansicht der App wechseln, um weitere Informationen, Dialogfelder oder Antworten bereitzustellen, die einen größeren Bildschirm erfordern. Implementierungsdetails finden Sie im Abschnitt [Wechsel von der Kompaktansicht zur vollständigen Ansicht](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view).

![Screenshots des Sprach-Assistenten unter Windows vor und nach dem Erweitern der Kompaktansicht](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Beginnen Sie mit der Entwicklung eines Sprach-Assistenten](how-to-windows-voice-assistants-get-started.md)