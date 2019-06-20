---
title: Azure Front Door Service – Überwachung des Abgleichs mit Routingregeln | Microsoft-Dokumentation
description: Dieser Artikel bietet grundlegende Informationen dazu, wie Azure Front Door Service entscheidet, welche Routingregel für eine eingehende Anforderung verwendet werden soll.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: eec99bde0ea73a99a9dc1345f938b821a95a7c05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736281"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Abgleich von Anforderungen mit Routingregeln durch Front Door

Wenn eine Anforderung nach dem Herstellen einer Verbindung und dem Ausführen eines SSL-Handshakes in einer Front Door-Umgebung eingeht, besteht eine der ersten Aktionen von Front Door darin, anhand aller Konfigurationen zu bestimmen, mit welcher Routingregel die Anforderung abgeglichen werden soll. Danach wird die definierte Aktion ausgeführt. Der folgende Artikel erläutert, wie Front Door bestimmt, welche Routenkonfiguration beim Verarbeiten einer HTTP-Anforderung verwendet werden soll.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktur einer Front Door-Routenkonfiguration
Die Konfiguration einer Front Door-Routingregel besteht aus zwei Hauptteilen: einer „linken Seite“ und einer „rechten Seite“. Die eingehende Anforderung wird mit der linken Seite der Route abgeglichen, während die rechte Seite definiert, wie die Anforderung verarbeitet wird.

### <a name="incoming-match-left-hand-side"></a>Abgleich der eingehenden Anforderung (linke Seite)
Die folgenden Eigenschaften bestimmen, ob die eingehende Anforderung mit der Routingregel (also der linken Seite) übereinstimmt:

* **HTTP-Protokolle** (HTTP/HTTPS)
* **Hosts** (z.B. www\.foo.com, \*.bar.com)
* **Pfade** (z.B.: /\*, /users/\*, /file.gif)

Diese Eigenschaften werden intern erweitert, sodass jede Kombination aus Protokoll/Host/Pfad ein potenzieller Treffer ist.

### <a name="route-data-right-hand-side"></a>Weiterleiten der Daten (rechte Seite)
Die Entscheidung darüber, wie die Anforderung verarbeitet werden soll, richtet sich danach, ob für die angegebene Route das Caching aktiviert ist oder nicht. Wenn keine zwischengespeicherte Antwort für die Anforderung vorhanden ist, wird die Anforderung an das geeignete Back-End im konfigurierten Back-End-Pool weitergeleitet.

## <a name="route-matching"></a>Routenabgleich
In diesem Abschnitt wird erläutert, wie der Abgleich mit einer bestimmten Front Door-Routingregel funktioniert. Das grundlegende Konzept besteht darin, immer den **ersten genauesten Treffer** zu verwenden, wobei nur die „linke Seite“ in Betracht gezogen wird.  Der Abgleich erfolgt zunächst basierend auf dem HTTP-Protokoll, dann basierend auf dem Front-End-Host, dann basierend auf dem Pfad.

### <a name="frontend-host-matching"></a>Abgleich mit Front-End-Hosts
Beim Abgleich mit Front-End-Hosts wird folgende Logik verwendet:

1. Es wird nach allen Routingregeln mit einem exakten Treffer für den Host gesucht.
2. Wenn kein exakter Treffer für einen Front-End-Host gefunden wird, wird die Anforderung abgelehnt und der Fehler „400 – Ungültige Anforderung“ gesendet.

Um diesen Prozess genauer zu erklären, betrachten wir eine Beispielkonfiguration von Front Door-Routen (nur linke Seite):

| Routingregel | Front-End-Hosts | `Path` |
|-------|--------------------|-------|
| Eine Datei | foo.contoso.com | /\* |
| b | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Wenn die folgenden eingehenden Anforderungen an Front Door gesendet würden, würden sie mit den folgenden Routingregeln von oben abgeglichen:

| Front-End-Host für eingehende Anforderungen | Abgeglichene Routingregel(n) |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Fehler 400: Ungültige Anforderung |
| foo.adventure-works.com | C |
| contoso.com | Fehler 400: Ungültige Anforderung |
| www\.adventure-works.com | Fehler 400: Ungültige Anforderung |
| www\.northwindtraders.com | Fehler 400: Ungültige Anforderung |

### <a name="path-matching"></a>Pfadabgleich
Nach dem Ermitteln des Front-End-Hosts und dem Filtern möglicher Routingregeln, um sicherzustellen, dass nur die Routen mit diesem Front-End-Host verwendet werden, filtert Front Door die Routingregeln basierend auf dem Anforderungspfad. Hierbei wird eine ähnliche Logik wie bei den Front-End-Hosts verwendet:

1. Es wird nach allen Routingregeln mit einem exakten Treffer für den Pfad gesucht.
2. Wenn kein exakter Pfad vorhanden ist, wird nach Routingregeln mit einem übereinstimmenden Platzhalterpfad gesucht.
3. Wenn keine Routingregeln mit einem übereinstimmenden Pfad gefunden werden, wird die Anforderung abgelehnt und eine HTTP-Antwort „400: Ungültige Anforderung“ gesendet.

>[!NOTE]
> Alle Pfade ohne Platzhalter gelten als exakte Treffer für den Pfad. Selbst wenn ein Pfad mit einem Schrägstrich endet, wird er dennoch als exakter Treffer betrachtet.

Sehen wir uns weitere Beispiele an, um dies genauer zu erläutern:

| Routingregel | Front-End-Host    | `Path`     |
|-------|---------|----------|
| Eine Datei     | www\.contoso.com | /        |
| b     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

In dieser Konfiguration würde sich folgende Treffertabelle ergeben:

| Eingehende Anforderung    | Übereinstimmende Route |
|---------------------|---------------|
| www\.contoso.com/            | Eine Datei             |
| www\.contoso.com/a           | b             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | b             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | b             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | b             |

>[!WARNING]
> </br> Wenn keine Routingregeln für einen exakt übereinstimmenden Front-End-Host mit einem Routingpfad zur Abdeckung aller Fälle (`/*`) vorhanden sind, gibt es für keine Routingregel einen Treffer.
>
> Beispielkonfiguration:
>
> | Weiterleiten | Host             | `Path`    |
> |-------|------------------|---------|
> | Eine Datei     | profile.contoso.com | /api/\* |
>
> Treffertabelle:
>
> | Eingehende Anforderung       | Übereinstimmende Route |
> |------------------------|---------------|
> | profile.domain.com/other | None (Keine): Fehler 400: Ungültige Anforderung |

### <a name="routing-decision"></a>Routingentscheidung
Nachdem der Abgleich mit einer einzelnen Front Door-Routingregel erfolgt ist, muss entschieden werden, wie die Anforderung verarbeitet werden soll. Wenn Front Door für die übereinstimmende Routingregel über eine zwischengespeicherte Antwort verfügt, wird diese an den Client gesendet. Andernfalls wird als Nächstes ausgewertet, ob für die übereinstimmende Routingregel ein [URL-Rewrite (benutzerdefinierter Weiterleitungspfad)](front-door-url-rewrite.md) konfiguriert wurde. Wenn kein benutzerdefinierter Weiterleitungspfad definiert wurde, wird die Anforderung unverändert an das entsprechende Back-End im konfigurierten Back-End-Pool weitergeleitet. Andernfalls wird der Anforderungspfad gemäß dem definierten [benutzerdefinierten Weiterleitungspfad](front-door-url-rewrite.md) aktualisiert, dann erfolgt die Weiterleitung an das Back-End.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Azure Front Door Service-Konfiguration](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
