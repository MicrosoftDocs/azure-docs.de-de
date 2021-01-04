---
title: Häufig gestellte Fragen zur Konfiguration
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zu Konfigurations- und Verwaltungsproblemen in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fb193637525722bf227241a614cd977fbf70c9ac
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074181"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Häufig gestellte Fragen zur Konfiguration und Verwaltung von Web-Apps in Azure

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zu Konfigurations- und Verwaltungsproblemen bei [Azure App Service-Web-Apps](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Gibt es Einschränkungen, die ich berücksichtigen muss, wenn ich App Service-Ressourcen verschieben möchte?

Wenn Sie App Service-Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement verschieben möchten, sind einige Einschränkungen zu beachten. Weitere Informationen finden Sie unter [App Service-Einschränkungen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Wie verwende ich einen benutzerdefinierten Domänennamen für meine Web-App?

Antworten auf häufig gestellte Fragen zur Verwendung eines benutzerdefinierten Domänennamens mit Ihrer Azure-Web-App finden Sie in unserem siebenminütigen Video [Add a Custom Domain Name](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) (Hinzufügen eines benutzerdefinierten Domänennamens). Das Video bietet eine exemplarische Vorgehensweise zum Hinzufügen eines benutzerdefinierten Domänennamens. Es wird beschrieben, wie Sie Ihre URL anstelle der URL „*.azurewebsites.net“ mit Ihrer App Service-Web-App verwenden. Außerdem sehen Sie eine ausführliche exemplarische Vorgehensweise zum [Zuordnen eines benutzerdefinierten Domänennamens](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Wie erwerbe ich für meine Web-App eine neue benutzerdefinierte Domäne?

Weitere Informationen zum Kauf und Einrichten einer benutzerdefinierten Domäne für Ihre App Service-Web-App finden Sie unter [Kaufen und Konfigurieren eines benutzerdefinierten Domänennamens in App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Wie kann ich ein vorhandenes TLS-/SSL-Zertifikat für meine Web-App hochladen und konfigurieren?

Informationen zum Hochladen und Einrichten eines vorhandenen benutzerdefinierten TLS-/SSL-Zertifikats finden Sie unter [Hinzufügen eines TLS-/SSL-Zertifikats zu Ihrer App Service-App](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Wie kaufe und konfiguriere ich ein neues TLS-/SSL-Zertifikat für meine Web-App in Azure?

Informationen zum Erwerben und Einrichten eines TLS-/SSL-Zertifikats für Ihre App Service-Web-App finden Sie unter [Hinzufügen eines TLS-/SSL-Zertifikats zu Ihrer App Service-App](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Wie verschiebe ich Application Insights-Ressourcen?

Derzeit unterstützt Azure Application Insights keine Verschiebevorgänge. Wenn Ihre ursprüngliche Ressourcengruppe eine Application Insights-Ressource enthält, können Sie diese Ressource nicht verschieben. Wenn Sie die Application Insights-Ressource in den Versuch des Verschiebens einer App Service-App einschließen, misslingt der gesamte Verschiebevorgang. Application Insights und der App Service-Plan müssen sich allerdings nicht in derselben Ressourcengruppe wie die App befinden, damit die App ordnungsgemäß funktioniert.

Weitere Informationen finden Sie unter [App Service-Einschränkungen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Wo kann ich eine Checkliste finden und mehr über Verschiebevorgänge von Ressourcen erfahren?

Unter [App Service-Einschränkungen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) erfahren Sie, wie Sie Ressourcen in ein neues Abonnement oder eine neue Ressourcengruppe im gleichen Abonnement verschieben. Sie erhalten eine Checkliste für das Verschieben von Ressourcen, erfahren, welche Dienste den Verschiebevorgang unterstützen, und finden weitere Informationen zu App Service-Einschränkungen und anderen Themen.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Wie richte ich die Serverzeitzone für meine Web-App ein?

So richten Sie die Serverzeitzone für Ihre Web-App ein

1. Wechseln Sie im Azure-Portal in Ihrem App Service-Abonnement zum Menü **Anwendungseinstellungen**.
2. Fügen Sie unter **App-Einstellungen** diese Einstellung hinzu:
    * Schlüssel = WEBSITE_TIME_ZONE
    * Wert = *Die gewünschte Zeitzone*
3. Wählen Sie **Speichern** aus.

Informationen zu den App-Diensten, die unter Windows ausgeführt werden, finden Sie in der Ausgabe des Windows-Befehls `tzutil /L`. Verwenden Sie den Wert aus der zweiten Zeile jedes Eintrags. Beispiel: "Tonga Normalzeit". Einige dieser Werte werden auch im Artikel [Standardzeitzonen](/windows-hardware/manufacture/desktop/default-time-zones) in der Spalte **Zeitzone** aufgeführt.

Legen Sie für die App-Dienste, die unter Linux ausgeführt werden, einen Wert aus der [IANA TZ-Datenbank](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) fest. Beispiel: „America/Adak“.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Warum misslingen meine fortlaufend ausgeführten WebJobs mitunter?

Web-Apps werden standardmäßig entladen, wenn sie einen festgelegten Zeitraum im Leerlauf waren. Dadurch spart das System Ressourcen. In den Tarifen „Basic“ und „Standard“ können Sie die Einstellung **Always On** aktivieren, damit die Web-App die ganze Zeit geladen bleibt. Wenn Ihre Web-App fortlaufende WebJobs ausführt, sollten Sie **Always On** aktivieren, weil die WebJobs ansonsten nicht zuverlässig ausgeführt werden. Weitere Informationen finden Sie unter [Erstellen eines fortlaufend ausgeführten Webjobs](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Wie rufe ich die ausgehende IP-Adresse meiner Web-App ab?

So rufen Sie die Liste ausgehender IP-Adressen für Ihre Web-App ab

1. Wechseln Sie im Azure-Portal auf dem Blatt Ihrer Web-App zum Menü **Eigenschaften**.
2. Navigieren Sie zu **Ausgehende IP-Adressen**.

Die Liste ausgehender IP-Adressen wird angezeigt.

Um mehr darüber zu erfahren, wie Sie die ausgehende IP-Adresse abrufen, wenn Ihre Website in einer App Service-Umgebung gehostet wird, lesen Sie [Ausgehende Netzwerkadressen](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Wie kann eine reservierte oder dedizierte eingehende IP-Adresse für meine Web-App abgerufen werden?

Zum Einrichten einer dedizierten oder reservierten IP-Adresse für an die Website Ihrer Azure-App eingehende Aufrufe müssen Sie ein IP-basiertes TLS-/SSL-Zertifikat installieren und konfigurieren.

Beachten Sie, dass zum Verwenden einer dedizierten oder reservierten IP-Adresse für eingehende Aufrufe Ihr App Service-Plan mindestens den Tarif „Basic“ aufweisen muss.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kann ich mein App Service-Zertifikat zur Verwendung außerhalb von Azure exportieren, z.B. für eine Website, die an anderer Stelle gehostet wird? 

Ja, Sie können diese Zertifikate zur Verwendung außerhalb von Azure exportieren. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu App Service-Zertifikaten und benutzerdefinierten Domänen](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kann ich mein App Service-Zertifikat zur Verwendung mit anderen Azure-Clouddiensten exportieren?

Das Portal bietet eine erstklassige Umgebung für die Bereitstellung eines App Service-Zertifikats über Azure Key Vault für App Service-Apps. Allerdings haben sich Kunden mit dem Wunsch an uns gewendet, diese Zertifikate außerhalb der App Service-Plattform nutzen zu können, z.B. mit Azure Virtual Machines. Informationen dazu, wie Sie eine lokale PFX-Kopie Ihres App Service-Zertifikats zur Verwendung des Zertifikats mit anderen Azure-Ressourcen erstellen, finden Sie unter [Erstellen einer lokalen PFX-Kopie eines App Service-Zertifikats](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu App Service-Zertifikaten und benutzerdefinierten Domänen](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Warum wird die Meldung „Teilweise erfolgreich“ angezeigt, wenn ich versuche, meine Web-App zu sichern?

Eine häufige Ursache für Sicherungsfehler ist, dass einige Dateien von der Anwendung verwendet werden. Dateien, die verwendet werden, sind gesperrt, während die Sicherung erfolgt. Dies verhindert, dass diese Dateien gesichert werden, und führt ggf. zum Status „Teilweise erfolgreich“. Sie können dies möglicherweise dadurch verhindern, dass Sie Dateien vom Sicherungsprozess ausschließen. Sie können nach Wunsch nur das sichern, was sie benötigen. Weitere Informationen finden Sie unter [Backup just the Important Parts of your Site with Azure Web Apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/) (Ausschließliches Sichern der wichtigen Teile Ihrer Azure-Web-Apps-Website).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Wie entferne ich einen Header aus der HTTP-Antwort?

Um die Header aus der HTTP-Antwort zu entfernen, müssen Sie die Datei „web.config“ Ihrer Website bearbeiten. Weitere Informationen finden Sie unter [Removing standard server headers on Windows Azure Web Sites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) (Entfernen standardmäßiger Serverheader auf Ihren Azure-Websites).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Ist App Service mit dem PCI-Standard 3.0 und 3.1 konform?

Derzeit ist die Web-Apps-Funktion von Azure App Service mit PCI Data Security Standard (DSS), Version 3.0 Level 1, konform. Konformität mit PCI DSS, Version 3.1, ist in Kürze vorgesehen. Die Planung der Anpassung an den neuesten Standard ist bereits im Gange.

Die Zertifizierung gemäß PCI DSS-Version 3.1 erfordert das Deaktivieren von Transport Layer Security (TLS) 1.0. Derzeit ist die Deaktivierung von TLS 1.0 für die meisten App Service-Pläne nicht möglich. Wenn Sie jedoch die App Service-Umgebung verwenden möchten oder bereit sind, Ihre Workload in die App Service-Umgebung zu verlagern, erhalten Sie mehr Kontrolle über Ihre Umgebung. Dies umfasst das Deaktivieren von TLS 1.0 nach Kontaktierung des Azure-Supports. Für die nahe Zukunft ist geplant, Benutzern den Zugriff auf diese Einstellungen zu ermöglichen.

Weitere Informationen finden Sie unter [Konformität von Microsoft Azure App Service-Web-Apps mit dem PCI-Standard 3.0 und 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Wie verwende ich die Stagingumgebung und Bereitstellungsslots?

Sie können in den App Service-Plänen „Standard“ und „Premium“ die Bereitstellung Ihrer Web-App in App Service in einem separaten Bereitstellungsslot anstelle des Standardproduktionsslots vornehmen. Bereitstellungsslots sind Live-Web-Apps mit eigenen Hostnamen. Elemente für Web-App-Inhalte und -Konfigurationen können zwischen zwei Bereitstellungsslots, einschließlich des Produktionsslots, ausgetauscht werden.

Weitere Informationen zur Verwendung von Bereitstellungsslots finden Sie unter [Einrichten einer Stagingumgebung in App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Wie kann ich auf WebJob-Protokolle zugreifen und diese überprüfen?

So überprüfen Sie WebJob-Protokolle

1. Melden Sie sich bei Ihrer **Kudu-Website** (`https://*yourwebsitename*.scm.azurewebsites.net`) an.
2. Wählen Sie den WebJob aus.
3. Klicken Sie auf die Schaltfläche **Ausgabe umschalten**.
4. Klicken Sie zum Herunterladen der Ausgabedatei auf den Link **Herunterladen**.
5. Klicken Sie für einzelne Ausführungen auf **Individual Invoke** (Einzeln aufrufen).
6. Klicken Sie auf die Schaltfläche **Ausgabe umschalten**.
7. Klicken Sie auf den Link zum Herunterladen.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Ich versuche, Hybridverbindungen mit SQL Server zu verwenden. Warum wird die Meldung „System.OverflowException: Arithmetic operation resulted in an overflow“ (System.OverflowException: Die arithmetische Operation hat einen Überlauf verursacht) angezeigt?

Wenn Sie Hybridverbindungen verwenden, um auf SQL Server zuzugreifen, kann ein Update für Microsoft .NET vom 10. Mai 2016 zu Verbindungsfehlern führen. Ggf. wird diese Meldung angezeigt:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Lösung

Die Ausnahme wurde durch ein Problem beim Hybridverbindungs-Manager ausgelöst, das inzwischen behoben wurde. Achten Sie darauf, ein [Update Ihres Hybridverbindungs-Managers](https://go.microsoft.com/fwlink/?LinkID=841308) vorzunehmen, um dieses Problem zu beheben.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Wie kann ich eine URL-Rewrite-Regel hinzufügen?

Um eine URL-Rewrite-Regel (Regel zum erneuten Schreiben einer URL) hinzuzufügen, erstellen Sie eine „web.config“-Datei mit den entsprechenden Konfigurationseinträgen im Ordner **wwwroot**. Weitere Informationen finden Sie unter [Azure App Services: Understanding URL Re-write](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Wie steuere ich den in App Service eingehenden Datenverkehr?

Auf Websiteebene haben Sie zwei Optionen zum Steuern des in App Service eingehenden Datenverkehrs:

* Aktivieren Sie dynamische IP-Einschränkungen. Informationen zum Aktivieren dynamischer IP-Einschränkungen finden Sie unter [IP and Domain Restrictions for Windows Azure Web Sites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) (IP- und Domäneneinschränkungen für Azure-Websites).
* Aktivieren Sie Modulsicherheit. Informationen zum Aktivieren von Modulsicherheit finden Sie unter [ModSecurity web application firewall on Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/) (ModSecurity-Webanwendungs-Firewall für Azure-Websites).

Wenn Sie die App Service-Umgebung verwenden, können Sie die [Barracuda-Firewall](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/) nutzen.

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Wie blockiere ich Ports in einer App Service-Web-App?

In der gemeinsam genutzten Mandantenumgebung von App Service ist es aufgrund des Wesens der Infrastruktur nicht möglich, bestimmte Ports zu blockieren. Die TCP-Ports 4020, 4022 und 4024 sind möglicherweise zudem für das Remotedebuggen von Visual Studio geöffnet.

In der App Service-Umgebung haben Sie die vollständige Kontrolle über ein- und ausgehenden Datenverkehr. Sie können Netzwerksicherheitsgruppen einsetzen, um bestimmte Ports einzuschränken oder zu blockieren. Weitere Informationen zur App Service-Umgebung finden Sie unter [Einführung in die App Service-Umgebung](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Wie zeichne ich eine F12-Ablaufverfolgung auf?

Sie haben zwei Optionen zum Aufzeichnen einer F12-Ablaufverfolgung:

* F12-HTTP-Ablaufverfolgung
* F12-Konsolenausgabe

### <a name="f12-http-trace"></a>F12-HTTP-Ablaufverfolgung

1. Wechseln Sie in Internet Explorer zu Ihrer Website. Vor den nächsten Schritten müssen Sie sich anmelden. Andernfalls erfasst die F12-Ablaufverfolgung vertrauliche Anmeldedaten.
2. Drücken Sie F12.
3. Überprüfen Sie, ob die Registerkarte **Netzwerk** ausgewählt ist, und klicken Sie dann auf die grüne Schaltfläche **Wiedergeben**.
4. Führen Sie die Schritte zum Reproduzieren des Problems aus.
5. Klicken Sie auf die rote Schaltfläche **Beenden**.
6. Klicken Sie auf die Schaltfläche **Speichern** (Datenträgersymbol), und speichern Sie die HAR-Datei (in Internet Explorer und Microsoft Edge), *oder* klicken Sie mit der rechten Maustaste auf die HAR-Datei, und wählen Sie dann **Als HAR mit Inhalt speichern** (in Chrome) aus.

### <a name="f12-console-output"></a>F12-Konsolenausgabe

1. Klicken Sie auf die Registerkarte **Konsole**.
2. Für jede Registerkarte, die mehr als 0 Elemente enthält, klicken Sie auf die Registerkarte ( **Fehler** , **Warnung** oder **Informationen** ). Wenn die Registerkarte nicht ausgewählt ist, ist das Registerkartensymbol grau oder schwarz, sobald Sie den Cursor davon entfernen.
3. Klicken Sie im Meldungsabschnitt des Bereichs mit der rechten Maustaste, und wählen Sie dann **Alle kopieren** aus.
4. Fügen Sie den kopierten Text in eine Datei ein, und speichern Sie dann die Datei.

Eine HAR-Datei können Sie im [HAR Viewer](http://www.softwareishard.com/har/viewer/) anzeigen.

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Warum erhalte ich eine Fehlermeldung beim Versuch, eine App Service-Web-App mit einem virtuellen Netzwerk zu verbinden, das mit ExpressRoute verbunden ist?

Wenn Sie versuchen, eine Azure-Web-App mit einem virtuellen Netzwerk zu verbinden, das mit Azure ExpressRoute verbunden ist, tritt ein Fehler auf. Die folgende Meldung wird angezeigt: „Gateway ist kein VPN-Gateway.“

Derzeit sind keine Punkt-zu-Standort-VPN-Verbindungen mit einem virtuellen Netzwerk möglich, das mit ExpressRoute verbunden ist. Eine Punkt-zu-Standort-VPN- und eine ExpressRoute-Verbindung dürfen für ein virtuelles Netzwerk nicht gleichzeitig vorhanden sein. Weitere Informationen finden Sie unter [ExpressRoute- und Standort-zu-Standort-Verbindungen: Grenzwerte und Einschränkungen](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Wie verbinde ich eine App Service-Web-App mit einem virtuellen Netzwerk, das ein statisches (richtlinienbasiertes) Routinggateway hat?

Derzeit wird das Verbinden einer App Service-Web-App mit einem virtuellen Netzwerk, das ein statisches (richtlinienbasiertes) Routinggateway hat, nicht unterstützt. Wenn Ihr virtuelles Zielnetzwerk bereits vorhanden ist, muss es über ein Punkt-zu-Standort-VPN mit einem dynamischen Routinggateway aktiviert werden, bevor es mit einer App verbunden werden kann. Wenn Ihr Gateway auf statisches Routing festgelegt ist, kann ein Punkt-zu-Standort-VPN nicht aktiviert werden. 

Weitere Informationen finden Sie unter [Integrieren einer App in ein virtuelles Azure-Netzwerk](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Warum kann ich in meiner App Service-Umgebung nur einen App Service-Plan erstellen, obwohl zwei Worker verfügbar sind?

Um Fehlertoleranz zu gewährleisten, fordert die App Service-Umgebung an, dass für jeden Workerpool mindestens eine zusätzliche Computeressource vorhanden ist. Der zusätzlichen Computeressource kann keine Workload zugewiesen werden.

Weitere Informationen finden Sie unter [Erstellen einer App Service-Umgebung](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Warum treten Zeitüberschreitungen beim Versuch auf, eine App Service-Umgebung erstellen?

Das Erstellen einer App Service-Umgebung misslingt mitunter. In diesem Fall wird die folgende Fehlermeldung in den Aktivitätsprotokollen angezeigt:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Stellen Sie zum Beheben dieses Problems sicher, dass keine der folgenden Bedingungen vorliegt:
* Das Subnetz ist zu klein.
* Das Subnetz ist nicht leer.
* ExpressRoute verhindert das Erfüllen der Anforderungen an die Netzwerkverbindung einer App Service-Umgebung.
* Eine fehlerhafte Netzwerksicherheitsgruppe verhindert das Erfüllen der Anforderungen an die Netzwerkverbindung einer App Service-Umgebung.
* Erzwungenes Tunneln ist aktiviert.

Weitere Informationen finden Sie unter [Most frequent issues when deploying (creating) a new Azure App Service Environment (ASE)](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase) (Häufig auftretende Probleme beim Bereitstellen [oder Erstellen] einer neuen Azure App Service-Umgebung).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Warum kann ich meinen App Service-Plan nicht löschen?

Sie können einen App Service-Plan nicht löschen, solange ihm App Service-Apps zugeordnet sind. Bevor Sie einen App Service-Plan löschen, entfernen Sie alle zugeordneten App Service-Apps aus dem App Service-Plan.

## <a name="how-do-i-schedule-a-webjob"></a>Wie plane ich einen WebJob?

Sie können einen geplanten WebJob mithilfe von CRON-Ausdrücken erstellen:

1. Erstellen Sie die Datei „settings.job“.
2. Fügen Sie dieser JSON-Datei mithilfe eines Cron-Ausdrucks eine Zeitplaneigenschaft hinzu: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Weitere Informationen zu geplanten WebJobs finden Sie unter [Erstellen eines geplanten WebJobs mithilfe eines CRON-Ausdrucks](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Wir führe ich Penetrationstests für meine App Service-App aus?

Wenn Sie Penetrationstests ausführen möchten, [übermitteln Sie eine Anforderung](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Wie konfiguriere ich einen benutzerdefinierten Domänennamen für eine Azure App Service-Web-App, die Traffic Manager verwendet?

Informationen dazu, wie Sie einen benutzerdefinierten Domänennamen mit einer App Service-App nutzen, die Azure Traffic Manager für den Lastenausgleich verwendet, finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App in Azure App Services, die Traffic Manager verwendet](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mein App Service-Zertifikat ist mit einer Betrugsmeldung gekennzeichnet. Wie löse ich dieses Problem?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bei der Überprüfung der Domäne beim Kauf eines App Service-Zertifikats wird möglicherweise die folgende Meldung angezeigt:

„Ihr Zertifikat wurde als betrugsverdächtig gekennzeichnet. Die Anforderung wird aktuell geprüft. Wenn das Zertifikat nicht innerhalb von 24 Stunden verwendbar ist, wenden Sie sich an den Azure-Support.“

Wie die Meldung andeutet, kann dieser Überprüfungsvorgang auf Betrug bis zu 24 Stunden dauern. Während dieser Zeit wird die Meldung weiterhin angezeigt.

Wenn nach 24 Stunden diese Meldung immer noch für Ihr App Service-Zertifikat angezeigt wird, führen Sie das folgende PowerShell-Skript aus. Das Skript kontaktiert den [Zertifikatanbieter](https://www.godaddy.com/) direkt mit Bitte um Behebung des Problems.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Wie funktionieren Authentifizierung und Autorisierung in Azure App Service?

Eine ausführliche Dokumentation der Authentifizierung und Autorisierung in App Service finden Sie in der Dokumentation zu den verschiedenen Identitätsanbieteranmeldungen:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-Konto](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Wie leite ich die Standarddomäne „*.azurewebsites.net“ zur benutzerdefinierten Domäne meiner Azure-Web-App um?

Beim Erstellen einer neuen Website mithilfe von Web-Apps in Azure wird Ihrer Website standardmäßig die Domäne „ *Websitename*.azurewebsites.net“ zugewiesen. Wenn Sie Ihrer Website einen benutzerdefinierten Hostnamen hinzufügen und nicht möchten, dass Benutzer auf Ihre Standarddomäne „*.azurewebsites.net“ zugreifen, können Sie die Standard-URL umleiten. Informationen zum Umleiten des gesamten Datenverkehrs von der Standarddomäne Ihrer Website zu Ihrer benutzerdefinierten Domäne finden Sie unter [Redirect the default domain to your custom domain in Azure web apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/) (Umleiten der Standarddomäne zu Ihrer benutzerdefinierten Domäne in Azure-Web-Apps).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Wie erkenne ich, welche .NET-Version in App Service installiert ist?

Die schnellste Methode, die Version von Microsoft .NET zu bestimmen, die in App Service installiert ist, bietet die Kudu-Konsole. Auf die Kudu-Konsole können Sie im Portal oder über die URL Ihrer App Service-App zugreifen. Ausführliche Anweisungen finden Sie unter [How to determine the installed .NET version in Azure App Services](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services) (Bestimmen der installierten Version von .NET in App Service).

## <a name="why-isnt-autoscale-working-as-expected"></a>Warum funktioniert die automatische Skalierung nicht wie erwartet?

Wenn die automatische Skalierung in Azure die Web-App nicht wie erwartet horizontal hoch- oder herunterskaliert hat, landen Sie ggf. bei einem Szenario, bei dem wir absichtlich auf eine Skalierung verzichten, um eine unendliche Schleife aufgrund einer „Pendelsituation“ zu vermeiden. Dies geschieht normalerweise, wenn kein angemessener Spielraum zwischen dem Schwellenwert für horizontales Hoch- und Herunterskalieren vorhanden ist. Informationen zum Vermeiden von Pendelsituationen und zu anderen empfohlenen Methoden bei der automatischen Skalierung finden Sie unter [Empfohlene Methoden für die automatische Skalierung](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Warum erfolgt bei der automatischen Skalierung mitunter nur eine teilweise Skalierung?

Die automatische Skalierung wird ausgelöst, wenn Metriken vorkonfigurierte Grenzen überschreiten. Mitunter bemerken Sie, dass die Kapazität im Vergleich zu Ihren Erwartungen nur teilweise erfüllt wird. Dies kann auftreten, wenn die gewünschte Anzahl von Instanzen nicht verfügbar ist. In diesem Fall erfüllt die automatische Skalierung die Anforderungen mit der Anzahl der verfügbaren Instanzen nur teilweise. Die automatische Skalierung führt dann die Ausgleichslogik aus, um für weitere Kapazität zu sorgen. Dafür werden die verbleibenden Instanzen zugeordnet. Beachten Sie, dass dies einige Minuten dauern kann.

Wenn die erwartete Anzahl von Instanzen nach einigen Minuten nicht angezeigt wird, kann dies daran liegen, dass die teilweise Wiederauffüllung gereicht hat, um die Metriken wieder in die gewünschten Bahnen zu bringen. Oder die automatische Skalierung hat zentral herunterskaliert, da der untere Metrikgrenzwert erreicht wurde.

Wenn keine dieser Bedingungen zutrifft und das Problem bestehen bleibt, senden Sie eine Supportanfrage.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Wie aktiviere ich die HTTP-Komprimierung für meine Inhalte?

Fügen Sie zum Aktivieren der Komprimierung für statische und dynamische Inhaltstypen der Datei „web.config“ auf Anwendungsebene den folgenden Code hinzu:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Sie können auch die spezifischen dynamischen und statischen MIME-Typen angeben, die Sie komprimieren möchten. Weitere Informationen finden Sie in unserer Antwort auf eine Forumsfrage unter [httpCompression settings on a simple Azure website](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview) (httpCompression-Einstellungen auf einer einfachen Azure-Website).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Wie migriere ich von einer lokalen Umgebung zu App Service?

Um Websites von Windows- und Linux-Webservern zu App Service zu migrieren, können Sie den Azure App Service-Migrations-Assistenten verwenden. Das Migrationstool erstellt Web-Apps und Datenbanken in Azure nach Bedarf und veröffentlicht dann den Inhalt. Weitere Informationen finden Sie unter [Azure App Service-Migrations-Assistent](https://appmigration.microsoft.com/).
