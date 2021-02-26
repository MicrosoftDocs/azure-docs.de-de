---
title: Überwachen von Apps
description: In diesem Artikel wird die Überwachung von Apps in Azure App Service mit dem Azure-Portal beschrieben. Hier werden die gemeldeten Kontingente und Metriken erklärt.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 587ad53fa3a4875f7019a8c094db1b9a5f096519
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509552"
---
# <a name="monitor-apps-in-azure-app-service"></a>Überwachen von Apps in Azure App Service
[Azure App Service](./overview.md) bietet integrierte Überwachungsfunktionen für Web-Apps, mobile und API-Apps im [Azure-Portal](https://portal.azure.com).

Im Azure-Portal können Sie *Kontingente* und *Metriken* für eine App sowie den App Service-Plan einsehen und Regeln für *Warnungen* und die *automatische Skalierung* auf Grundlage von Metriken einrichten.

## <a name="understand-quotas"></a>Grundlegende Informationen zu Kontingenten

In App Service gehostete Apps unterliegen bestimmten Grenzwerten in Bezug auf die verwendbaren Ressourcen. Die Grenzwerte werden über den App Service-Plan definiert, der der App zugeordnet ist.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Wenn die App in einem Plan vom Typ *Free* oder *Shared* gehostet wird, werden die Grenzwerte für die Ressourcen, die von der App verwendet werden können, durch Kontingente definiert.

Wenn die App in einem Plan vom Typ *Basic*, *Standard* oder *Premium* gehostet wird, werden die Grenzwerte der Ressourcen, die verwendet werden können, über die Elemente *Größe* (Klein, Mittel, Groß) und *Instanzanzahl* (1, 2, 3,...) des App Service-Plans festgelegt.

Im Folgenden sind die Kontingente für Free- oder Shared-Apps aufgelistet:

| Kontingent | BESCHREIBUNG |
| --- | --- |
| **CPU (Short)** (CPU (kurz)) | Die zulässige CPU-Menge für diese App in einem Fünf-Minuten-Intervall. Dieses Kontingent wird alle fünf Minuten zurückgesetzt. |
| **CPU (Day)** (CPU (Tag)) | Die zulässige CPU-Gesamtmenge für diese App für einen Tag. Dieses Kontingent wird alle 24 Stunden um Mitternacht (UTC) zurückgesetzt. |
| **Memory** | Die zulässige Gesamtmenge an Arbeitsspeicher für diese App. |
| **Bandwidth** | Die zulässige Gesamtmenge an ausgehender Bandbreite für diese App für einen Tag. Dieses Kontingent wird alle 24 Stunden um Mitternacht (UTC) zurückgesetzt. |
| **Filesystem** | Die zulässige Gesamtmenge an Speicher. |

Das einzige Kontingent, das für Apps gilt, die in *Basic*, *Standard* und *Premium* gehostet werden, ist „Filesystem“ (Dateisystem).

Weitere Informationen zu den spezifischen Kontingenten, Grenzwerten und Features, die für die verschiedenen App Service-SKUs gelten sind, finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Kontingenterzwingung

Wenn eine App das Kontingent für *CPU (Short)* (CPU (kurz)), *CPU (Day)* (CPU (Tag)) oder *Bandwidth* (Bandbreite) überschreitet, wird die App beendet, bis das Kontingent zurückgesetzt wird. Während dieses Zeitraums führen alle eingehenden Anforderungen zu einem HTTP 403-Fehler.

![403-Fehlermeldung][http403]

Wenn das Arbeitsspeicherkontingent der App überschritten wird, wird die App vorübergehend beendet.

Wenn das Dateisystemkontingent überschritten wird, tritt bei jedem Schreibvorgang ein Fehler auf. Zu Schreibvorgängen zählen auch alle Protokollschreibvorgänge.

Sie können Kontingente erhöhen oder aus Ihrer App entfernen, indem Sie ein Upgrade für den App Service-Plan durchführen.

## <a name="understand-metrics"></a>Grundlegendes zu Metriken

> [!NOTE]
> **Dateisystemnutzung** ist eine neue Metrik, die global eingeführt wird. Es werden keine Daten erwartet, es sei denn, Ihre App wird in einer App Service-Umgebung gehostet.
> 

> [!IMPORTANT]
> **Durchschnittliche Antwortzeit** wird eingestellt, um Verwechslungen mit Metrikaggregationen zu vermeiden. Verwenden Sie als Ersatz **Antwortzeit**.

> [!NOTE]
> Metriken für eine App umfassen die Anforderungen an die SCM-Site (Kudu) der App.  Dies schließt Anforderungen ein, um den Protokolldatenstrom (Logstream) der Website mithilfe von Kudu anzuzeigen.  Logstream-Anforderungen können mehrere Minuten dauern, was sich auf die Anforderungszeitmetriken auswirkt.  Benutzer sollten diese Beziehung beachten, wenn Sie diese Metriken mit der Autoskalierungslogik verwenden.
> 

Metriken liefern Informationen zur App oder zum Verhalten des App Service-Plans.

Für eine App sind folgende Metriken verfügbar:

| Metrik | BESCHREIBUNG |
| --- | --- |
| **Antwortzeit** | Die Zeit in Sekunden, die die App zum Bedienen von Anforderungen benötigt. |
| **Durchschnittliche Antwortzeit (veraltet)** | Die durchschnittliche Zeit in Sekunden, die die App zum Bereitstellen von Anforderungen benötigt. |
| **Durchschnittlicher Arbeitssatz für Arbeitsspeicher** | Die durchschnittliche Menge an Arbeitsspeicher, die von der App verwendet wird, in Megabytes (MiB). |
| **Verbindungen** | Die Anzahl gebundener Sockets in der Sandbox („w3wp.exe“ und untergeordnete Prozesse).  Ein gebundener Socket wird durch Aufrufen von bind()-/connect()-APIs erstellt und bleibt erhalten, bis er per „CloseHandle()“/“closesocket()“ geschlossen wird. |
| **CPU-Zeit** | Die CPU-Menge in Sekunden, die von der App verbraucht wird. Weitere Informationen zu dieser Metrik finden Sie unter [CPU-Zeit und CPU-Prozentsatz](#cpu-time-vs-cpu-percentage). |
| **Aktuelle Assemblys** | Die aktuelle Anzahl von Assemblys, die in allen Anwendungsdomänen in dieser Anwendung geladen wurden. |
| **Eingehende Daten** | Die Menge an eingehender Bandbreite in MiB, die von der App verbraucht wird. |
| **Ausgehende Daten** | Die Menge an ausgehender Bandbreite in MiB, die von der App verbraucht wird. |
| **Dateisystemnutzung** | Die Nutzungsmenge in Bytes nach Speicherfreigabe. |
| **Garbage Collections der Generation 0** | Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 0 ausgeführt wurde. In Garbage Collections höherer Generationen sind alle Garbage Collections niedrigerer Generationen enthalten.|
| **Garbage Collections der Generation 1** | Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 1 ausgeführt wurde. In Garbage Collections höherer Generationen sind alle Garbage Collections niedrigerer Generationen enthalten.|
| **Garbage Collections der Generation 2** | Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 2 ausgeführt wurde.|
| **Anzahl Handles** | Die Gesamtanzahl von Handles, die aktuell durch den App-Prozess geöffnet sind.|
| **Status der Integritätsüberprüfung** | Der durchschnittliche Integritätsstatus über die Instanzen der Anwendung im App Service-Plan.|
| **HTTP 2xx** | Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode ≥ 200 und < 300 führen. |
| **HTTP 3xx** | Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode ≥ 300 und < 400 führen. |
| **HTTP 401** | Die Anzahl von Anforderungen, die zu einem HTTP 401-Statuscode führen. |
| **HTTP 403** | Die Anzahl von Anforderungen, die zu einem HTTP 403-Statuscode führen. |
| **HTTP 404** | Die Anzahl von Anforderungen, die zu einem HTTP 404-Statuscode führen. |
| **HTTP 406** | Die Anzahl von Anforderungen, die zu einem HTTP 406-Statuscode führen. |
| **HTTP 4xx** | Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode ≥ 400 und < 500 führen. |
| **HTTP-Serverfehler** | Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode ≥ 500 und < 600 führen. |
| **IO Other Bytes Per Second** (E/A: Andere Bytes pro Sekunde) | Die Rate, mit der der App-Prozess Bytes an E/A-Vorgänge ausgibt, die keine Daten beinhalten (beispielsweise Steuerungsvorgänge).|
| **IO Other Operations Per Second** (E/A: Andere Vorgänge pro Sekunde) | Die Rate, mit der der App-Prozess E/A-Vorgänge ausgibt, bei denen es sich um keine Lese- oder Schreibvorgänge handelt.|
| **E/A: Gelesene Bytes pro Sekunde** | Die Rate, mit der der App-Prozess Bytes aus E/A-Vorgängen liest.|
| **E/A: Lesevorgänge pro Sekunde** | Die Rate, mit der der App-Prozess E/A-Lesevorgänge ausgibt.|
| **E/A: Geschriebene Bytes pro Sekunde** | Die Rate, mit der der App-Prozess Bytes in E/A-Vorgänge schreibt.|
| **E/A: Schreibvorgänge pro Sekunde** | Die Rate, mit der der App-Prozess E/A-Schreibvorgänge ausgibt.|
| **Arbeitssatz für Arbeitsspeicher** | Die aktuelle Menge an Arbeitsspeicher in MiB, die von der App verwendet wird. |
| **Private Bytes** | „Private Bytes“ gibt die aktuell durch den App-Prozess zugeordnete Größe des Arbeitsspeichers (in Bytes) an, die nicht mit anderen Prozessen geteilt werden kann.|
| **Anforderungen** | Die Gesamtzahl von Anforderungen, unabhängig vom sich ergebenden HTTP-Statuscode. |
| **Requests In Application Queue** (Anforderungen in der Anwendungswarteschlange) | Die Anzahl von Anforderungen in der Warteschlange für Anwendungsanforderungen.|
| **Threadanzahl** | Die Anzahl von Threads, die derzeit im App-Prozess aktiv sind.|
| **Anwendungsdomänen insgesamt** | Die Anzahl von Anwendungsdomänen, die aktuell in dieser Anwendung geladen sind.|
| **Gesamtanzahl der entladenen Anwendungsdomänen** | Die Gesamtanzahl von Anwendungsdomänen, die seit dem Start der Anwendung entladen wurden.|


Für einen App Service-Plan sind folgende Metriken verfügbar:

> [!NOTE]
> Metriken für App Service-Pläne sind nur für Pläne in den Tarifen *Basic*, *Standard* und *Premium* verfügbar.
> 

| Metrik | BESCHREIBUNG |
| --- | --- |
| **CPU-Prozentsatz** | Die durchschnittliche CPU-Nutzung über alle Instanzen des Plans hinweg. |
| **Arbeitsspeicherprozentsatz** | Die durchschnittliche Arbeitsspeichernutzung über alle Instanzen des Plans hinweg. |
| **Eingehende Daten** | Die durchschnittliche eingehende Bandbreite, die für alle Instanzen des Plans verwendet wird. |
| **Ausgehende Daten** | Die durchschnittliche ausgehende Bandbreite, die für alle Instanzen des Plans verwendet wird. |
| **Länge der Datenträgerwarteschlange** | Die durchschnittliche Anzahl von Lese- und Schreibanforderungen, die im Speicher in die Warteschlange eingereiht wurden. Ein hoher Wert für die Länge der Datenträgerwarteschlange ist ein Anzeichen dafür, dass eine App aufgrund einer übermäßigen E/A-Aktivität des Datenträgers verlangsamt wird. |
| **Länge der HTTP-Warteschlange** | Die durchschnittliche Anzahl von HTTP-Anforderungen, die sich in der Warteschlange befinden müssen, bevor sie erfüllt werden. Eine hohe oder zunehmende HTTP-Warteschlangenlänge deutet darauf hin, dass für einen Plan eine hohe Auslastung besteht. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-Zeit und CPU-Prozentsatz
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Es gibt zwei Metriken, die die CPU-Auslastung widerspiegeln:

**CPU-Zeit**: Hilfreich für Apps, die unter einem Plan vom Typ „Free“ oder „Shared“ gehostet werden, da eines der Kontingente basierend auf den von der App verbrauchten CPU-Minuten definiert ist.

**CPU percentage** (CPU-Prozentsatz): Hilfreich für Apps, die in den Plänen „Basic“, „Standard“ und „Premium“ gehostet werden, da sie horizontal hochskaliert werden können. Der CPU-Prozentsatz ist ein guter Indikator für die allgemeine Nutzung über alle Instanzen hinweg.

## <a name="metrics-granularity-and-retention-policy"></a>Granularität und Aufbewahrungsrichtlinien für Metriken
Metriken für eine App und einen App Service-Plan werden vom Dienst protokolliert und aggregiert sowie [gemäß diesen Regeln aufbewahrt](../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics).

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Überwachen von Kontingenten und Metriken im Azure-Portal
Im [Azure-Portal](https://portal.azure.com) können Sie den Status der verschiedenen Kontingente und Metriken einsehen, die für eine App gelten.

![Kontingentdiagramm im Azure-Portal][quotas]

Wählen Sie **Einstellungen** > **Kontingente** aus, um nach Kontingenten zu suchen. Folgendes können Sie vom Diagramm ablesen: 
1. den Namen des Kontingents
1. sein Zurücksetzungsintervall
1. sein aktueller Grenzwert
1. sein aktueller Wert

![Metrikdiagramm im Azure-Portal][metrics] Sie können direkt über die Seite **Übersicht** der Ressource auf Metriken zugreifen. Hier werden Diagramme angezeigt, die einige der Metriken für Apps darstellen.

Wenn Sie auf eins der Diagramme klicken, wechseln Sie zur Metrikansicht, in der Sie benutzerdefinierte Diagramme erstellen, verschiedene Metriken abfragen und vieles mehr können. 

Weitere Informationen zu Metriken finden Sie unter [Von Azure Monitor gesammelte Überwachungsdaten](../azure-monitor/platform/data-platform.md).

## <a name="alerts-and-autoscale"></a>Warnungen und automatische Skalierung
Metriken für eine App oder einen App Service-Plan können mit Warnungen verknüpft werden. Weitere Informationen finden Sie unter [Verwenden des Azure-Portals zum Erstellen von Warnungen für Azure-Dienste](../azure-monitor/platform/alerts-classic-portal.md).

App Service-Apps, die unter App Service-Plänen vom Typ „Basic“ oder höher gehostet werden, unterstützen die automatische Skalierung. Mithilfe der automatischen Skalierung können Sie Regeln zur Überwachung der Metriken des App Service-Plans konfigurieren. Über Regeln kann die Anzahl der Instanzen erhöht oder verringert werden, wodurch bei Bedarf zusätzliche Ressourcen bereitgestellt werden können. Außerdem helfen Regeln auch dabei, Geld zu sparen, wenn die App zu oft bereitgestellt wird.

Weitere Informationen zur automatischen Skalierung finden Sie unter [Erste Schritte mit der automatischen Skalierung in Azure](../azure-monitor/platform/autoscale-get-started.md) und [Bewährte Methoden für die automatische Skalierung](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
