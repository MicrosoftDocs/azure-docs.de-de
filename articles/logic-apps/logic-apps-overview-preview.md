---
title: Übersicht über die Vorschauversion von Azure Logic Apps
description: Azure Logic Apps (Vorschau) ist eine Cloudlösung für die Erstellung automatisierter zustandsbehafteter und zustandsloser Workflows, die Apps, Daten, Dienste und Systeme mit minimalem Code in Szenarien auf Unternehmensniveau integrieren.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 5db0214e9b985df5c5aedb1dbe9878e484af2a55
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430796"
---
# <a name="overview-azure-logic-apps-preview"></a>Übersicht: Vorschauversion von Azure Logic Apps

> [!IMPORTANT]
> Diese Funktion befindet sich in der öffentlichen Vorschauphase, wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit Azure Logic Apps (Vorschau) können Sie Automatisierungs- und Integrationslösungen für Apps, Daten, Clouddienste und Systeme erstellen, indem Sie Logik-Apps erstellen und ausführen, die [*zustandsbehaftete* und *zustandslose* Workflows](#stateful-stateless) integrieren, indem Sie den neuen Ressourcentyp **Logik-App (Vorschau)** verwenden. Mit diesem neuen Logik-App-Typ können Sie mehrere Workflows erstellen, die von der neu gestalteten Azure Logic Apps-Runtime (Vorschau) unterstützt werden. Diese Runtime bietet Portabilität, eine bessere Leistung und mehr Flexibilität bei der Bereitstellung und Ausführung in verschiedenen Hostingumgebungen – nicht nur in Azure, sondern auch in Docker-Containern.

Wie ist das möglich? Die neu gestaltete Runtime wendet das [Azure Functions-Erweiterbarkeitsmodell](../azure-functions/functions-bindings-register.md) an und wird als Erweiterung der Azure Functions-Runtime gehostet. Aufgrund dieser Architektur können Sie den neuen Logik-App-Typ überall dort ausführen können, wo Azure Functions ausgeführt werden kann. Sie können die neu gestaltete Runtime in nahezu allen Netzwerktopologien hosten und jede verfügbare Computegröße auswählen, um die für Ihre Workflows erforderliche Workload zu verarbeiten. Weitere Informationen finden Sie unter [Einführung in Azure Functions](../azure-functions/functions-overview.md) und [Azure Functions-Trigger und -Bindungen](../azure-functions/functions-triggers-bindings.md).

Für die Erstellung der Ressource **Logik-App (Vorschau)** können Sie das [Azure-Portal](create-stateful-stateless-workflows-azure-portal.md) verwenden, oder Sie [erstellen in Visual Studio Code ein Projekt mit der Azure Logic Apps-Erweiterung (Vorschau)](create-stateful-stateless-workflows-visual-studio-code.md). Außerdem können Sie in Visual Studio Code Ihre Workflows in Ihrer Entwicklungsumgebung erstellen und *lokal ausführen*. Unabhängig davon, ob Sie das Portal oder Visual Studio Code verwenden, können Sie den neuen Logik-App-Typ in denselben Hostingumgebungen bereitstellen und ausführen.

In dieser Übersicht geht es um die folgenden Bereiche:

* [Unterschiede zwischen Azure Logic Apps (Vorschau), der Azure Logic Apps-Umgebung mit mehreren Mandanten und der Integrationsdienstumgebung](#preview-differences)

* [Unterschiede zwischen zustandsbehafteten und zustandslosen Workflows](#stateful-stateless), einschließlich abweichender Verhaltensmuster zwischen [geschachtelten zustandsbehafteten und zustandslosen Workflows](#nested-behavior)

* [Funktionen dieser öffentlichen Vorschauversion](#public-preview-contents)

* [Das Preismodell](#pricing-model)

* [Geänderte, eingeschränkte, nicht verfügbare oder nicht unterstützte Funktionen](#limited-unavailable-unsupported)

* [Grenzwerte der Vorschauversion von Azure Logic Apps](#limits)

Weiterführende Informationen finden Sie in den folgenden Artikeln:

* [Azure Logic Apps Running Anywhere - Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564) (Azure Logic Apps läuft überall – Runtimeanalyse)

* [Logic Apps Public Preview Known Issues](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) (Bekannte Probleme der öffentlichen Vorschauversion von Logic Apps; GitHub)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Worin unterscheidet sich die Vorschauversion von Azure Logic Apps?

Die Azure Logic Apps-Runtime (Vorschau) wendet das Erweiterbarkeitsmodell von [Azure Functions](../azure-functions/functions-overview.md) an und wird als Erweiterung der Azure Functions-Runtime gehostet. Aufgrund dieser Architektur können Sie den neuen Logik-App-Typ überall dort ausführen können, wo Azure Functions ausgeführt werden kann. Sie können die Azure Logic Apps-Runtime (Vorschau) in jeder gewünschten Netzwerktopologie hosten und jede verfügbare Computegröße auswählen, um die für Ihre Workflows erforderliche Workload zu verarbeiten. Weitere Informationen zur Azure Functions-Erweiterbarkeit finden Sie unter [WebJobs SDK: Creating custom input and output bindings](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings) (WebJobs SDK: Erstellen benutzerdefinierter Eingabe- und Ausgabebindungen).

Dank dieses neuen Ansatzes sind die Azure Logic Apps-Runtime (Vorschau) und Ihre Workflows Bestandteil Ihrer App und können zusammen gepackt werden. So können Sie Ihre Workflows bereitstellen und ausführen, indem Sie einfach Artefakte in die Hostingumgebung kopieren und die App starten. Dieser Ansatz bietet auch eine stärker standardisierte Umgebung für die Erstellung von DevOps-Pipelines für Workflowprojekte, mit denen Sie die erforderlichen Tests und Überprüfungen ausführen können, bevor Sie Änderungen in Produktionsumgebungen bereitstellen. Weitere Informationen finden Sie unter [Azure Logic Apps Running Anywhere - Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564) (Azure Logic Apps läuft überall – Runtimeanalyse).

In der folgenden Tabelle werden die Unterschiede bei der gemeinsamen Verwendung von Ressourcen durch Workflows in Bezug auf die Umgebung, in der sie ausgeführt werden, kurz zusammengefasst. Die Unterschiede bei den Grenzwerten finden Sie unter [Grenzwerte der Vorschauversion von Azure Logic Apps](#limits).

| Umgebung | Ressourcenfreigabe und -verbrauch |
|-------------|----------------------------------|
| Azure Logic Apps (mehrere Mandanten) | Workflows *von Kunden in mehreren Mandanten* nutzen dieselben Ressourcen für die Verarbeitung (Compute), den Speicher, das Netzwerk usw. |
| Azure Logic Apps (Vorschau) | Workflows *in derselben Logik-App* nutzen dieselben Ressourcen für die Verarbeitung (Compute), den Speicher, das Netzwerk usw. |
| Integrationsdienstumgebung (in der Vorschau nicht verfügbar) | Workflows *in derselben Umgebung* nutzen dieselben Ressourcen für die Verarbeitung (Compute), den Speicher, das Netzwerk usw. |
||||

Derzeit können Sie auch weiterhin noch den ursprünglichen Logik-App-Typ im Azure-Portal und in Visual Studio Code erstellen, indem Sie die ursprüngliche Azure Logic Apps-Erweiterung verwenden. Trotz der Unterschiede bei der Entwicklung zwischen dem ursprünglichen und dem neuen Logik-App-Typ kann Ihr Azure-Abonnement beide Typen enthalten. Sie können alle bereitgestellten Logik-Apps in Ihrem Azure-Abonnement anzeigen und darauf zugreifen, aber die Apps werden getrennt in ihren eigenen Kategorien und Abschnitten angeordnet.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Zustandsbehaftete und zustandslose Workflows

* *Zustandsbehaftet*

  Erstellen Sie zustandsbehaftete Workflows, wenn Sie Daten von vorherigen Ereignissen beibehalten, überprüfen oder referenzieren müssen. Diese Workflows behalten die Ein- und Ausgabe für jede Aktion und deren Zustände im externen Speicher bei, sodass die Ausführungsdetails und der Ausführungsverlauf nach Abschluss der jeweiligen Ausführung überprüft werden können. Zustandsbehaftete Workflows bieten hohe Resilienz, falls es zu Ausfällen kommen sollte. Nachdem Dienste und Systeme wiederhergestellt wurden, können Sie unterbrochene Ausführungen aus dem gespeicherten Zustand rekonstruieren und die Workflows bis zum Abschluss erneut ausführen. Die Ausführung zustandsbehafteter Workflows kann bis zu ein Jahr lang fortgesetzt werden.

* *Zustandslos*

  Erstellen Sie zustandslose Workflows, wenn Sie Daten von vorherigen Ereignissen nicht zur späteren Überprüfung in externem Speicher speichern, überprüfen oder referenzieren müssen. Diese Workflows speichern die Ein- und Ausgabe für jede Aktion und deren Zustände *nur im Arbeitsspeicher*, anstatt diese Daten in den externen Speicher zu übertragen. Daher sind die Ausführungen zustandsloser Workflows kürzer (in der Regel nicht länger als 5 Minuten), sie bieten eine höhere Leistung mit schnelleren Antwortzeiten, einen höheren Durchsatz und niedrigere Ausführungskosten, da die Ausführungsdetails und der Ausführungsverlauf nicht im externen Speicher aufbewahrt werden. Wenn es aber zu Ausfällen kommen sollte, werden unterbrochene Ausführungen nicht automatisch wiederhergestellt, sodass der Aufrufer unterbrochene Ausführungen manuell erneut übermitteln muss. Diese Workflows können nur synchron ausgeführt werden.

  Um das Debuggen zu vereinfachen, können Sie den Ausführungsverlauf für einen zustandslosen Workflow aktivieren und den Ausführungsverlauf wieder deaktivieren, wenn Sie fertig sind, da die Aktivierung die Leistung beeinträchtigt. Weitere Informationen finden Sie unter [Erstellen zustandsbehafteter oder zustandsloser Workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) oder [Erstellen zustandsbehafteter oder zustandsloser Workflows im Azure-Portal](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Zustandslose Workflows unterstützen derzeit nur *Aktionen* für in Azure bereitgestellte [verwaltete Connectors](../connectors/apis-list.md#managed-api-connectors), keine Aktionen für Trigger. Um Ihren Workflow zu starten, wählen Sie einen der [Trigger für integrierte Anforderungen, Event Hubs oder Service Bus](../connectors/apis-list.md#built-ins) aus. Diese Trigger werden nativ in der Azure Logic Apps-Runtime (Vorschau) ausgeführt. Weitere Informationen zu eingeschränkten, nicht verfügbaren oder nicht unterstützten Triggern, Aktionen und Connectors finden Sie unter [Geänderte, eingeschränkte, nicht verfügbare oder nicht unterstützte Funktionen](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Unterschiede im Verhalten von geschachtelten zustandsbehafteten und geschachtelten zustandslosen Workflows

Sie können einen [Workflow aus anderen Workflows heraus aufrufbar machen](../logic-apps/logic-apps-http-endpoint.md), die in derselben **Logik-App-Ressource (Vorschau)** enthalten sind, indem Sie den [Anforderungstrigger](../connectors/connectors-native-reqres.md), den [HTTP-Webhooktrigger](../connectors/connectors-native-webhook.md) oder den Trigger für verwaltete Connectors verwenden, die vom [Typ ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) sind und HTTPS-Anforderungen empfangen können.

Im Folgenden sind die Verhaltensmuster geschachtelter Workflows aufgeführt, nachdem ein übergeordneter Workflow einen untergeordneten Workflow aufruft:

* Asynchrones Abrufmuster

  Der übergeordnete Workflow wartet nicht auf eine Antwort auf seinen ursprünglichen Aufruf, sondern überprüft fortlaufend den Ausführungsverlauf Verlauf des untergeordneten Workflows, bis dessen Ausführung beendet wird. Standardmäßig halten zustandsbehaftete Workflows dieses Muster ein, das sich ideal für untergeordnete Workflows mit langer Laufzeit eignet, die [Anforderungstimeout-Limits](../logic-apps/logic-apps-limits-and-config.md) überschreiten können.

* Synchrones Muster („Fire and Forget“)

  Der untergeordnete Workflow bestätigt den Aufruf durch sofortige Rückgabe einer `202 ACCEPTED`-Antwort, und der übergeordnete Workflow fährt mit der nächsten Aktion fort, ohne auf die Ergebnisse des untergeordneten Workflows zu warten. Stattdessen empfängt der übergeordnete Workflow die Ergebnisse, wenn der untergeordnete Workflow abgeschlossen ist. Untergeordnete zustandsbehaftete Workflows, die keine Antwortaktion enthalten, folgen immer dem synchronen Muster. Für untergeordnete zustandsbehaftete Workflows steht Ihnen der Ausführungsverlauf zur Prüfung zur Verfügung.

  Um dieses Verhalten zu aktivieren, legen Sie in der JSON-Definition des Workflows die `operationOptions`-Eigenschaft auf `DisableAsyncPattern` fest. Weitere Informationen finden Sie unter [Trigger- und Aktionstypen: Optionen für Vorgänge](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Auslösen und Warten

  Bei einem untergeordneten zustandslosen Workflow wartet der übergeordnete Workflow auf eine Antwort, mit der die Ergebnisse vom untergeordneten Workflow zurückgegeben werden. Dieses Muster funktioniert ähnlich wie die Verwendung des integrierten [HTTP-Triggers oder der HTTP-Aktion](../connectors/connectors-native-http.md), um einen untergeordneten Workflow aufzurufen. Untergeordnete zustandslose Workflows, die keine Antwortaktion enthalten, geben sofort eine `202 ACCEPTED`-Antwort zurück, aber der übergeordnete Workflow wartet, bis der untergeordnete Workflow abgeschlossen ist, bevor er mit der nächsten Aktion fortfährt. Diese Verhalten gelten nur für untergeordnete zustandslose Workflows.

Diese Tabelle gibt das Verhalten des untergeordneten Workflows an, je nachdem, ob der übergeordnete und untergeordnete Workflow zustandsbehaftete, zustandslose oder gemischte Workflowtypen sind:

| Übergeordneter Workflow | Untergeordneter Workflow | Untergeordnetes Verhalten |
|-----------------|----------------|----------------|
| Zustandsbehaftet | Zustandsbehaftet | Asynchron oder synchron mit `"operationOptions": "DisableAsyncPattern"`-Einstellung |
| Zustandsbehaftet | Zustandslos | Auslösen und Warten |
| Zustandslos | Zustandsbehaftet | Synchron |
| Zustandslos | Zustandslos | Auslösen und Warten |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Funktionen

Azure Logic Apps (Vorschau) umfasst viele aktuelle und zusätzliche Funktionen, z. B.:

* Erstellen von Logik-Apps für Workflows mit [mehr als 390 Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) für SaaS- (Software-as-a-Service) und PaaS-Apps (Platform as a Service) und -Dienste sowie Connectors für lokale Systeme

  * Einige verwaltete Connectors, z. B. für Azure Service Bus, Azure Event Hubs und SQL Server, werden ähnlich wie die integrierten Trigger und Aktionen ausgeführt, die in die Azure Logic Apps-Runtime (Vorschau) integriert sind, wie z. B. der Anforderungstrigger und die HTTP-Aktion. Weitere Informationen finden Sie unter [Azure Logic Apps Running Anywhere: Built-in connector extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) (Azure Logic Apps läuft überall: Erweiterbarkeit integrierter Connectors).

  * Sie können die B2B-Aktionen für Liquid- und XML-Vorgänge ohne Integrationskonto verwenden. Um diese Aktionen zu verwenden, benötigen Sie Liquid-Zuordnungen, XML-Zuordnungen oder XML-Schemas, die Sie über die entsprechenden Aktionen im Azure-Portal hochladen können. Sie können sie auch mithilfe der entsprechenden Ordner für **Zuordnungen** und **Schemas** im Ordner für **Artefakte** des Visual Studio Code-Projekts hinzufügen.

  * Erstellen und Bereitstellen von Logik-Apps, die überall ausgeführt werden können, weil der Azure Logic Apps-Dienst SAS-Verbindungszeichenfolgen (Shared Access Signature) generiert, die diese Logik-Apps zum Senden von Anforderungen an den Runtime-Endpunkt der Cloudverbindung verwenden können. Der Logic Apps-Dienst speichert diese Verbindungszeichenfolgen zusammen mit anderen Anwendungseinstellungen, sodass Sie diese Werte bei einer Bereitstellung in Azure problemlos in Azure Key Vault speichern können.

    > [!NOTE]
    > Standardmäßig wird die [systemseitig zugewiesene verwaltete Identität](../logic-apps/create-managed-service-identity.md) einer **Logik-App**-Ressource (Vorschau) automatisch aktiviert, um Verbindungen zur Laufzeit zu authentifizieren. Diese Identität unterscheidet sich von den Anmeldeinformationen für die Authentifizierung oder der Verbindungszeichenfolge, die Sie verwenden, wenn Sie eine Verbindung herstellen. Wenn Sie diese Identität deaktivieren, funktionieren Verbindungen zur Laufzeit nicht. Wählen Sie zum Anzeigen dieser Einstellung im Menü Ihrer Logik-App unter **Einstellungen** die Option **Identität** aus.

* Erstellen Sie Logik-Apps mit zustandslosen Workflows, die nur im Arbeitsspeicher ausgeführt werden, damit sie schneller beendet werden, schneller reagieren, einen höheren Durchsatz aufweisen und sich kostengünstiger ausführen lassen, da die Ausführungsverläufe und -daten zwischen Aktionen nicht dauerhaft in externem Speicher gespeichert werden. Optional können Sie den Ausführungsverlauf für leichteres Debuggen aktivieren. Weitere Informationen finden Sie unter [Zustandsbehaftete Logik-Apps im Vergleich zu zustandslosen Logik-Apps](#stateful-stateless).

* Nutzen Sie die Visual Studio Code-Entwicklungsumgebung, um Ihre Logik-Apps und die zugehörigen Workflows lokal auszuführen, zu testen und zu debuggen.

  Bevor Sie Ihre Logik-App ausführen und testen, können Sie das Debuggen vereinfachen, indem Sie in der Datei **workflow.json** für einen Workflow Breakpoints hinzufügen und verwenden. Breakpoints werden derzeit jedoch nur für Aktionen unterstützt, nicht für Trigger. Weitere Informationen finden Sie unter [Erstellen zustandsbehafteter und zustandsloser Workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Veröffentlichen Sie Ihre Logik-Apps und die zugehörigen Workflows aus Visual Studio Code, oder stellen Sie sie direkt in verschiedenen Hostumgebungen bereit, z. B. in Azure und in [Docker-Containern](/dotnet/core/docker/introduction).

* Aktivieren Sie die Funktionen zur Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App, indem Sie [Application Insights](../azure-monitor/app/app-insights-overview.md) verwenden, sofern dies von Ihrem Azure-Abonnement und den Einstellungen der Logik-App unterstützt wird.

* Generieren Sie Zugriffsschlüssel für verwaltete Verbindungen erneut, die von einzelnen Workflows in einer **Logik-App (Vorschau)** -Ressource verwendet werden. [Führen Sie die gleichen Schritte wie für die **Logic Apps**-Ressource, aber auf der individuellen Workflowebene](logic-apps-securing-a-logic-app.md#regenerate-access-keys), nicht auf der Logik-App-Ressourcenebene, für diese Aufgabe aus.

> [!NOTE]
> Informationen zu aktuellen bekannten Problemen finden Sie in GitHub unter [Logic Apps Public Preview Known Issues](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) (Bekannte Probleme der öffentlichen Vorschauversion von Logic Apps).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Preismodell

Wenn Sie den neuen Logik-App-Typ im Azure-Portal erstellen oder aus Visual Studio Code bereitstellen, müssen Sie als Hostingplan entweder [App Service oder Premium](../azure-functions/functions-scale.md) auswählen, damit Ihre Logik-App verwendet werden kann. Dieser Plan bestimmt das Preismodell für die Ausführung Ihrer Logik-App. Wenn Sie den App Service-Plan auswählen, müssen Sie außerdem einen [Tarif](../app-service/overview-hosting-plans.md) auswählen.

*Zustandsbehaftete* Workflows verwenden [externen Speicher](../azure-functions/storage-considerations.md#storage-account-requirements), sodass das [Azure Storage-Preismodell](https://azure.microsoft.com/pricing/details/storage/) auf Speichertransaktionen angewandt wird, die von der Azure Logic Apps-Runtime (Vorschau) ausgeführt werden. Beispielsweise werden Warteschlangen für die Zeitplanung verwendet, während Tabellen und Blobs zum Speichern von Workflowzuständen verwendet werden.

> [!NOTE]
> Während der öffentlichen Vorschauphase entstehen durch das Ausführen von Logik-Apps in App Service keine *zusätzlichen* Gebühren zum ausgewählten Plan.

Weitere Informationen zu den Preismodellen, die für diesen neuen Ressourcentyp gelten, finden Sie in den folgenden Themen:

* [Skalierung und Hosting von Azure Functions](../azure-functions/functions-scale.md)
* [Hochskalieren einer App in Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions – Preisdetails](https://azure.microsoft.com/pricing/details/functions/)
* [App Service-Preisdetails](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure Storage – Preisdetails](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Geänderte, eingeschränkte, nicht verfügbare oder nicht unterstützte Funktionen

Die folgenden Funktionen wurden in der Vorschauversion von Azure Logic Apps geändert, sind zurzeit eingeschränkt oder nicht verfügbar oder werden nicht unterstützt:

* **Betriebssystemunterstützung:** Derzeit funktioniert der Designer in Visual Studio Code nicht mit einem Linux-Betriebssystem. Sie können jedoch weiterhin Logik-Apps bereitstellen, die die Vorschauversion der Logic Apps-Runtime für Linux-VMs verwenden. Zum jetzigen Zeitpunkt können Sie Ihre Logik-Apps in Visual Studio Code unter Windows oder macOS erstellen und dann auf einer Linux-basierten VM bereitstellen.

* **Trigger und Aktionen:** Einige integrierte Trigger, wie z. B. gleitende Fenster und Batches, sind nicht verfügbar. Um Ihren Workflow zu starten, verwenden Sie den [integrierten Serien-, Anforderungs-, HTTP-, HTTP-Webhook-, Event Hubs- oder Service Bus-Trigger](../connectors/apis-list.md). Integrierte Trigger und Aktionen werden nativ in der Azure Logic Apps-Runtime (Vorschau) ausgeführt, während verwaltete Connectors in Azure bereitgestellt werden. Im Designer werden die integrierten Trigger und Aktionen auf der Registerkarte **Integriert** angezeigt, während die Trigger und Aktionen der verwalteten Connectors auf der Registerkarte **Azure** angezeigt werden.

  > [!NOTE]
  > Damit webhookbasierte Trigger und Aktionen lokal in Visual Studio Code ausgeführt werden können, sind zusätzliche Einrichtungsschritte erforderlich. Weitere Informationen finden Sie unter [Erstellen zustandsbehafteter und zustandsloser Workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * Bei *zustandslosen Workflows* wird die Registerkarte **Azure** nicht angezeigt, wenn Sie einen Trigger auswählen, da Sie nur [*Aktionen* verwalteter Connectors, aber keine Trigger](../connectors/apis-list.md#managed-api-connectors) auswählen können. Obwohl Sie verwaltete Connectors, die in Azure bereitgestellt wurden, für zustandslose Workflows aktivieren können, zeigt der Designer keine Trigger für verwaltete Connectors an, die Sie auswählen können.

  * Bei *zustandsbehafteten Workflows* können Sie sowohl [Trigger als auch Aktionen verwalteter Connectors](../connectors/apis-list.md#managed-api-connectors) verwenden. Ausnahmen bilden die Trigger und Aktionen, die unten als nicht verfügbar aufgeführt sind.

  * Diese Trigger und Aktionen wurden entweder geändert, sind zurzeit eingeschränkt oder nicht verfügbar oder werden nicht unterstützt:

    * [Lokale Datengateway *trigger*](../connectors/apis-list.md#on-premises-connectors) sind nicht verfügbar, aber Gatewayaktionen *sind* verfügbar.

    * [Benutzerdefinierte Connectors](../connectors/apis-list.md#custom-apis-and-connectors) sind nicht verfügbar.

    * Die integrierte Aktion [Azure Functions: Azure-Funktion auswählen](logic-apps-azure-functions.md) heißt nun **Azure Functions-Vorgänge: Azure-Funktion aufrufen**. Diese Aktion funktioniert derzeit nur für Funktionen, die über die Vorlage für **HTTP-Trigger** erstellt werden.

      Sie können im Azure-Portal eine HTTP-Triggerfunktion auswählen, auf die Sie Zugriff haben, indem Sie eine Verbindung über die Benutzeroberfläche erstellen. Wenn Sie die JSON-Definition der Funktionsaktion in der Codeansicht oder der Datei **workflow.json** überprüfen, verweist die Aktion über einen `connectionName`-Verweis auf die Funktion. Diese Version abstrahiert die Informationen der Funktion als Verbindung, die Sie in der Datei **connections.json** Ihres Projekts finden, die nach dem Erstellen einer Verbindung verfügbar ist.

      > [!NOTE]
      > In der Vorschauversion unterstützen Funktionsaktionen nur die Authentifizierung über Abfragezeichenfolgen. Azure Logic Apps (Vorschau) ruft den Standardschlüssel beim Herstellen der Verbindung aus der Funktion ab, speichert ihn in den Einstellungen Ihrer App und verwendet ihn beim Aufrufen der Funktion für die Authentifizierung.
      >
      > Wie in der ursprünglichen Version gilt auch hier: Wenn Sie diesen Schlüssel erneuern (z. B. über die Azure Functions-Benutzeroberfläche im Portal), funktioniert die Funktionsaktion aufgrund eines ungültigen Schlüssels nicht mehr. Um dieses Problem zu beheben, müssen Sie die Verbindung mit der Funktion, die Sie aufrufen möchten, neu erstellen oder die App-Einstellungen mit dem neuen Schlüssel aktualisieren.

    * Die integrierte Aktion [Inlinecode: JavaScript-Code ausführen](logic-apps-add-run-inline-code.md) heißt nun **Inlinecodevorgänge: JavaScript inline ausführen**.

      * Für Aktionen in Inlinecodevorgängen ist kein Integrationskonto mehr erforderlich.

      * Unter macOS oder Linux sind **Inlinecodevorgänge** derzeit nicht verfügbar, wenn Sie die Azure Logic Apps-Erweiterung (Vorschau) in Visual Studio Code verwenden.

      * Wenn Sie Änderungen an einer Aktion in Inlinecodevorgängen vornehmen, müssen Sie die Logik-App neu starten.

      * Die Grenzwerte für Aktionen in Inlinecodevorgängen wurden [aktualisiert](logic-apps-overview-preview.md#inline-code-limits).

    * Einige [integrierte B2B-Trigger und -Aktionen für Integrationskonten](../connectors/apis-list.md#integration-account-connectors) sind nicht verfügbar. Dies betrifft z. B. die Aktionen für die Codierung und Decodierung von **Flatfiles**.

    * Die integrierte Aktion [Azure Logic Apps: Logik-App-Workflow auswählen](logic-apps-http-endpoint.md) heißt nun **Workflowvorgänge: Workflow in dieser Workflow-App aufrufen**.

* **Verfügbarkeit des Hostingplans:** Unabhängig davon, ob Sie den neuen Ressourcentyp **Logik-App (Vorschau)** im Azure-Portal erstellen oder in Visual Studio Code bereitstellen, benötigen Sie einen Premium- oder App Service-Hostingplan in Azure. Hostingpläne nach Verbrauch sind nicht verfügbar und werden zur Bereitstellung dieses Ressourcentyps nicht unterstützt. Sie können eine Bereitstellung aus Visual Studio Code in einem Docker-Container, jedoch nicht in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) durchführen.

* **Parallele Branches:** Derzeit können Sie über die neue Designer-Benutzeroberfläche keine parallelen Branches hinzufügen. Sie können Sie diese Branches allerdings weiterhin über die ursprüngliche Designer-Benutzeroberfläche hinzufügen und dann im neuen Designer anzeigen lassen.

  1. Deaktivieren Sie unten im Designer die neue Benutzeroberfläche, indem Sie das Steuerelement **Neue Canvas** auswählen.

  1. Fügen Sie dem Workflow die parallelen Branches hinzu.

  1. Aktivieren Sie die neue Benutzeroberfläche, indem Sie das Steuerelement **Neue Canvas** erneut auswählen.

* **Zoomsteuerelement:** Das Zoomsteuerelement ist im Designer derzeit nicht verfügbar.

* **Breakpoints für das Debuggen in Visual Studio Code:** Sie können zwar in der Datei **workflow.json** Breakpoints für einen Workflow hinzufügen und verwenden, aber diese Breakpoints werden derzeit nur für Aktionen unterstützt, nicht für Trigger. Weitere Informationen finden Sie unter [Erstellen zustandsbehafteter und zustandsloser Workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

<a name="limits"></a>

## <a name="updated-limits"></a>Aktualisierte Grenzwerte

Auch wenn viele Grenzwerte bei der Vorschauversion von Azure Logic Apps mit den [Grenzwerten für Azure Logic Apps mit mehreren Mandanten ](logic-apps-limits-and-config.md) identisch sind, wurden einige Grenzwerte geändert.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Grenzwerte für HTTP-Timeouts

Bei einem einzelnen ein- oder ausgehenden Aufruf beträgt der Timeoutgrenzwert für diese Trigger und Aktionen 230 Sekunden (3,9 Minuten):

* Ausgehende Anforderung: HTTP-Trigger, HTTP-Aktion
* Eingehende Anforderung: Anforderungstrigger, HTTP-Webhooktrigger, HTTP-Webhookaktion

Zum Vergleich finden Sie hier die Timeoutgrenzwerte für diese Trigger und Aktionen in anderen Umgebungen, in denen Logik-Apps und die zugehörigen Workflows ausgeführt werden:

* Azure Logic Apps mit mehreren Mandanten: 120 Sekunden (2 Minuten)
* Integrationsdienstumgebung: 240 Sekunden (4 Minuten)

Weitere Informationen finden Sie unter [HTTP-Grenzwerte](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Verwaltete Connectors

Verwaltete Connectors sind auf 50 Anforderungen pro Minute und Verbindung begrenzt. Informationen zur Problembehandlung bei der Connectordrosselung finden Sie unter [Behandeln von Drosselungsproblemen (Fehler 429: „Zu viele Anforderungen“) in Azure Logic Apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Inlinecodevorgänge (JavaScript-Code ausführen)

Bei einer einzelnen Logik-App-Definition gelten für die Aktion [**JavaScript-Code ausführen**](logic-apps-add-run-inline-code.md) in Inlinecodevorgängen folgende neue Grenzwerte:

* Die maximale Anzahl von Codezeichen erhöht sich von 1.024 Zeichen auf 100.000 Zeichen.

* Die maximale Dauer für die Codeausführung wird von fünf Sekunden auf 15 Sekunden erhöht.

Weitere Informationen finden Sie in den [Definitionslimits für Logik-Apps](logic-apps-limits-and-config.md#definition-limits).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen zustandsbehafteter und zustandsloser Workflows im Azure-Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Erstellen zustandsbehafteter und zustandsloser Workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps Public Preview Known Issues](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) (Bekannte Probleme der öffentlichen Vorschauversion von Logic Apps; GitHub)

Teilen Sie uns bitte auch Ihre Erfahrungen mit der Vorschauversion von Azure Logic Apps mit!

* Bei Fehlern oder Problemen [erstellen Sie Ihre Probleme in GitHub](https://github.com/Azure/logicapps/issues).
* Bei Fragen, Anforderungen, Kommentaren und anderem Feedback [verwenden Sie dieses Feedbackformular](https://aka.ms/lafeedback).
