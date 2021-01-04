---
title: Erhöhen der Resilienz von Authentifizierung und Autorisierung in Daemon-Anwendungen, die Sie entwickeln
titleSuffix: Microsoft identity platform
description: Leitfaden zur Erhöhung der Resilienz von Authentifizierung und Autorisierung in einer Daemon-Anwendung mithilfe von Microsoft Identity Platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 74bfc9eeeb8375fca2c88a3fd3c31f17e130fc99
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919062"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Erhöhen der Resilienz von Authentifizierung und Autorisierung in Daemon-Anwendungen, die Sie entwickeln

Dieser Artikel enthält Anleitungen dazu, wie Entwickler Microsoft Identity Platform und Azure Active Directory nutzen können, um die Resilienz von Daemon-Anwendungen zu erhöhen. Dies umfasst Hintergrundprozesse, Dienste, Server-zu-Server-Apps und Anwendungen ohne Benutzer.

![Eine Daemon-Anwendung, die einen Aufruf von Microsoft Identity ausführt](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen

Entwickler, die Daemon-Apps in Microsoft Azure entwickeln, können [verwaltete Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) verwenden. Durch verwaltete Identitäten müssen Entwickler keine Geheimnisse und Anmeldeinformationen verwalten. Das Feature verbessert die Resilienz, indem Fehler im Zusammenhang mit dem Zertifikatablauf, Rotationsfehlern oder Vertrauensstellungen vermieden werden. Außerdem sind mehrere integrierte Features enthalten, die speziell zur Erhöhung der Resilienz dienen.

Verwaltete Identitäten verwenden langlebige Zugriffstoken und Informationen von Microsoft Identity, um in einem großen Zeitfenster proaktiv neue Token abzurufen, bevor das vorhandene Token abläuft. Ihre App kann weiterhin ausgeführt werden, während versucht wird, ein neues Token abzurufen.

Verwaltete Identitäten verwenden auch regionale Endpunkte, um die Leistung und Resilienz gegenüber Ausfällen außerhalb der Region zu verbessern. Durch die Verwendung eines regionalen Endpunkts wird der gesamte Datenverkehr in einem geografischen Bereich gehalten. Wenn sich Ihre Azure-Ressource z. B. in WestUS2 befindet, sollte der gesamte Datenverkehr (einschließlich des von Microsoft Identity generierten Datenverkehrs) in WestUS2 verbleiben. Dadurch können mögliche Fehlerursachen durch Konsolidieren der Abhängigkeiten Ihres Diensts beseitigt werden.

## <a name="use-the-microsoft-authentication-library"></a>Verwenden der Microsoft-Authentifizierungsbibliothek

Entwickler von Daemon-Apps, die keine verwalteten Identitäten verwenden, können [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) verwenden, durch die die Implementierung von Authentifizierung und Autorisierung vereinfacht wird und automatisch bewährte Methoden für Resilienz verwendet werden. MSAL vereinfacht die Bereitstellung der erforderlichen Clientanmeldeinformationen. Beispielsweise muss Ihre Anwendung beim Verwenden von zertifikatbasierten Anmeldeinformationen keine Erstellung und Signierung von JSON-Webtokenassertionen implementieren.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Verwenden von Microsoft.Identity.Web für .NET-Entwickler

Entwickler, die Daemon-Apps mit ASP.NET Core erstellen, können die [Microsoft.Identity.Web](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web)-Bibliothek verwenden. Diese Bibliothek setzt auf MSAL auf, um die Implementierung von Autorisierung für ASP.NET Core-Apps noch einfacher zu gestalten. Sie umfasst mehrere [verteilte Tokencachestrategien](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) für verteilte Apps, die in mehreren Regionen ausgeführt werden können.

## <a name="cache-and-store-tokens"></a>Zwischenspeichern und Speichern von Token

Wenn Sie MSAL nicht zum Implementieren von Authentifizierung und Autorisierung verwenden, können Sie einige bewährte Methoden zum Zwischenspeichern und Speichern von Token implementieren. MSAL implementiert und befolgt diese bewährten Methoden automatisch.

Eine Anwendung ruft Token von einem Identitätsanbieter ab, um die Anwendung für das Aufrufen geschützter APIs zu autorisieren. Wenn Ihre App Token empfängt, enthält die Antwort, die die Token enthält, auch eine Eigenschaft „expires\_in“, die die Anwendung informiert, wie lange das Token zwischengespeichert und wiederverwendet werden soll. Es ist wichtig, dass Anwendungen die Eigenschaft „expires\_in“ verwenden, um die Lebensdauer des Tokens zu bestimmen. Die Anwendung darf niemals versuchen, ein API-Zugriffstoken zu decodieren. Die Verwendung des zwischengespeicherten Tokens verhindert unnötigen Datenverkehr zwischen Ihrer App und Microsoft Identity. Der Benutzer kann für die Länge der Lebensdauer des Tokens bei Ihrer Anwendung angemeldet bleiben.

## <a name="properly-handle-service-responses"></a>Ordnungsgemäßes Verarbeiten von Dienstantworten

Schließlich sollten die Anwendungen zwar alle Fehlerantworten verarbeiten, aber es gibt einige Antworten, die sich auf die Resilienz auswirken können. Wenn Ihre Anwendung einen HTTP-Antwortcode 429 (zu viele Anforderungen) empfängt, drosselt Microsoft Identity Ihre Anforderungen. Wenn Ihre App weiterhin zu viele Anforderungen vornimmt, wird sie weiterhin gedrosselt, damit Ihre App keine Token empfängt. Die Anwendung sollte nicht erneut versuchen, ein Token abzurufen, bis die Zeit (in Sekunden) im Antwortfeld „Retry-After“ verstrichen ist. Das Empfangen einer Antwort vom Typ 429 ist häufig ein Hinweis darauf, dass die Anwendung Token nicht ordnungsgemäß zwischenspeichert und wiederverwendet. Entwickler sollten überprüfen, wie Token in der Anwendung zwischengespeichert und wiederverwendet werden.

Wenn eine Anwendung einen HTTP-Antwortcode 5xx empfängt, darf die App nicht in eine schnelle Wiederholungsschleife eintreten. Wenn vorhanden, sollte die Anwendung dieselbe „Retry-After“-Verarbeitung beachten wie bei einer 429-Antwort. Wenn von der Antwort kein „Retry-After“-Header bereitgestellt wird, wird die Implementierung eines exponentiellen Backoff-Wiederholungsversuchs mit dem ersten Wiederholungsversuch mindestens 5 Sekunden nach der Antwort empfohlen.

Wenn für eine Anforderung ein Timeout auftritt, sollten Anwendungen nicht sofort Wiederholungsversuche ausführen. Implementieren Sie einen exponentiellen Backoff-Wiederholungsversuch mit dem ersten Wiederholungsversuch mindestens 5 Sekunden nach der Antwort.

## <a name="next-steps"></a>Nächste Schritte

- [Erzielen von Resilienz in Anwendungen, die Benutzer anmelden](resilience-client-app.md)
- [Erzielen von Resilienz in Ihrer Identitäts- und Zugriffsverwaltungsinfrastruktur](resilience-in-infrastructure.md)
- [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)
