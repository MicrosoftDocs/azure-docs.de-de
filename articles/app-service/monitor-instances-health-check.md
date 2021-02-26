---
title: Überwachen der Integrität von App Service-Instanzen
description: Erfahren Sie, wie Sie die Integrität von App Service-Instanzen mithilfe der Integritätsprüfung überwachen.
keywords: Azure App Service, Web-App, Integritätsprüfung, Datenverkehr weiterleiten, fehlerfreie Instanzen, Pfad, Überwachung,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 8892723ec1a53c59e3e6183b5d53c2e61db4e5d0
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575227"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Überwachen von App Service-Instanzen mit der Integritätsprüfung

![Fehler bei der Integritätsprüfung][2]

In diesem Artikel wird die Integritätsprüfung im Azure-Portal verwendet, um App Service-Instanzen zu überwachen. Durch die Integritätsprüfung wird die Verfügbarkeit Ihrer Anwendung erhöht, indem fehlerhafte Instanzen entfernt werden. Ihr [App Service-Plan](/azure/app-service/overview-hosting-plans) sollte auf zwei oder mehr Instanzen skaliert sein, um die Integritätsprüfung verwenden zu können. Der Pfad der Integritätsüberprüfung sollte die kritischen Komponenten Ihrer Anwendung überprüfen. Wenn Ihre Anwendung z. B. von einer Datenbank und einem Messagingsystem abhängig ist, sollte der Endpunkt der Integritätsüberprüfung eine Verbindung mit diesen Komponenten herstellen. Wenn die Anwendung keine Verbindung mit einer kritischen Komponente herstellen kann, sollte der Pfad einen Antwortcode auf 500-Ebene zurückgeben, um damit anzugeben, dass die App fehlerhaft ist.

## <a name="what-app-service-does-with-health-checks"></a>Verwendung von Integritätsprüfungen durch App Service

- Wenn Sie einen Pfad Ihrer App an die Integritätsprüfung übergeben, pingt diese den Pfad bei allen Instanzen Ihrer App Service-App in Intervallen von 1 Minute.
- Wenn eine Instanz nach zwei oder mehr Anforderungen nicht mit einem Statuscode zwischen 200 und 299 (inklusive) antwortet oder nicht auf das Pingsignal reagiert, bestimmt das System, dass sie fehlerhaft ist, und entfernt sie.
- Nach der Entfernung wird die fehlerhafte Instanz von der Integritätsprüfung weiterhin gepingt. Wenn diese weiterhin erfolglos antwortet, startet App Service den zugrunde liegenden virtuellen Computer neu, um so die Instanz wieder in einen fehlerfreien Zustand zu versetzen.
- Wenn eine Instanz für eine Stunde fehlerhaft bleibt, wird sie durch eine neue Instanz ersetzt.
- Beim zentralen Hoch- oder Herunterskalieren pingt App Service außerdem den Pfad der Integritätsüberprüfung, um sicherzustellen, dass neue Instanzen bereit sind.

> [!NOTE]
> Die Integritätsprüfung folgt keinen 302-Umleitungen. Pro Stunde wird höchstens eine Instanz ersetzt, wobei der Maximalwert bei drei Instanzen pro Tag und App Service-Plan liegt.
>

## <a name="enable-health-check"></a>Aktivieren der Integritätsprüfung

![Navigation der Integritätsprüfung im Azure-Portal][3]

- Um die Integritätsprüfung zu aktivieren, wechseln Sie zum Azure-Portal, und wählen Sie Ihre App Service-App aus.
- Wählen Sie unter **Überwachung** die Option **Integritätsprüfung** aus.
- Wählen Sie **Aktivieren** aus, und geben Sie einen gültigen URL-Pfad für die Anwendung an, z. B. `/health` oder `/api/health`.
- Klicken Sie auf **Speichern**.

> [!CAUTION]
> Durch Änderungen an der Konfiguration der Integritätsprüfung wird Ihre App neu gestartet. Um die Auswirkungen auf Produktions-Apps zu minimieren, empfehlen wir, [Stagingslots zu konfigurieren](deploy-staging-slots.md) und diese in die Produktion zu tauschen.
>

