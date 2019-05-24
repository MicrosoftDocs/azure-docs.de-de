---
title: Einrichten von Stagingumgebungen für Web-Apps in Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Stagingveröffentlichungen Ihrer Web-Apps in Azure App Service verwenden.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 1e09eec89c683d36df49110227488a6413ed371c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955908"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Einrichten von Stagingumgebungen in Azure App Service
<a name="Overview"></a>

> [!NOTE]
> In dieser Schrittanleitung erfahren Sie, wie Sie Slots über eine neue Verwaltungsseite (Vorschauversion) verwalten. Kunden, die mit der bereits vorhandenen Slotverwaltungsseite vertraut sind, können diese weiter wie gewohnt verwenden. 
>

Sie können die Bereitstellung Ihrer Web-App, Web-App unter Linux, Ihres mobilen Back-Ends oder Ihrer API-App in [App Service](https://go.microsoft.com/fwlink/?LinkId=529714) in einem separaten Bereitstellungsslot anstelle des Standardproduktionsslots vornehmen, wenn die Ausführung im Tarif **Standard**, **Premium** oder **I*** des App Service-Plans erfolgt. Bereitstellungsslots sind tatsächlich aktive Apps mit eigenen Hostnamen. Elemente für App-Inhalte und -Konfigurationen können zwischen zwei Bereitstellungsslots, einschließlich des Produktionsslots, ausgetauscht werden. Die Bereitstellung von Anwendungen in einem produktionsfremden Slot hat folgende Vorteile:

* Sie können App-Änderungen in einem Stagingbereitstellungsslot überprüfen, bevor Sie die App in den Produktionsslot überführen.
* Wenn Sie eine App zuerst in einem Slot bereitstellen und dann in den Produktionsslot überführen, ist sichergestellt, dass alle Instanzen erst nach einer Anlaufzeit in den Produktionsslot übernommen werden. Dadurch vermeiden Sie Ausfallzeiten bei der Bereitstellung der App. Die Umleitung des Datenverkehrs erfolgt übergangslos, und es gehen keine Anforderungen aufgrund des Tauschs verloren. Der gesamte Workflow kann durch Konfigurieren von [Automatisch tauschen](#Auto-Swap) automatisiert werden, wenn keine Überprüfung vor dem Tausch erforderlich ist.
* Nach der Überführung enthält der Slot mit der vorherigen Staging-App die vorherige Produktions-App. Wenn die in den Produktionsslot überführten Änderungen nicht Ihren Erwartungen entsprechen, können Sie den gleichen Tausch sofort noch einmal vornehmen, um Ihre „letzte als funktionierend bekannte Website“ zurückzuerhalten.

Jeder App Service-Plantarif unterstützt eine andere Anzahl von Bereitstellungsslots. Informationen zur Ermittlung, wie viele Slots der Tarif Ihrer App unterstützt, finden Sie unter [App Service-Grenzwerte](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Wenn Sie Ihre App auf einen anderen Tarif skalieren möchten, muss der Zieltarif die Anzahl von Slots unterstützen, die Ihre App bereits verwendet. Falls Ihre App also beispielsweise mehr als fünf Slots besitzt, können Sie sie nicht auf den Tarif **Standard** herunterskalieren, da der Tarif **Standard** nur fünf Bereitstellungsslots unterstützt.

<a name="Add"></a>

## <a name="add-slot"></a>Hinzufügen eines Slots
Die App muss im Tarif **Standard**, **Premium** oder **I*** ausgeführt werden, um mehrere Bereitstellungsslots aktivieren zu können.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) die Seite [Ressourcen](../azure-resource-manager/manage-resources-portal.md#manage-resources) Ihrer App.

2. Wählen Sie im linken Navigationsbereich die Option **Bereitstellungsslots (Vorschau)** aus, und klicken Sie anschließend auf **Slot hinzufügen**.
   
    ![Neuen Bereitstellungsslot hinzufügen](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Wenn die App nicht bereits im Tarif **Standard**, **Premium** oder **I*** ausgeführt wird, wird eine Meldung angezeigt, in der auf die unterstützten Tarife für die Veröffentlichung in einer Stagingumgebung hingewiesen wird. An diesem Punkt können Sie **Upgrade** auswählen und zur Registerkarte **Skalierung** der App navigieren, bevor Sie den Vorgang fortsetzen.
   > 

3. Weisen Sie dem Slot im Dialogfeld **Slot hinzufügen** einen Namen zu, und wählen Sie aus, ob Sie die App-Konfiguration eines anderen vorhandenen Bereitstellungsslots klonen möchten. Klicken Sie auf **Hinzufügen**, um den Vorgang fortzusetzen.
   
    ![Konfigurationsquelle](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Sie können die Konfiguration aus einem beliebigen vorhandenen Slot klonen. Zu den Einstellungen, die geklont werden können, zählen App-Einstellungen, Verbindungszeichenfolgen, Sprachframework-Versionen, Websockets, die HTTP-Version und die Bitanzahl der Plattform.

4. Klicken Sie nach dem Hinzufügen des Slots auf **Schließen**, um das Dialogfeld zu schließen. Der neue Slot wird nun auf der Seite **Bereitstellungsslots (Vorschau)** angezeigt. **Datenverkehr %** ist für den neuen Slot standardmäßig auf „0“ festgelegt, sodass der gesamte Kundendatenverkehr an den Produktionsslot weitergeleitet wird.

5. Klicken Sie auf den neuen Bereitstellungsslot, um die Ressourcenseite dieses Slots zu öffnen.
   
    ![Titel des Bereitstellungsslots](./media/web-sites-staged-publishing/StagingTitle.png)

    Der Stagingslot verfügt genau wie jede andere App Service-App über eine Verwaltungsseite. Sie können die Konfiguration des Slots ändern. Am oberen Seitenrand wird der Name des Slots angezeigt, um Sie daran zu erinnern, dass es sich um den Bereitstellungsslot handelt.

6. Klicken Sie auf der Ressourcenseite des Slots auf die App-URL. Der Bereitstellungsslot besitzt einen eigenen Hostnamen und ist außerdem eine Live-App. Informationen zum Beschränken des öffentlichen Zugriffs auf den Bereitstellungsslot finden Sie unter [Statische Azure App Service-IP-Einschränkungen](app-service-ip-restrictions.md).

Der neue Bereitstellungsslot hat keinen Inhalt. Das gilt auch, wenn Sie die Einstellungen eines anderen Slots klonen. Beispiel: [Für die Veröffentlichung in diesem Slot können Sie Git verwenden](app-service-deploy-local-git.md). Die Bereitstellung im Slot kann aus einem anderen Repository-Branch oder aus einem anderen Repository erfolgen. 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Welche Einstellungen werden ausgetauscht?
Wenn Sie die Konfiguration von einem anderen Bereitstellungsslot klonen, kann die geklonte Konfiguration bearbeitet werden. Darüber hinaus folgen einige Konfigurationselemente bei einem Austausch dem Inhalt (nicht slotspezifisch), während andere Konfigurationselemente nach einem Austausch beim Slot verbleiben (slotspezifisch). Im Anschluss sind die Einstellungen aufgeführt, die sich beim Austauschen der Slots ändert.

**Einstellungen, die ausgetauscht werden**:

* Allgemeine Einstellungen – z. B. Framework-Version, 32/64-Bit-Angabe, WebSockets
* App-Einstellungen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Verbindungszeichenfolgen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Handlerzuordnungen
* Überwachungs- und Diagnoseeinstellungen
* Öffentliche Zertifikate
* WebJobs-Inhalte
* Hybridverbindungen *
* VNET-Integration *
* Dienstendpunkte *
* Azure CDN *

Features, die mit einem * markiert sind, sollen so konfiguriert werden, dass sie beim Slot verbleiben. 

**Einstellungen, die nicht ausgetauscht werden**:

* Veröffentlichungsendpunkte
* Benutzerdefinierte Domänennamen
* Private Zertifikate und SSL-Bindungen
* Skalierungseinstellungen
* WebJobs-Planer
* IP-Einschränkungen
* Always On
* Protokolleinstellungen (HTTP**S**, TLS-Version, Clientzertifikate)
* Einstellungen für das Diagnoseprotokoll
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

Wenn Sie eine App-Einstellung oder eine Verbindungszeichenfolge so konfigurieren möchten, dass sie bei einem bestimmten Slot verbleibt (also nicht ausgetauscht wird), navigieren Sie zur Seite **Anwendungseinstellungen** für den gewünschten Slot, und aktivieren Sie das Kontrollkästchen **Sloteinstellung** für die Konfigurationselemente, die bei dem Slot verbleiben sollten. Als slotspezifisch markierte Konfigurationselemente werden von App Service nicht ausgetauscht. 

![Sloteinstellung](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Austauschen von zwei Slots 
Bereitstellungsslots können auf der Seite **Bereitstellungsslots (Vorschau)** Ihrer App ausgetauscht werden. 

Slots können auch auf den Seiten **Übersicht** und **Bereitstellungsslots** ausgetauscht werden (momentan allerdings nur über die alte Benutzeroberfläche). In dieser Anleitung wird die Verwendung der neuen Benutzeroberfläche auf der Seite **Bereitstellungsslots (Vorschau)** behandelt.

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Überführen einer App aus einem Bereitstellungsslot in die Produktion, dass alle Einstellungen im Austauschziel wie gewünscht konfiguriert sind.
> 
> 

Gehen Sie zum Austauschen von Bereitstellungsslots wie folgt vor:

1. Navigieren Sie zur Seite **Bereitstellungsslots (Vorschau)** Ihrer App, und klicken Sie auf **Austauschen**.
   
    ![Schaltfläche Swap](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Im Dialogfeld **Austauschen** werden Einstellungen in den ausgewählten Quell- und Zielslots angezeigt, die ausgetauscht werden.

2. Wählen Sie die gewünschten Slots für **Quelle** und **Ziel** aus. Bei dem Ziel handelt es sich in der Regel um einen Produktionsslot. Klicken Sie auch auf die Registerkarten **Quelländerungen** und **Zieländerungen**, und vergewissern Sie sich, dass die Konfigurationsänderungen Ihren Erwartungen entsprechen. Klicken Sie anschließend auf **Austauschen**, um die Slots umgehend auszutauschen.

    ![Vollständiger Austausch](./media/web-sites-staged-publishing/SwapImmediately.png)

    Wenn Sie sich vor dem tatsächlichen Austausch ansehen möchten, wie der Zielslot mit den neuen Einstellungen funktioniert, klicken Sie nicht auf **Austauschen**, sondern führen Sie die Schritte unter [Mit Vorschau austauschen](#Multi-Phase) aus.

3. Klicken Sie abschließend auf **Schließen**, um das Dialogfeld zu schließen.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Mit Vorschau austauschen (Austausch mit mehreren Phasen)

> [!NOTE]
> „Mit Vorschau austauschen“ wird in Web-Apps unter Linux nicht unterstützt.

Vergewissern Sie sich vor der Überführung in die Produktion (Zielslot), dass die App mit den ausgetauschten Einstellungen funktioniert, bevor der tatsächliche Austausch erfolgt. Der Quellslot wird auch vor Abschluss des Austauschs vorbereitet, was für unternehmenskritische Anwendungen von Vorteil ist.

Bei einem Austausch mit Vorschau geschieht Folgendes, wenn Sie den Austausch starten:

- Der Zielslot bleibt unverändert, sodass die in diesem Slot vorhandene Workload (beispielsweise Produktion) nicht beeinträchtigt wird.
- Die Konfigurationselemente des Zielslots werden auf den Quellslot angewendet (einschließlich der slotspezifischen Verbindungszeichenfolgen und App-Einstellungen).
- Die Workerprozesse für den Quellslot werden unter Verwendung dieser Konfigurationselemente neu gestartet. Sie können zum Quellslot navigieren und sich die mit Konfigurationsänderungen ausgeführte App ansehen.

Wenn Sie den Austausch in einem separaten Schritt abschließen, überführt App Service den vorbereiteten Quellslot in den Zielslot und den Zielslot in den Quellslot. Wenn Sie den Austausch abbrechen, wendet App Service die Konfigurationselemente des Quellslots wieder auf den Quellslot an.

Gehen Sie für einen Austausch mit Vorschau wie folgt vor:

1. Führen Sie die Schritte unter [Überführen von Bereitstellungsslots](#Swap) aus, wählen Sie dabei jedoch **Tausch mit Vorschau ausführen** aus.

    ![Austauschen mit Vorschau](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Das Dialogfeld zeigt, wie sich die Konfiguration im Quellslot ändert (Phase 1) und wie sich der Quell- und der Zielslot ändern (Phase 2).

2. Wenn Sie bereit sind, den Austausch zu starten, klicken Sie auf **Tausch starten**.

    Sie werden im Dialogfeld über den Abschluss von Phase 1 informiert. Navigieren Sie zu `https://<app_name>-<source-slot-name>.azurewebsites.net`, um sich eine Vorschau des Austauschs im Quellslot anzusehen. 

3. Wenn Sie bereit sind, den ausstehenden Austausch abzuschließen, wählen Sie unter **Austauschaktion** die Option **Austausch abschließen** aus, und klicken Sie anschließend auf **Austausch abschließen** .

    Wenn Sie einen ausstehenden Austausch abbrechen möchten, wählen Sie stattdessen **Austausch abbrechen** aus, und klicken Sie anschließend auf **Austausch abbrechen**.

4. Klicken Sie abschließend auf **Schließen**, um das Dialogfeld zu schließen.

Informationen zum Automatisieren eines mehrstufigen Austauschs finden Sie unter „Automatisieren mit PowerShell“.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Ausführen eines Rollbacks für einen Austausch
Sollten nach einem Slotaustausch Fehler im Zielslot (etwa im Produktionsslot) auftreten, stellen Sie für die Slots den Zustand vor dem Austausch wieder her, indem Sie die beiden gleichen Slots sofort erneut austauschen.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurieren des automatischen Austauschs

> [!NOTE]
> Automatisches Austauschen wird in Web-Apps unter Linux nicht unterstützt.

Das Feature „Automatisch tauschen“ rationalisiert DevOps-Szenarien, bei denen Ihre App kontinuierlich ohne Kaltstarts und ohne Ausfallzeiten für Endkunden der App bereitgestellt werden soll. Bei automatischer Überführung eines Slots in die Produktion gilt Folgendes: Sobald Sie Ihre Codeänderungen per Push an diesen Slot übertragen, überführt App Service die App automatisch in die Produktion, nachdem sie im Quellslot vorbereitet wurde.

   > [!NOTE]
   > Es empfiehlt sich, das Feature „Automatisch tauschen“ zunächst in einem produktionsfremden Zielslot zu testen, bevor Sie es für den Produktionsslot konfigurieren.
   > 

Gehen Sie zum Konfigurieren des Features „Automatisch tauschen“ wie folgt vor:

1. Navigieren Sie zur Ressourcenseite Ihrer App. Wählen Sie **Bereitstellungsslots (Vorschau)** > *\<gewünschter Quellslot>* > **Anwendungseinstellungen** aus.
   
2. Wählen Sie unter **Automatisch tauschen** die Option **Ein** und anschließend unter **Slot für automatischen Tausch** den gewünschten Zielslot aus, und klicken Sie dann auf der Befehlsleiste auf **Speichern**. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Führen Sie einen Codepushvorgang an den Quellslot aus. Der automatische Austausch erfolgt nach kurzer Zeit, und die URL Ihres Zielslots spiegelt die Änderung wider.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Benutzerdefinierte Aufwärmphase
Bei Verwendung des Features [Automatisch tauschen](#Auto-Swap) müssen für einige Apps vor dem Austausch unter Umständen benutzerdefinierte Vorbereitungsschritte ausgeführt werden. Mithilfe des Konfigurationselements `applicationInitialization` in „web.config“ können Sie benutzerdefinierte Initialisierungsaktionen angeben, die ausgeführt werden sollen. Der Austausch mit dem Zielslot erfolgt dann erst nach Abschluss dieser benutzerdefinierten Aufwärmphase. Im Folgenden finden Sie ein Beispielfragment aus der Datei „Web.config“.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Weitere Informationen zum Anpassen des `applicationInitialization`-Elements finden Sie unter [Häufigste Bereitstellungsfehler beim Slotaustausch und Vorgehensweise zu deren Behebung](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Sie können das Aufwärmverhalten ferner mit einer oder mehrerer der folgenden [App-Einstellungen](configure-common.md) anpassen:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Der Pfad zum Senden eines Pings zum Aufwärmen Ihrer Website. Fügen Sie diese App-Einstellung durch Angeben eines benutzerdefinierten Pfads hinzu, der mit einem Schrägstrich als Wert beginnt. Beispiel: `/statuscheck`. Standardwert: `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Gültige HTTP-Antwortcodes für den Aufwärmvorgang. Fügen Sie diese App-Einstellung mit einer durch Trennzeichen getrennten Liste mit HTTP-Codes hinzu. Beispiel: `200,202`. Wenn der zurückgegebene Statuscode nicht in der Liste enthalten ist, werden die Aufwärm- und Austauschvorgänge beendet. Standardmäßig sind alle Antwortcodes gültig.

## <a name="monitor-swap"></a>Überwachen des Austauschs

Bei länger dauernden Austauschvorgängen finden Sie Informationen zum Austauschvorgang im [Aktivitätsprotokoll](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Klicken Sie im Portal auf der Ressourcenseite Ihrer App im linken Navigationsbereich auf **Aktivitätsprotokoll**.

Ein Austauschvorgang wird in der Protokollabfrage als `Swap Web App Slots` angezeigt. Sie können die Abfrage erweitern und Untervorgänge oder Fehler auswählen, um die entsprechenden Details anzuzeigen.

## <a name="route-traffic"></a>Weiterleiten von Datenverkehr

Standardmäßig werden alle an die Produktions-URL Ihrer App (`http://<app_name>.azurewebsites.net`) gerichteten Clientanforderungen an den Produktionsslot weitergeleitet. Sie können einen Teil des Datenverkehrs an einen anderen Slot weiterleiten. Dieses Feature ist hilfreich, wenn Sie Benutzerfeedback zu einem neuen Update benötigen, das noch nicht für die Veröffentlichung in der Produktionsumgebung bereit ist.

### <a name="route-production-traffic-automatically"></a>Automatisches Weiterleiten von Produktionsdatenverkehr

Gehen Sie zum automatischen Weiterleiten von Produktionsdatenverkehr wie folgt vor:

1. Navigieren Sie zur Ressourcenseite Ihrer App, und wählen Sie **Bereitstellungsslots (Vorschau)** aus.

2. Geben Sie in der Spalte **Datenverkehr %** des gewünschten Zielslots für die Weiterleitung durch einen Prozentwert (zwischen 0 und 100) an, welcher Anteil des gesamten Datenverkehrs weitergeleitet werden soll. Klicken Sie auf **Speichern**.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

Nach dem Speichern der Einstellung wird der angegebene Prozentsatz von Clients nach dem Zufallsprinzip an den produktionsfremden Slot weitergeleitet. 

Sobald ein Client automatisch an einen bestimmten Slot weitergeleitet wird, ist er für die Dauer der Clientsitzung auf diesen Slot festgelegt. Im Clientbrowser sehen Sie anhand des Cookies `x-ms-routing-name` in Ihren HTTP-Headern, mit welchem Slot Ihre Sitzung verknüpft ist. Anforderungen, die an den Stagingslot weitergeleitet werden, haben das Cookie `x-ms-routing-name=staging`. Anforderungen, die an den Produktionsslot weitergeleitet werden, haben das Cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Manuelles Weiterleiten von Produktionsdatenverkehr

Neben dem automatischen Datenverkehrsrouting kann App Service Anforderungen auch an einen bestimmten Slot weiterleiten. Dies ist hilfreich, wenn Sie Ihren Benutzern ermöglichen möchten, die Beta-App zu nutzen oder die Nutzung zu beenden. Zur manuellen Weiterleitung von Produktionsdatenverkehr wird der Abfrageparameter `x-ms-routing-name` verwendet.

Damit Benutzer die Nutzung Ihrer Beta-App beenden können, können Sie z. B. folgenden Link auf Ihre Webseite bereitstellen:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Die Zeichenfolge `x-ms-routing-name=self` gibt den Produktionsslot an. Wenn der Clientbrowser auf den Link zugreift, wird er nicht nur an den Produktionsslot weitergeleitet, auch jede nachfolgende Anforderung erhält das Cookie `x-ms-routing-name=self`, das die Sitzung mit dem Produktionsslot verknüpft.

Damit Benutzer Ihre Beta-App nutzen können, legen Sie den gleichen Abfrageparameter auf den Namen des nicht produktiven Slots fest, z. B.:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Standardmäßig erhalten neue Slots eine Routingregel von `0%`, die in grau dargestellt. Wenn Sie diesen Wert auf `0%` festlegen (als schwarzer Text dargestellt), können Ihre Benutzer mithilfe des Abfrageparameters `x-ms-routing-name` auf den Stagingslot zugreifen. Sie werden jedoch nicht automatisch an den Slot weitergeleitet, da der Prozentsatz für die Weiterleitung auf 0 festgelegt ist. Dies ist ein erweitertes Szenario, in dem Sie Ihren Stagingslot vor der Öffentlichkeit verbergen können, während Sie gleichzeitig zulassen, dass interne Teams Änderungen am Slot testen.

<a name="Delete"></a>

## <a name="delete-slot"></a>Löschen eines Slots

Navigieren Sie zur Ressourcenseite Ihrer App. Wählen Sie **Bereitstellungsslots (Vorschau)** > *\<zu löschender Slot>* > **Übersicht** aus. Klicken Sie auf der Befehlsleiste auf **Löschen**.  

![Löschen eines Bereitstellungsslots](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatisieren mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Azure über Windows PowerShell bietet, einschließlich Unterstützung der Verwaltung von Bereitstellungsslots für Azure App Service.

Informationen zum Installieren und Konfigurieren von Azure PowerShell sowie zur Authentifizierung von Azure PowerShell mit Ihrem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-web-app"></a>Web-App erstellen
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>Erstellen eines Slots
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Initiieren des Austauschs mit Überprüfung (Austausch in mehreren Phasen) und Anwenden der Konfiguration des Zielslots auf den Quellslot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Abbrechen des ausstehenden Austauschs (Austausch mit Überprüfung) und Wiederherstellen der Konfiguration des Quellslots
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Überführen von Bereitstellungsslots
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Überwachen von Austauschereignissen im Aktivitätsprotokoll
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>Löschen eines Slots
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatisieren mithilfe der Befehlszeilenschnittstelle

Informationen zu Befehlen der [Azure CLI](https://github.com/Azure/azure-cli) für Bereitstellungsslots finden Sie unter [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Nächste Schritte
[Blockieren des Zugriffs auf produktionsfremde Slots](app-service-ip-restrictions.md)
