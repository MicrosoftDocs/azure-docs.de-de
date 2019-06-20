---
title: 'Benutzerinteraktion und Zeitlimits in Durable Functions: Azure'
description: In diesem Artikel wird erläutert, wie Sie die Benutzerinteraktion und Zeitlimits in der Erweiterung „Durable Functions“ für Azure Functions behandeln.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: cf43e29e967ee6f920eb38feb9c73d70f9621ea4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123313"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Benutzerinteraktion in Durable Functions: Beispiel zur Telefonüberprüfung

In diesem Beispiel wird demonstriert, wie eine [Durable Functions](durable-functions-overview.md)-Orchestrierung erstellt wird, die eine Benutzerinteraktion beinhaltet. Wenn eine Person in einen automatisierten Prozess einbezogen wird, muss der Prozess asynchron Benachrichtigungen an die Person senden und Antworten empfangen können. Zudem muss die Möglichkeit berücksichtigt werden, dass die Person nicht erreichbar ist. (In diesem letzten Teil gewinnen Zeitlimits an Bedeutung.)

In diesem Beispiel wird ein SMS-basiertes Telefonüberprüfungssystem implementiert. Diese Flusstypen werden häufig bei der Überprüfung der Telefonnummer eines Kunden oder bei der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) verwendet. Dies ist ein eindrucksvolles Beispiel, da die gesamte Implementierung über einige kleine Funktionen erfolgt. Es ist kein externer Datenspeicher, z.B. eine Datenbank, erforderlich.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Übersicht über das Szenario

Bei der Telefonüberprüfung wird überprüft, ob Benutzer Ihrer Anwendung Spammer sind und ob die Angaben zu dieser Person korrekt sind. Die mehrstufige Authentifizierung wird häufig angewendet, um Benutzerkonten vor Hackern zu schützen. Die Herausforderung bei der Implementierung Ihrer eigenen Telefonüberprüfung besteht darin, dass eine **zustandsbehaftete Interaktion** mit einer Person erforderlich ist. In der Regel wird Code für einen Benutzer bereitgestellt (z.B. eine vierstellige Zahl). Dieser muss **in einem angemessenen Zeitraum** darauf reagieren.

Normale Azure-Funktionen sind zustandslos (wie viele andere Cloudendpunkte auf anderen Plattformen). Folglich umfassen diese Interaktionsarten explizit eine externe Verwaltung der Zustände in einer Datenbank oder einem anderen persistenten Speicher. Zusätzlich muss die Interaktion in mehrere Funktionen unterteilt werden, die zusammen koordiniert werden können. Sie benötigen beispielsweise mindestens eine Funktion, um sich für einen Code zu entscheiden, diesen an einer bestimmten Position persistent zu speichern und an das Telefon des Benutzers zu senden. Darüber hinaus ist mindestens eine weitere Funktion erforderlich, damit Sie eine Antwort vom Benutzer empfangen und diese wieder dem ursprünglichen Funktionsaufruf zuordnen können, um anschließend die Codevalidierung durchführen zu können. Ein Zeitlimit ist ein weiterer wichtiger Aspekt zur Gewährleistung der Sicherheit. Dies kann ziemlich schnell komplex werden.

Mit Durable Functions wird die Komplexität dieses Szenarios erheblich reduziert. Wie Sie in diesem Beispiel sehen, kann eine Orchestratorfunktion die zustandsbehaftete Interaktion einfach und ohne die Einbeziehung von externen Datenspeichern verwalten. Da Orchestratorfunktionen *dauerhaft* sind, sind diese interaktiven Flüsse auch sehr zuverlässig.

## <a name="configuring-twilio-integration"></a>Konfigurieren der Twilio-Integration

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Die Funktionen

In diesem Artikel werden die folgenden Funktionen in der Beispiel-App schrittweise erläutert:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

In den folgenden Abschnitten werden die Konfiguration und der Code beschrieben, die für C#-Skripts und JavaScript verwendet werden. Der Code für die Visual Studio-Entwicklung ist am Ende des Artikels angegeben.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Die Orchestrierung der SMS-Überprüfung (Visual Studio Code und Azure-Portal-Beispielcode)

Die Funktion **E4_SmsPhoneVerification** verwendet für Orchestratorfunktionen die Standarddatei *function.json*.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Im Folgenden wird der Code dargestellt, der die Funktion implementiert:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Nach dem Start führt diese Orchestratorfunktion Folgendes aus:

