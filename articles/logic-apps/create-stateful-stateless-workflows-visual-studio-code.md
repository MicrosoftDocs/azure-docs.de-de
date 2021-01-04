---
title: Erstellen von Automatisierungsworkflows (Vorschau) in Visual Studio Code
description: Erstellen von zustandslosen oder zustandsbehafteten Automatisierungsworkflows mit der Azure Logic Apps (Vorschau)-Erweiterung in Visual Studio Code, um Apps, Daten, Clouddienste und lokale Systeme zu integrieren
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 14809cb28870e88cfa584c4f02360d50beabf901
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981040"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Erstellen zustandsbehafteter oder zustandsloser Workflows in Visual Studio Code mit der Azure Logic Apps (Vorschau)-Erweiterung

> [!IMPORTANT]
> Diese Funktion befindet sich in der öffentlichen Vorschauphase, wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zum Erstellen von Logik-App-Workflows, die über Apps, Daten, Clouddienste und Systeme hinweg integriert werden können, können Sie mit Visual Studio Code und der Azure Logic Apps-Erweiterung (Vorschau) [*zustandsbehaftete* und *zustandslose* Logik-App-Workflows](#stateful-stateless) lokal in Ihrer Entwicklungsumgebung erstellen und ausführen.

![Screenshot, der Visual Studio Code und einen Logik-App-Workflow zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Die Logik-Apps, die Sie mit der Erweiterung der öffentlichen Vorschau erstellen, verwenden den neuen **Logik-App**-Ressourcentyp (Vorschau) und werden in Ihrer lokalen Umgebung von der [Azure Functions](../azure-functions/functions-overview.md)-Runtime unterstützt. Dieser neue Ressourcentyp kann mehrere Workflows enthalten und ähnelt in gewisser Weise dem **Funktions-App**-Ressourcentyp, der mehrere Funktionen enthalten kann.

In der Zwischenzeit gibt es weiterhin den ursprünglichen **Logic Apps**-Ressourcentyp, mit dem Sie in Visual Studio Code und im Azure-Portal erstellen und diesen dort verwenden können. Die Erfahrungen für den ursprünglichen Ressourcentyp sind jedoch vom neuen Ressourcentyp getrennt. Zurzeit können sowohl **Logic Apps**- als auch **Logik-App (Vorschau)** -Ressourcentypen gleichzeitig in Visual Studio Code und im Azure-Portal vorhanden sein. Sie können alle bereitgestellten Logik-Apps in Ihrem Azure-Abonnement anzeigen und darauf zugreifen, aber sie werden getrennt in ihren eigenen Kategorien und Abschnitten angezeigt und aufbewahrt.

Dieser Artikel bietet eine allgemeine [Übersicht über diese öffentliche Vorschau](#whats-new), es werden verschiedene Aspekte des **Logik-App (Vorschau)** -Ressourcentyps und die Vorgehensweise zum Erstellen dieser Ressource mithilfe von Visual Studio Code beschrieben:

* Worin sich [zustandsbehaftete und zustandslose](#stateful-stateless) Logik-Apps voneinander unterscheiden.

* Wie Sie die [Einrichtungsanforderungen](#prerequisites) erfüllen und [Visual Studio Code für die Erweiterung der öffentlichen Vorschau einrichten](#set-up).

* Wie Sie neue **Logik-App (Vorschau)** -Workflows erstellen, indem Sie [ein Projekt erstellen und eine Workflowvorlage auswählen](#create-project).

* Wie Sie Ihre neuen Logik-Apps in Visual Studio Code lokal ausführen und debuggen.

* Wie Sie diese neuen Logik-Apps direkt aus Visual Studio Code [in Azure](#publish-azure) oder [in einem Docker-Container](#deploy-docker) veröffentlichen, den Sie überall ausführen können. Weitere Informationen zu Docker finden Sie unter [Was ist Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined).

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Inhalt dieser öffentlichen Vorschauversion

Die Azure Logic Apps (Vorschau)-Erweiterung bietet viele aktuelle und zusätzliche Logic Apps-Funktionen für Ihre lokale Entwicklungserfahrung in Visual Studio Code, z. B.:

* Erstellen von Logik-Apps für Integrations- und Automatisierungsworkflows von [390 und mehr Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) für SaaS-Apps (Software-as-a-Service)- und PaaS (Platform-as-a-Service)-Apps und -Dienste, zuzüglich Connectors für lokale Systeme.

  * Einige verwaltete Connectors wie Azure Service Bus, Azure Event Hubs und SQL Server werden ähnlich wie integrierte native Trigger und Aktionen wie die HTTP-Aktion ausgeführt.

  * Erstellen und Bereitstellen von Logik-Apps, die überall ausgeführt werden können, weil der Azure Logic Apps-Dienst SAS-Verbindungszeichenfolgen (Shared Access Signature) generiert, die diese Logik-Apps zum Senden von Anforderungen an den Runtime-Endpunkt der Cloudverbindung verwenden können. Der Logic Apps-Dienst speichert diese Verbindungszeichenfolgen mit anderen Anwendungseinstellungen, sodass Sie diese Werte problemlos in Azure Key Vault speichern können, wenn Sie in Azure bereitstellen.

    > [!NOTE]
    > Standardmäßig wird die [systemseitig zugewiesene verwaltete Identität](../logic-apps/create-managed-service-identity.md) einer **Logik-App**-Ressource (Vorschau) automatisch aktiviert, um Verbindungen zur Laufzeit zu authentifizieren. Diese Identität unterscheidet sich von den Anmeldeinformationen für die Authentifizierung oder der Verbindungszeichenfolge, die Sie verwenden, wenn Sie eine Verbindung herstellen. Wenn Sie diese Identität deaktivieren, funktionieren Verbindungen zur Laufzeit nicht.

* Erstellen Sie zustandslose Logik-Apps, die nur im Arbeitsspeicher ausgeführt werden, damit Sie schneller beendet werden, schneller reagieren, einen höheren Durchsatz aufweisen und sich kostengünstiger ausführen lassen, weil die Ausführungsverläufe und -daten zwischen Aktionen nicht dauerhaft in externem Speicher gespeichert werden. Optional können Sie den Ausführungsverlauf für leichteres Debuggen aktivieren. Weitere Informationen finden Sie unter [Zustandsbehaftete Logik-Apps im Vergleich zu zustandslosen Logik-Apps](#stateful-stateless).

* Führen Sie Ihre Logik-Apps lokal in der Visual Studio Code-Entwicklungsumgebung aus, und debuggen Sie sie dort.

* Veröffentlichen Sie Ihre Logik-Apps aus Visual Studio Code, und stellen Sie sie direkt in verschiedenen Hostumgebungen bereit, z. B. [Azure App Service](../app-service/environment/intro.md) und [Docker-Container](/dotnet/core/docker/introduction).

> [!NOTE]
> Informationen zu aktuellen bekannten Problemen finden Sie auf der [GitHub-Seite „Bekannte Probleme“](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) der Vorschauerweiterung.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Zustandsbehaftete Logik-Apps im Vergleich zu zustandslosen Logik-Apps

* *Zustandsbehaftet*

  Erstellen Sie zustandsbehaftete Logik-Apps, wenn Sie Daten aus vorherigen Ereignissen beibehalten, überprüfen oder referenzieren müssen. Diese Logik-Apps behalten die Eingabe und Ausgabe für jede Aktion und deren Workflowzustände im externen Speicher bei, wodurch die Ausführungsdetails und der Ausführungsverlauf nach Abschluss der jeweiligen Ausführung überprüft werden können. Zustandsbehaftete Logik-Apps bieten hohe Resilienz, wenn es zu Ausfällen kommen sollte. Nachdem Dienste und Systeme wiederhergestellt wurden, können Sie unterbrochene Logik-App-Ausführungen aus dem gespeicherten Zustand rekonstruieren und die Logik-Apps bis zum Abschluss erneut ausführen. Die Ausführung zustandsbehafteter Workflows kann bis zu ein Jahr lang fortgesetzt werden.

* *Zustandslos*

  Erstellen Sie zustandslose Logik-Apps, wenn Sie Daten aus vorherigen Ereignissen nicht zur späteren Überprüfung in externem Speicher speichern, überprüfen oder referenzieren müssen. Diese Logik-Apps behalten die Eingabe und Ausgabe für jede Aktion und deren Workflowzustände nur im Arbeitsspeicher bei, anstatt diese Informationen in den externen Speicher zu übertragen. Daher sind die Ausführungen zustandsloser Logik-Apps kürzer, in der Regel nicht länger als 5 Minuten, sie haben eine schnellere Leistung mit schnelleren Antwortzeiten, einen höheren Durchsatz und niedrigere Ausführungskosten, weil die Ausführungsdetails und der Ausführungsverlauf nicht im externen Speicher aufbewahrt werden. Wenn es aber zu Ausfällen kommen sollte, werden unterbrochene Ausführungen nicht automatisch wiederhergestellt, weshalb der Aufrufer unterbrochene Ausführungen manuell erneut übermitteln muss. Diese Logik-Apps können nur synchron ausgeführt werden. Um das Debuggen zu vereinfachen, können Sie den [Ausführungsverlauf aktivieren](#run-history), was Auswirkungen auf die Leistung hat.

  Zustandslose Workflows unterstützen derzeit nur *Aktionen* für in Azure bereitgestellte [verwaltete Connectors](../connectors/apis-list.md#managed-api-connectors), keine Aktionen für Trigger. Wählen Sie den [integrierten Anforderungs-, Event Hubs- oder Service Bus-Trigger](../connectors/apis-list.md#built-ins) aus, der nativ in der Logic Apps-Runtime ausgeführt wird, um Ihren Workflow zu starten. Weitere Informationen zu nicht unterstützten Triggern, Aktionen und Connectors finden Sie unter [Nicht unterstützte oder nicht verfügbare Funktionen](#unsupported).

Informationen zur Art und Weise, wie sich geschachtelte Logik-Apps zwischen zustandsbehafteten und zustandslosen Logik-Apps unterschiedlich verhalten, finden Sie unter [Unterschiede im geschachtelten Verhalten zwischen zustandsbehafteten und zustandslosen Logik-Apps](#nested-behavior).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Preismodell

Wenn Sie den neuen **Logik-App (Vorschau)** -Ressourcentyp bereitstellen, werden Sie aufgefordert, einen Hostingplan auszuwählen, insbesondere den [App Service-Plan oder Premium-Plan](../azure-functions/functions-scale.md), der als Preismodell verwendet werden soll. Wenn Sie den App Service-Plan auswählen, werden Sie außerdem aufgefordert, einen [Tarif](../app-service/overview-hosting-plans.md) auszuwählen. Während der öffentlichen Vorschauphase entstehen durch das Ausführen von Logik-Apps im App Service keine *zusätzlichen* Gebühren zum ausgewählten Plan.

Zustandsbehaftete Logik-Apps verwenden [externen Speicher](../azure-functions/functions-scale.md#storage-account-requirements), sodass das Azure Storage-Preismodell auf Speichertransaktionen angewendet wird, die von der Azure Logic Apps-Runtime ausgeführt werden. Beispielsweise werden Warteschlangen für die Zeitplanung verwendet, während Tabellen und Blobs zum Speichern von Workflowzuständen verwendet werden.

Weitere Informationen zu den Preismodellen, die für diesen neuen Ressourcentyp gelten, finden Sie in den folgenden Themen:

* [Skalierung und Hosting von Azure Functions](../azure-functions/functions-scale.md)
* [Hochskalieren einer App in Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions – Preisdetails](https://azure.microsoft.com/pricing/details/functions/)
* [App Service-Preisdetails](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Azure Storage – Preisdetails](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Nicht verfügbare oder nicht unterstützte Funktionen

Diese Funktionen sind für diese öffentliche Vorschau nicht verfügbar oder werden nicht unterstützt:

* Das Erstellen der neuen **Logik-App**-Ressource (Vorschau) ist zurzeit unter macOS nicht verfügbar.

* Es werden noch nicht alle Azure-Regionen unterstützt. Überprüfen Sie für derzeit verfügbare Regionen die [Regionenliste](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

* Verwenden Sie den [integrierten Anforderungs-, HTTP-, Event Hubs- oder Service Bus-Trigger](../connectors/apis-list.md), der nativ in der Logic Apps-Runtime ausgeführt wird, um Ihren Workflow zu starten. Derzeit werden [Unternehmensconnectors](../connectors/apis-list.md#enterprise-connectors), [lokale Datengatewaytrigger](../connectors/apis-list.md#on-premises-connectors), webhook-basierte Trigger, „Gleitendes Fenster“-Trigger, [benutzerdefinierte Connectors](../connectors/apis-list.md#custom-apis-and-connectors), Integrationskonten, ihre Artefakte und [ihre Connectors](../connectors/apis-list.md#integration-account-connectors) in dieser Vorschauversion nicht unterstützt. Da die Funktionalität „Aufrufen einer Azure-Funktion“ nicht verfügbar ist, verwenden Sie vorerst die HTTP-*Aktion*, um die Anforderungs-URL für die Azure-Funktion aufzurufen.

  Mit Ausnahme der zuvor angegebenen Trigger können *zustandsbehaftete* Workflows im Gegensatz zu integrierten Triggern und Aktionen, die nativ mit der Logic Apps-Laufzeit ausgeführt werden, sowohl Trigger als auch Aktionen für [verwaltete Connectors](../connectors/apis-list.md#managed-api-connectors) verwenden, die in Azure bereitgestellt werden. *Zustandslose* Workflows unterstützen derzeit nur *Aktionen* für verwaltete Connectors, keine für Trigger. Obwohl Sie Connectors in Azure für zustandslose Workflows aktivieren können, zeigt der Designer keine verwalteten Connectortrigger an, die Sie auswählen können.

* Sie können den neuen **Logik-App**-Ressourcentyp (Vorschau) nur für einen [Premium- oder App Service-Hostingplan in Azure](#publish-azure) oder einen [Docker-Container](#deploy-docker) bereitstellen, nicht für [Integrationsdienstumgebungen (Integration Service Environment, ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Hostingpläne für den **Verbrauch** werden weder unterstützt, noch stehen sie zur Bereitstellung dieses Ressourcentyps zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="access-and-connectivity"></a>Zugriff und Konnektivität

* Zugriff auf das Internet, damit Sie die Anforderungen herunterladen, von Visual Studio Code aus eine Verbindung mit Ihrem Azure-Konto herstellen und aus Visual Studio Code in Azure, einem Docker-Container oder einer anderen Umgebung veröffentlichen können.

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Um dieselbe Beispiel-Logik-App in diesem Artikel zu erstellen, benötigen Sie ein Office 365 Outlook-E-Mail-Konto, das ein Geschäfts-, Schul- oder Unikonto von Microsoft für die Anmeldung verwendet.

  Wenn Sie sich entschließen, einen anderen [E-Mail-Connector zu verwenden, der von Azure Logic Apps unterstützt wird](/connectors/), z. B. Outlook.com oder [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), können Sie dennoch das Beispiel durcharbeiten, und die allgemeinen übergeordneten Schritte sind identisch, doch Ihre Benutzeroberfläche und deren Optionen können sich in gewisser Weise unterscheiden. Wenn Sie beispielsweise den Outlook.com-Connector verwenden, verwenden Sie stattdessen Ihr persönliches Microsoft-Konto, um sich anzumelden.

### <a name="storage-requirements"></a>Speicheranforderungen

1. Laden Sie den [Azure Storage-Emulator 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179) herunter, und installieren Sie ihn.

1. Zum Ausführen des Emulators benötigen Sie eine lokale SQL-DB-Installation, z. B. die kostenlose [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658). Weitere Informationen finden Sie unter [Verwenden des Azure Storage-Emulators für Entwicklung und Tests](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Bevor Sie den Logik-App-Designer öffnen, um Ihren Logik-App-Workflow zu erstellen, stellen Sie sicher, dass Sie den Emulator starten. Andernfalls erhalten Sie die Meldung: `Workflow design time could not be started`.
   >
   > ![Screenshot, der den ausgeführten Azure Storage-Emulator zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>Tools

* [Visual Studio Code 1.30.1 (Januar 2019) oder höher](https://code.visualstudio.com/) (kostenlos). Laden Sie ferner diese zusätzlichen Tools für Visual Studio Code herunter, und installieren Sie sie, falls sie noch nicht vorhanden sind:

  * [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), die eine einzelne allgemeine Azure-Anmeldungs- und -Abonnementfilterungserfahrung für alle anderen Azure-Erweiterungen in Visual Studio Code bereitstellt.

  * [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), mit der die F5-Funktionalität in die Lage versetzt wird, Ihre Logik-App auszuführen.

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md), entweder Version [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) oder [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936) über Microsoft Installer (MSI). Diese Tools enthalten eine Version derselben Runtime, auf der die Azure Functions-Runtime basiert, die in Visual Studio Code ausgeführt wird.

    > [!IMPORTANT]
    > Wenn Sie über eine Installation verfügen, die älter als diese Versionen ist, deinstallieren Sie diese Version zuerst, oder stellen Sie sicher, dass die PATH-Umgebungsvariable auf die Version verweist, die Sie herunterladen und installieren.
    >
    > Wenn Sie die [**Inlinecode**-Aktion](../logic-apps/logic-apps-add-run-inline-code.md) zum Ausführen von JavaScript-Code verwenden möchten, müssen Sie die Azure Functions-Laufzeitversion 3x verwenden, weil die Aktion die Version 2x nicht unterstützt. Außerdem wird diese Aktion derzeit nicht unter Linux-Betriebssystemen unterstützt.

  * [Azure Logic Apps (Vorschau)-Erweiterung für Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Diese Erweiterung in der öffentlichen Vorschau bietet Ihnen die Möglichkeit, zustandsbehaftete und zustandslose Logik-Apps zu erstellen und sie lokal in Visual Studio Code auszuführen.

    Zurzeit können Sie sowohl die ursprüngliche **Azure Logic Apps**-Erweiterung als auch die neue **Azure Logic Apps (Vorschau)** -Erweiterung gleichzeitig in Visual Studio Code installiert haben. Wenn Sie das Azure-Symbol auf der Visual Studio Code-Symbolleiste auswählen, können Sie alle in Azure bereitgestellten Logik-Apps anzeigen, aber jeder Ressourcentyp wird in seinen eigenen Erweiterungsabschnitten angezeigt, **Logic Apps** und **Azure Logic Apps (Vorschau)** .

    > [!IMPORTANT]
    > Wenn Sie Logik-Apps mithilfe der **Azure Logic Apps (Private Vorschau)** -Erweiterung erstellt haben, funktionieren diese Logik-Apps nicht mit der öffentlichen Vorschauerweiterung. Sie können diese Logik-Apps jedoch migrieren, indem Sie die private Vorschauerweiterung deinstallieren, die erforderliche Bereinigung ausführen und die öffentliche Vorschauerweiterung installieren. Anschließend können Sie dann Ihr neues Projekt in Visual Studio Code erstellen und Ihre zuvor erstellte **workflow.definition**-Datei der Logik-App in Ihr neues Projekt kopieren.
    >
    > Stellen Sie also vor der Installation der öffentlichen Vorschauerweiterung sicher, dass Sie alle früheren Versionen deinstallieren, und löschen Sie die folgenden Artefakte:
    >
    > * Den Ordner **Microsoft.Azure.Functions.ExtensionBundle.Workflows**, der vorherige Erweiterungsbündel enthält und sich in einem dieser beiden Pfade befindet:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * Den Ordner **microsoft.azure.workflows.webjobs.extension**, der der [NuGet](/nuget/what-is-nuget)-Cache für die private Vorschauerweiterung ist und sich in diesem Pfad befindet:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    Führen Sie zum Installieren der **Azure Logic Apps (Vorschau)** -Erweiterung die folgenden Schritte aus:

    1. Wählen Sie in Visual Studio Code in der linken Symbolleiste **Erweiterungen** aus.

    1. Geben Sie in das Suchfeld für Erweiterungen `azure logic apps preview` ein. Wählen Sie in der Ergebnisliste **Azure Logic Apps (Vorschau)** **>** **Installieren** aus.

       Nach Abschluss der Installation wird die Erweiterung der öffentlichen Vorschau in der Lite **Erweiterungen: Installiert** angezeigt.

       ![Screenshot, der die Liste der installierten Erweiterungen in Visual Studio Code mit unterstrichener Erweiterung „Azure Logic Apps (Vorschau)“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Zum Testen der Beispiel-Logik-App, die Sie in diesem Artikel erstellen, benötigen Sie ein Tool, das Aufrufe an den Anforderungstrigger senden kann, wobei es sich um den ersten Schritt in der Beispiel-Logik-App handelt. Falls Sie nicht über ein solches Tool verfügen, können Sie [Postman](https://www.postman.com/downloads/) herunterladen, installieren und verwenden.

* Zur Vereinfachung der Diagnoseprotokollierungs- und Ablaufverfolgungsfunktion können Sie eine [Application Insights](../azure-monitor/app/app-insights-overview.md)-Ressource hinzufügen und verwenden. Sie können diese Ressource während der Bereitstellung der Logik-App erstellen oder im Azure-Portal, nachdem Sie Ihre Logik-App bereitgestellt haben.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Einrichten von Visual Studio Code

1. Um sicherzustellen, dass alle Erweiterungen ordnungsgemäß installiert sind, laden Sie Visual Studio Code neu, oder starten Sie es erneut.

1. Aktivieren oder überprüfen Sie, dass Visual Studio Code Erweiterungsupdates automatisch sucht und installiert, damit Ihre öffentliche Vorschauerweiterung die neuesten Updates erhält.

   Gehen Sie wie folgt vor, um diese Einstellung zu überprüfen:

   1. Navigieren Sie im Menü **Datei** zu **Voreinstellungen** **>** **Einstellungen**.

   1. Wechseln Sie auf der Registerkarte **Benutzer** zu **Features** **>** **Erweiterungen**.

   1. Vergewissern Sie sich, dass **Updates automatisch suchen** und **Automatische Aktualisierung** ausgewählt sind.

1. Aktivieren oder überprüfen Sie, dass diese **Azure Logic Apps (Vorschau)** -Erweiterungseinstellungen in Visual Studio Code eingerichtet sind:

   * **Azure Logic Apps V2: Panelmodus**
   * **Azure Logic Apps V2: Projektlaufzeit**

   1. Navigieren Sie im Menü **Datei** zu **Voreinstellungen** **>** **Einstellungen**.

   1. Wechseln Sie auf der Registerkarte **Benutzer** zu **>** **Erweiterungen** **>** **Azure Logic Apps (Vorschau)** .

   1. Vergewissern Sie sich unter **Azure Logic Apps V2: Panelmodus**, dass **Panelmodus aktivieren** ausgewählt ist. Legen Sie unter **Azure Logic Apps V2: Projektlaufzeit** die Version auf **~3** oder **~2** fest, basierend auf der [Version der Azure Functions Core Tools](#prerequisites), die Sie zuvor installiert haben.

      > [!IMPORTANT]
      > Wenn Sie die [**Inlinecode**-Aktion](../logic-apps/logic-apps-add-run-inline-code.md) zum Ausführen von JavaScript-Code verwenden möchten, stellen Sie sicher, dass Sie Projektlaufzeitversion 3 verwenden, weil die Aktion die Version 2 nicht unterstützt. Außerdem wird diese Aktion derzeit nicht unter Linux-Betriebssystemen unterstützt.

      ![Screenshot, der die Visual Studio Code-Einstellungen für die Erweiterung „Azure Logic Apps (Vorschau)“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Herstellen einer Verbindung mit Ihrem Azure-Konto

1. Wählen Sie in der Visual Studio Code-Symbolleiste das Azure-Symbol aus.

   ![Screenshot, der die Visual Studio Code-Symbolleiste mit ausgewähltem Azure-Symbol zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Wählen Sie im Azure-Bereich unter **Azure: Logic Apps (Vorschau)** die Option **Bei Azure anmelden** aus. Wenn die Authentifizierungsseite für Visual Studio Code angezeigt wird, melden Sie sich mit Ihrem Azure-Konto an.

   ![Screenshot, der den Azure-Bereich und den ausgewählten Link für die Azure-Anmeldung zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Nachdem Sie sich angemeldet haben, werden im Azure-Bereich die Abonnements in Ihrem Azure-Konto angezeigt. Wenn Sie die öffentlich verfügbare Logic Apps-Erweiterung haben, finden Sie alle ursprünglichen Logic Apps-Ressourcen, die Sie mit der ursprünglichen Erweiterung erstellt haben, im Abschnitt **Logic Apps** der veröffentlichten Erweiterung und nicht im Abschnitt **Logik-Apps (Vorschau)** der Erweiterungsvorschauversion.
   
   Wenn die erwarteten Abonnements nicht angezeigt werden oder der Bereich nur bestimmte Abonnements anzeigen soll, führen Sie die folgenden Schritte aus:

   1. Bewegen Sie in der Liste Abonnements den Zeiger neben das erste Abonnement, bis die Schaltfläche (Filtersymbol) **Abonnements auswählen** angezeigt wird. Wählen Sie das Filtersymbol aus.

      ![Screenshot, der den Azure-Bereich und das ausgewählte Filtersymbol zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Oder wählen Sie in der Statusleiste von Visual Studio Code Ihr Azure-Konto aus. 

   1. Wenn eine weitere Abonnement liste angezeigt wird, wählen Sie die gewünschten Abonnements aus, und stellen Sie dann sicher, dass Sie **OK** auswählen.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Erstellen eines lokalen Projekts

Bevor Sie Ihre Logik-App erstellen können, erstellen Sie ein lokales Projekt, damit Sie Ihre Logik-App aus Visual Studio Code verwalten und bereitstellen können. Das zugrunde liegende Projekt ähnelt stark einem Azure Functions-Projekt, das auch als Funktions-App-Projekt bezeichnet wird. Diese Projekttypen sind jedoch voneinander getrennt, sodass Logik-App-Workflows und -Funktionen nicht im selben Projekt vorhanden sein können.

1. Erstellen Sie auf Ihrem Computer einen *leeren* lokalen Ordner, der für das Projekt verwendet werden soll, das Sie später in Visual Studio Code erstellen werden.

1. Schließen Sie in Visual Studio Code alle offenen Ordner.

1. Wählen Sie im Azure-Bereich neben **Azure: Logic Apps (Vorschau)** die Option **Neues Projekt erstellen** (Symbol mit Ordner und Blitz) aus.

   ![Screenshot, der die Symbolleiste im Azure-Bereich mit ausgewähltem „Neues Projekt erstellen“ anzeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Wenn Sie von der Windows Defender Firewall aufgefordert werden, für `Code.exe`, wobei es sich um Visual Studio Code handelt, und für `func.exe`, wobei es sich um Azure Functions Core Tools handelt, Netzwerkzugriff zu gewähren, wählen Sie **Private Netzwerke, z. B. Heim- oder Firmennetzwerk** **>** **Zugriff zulassen** aus.

1. Navigieren Sie zu dem Speicherort, an dem Sie Ihren Projektordner erstellt haben, wählen Sie diesen Ordner aus, und fahren Sie fort.

   ![Screenshot, der das Dialogfeld „Ordner auswählen“ mit einem neu erstellten Projektordner und der ausgewählten Schaltfläche „Auswählen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Wählen Sie in der angezeigten Vorlagenliste entweder **Zustandsbehafteter Workflow** oder **Zustandsloser Workflow** aus. In diesem Beispiel wird **Zustandsbehafteter Workflow** ausgewählt.

   ![Screenshot, der die Liste der Workflowvorlagen mit ausgewähltem „Zustandsbehafteter Workflow“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Geben Sie einen Namen für Ihren Logik-App-Workflow ein, und drücken Sie die EINGABETASTE. In diesem Beispiel wird `example-workflow` als Name verwendet.

   ![Screenshot, der das Feld „Neuen zustandsbehafteten Workflow erstellen (3/4)“ und „example-workflow“ als Workflownamen zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. Wählen Sie in der nächsten angezeigten Liste **In aktuellem Fenster öffnen** aus.

   ![Screenshot, der eine Liste mit ausgewählter Option „In aktuellem Fenster öffnen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code wird neu geladen, öffnet den Explorer-Bereich und zeigt Ihr Projekt an, das nun automatisch generierte Projektdateien enthält. Beispielsweise verfügt das Projekt über einen Ordner, der den Namen Ihres Logik-App-Workflows trägt. In diesem Ordner enthält die **workflow.json**-Datei die zugrunde liegende JSON-Definition Ihres Logik-App-Workflows.

   ![Screenshot, der das Explorer-Fenster mit Projektordner, Workflowordner und der Datei „workflow.json“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Öffnen der Workflowdefinitionsdatei im Logik-App-Designer

1. Überprüfen Sie die auf Ihrem Computer installierten Versionen mit folgendem Befehl:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Wenn Sie über .NET Core SDK 5.x verfügen, kann diese Version Sie möglicherweise daran hindern, die zugrunde liegende Workflowdefinition der Logik-App im Designer zu öffnen. Anstatt diese Version zu deinstallieren, erstellen Sie im Stammverzeichnis Ihres Projekts eine **global.json**-Datei, die auf Ihre .NET Core Runtime 3.x-Version verweist, die höher als 3.1.201 ist, z. B.:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Stellen Sie sicher, dass Sie dem Projekt diese **global.json**-Datei explizit im Stammverzeichnis in Visual Studio Code hinzufügen. Andernfalls wird der Designer nicht geöffnet.

1. Wenn Visual Studio Code unter Windows oder Linux ausgeführt wird, stellen Sie sicher, dass der Azure Storage-Emulator ausgeführt wird. Weitere Informationen finden Sie in den [Voraussetzungen](#prerequisites).

1. Erweitern Sie den Projektordner für Ihren Workflow. Öffnen Sie das Kontextmenü der Datei **workflow.json**, und wählen Sie **Im Designer öffnen** aus.

   ![Screenshot, der den Explorer-Bereich und das Kontextmenüfenster für die Datei „workflow.json“ mit ausgewähltem „In Designer öffnen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Wenn Sie die Fehlermeldung erhalten, dass die `Workflow design time could not be started`, stellen Sie sicher, dass der Azure Storage-Emulator ausgeführt wird. Probieren Sie andernfalls die folgenden Problembehandlungsvorschläge aus:

   Überprüfen Sie in Visual Studio Code die Ausgabe der Vorschauerweiterung.

   1. Wählen Sie im Menü **Ansicht** die Option **Ausgabe** aus.

   1. Wählen Sie in der Liste auf der **Ausgabe**-Titelleiste **Azure Logic Apps** aus, damit Sie die Ausgabe für die Vorschauerweiterung anzeigen können, z. B.:

      ![Screenshot, der das Ausgabefenster von Visual Studio Code mit ausgewählten „Azure Logic Apps“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Überprüfen Sie die Ausgabe, und prüfen Sie, ob diese Fehlermeldung angezeigt wird:

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Dieser Fehler kann auftreten, wenn Sie zuvor versucht haben, den Designer zu öffnen, und dann Ihr Projekt eingestellt oder gelöscht haben. Um diesen Fehler zu beheben, löschen Sie den Ordner **ExtensionBundles** an diesem Speicherort **...\Benutzer\\{Ihr-Benutzername}\AppData\Local\Temp\Functions\ExtensionBundles**, und versuchen Sie erneut, die Datei **workflow.json** im Designer zu öffnen.

1. Wählen Sie in der Liste **Enable connectors in Azure** (Connectors in Azure aktivieren) die Option **Use connectors from Azure** (Connectors aus Azure verwenden) aus, die für alle verwalteten Connectors gilt, die im Azure-Portal verfügbar sind, nicht nur für Connectors für Azure-Dienste.

   ![Screenshot, der den Explorer-Bereich mit der geöffneten Liste „Connectors in Azure aktivieren“ und ausgewähltem „Connectors von Azure verwenden“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Zustandslose Workflows unterstützen derzeit nur *Aktionen* für in Azure bereitgestellte [verwaltete Connectors](../connectors/apis-list.md#managed-api-connectors), keine Aktionen für Trigger. Obwohl Sie die Möglichkeit haben, Connectors in Azure für zustandslose Workflows zu aktivieren, zeigt der Designer keine verwalteten Connectortrigger an, die Sie auswählen können.

1. Wählen Sie in der Liste der Ressourcengruppen **Neue Ressourcengruppe erstellen** aus.

   ![Screenshot, der den Explorer-Bereich mit der Liste der Ressourcengruppen und ausgewähltem „Neue Ressourcengruppe erstellen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Geben Sie einen Namen für die Ressourcengruppe an, und drücken Sie die EINGABETASTE. In diesem Beispiel wird `example-logic-app-preview-rg` verwendet.

   ![Screenshot, der den Explorer-Bereich und das Feld für den Namen der Ressourcengruppe zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Suchen Sie in der Liste der Standorte eine [unterstützte Azure-Region](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions), die für die Erstellung Ihrer Ressourcengruppe und Ressourcen verwendet werden soll, und wählen Sie diese aus. In diesem Beispiel wird **USA, Westen-Mitte** verwendet.

   > [!IMPORTANT]
   > Derzeit werden nicht alle Regionen unterstützt, aber Updates zum Hinzufügen weiterer Regionen werden kommen. Die Auswahl einer nicht unterstützten Region kann z. B. beim Erstellen von Verbindungen zu Problemen führen. Informationen zu derzeit unterstützten Regionen finden Sie auf der [GitHub-Seite zu bekannten Problemen](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) der Vorschauerweiterung.

   ![Screenshot, der den Explorer-Bereich mit der Liste der Standorte und ausgewähltem „USA, Westen-Mitte“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Nachdem Sie diesen Schritt ausgeführt haben, öffnet Visual Studio Code den Logik-App-Designer.

   > [!NOTE]
   > Wenn Visual Studio Code die Workflow-Entwurfszeit-API startet, wird eine Meldung angezeigt, dass der Startvorgang ein paar Sekunden dauern kann. Sie können diese Meldung ignorieren oder **OK** auswählen.

   Nachdem der Logik-App-Designer angezeigt wird, wird die Eingabeaufforderung **Vorgang auswählen** im Designer standardmäßig ausgewählt angezeigt, wodurch der Bereich **Aktion hinzufügen** angezeigt wird.

   ![Screenshot, der den Logik-App-Designer zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Als Nächstes [fügen Sie Ihrem Workflow einen Trigger und Aktionen hinzu](#add-trigger-actions).

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Hinzufügen eines Triggers und von Aktionen

Nachdem Sie den Logik-App-Designer über das Kontextmenü Ihrer Datei **workflow.json** geöffnet haben, wird die Eingabeaufforderung **Vorgang auswählen** im Designer standardmäßig ausgewählt angezeigt. Sie können nun mit dem Erstellen Ihres Workflows beginnen, indem Sie einen Trigger und Aktionen hinzufügen.

Der Logik-App-Workflow in diesem Beispiel verwendet diesen Triggern und die folgenden Aktionen:

* Den integrierten [Anforderungstrigger](../connectors/connectors-native-reqres.md), **Beim Empfang einer HTTP-Anforderung**, der eingehende Aufrufe oder Anforderungen empfängt und einen Endpunkt erstellt, der von anderen Diensten oder Logik-Apps aufgerufen werden kann.

* Die [Office 365 Outlook-Aktion](../connectors/connectors-create-api-office365-outlook.md): **E-Mail senden**.

* Die integrierte [Antwortaktion](../connectors/connectors-native-reqres.md), mit der Sie eine Antwort gesendet und Daten an den Aufrufer zurückgegeben haben.

### <a name="add-the-request-trigger"></a>Hinzufügen des Anforderungstriggers

1. Vergewissern Sie sich neben dem Designer im Bereich **Trigger hinzufügen** unter dem Suchfeld **Vorgang auswählen**, dass **Integriert** ausgewählt ist, damit Sie einen Trigger auswählen können, der nativ ausgeführt wird.

1. Geben Sie in das Suchfeld **Vorgang auswählen** die Zeichenfolge `when a http request` ein, und wählen Sie den integrierten Anforderungstrigger namens **Beim Empfang einer HTTP-Anforderung** aus.

   ![Screenshot, der den Logik-App-Designer mit dem Bereich **Trigger hinzufügen** mit ausgewähltem Trigger „Beim Empfang einer HTTP-Anforderung“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Wenn der Trigger im Designer angezeigt wird, wird der Detailbereich des Triggers geöffnet, um die Eigenschaften, Einstellungen und anderen Aktionen des Triggers anzuzeigen.

   ![Screenshot, der den Logik-App-Designer mit dem ausgewählten Trigger „Beim Empfang einer HTTP-Anforderung“ und geöffnetem Triggerdetailbereich zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Wenn der Detailbereich nicht angezeigt wird, stellen Sie sicher, dass der Trigger im Designer ausgewählt ist.

1. Wenn Sie ein Element im Designer löschen müssen, führen Sie diese Schritte aus:

   1. Wählen Sie im Designer das Element aus, das den Detailbereich des Elements auf der rechten Seite öffnet.

   1. Erweitern Sie das Visual Studio Code-Fenster ausreichend, sodass die Schaltfläche mit den Auslassungspunkten ( **...** ) neben dem Trigger- oder Aktionsnamen in der rechten oberen Ecke angezeigt wird. 

   1. Öffnen Sie das Menü mit den Auslassungspunkten ( **...** ), und wählen Sie **Löschen** aus. Um das Löschen zu bestätigen, wählen Sie **OK** aus.

      ![Screenshot, der das ausgewählte Element im Designer mit geöffnetem Detailbereich mit ausgewählter Schaltfläche mit Auslassungspunkten und mit der Option „Löschen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Hinzufügen der Office 365 Outlook-Aktion

1. Wählen Sie im Logik-App-Designer unter dem von Ihnen hinzugefügten Trigger **Neuer Schritt** aus.

   Die Eingabeaufforderung **Vorgang auswählen** wird im Designer angezeigt, und der Bereich **Aktion hinzufügen** wird erneut geöffnet, sodass Sie die nächste Aktion auswählen können.

1. Wählen Sie im Bereich **Aktion hinzufügen** unter dem Suchfeld **Vorgang auswählen** die Zeichenfolge **Azure** aus, damit Sie eine Aktion für einen verwalteten Connector suchen und auswählen können, der in Azure bereitgestellt ist.

   In diesem Beispiel wird die Office 365 Outlook-Aktion **E-Mail senden (V2)** ausgewählt und verwendet.

   ![Screenshot, der den Logik-App-Designer und den Bereich **Aktion hinzufügen** mit ausgewählter Office 365 Outlook-Aktion „E-Mail senden“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Wählen Sie im Detailbereich der Aktion **Anmelden** aus, damit Sie eine Verbindung mit Ihrem E-Mail-Konto herstellen können.

   ![Screenshot, der den Logik-App-Designer und den Bereich **E-Mail senden (V2)** mit ausgewähltem „Anmelden“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Wenn Sie die Fehlermeldung `Failed to create connection...` erhalten, haben Sie möglicherweise eine derzeit nicht unterstützte Region für Ihre Logik-App ausgewählt. Updates zum Hinzufügen weiterer Regionen werden kommen. In der Zwischenzeit finden Sie Informationen zu derzeit unterstützten Regionen auf der [GitHub-Seite zu bekannten Problemen](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) der Vorschauerweiterung.

1. Wenn Visual Studio Code Sie zur Zustimmung für den Zugriff auf Ihr E-Mail-Konto auffordert, wählen Sie **Öffnen** aus.

   ![Screenshot, der die Aufforderung von Visual Studio Code zur Zulassung des Zugriffs zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Um zukünftige Aufforderungen zu vermeiden, wählen Sie **Vertrauenswürdige Domänen konfigurieren** aus, damit Sie die Authentifizierungsseite als vertrauenswürdige Domäne hinzufügen können.

1. Befolgen Sie die nachfolgenden Aufforderungen, um sich anzumelden, den Zugriff zuzulassen und die Rückkehr zu Visual Studio Code zuzulassen.

   > [!NOTE]
   > Wenn zu viel Zeit verstrichen ist, bevor Sie die Eingabeaufforderungen erfüllt haben, kommt es beim Authentifizierungsprozess zum Timeout, und er schlägt fehl. Kehren Sie in diesem Fall zum Designer zurück, und wiederholen Sie die Anmeldung, um die Verbindung zu erstellen.

1. Wenn die Vorschauerweiterung von Azure Logic Apps Sie zur Zustimmung für den Zugriff auf Ihr E-Mail-Konto auffordert, wählen Sie **Öffnen** aus. Befolgen Sie die nachfolgende Aufforderung, um den Zugriff zuzulassen.

   ![Screenshot, der die Aufforderung der Vorschauerweiterung zur Zulassung des Zugriffs zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Um zukünftige Eingabeaufforderungen zu vermeiden, wählen Sie **Für diese Erweiterung nicht mehr nachfragen** aus.

   Nachdem Visual Studio Code die Verbindung erstellt hat, zeigen einige Connectors die Meldung `The connection will be valid for {n} days only.` an. Dieses Zeitlimit gilt nur für die Dauer des Erstellens Ihrer Logik-App in Visual Studio Code. Nach der Bereitstellung gilt dieses Limit nicht mehr, da sich Ihre Logik-App zur Laufzeit mittels der automatisch aktivierten [vom System zugewiesenen verwalteten Identität](../logic-apps/create-managed-service-identity.md) authentifizieren kann. Diese verwaltete Identität unterscheidet sich von den Anmeldeinformationen für die Authentifizierung oder der Verbindungszeichenfolge, die Sie verwenden, wenn Sie eine Verbindung herstellen. Wenn Sie diese vom System zugewiesene verwaltete Identität deaktivieren, funktionieren Verbindungen zur Laufzeit nicht.

1. Wenn die Aktion **E-Mail senden** im Designer nicht ausgewählt angezeigt wird, wählen Sie diese Aktion aus.

1. Geben Sie im Detailbereich der Aktion auf der Registerkarte **Parameter** die erforderlichen Informationen für die Aktion an, z. B.:

   ![Screenshot, der den Logik-App-Designer mit Details für die Office 365 Outlook-Aktion „E-Mail senden“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **An** | Ja | <*Ihre E-Mail-Adresse*> | Der E-Mail-Empfänger, bei dem es sich um Ihre E-Mail-Adresse zu Testzwecken handeln kann. In diesem Beispiel wird die fiktive E-Mail-Adresse `sophiaowen@fabrikam.com` verwendet. |
   | **Subject** | Ja | `An email from your example workflow` | der E-Mail-Betreff |
   | **Text** | Ja | `Hello from your example workflow!` | Der Textinhalt der E-Mail |
   ||||

   > [!NOTE]
   > Wenn Sie Änderungen im Detailbereich auf der Registerkarte **Einstellungen**, **Ausführen nach** oder **Statisches Ergebnis** vornehmen möchten, stellen Sie sicher, dass Sie **Fertig** ausgewählt haben, um die Änderungen zu übernehmen, bevor Sie zwischen Registerkarten wechseln oder den Fokus innerhalb des Designers verschieben. Andernfalls behält Visual Studio Code Ihre Änderungen nicht. Weitere Informationen finden Sie auf der [GitHub-Seite „Bekannte Probleme“](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) der Vorschauerweiterung.

1. Wählen Sie im Designer **Speichern** aus.

Führen Sie als Nächstes Ihren Workflow lokal in Visual Studio Code aus und debuggen Sie ihn.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>Lokales Ausführen und Debuggen

Um Ihre Logik-App zu testen, führen Sie die folgenden Schritte aus, um eine Debugsitzung zu starten und die URL für den Endpunkt zu finden, der durch den Anforderungstrigger erstellt wird. Sie benötigen diese URL, damit Sie später eine Anforderung an diesen Endpunkt senden können.

1. Damit Sie einen zustandslosen Logik-App-Workflow leichter debuggen können, können Sie [den Ausführungsverlauf für diesen Workflow aktivieren](#run-history).

1. Öffnen Sie auf der Symbolleiste von Visual Studio Code das Menü **Ausführen**, und wählen Sie **Debuggen starten** (F5) aus.

   Das **Terminal** fenster wird geöffnet, sodass Sie die Debugsitzung überprüfen können.

1. Suchen Sie nun die Rückruf-URL für den Endpunkt im Anforderungstrigger.

   1. Öffnen Sie den Explorer-Bereich erneut, damit Sie Ihr Projekt anzeigen können.

   1. Wählen Sie im Kontextmenü der Datei **workflow.json** die Option **Übersicht** aus.

      ![Screenshot, der den Explorer-Bereich und das Kontextmenüfenster für die Datei „workflow.json“ mit ausgewähltem „Übersicht“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Suchen Sie den Wert **Rückruf-URL**, der dieser URL für den Anforderungsbeispieltrigger ähnelt:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Screenshot, der die Übersichtsseite Ihres Workflows mit Rückruf-URL zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Um die Rückruf-URL zu testen, indem Sie den Logik-App-Workflow auslösen, öffnen Sie [Postman](https://www.postman.com/downloads/) oder Ihr bevorzugtes Tool zum Erstellen und Senden von Anforderungen.

   Dieses Beispiel wird mit Postman fortgesetzt. Weitere Informationen finden Sie unter [Erste Schritte mit Postman](https://learning.postman.com/docs/getting-started/introduction/).

   1. Wählen Sie auf der Postman-Symbolleiste **Neu** aus.

      ![Screenshot, der Postman mit ausgewählter Schaltfläche „Neu“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Wählen Sie im Bereich **Neu erstellen** unter **Bausteine** die Option **Anforderung** aus.

   1. Geben Sie im Fenster **Anforderung speichern** unter **Anforderungsname** einen Namen für die Anforderung an, z. B. `Test workflow trigger`.

   1. Wählen Sie unter **Sammlung oder Ordner als Speicherziel auswählen** den Eintrag **Sammlung erstellen** aus.

   1. Geben Sie unter **Alle Sammlungen** einen Namen für die zu erstellende Sammlung an, um Ihre Anforderungen zu organisieren, und wählen Sie **Speichern unter <*Sammlungs-Name*>** aus. In diesem Beispiel wird `Logic Apps requests` als Name der Sammlung verwendet.

      Der Anforderungsbereich von Postman wird geöffnet, sodass Sie eine Anforderung an die Rückruf-URL für den Anforderungstrigger senden können.

      ![Screenshot, der Postman mit geöffnetem Anforderungsbereich zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Kehren Sie zu Visual Studio Code zurück. Kopieren Sie von der Übersichtsseite des Workflows den Eigenschaftswert von **Rückruf-URL**.

   1. Kehren Sie zu Postman zurück. Fügen Sie im Anforderungsbereich neben der Liste der Methoden, in der derzeit **GET** als Standardanforderungsmethode angezeigt wird, die zuvor kopierte Rückruf-URL in das Feld „Adresse“ ein, und wählen Sie **Senden** aus.

      ![Screenshot, der Postman und die Rückruf-URL im Adressfeld mit ausgewählter Schaltfläche „Senden“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Der Logik-App-Beispielworkflow sendet eine E-Mail, die in etwa wie im folgenden Beispiel aussieht:

      ![Screenshot, der die wie im Beispiel beschriebene Outlook-E-Mail zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Kehren Sie in Visual Studio Code zur Übersichtsseite Ihres Workflows zurück.

   Wenn Sie einen zustandsbehafteten Workflow erstellt haben, werden auf der Übersichtsseite der Ausführungsstatus und -verlauf des Workflows angezeigt, nachdem die von Ihnen gesendete Anforderung den Workflow ausgelöst hat.

   > [!TIP]
   > Wenn der Ausführungsstatus nicht angezeigt wird, aktualisieren Sie die Übersichtsseite, indem Sie **Aktualisieren** auswählen. Für Trigger, die aufgrund von nicht erfüllten Kriterien oder nicht gefundenen Daten ausgelassen werden, erfolgt keine Ausführung.

   ![Screenshot: Übersichtsseite Ihres Workflows mit Ausführungsstatus und -verlauf](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Ausführungsstatus | Beschreibung |
   |------------|-------------|
   | **Aborted** | Die Ausführung wurde aufgrund externer Probleme beendet oder nicht fertig gestellt, z. B. wegen eines Systemausfalls oder abgelaufenen Azure-Abonnements. |
   | **Abgebrochen** | Die Ausführung wurde ausgelöst und gestartet, es wurde jedoch eine Abbruchanforderung empfangen. |
   | **Fehler** | Mindestens eine Aktion in der Ausführung ist fehlgeschlagen. Es wurden keine nachfolgenden Aktionen im Workflow eingerichtet, um den Fehler zu verarbeiten. |
   | **Wird ausgeführt** | Die Ausführung wurde ausgelöst und wird gerade ausgeführt. Dieser Status kann jedoch auch für eine Ausführung angezeigt werden, die aufgrund von [Aktionslimits](logic-apps-limits-and-config.md) oder des [aktuellen Tarifs](https://azure.microsoft.com/pricing/details/logic-apps/) gedrosselt wird. <p><p>**Tipp**: Wenn Sie die [Diagnoseprotokollierung](monitor-logic-apps-log-analytics.md) einrichten, erhalten Sie Informationen zu ggf. aufgetretenen Drosselungsereignissen. |
   | **Erfolgreich** | Die Ausführung war erfolgreich. Wenn eine Aktion fehlgeschlagen ist, wurde dieser Fehler von einer nachfolgenden Aktion im Workflow verarbeitet. |
   | **Timeout** | Bei der Ausführung ist ein Timeout aufgetreten, weil die aktuelle Dauer die maximale Dauer von Ausführungen überschritten hat, die von der [Einstellung **Aufbewahrung des Ausführungsverlaufs in Tagen**](logic-apps-limits-and-config.md#run-duration-retention-limits) gesteuert wird. Die Dauer der Ausführung wird anhand der Startzeit der Ausführung und der maximalen Dauer von Ausführungen zu dieser Startzeit berechnet. <p><p>**Hinweis**: Wenn die Ausführungsdauer auch das aktuelle *Aufbewahrungslimit im Ausführungsverlauf* überschreitet, das ebenfalls von der [Einstellung **Aufbewahrung des Ausführungsverlaufs in Tagen**](logic-apps-limits-and-config.md#run-duration-retention-limits) gesteuert wird, wird die Ausführung durch einen täglichen Cleanupauftrag aus dem Ausführungsverlauf gelöscht. Unabhängig davon, ob bei der Ausführung ein Timeout auftritt oder ob diese fertig gestellt wird, wird die Aufbewahrungsdauer immer anhand der Startzeit der Ausführung und der *aktuellen* Aufbewahrungsdauer berechnet. Wenn Sie also die maximale Dauer einer aktiven Ausführung herabsetzen, tritt ein Timeout für die Ausführung auf. Je nachdem, ob die Ausführungsdauer die Aufbewahrungsdauer überschreitet, bleibt die Ausführung entweder unverändert oder wird aus dem Ausführungsverlauf gelöscht. |
   | **Wartet** | Die Ausführung wurde nicht gestartet oder wurde angehalten, z. B. aufgrund einer früheren Workflowinstanz, die noch ausgeführt wird. |
   |||

1. Wählen Sie zum Überprüfen der Statuswerte für jeden Schritt in einer bestimmten Ausführung sowie der Eingaben und Ausgaben des Schritts die Schaltfläche mit den Auslassungspunkten ( **...** ) für diese Ausführung aus, und wählen Sie **Ausführung anzeigen** aus.

   ![Screenshot, der die Zeile mit dem Ausführungsverlauf Ihres Workflows mit der Schaltfläche mit Auslassungspunkten und ausgewähltem „Ausführung anzeigen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code öffnet die Überwachungsansicht und zeigt den Status der einzelnen Ausführungsschritte an.

   ![Screenshot: einzelne Ausführungsschritte in der Workflowausführung und ihr Status](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Wenn bei einer Ausführung ein Schritt in der Überwachungsansicht den Fehler `400 Bad Request` anzeigt, wird das Problem möglicherweise durch einen längeren Trigger- oder Aktionsnamen verursacht, der dazu führt, dass der zugrunde liegende URI (Uniform Resource Identifier) das Standardzeichenlimit überschreitet. Weitere Informationen finden Sie unter [„400 – Ungültige Anforderung“](#400-bad-request).

   In der folgenden Tabelle sind die möglichen Statuswerte aufgeführt, die in den einzelnen Workflowschritte aufweisen können:

   | Status einer Aktion | Symbol | BESCHREIBUNG |
   |---------------|------|-------------|
   | Aborted | ![Symbol für den Aktionsstatus „Abgebrochen“][aborted-icon] | Die Aktion wurde aufgrund externer Probleme beendet oder nicht fertig gestellt, z. B. wegen eines Systemausfalls oder abgelaufenen Azure-Abonnements. |
   | Abgebrochen | ![Symbol für den Aktionsstatus „Storniert“][cancelled-icon] | Die Aktion wurde ausgeführt, hat aber eine Abbruchanforderung erhalten. |
   | Fehler | ![Symbol für den Aktionsstatus „Fehlerhaft“][failed-icon] | Die Aktion ist fehlgeschlagen. |
   | Wird ausgeführt | ![Symbol für den Aktionsstatus „Wird ausgeführt“][running-icon] | Die Aktion wird zurzeit ausgeführt. |
   | Ausgelassen | ![Symbol für den Aktionsstatus „Übersprungen“][skipped-icon] | Die Aktion wurde übersprungen, weil die unmittelbar vorhergehende Aktion fehlgeschlagen ist. Eine Aktion weist eine `runAfter`-Bedingung auf, die erfordert, dass die vorherige Aktion erfolgreich abgeschlossen wurde, bevor die aktuelle Aktion ausgeführt werden kann. |
   | Erfolgreich | ![Symbol für den Aktionsstatus „Erfolgreich“][succeeded-icon] | Die Aktion war erfolgreich. |
   | Erfolgreich mit Wiederholungen | ![Symbol für den Aktionsstatus „Erfolgreich mit Wiederholungen“][succeeded-with-retries-icon] | Die Aktion war erfolgreich, jedoch erst nach mindestens einem Wiederholungsversuch. Wählen Sie diese Aktion zum Überprüfen des Wiederholungsverlaufs in der Detailansicht des Ausführungsverlaufs aus, damit Sie die Ein- und Ausgaben anzeigen können. |
   | Timeout | ![Symbol für Aktionsstatus „Timeout“][timed-out-icon] | Die Aktion wurde aufgrund des Timeoutlimits beendet, das durch die Einstellungen dieser Aktion gesteuert wird. |
   | Warten | ![Symbol für den Aktionsstatus „Wartend“][waiting-icon] | Gilt für eine Webhookaktion, die auf eine eingehende Anforderung eines Aufrufers wartet. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Wählen Sie zum Überprüfen der Ein- und Ausgaben für die einzelnen Schritte den Schritt aus, den Sie untersuchen möchten.

   ![Screenshot, der den Status für jeden Schritt im Workflow sowie die Ein- und Ausgaben in der erweiterten Aktion „E-Mail senden“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Um die unformatierten Ein- und Ausgaben für diesen Schritt weiter zu überprüfen, wählen Sie **Unformatierte Eingaben anzeigen** oder **Unformatierte Ausgaben anzeigen** aus.

1. Um die Debugsitzung zu beenden, wählen Sie im menü **Ausführen** den Eintrag **Debuggen beenden** (UMSCHALT+F5) aus.

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Zurückgeben einer Antwort an den Aufrufer

Um eine Antwort an den Aufrufer zurückzugeben, der eine Anforderung an Ihre Logik-App gesendet hat, können Sie die integrierte [Antwortaktion](../connectors/connectors-native-reqres.md) für einen Workflow verwenden, der mit dem Anforderungstrigger gestartet wird.

1. Wählen Sie im Logik-App-Designer unter der Aktion **E-Mail senden** die Option **Neuer Schritt** aus.

   Die Eingabeaufforderung **Vorgang auswählen** wird im Designer angezeigt, und der Bereich **Aktion hinzufügen** wird erneut geöffnet, sodass Sie die nächste Aktion auswählen können.

1. Vergewissern Sie sich, dass im Bereich **Aktion hinzufügen** unter dem Suchfeld **Aktion auswählen** die Option **Integriert** ausgewählt ist. Geben Sie `response` in das Suchfeld ein, und wählen Sie die Aktion **Antwort** aus.

   ![Screenshot, der den Logik-App-Designer mit ausgewählter Antwortaktion zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Wenn die Aktion **Antwort** im Designer angezeigt wird, wird automatisch der Detailbereich der Aktion geöffnet.

   ![Screenshot, der den Logik-App-Designer mit geöffnetem Detailbereich der Aktion „Antwort“ und die auf den Wert der Eigenschaft „Text“ der Aktion „E-Mail senden“ festgelegte Eigenschaft „Text“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Geben Sie auf der Registerkarte **Parameter** die erforderlichen Informationen für die Funktion an, die Sie aufrufen möchten.

   In diesem Beispiel wird der Wert der Eigenschaft **Text** zurückgegeben, der von der Aktion **E-Mail senden** ausgegeben wird.

   1. Klicken Sie in das Eigenschaftsfeld **Text**, damit die Liste dynamischer Inhalte und die verfügbaren Ausgabewerte des vorherigen Auslösers und der Aktionen im Workflow angezeigt werden.

      ![Screenshot, der den Detailbereich der Aktion „Antwort“ mit dem Mauszeiger in der Eigenschaft „Text“ zeigt, sodass die Liste dynamischer Inhalte angezeigt wird.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Wählen Sie in der Liste dynamischer Inhalte unter **E-Mail senden** die Option **Text** aus.

      ![Screenshot, der die offene Liste dynamischer Inhalte zeigt. In der Liste ist unter der Kopfzeile „E-Mail senden“ der Ausgabewert „Text“ ausgewählt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Wenn Sie fertig sind, wird die Eigenschaft **Text** der Antwortaktion auf den Ausgabewert von **Text** für die Aktion **E-Mail senden** festgelegt.

      ![Screenshot, der den Status für jeden Schritt im Workflow sowie die Ein- und Ausgaben in der erweiterten Aktion „Antwort“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Wählen Sie im Designer **Speichern** aus.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Erneutes Testen Ihrer Logik-App

Nachdem Sie Aktualisierungen an Ihrer Logik-App vorgenommen haben, können Sie einen weiteren Test ausführen, indem Sie den Debugger in Visual Studio erneut ausführen und eine weitere Anforderung senden, um Ihre aktualisierte Logik-App auszulösen, ähnlich wie die Schritte unter [Debuggen und Testen Ihrer Logik-App](#debug-test-locally).

1. Öffnen Sie auf der Symbolleiste von Visual Studio Code das Menü **Ausführen**, und wählen Sie **Debuggen starten** (F5) aus.

1. Senden Sie in Postman oder Ihrem Tool zum Erstellen und Senden von Anforderungen eine weitere Anforderung, um Ihren Workflow auszulösen.

1. Wenn Sie einen zustandsbehafteten Workflow erstellt haben, überprüfen Sie auf der Übersichtsseite des Workflows den Status der letzten Ausführung. Wählen Sie zum Anzeigen des Status, der Ein- und Ausgaben für jeden Schritt in dieser Ausführung die Schaltfläche mit den Auslassungspunkten ( **...** ) für diese Ausführung aus, und wählen Sie **Ausführung anzeigen** aus.

   Hier ist beispielsweise der schrittweise Status einer Ausführung, nachdem der Beispielworkflow mit der Antwortaktion aktualisiert wurde.

   ![Screenshot, der den Status für jeden Schritt im aktualisierten Workflow sowie die Ein- und Ausgaben in der erweiterten Aktion „Antwort“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Um die Debugsitzung zu beenden, wählen Sie im menü **Ausführen** den Eintrag **Debuggen beenden** (UMSCHALT+F5) aus.

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

In Visual Studio Code können Sie Ihr Projekt direkt in Azure bereitstellen, das Ihre Logik-App mit dem neuen Ressourcentyp **Logik-App (Vorschau)** veröffentlicht. Ähnlich wie bei der Funktions-App-Ressource in Azure Functions erfordert die Bereitstellung für diesen neuen Ressourcentyp, dass Sie einen [Hostingplan und Tarif](../app-service/overview-hosting-plans.md) auswählen, die Sie während der Bereitstellung einrichten können. Weitere Informationen zu Hostingplänen und Preisen finden Sie in diesen Themen:

* [Hochskalieren einer App in Azure App Service](../app-service/manage-scale-up.md)
* [Skalierung und Hosting von Azure Functions](../azure-functions/functions-scale.md)

Sie können Ihre Logik-App als neue Ressource veröffentlichen, wodurch automatisch alle zusätzlichen erforderlichen Ressourcen erstellt werden, z. B. ein [Azure-Speicherkonto, ähnlich den Funktions-App-Anforderungen](../azure-functions/storage-considerations.md). Alternativ können Sie Ihre Logik-App in einer zuvor bereitgestellten **Logik-App**-Ressource (Vorschau) veröffentlichen, die diese Logik-App überschreibt.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Veröffentlichen als neue Logik-App (Vorschau)-Ressource

1. Wählen Sie in der Visual Studio Code-Symbolleiste das Azure-Symbol aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Azure: Logic Apps (Vorschau)** die Option **In Logik-App bereitstellen** aus.

   ![Screenshot, der den Bereich „Azure: Logic Apps (Vorschau)“ und die Symbolleiste des Bereichs mit ausgewähltem „In Logik-App bereitstellen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Wählen Sie in der Liste, die Visual Studio Code öffnet, aus den folgenden Optionen aus:

   * **Neue Logik-App (Vorschau) in Azure erstellen** (schnell)
   * **Neue Logik-App (Vorschau) in Azure Advanced erstellen**
   * Eine zuvor bereitgestellte **Logik-App (Vorschau)** -Ressource, falls vorhanden

   Dieses Beispiel wird mit **Neue Logik-App (Vorschau) in Azure Advanced erstellen** fortgesetzt.

   ![Screenshot, der den Bereich „Azure: Logic Apps (Vorschau)“ und eine Liste mit ausgewähltem „Neue Logik-App (Vorschau) in Azure erstellen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Um Ihre neue **Logik-App (Vorschau)** -Ressource zu erstellen, gehen Sie wie folgt vor:

   1. Geben Sie einen global eindeutigen Namen für Ihre neue Logik-App an, wobei es sich um den Namen handelt, der für die **Logik-App (Vorschau)** -Ressource verwendet werden soll. In diesem Beispiel wird `example-logic-app-preview` verwendet.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Vorschau)“ und eine Aufforderung zur Eingabe eines Namens für die neue, zu erstellende Logik-App zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Wählen Sie einen Hostingplan für Ihre neue Logik-App aus, entweder [**App Service-Plan**](../azure-functions/functions-scale.md#app-service-plan) oder [**Premium**](../azure-functions/functions-scale.md#premium-plan). In diesem Beispiel wird **App Service-Plan** ausgewählt.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Vorschau)“und eine Aufforderung zur Auswahl von „App Service-Plan“ oder „Premium“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Erstellen Sie einen neuen App Service-Plan, oder wählen Sie einen vorhandenen Plan aus. In diesem Beispiel wird **Neuen App Service-Plan erstellen** ausgewählt.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Vorschau)“und eine Aufforderung zum „Neuen App Service-Plan erstellen“ oder zum Auswählen eines vorhandenen App Service-Plans zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Geben Sie einen Namen für Ihren App Service-Plan an, und wählen Sie dann einen [Tarif](../app-service/overview-hosting-plans.md) für den Plan aus. In diesem Beispiel wird der Plan **F1 Free** ausgewählt.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Vorschau)“und eine Aufforderung zur Auswahl eines Tarifs zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Um eine optimale Leistung zu erzielen, suchen Sie dieselbe Ressourcengruppe wie die für die Bereitstellung des Projekts verwendete, und wählen Sie sie aus.

      > [!NOTE]
      > Obwohl Sie eine andere Ressourcengruppe erstellen oder verwenden können, könnte sich dies negativ auf die Leistung auswirken. Wenn Sie eine andere Ressourcengruppe erstellen oder auswählen, nach der Anzeige der Bestätigungsaufforderung aber abbrechen, wird Ihre Bereitstellung ebenfalls abgebrochen.

   1. Wählen Sie für zustandsbehaftete Workflows **Neues Speicherkonto erstellen** oder ein vorhandenes Speicherkonto aus.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Vorschau)“und eine Aufforderung zum Erstellen oder Auswählen eines Speicherkontos zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Zur Vereinfachung der Diagnoseprotokollierungs- und Ablaufverfolgungsfunktion können Sie eine vorhandene Application Insights-Ressource auswählen. Andernfalls können Sie **Neue Application Insights-Ressource erstellen** auswählen oder Application Insights im Azure-Portal einrichten, nachdem Sie Ihre App bereitgestellt haben.

      Stellen Sie vor der Bereitstellung sicher, dass Sie das `logLevel`-Objekt dem `logging`-Objekt in der Datei **host.json** hinzugefügt haben, die sich auf der Stammebene Ihres Projekts befindet, und legen Sie den `Host.Triggers.Workflow` auf `Information` fest, z. B.:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      So sieht die Datei **host.json** ungefähr aus:

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   Wenn Sie fertig sind, beginnt Visual Studio Code mit der Erstellung und Bereitstellung der Ressourcen, die für das Veröffentlichen Ihrer Logik-App erforderlich sind.

1. Um den Bereitstellungsprozess zu überwachen und zu überprüfen, wählen Sie im Menü **Ansicht** die Option **Ausgabe** aus. Wählen Sie in der Liste der Symbolleiste des Ausgabefensters **Azure Logic Apps** aus.

   ![Screenshot, der das Ausgabefenster mit in der Symbolleistenliste ausgewähltem „Azure Logic Apps“ zusammen mit Bereitstellungsfortschritt und -statuswerten zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Wenn Visual Studio Code die Bereitstellung Ihres Logik-App-Workflows in Azure abschließt, wird diese Meldung angezeigt:

   ![Screenshot, der eine Meldung zeigt, dass die Bereitstellung in Azure erfolgreich abgeschlossen wurde.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Herzlichen Glückwunsch, ihre Logik-App ist jetzt live in Azure und standardmäßig aktiviert.

Als Nächstes erfahren Sie, wie Sie diese Aufgaben ausführen:

* [Finden Ihrer bereitgestellten Logik-App im Azure-Portal](#find-manage-deployed-workflows-portal) oder [in Visual Studio Code](#find-manage-deployed-workflows-vs-code).

* [Aktivieren des Ausführungsverlauf für zustandslose Logik-App-Workflows](#run-history).

* [Aktivieren der Überwachung für bereitgestellte **Logik-App**-Ressourcen (Vorschau)](#enable-monitoring).

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Suchen und Verwalten bereitgestellter Logik-Apps in Visual Studio Code

In Visual Studio Code können Sie alle bereitgestellten Logik-Apps in Ihrem Azure-Abonnement anzeigen, unabhängig davon, ob es sich um den ursprünglichen **Logic Apps**- oder den **Logik-App (Vorschau)** -Ressourcentyp handelt, und Aufgaben auswählen, die Ihnen bei der Verwaltung dieser Logik-Apps helfen. Für den Zugriff auf beide Ressourcentypen benötigen Sie aber beide Erweiterungen für Visual Studio Code: **Azure Logic Apps** und **Azure Logic Apps (Vorschau)** .

1. Wählen Sie auf der linken Symbolleiste das Azure-Symbol aus. Wählen Sie im Bereich **Azure: Logic Apps (Vorschau)** Ihr Abonnement, in dem alle bereitgestellten Logik-Apps für dieses Abonnement angezeigt werden.

1. Suchen Sie die Logik-App, die Sie verwalten möchten, und wählen Sie sie aus. Öffnen Sie das Kontextmenü der Logik-App, und wählen Sie die Aufgabe aus, die Sie ausführen möchten.

   Beispielsweise können Sie Aufgaben wie das Beenden, Starten, Neustarten oder Löschen Ihrer bereitgestellten Logik-App auswählen.

   ![Screenshot, der Visual Studio Code dem geöffneten Bereich der Erweiterung „Azure Logic Apps (Vorschau)“ und dem bereitgestellten Workflow zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Um alle Workflows in der Logik-App anzuzeigen, erweitern Sie Ihre Logik-App und dann den Knoten **Workflows**.

1. Um einen bestimmten Workflow anzuzeigen, öffnen Sie das Kontextmenü des Workflows, und wählen Sie **Im Designer öffnen** aus. Dadurch wird der Workflow im schreibgeschützten Modus geöffnet.

   Um den Workflow zu bearbeiten, haben Sie folgende Optionen:

   * Öffnen Sie in Visual Studio Code die **workflow.json**-Datei des Projekts im Logik-App-Designer, nehmen Sie Ihre Änderungen vor, und stellen Sie Ihre Logik-App erneut in Azure bereit.

   * [Suchen Sie im Azure-Portal nach Ihrer Logik-App](#find-manage-deployed-workflows-portal), und öffnen Sie sie. Suchen, bearbeiten und speichern Sie den Workflow.

1. Um die bereitgestellte Logik-App im Azure-Portal zu öffnen, öffnen Sie das Kontextmenü der Logik-App, und wählen Sie **Im Portal öffnen** aus.

   Das Azure-Portal wird in Ihrem Browser geöffnet, Sie werden automatisch beim Portal angemeldet, wenn Sie bei Visual Studio Code angemeldet sind, und Ihre Logik-App wird angezeigt.

   ![Screenshot, der die Seite des Azure-Portals für Ihre Logik-App in Visual Studio Code zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Sie können sich auch gesondert beim Azure-Portal anmelden, Ihre Logik-App über das Suchfeld des Portals suchen und dann Ihre Logik-App aus der Ergebnisliste auswählen.

   ![Screenshot, der das Azure-Portal und die Suchleiste mit Suchergebnissen für die bereitgestellte Logik-App zeigt, die ausgewählt angezeigt wird.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Suchen und Verwalten bereitgestellter Logik-Apps im Portal

Im Azure-Portal können Sie alle bereitgestellten Logik-Apps anzeigen, die in Ihrem Azure-Abonnement enthalten sind, unabhängig davon, ob es sich um den ursprünglichen **Logic Apps**-Ressourcentyp oder den **Logik-App (Vorschau)** -Ressourcentyp handelt. Zurzeit wird jeder Ressourcentyp in Azure als separate Kategorien organisiert und verwaltet. Um Logik-Apps zu finden, die vom Ressourcentyp **Logik-App (Vorschau)** sind, gehen Sie wie folgt vor:

1. Geben Sie in das Suchfeld des Azure-Portals `logic app preview` ein. Wenn die Ergebnisliste angezeigt wird, wählen Sie unter **Dienste** die Option **Logik-App (Vorschau)** aus.

   ![Screenshot, der das Suchfeld des Azure-Portals mit dem Suchtext „Logik-App Vorschau“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Suchen Sie im Bereich **Logik-App (Vorschau)** die Logik-App, die Sie aus Visual Studio Code bereitgestellt haben, und wählen Sie sie aus.

   ![Screenshot, der das Azure-Portal und die in Azure bereitgestellten Logic App (Vorschau)-Ressourcen zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Das Azure-Portal öffnet die Seite der einzelnen Ressource für die ausgewählte Logik-App.

   ![Screenshot, der die Ressourcenseite Ihres Logik-App-Workflows im Azure-Portal zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Um die Workflows in dieser Logik-App auszuwählen, wählen im Menü der Logik-App **Workflows** aus.

   Im Bereich **Workflows** werden alle in der aktuellen Logik-App vorhandenen Workflows angezeigt. Dieses Beispiel zeigt den Workflow, den Sie in Visual Studio Code erstellt haben.

   ![Screenshot, der eine „Logik-App (Vorschau)“-Ressourcenseite mit geöffnetem Bereich „Workflows“ und dem bereitgestellten Workflow zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Um einen Workflow anzuzeigen, wählen Sie im Bereich **Workflows** diesen Workflow aus.

   Der Workflowbereich wird geöffnet und zeigt weitere Informationen und Aufgaben an, die Sie für diesen Workflow ausführen können.

   Um beispielsweise die Schritte in dem Workflow anzuzeigen, wählen Sie **Designer** aus.

   ![Screenshot, der den Bereich „Übersicht“ des ausgewählten Workflows zeigt, während im Menü „Workflow“ der ausgewählte Befehl „Designer“ angezeigt wird.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Der Logik-App-Designer wird geöffnet und zeigt den Workflow an, den Sie in Visual Studio Code erstellt haben. Sie können jetzt Änderungen an diesem Workflow im Azure-Portal vornehmen.

   ![Screenshot, der den Logik-App-Designer und den aus Visual Studio Code bereitgestellten Workflow zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Hinzufügen eines Workflows zu bereitgestellten Logik-Apps

Über das Azure-Portal können Sie einer **Logik-App**-Ressource (Vorschau), die Sie aus Visual Studio Code bereitgestellt haben, leere Workflows hinzufügen und diese Workflows im Azure-Portal erstellen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) Ihre bereitgestellte **Logik-App (Vorschau)** -Ressource, und wählen Sie sie aus.

1. Wählen Sie im Menü der Logik-App **Workflows** aus. Wählen Sie im Bereich **Workflows** den Befehl **Hinzufügen** aus.

   ![Screenshot, der den Bereich „Workflows“ der ausgewählten Logik-App und die Symbolleiste mit ausgewähltem Befehl „Hinzufügen“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Geben Sie im Bereich **Neuer Workflow** einen Namen für den Workflow an. Wählen Sie entweder **Zustandsbehaftet** oder **Zustandslos** **>** **Erstellen** aus.

   Nachdem Azure ihren neuen Workflow bereitgestellt hat, der im Bereich **Workflows** angezeigt wird, wählen Sie diesen Workflow aus, um Verwaltungsaufgaben und andere Aufgaben auszuführen, z. B. das Öffnen des Logik-App-Designers oder der Codeansicht.

   ![Screenshot, der den ausgewählten Workflow mit Verwaltungs- und Überprüfungsoptionen zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Wenn Sie z. B. den Designer für einen neuen Workflow öffnen, wird ein leerer Zeichenbereich angezeigt. Sie können diesen Workflow jetzt im Azure-Portal erstellen.

   ![Screenshot, der den Logik-App-Designer und einen leeren Workflow zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Ausführungsverlauf für zustandslose Logik-App-Workflows

Damit Sie einen zustandslosen Logik-App-Workflow leichter debuggen können, können Sie den Ausführungsverlauf für diesen Workflow entweder in Visual Studio Code oder im Azure-Portal aktivieren und dann den Ausführungsverlauf wieder deaktivieren, wenn Sie fertig sind.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Für einen zustandslosen Logik-App-Workflow in Visual Studio Code

Wenn Sie den zustandslosen Logik-App-Workflow lokal in Visual Studio Code bearbeiten und ausführen, führen Sie folgende Schritte aus:

1. Suchen und erweitern Sie in Ihrem Projekt den Ordner **workflow-designtime**. Suchen und öffnen Sie die Datei **local.settings.json**.

1. Fügen Sie die Eigenschaft `Workflows.{yourWorkflowName}.operationOptions` hinzu, und legen Sie deren Wert auf `WithStatelessRunHistory` fest, z. B.:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Um den Ausführungsverlauf zu deaktivieren, wenn Sie fertig sind, löschen Sie entweder die Eigenschaft `Workflows.{yourWorkflowName}.OperationOptions` und deren Wert, oder Sie legen die Eigenschaft auf `None` fest.

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Für einen zustandslosen Logik-App-Workflow im Azure-Portal

Wenn Sie Ihr Projekt bereits im Azure-Portal bereitgestellt haben, führen Sie die folgenden Schritte aus:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) Ihre **Logik-App (Vorschau)** -Ressource, und öffnen Sie sie.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Konfiguration** aus.

1. Wählen Sie auf der Registerkarte **Anwendungseinstellungen** die Option **Neue Anwendungseinstellung** aus.

1. Geben Sie im Bereich **Anwendungseinstellung hinzufügen/bearbeiten** in das Feld **Name** den Namen dieser Vorgangsoption ein: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Geben Sie in dass Feld **Wert** den folgenden Wert ein: `WithStatelessRunHistory`

   Beispiel:

   ![Screenshot, der das Azure-Portal und die Logik-App-Ressource (Vorschau) mit dem geöffneten Bereich „Konfiguration“ > „Neue Anwendungseinstellung“ < „Anwendungseinstellung hinzufügen/bearbeiten“ und der auf „WithStatelessRunHistory“ festgelegten Option „Workflow.{NameIhresWorkflows}.OperationOptions“ zeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Wenn Sie fertig sind, wählen Sie **OK**. Wählen Sie im Bereich **Konfiguration** den Befehl **Speichern** aus.

Fahren Sie mit dem nächsten Abschnitt fort, um die Überwachung der bereitgestellten Logik-App-Ressource (Vorschau) zu aktivieren.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Aktivieren der Überwachung für bereitgestellte Logik-App (Vorschau)-Ressourcen

Um die Überwachung für eine bereitgestellte **Logik-App**-Ressource (Vorschau) zu aktivieren, führen Sie die folgenden Schritte aus:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die bereitgestellte **Logik-App (Vorschau)** -Ressource, und wählen Sie sie aus.

1. Wählen Sie im Menü dieser Ressource unter **API** die Option **CORS** aus.

1. Fügen Sie im Bereich **CORS** unter **Zulässige Ursprünge** das Platzhalterzeichen (*) hinzu.

1. Wenn Sie fertig sind, wählen Sie auf der **CORS**-Symbolleiste **Speichern** aus.

   ![Screenshot, der das Azure-Portal mit einer bereitgestellten Logic Apps (Vorschau)-Ressource zeigt. Im Ressourcenmenü ist „CORS“ ausgewählt, mit einem neuen Eintrag für „Zulässige Ursprünge“, der auf das Platzhalterzeichen „*“ festgelegt ist.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Bereitstellen in einem Docker-Container

Mithilfe des [.NET Core CLI-Tools (Befehlszeilenschnittstelle)](/dotnet/core/tools/) können Sie Ihr Projekt erstellen und dann Ihren Build veröffentlichen. Anschließend können Sie einen [Docker-Container](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) als Ziel für die Bereitstellung Ihres Logik-App-Workflows erstellen und verwenden. Weitere Informationen finden Sie in diesen Themen:

* [Introduction to Containers and Docker (Einführung in Container und Docker)](/dotnet/architecture/microservices/container-docker-introduction/)
* [Einführung in .NET and Docker](/dotnet/core/docker/introduction)
* [Docker-Terminologie](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Tutorial: Erste Schritte mit Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Um Ihr Projekt zu erstellen, öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

   `dotnet build -c release`

   Weitere Informationen finden Sie auf der Referenzseite zu [dotnet build](/dotnet/core/tools/dotnet-build/).

1. Veröffentlichen Sie Ihren Build, indem Sie den folgenden Befehl ausführen:

   `dotnet publish -c release`

   Weitere Informationen finden Sie auf der Referenzseite zu [dotnet publish](/dotnet/core/tools/dotnet-publish/).

1. Erstellen Sie einen Docker-Container, indem Sie eine Docker-Datei für einen .NET-Workflow verwenden und den folgenden Befehl ausführen:

   `docker build --tag local/workflowcontainer .`

   Hier sehen Sie eine Docker-Beispieldatei, die eine zustandsbehaftete Logik-App bereitstellt und die Verbindungszeichenfolge für das Azure Storage Konto angibt, das zum Veröffentlichen der Logik-App im Azure-Portal verwendet wurde. Informationen zum Suchen und Kopieren der Verbindungszeichenfolge des Speicherkontos im Azure-Portal finden Sie unter [Anzeigen von Kontozugriffsschlüsseln](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

   ![Screenshot, der das Azure-Portal mit Speicherkonto-Zugriffsschlüsseln und kopierter Verbindungszeichenfolge anzeigt.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Die Verbindungszeichenfolge ähnelt folgendem Beispiel:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Dies ist das Format für die Docker-Datei:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Weitere Informationen finden Sie unter [docker build](https://docs.docker.com/engine/reference/commandline/build/).

1. Speichern Sie die Zeichenfolge an einem sicheren Ort, sodass Sie die Zeichenfolge später den **local.settings.json**-Dateien in dem Projekt hinzufügen können, das Sie zum Erstellen Ihrer Logik-App in Visual Studio Code verwenden.

1. Führen Sie den Container lokal mithilfe des folgenden Befehls aus:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Weitere Informationen finden Sie unter [docker run](https://docs.docker.com/engine/reference/commandline/run/).

1. Um die Rückruf-URL für den Anforderungstrigger abzurufen, senden Sie diese Anforderung:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   Der Wert <*master-key*> ist in dem Azure-Speicherkonto definiert, das Sie für `AzureWebJobsStorage` in der Datei **azure-webjobs-secrets/{deployment-name}/host.json** festgelegt haben, in der Sie den Wert im folgenden Abschnitt finden können:

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Unterschiede im Verhalten von geschachtelten zustandsbehafteten und geschachtelten zustandslosen Logik-Apps

Sie können einen [Logik-App-Workflow aus anderen Logik-App-Workflows heraus aufrufbar machen](../logic-apps/logic-apps-http-endpoint.md), die in derselben **Logik-App-Ressource (Vorschau)** vorhanden sind, indem Sie den [Anforderungstrigger](../connectors/connectors-native-reqres.md), [HTTP-Webhook](../connectors/connectors-native-webhook.md)-Trigger oder verwaltete Connectortrigger verwenden, die vom [Typ ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) sind und HTTPS-Anforderungen empfangen können.

Im Folgenden sind die Verhaltensmuster aufgeführt, die geschachtelte Logik-App-Workflows einhalten können, nachdem ein übergeordneter Workflow einen untergeordneten Workflow aufgerufen hat:

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

## <a name="limits"></a>Grenzwerte

Obwohl viele [vorhandene Limits für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) für diesen Ressourcentyp identisch sind, finden Sie hier die Unterschiede in dieser öffentlichen Vorschauerweiterung:

* Verwaltete Connectors: 50 Anforderungen pro Minute und Verbindung

* Für die [Inlinecodeaktion für JavaScript](../logic-apps/logic-apps-add-run-inline-code.md)-Aktion haben sich diese Limits geändert:

  * Das Limit für Codezeichen erhöht sich von 1.024 Zeichen auf 100.000 Zeichen.

  * Die Zeitbeschränkung für das Ausführen des Codes erhöht sich von fünf Sekunden auf 15 Sekunden.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Beheben von Fehlern und Problemen

<a name="400-bad-request"></a>

### <a name="400-bad-request"></a>„400 – Ungültige Anforderung“

Wenn Sie nach einem Fehler in einer Ausführung diese Ausführung in der Überwachungsansicht untersuchen, kann dieser Fehler bei einem Trigger oder einer Aktion mit einem längeren Namen angezeigt werden. Ein solcher Name kann dazu führen, dass der zugrunde liegende URI (Uniform Resource Identifier) das Standardzeichenlimit überschreitet.

Um dieses Problem zu beheben und längere URIs zuzulassen, bearbeiten Sie die Registrierungsschlüssel `UrlSegmentMaxCount` und `UrlSegmentMaxLength` auf Ihrem Computer. Führen Sie dazu die folgenden Schritte aus. Die Standardwerte dieser Schlüssel werden in diesem Thema beschrieben: [Http.sys-Registrierungseinstellungen für Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Speichern Sie Ihre bisherige Arbeit, bevor Sie beginnen. Diese Lösung erfordert nach Abschluss der Änderungen einen Neustart Ihres Computers, damit die Änderungen wirksam werden.

1. Öffnen Sie auf Ihrem Computer das Fenster **Ausführen**, und führen Sie den Befehl `regedit` aus. Dieser öffnet den Registrierungs-Editor.

1. Klicken Sie im Feld **Benutzerkontensteuerung** auf **Ja**, um Änderungen am Computer zuzulassen.

1. Erweitern Sie im linken Bereich unter **Computer** die Knoten im Pfad **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters**, und wählen Sie dann **Parameters** aus.

1. Suchen Sie im rechten Bereich nach den Registrierungsschlüsseln `UrlSegmentMaxCount` und `UrlSegmentMaxLength`.

1. Erhöhen Sie die Werte dieser Schlüssel so, dass die URIs die von Ihnen gewünschten Namen aufnehmen können. Wenn diese Schlüssel nicht vorhanden sind, fügen Sie sie mithilfe der folgenden Schritte zum Ordner **Parameters** hinzu:

   1. Wählen Sie im Kontextmenü für **Parameters** die Option **Neu** > **DWORD-Wert (32-Bit)** aus.

   1. Geben Sie im daraufhin angezeigten Bearbeitungsfeld `UrlSegmentMaxCount` als neuen Schlüsselnamen ein.

   1. Öffnen Sie das Kontextmenü des neuen Schlüssels, und wählen Sie **Ändern** aus.

   1. Geben Sie im angezeigten Feld **DWORD-Wert (32-Bit) bearbeiten** unter **Wertdaten** den gewünschten Schlüsselwert im Hexadezimal- oder Dezimalformat ein. Der Wert `400` im Hexadezimalformat entspricht beispielsweise dem Wert `1024` im Dezimalformat.

   1. Zum Hinzufügen des Schlüsselwerts `UrlSegmentMaxLength` wiederholen Sie diese Schritte.

   Nachdem Sie die Schlüsselwerte erhöht oder hinzugefügt haben, sieht der Registrierungs-Editor wie folgt aus:

   ![Screenshot des Registrierungs-Editors](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Wenn Sie fertig sind, starten Sie Ihren Computer neu, damit die Änderungen wirksam werden.

## <a name="next-steps"></a>Nächste Schritte

Wir freuen uns, von Ihren Erfahrungen mit dieser öffentlichen Vorschauerweiterung zu hören!

* Bei Fehlern oder Problemen [erstellen Sie Ihre Probleme in GitHub](https://github.com/Azure/logicapps/issues).
* Bei Fragen, Anforderungen, Kommentaren und anderem Feedback [verwenden Sie dieses Feedbackformular](https://aka.ms/lafeedback).
