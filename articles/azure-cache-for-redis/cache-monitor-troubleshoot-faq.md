---
title: Häufig gestellte Fragen zur Überwachung und Problembehandlung von Azure Cache for Redis
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen, die Ihnen bei der Überwachung und Problembehandlung von Azure Cache for Redis helfen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537523"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Häufig gestellte Fragen zur Überwachung und Problembehandlung von Azure Cache for Redis
Dieser Artikel bietet Antworten auf häufig gestellte Fragen rundum die Überwachung und Problembehandlung von Azure Cache for Redis.

## <a name="common-questions-and-answers"></a>Häufig gestellte Fragen und deren Antworten
In diesem Abschnitt werden die folgenden häufig gestellten Fragen behandelt:

* [Wie überwache ich die Integrität und Leistung meines Caches?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Warum kommt es zu Timeouts?](#why-am-i-seeing-timeouts)
* [Warum wurde mein Client vom Cache getrennt?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Wie überwache ich die Integrität und Leistung meines Caches?
Microsoft Azure Cache for Redis-Instanzen können im [Azure-Portal](https://portal.azure.com)überwacht werden. Sie können Metriken anzeigen, Metrikdiagramme an das Startmenü anheften, Daten- und Zeitbereiche für Überwachungsdiagramme anpassen, Metriken aus Diagrammen hinzufügen und entfernen sowie Warnungen festlegen, die ausgelöst werden, wenn bestimmte Bedingungen erfüllt sind. Weitere Informationen finden Sie unter [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md).

Das **Ressourcenmenü** von Azure Cache for Redis enthält auch mehrere Tools zur Überwachung und Problembehandlung für Ihre Caches.

* **Diagnose und Problembehandlung** bietet Informationen zu häufigen Problemen sowie Strategien zu deren Behebung.
* **Ressourcenintegrität** dienen zum Überwachen Ihrer Ressource und informieren Sie darüber, ob sie wie erwartet ausgeführt wird. Weitere Informationen zum Azure Resource Health-Dienst finden Sie in der [Übersicht über Azure Resource Health](../service-health/resource-health-overview.md).
* **Neue Supportanfrage** bietet Optionen, um eine Supportanfrage für Ihren Cache zu öffnen.

Diese Tools ermöglichen es Ihnen, die Integrität Ihrer Azure Cache for Redis-Instanzen zu überwachen, und unterstützen Sie beim Verwalten Ihrer Cachinganwendungen. Weitere Informationen finden Sie im Abschnitt „Einstellungen zu Support und Problembehandlung“ des Artikels [Gewusst wie: Konfigurieren von Azure Cache for Redis](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Warum kommt es zu Timeouts?
Timeouts treten auf dem Client auf, der mit Redis kommuniziert. Wenn ein Befehl an den Redis-Server gesendet wird, wird dieser in die Warteschlange eingereiht, bis der Redis-Server den Befehl auswählt und ausführt. Auf dem Client kann es bei diesem Vorgang zu einem Timeout kommen. In diesem Fall wird auf der aufrufenden Seite eine Ausnahme ausgelöst. Weitere Informationen zur Behandlung von Timeoutproblemen finden Sie unter [Behandeln von clientseitigen Problemen](cache-troubleshoot-client.md) und [StackExchange.Redis-Timeoutausnahmen](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Warum wurde mein Client vom Cache getrennt?
Nachfolgend werden einige Gründe dafür aufgeführt, warum ein Cache getrennt wird.

* Gründe auf Clientseite
  * Die Clientanwendung wurde neu bereitgestellt.
  * Die Clientanwendung hat eine Skalierung ausgeführt.
    * Bei Cloud Services oder Web-Apps kann dies aufgrund einer automatischen Skalierung der Fall sein.
  * Die Netzwerkschicht auf Clientseite wurde geändert.
  * Vorübergehende Fehler auf dem Client oder in den Netzwerkknoten zwischen Client und Server.
  * Die Bandbreitenschwellenwerte wurden erreicht.
  * Die Ausführung CPU-bezogener Vorgänge hat zu viel Zeit in Anspruch genommen.
* Gründe auf Serverseite
  * Im Standard-Cacheangebot initiiert der Azure Cache for Redis-Dienst ein Failover vom primären Knoten auf den Replikatknoten.
  * Azure hat ein Patching der Instanz durchgeführt, auf dem der Cache bereitgestellt wurde.
    * Dies kann bei Redis-Serverupdates oder im Rahmen der allgemeinen VM-Wartung der Fall sein.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Überwachung und Problembehandlung Ihrer Azure Cache for Redis-Instanzen finden Sie unter [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md) und in den verschiedenen Handbüchern zur Problembehandlung.

Erfahren Sie mehr über weitere [häufig gestellte Fragen zu Azure Cache for Redis](cache-faq.md).