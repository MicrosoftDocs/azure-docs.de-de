---
title: Vorlagen
description: In diesem Thema werden Vorlagen für Azure Notification Hubs erläutert.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 02473eb5649c7d201b6a54fd57faea997c1a21cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60872089"
---
# <a name="templates"></a>Vorlagen

Mithilfe von Vorlagen kann eine Clientanwendung das genaue Format der Benachrichtigungen angeben, die sie empfangen möchte. Die Verwendung von Vorlagen bietet einer App viele verschiedene Vorteile, einschließlich der folgenden:

- Ein plattformunabhängiges Back-End
- Personalisierte Benachrichtigungen
- Unabhängigkeit von der Clientversion
- Einfache Lokalisierung

Dieser Abschnitt enthält zwei ausführliche Beispiele. Sie zeigen, wie Vorlagen verwendet werden, um plattformunabhängige Benachrichtigungen plattformübergreifend an alle Geräte zu senden und um Übertragungsbenachrichtigungen für jedes Gerät zu personalisieren.

## <a name="using-templates-cross-platform"></a>Plattformübergreifende Verwendung von Vorlagen

Das Standardverfahren zum Senden von Pushbenachrichtigungen besteht darin, für jede Benachrichtigung, die gesendet werden soll, eine bestimmte Nutzlast an Plattformbenachrichtigungsdienste (WNS, APNS) zu senden. Beim Senden einer Benachrichtigung an APNS entspricht die Nutzlast beispielsweise einem JSON-Objekt im folgenden Format:

```json
{"aps": {"alert" : "Hello!" }}
```

Zum Senden einer ähnlichen Popupnachricht für eine Windows Store-Anwendung sieht die XML-Nutzlast wie folgt aus:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Sie können ähnliche Nutzlasten für MPNS (Windows Phone)- und FCM (Android)-Plattformen erstellen.

Durch diese Anforderung ist das App-Back-End gezwungen, für jede Plattform unterschiedliche Nutzlasten zu erzeugen, und ist dadurch effektiv für einen Teil der Darstellungsschicht der App verantwortlich. Die Lokalisierung und grafischen Layouts (insbesondere für Windows Store-Apps, die Benachrichtigungen für verschiedene Kacheltypen umfassen) sind dabei nicht unproblematisch.

Das Notification Hubs-Vorlagenfeature ermöglicht einer Client-App das Erstellen spezieller Registrierungen – so genannter Vorlagenregistrierungen – die neben den Tags eine Vorlage enthalten. Mit dem Notification Hubs-Vorlagenfeature kann eine Client-App Geräten Vorlagen zuordnen, und zwar unabhängig davon ob Sie mit Installationen (bevorzugt) oder Registrierungen arbeiten. Aus den zuvor aufgeführten Nutzlastbeispielen ergibt sich, dass der eigentliche Benachrichtigungstext (Hello!) die einzige plattformunabhängige Information ist. Eine Vorlage umfasst eine Reihe von Anweisungen für den Notification Hub, die angeben, wie eine plattformunabhängige Nachricht für die Registrierung der spezifischen Client-App formatiert werden soll. Im vorherigen Beispiel umfasst die plattformunabhängige Nachricht nur eine Eigenschaft: `message = Hello!`.

Das Verfahren wird in der folgenden Abbildung veranschaulicht.

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Die Vorlage für die Registrierung der iOS-Client-App sieht wie folgt aus:

```json
{"aps": {"alert": "$(message)"}}
```

Die entsprechende Vorlage für die Windows Store-Client-App entspricht:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Beachten Sie, dass die tatsächliche Nachricht durch den Ausdruck $(message) ersetzt wird. Dieser Ausdruck weist den Notification Hub an, bei jedem Senden einer Nachricht an diese bestimmte Registrierung eine der Vorlage entsprechende Nachricht zu erstellen, und fügt den allgemeinen Wert ein.

Bei Verwendung des Installationsmodells arbeiten, enthält der "templates"-Schlüssel der Installation ein JSON-Objekt mit mehreren Vorlagen. Bei Verwendung des Registrierungsmodells kann die Clientanwendung mehrere Registrierungen erstellen, um mehrere Vorlagen zu verwenden, also beispielsweise eine Vorlage für Benachrichtigungen und eine für Kachelaktualisierungen. Clientanwendungen können auch systemeigene Registrierungen (Registrierungen ohne Vorlage) und Vorlagenregistrierungen kombinieren.

Der Notification Hub sendet eine Benachrichtigung für jede Vorlage, ohne zu berücksichtigen, ob sie zur selben Client-App gehören. Dieses Verhalten kann verwendet werden, um plattformunabhängige Benachrichtigungen in weitere Benachrichtigungen zu übersetzen. Beispielsweise kann dieselbe plattformunabhängige Nachricht an den Notification Hub nahtlos in eine Popupbenachrichtigung und eine Kachelaktualisierung übersetzt werden, ohne dass das Back-End daran beteiligt ist. Einige Plattformen (beispielsweise iOS) reduzieren ggf. mehrere Benachrichtigungen an dasselbe Gerät, wenn diese innerhalb eines kurzen Zeitraums gesendet werden.

