---
title: Richtlinieneinstellungen für Web Application Firewall mit Azure Front Door
description: Erfahren Sie mehr über Web Application Firewall (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459367"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Richtlinieneinstellungen für Web Application Firewall mit Azure Front Door

Mit einer Richtlinie für Web Application Firewall (WAF) können Sie den Zugriff auf Ihre Webanwendungen mit einer Reihe von benutzerdefinierten und verwalteten Regeln steuern. Der WAF-Richtlinienname muss eindeutig sein. Sie erhalten eine Validierungsfehlermeldung, wenn Sie versuchen, einen vorhandenen Namen zu verwenden. Es gibt verschiedene Richtlinienebenen-Einstellungen, die für alle Regeln gelten, die für die jeweilige Richtlinie angegeben werden, wie in diesem Artikel beschrieben.

## <a name="waf-state"></a>WAF-Status

Eine WAF-Richtlinie für Front Door kann einen der beiden folgenden Zustände aufweisen:
- **Aktiviert:** Wenn eine Richtlinie aktiviert ist, überprüft WAF aktiv eingehende Anforderungen und führt den Regeldefinitionen entsprechende Aktionen durch.
- **Deaktiviert:** Wenn eine Richtlinie deaktiviert ist, wird die WAF-Überprüfung angehalten. Eingehende Anforderungen umgehen WAF und werden auf dem Front Door-Routing basierend an Back-Ends gesendet.

## <a name="waf-mode"></a>WAF-Modus

Für die Ausführung der WAF-Richtlinie können die beiden folgenden Modi konfiguriert werden:

- **Erkennungsmodus**: Bei der Ausführung im Erkennungsmodus sind die WAF-Aktionen auf die Überwachung sowie die Protokollierung der Anforderung und der entsprechenden WAF-Regel in WAF-Protokollen beschränkt. Aktivieren Sie die Protokollierung von Diagnosedaten für Front Door (wechseln Sie dazu im Azure-Portal zum Abschnitt **Diagnose**).

- **Schutzmodus**: Bei Konfiguration zur Ausführung im Schutzmodus führt WAF die angegebene Aktion aus, wenn eine Anforderung einer Regel entspricht. Jede Anforderung mit einer Regelübereinstimmung wird außerdem in den WAF-Protokollen protokolliert.

## <a name="waf-response-for-blocked-requests"></a>WAF-Antwort für blockierte Anforderungen

Wenn WAF aufgrund einer übereinstimmenden Regel eine Anforderung blockiert, wird standardmäßig ein 403-Statuscode mit der Meldung **Die Anforderung wird blockiert** zurückgegeben. Für die Protokollierung wird auch eine Verweiszeichenfolge zurückgegeben.

Sie können einen benutzerdefinierten Antwortstatuscode und eine Antwortmeldung für den Fall definieren, dass eine Anforderung durch WAF blockiert wird. Die folgenden benutzerdefinierten Statuscodes werden unterstützt:

- 200    OK
- 403    Verboten
- 405    Methode unzulässig
- 406    Nicht annehmbar
- 429    Zu viele Anforderungen

Benutzerdefinierter Antwortstatuscode und Antwortmeldung sind eine Richtlinieneinstellung. Sobald sie konfiguriert ist, erhalten alle blockierten Anforderungen den gleichen benutzerdefinierten Antwortstatus und die gleiche Antwortmeldung.

## <a name="uri-for-redirect-action"></a>URI für Umleitungsaktion

Sie müssen einen URI definieren, an den Anforderungen umgeleitet werden können, wenn die **UMLEITUNGSAKTION** für eine der Regeln ausgewählt ist, die in einer WAF-Richtlinie enthalten sind. Dieser Umleitungs-URI muss eine gültige HTTP(S)-Website sein, und sobald er konfiguriert ist, werden alle Anforderungen, die mit Regeln mit einer „UMLEITUNGSAKTION“ übereinstimmen, an die angegebene Website umgeleitet.


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [benutzerdefinierte Antworten](waf-front-door-configure-custom-response-code.md) für WAF definieren.
