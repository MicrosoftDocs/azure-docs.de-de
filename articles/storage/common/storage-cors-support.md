---
title: Unterstützung von Cross-Origin Resource Sharing (CORS) | Microsoft Docs
description: Erfahren Sie, wie die CORS-Unterstützung für die Microsoft Azure Storage-Dienste aktiviert werden.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bb296db0d97382deac984369704777de5d5cb362
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147688"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Unterstützung von Cross-Origin Resource Sharing (CORS) für die Azure Storage-Dienste
Ab Version 2013-08-15 unterstützen die Azure Storage-Dienste Cross-Origin Resource Sharing (CORS) für die Blob-, Tabellen-, Warteschlangen- und Dateidienste. CORS ist eine HTTP-Funktion, die einer Webanwendung in einer Domäne den Zugriff auf Ressourcen in einer anderen Domäne ermöglicht. Webbrowser implementieren eine Sicherheitseinschränkung, die als [Same-Origin-Richtlinie](https://www.w3.org/Security/wiki/Same_Origin_Policy) (Richtlinie desselben Ursprungs) bezeichnet wird und verhindert, dass eine Webseite APIs in einer anderen Domäne aufruft. CORS bietet eine sichere Methode, einer Domäne (der Ursprungsdomäne) das Aufrufen von APIs in einer anderen Domäne zu ermöglichen. Informationen zu CORS finden Sie in der [CORS-Spezifikation](https://www.w3.org/TR/cors/).

Sie können CORS-Regeln einzeln für jeden der Speicherdienste festlegen, indem Sie [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) und [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx) aufrufen. Nachdem Sie die CORS-Regeln für den Dienst festgelegt haben, wird durch Auswertung einer korrekt autorisierten Anforderung, die von einer anderen Domäne an den Dienst gesendet wurde, ermittelt, ob die Anforderung gemäß den von Ihnen festgelegten Regeln zulässig ist.

> [!NOTE]
> Beachten Sie, dass CORS kein Authentifizierungsmechanismus ist. Jede Anforderung, die bei Aktivierung von CORS für eine Speicherressource ausgeführt wird, muss entweder über eine geeignete Authentifizierungssignatur verfügen oder für eine öffentliche Ressource ausgeführt werden.
> 
> 

## <a name="understanding-cors-requests"></a>Grundlagen zu CORS-Anforderungen
Eine CORS-Anforderung von einer Ursprungsdomäne kann aus zwei separaten Anforderungen bestehen:

* Einer Preflight-Anforderung, durch die die vom Dienst auferlegten CORS-Einschränkungen abgefragt werden. Die Preflightanforderung ist erforderlich, sofern die Anforderungsmethode keine [einfache Methode](https://www.w3.org/TR/cors/) (d.h. GET, HEAD oder POST) ist.
* Die für die gewünschte Ressource ausgeführte tatsächliche Anforderung.

### <a name="preflight-request"></a>Preflight-Anforderung
Durch die Preflight-Anforderung werden die CORS-Einschränkungen abgerufen, die vom Kontobesitzer für den Speicherdienst festgelegt wurden. Der Webbrowser (oder ein anderer Benutzer-Agent) übermittelt eine OPTIONS-Anforderung, die die Anforderungsheader, die Methode und die Ursprungsdomäne enthält. Der Speicherdienst wertet den gewünschten Vorgang anhand eines vorkonfigurierten CORS-Regelsatzes aus, in dem festgelegt ist, welche Ursprungsdomänen, Anforderungsmethoden und Anforderungsheader für eine tatsächliche, an eine Speicherressource gerichtete Anforderung angegeben werden können.

Wenn CORS für den Dienst aktiviert ist, und es eine CORS-Regel gibt, die mit der Preflight-Anforderung übereinstimmt, antwortet der Dienst mit dem Statuscode 200 (OK) und schließt die erforderlichen Access-Control-Header in die Antwort ein.

Wenn CORS nicht für den Dienst aktiviert ist bzw. keine CORS-Regel mit der Preflight-Anforderung übereinstimmt, antwortet der Dienst mit dem Statuscode 403 (Verboten).

Wenn die erforderlichen CORS-Header (der Origin-Header und der Access-Control-Request-Method-Header) nicht in der OPTIONS-Anforderung enthalten sind, antwortet der Dienst mit dem Statuscode 400 (Ungültige Anforderung).

Beachten Sie, dass eine Preflight-Anforderung für den Dienst (BLOB, Warteschlange oder Tabelle) und nicht für die angeforderte Ressource ausgewertet wird. Der Kontobesitzer muss CORS in den Kontodiensteigenschaften aktiviert haben, damit die Anforderung erfolgreich ist.

### <a name="actual-request"></a>Tatsächliche Anforderung
Nachdem die Preflight-Anforderung akzeptiert und die Antwort zurückgegeben wurde, sendet der Browser die tatsächliche Anforderung an die Speicherressource. Wenn die Preflight-Anforderung zurückgewiesen wird, wird die tatsächliche Anforderung vom Browser sofort abgelehnt.

Die tatsächliche Anforderung wird als normale Anforderung für den Speicherdienst behandelt. Wenn der Origin-Header vorhanden ist, deutet dies darauf hin, dass es sich bei der Anforderung um eine CORS-Anforderung handelt und dass der Dienst diese mit den CORS-Regeln abgleicht. Wenn eine Übereinstimmung gefunden wird, werden die Access-Control-Header der Antwort hinzugefügt und an den Client zurückgesendet. Wenn keine Übereinstimmung gefunden wird, werden die CORS-Access-Control-Header nicht zurückgegeben.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Aktivieren von CORS für die Azure Storage-Dienste
CORS-Regeln werden auf Dienstebene festgelegt, sodass Sie CORS für jeden Dienst (BLOB, Warteschlange oder Tabelle) separat aktivieren oder deaktivieren müssen. CORS ist standardmäßig für jeden Dienst deaktiviert. Um CORS zu aktivieren, müssen Sie die entsprechenden Diensteigenschaften mithilfe von Version 2013-08-15 oder höher festlegen und den Diensteigenschaften CORS-Regeln hinzufügen. Ausführliche Informationen dazu, wie Sie CORS für einen Dienst aktivieren oder deaktivieren und wie Sie CORS-Regeln festlegen können, finden Sie unter [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) und [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx).

Im Folgenden sehen Sie ein Beispiel für eine einzelne CORS-Regel, die in einem Vorgang zum Festlegen von Diensteigenschaften angegeben wird:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Jedes in der CORS-Regel enthaltene Element wird unten beschrieben:

* **AllowedOrigins:** Die Ursprungsdomänen, die über CORS eine Anforderung an den Speicherdienst senden dürfen. Die Ursprungsdomäne ist die Domäne, von der die Anforderung stammt. Beachten Sie, dass die Groß-/Kleinschreibung der Ursprungsdomäne genau mit der der Ursprungsdomäne übereinstimmen muss, die der Benutzer-Agent an den Dienst sendet. Sie können auch das Platzhalterzeichen "*" verwenden, um allen Ursprungsdomänen die Ausführung von CORS-Anforderungen zu erlauben. Im Beispiel oben können die Domänen „http:\//www.contoso.com“ und „http:\//www.fabrikam.com“ über CORS Anforderungen an den Dienst senden.
* **AllowedMethods:** Die Methoden (HTTP-Anforderungsverben), die die Ursprungsdomäne für eine CORS-Anforderung verwenden kann. Im vorangehenden Beispiel sind PUT- und GET-Anforderungen zulässig.
* **AllowedHeaders:** Die Anforderungsheader, die die Ursprungsdomäne für die CORS-Anforderung angeben kann. Im vorangehenden Beispiel sind alle Metadatenheader zulässig, die mit x-ms-meta-data, x-ms-meta-target und x-ms-meta-abc beginnen. Das Platzhalterzeichen "*" zeigt an, dass beliebige Header, die mit dem angegebenen Präfix beginnen, zulässig sind.
* **ExposedHeaders:** Die Antwortheader, die in der Antwort an die CORS-Anforderung gesendet und vom Browser gegenüber dem Anforderungsaussteller verfügbar gemacht werden können. Im vorangehenden Beispiel wird der Browser angewiesen, beliebige Header, die mit x-ms-meta beginnen, verfügbar zu machen.
* **MaxAgeInSeconds:** Die maximale Zeit, über die die OPTIONS-Preflight-Anforderung vom Browser zwischengespeichert werden soll.

Die Angabe von Headern mit Präfix wird von den Azure Storage-Diensten sowohl für das **AllowedHeaders**-Element als auch für das **ExposedHeaders**-Element unterstützt. Um eine Headerkategorie zuzulassen, können Sie für diese Kategorie ein gemeinsames Präfix angeben. Wenn Sie z.B. *x-ms-meta** als Header mit Präfix angeben, wird eine Regel erstellt, durch die alle Header abgeglichen werden, die mit „x-ms-meta“ beginnen.

Für CORS-Regeln gelten die folgenden Einschränkungen:

* Sie können bis zu fünf CORS-Regeln pro Speicherdienst (BLOB, Tabelle und Warteschlange) angeben.
* Die maximale Größe aller Einstellungen für CORS-Regeln in der Anforderung sollte 2 KB nicht überschreiten. XML-Tags werden hierbei nicht eingerechnet.
* Die Länge eines zulässigen Headers oder Ursprungs bzw. eines verfügbar gemachten Headers sollte maximal 256 Zeichen betragen.
* Zulässige und verfügbar gemachte Header können in folgender Form vorkommen:
  * Literale Header, bei denen der genaue Headername angegeben ist, z.B. **x-ms-meta-processed**. In der Anforderung können maximal 64 literale Header angegeben werden.
  * Header mit Präfix, bei denen ein Präfix des Headers angegeben ist, z.B. **x-ms-meta-data**\*. Wird auf diese Weise ein Präfix angegeben, sind alle Header zulässig bzw. werden alle Header verfügbar gemacht, die mit dem angegebenen Präfix beginnen. In der Anforderung können maximal zwei Header mit Präfix angegeben werden.
* Die Methoden (oder HTTP-Verben), die im **AllowedMethods** -Element angegeben sind, müssen den Methoden entsprechen, die von Azure Storage-Dienst-APIs unterstützt werden. Unterstützte Methoden sind DELETE, GET, HEAD, MERGE, POST, OPTIONS und PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Grundlagen zur Auswertungslogik für CORS-Regeln
Wenn ein Speicherdienst eine Preflight-Anforderung oder eine tatsächliche Anforderung empfängt, wertet er diese Anforderung im entsprechenden Vorgang zum Festlegen von Diensteigenschaften anhand der CORS-Regeln aus, die Sie für den Dienst festgelegt haben. CORS-Regeln werden in der Reihenfolge ausgewertet, in der sie im Anforderungstext des Vorgangs zum Festlegen von Diensteigenschaften definiert wurden.

CORS-Regeln werden wie folgt ausgewertet:

1. Zunächst wird die Ursprungsdomäne der Anforderung anhand der Domänen überprüft, die für das **AllowedOrigins**-Element aufgelistet sind. Wenn die Ursprungsdomäne in der Liste enthalten ist bzw. alle Domänen mittels Platzhalterzeichen "*" zulässig sind, dann wird die Regelauswertung fortgesetzt. Wenn die Ursprungsdomäne nicht enthalten ist, tritt ein Anforderungsfehler auf.
2. Als Nächstes wird die Methode (oder das HTTP-Verb) der Anforderung anhand der Methoden überprüft, die im **AllowedMethods** -Element aufgelistet sind. Wenn die Methode in der Liste enthalten ist, wird die Regelauswertung fortgesetzt; andernfalls ist die Anforderung nicht erfolgreich.
3. Wenn die Anforderung mit einer Regel in deren Ursprungsdomäne und der zugehörigen Methode übereinstimmt, wird diese Regel zur Verarbeitung der Anforderung ausgewählt, und es werden keine weiteren Regeln ausgewertet. Damit die Anforderung erfolgreich ausgeführt werden kann, müssen jedoch erst die für die Anforderung angegebenen Header anhand der Header überprüft werden, die im **AllowedHeaders** -Element aufgelistet sind. Wenn die gesendeten Header nicht den zulässigen Headern entsprechen, tritt ein Anforderungsfehler auf.

Da die Regeln in der Reihenfolge verarbeitet werden, in der sie im Anforderungstext aufgeführt sind, wird empfohlen, die restriktivsten Regeln für die obersten Ursprungsdomänen in der Liste festzulegen, damit diese zuerst ausgewertet werden. Geben Sie weniger restriktive Regeln – z. B. eine, die alle Ursprungsdomänen zulässt – für Ursprungsdomänen am Ende der Liste an.

### <a name="example--cors-rules-evaluation"></a>Beispiel – Auswertung von CORS-Regeln
Im folgenden Beispiel wird ein partieller Anforderungstext für einen Vorgang angezeigt, durch den CORS-Regeln für die Speicherdienste festgelegt werden. Ausführliche Informationen zum Erstellen der Anforderung finden Sie unter [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) und [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx).

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Betrachten Sie als Nächstes die folgenden CORS-Anforderungen:

| Anforderung |  |  | response |  |
| --- | --- | --- | --- | --- |
| **Methode** |**Ursprung** |**Anforderungsheader** |**Regelübereinstimmung** |**Ergebnis** |
| **PUT** |http:\//www.contoso.com |x-ms-blob-content-type |Erste Regel |Erfolgreich |
| **GET** |http:\//www.contoso.com |x-ms-blob-content-type |Zweite Regel |Erfolgreich |
| **GET** |http:\//www.contoso.com |x-ms-client-request-id |Zweite Regel |Fehler |

Die erste Anforderung stimmt mit der ersten Regel überein – die Ursprungsdomäne fällt unter die zulässigen Ursprungsdomänen, die Methode unter die zulässigen Methoden und der Header unter die zulässigen Header – sie wird somit erfolgreich ausgeführt.

Die zweite Anforderung stimmt nicht mit der ersten Regel überein, weil die Methode nicht unter die zulässigen Methoden fällt. Sie stimmt jedoch mit der zweiten Regel überein und wird somit erfolgreich ausgeführt.

Die dritte Anforderung stimmt mit der zweiten Regel in ihrer Ursprungsdomäne und der Methode überein, sodass keine weiteren Regeln ausgewertet werden. Allerdings ist der *x-ms-client-request-id*-Header gemäß der zweiten Regel nicht zulässig, sodass bei der Anforderung ein Fehler auftritt. Dies gilt, obwohl die Anforderung gemäß der Semantik der dritten Regel erfolgreich gewesen wäre.

> [!NOTE]
> Obwohl in diesem Beispiel eine weniger restriktive Regel vor einer restriktiveren Regel angewendet wird, empfiehlt es sich im Allgemeinen, die restriktivsten Regeln an den Anfang zu stellen.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Grundlagen zum Festlegen des Vary-Headers
Der *Vary*-Header ist ein HTTP/1.1-Standardheader, der aus einer Gruppe von Anforderungsheaderfeldern besteht, über die dem Browser bzw. Benutzer-Agent mitgeteilt wird, welche Kriterien der Server zur Verarbeitung der Anforderung ausgewählt hat. Der *Vary*-Header wird hauptsächlich von Proxys, Browsern und CDNs verwendet, um zu bestimmen, wie die Antwort zwischengespeichert werden soll. Ausführliche Informationen finden Sie in der Spezifikation zum [Vary-Header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Wenn der Browser oder ein anderer Benutzer-Agent die Antwort von einer CORS-Anforderung zwischenspeichert, wird die Ursprungsdomäne als zulässige Ursprungsdomäne zwischengespeichert. Wenn eine zweite Domäne die Anforderung für eine Speicherressource ausgibt, während der Cache aktiv ist, ruft der Benutzer-Agent die zwischengespeicherte Ursprungsdomäne ab. Die zweite Domäne stimmt nicht mit der zwischengespeicherten Domäne überein, sodass die andernfalls erfolgreiche Anforderung nicht ausgeführt werden kann. In bestimmten Fällen wird der Vary-Header von Azure Storage auf **Origin** festgelegt, um den Benutzer-Agent anzuweisen, die nachfolgende CORS-Anforderung an den Dienst zu senden, wenn sich die anfordernde Domäne von der zwischengespeicherten Ursprungsdomäne unterscheidet.

Azure Storage legt den *Vary* -Header für tatsächliche GET/HEAD-Anforderungen in den folgenden Fällen auf **Origin** fest:

* Die Ursprungsdomäne der Anforderung stimmt genau mit der zulässigen Ursprungsdomäne überein, die von einer CORS-Regel definiert wird. Eine genaue Übereinstimmung wird nur erzielt, wenn die CORS-Regel kein Platzhalterzeichen " * " enthält.
* Es gibt keine Regel, die mit der Ursprungsdomäne der Anforderung übereinstimmt, CORS ist jedoch für den Speicherdienst aktiviert.

Falls eine GET-/HEAD-Anforderung mit einer CORS-Regel übereinstimmt, die alle Ursprungsdomänen zulässt, gibt die Antwort an, dass alle Ursprungsdomänen zulässig sind. Solange der Cache aktiv ist, lässt der Benutzer-Agent-Cache nachfolgende Anforderungen von beliebigen Ursprungsdomänen zu.

Bei Anforderungen, die andere Methoden als GET/HEAD verwenden, wird der Vary-Header von den Speicherdiensten nicht festgelegt, da die Antworten auf diese Methoden nicht von den Benutzer-Agents zwischengespeichert werden.

Der folgenden Tabelle können Sie entnehmen, wie Azure Storage in den oben beschriebenen Fällen auf GET-/HEAD-Anforderungen antwortet:

| Anforderung | Kontoeinstellung und Ergebnis der Regelauswertung |  |  | response |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Origin-Header für Anforderung vorhanden** |**CORS-Regel(n) für den Dienst angegeben** |**Abgleichsregel vorhanden, die alle Ursprungsdomänen zulässt (*)** |**Abgleichsregel für genaue Übereinstimmung mit Ursprungsdomäne vorhanden** |**Antwort enthält Vary-Header, der auf "Origin" festgelegt ist** |**Antwort enthält „Access-Control-Allowed-Origin“: "*"** |**Antwort enthält Access-Control-Exposed-Header** |
| Nein |Nein |Nein |Nein |Nein |Nein |Nein |
| Nein |Ja |Nein |Nein |Ja |Nein |Nein |
| Nein |Ja |Ja |Nein |Nein |Ja |Ja |
| Ja |Nein |Nein |Nein |Nein |Nein |Nein |
| Ja |Ja |Nein |Ja |Ja |Nein |Ja |
| Ja |Ja |Nein |Nein |Ja |Nein |Nein |
| Ja |Ja |Ja |Nein |Nein |Ja |Ja |

## <a name="billing-for-cors-requests"></a>Abrechnung von CORS-Anforderungen
Erfolgreiche Preflightanforderungen werden in Rechnung gestellt, wenn Sie CORS für einen der Speicherdienste in Ihrem Konto aktiviert haben (durch Aufrufen von [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) oder [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx)). Um die Gebühren zu minimieren, sollten Sie das **MaxAgeInSeconds** -Element in den CORS-Regeln auf einen hohen Wert festlegen, sodass die Anforderung vom Benutzer-Agent zwischengespeichert wird.

Nicht erfolgreiche Preflight-Anforderungen werden nicht berechnet.

## <a name="next-steps"></a>Nächste Schritte
[Festlegen von Blob-Diensteigenschaften](https://msdn.microsoft.com/library/hh452235.aspx)

[Festlegen von Warteschlangendiensteigenschaften](https://msdn.microsoft.com/library/hh452232.aspx)

[Festlegen von Tabellendiensteigenschaften](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C-Spezifikation für CORS (Cross-Origin Resource Sharing)](https://www.w3.org/TR/cors/)

