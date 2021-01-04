---
title: Azure Monitor-Metriken für Application Gateway
description: Erfahren Sie, wie Sie mit Metriken die Leistung von Application Gateway überwachen.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: surmb
ms.openlocfilehash: be629d9f8441ad40fe15f005f4aeb0ec5565a7ec
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437064"
---
# <a name="metrics-for-application-gateway"></a>Metriken für Application Gateway

Application Gateway veröffentlicht Datenpunkte, sogenannte Metriken, für die Leistung Ihrer Application Gateway- und Back-End-Instanzen bei [Azure Monitor](../azure-monitor/overview.md). Diese Metriken sind numerische Werte in einer geordneten Menge von Zeitreihendaten, die einen Aspekt Ihrer Application Gateway-Instanz zu einem bestimmten Zeitpunkt beschreiben. Wenn Anforderungen durch Application Gateway geleitet werden, werden die Metriken in 60-Sekunden-Intervallen gemessen und gesendet. Wenn keine Anforderungen durch Application Gateway oder keine Daten für eine Metrik geleitet werden, wird die Metrik nicht gemeldet. Weitere Informationen finden Sie unter [Azure Monitor-Metriken](../azure-monitor/platform/data-platform-metrics.md).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Von der Application Gateway V2-SKU unterstützte Metriken

### <a name="timing-metrics"></a>Zeitmetriken

Application Gateway bietet mehrere integrierte Zeitsteuerungsmetriken, die sich auf die Anforderung und die Antwort beziehen und jeweils in Millisekunden gemessen werden. 

