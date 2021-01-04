---
title: Unterstützung von hohem Datenverkehrsaufkommen für Application Gateway
description: Dieser Artikel enthält Anleitungen zum Konfigurieren von Azure Application Gateway zur Unterstützung von Szenarios mit hohem Netzwerkdatenverkehrsaufkommen.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: a5f7569fc46d4678ca0c12299e33caa3c78df849
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182919"
---
# <a name="application-gateway-high-traffic-support"></a>Unterstützung von hohem Datenverkehr für Application Gateway

>[!NOTE]
> In diesem Artikel werden einige empfohlene Richtlinien beschrieben, die Ihnen bei der Einrichtung Ihres Application Gateway für den Umgang mit zusätzlichem starkem Datenverkehr helfen, der auftreten könnte. Die Schwellenwerte für Warnungen sind nur Vorschläge und allgemein. Benutzer können Warnungsschwellenwerte basierend auf ihrer Workload und den Auslastungserwartungen ermitteln.

Sie können Application Gateway mit der Web Application Firewall (WAF) für eine skalierbare und sichere Methode zum Verwalten von Datenverkehr an Ihre Webanwendungen verwenden.

Es ist wichtig, dass Sie Ihr Application Gateway entsprechend dem Datenverkehr und mit etwas Puffer skalieren, damit Sie auf Datenverkehrsspitzen vorbereitet sind und die Auswirkungen minimieren, die sie auf Ihre QoS haben könnten. Mithilfe der folgenden Vorschläge können Sie Application Gateway mit WAF einrichten, um zusätzlichen Datenverkehr zu verarbeiten.

