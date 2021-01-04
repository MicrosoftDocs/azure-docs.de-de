---
title: Erste Schritte mit der automatischen Skalierung in Azure
description: Informationen zum Skalieren Ihrer Ressource Web-App, Clouddienst, virtueller Computer oder VM-Skalierungsgruppe in Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 364309301b403234936da1bac6e1b74af24c2fdb
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573305"
---
# <a name="get-started-with-autoscale-in-azure"></a>Erste Schritte mit der automatischen Skalierung in Azure
In diesem Artikel wird beschrieben, wie Sie Ihre automatische Skalierungseinstellung für Ihre Ressource im Microsoft Azure-Portal einrichten.

Die automatische Skalierung von Azure Monitor gilt nur für [VM.Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Clouddienste](https://azure.microsoft.com/services/cloud-services/), [App Service – Web-Apps](https://azure.microsoft.com/services/app-service/web/) und [API Management-Dienste](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Ermitteln der Einstellungen der automatischen Skalierung in Abonnements

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Sie können alle Ressourcen ermitteln, für die die automatische Skalierung in Azure Monitor angewendet wird. Befolgen Sie die folgenden Schritte für eine ausführliche exemplarische Vorgehensweise:

1. Öffnen Sie das [Azure-Portal][1].
1. Klicken Sie auf das Azure Monitor-Zeichen im linken Bereich.
  ![Öffnen Sie Azure Monitor][2].
1. Klicken Sie auf **Automatische Skalierung**, um alle Ressourcen anzuzeigen, für die die automatische Skalierung zutrifft, zusammen mit dem aktuellen Status der automatischen Skalierung.
  ![Entdecken von Metriken und automatischer Skalierung in Azure Monitor][3]

Über den Filterbereich im oberen Bereich können Sie die Liste nach unten verschieben, um Ressourcen in einer bestimmten Ressourcengruppe, bestimmte Ressourcentypen oder eine bestimmte Ressource auszuwählen.

Zu jeder Ressource finden Sie die aktuelle Anzahl der Instanzen sowie den jeweiligen Autoskalierungsstatus. Der Autoskalierungsstatus kann wie folgt lauten:

- **Nicht konfiguriert:** Sie haben die automatische Skalierung für diese Ressource noch nicht aktiviert.
- **Enabled**: Sie haben die automatische Skalierung für diese Ressource aktiviert.
- **Disabled**: Sie haben die automatische Skalierung für diese Ressource deaktiviert.

## <a name="create-your-first-autoscale-setting"></a>Erstellen Ihrer ersten Autoskalierungseinstellung

Nun können Sie eine einfache schrittweise exemplarische Vorgehensweise zum Erstellen Ihrer ersten Einstellung für die automatische Skalierung durchgehen.

1. Öffnen Sie in Azure Monitor das Blatt **Automatisch skalieren**, und wählen Sie eine Ressource aus, die skaliert werden soll. (Die folgenden Schritte beruhen auf einem App Service-Plan, der einer Web-App zugeordnet ist. Sie können [Ihre erste ASP.NET-Web-App in Azure in fünf Minuten erstellen][4].)
1. Beachten Sie, dass die aktuelle Instanzenanzahl 1 beträgt. Klicken Sie auf **Automatische Skalierung aktivieren**.
  ![Skalierungseinstellung für die neue Web-App][5]
1. Geben Sie einen Namen für die Skalierungseinstellung an, und klicken Sie dann auf **Regel hinzufügen**. Beachten Sie die Optionen für die Skalierungsregel, die auf der rechten Seite als Kontextbereich geöffnet wird. Standardmäßig wird die Option zum Skalieren der Anzahl Ihrer Instanzen auf „1“ festgelegt, wenn der CPU-Prozentsatz der Ressource 70 % überschreitet. Behalten Sie die Standardwerte bei, und klicken Sie auf **Hinzufügen**.
  ![Erstellen der Skalierungseinstellung für eine Web-App][6]
1. Nun haben Sie Ihre erste Skalierungsregel erstellt. Beachten Sie, dass die UX bewährte Methoden empfiehlt und angibt, dass empfohlen wird, mindestens eine Regel zum Abskalieren zu verwenden. Gehen Sie folgendermaßen vor:

    a. Klicken Sie auf **Hinzufügen einer Regel**

    b. Legen Sie **Operator** auf **Weniger als** fest.

    c. Legen Sie **Schwellenwert** auf **20** fest.

    d. Legen Sie **Vorgang** auf **Anzahl verringern um** fest.

   Sie sollten nun über eine Skalierungseinstellung verfügen, die basierend auf der CPU-Auslastung zentral hoch- bzw. herunterskaliert.
   ![Skalieren basierend auf der CPU][8]
1. Klicken Sie auf **Speichern**.

Glückwunsch! Sie haben nun Ihre erste Skalierungseinstellung erfolgreich für die automatische Skalierung Ihrer Web-App basierend auf der CPU-Auslastung konfiguriert.

> [!NOTE]
> Diese Schritte gelten auch für die ersten Schritte mit der VM-Skalierungsgruppe oder einer Clouddienstrolle.

## <a name="other-considerations"></a>Weitere Überlegungen
### <a name="scale-based-on-a-schedule"></a>Skalieren basierend auf einem Zeitplan
Neben der Skalierung basierend auf der CPU können Sie festlegen, dass die Skalierung unterschiedlich an bestimmten Wochentage skaliert werden soll.

1. Klicken Sie auf **Skalierungsbedingung hinzufügen**.
1. Legen Sie den Skalierungsmodus fest. Es gelten dieselben Regeln wie bei der Standardbedingung.
1. Wählen Sie für den Zeitplan **An bestimmten Tagen wiederholen** aus.
1. Wählen Sie die Tage sowie Start- bzw. Endzeit aus, an denen bzw. zu der die Skalierungsbedingung angewendet werden soll.

![Skalierungsbedingung basierend auf einem Zeitplan][9]
### <a name="scale-differently-on-specific-dates"></a>Individuelles Skalieren an bestimmten Daten
Neben der Skalierung basierend auf der CPU können Sie auch festlegen, dass die Skalierung unterschiedlich an bestimmten Daten skaliert werden soll.

1. Klicken Sie auf **Skalierungsbedingung hinzufügen**.
1. Legen Sie den Skalierungsmodus fest. Es gelten dieselben Regeln wie bei der Standardbedingung.
1. Wählen Sie für den Zeitplan **Start-/Enddatum angeben** aus.
1. Wählen Sie die Start-/End-Datumsangaben sowie Start- bzw. Endzeit aus, an denen bzw. zu der die Skalierungsbedingung angewendet werden soll.

![Skalierungsbedingung basierend auf Daten][10]

### <a name="view-the-scale-history-of-your-resource"></a>Anzeigen des Skalierungsverlaufs der Ressource
Wenn die Ressource zentral hoch- oder herunterskaliert wird, wird ein Ereignis im Aktivitätsprotokoll protokolliert. Sie können den Skalierungsverlauf der Ressource der letzten 24 Stunden anzeigen, indem Sie zur Registerkarte **Verlauf ausführen** wechseln.

![Ausführungsverlauf][11]

Wenn Sie den gesamten Skalierungsverlauf (für bis zu 90 Tage) anzeigen möchten, klicken Sie auf **Klicken Sie hier, um weitere Details anzuzeigen**. Das Aktivitätsprotokoll wird mit der vorausgewählten Kategorie „Automatisch skalieren“ für Ihre Ressource und Kategorie geöffnet.

### <a name="view-the-scale-definition-of-your-resource"></a>Anzeigen der Skalierungsdefinition Ihrer Ressource
Die automatische Skalierung ist eine Azure Resource Manager-Ressource. Sie können die Skalierungsdefinition im JSON-Format anzeigen, indem Sie zur Registerkarte **JSON** wechseln.

![Skalierungsdefinition][12]

Bei Bedarf können Sie Änderungen direkt in JSON vornehmen. Diese Änderungen werden nach dem Speichern berücksichtigt.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Deaktivieren der automatischen Skalierung und manuelles Skalieren der Instanzen
Es kann möglicherweise vorkommen, dass Sie Ihre aktuelle Skalierungseinstellung deaktivieren und die Ressource manuell skalieren müssen.

Klicken Sie oben auf die Schaltfläche **Automatische Skalierung deaktivieren**.
![Deaktivieren der automatischen Skalierung][13]

> [!NOTE]
> Diese Option deaktiviert Ihre Konfiguration. Sie können jedoch zu ihr zurückgelangen, wenn Sie die automatische Skalierung erneut aktivieren.

Nun können Sie die Anzahl der Instanzen festlegen, die manuell skaliert werden sollen.

![Festlegen der manuellen Skalierung][14]

Sie können immer zur automatischen Skalierung zurückkehren, indem Sie auf **Automatische Skalierung aktivieren** und dann auf **Speichern** klicken.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Weiterleiten von Datenverkehr an fehlerfreie Instanzen (App Service)

Wenn Sie auf mehrere Instanzen aufskalieren, kann App Service Integritätsprüfungen für Ihre Instanzen durchführen, damit Datenverkehr nur an die fehlerfreien Instanzen weitergeleitet wird. Öffnen Sie hierzu im Portal Ihre App Service-Instanz, und wählen Sie dann **Integrität überprüfen** unter **Überwachung** aus. Wählen Sie **Aktivieren** aus, und geben Sie einen gültigen URL-Pfad für die Anwendung an, z. B. `/health` oder `/api/health`. Klicken Sie auf **Speichern**.

Um die Funktion mit ARM-Vorlagen zu aktivieren, legen Sie die Eigenschaft `healthcheckpath` der `Microsoft.Web/sites`-Ressource auf den Pfad der Integritätsüberprüfung für Ihren Standort fest, z. B. `"/api/health/"`. Zum Deaktivieren der Funktion legen Sie die Eigenschaft wieder auf eine leere Zeichenfolge (`""`) fest.

### <a name="health-check-path"></a>Pfad der Integritätsüberprüfung

Der Pfad muss innerhalb einer Minute mit einem Statuscode zwischen 200 und 299 (einschließlich) antworten. Sollte der Pfad nicht innerhalb einer Minute antworten oder einen Statuscode außerhalb des Bereichs zurückgeben, gilt die Instanz als fehlerhaft. App Service folgt keinen 302-Umleitungen im Integritätsüberprüfungspfad. Die Integritätsüberprüfung ist in die Authentifizierungs- und Autorisierungsfeatures von App Service integriert. Das System erreicht den Endpunkt auch dann, wenn diese Sicherheitsfeatures aktiviert sind. Wenn Sie ein eigenes Authentifizierungssystem verwenden, muss der Pfad der Integritätsüberprüfung anonymen Zugriff zulassen. Wenn für den Standort „Nur HTTP **S**“ aktiviert ist, wird die Anforderung einer Integritätsprüfung über HTTP **S** gesendet.

Der Pfad der Integritätsüberprüfung sollte die kritischen Komponenten der Anwendung überprüfen. Wenn Ihre Anwendung z. B. von einer Datenbank und einem Messagingsystem abhängig ist, sollte der Endpunkt der Integritätsüberprüfung eine Verbindung mit diesen Komponenten herstellen. Wenn die Anwendung keine Verbindung mit einer kritischen Komponente herstellen kann, sollte der Pfad einen Antwortcode auf 500-Ebene zurückgeben, um damit anzugeben, dass die App fehlerhaft ist.

#### <a name="security"></a>Sicherheit 

Entwicklungsteams in großen Unternehmen müssen häufig Sicherheitsanforderungen für die verfügbar gemachten APIs erfüllen. Um den Endpunkt der Integritätsprüfung zu sichern, sollten Sie zunächst Features wie [IP-Einschränkungen](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule), [Clientzertifikate](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule) oder ein virtuelles Netzwerk verwenden, um den Zugriff auf die Anwendung einzuschränken. Sie können den Endpunkt der Integritätsprüfung selbst sichern, indem Sie anfordern, dass der `User-Agent` der eingehenden Anforderung `ReadyForRequest/1.0` entspricht. Der Benutzer-Agent kann nicht manipuliert werden, da die Anforderung bereits durch die vorherigen Sicherheitsfeatures gesichert wurde.

### <a name="behavior"></a>Verhalten

Wenn der Pfad der Integritätsprüfung angegeben ist, sendet App Service auf allen Instanzen ein Ping an den Pfad. Wenn nach 5 Pings kein erfolgreicher Antwortcode empfangen wird, gilt diese Instanz als fehlerhaft. Fehlerhafte Instanzen werden von der Load Balancer-Rotation ausgeschlossen, wenn Sie auf 2 oder mehr Instanzen aufskaliert haben und mindestens den [Basic-Tarif](../../app-service/overview-hosting-plans.md) verwenden. Sie können die erforderliche Anzahl fehlerhafter Pings mit der App-Einstellung `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` konfigurieren. Diese App-Einstellung kann auf eine beliebige Ganzzahl zwischen 2 und 10 festgelegt werden. Wenn sie beispielsweise auf `2` festgelegt wird, werden die Instanzen nach zwei fehlgeschlagenen Pings aus dem Lastenausgleich entfernt. Wenn Sie darüber hinaus zentral oder horizontal hochskalieren, pingt App Service den Pfad der Integritätsüberprüfung, um sicherzustellen, dass die neuen Instanzen für Anforderungen bereit sind, bevor sie zum Lastenausgleich hinzugefügt werden.

> [!NOTE]
> Denken Sie daran, dass der App Service-Plan auf mindestens 2 Instanzen aufskaliert und **mindestens der Basic-Tarif** verwendet werden muss, damit der Lastenausgleichsausschluss erfolgt. Wenn Sie nur über eine Instanz verfügen, wird diese auch dann nicht aus dem Lastenausgleich entfernt, wenn sie fehlerhaft ist. 

Die verbleibenden fehlerfreien Instanzen werden möglicherweise stärker ausgelastet. Um zu vermeiden, dass die verbleibenden Instanzen überlastet werden, wird nicht mehr als die Hälfte der Instanzen ausgeschlossen. Wenn z. B. ein App Service-Plan auf vier Instanzen aufskaliert wird und drei fehlerhaft sind, werden höchstens zwei von der Lastenausgleichsrotation ausgeschlossen. Die anderen beiden Instanzen (1 fehlerfrei und 1 fehlerhaft) empfangen weiterhin Anforderungen. Sollten im ungünstigsten Fall alle Instanzen fehlerhaft sein, wird keine Instanz ausgeschlossen. Wenn Sie dieses Verhalten überschreiben möchten, können Sie die App-Einstellung `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` auf einen Wert zwischen `0` und `100` festlegen. Ein höherer Wert führt dazu, dass mehr fehlerhafte Instanzen entfernt werden. (Der Standardwert ist „50“.)

Wenn eine Instanz für eine Stunde fehlerhaft bleibt, wird sie durch eine neue Instanz ersetzt. Pro Stunde wird höchstens eine Instanz ersetzt, wobei der Maximalwert bei drei Instanzen pro Tag und App Service-Plan liegt.

### <a name="monitoring"></a>Überwachung

Nachdem Sie den Pfad der Integritätsüberprüfung der Anwendung angegeben haben, können Sie die Integrität Ihres Standorts mithilfe von Azure Monitor überwachen. Klicken Sie im Portal auf dem Blatt **Integrität überprüfen** auf der oberen Symbolleiste auf **Metriken**. Daraufhin wird ein neues Blatt geöffnet, auf dem Sie den Verlauf des Integritätsstatus der Site anzeigen und eine neue Warnungsregel erstellen können. Weitere Informationen zum Überwachen Ihrer Standorte finden Sie im [Handbuch zu Azure Monitor](../../app-service/web-sites-monitor.md).

## <a name="moving-autoscale-to-a-different-region"></a>Verschieben der Autoskalierung in eine andere Region
In diesem Abschnitt wird beschrieben, wie Sie die Autoskalierung von Azure in eine andere Region im selben Abonnement und in derselben Ressourcengruppe verschieben. Sie können die Einstellungen für die Autoskalierung mithilfe der REST-API verschieben.
### <a name="prerequisite"></a>Voraussetzungen
1. Stellen Sie sicher, dass das Abonnement und die Ressourcengruppe verfügbar sind und die Details in der Quell- und der Zielregion identisch sind.
1. Stellen Sie sicher, dass die Autoskalierung von Azure in der [Azure-Zielregion für das Verschieben](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) verfügbar ist.

### <a name="move"></a>Move
Erstellen Sie mithilfe der [REST-API](/rest/api/monitor/autoscalesettings/createorupdate) eine Autoskalierungseinstellung in der neuen Umgebung. Die in der Zielregion erstellte Einstellung für die Autoskalierung ist eine Kopie der Autoskalierungseinstellung in der Quellregion.

[Diagnoseeinstellungen](./diagnostic-settings.md), die in Verbindung mit der Autoskalierungseinstellung in der Quellregion erstellt wurden, können nicht verschoben werden. Sie müssen die Diagnoseeinstellungen nach Abschluss der Erstellung der Autoskalierungseinstellungen in der Zielregion neu erstellen. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Weitere Informationen zum Verschieben von Ressourcen zwischen Azure-Regionen
Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen einer Aktivitätsprotokollwarnung, um alle Vorgänge der Engine für die automatische Skalierung für Ihr Abonnement zu überwachen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Erstellen Sie eine Aktivitätsprotokollwarnung, um alle Autoskalierungs-Vorgänge zum horizontalen Herunterskalieren und horizontalen Hochskalieren in Ihrem Abonnement, bei denen Fehler aufgetreten sind, zu überwachen.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