![Diagramm der Timingmetriken für das Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Wenn Application Gateway mehr als einen Listener enthält, filtern Sie immer nach der Dimension *Listener*, und vergleichen Sie dabei verschiedene Latenzmetriken, um aussagekräftige Rückschlüsse zu erhalten.

- **Verbindungszeit für das Back-End**

  Zeitaufwand für das Herstellen einer Verbindung mit der Back-End-Anwendung. 

  Dies umfasst auch die Netzwerklatenz sowie die Zeit, die der TCP-Stapel des Back-End-Servers zum Aufbauen neuer Verbindungen benötigt. Im Fall von TLS umfasst dies auch die mit dem Handshake verbrachte Zeit. 

- **Antwortzeit für erstes Byte des Back-Ends**

  Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antwortheaders. 

  Dies entspricht ungefähr der Summe aus der *Back-End-Verbindungszeit*, der von der Anforderung zum Erreichen des Back-Ends von Application Gateway benötigten Zeit, der Zeit, die die Back-End-Anwendung für die Antwort benötigt (Zeit für die Generierung der Inhalte durch den Server, ggf. Abrufen von Datenbankabfragen), und der Zeit, bis das erste Byte der Antwort von Application Gateway vom Back-End erreicht.

- **Antwortzeit für letztes Byte des Back-Ends**

  Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antworttexts. 

  Dies entspricht der Summe aus der *Back-End-Antwortzeit für das erste Byte* und der Datenübertragungszeit. (Diese Zahl variiert in Abhängigkeit von der Größe der angeforderten Objekte und der Latenz des Servernetzwerks teilweise erheblich.)

- **Application Gateway-Gesamtzeit**

  Durchschnittliche Zeit, bis eine Anforderung empfangen, verarbeitet und die zugehörige Antwort gesendet wurde. 

  Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem das letzte Byte der Antwort an den Client gesendet wurde. Dies schließt die Verarbeitungszeit durch Application Gateway, die *Antwortzeit bis zum letzten Byte vom Back-End*, die Zeit, die Application Gateway zum Senden der Antwort benötigt, sowie die *Client-RTT* ein.

- **Client-RTT**

  Durchschnittliche Roundtripzeit zwischen Clients und Application Gateway.



Mithilfe dieser Metriken können Sie ermitteln, ob die beobachtete Verlangsamung auf das Clientnetzwerk, die Leistung von Application Gateway, das Back-End-Netzwerk und die TCP-Stapelauslastung des Back-End-Servers, die Leistung der Back-End-Anwendung oder eine große Datei zurückzuführen ist.

Wenn z. B. eine Spitze im Trend der *Back-End-Antwortzeit für das erste Byte* auftritt, aber der Trend der *Back-End-Verbindungszeit* stabil ist, können Sie daraus ableiten, dass es Latenzen zwischen Application Gateway und dem Back-End gibt und dass die Zeit zum Herstellen der Verbindung gleichbleibend ist. Damit wissen Sie, dass die Spitze durch einen Anstieg der Antwortzeit der Back-End-Anwendung verursacht wird. Wenn andererseits die Spitze bei der *Back-End-Antwortzeit für das erste Byte* mit einer entsprechenden Spitze bei der *Back-End-Verbindungszeit* zusammenhängt, können Sie daraus schließen, dass entweder das Netzwerk zwischen Application Gateway und dem Back-End-Server oder der TCP-Stapel des Back-End-Servers ausgelastet ist. 

Wenn Sie eine Spitze bei der *Back-End-Antwortzeit für das erste Byte* feststellen, aber die *Back-End-Antwortzeit für das erste Byte* gleichbleibend ist, kann die Spitze darauf zurückgeführt werden, dass eine größere Datei angefordert wird.

Entsprechend kann eine Spitze bei der *Application Gateway-Gesamtzeit* bei gleichbleibender *Back-End-Antwortzeit für das letzte Byte* ein Zeichen entweder für einen Leistungsengpass bei Application Gateway oder einen Engpass im Netzwerk zwischen Client und Application Gateway sein. Wenn außerdem die *Client-RTT* eine entsprechende Spitze aufweist, deutet dies darauf hin, dass das Problem auf das Netzwerk zwischen Client und Application Gateway zurückzuführen ist.

### <a name="application-gateway-metrics"></a>Application Gateway-Metriken

Für Application Gateway werden folgende Metriken unterstützt:

- **Empfangene Bytes**

   Anzahl der von Application Gateway von den Clients empfangenen Bytes

- **Gesendete Bytes**

   Anzahl der von Application Gateway an die Clients gesendeten Bytes

- **Client-TLS-Protokoll**

   Anzahl von TLS- und Nicht-TLS-Anforderungen, die von dem Client initiiert wurden, der eine Verbindung mit Application Gateway hergestellt hat. Um die Verteilung des TLS-Protokolls anzuzeigen, filtern Sie nach dem Dimensions-TLS-Protokoll.

- **Aktuelle Kapazitätseinheiten**

   Anzahl von Kapazitätseinheiten, die für den Lastenausgleich des Datenverkehrs genutzt werden. Die Kapazitätseinheit setzt sich aus drei Größen zusammen: Compute-Einheit, permanente Verbindungen und Durchsatz. Jede Kapazitätseinheit setzt sich maximal zusammen aus: 1 Compute-Einheit oder 2500 permanente Verbindungen oder 2,22 MBit/s Durchsatz.

- **Aktuelle Compute-Einheiten**

   Menge der verbrauchten Prozessorkapazität. Faktoren, die sich auf die Compute-Einheit auswirken, sind TLS-Verbindungen/Sekunde, URL-Rewrite-Berechnungen und Verarbeitung von WAF-Regeln. 

- **Aktuelle Verbindungen**

   Die Gesamtanzahl der von Clients gleichzeitig aktiven Verbindungen mit dem Application Gateway
   
- **Geschätzte abgerechnete Kapazitätseinheiten**

  Bei der V2-SKU wird ein verbrauchsbasiertes Preismodell verwendet. Kapazitätseinheiten geben die verbrauchsbasierten Kosten an, die zusätzlich zu den Fixkosten berechnet werden. *Geschätzte abgerechnete Kapazitätseinheiten* geben die Anzahl von Kapazitätseinheiten an, für die die Abrechnung geschätzt wird. Hierzu wird der größere Wert zwischen *Aktuelle Kapazitätseinheiten* (Kapazitätseinheiten, die für den Lastenausgleich des Datenverkehrs erforderlich sind) und *Feste abrechenbare Kapazitätseinheiten* (Mindestanzahl von Kapazitätseinheiten, die bereitgestellt bleiben) berechnet.

- **Anforderungsfehler**

  Anzahl von Anforderungen, die von Application Gateway mit 5xx-Serverfehlercodes verarbeitet wurden. Dies umfasst die 5xx-Codes, die von Application Gateway generiert werden, sowie die 5xx-Codes, die vom Back-End generiert werden. Die Anzahl der Anforderungen kann weiter gefiltert werden, um die Anzahl für die einzelnen/spezifischen Kombinationen aus Back-End-Pools und HTTP-Einstellungen anzuzeigen.
   
- **Feste abrechenbare Kapazitätseinheiten**

  Die Mindestanzahl von Kapazitätseinheiten, die gemäß der Einstellung *Mindesteinheiten für Skalierung* (in der Application Gateway-Konfiguration) bereitgestellt bleiben. Eine Instanz entspricht hierbei zehn Kapazitätseinheiten.
   
 - **Neue Verbindungen pro Sekunde**

   Die durchschnittliche Anzahl neuer TCP-Verbindungen pro Sekunde, die von Clients mit Application Gateway und von Application Gateway mit den Back-End-Mitgliedern hergestellt werden.


- **Antwortstatus**

   Von Application Gateway zurückgegebener HTTP-Antwortstatus. Die Antwortstatuscode-Verteilung kann weiter kategorisiert werden, um Antworten in 2xx-, 3xx-, 4xx- und 5xx-Kategorien anzuzeigen.

- **Durchsatz**

   Anzahl von Bytes pro Sekunde, die das Application Gateway bereitgestellt hat

- **Anforderungen insgesamt**

   Anzahl von erfolgreichen Anforderungen über Application Gateway. Die Anzahl der Anforderungen kann weiter gefiltert werden, um die Anzahl für die einzelnen/spezifischen Kombinationen aus Back-End-Pools und HTTP-Einstellungen anzuzeigen.

### <a name="backend-metrics"></a>Back-End-Metriken

Für Application Gateway werden folgende Metriken unterstützt:

- **Back-End-Antwortstatus**

  Anzahl der von den Back-Ends zurückgegebenen HTTP-Antwortstatuscodes. Dies schließt nicht die von Application Gateway generierten Antwortcodes ein. Die Antwortstatuscode-Verteilung kann weiter kategorisiert werden, um Antworten in 2xx-, 3xx-, 4xx- und 5xx-Kategorien anzuzeigen.

- **Anzahl von fehlerfreien Hosts**

  Die Anzahl der Back-Ends, die im Integritätstest als fehlerfrei ermittelt wurden. Sie können auf Back-End-Pool-Basis filtern, um die Anzahl fehlerfreier Hosts in einem bestimmten Back-End-Pool anzuzeigen.

- **Anzahl von fehlerhaften Hosts**

  Die Anzahl der Back-Ends, die im Integritätstest als fehlerhaft ermittelt wurden. Sie können auf Back-End-Pool-Basis filtern, um die Anzahl fehlerhafter Hosts in einem bestimmten Back-End-Pool anzuzeigen.
  
- **Anforderungen pro Minute pro fehlerfreiem Host**

  Die durchschnittliche Anzahl von Anforderungen, die von jedem fehlerfreien Mitglied in einem Back-End-Pool pro Minute empfangen werden. Der Back-End-Pool muss über die Dimension *BackendPool HttpSettings* angegeben werden.  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Von der Application Gateway V1-SKU unterstützte Metriken

### <a name="application-gateway-metrics"></a>Application Gateway-Metriken

Für Application Gateway werden folgende Metriken unterstützt:

- **CPU-Auslastung**

  Zeigt die Auslastung der CPUs an, die dem Application Gateway zugeordnet sind.  Unter normalen Bedingungen sollte die CPU-Auslastung regelmäßig nicht 90 % überschreiten, da dies zu Wartezeiten bei den Websites führen kann, die hinter dem Application Gateway gehostet werden, sowie die Clienterfahrung merklich stören kann. Sie können die CPU-Auslastung indirekt steuern oder verbessern, indem Sie die Konfiguration des Application Gateway durch Erhöhen der Anzahl der Instanzen ändern oder durch Verschieben zu einer größeren SKU-Größe – oder durch beides.

- **Aktuelle Verbindungen**

  Anzahl von aktuellen Verbindungen mit Application Gateway

- **Anforderungsfehler**

  Die Anzahl von Anforderungen, für die aufgrund von Verbindungsproblemen ein Fehler aufgetreten ist. In dieser Anzahl sind auch Anforderungen enthalten, für die aufgrund einer Überschreitung der HTTP-Einstellung „Anforderungstimeout“ ein Fehler aufgetreten ist, sowie Anforderungen, die aufgrund von Verbindungsproblemen zwischen dem Anwendungsgateway und dem Back-End nicht erfolgreich waren. Diese Anzahl umfasst keine Fehler, die aufgetreten sind, weil kein fehlerfreies Back-End verfügbar war. Back-End-Antworten vom Typ „4xx“ und „5xx“ werden im Rahmen dieser Metrik ebenfalls nicht berücksichtigt.

- **Antwortstatus**

  Von Application Gateway zurückgegebener HTTP-Antwortstatus. Die Antwortstatuscode-Verteilung kann weiter kategorisiert werden, um Antworten in 2xx-, 3xx-, 4xx- und 5xx-Kategorien anzuzeigen.

- **Durchsatz**

  Anzahl von Bytes pro Sekunde, die das Application Gateway bereitgestellt hat

- **Anforderungen insgesamt**

  Anzahl von erfolgreichen Anforderungen über Application Gateway. Die Anzahl der Anforderungen kann weiter gefiltert werden, um die Anzahl für die einzelnen/spezifischen Kombinationen aus Back-End-Pools und HTTP-Einstellungen anzuzeigen.

- **Anzahl der von Web Application Firewall blockierten Anforderungen**
- **Verteilung der von Web Application Firewall blockierten Anforderungen**
- **Gesamtregelverteilung in Web Application Firewall**

### <a name="backend-metrics"></a>Back-End-Metriken

Für Application Gateway werden folgende Metriken unterstützt:

- **Anzahl von fehlerfreien Hosts**

  Die Anzahl der Back-Ends, die im Integritätstest als fehlerfrei ermittelt wurden. Sie können auf Back-End-Pool-Basis filtern, um die Anzahl fehlerfreier Hosts in einem bestimmten Back-End-Pool anzuzeigen.

- **Anzahl von fehlerhaften Hosts**

  Die Anzahl der Back-Ends, die im Integritätstest als fehlerhaft ermittelt wurden. Sie können auf Back-End-Pool-Basis filtern, um die Anzahl fehlerhafter Hosts in einem bestimmten Back-End-Pool anzuzeigen.

## <a name="metrics-visualization"></a>Metrikvisualisierung

Navigieren Sie zu einem Anwendungsgateway, und wählen Sie unter **Überwachung** die Option **Metriken** aus. Um die verfügbaren Werte anzuzeigen, wählen Sie die Dropdownliste **METRIK** aus.

In der folgenden Abbildung sehen Sie ein Beispiel mit drei Metriken für die letzten 30 Minuten:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Metrikansicht" lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Eine aktuelle Liste mit Metriken finden Sie unter [Unterstützte Metriken von Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Warnungsregeln für Metriken

Sie können Warnungsregeln basierend auf Metriken für eine Ressource starten. Eine Warnung kann beispielsweise einen Webhook aufrufen oder eine E-Mail an einen Administrator senden, wenn der Durchsatz des Anwendungsgateways für einen angegebenen Zeitraum oberhalb oder unterhalb eines Schwellenwerts bzw. genau auf einem Schwellenwert liegt.

Im folgenden Beispiel wird schrittweise die Erstellung einer Warnungsregel beschrieben, die eine E-Mail an einen Administrator sendet, nachdem ein Durchsatzschwellenwert verletzt wurde:

1. Wählen Sie **Metrikwarnung hinzufügen** aus, um die Seite **Regel hinzufügen** zu öffnen. Sie können diese Seite auch über die Seite mit den Metriken erreichen.

   ![Schaltfläche „Metrikwarnung hinzufügen“][6]

2. Füllen Sie auf der Seite **Regel hinzufügen** die Abschnitte für den Namen, die Bedingung und die Benachrichtigung aus, und wählen Sie **OK** aus.

   * Wählen Sie unter **Bedingung** einen der vier Werte aus: **Größer als**, **Größer oder gleich**, **Kleiner als** oder **Kleiner oder gleich**.

   * Wählen Sie unter **Zeitraum** einen Zeitraum zwischen fünf Minuten und sechs Stunden aus.

   * Wenn Sie **E-Mail-Besitzer, Mitwirkende und Leser** wählen, kann die E-Mail-Adresse dynamisch basierend auf den Benutzern festgelegt werden, die auf diese Ressource zugreifen können. Andernfalls können Sie im Feld **Weitere Administrator-E-Mail(s)** eine durch Kommas getrennte Liste mit Benutzern angeben.

   ![Seite „Regel hinzufügen“][7]

Wenn der Schwellenwert überschritten wird, trifft eine E-Mail ein, die in etwa wie in der folgenden Abbildung aussieht:

![E-Mail zu überschrittenem Schwellenwert][8]

Nach dem Erstellen einer Metrikwarnung wird eine Liste mit Warnungen angezeigt. Dies ist eine Übersicht über alle Warnungsregeln.

![Liste mit Warnungen und Regeln][9]

Weitere Informationen zu Warnungsbenachrichtigungen finden Sie unter [Empfangen von Warnungsbenachrichtigungen](../azure-monitor/platform/alerts-overview.md).

Weitere Informationen zu Webhooks und deren Verwendung mit Warnungen finden Sie unter [Konfigurieren eines Webhooks für eine Azure-Metrikwarnung](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Nächste Schritte

* Visualisieren von Indikator- und Ereignisprotokollen mit [Azure Monitor-Protokollen](../azure-monitor/insights/azure-networking-analytics.md)
* Blogbeitrag [Visualize your Azure activity log with Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) (Visualisieren von Azure-Aktivitätsprotokollen mit Power BI)
* Blogbeitrag [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Anzeigen und Analysieren von Azure-Aktivitätsprotokollen in Power BI und mehr)

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png