1. Sie ruft eine Telefonnummer ab, an die die SMS-Benachrichtigung *gesendet* wird.
2. Sie ruft **E4_SendSmsChallenge** auf, um eine SMS an den Benutzer zu senden, und gibt den erwarteten vierstelligen Abfragecode zurück.
3. Sie erstellt einen permanenten Timer, der 90 Sekunden nach der aktuellen Uhrzeit ausgelöst wird.
4. Parallel zum Timer wartet sie auf ein **SmsChallengeResponse**-Ereignis des Benutzers.

Der Benutzer erhält eine SMS-Nachricht mit einem vierstelligen Code. Zum Abschließen des Überprüfungsprozesses muss er diesen vierstelligen Code innerhalb von 90 Sekunden zurück an die Instanz der Orchestratorfunktion senden. Wenn er den falschen Code übergibt, hat er weitere drei Versuche, um den Code richtig einzugeben (innerhalb der 90 Sekunden).

> [!NOTE]
> Es ist möglicherweise nicht gleich erkennbar, aber diese Orchestratorfunktion ist komplett deterministisch. Grund hierfür ist, dass die Eigenschaften `CurrentUtcDateTime` (.NET) oder `currentUtcDateTime` (JavaScript) zur Berechnung der Zeit bis zum Ablauf des Timers verwendet werden und diese Eigenschaften an diesem Punkt im Orchestratorcode bei jeder Wiedergabe den gleichen Wert zurückgeben. Dies ist wichtig, um sicherzustellen, dass jeder wiederholte Aufruf von `Task.WhenAny` (.NET) oder `context.df.Task.any` (JavaScript) den gleichen `winner` ergibt.

> [!WARNING]
> Es ist wichtig, [Timer abzubrechen](durable-functions-timers.md), wenn Sie diese nicht mehr benötigen, z.B. wenn wie im obigen Beispiel eine Abfragerückmeldung akzeptiert wurde.

## <a name="send-the-sms-message"></a>Senden der SMS-Nachricht

Die Funktion **E4_SendSmsChallenge** sendet die SMS-Nachricht mit dem vierstelligen Code über die Twilio-Bindung an den Benutzer. Die Datei *function.json* wird wie folgt definiert:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Dies ist der Code, der den vierstelligen Abfragecode generiert und die SMS-Nachricht sendet:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Die Funktion **E4_SendSmsChallenge** wird nur einmal aufgerufen, selbst dann, wenn der Prozess abstürzt oder wiedergegeben wird. Dies ist gut, da auf diese Weise sichergestellt wird, dass der Benutzer nicht mehrere SMS-Nachrichten erhält. Der Rückgabewert `challengeCode` wird automatisch gespeichert, sodass die Orchestratorfunktion immer weiß, wie der korrekte Code lautet.

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
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Die Orchestratorfunktion empfängt die angegebene Telefonnummer und sendet sofort eine SMS-Nachricht mit einem zufällig generierten vierstelligen Überprüfungscode an diese Nummer, &mdash;z.B. *2168*. Anschließend wartet die Funktion 90 Sekunden auf eine Antwort.

Für eine Beantwortung mit dem Code können Sie [`RaiseEventAsync` (.NET) oder `raiseEvent` (JavaScript)](durable-functions-instance-management.md) in einer anderen Funktion verwenden oder den HTTP POST-Webhook **sendEventUrl** aufrufen, auf den oben in der 202-Antwort verwiesen wird. Dabei wird `{eventName}` durch den Namen des Ereignisses, `SmsChallengeResponse`, ersetzt:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Wenn Sie dies vor Ablauf des Timers senden, ist die Orchestrierung abgeschlossen, und das Feld `output` wird auf `true` festgelegt, was auf eine erfolgreiche Überprüfung hinweist.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
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

## <a name="visual-studio-sample-code"></a>Visual Studio-Beispielcode

So sieht die Orchestrierung als einzelne C#-Datei in einem Visual Studio-Projekt aus:

> [!NOTE]
> Sie müssen das `Microsoft.Azure.WebJobs.Extensions.Twilio`-NuGet-Paket installieren, um den unten stehenden Beispielcode auszuführen.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel werden einige der erweiterten Funktionen von Durable Functions veranschaulicht, insbesondere `WaitForExternalEvent` und `CreateTimer`. Sie haben gesehen, wie diese mit `Task.WaitAny` kombiniert werden können, um ein zuverlässiges Zeitlimitsystem zu implementieren. Dies ist bei der Interaktion mit realen Personen häufig hilfreich. Weitere Informationen zur Verwendung von Durable Functions finden Sie in einer Reihe von Artikeln, die detaillierte Erläuterungen zu bestimmten Themen bieten.

> [!div class="nextstepaction"]
> [Zum ersten Artikel der Reihe wechseln](durable-functions-bindings.md)