## <a name="using-templates-for-personalization"></a>Verwenden von Vorlagen zur Personalisierung

Ein weiterer Vorteil von Vorlagen besteht darin, dass Benachrichtigungen mithilfe von Notification Hubs pro Registrierung personalisiert werden können. Beispiel: Eine Wetter-App zeigt eine Kachel mit den Wetterbedingungen an einem bestimmten Ort an. Ein Benutzer kann zwischen Grad Celsius und Fahrenheit und einer eintägigen oder fünftägigen Vorhersage wählen. Mithilfe von Vorlagen kann sich jede Client-App-Installation für das erforderliche Format (1 Tag Celsius, 1 Tag Fahrenheit, 5 Tage Celsius, 5 Tage Fahrenheit) registrieren und das Back-End eine einzelne Nachricht senden lassen, die alle erforderlichen Informationen zum Füllen dieser Vorlagen enthält (z. B. eine fünftägige Vorhersage mit Gradangaben in Celsius und Fahrenheit).

Die Vorlage für die eintägige Vorhersage mit Temperaturangaben in Celsius sieht wie folgt aus:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Die an den Notification Hub gesendete Nachricht enthält die folgenden Eigenschaften:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Durch die Verwendung dieses Musters sendet das Back-End nur eine einzelne Nachricht und muss keine bestimmten Personalisierungsoptionen für die App-Benutzer speichern. Dieses Szenario wird in der folgenden Abbildung veranschaulicht.

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Registrieren von Vorlagen

Informationen zur Vorlagenregistrierung mit dem Installationsmodell (bevorzugt) oder dem Registrierungsmodell finden Sie unter [Registrierungsverwaltung](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Sprache für Vorlagenausdrücke

Vorlagen sind auf das XML- oder JSON-Dokumentformat beschränkt. Außerdem können Sie Ausdrücke nur an bestimmten Stellen einfügen, z. B. in Node-Attributen oder -Werten für XML und Zeichenfolgen-Eigenschaftswerten für JSON.

In der folgenden Tabelle wird die in Vorlagen zulässige Sprache gezeigt:

| Ausdruck       | BESCHREIBUNG |
| ---------------- | --- |
| $(prop)          | Verweist auf eine Ereigniseigenschaft mit dem angegebenen Namen. Bei Eigenschaftennamen wird nicht zwischen Groß- und Kleinschreibung unterschieden. Dieser Ausdruck wird in den Textwert der Eigenschaft oder in eine leere Zeichenfolge aufgelöst, wenn die Eigenschaft nicht vorhanden ist. |
| $(prop, n)       | Wie oben, allerdings wird der Text bei n Zeichen explizit abgeschnitten. Bei $(title, 20) wird der Inhalt der title-Eigenschaft beispielsweise nach 20 Zeichen abgeschnitten. |
| .(prop, n)       | Wie oben, allerdings werden dem Text beim Abschneiden drei Punkte als Suffix hinzugefügt. Die Gesamtgröße der abgeschnittenen Zeichenfolge und des Suffixes überschreitet n Zeichen nicht. Bei .(title, 20) mit der Eingabeeigenschaft "This is the title line" erhalten Sie **This is the title...** |
| %(prop)          | Ähnlich wie $(name) mit der Ausnahme, dass die Ausgabe URI-codiert ist. |
| #(prop)          | Wird in JSON-Vorlagen (z.B. für iOS- und Android-Vorlagen) verwendet.<br><br>Diese Funktion verhält sich genauso wie die oben beschriebene Funktion $(prop), außer bei Verwendung in JSON-Vorlagen (z.B. Apple-Vorlagen). Wenn in diesem Fall die Funktion nicht von „{','}“ umschlossen ist (z.B. 'myJsonProperty' : '#(name)') und zu einer Zahl im JavaScript-Format ausgewertet wird (z.B. regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*))(\.&#91;0-9&#93;+)?((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?), ist der JSON-Ausgabecode eine Zahl.<br><br>Beispiel: 'badge : '#(name)' wird zu 'badge' : 40 (nicht '40'). |
| 'text' oder "text" | Ein Literal. Literale enthalten beliebigen Text, der in einfache oder doppelte Anführungszeichen eingeschlossen ist. |
| expr1 + expr2    | Der Verkettungsoperator, der zwei Ausdrücke zu einer Zeichenfolge verbindet. |

Die Ausdrücke können eine der oben beschriebenen Formen aufweisen.

Bei Verwendung der Verkettung muss der gesamte Ausdruck in `{}` eingeschlossen werden. Beispiel: `{$(prop) + ‘ - ’ + $(prop2)}`.

Die folgende Vorlage ist beispielsweise keine gültige XML-Vorlage:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Wie zuvor erläutert, müssen Ausdrücke bei Verwendung der Verkettung in geschweifte Klammern eingeschlossen werden. Beispiel:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```