### <a name="configuration"></a>Konfiguration

Zusätzlich zum Konfigurieren der Optionen der Integritätsprüfung können Sie auch die folgenden [App-Einstellungen](configure-common.md) konfigurieren:

| Name der App-Einstellung | Zulässige Werte | BESCHREIBUNG |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2–10 | Die maximale Anzahl von Pingfehlern. Wenn die Einstellung beispielsweise auf `2` festgelegt wird, werden Ihre Instanzen nach `2` fehlgeschlagenen Pings entfernt. Beim zentralen Hoch- oder Herunterskalieren pingt App Service außerdem den Pfad der Integritätsüberprüfung, um sicherzustellen, dass neue Instanzen bereit sind. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0-100 | Um zu vermeiden, dass fehlerfreie Instanzen überlastet werden, wird nicht mehr als die Hälfte der Instanzen ausgeschlossen. Wenn z. B. ein App Service-Plan auf vier Instanzen skaliert ist und drei fehlerhaft sind, werden höchstens zwei ausgeschlossen. Die anderen beiden Instanzen (eine fehlerfreie und eine fehlerhafte) empfangen weiterhin Anforderungen. Im ungünstigsten Fall, in dem alle Instanzen fehlerhaft sind, wird keine ausgeschlossen. Um dieses Verhalten außer Kraft zu setzen, legen Sie die App-Einstellung auf einen Wert zwischen `0` und `100` fest. Ein höherer Wert führt dazu, dass mehr fehlerhafte Instanzen entfernt werden (Standardwert ist 50). |

#### <a name="authentication-and-security"></a>Authentifizierung und Sicherheit

Die Integritätsprüfung integriert sich in die Authentifizierungs- und Autorisierungsfunktionen von App Service. Es sind keine zusätzlichen Einstellungen erforderlich, wenn diese Sicherheitsfeatures aktiviert sind. Wenn Sie aber Ihr eigenes Authentifizierungssystem verwenden, muss der Pfad der Integritätsüberprüfung anonymen Zugriff zulassen. Wenn für den Standort „Nur HTTP **S**“ aktiviert ist, wird die Anforderung einer Integritätsprüfung über HTTP **S** gesendet.

Entwicklungsteams in großen Unternehmen müssen häufig Sicherheitsanforderungen für verfügbar gemachte APIs erfüllen. Um den Endpunkt der Integritätsprüfung zu sichern, sollten Sie zunächst Features wie [IP-Einschränkungen](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [Clientzertifikate](app-service-ip-restrictions.md#set-an-ip-address-based-rule) oder ein virtuelles Netzwerk verwenden, um den Anwendungszugriff einzuschränken. Sie können den Endpunkt der Integritätsprüfung sichern, indem Sie anfordern, dass der `User-Agent` der eingehenden Anforderung `ReadyForRequest/1.0` entspricht. Der Benutzer-Agent kann nicht manipuliert werden, da die Anforderung bereits durch die vorherigen Sicherheitsfeatures gesichert wäre.

## <a name="monitoring"></a>Überwachung

Nachdem Sie den Pfad der Integritätsüberprüfung der Anwendung angegeben haben, können Sie die Integrität Ihres Standorts mithilfe von Azure Monitor überwachen. Klicken Sie im Portal auf dem Blatt **Integrität überprüfen** auf der oberen Symbolleiste auf **Metriken**. Daraufhin wird ein neues Blatt geöffnet, auf dem Sie den Verlauf des Integritätsstatus der Site anzeigen und eine neue Warnungsregel erstellen können. Weitere Informationen zum Überwachen Ihrer Standorte finden Sie im [Handbuch zu Azure Monitor](web-sites-monitor.md).

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen einer Aktivitätsprotokollwarnung, um alle Vorgänge der Engine für die automatische Skalierung für Ihr Abonnement zu überwachen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Erstellen Sie eine Aktivitätsprotokollwarnung, um alle Autoskalierungs-Vorgänge zum horizontalen Herunterskalieren und horizontalen Hochskalieren in Ihrem Abonnement, bei denen Fehler aufgetreten sind, zu überwachen.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