Eine umfassende Liste der von Application Gateway angebotenen Metriken finden Sie in der Dokumentation zu [Metriken](./application-gateway-metrics.md). Wie Sie Warnungen für Metriken festlegen, erfahren Sie unter [Metrikvisualisierung](./application-gateway-metrics.md#metrics-visualization) im Azure-Portal und unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Skalierung für Application Gateway v1-SKU (Standard/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Festlegen der Instanzanzahl basierend auf der CPU-Auslastung
Wenn Sie ein v1-SKU-Gateway verwenden, können Sie Ihr Application Gateway für eine Skalierung auf bis zu 32 Instanzen einrichten. Überprüfen Sie die CPU-Auslastung Ihres Application Gateway im vergangenen Monat auf Spitzen über 80 %. Sie ist als Metrik verfügbar, die Sie überwachen können. Sie sollten die Anzahl der Instanzen entsprechend ihrer Spitzenauslastung und mit einem zusätzlichen Puffer von 10 % bis 20 % festlegen, um Datenverkehrsspitzen zu berücksichtigen.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="v1-Metrik zur CPU-Auslastung" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Verwenden Sie die v2-SKU anstelle von v1 wegen ihrer automatischen Skalierungsfunktionen und Leistungsvorteile.
Die v2-SKU bietet automatische Skalierung, um sicherzustellen, dass Ihr Application Gateway bei steigendem Datenverkehr zentral hochskaliert werden kann. Außerdem bietet sie weitere bedeutende Leistungsvorteile, z. B. eine 5-fach bessere TLS-Abladeleistung, schnellere Bereitstellungs- und Aktualisierungszeiten, Zonenredundanz und mehr im Vergleich zu v1. Weitere Informationen finden Sie in der [Dokumentation zu v2](./application-gateway-autoscaling-zone-redundant.md) und in der [Dokumentation zur Migration](./migrate-v1-v2.md) von v1 zu v2. Dort erfahren Sie, wie Sie Ihre vorhandenen v1-SKU-Gateways zu v2-SKU migrieren. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Automatische Skalierung für die Application Gateway v2-SKU (Standard_v2-/WAF_v2-SKU)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Festlegen der maximalen Anzahl von Instanzen auf den maximal möglichen Wert (125)
 
Wenn für die Application Gateway v2-SKU die maximale Anzahl von Instanzen auf den Maximalwert 125 festgelegt wird, kann das Application Gateway nach Bedarf aufskaliert werden. Dadurch können Sie die mögliche Zunahme des Datenverkehrs an Ihre Anwendungen bewältigen. Ihnen werden nur die von Ihnen verwendeten Kapazitätseinheiten (Capacity Units, CUs) in Rechnung gestellt. 

Überprüfen Sie die Subnetzgröße und die Anzahl der verfügbaren IP-Adressen in Ihrem Subnetz, und legen Sie die maximale Anzahl der Instanzen auf dieser Grundlage fest. Wenn Ihr Subnetz nicht über genügend Speicherplatz verfügt, müssen Sie das Gateway in demselben Subnetz neu erstellen, oder einem anderen, das über genügend Kapazität verfügt. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="Konfiguration der automatischen v2-Skalierung" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Festlegen Ihrer Mindestanzahl von Instanzen auf Grundlage Ihrer durchschnittlichen Compute-Einheit-Nutzung

Bei der Application Gateway v2-SKU dauert die automatische Skalierung sechs bis sieben Minuten für die Aufskalierung und Bereitstellung einer zusätzlichen Gruppe von Instanzen, die für den Datenverkehr bereit sind. Bis zu diesem Zeitpunkt könnten Ihre vorhandenen Gatewayinstanzen sehr stark belastet werden, wenn kurze Datenverkehrsspitzen auftreten, und dies könnte zu unerwarteter Latenz oder Datenverkehrsverlust führen. 

Sie sollten die Mindestanzahl der Instanzen auf ein optimales Niveau festlegen. Wenn Sie z. B. 50 Instanzen benötigen, um den Datenverkehr bei Spitzenlast zu verarbeiten, empfiehlt sich die Festlegung des Minimums auf 25 bis 30 anstatt <10, damit Application Gateway auch kurze Datenverkehrsspitzen verarbeiten kann und ausreichend Zeit zur Verfügung steht, dass die automatische Skalierung reagieren und wirksam werden kann.

Überprüfen Sie die Compute-Einheit-Metrik für den letzten Monat. Die Compute-Einheit-Metrik ist eine Darstellung der CPU-Auslastung Ihres Gateways, und basierend auf Ihrer Spitzenauslastung geteilt durch 10 können Sie die Mindestanzahl erforderlicher Instanzen festlegen. Beachten Sie, dass 1 Application Gateway-Instanz mindestens 10 Compute-Einheiten verarbeiten kann.

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="v2-Compute-Einheit-Metriken" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Manuelle Skalierung für die Application Gateway v2-SKU (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Festlegen der Instanzanzahl basierend auf der Compute-Einheit-Auslastung 

Anders als bei der automatischen Skalierung müssen Sie bei manueller Skalierung die Anzahl der Application Gateway-Instanzen basierend auf den Anforderungen des Datenverkehrs manuell festlegen. Sie sollten die Anzahl der Instanzen entsprechend ihrer Spitzenauslastung und mit einem zusätzlichen Puffer von 10 % bis 20 % festlegen, um Datenverkehrsspitzen zu berücksichtigen. Wenn Ihr Datenverkehr z. B. 50 Instanzen zu Spitzenzeiten erfordert, stellen Sie 55 bis 60 Instanzen bereit, um ggf. unerwartete Datenverkehrsspitzen verarbeiten zu können.

Überprüfen Sie die Compute-Einheit-Metrik für den letzten Monat. Die Compute-Einheit-Metrik ist eine Darstellung der CPU-Auslastung Ihres Gateways, und basierend auf Ihrer Spitzenauslastung geteilt durch 10 können Sie die Anzahl erforderlicher Instanzen festlegen, da 1 Application Gateway-Instanz mindestens 10 Compute-Einheiten verarbeiten kann.

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Um ggf. Benachrichtigungen zu Datenverkehrs- oder Auslastungsanomalien zu erhalten, können Sie Warnungen für bestimmte Metriken einrichten. Eine umfassende Liste der von Application Gateway angebotenen Metriken finden Sie in der [Dokumentation zu Metriken](./application-gateway-metrics.md). Wie Sie Warnungen für Metriken festlegen, erfahren Sie unter [Metrikvisualisierung](./application-gateway-metrics.md#metrics-visualization) im Azure-Portal und unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Warnungen für Application Gateway v1-SKU (Standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Warnung, wenn die durchschnittliche CPU-Auslastung 80 % überschreitet

Unter normalen Bedingungen sollte die CPU-Auslastung regelmäßig nicht 90 % überschreiten, da dies zu Wartezeiten bei den Websites führen kann, die hinter dem Application Gateway gehostet werden, sowie die Clienterfahrung merklich stören kann. Sie können die CPU-Auslastung indirekt steuern oder verbessern, indem Sie die Konfiguration des Application Gateway durch Erhöhen der Anzahl der Instanzen ändern oder durch Verschieben zu einer größeren SKU-Größe – oder durch beides. Legen Sie eine Warnung fest, wenn die CPU-Auslastungsmetrik einen Durchschnitt von 80 % überschreitet.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Warnung, wenn „Anzahl von fehlerhaften Hosts“ den Schwellenwert überschreitet

Diese Metrik zeigt die Anzahl von Back-End-Servern an, die das Anwendungsgateway nicht erfolgreich testen kann. Dadurch werden Probleme abgefangen, bei denen Application Gateway-Instanzen keine Verbindung mit dem Back-End herstellen können. Eine Warnung sollte ausgegeben werden, wenn diese Anzahl mehr als 20 % der Back-End-Kapazität überschreitet. Beispiel: Wenn derzeit 30 Back-End-Server im Back-End-Pool vorhanden sind, legen Sie eine Warnung für den Fall fest, dass die Anzahl fehlerhafter Hosts 6 überschreitet.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Warnung, wenn der Antwortstatus (4xx, 5xx) den Schwellenwert überschreitet 

Eine Warnung sollte ausgegeben werden, wenn der Application Gateway-Antwortstatus 4xx oder 5xx lautet. Aufgrund vorübergehender Probleme kann gelegentlich die Antwort 4xx oder 5xx angezeigt werden. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Warnung, wenn fehlerhafte Anforderungen Schwellenwert überschreiten 

Eine Warnung sollte ausgegeben werden, wenn die Metrik „Fehlerhafte Anforderungen“ den Schwellenwert überschreitet. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>Beispiel: Einrichten einer Warnung für mehr als 100 fehlerhafte Anforderungen in den letzten 5 Minuten

In diesem Beispiel wird gezeigt, wie Sie mit dem Azure-Portal eine Warnung einrichten für den Fall, dass die Anzahl fehlerhafter Anforderungen in den letzten 5 Minuten mehr als 100 beträgt.
1. Navigieren Sie zu Ihrem **Application Gateway**.
2. Wählen Sie im linken Bereich unter **Überwachung** die Option **Metriken** aus. 
3. Fügen Sie eine Metrik für **Fehlerhafte Anforderungen** hinzu.
4. Klicken Sie auf **Neue Warnungsregel**, und definieren Sie Ihre Bedingung und Aktionen.
5. Klicken Sie auf **Warnungsregel erstellen**, um die Warnung zu erstellen und zu aktivieren.

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="v2: Erstellen von Warnungen" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Warnungen für die Application Gateway v2-SKU (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Warnung, wenn die Auslastung der Compute-Einheiten 75 % der durchschnittlichen Nutzung überschreitet 

Die Compute-Einheit ist das Maß für die Computeauslastung Ihrer Application Gateway-Instanz. Überprüfen Sie Ihre durchschnittliche Nutzung der Compute-Einheiten im letzten Monat, und legen Sie eine Warnung fest für den Fall, dass 75 % davon überschritten werden. Wenn Ihre durchschnittliche Auslastung z. B. 10 Compute-Einheiten beträgt, legen Sie eine Warnung ab 7,5 Compute-Einheiten fest. Dadurch wird eine Warnung angezeigt, wenn die Nutzung zunimmt, und Sie erhalten Zeit, zu reagieren. Sie können den Mindestwert erhöhen, wenn Sie der Ansicht sind, dass der Datenverkehr länger so bleibt, um Sie darauf aufmerksam zu machen, dass der Datenverkehr eventuell steigen wird. Befolgen Sie die obigen Skalierungsvorschläge, um bei Bedarf aufzuskalieren.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Beispiel: Einrichten einer Warnung bei 75 % der durchschnittlichen CU-Nutzung

Dieses Beispiel zeigt, wie Sie im Azure-Portal eine Warnung einrichten können, wenn 75 % der durchschnittlichen CU-Nutzung erreicht wird. 
1. Navigieren Sie zu Ihrem **Application Gateway**.
2. Wählen Sie im linken Bereich unter **Überwachung** die Option **Metriken** aus. 
3. Fügen Sie eine Metrik für **Durchschnittliche aktuelle Compute-Einheiten** hinzu. 
4. Wenn Sie Ihre Mindestanzahl von Instanzen auf Ihre durchschnittliche CU-Nutzung festgelegt haben, fahren Sie fort, und legen Sie eine Warnung fest, wenn 75 % ihrer Mindestanzahl von Instanzen verwendet werden. Wenn Ihre durchschnittliche Auslastung z. B. 10 CUs beträgt, legen Sie eine Warnung ab 7,5 CUs fest. Dadurch wird eine Warnung angezeigt, wenn die Nutzung zunimmt, und Sie erhalten Zeit, zu reagieren. Sie können den Mindestwert erhöhen, wenn Sie der Ansicht sind, dass der Datenverkehr länger so bleibt, um Sie darauf aufmerksam zu machen, dass der Datenverkehr eventuell steigen wird. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="v2-Compute-Einheit-Warnungen" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> Sie können festlegen, dass die Warnung bei einem niedrigeren oder höheren CU-Auslastungsprozentsatz auftritt, je nachdem, wie empfindlich Sie auf potenzielle Datenverkehrsspitzen reagieren möchten.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Warnung, wenn die Kapazitätseinheitenauslastung 75 % der Spitzenauslastung überschreitet 

Kapazitätseinheiten stellen die Gesamtauslastung des Gateways in Bezug auf Durchsatz, Compute und Verbindungsanzahl dar. Überprüfen Sie Ihre maximale Kapazitätseinheitennutzung im letzten Monat, und legen Sie eine Warnung fest für den Fall, dass 75 % davon überschritten werden. Wenn Ihre maximale Auslastung z. B. 100 Kapazitätseinheiten beträgt, legen Sie eine Warnung ab 75 Kapazitätseinheiten fest. Befolgen Sie die obigen beiden Skalierungsvorschläge, um bei Bedarf aufzuskalieren.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Warnung, wenn „Anzahl von fehlerhaften Hosts“ den Schwellenwert überschreitet 

Diese Metrik zeigt die Anzahl von Back-End-Servern an, die das Anwendungsgateway nicht erfolgreich testen kann. Dadurch werden Probleme abgefangen, bei denen Application Gateway-Instanzen keine Verbindung mit dem Back-End herstellen können. Eine Warnung sollte ausgegeben werden, wenn diese Anzahl mehr als 20 % der Back-End-Kapazität überschreitet. Beispiel: Wenn derzeit 30 Back-End-Server im Back-End-Pool vorhanden sind, legen Sie eine Warnung für den Fall fest, dass die Anzahl fehlerhafter Hosts 6 überschreitet.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Warnung, wenn der Antwortstatus (4xx, 5xx) den Schwellenwert überschreitet 

Eine Warnung sollte ausgegeben werden, wenn der Application Gateway-Antwortstatus 4xx oder 5xx lautet. Aufgrund vorübergehender Probleme kann gelegentlich die Antwort 4xx oder 5xx angezeigt werden. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Warnung, wenn fehlerhafte Anforderungen Schwellenwert überschreiten 

Eine Warnung sollte ausgegeben werden, wenn die Metrik „Fehlerhafte Anforderungen“ den Schwellenwert überschreitet. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Warnung, wenn die Antwortzeit des Back-Ends für das letzte Byte den Schwellenwert überschreitet 

Diese Metrik gibt das Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antworttexts an. Eine Warnung sollte ausgegeben werden, wenn die Wartezeit für die Back-End-Antwort länger ist als üblich. Legen Sie z. B. fest, dass Sie gewarnt werden, wenn die Wartezeit für die Back-End-Antwort den üblichen Wert um mehr als 30 % überschreitet.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Warnung, wenn Application Gateway-Gesamtzeit den Schwellenwert überschreitet

Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem das letzte Byte der Antwort an den Client gesendet wurde. Eine Warnung sollte ausgegeben werden, wenn die Wartezeit für die Back-End-Antwort den üblichen Wert überschreitet. Legen Sie z. B. fest, dass Sie gewarnt werden, wenn die gesamte Wartezeit den üblichen Wert um mehr als 30 % überschreitet.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>Einrichten von WAF mit Geofilterung und Bot-Schutz, um Angriffe zu verhindern
Wenn Sie vor Ihrer Anwendung noch eine zusätzliche Sicherheitsschicht benötigen, verwenden Sie die Application Gateway WAF_v2-SKU für WAF-Funktionen. Sie können die v2-SKU so konfigurieren, dass nur der Zugriff auf Ihre Anwendungen aus einem oder mehreren bestimmten Ländern bzw. aus einer oder mehreren Regionen zugelassen wird. Sie richten eine benutzerdefinierte WAF-Regel ein, um Datenverkehr basierend auf dem Geostandort explizit zuzulassen oder zu blockieren. Weitere Informationen finden Sie unter [Geofilterung benutzerdefinierter Regeln](../web-application-firewall/ag/geomatch-custom-rules.md) und [Konfigurieren von benutzerdefinierten Regeln in Application Gateway WAF_v2-SKU mit PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Aktivieren Sie den Bot-Schutz, um bekannte bösartige Bots zu blockieren. Dadurch sollte die Menge des Datenverkehrs, der zu Ihrer Anwendung gelangt, reduziert werden. Weitere Informationen finden Sie unter [Bot-Schutz mit Einrichtungsanweisungen](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Aktivieren der Diagnose für Application Gateway und WAF

Diagnoseprotokolle ermöglichen es Ihnen, Firewallprotokolle, Leistungsprotokolle und Zugriffsprotokolle anzuzeigen. Sie können diese Protokolle in Azure verwenden, um Application Gateways zu verwalten und Probleme zu behandeln. Weitere Informationen finden Sie in unserer [Diagnosedokumentation](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Einrichten einer TLS-Richtlinie für zusätzliche Sicherheit
Stellen Sie sicher, dass Sie die neueste Version der TLS-Richtlinie verwenden ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)). Diese erzwingt TLS 1.2 und stärkere Verschlüsselungen. Weitere Informationen finden Sie unter [Konfigurieren von TLS-Richtlinienversionen und Verschlüsselungssammlungen über PowerShell](./application-gateway-configure-ssl-policy-powershell.md).