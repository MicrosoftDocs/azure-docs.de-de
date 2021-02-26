---
title: 'Benutzerinteraktion und Zeitlimits in Durable Functions: Azure'
description: In diesem Artikel wird erläutert, wie Sie die Benutzerinteraktion und Zeitlimits in der Erweiterung „Durable Functions“ für Azure Functions behandeln.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: dd7f8416b2f4520ec8e94c8608f753f7412afc4d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627371"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Benutzerinteraktion in Durable Functions: Beispiel zur Telefonüberprüfung

In diesem Beispiel wird demonstriert, wie eine [Durable Functions](durable-functions-overview.md)-Orchestrierung erstellt wird, die eine Benutzerinteraktion beinhaltet. Wenn eine Person in einen automatisierten Prozess einbezogen wird, muss der Prozess asynchron Benachrichtigungen an die Person senden und Antworten empfangen können. Zudem muss die Möglichkeit berücksichtigt werden, dass die Person nicht erreichbar ist. (In diesem letzten Teil gewinnen Zeitlimits an Bedeutung.)

In diesem Beispiel wird ein SMS-basiertes Telefonüberprüfungssystem implementiert. Diese Flusstypen werden häufig bei der Überprüfung der Telefonnummer eines Kunden oder bei der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) verwendet. Es handelt sich um ein eindrucksvolles Beispiel, da die gesamte Implementierung über einige kleine Funktionen erfolgt. Es ist kein externer Datenspeicher, z.B. eine Datenbank, erforderlich.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Übersicht über das Szenario

Bei der Telefonüberprüfung wird überprüft, ob Benutzer Ihrer Anwendung Spammer sind und ob die Angaben zu dieser Person korrekt sind. Die mehrstufige Authentifizierung wird häufig angewendet, um Benutzerkonten vor Hackern zu schützen. Die Herausforderung bei der Implementierung Ihrer eigenen Telefonüberprüfung besteht darin, dass eine **zustandsbehaftete Interaktion** mit einer Person erforderlich ist. In der Regel wird Code für einen Benutzer bereitgestellt (z.B. eine vierstellige Zahl). Dieser muss **in einem angemessenen Zeitraum** darauf reagieren.

Normale Azure-Funktionen sind zustandslos (wie viele andere Cloudendpunkte auf anderen Plattformen). Folglich umfassen diese Interaktionsarten explizit eine externe Verwaltung der Zustände in einer Datenbank oder einem anderen persistenten Speicher. Zusätzlich muss die Interaktion in mehrere Funktionen unterteilt werden, die zusammen koordiniert werden können. Sie benötigen beispielsweise mindestens eine Funktion, um sich für einen Code zu entscheiden, diesen an einer bestimmten Position persistent zu speichern und an das Telefon des Benutzers zu senden. Darüber hinaus ist mindestens eine weitere Funktion erforderlich, damit Sie eine Antwort vom Benutzer empfangen und diese wieder dem ursprünglichen Funktionsaufruf zuordnen können, um anschließend die Codevalidierung durchführen zu können. Ein Zeitlimit ist ein weiterer wichtiger Aspekt zur Gewährleistung der Sicherheit. Dieses Szenario kann schnell ziemlich komplex werden.

Mit Durable Functions wird die Komplexität dieses Szenarios erheblich reduziert. Wie Sie in diesem Beispiel sehen, kann eine Orchestratorfunktion die zustandsbehaftete Interaktion einfach und ohne die Einbeziehung von externen Datenspeichern verwalten. Da Orchestratorfunktionen *dauerhaft* sind, sind diese interaktiven Flüsse auch sehr zuverlässig.

## <a name="configuring-twilio-integration"></a>Konfigurieren der Twilio-Integration

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Die Funktionen

In diesem Artikel werden die folgenden Funktionen in der Beispiel-App schrittweise erläutert:

* `E4_SmsPhoneVerification`: Eine [Orchestratorfunktion](durable-functions-bindings.md#orchestration-trigger), die den Telefonüberprüfungsprozess durchführt, einschließlich der Verwaltung von Timeouts und Wiederholungsversuchen.
* `E4_SendSmsChallenge`: Eine [Aktivitätsfunktion](durable-functions-bindings.md#activity-trigger), die einen Code über eine SMS sendet.

> [!NOTE]
> Die Funktion `HttpStart` in [Beispiel-App und Schnellstart](#prerequisites) fungiert als [Orchestrierungsclient](durable-functions-bindings.md#orchestration-client), der die Orchestratorfunktion auslöst.

### <a name="e4_smsphoneverification-orchestrator-function"></a>Orchestratorfunktion „E4_SmsPhoneVerification“

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Auch wenn es auf den ersten Blick nicht offensichtlich ist: Dieser Orchestrator verstößt nicht gegen die [Einschränkungen der deterministischen Orchestrierung](durable-functions-code-constraints.md). Sie ist deterministisch, weil die Eigenschaft `CurrentUtcDateTime` zur Berechnung der Zeit bis zum Ablauf des Timers verwendet wird und sie an diesem Punkt im Orchestratorcode bei jeder Wiedergabe den gleichen Wert zurückgibt. Dieses Verhalten ist wichtig, um sicherzustellen, dass jeder wiederholte Aufruf in `Task.WhenAny` den gleichen `winner` ergibt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die Funktion **E4_SmsPhoneVerification** verwendet für Orchestratorfunktionen die Standarddatei *function.json*.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Im Folgenden wird der Code dargestellt, der die Funktion implementiert:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Auch wenn es auf den ersten Blick nicht offensichtlich ist: Dieser Orchestrator verstößt nicht gegen die [Einschränkungen der deterministischen Orchestrierung](durable-functions-code-constraints.md). Sie ist deterministisch, weil die Eigenschaft `currentUtcDateTime` zur Berechnung der Zeit bis zum Ablauf des Timers verwendet wird und sie an diesem Punkt im Orchestratorcode bei jeder Wiedergabe den gleichen Wert zurückgibt. Dieses Verhalten ist wichtig, um sicherzustellen, dass jeder wiederholte Aufruf in `context.df.Task.any` den gleichen `winner` ergibt.

# <a name="python"></a>[Python](#tab/python)

Die Funktion **E4_SmsPhoneVerification** verwendet für Orchestratorfunktionen die Standarddatei *function.json*.

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/function.json)]

Im Folgenden wird der Code dargestellt, der die Funktion implementiert:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/\_\_init\_\_.py)]

