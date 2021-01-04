---
title: Verwenden von Azure CDN mit CORS | Microsoft Docs
description: Erfahren Sie, wie Sie das Azure Content Delivery Network (CDN), mit Cross-Origin Resource Sharing (Ressourcenfreigabe zwischen verschiedenen Ursprüngen; CORS) verwendet wird.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018646"
---
# <a name="using-azure-cdn-with-cors"></a>Verwendung von Azure CDN mit CORS
## <a name="what-is-cors"></a>Was ist CORS?
CORS (Cross Origin Resource Sharing; Ressourcenfreigabe zwischen verschiedenen Ursprüngen) ist eine HTTP-Funktion, die einer Webanwendung, die in einer Domäne ausgeführt wird, den Zugriff auf Ressourcen in einer anderen Domäne ermöglicht. Alle modernen Webbrowser eine Sicherheitseinschränkung, die als [Same Origin Policy](https://www.w3.org/Security/wiki/Same_Origin_Policy)bekannt ist, um die Möglichkeiten für Cross-Site Scripting-Angriffe zu verringern.  Dies hindert eine Website daran, APIs in einer anderen Domäne aufzurufen.  CORS bietet eine sichere Methode, um einem Ursprung (der Ursprungsdomäne) das Aufrufen von APIs in einem anderen Ursprung zu ermöglichen.

## <a name="how-it-works"></a>Funktionsweise
Es gibt zwei Arten von CORS-Anforderungen: *einfache Anforderungen* und *komplexe Anforderungen*.

### <a name="for-simple-requests"></a>Für einfache Anforderungen gilt Folgendes:

1. Der Browser sendet die CORS-Anforderung mit einem zusätzlichen HTTP-Anforderungsheader vom Typ **Ursprung**. Der Wert dieses Headers ist der Ursprung, der die übergeordneten Seite bereitgestellt hat. Dabei handelt es sich um eine Kombination aus *Protokoll*, *Domäne* und *Port*.  Wenn eine Seite aus „https\://www.contoso.com“ versucht, auf die Benutzerdaten im Ursprung „fabrikam.com“ zuzugreifen, wird der folgende Anforderungsheader an „fabrikam.com“ gesendet:

   `Origin: https://www.contoso.com`

2. Der Server reagiert kann wie folgt reagieren:

   * Mit einem **Access-Control-Allow-Origin**-Header in der Antwort, der die zulässige Ursprungswebsites angibt. Beispiel:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Mit einem HTTP-Fehlercode (etwa 403), falls der Server die ursprungsübergreifende Anforderung nach der Überprüfung des Origin-Headers nicht zulässt.

   * Mit einem **Access-Control-Allow-Origin**-Header mit einem Platzhalter, der alle Ursprünge zulässt:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Für komplexe Anforderungen gilt Folgendes:

Eine komplexe-Anforderung ist eine CORS-Anforderung, bei der der Browser vor dem Senden der eigentlichen CORS-Anforderung eine *Preflight-Anforderung* (also einen Vorabtest) senden muss. Die Preflight-Anforderung ist eine `OPTIONS`-Anforderung an die gleiche URL und fordert vom Server eine Berechtigung zum Ausführen der ursprünglichen CORS-Anforderung an.

> [!TIP]
> Weitere Informationen zu CORS-Abläufen und häufigen Problemen finden Sie im [CORS-Leitfaden für REST-APIs](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Platzhalter oder Szenarien mit nur einem Ursprung
CORS für Azure CDN funktioniert automatisch ohne zusätzliche Konfiguration, wenn der **Access-Control-Allow-Origin** -Header auf Platzhalter (*) oder einen einzelnen Ursprung festgelegt ist.  Das CDN speichert die erste Antwort zwischen und nachfolgende Anforderungen verwenden den gleichen Header.

Wenn Anforderungen schon an CDN gesendet wurden, bevor CORS auf den Ursprung konfiguriert wurde, müssen Sie den Inhalt im Inhalt Ihres Endgeräts entfernen, um den Inhalt mit dem **Access-Control-Allow-Origin**-Header erneut zu laden.

## <a name="multiple-origin-scenarios"></a>Szenarien mit mehreren Ursprüngen
Wenn Sie eine bestimmte Liste von Ursprüngen für CORS zulassen müssen, wird es etwas komplizierter. Das Problem tritt auf, wenn CDN den **Access-Control-Allow-Origin** -Header für den ersten CORS-Ursprung zwischenspeichert.  Bei einer Folgeanforderung durch einen anderen CORS-Ursprung stellt das CDN den zwischengespeicherten **Access-Control-Allow-Origin**-Header bereit, dieser stimmt jedoch nicht überein.  Es gibt mehrere Möglichkeiten, dies zu korrigieren:

### <a name="azure-cdn-standard-profiles"></a>Azure CDN Standard-Profile
Unter Azure CDN Standard von Microsoft können Sie eine Regel in der [Standardregel-Engine](cdn-standard-rules-engine-reference.md) erstellen, um den **Origin**-Header (Ursprung) in der Anforderung zu überprüfen. Wenn es sich hierbei um einen gültigen Ursprung handelt, legt Ihre Regel den **Access-Control-Allow-Origin**-Header auf den gewünschten Wert fest. In diesem Fall wird der **Access-Control-Allow-Origin**-Header des Ursprungsservers der Datei ignoriert und die CDN-Regel-Engine verwaltet vollständig die zulässigen CORS-Ursprünge.

![Regelbeispiel mit der Standard-Regel-Engine](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Sie können Ihrer Regel zusätzliche Aktionen hinzufügen, um zusätzliche Antwortheader zu ändern, z. B. **Access-Control-Allow-Methods**.
> 

Unter [Azure CDN-Standard von Akamai](cdn-query-string.md) ist der einzige Mechanismus, um mehrere Ursprünge ohne Verwendung des Platzhalterursprungs zuzulassen, die Verwendung der **Zwischenspeicherung von Abfragezeichenfolgen**. Aktivieren Sie Abfragezeichenfolgen für den CDN-Endpunkt, und verwenden Sie anschließend eine eindeutige Abfragezeichenfolge für Anforderungen von den einzelnen zulässigen Domänen. Dadurch speichert das CDN ein separates Objekt für jede eindeutige Abfragezeichenfolge zwischen. Dieser Ansatz ist jedoch nicht ideal, da so mehrere Kopien derselben Datei im CDN zwischengespeichert werden.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium von Verizon
Unter Verwendung der Verizon Premium-Regel-Engine müssen Sie [eine Regel erstellen](./cdn-verizon-premium-rules-engine.md), um den **Ursprungsheader** in der Anforderung zu überprüfen.  Wenn es sich hierbei um einen gültigen Ursprung handelt, legt Ihre Regel den **Access-Control-Allow-Origin** -Header mit dem in der Anforderung bereitgestellten Ursprung fest.  Wenn der im Header **Origin** angegebene Ursprung nicht zulässig ist, sollte Ihre Regel den **Access-Control-Allow-Origin**-Header auslassen, der den Browser dazu bringt, die Anforderung abzulehnen. 

Es gibt zwei Möglichkeiten, dies mit der Premium-Regel-Engine zu tun. In beiden Fällen wird der **Access-Control-Allow-Origin**-Header des Ursprungsservers der Datei ignoriert und die CDN-Regel-Engine verwaltet vollständig die zulässigen CORS-Ursprünge.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Ein regulärer Ausdruck mit allen gültigen Ursprüngen
In diesem Fall erstellen Sie einen regulären Ausdruck, der alle Ursprünge enthält, die Sie zulassen möchten: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **Azure CDN Premium von Verizon** verwendet [Perl Compatible Regular Expressions](https://pcre.org/) als Engine für reguläre Ausdrücke.  Sie können ein Tool wie [Regular Expressions 101](https://regex101.com/) verwenden, um Ihre regulären Ausdrücke zu überprüfen.  Beachten Sie, dass das Zeichen „/“ in regulären Ausdrücken zulässig ist und nicht mit Escapezeichen versehen werden muss. Dieses Zeichen in Escapezeichen zu setzen ist jedoch die beste Methode und wird von einigen RegEx-Validierern erwartet.
> 
> 

Wenn der reguläre Ausdruck übereinstimmt, ersetzt Ihre Regel den **Access-Control-Allow-Origin**-Header (sofern vorhanden) des Ursprungs mit dem des Ursprungs, der die Anforderung gesendet hat.  Sie können zusätzliche CORS-Header hinzufügen, wie z.B. **Access-Control-Allow-Methods**.

![Beispiel für Regeln mit regulären Ausdruck](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Anforderungsheader-Regel für jeden Ursprung
Statt reguläre Ausdrücke können Sie stattdessen eine separate Regel für jeden Ursprung erstellen, den sie zulassen möchten. Verwenden Sie dazu die [Übereinstimmungsbedingung](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) des **Request Header**-Platzhalters. Wie bei der Methode des regulären Ausdrucks legt die Regel-Engine allein die CORS-Header fest. 

![Beispiel für Regeln ohne regulären Ausdruck](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> Im obigen Beispiel weist die Verwendung des Platzhalterzeichens „*“ die Regel-Engine an, sowohl HTTP als auch HTTPS abzugleichen.
> 
>