> [!NOTE]
> Auch wenn es auf den ersten Blick nicht offensichtlich ist: Dieser Orchestrator verstößt nicht gegen die [Einschränkungen der deterministischen Orchestrierung](durable-functions-code-constraints.md). Sie ist deterministisch, weil die Eigenschaft `currentUtcDateTime` zur Berechnung der Zeit bis zum Ablauf des Timers verwendet wird und sie an diesem Punkt im Orchestratorcode bei jeder Wiedergabe den gleichen Wert zurückgibt. Dieses Verhalten ist wichtig, um sicherzustellen, dass jeder wiederholte Aufruf in `context.df.Task.any` den gleichen `winner` ergibt.

---

Nach dem Start führt diese Orchestratorfunktion Folgendes aus:

1. Sie ruft eine Telefonnummer ab, an die die SMS-Benachrichtigung *gesendet* wird.
2. Sie ruft **E4_SendSmsChallenge** auf, um eine SMS an den Benutzer zu senden, und gibt den erwarteten vierstelligen Abfragecode zurück.
3. Sie erstellt einen permanenten Timer, der 90 Sekunden nach der aktuellen Uhrzeit ausgelöst wird.
4. Parallel zum Timer wartet sie auf ein **SmsChallengeResponse**-Ereignis des Benutzers.

Der Benutzer erhält eine SMS-Nachricht mit einem vierstelligen Code. Zum Abschließen des Überprüfungsprozesses muss er diesen vierstelligen Code innerhalb von 90 Sekunden zurück an die Instanz der Orchestratorfunktion senden. Wenn er den falschen Code übergibt, hat er weitere drei Versuche, um den Code richtig einzugeben (innerhalb der 90 Sekunden).

> [!WARNING]
> Es ist wichtig, [Timer abzubrechen](durable-functions-timers.md), wenn Sie diese nicht mehr benötigen, z.B. wenn wie im obigen Beispiel eine Abfragerückmeldung akzeptiert wurde.

## <a name="e4_sendsmschallenge-activity-function"></a>Aktivitätsfunktion „E4_SendSmsChallenge“

Die Funktion **E4_SendSmsChallenge** sendet die SMS-Nachricht mit dem vierstelligen Code über die Twilio-Bindung an den Benutzer.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Sie müssen das NuGet-Paket `Microsoft.Azure.WebJobs.Extensions.Twilio` installieren, um den Beispielcode auszuführen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die Datei *function.json* wird wie folgt definiert:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

Dies ist der Code, der den vierstelligen Abfragecode generiert und die SMS-Nachricht sendet:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

# <a name="python"></a>[Python](#tab/python)

Die Datei *function.json* wird wie folgt definiert:

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/function.json)]

Dies ist der Code, der den vierstelligen Abfragecode generiert und die SMS-Nachricht sendet:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>Ausführen des Beispiels

Wenn Sie die über HTTP ausgelösten Funktionen verwenden, die im Beispiel enthalten sind, können Sie mit der Orchestrierung beginnen, indem Sie folgende HTTP POST-Anforderung senden:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Die Orchestratorfunktion empfängt die angegebene Telefonnummer und sendet sofort eine SMS-Nachricht mit einem zufällig generierten vierstelligen Überprüfungscode an diese Nummer, &mdash;z.B. *2168*. Anschließend wartet die Funktion 90 Sekunden auf eine Antwort.

Für eine Beantwortung mit dem Code können Sie [`RaiseEventAsync` (.NET) oder `raiseEvent` (JavaScript)](durable-functions-instance-management.md) in einer anderen Funktion verwenden oder den HTTP POST-Webhook **sendEventUrl** aufrufen, auf den oben in der 202-Antwort verwiesen wird. Dabei wird `{eventName}` durch den Namen des Ereignisses, `SmsChallengeResponse`, ersetzt:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Wenn Sie dies vor Ablauf des Timers senden, ist die Orchestrierung abgeschlossen, und das Feld `output` wird auf `true` festgelegt, was auf eine erfolgreiche Überprüfung hinweist.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Wenn Sie den Timer ablaufen lassen oder viermal den falschen Code eingeben, können Sie den Status abfragen und die Ausgabe `false` der Orchestrierungsfunktion anzeigen, die angibt, dass die Telefonüberprüfung fehlgeschlagen ist.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel werden einige der erweiterten Funktionen von Durable Functions veranschaulicht, insbesondere `WaitForExternalEvent`- und `CreateTimer`-APIs. Sie haben gesehen, wie diese mit `Task.WaitAny` kombiniert werden können, um ein zuverlässiges Zeitlimitsystem zu implementieren. Dies ist bei der Interaktion mit realen Personen häufig hilfreich. Weitere Informationen zur Verwendung von Durable Functions finden Sie in einer Reihe von Artikeln, die detaillierte Erläuterungen zu bestimmten Themen bieten.

> [!div class="nextstepaction"]
> [Zum ersten Artikel der Reihe wechseln](durable-functions-bindings.md)
