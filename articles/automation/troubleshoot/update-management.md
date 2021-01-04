---
title: Beheben von Problemen bei der Azure Automation-Updateverwaltung
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit der Azure Automation-Updateverwaltung beheben.
services: automation
ms.date: 12/04/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: c6d0f38eaa25f2fe033a5e2cf48ee6daa51fcbe6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929275"
---
# <a name="troubleshoot-update-management-issues"></a>Beheben von Problemen bei der Updateverwaltung

In diesem Artikel werden Probleme beschrieben, die bei der Bereitstellung des Features für die Updateverwaltung auf Ihren Computern auftreten können. Es gibt eine Agent-Problembehandlung für den Hybrid Runbook Worker-Agent, mit dem das zugrunde liegende Problem bestimmt werden kann. Informationen dazu finden Sie unter [Beheben von Problemen mit dem Windows Update-Agent](update-agent-issues.md) und [Beheben von Problemen mit dem Linux Update-Agent](update-agent-issues-linux.md). Informationen zu anderen Problemen bei der Bereitstellung von Features finden Sie unter [Behandeln von Problemen beim Onboarding von Lösungen](onboarding.md).

>[!NOTE]
>Wenn beim Bereitstellen der Updateverwaltung auf einem Windows-Computer Probleme auftreten, öffnen Sie die Windows-Ereignisanzeige, und sehen Sie sich auf dem lokalen Computer das **Operations Manager**-Ereignisprotokoll unter **Anwendungs- und Dienstprotokolle** an. Suchen Sie nach Ereignissen mit der Ereignis-ID 4502 und Ereignisdetails, die `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` enthalten.

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Szenario: Linux-Updates, die als ausstehend angezeigt werden, und die, die installiert sind, unterscheiden sich

### <a name="issue"></a>Problem

Für Ihren Linux-Computer zeigt die Updateverwaltung bestimmte Updates an, die unter der Klassifizierung **Sicherheit** und **Sonstige** verfügbar sind. Wenn aber ein Zeitplan für Updates auf dem Computer ausgeführt wird, z. B. um nur Updates zu installieren, die der Klassifizierung **Sicherheit** entsprechen, unterscheiden sich die installierten Updates von den zuvor angezeigten Updates, die dieser Klassifizierung entsprechen, oder sind eine Teilmenge davon.

### <a name="cause"></a>Ursache

Wenn eine Bewertung der ausstehenden Betriebssystemupdates für Ihren Linux-Computer durchgeführt wird, werden [Open Vulnerability and Assessment Language](https://oval.mitre.org/) (OVAL)-Dateien, die vom Linux-Distributionsanbieter bereitgestellt werden, von der Updateverwaltung für die Klassifizierung verwendet. Die Kategorisierung erfolgt für Linux-Updates in **Sicherheit** oder **Sonstige**, basierend auf den OVAL-Dateien, die Updates angeben, die Sicherheitsprobleme oder Sicherheitsrisiken beheben. Wenn aber der Zeitplan für Updates ausgeführt wird, wird er auf dem Linux-Computer ausgeführt, wobei er den geeigneten Paket-Manager wie YUM, APT oder ZYPPER verwendet, um diese zu installieren. Der Paket-Manager für die Linux-Distribution verfügt möglicherweise über einen anderen Mechanismus zum Klassifizieren von Updates, sodass sich die Ergebnisse von denen, die die Updateverwaltung über die OVAL-Dateien erzielt hat, unterscheiden können.

### <a name="resolution"></a>Lösung

Sie können den Linux-Computer, die anwendbaren Updates und deren Klassifizierung manuell über den Paket-Manager der Distribution überprüfen. Um zu verstehen, welche Updates von Ihrem Paket-Manager mit **Sicherheit** klassifiziert werden, führen Sie die folgenden Befehle aus.

Für YUM gibt der folgende Befehl eine Liste der Updates ungleich NULL zurück, die von Red Hat mit **Sicherheit** kategorisiert sind. Beachten Sie, dass im Falle von CentOS immer eine leere Liste zurückgegeben wird und keine Sicherheitsklassifizierung vorkommt.

```bash
sudo yum -q --security check-update
```

Für ZYPPER gibt der folgende Befehl eine Liste der Updates ungleich NULL zurück, die von SUSE mit **Sicherheit** kategorisiert sind.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

Für APT gibt der folgende Befehl eine Liste der Updates ungleich NULL zurück, die von Canonical für Ubuntu Linux-Distributionen mit **Sicherheit** kategorisiert sind.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

Mit dieser Liste führen Sie den Befehl `grep ^Inst` aus, um alle ausstehenden Sicherheitsupdates zu erhalten.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Szenario: Sie erhalten die Fehlermeldung „Fehler beim Aktivieren der Updatelösung“.

### <a name="issue"></a>Problem

Wenn Sie versuchen, die Updateverwaltung in Ihrem Automation-Konto zu aktivieren, wird der folgende Fehler ausgegeben:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Ursache

Dieser Fehler kann aus den folgenden Gründe auftreten:

* Die Netzwerkfirewallanforderungen für den Log Analytics-Agent sind möglicherweise nicht ordnungsgemäß konfiguriert. Dadurch tritt beim Auflösen der DNS-URLs möglicherweise ein Fehler beim Agent auf.

* Das Ziel der Updateverwaltung ist falsch konfiguriert, und der Computer empfängt die Updates nicht wie erwartet.

* Sie werden möglicherweise auch feststellen, dass der Computer den Status `Non-compliant` unter **Konformität** anzeigt. Gleichzeitig meldet **Agent Desktop Analytics** den Agent als `Disconnected`.

### <a name="resolution"></a>Lösung

* Führen Sie je nach Betriebssystem die Problembehandlung für [Windows](update-agent-issues.md#troubleshoot-offline) oder [Linux](update-agent-issues-linux.md#troubleshoot-offline) aus.

* Rufen Sie den Abschnitt [Netzwerkkonfiguration](../automation-hybrid-runbook-worker.md#network-planning) auf, um zu ermitteln, welche Adressen und Ports zugelassen werden müssen, damit die Updateverwaltung funktioniert.  

* Suchen Sie nach Bereichskonfigurationsproblemen. Die [Bereichskonfiguration](../update-management/scope-configuration.md) bestimmt, welche Computer für die Updateverwaltung konfiguriert werden. Wenn Ihr Computer zwar in Ihrem Arbeitsbereich, aber nicht in der Updateverwaltung angezeigt wird, müssen Sie die Bereichskonfiguration so festlegen, dass sie auf die Computer ausgerichtet ist. Weitere Informationen zur Bereichskonfiguration finden Sie unter [Integrieren von Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

* Entfernen Sie die Workerkonfiguration, indem Sie die Schritte unter [Entfernen des Hybrid Runbook Workers von einem lokalen Windows-Computer](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) oder [Bereitstellen eines Linux-Hybrid Runbook Workers](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker) ausführen.

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Szenario: Ersetztes Update wird in der Updateverwaltung als fehlend angezeigt

### <a name="issue"></a>Problem

Alte Updates werden im Automation-Konto als fehlend angezeigt, selbst wenn sie bereits ersetzt wurden. Ein ersetztes Update ist ein Update, das nicht installiert werden muss, da ein späteres Update verfügbar ist, welches dasselbe Sicherheitsrisiko korrigiert. Die Updateverwaltung ignoriert das ersetzte Update und macht es nicht zugunsten des ersetzenden Updates anwendbar. Informationen zu einem verwandten Problem finden Sie unter [Update ist ersetzt](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Ursache

Ersetzte Updates werden in Windows Server Update Services (WSUS) nicht abgelehnt, sodass sie als nicht anwendbar angesehen werden können.

### <a name="resolution"></a>Lösung

Wenn ein ersetztes Update zu 100 Prozent nicht mehr anwendbar ist, sollten Sie den Genehmigungsstatus dieses Updates in WSUS in `Declined` ändern. So ändern Sie den Genehmigungsstatus für alle Ihre Updates:

1. Wählen Sie im Automation-Konto **Updateverwaltung** aus, um den Computerstatus anzuzeigen. Siehe [Anzeigen von Updatebewertungen](../update-management/view-update-assessments.md).

2. Überprüfen Sie das ersetzte Update, um sicherzustellen, dass es zu 100 Prozent nicht anwendbar ist.

3. [Lehnen Sie das Update auf dem WSUS-Server ab](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates), an den die Übermittlung der Computer erfolgt.

4. Wählen Sie **Computer** aus, und erzwingen Sie in der Spalte **Konformität** eine erneute Überprüfung auf Konformität. Weitere Informationen finden Sie unter [Verwalten von Updates für VMs](../update-management/manage-updates-for-vm.md).

5. Wiederholen Sie die obigen Schritte für weitere ersetzte Updates.

6. Bereinigen Sie für Windows Server Update Services (WSUS) mithilfe des WSUS-[Assistenten für die Serverbereinigung](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard) alle ersetzten Updates, um die Infrastruktur zu aktualisieren.

7. Wiederholen Sie dieses Verfahren regelmäßig, um das Anzeigeproblem zu korrigieren und den für die Updateverwaltung verwendeten Datenträgerspeicherplatz zu minimieren.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Szenario: Im Portal werden unter „Updateverwaltung“ keine Computer angezeigt

### <a name="issue"></a>Problem

Ihre Computer weisen die folgenden Symptome auf:

* In der Ansicht „Updateverwaltung“ einer VM wird Ihr Computer als `Not configured` angezeigt.

* Ihre Computer fehlen in der Ansicht „Updateverwaltung“ Ihres Azure Automation-Kontos.

* Es sind Computer vorhanden, die unter **Konformität** als `Not assessed` gezeigt werden. In Azure Monitor-Protokollen werden Heartbeatdaten für den Hybrid Runbook Worker, nicht jedoch für die Updateverwaltung angezeigt.

### <a name="cause"></a>Ursache

Dieses Problem kann durch lokale Konfigurationsprobleme oder eine falsch konfigurierte Bereichskonfiguration verursacht werden. Mögliche spezifische Ursachen:

* Möglicherweise müssen Sie den Hybrid Runbook Worker erneut registrieren und installieren.

* Möglicherweise wurde ein in Ihrem Arbeitsbereich definiertes Kontingent erreicht, sodass eine weitere Datenspeicherung verhindert wird.

### <a name="resolution"></a>Lösung

1. Führen Sie je nach Betriebssystem die Problembehandlung für [Windows](update-agent-issues.md#troubleshoot-offline) oder [Linux](update-agent-issues-linux.md#troubleshoot-offline) aus.

2. Vergewissern Sie sich, dass Ihr Computer Meldungen an den richtigen Arbeitsbereich zurückgibt. Anweisungen zum Überprüfen dieses Punkts finden Sie unter [Überprüfen der Agent-Konnektivität mit Azure Monitor](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Stellen Sie außerdem sicher, dass dieser Arbeitsbereich mit Ihrem Azure Automation-Konto verknüpft ist. Rufen Sie dafür Ihr Automation-Konto auf, und wählen Sie unter **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

3. Stellen Sie sicher, dass die Computer im Log Analytics-Arbeitsbereich aufgeführt werden, der mit Ihrem Automation-Konto verknüpft ist. Führen Sie im Log Analytics-Arbeitsbereich die folgende Abfrage aus.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Wenn Ihr Computer nicht in den Abfrageergebnissen aufgeführt ist, wurde er in letzter Zeit nicht eingecheckt. Es liegt wahrscheinlich ein Problem mit der lokalen Konfiguration vor, und Sie sollten [den Agent neu installieren](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

5. Wenn Ihr Computer in den Abfrageergebnissen angezeigt wird, suchen Sie nach Bereichskonfigurationsproblemen. Die [Bereichskonfiguration](../update-management/scope-configuration.md) bestimmt, welche Computer für die Updateverwaltung konfiguriert werden.

6. Wenn Ihr Computer zwar in Ihrem Arbeitsbereich, aber nicht in der Updateverwaltung angezeigt wird, müssen Sie die Bereichskonfiguration so festlegen, dass sie auf die Computer ausgerichtet ist. Informationen hierzu finden Sie unter [Integrieren von Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

7. Führen Sie in Ihrem Arbeitsbereich diese Abfrage aus.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Wenn das Ergebnis `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` angezeigt wird, wurde ein für Ihren Arbeitsbereich definiertes Kontingent erreicht, wodurch das Speichern von Daten verhindert wird. Wechseln Sie in Ihrem Arbeitsbereich unter **Nutzung und geschätzte Kosten** zu **Datenvolumenverwaltung**, und ändern Sie das Kontingent, oder heben Sie es auf.

9. Wird das Problem dadurch immer noch nicht behoben, führen Sie die Schritte unter [Bereitstellen eines Windows Hybrid Runbook Workers](../automation-windows-hrw-install.md) aus, um den Hybrid Worker für Windows neu zu installieren. Informationen zu Linux finden Sie unter [Bereitstellen eines Linux Hybrid Runbook Workers](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Szenario: Der Automation-Ressourcenanbieter für Abonnements kann nicht registriert werden.

### <a name="issue"></a>Problem

Beim Arbeiten mit Featurebereitstellungen in Ihrem Automation-Konto tritt der folgende Fehler auf:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Ursache

Der Automation-Ressourcenanbieter ist nicht im Abonnement registriert.

### <a name="resolution"></a>Lösung

Führen Sie im Azure-Portal die folgenden Schritte aus, um den Automation-Ressourcenanbieter zu registrieren:

1. Wählen Sie unten im Portal in der Azure-Dienstliste die Option **Alle Dienste** und anschließend in der Dienstgruppe „Allgemein“ die Option **Abonnements** aus.

2. Wählen Sie Ihr Abonnement aus.

3. Wählen Sie unter **Einstellungen** die Option **Ressourcenanbieter** aus.

4. Überprüfen Sie in der Liste der Ressourcenanbieter, ob der Ressourcenanbieter Microsoft.Automation registriert ist.

5. Wenn dieser nicht aufgeführt ist, registrieren Sie den Anbieter Microsoft.Automation mithilfe der Schritte unter [Beheben von Fehlern bei der Ressourcenanbieterregistrierung](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Szenario: Bei einem geplanten Update mit einem dynamischen Zeitplan wurden einige Computer ausgelassen.

### <a name="issue"></a>Problem

In eine Updatevorschau eingeschlossene Computer werden nicht komplett in der Liste der Computer aufgeführt, die während einer geplanten Ausführung gepatcht wurden.

### <a name="cause"></a>Ursache

Das Problem kann eine der folgenden Ursachen haben:

* Die im Gültigkeitsbereich einer dynamischen Abfrage definierten Abonnements sind für den registrierten Automation-Ressourcenanbieter nicht konfiguriert.

* Die Computer waren beim Ausführen des Zeitplans nicht verfügbar, oder sie verfügten nicht über die entsprechenden Tags.

### <a name="resolution"></a>Lösung

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Abonnements sind nicht für den registrierten Automation-Ressourcenanbieter konfiguriert.

Wenn Ihr Abonnement nicht für den Automation-Ressourcenanbieter konfiguriert ist, können Sie keine Informationen zu Computern in diesem Abonnement abfragen oder abrufen. Führen Sie die folgenden Schritte aus, um die Registrierung für das Abonnement zu überprüfen.

1. Rufen Sie im [Azure-Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) die Azure-Liste der Dienste.

2. Wählen Sie **Alle Dienste** und anschließend in der Gruppe „Allgemeiner Dienst“ die Option **Abonnements** aus.

3. Suchen Sie das Abonnement, das im Bereich für Ihre Bereitstellung definiert ist.

4. Wählen Sie unter **Einstellungen** die Option **Ressourcenanbieter** aus.

5. Überprüfen Sie, ob der Ressourcenanbieter Microsoft.Automation registriert ist.

6. Wenn dieser nicht aufgeführt ist, registrieren Sie den Anbieter Microsoft.Automation mithilfe der Schritte unter [Beheben von Fehlern bei der Ressourcenanbieterregistrierung](../../azure-resource-manager/templates/error-register-resource-provider.md).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Computer sind bei Ausführung des Zeitplans nicht verfügbar oder nicht ordnungsgemäß markiert

Führen Sie das folgende Verfahren aus, wenn Ihr Abonnement für den Automation-Ressourcenanbieter konfiguriert ist, beim Ausführen des Updatetezeitplans mit den angegebenen [dynamischen Gruppen](../update-management/configure-groups.md) jedoch einige Computer ausgelassen wurden.

1. Öffnen Sie im Azure-Portal das Automation-Konto, und wählen Sie **Updateverwaltung** aus.

2. Bestimmen Sie anhand des Verlaufs der [Updateverwaltung](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) den genauen Zeitpunkt, zu dem die Updatebereitstellung ausgeführt wurde.

3. Verwenden Sie für Computer, die mutmaßlich von der Updateverwaltung ausgelassen wurden, Azure Resource Graph (ARG), um [Computeränderungen zu bestimmen](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details).

4. Suchen Sie über einen längeren Zeitraum (z. B. einen Tag) vor dem Ausführen der Updatebereitstellung nach Änderungen.

5. Überprüfen Sie die Suchergebnisse auf systemische Änderungen der Computer in diesem Zeitraum, z. B. Löschungen oder Aktualisierungen. Durch derartige Bearbeitungen können Status oder Tags von Computern geändert werden, sodass Computer bei der Bereitstellung von Updates nicht in der Liste der Computer ausgewählt werden.

6. Passen Sie die Computer- und Ressourceneinstellungen ggf. an, um Probleme mit dem Status oder Tags von Computern zu beheben.

7. Führen Sie den Updatezeitplan erneut aus, um sicherzustellen, dass die Bereitstellung mit den angegebenen dynamischen Gruppen alle Computer einschließt.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Szenario: Erwartete Computer werden in der Vorschau für die dynamische Gruppe nicht angezeigt.

### <a name="issue"></a>Problem

VMs für ausgewählte Bereiche einer dynamischen Gruppe werden nicht in der Vorschauliste des Azure-Portals angezeigt. Diese Liste enthält alle Computer, die mit einer ARG-Abfrage für die ausgewählten Bereiche abgerufen werden. Die Bereiche werden nach Computern gefiltert, auf denen Hybrid Runbook Worker installiert sind und für die Sie über Zugriffsberechtigungen verfügen.

### <a name="cause"></a>Ursache

Mögliche Ursachen für dieses Problem:

* Sie verfügen nicht über die richtigen Zugriffsberechtigungen für die ausgewählten Bereiche.
* Die ARG-Abfrage gibt nicht die erwarteten Computer zurück.
* Hybrid Runbook Worker ist nicht auf den Computern installiert.

### <a name="resolution"></a>Lösung 

#### <a name="incorrect-access-on-selected-scopes"></a>Inkorrekter Zugriff auf ausgewählte Bereiche

Im Azure-Portal werden nur Computer angezeigt, für die Sie in einem bestimmten Bereich über Schreibzugriff verfügen. Wenn Sie nicht über den entsprechenden Zugriff für einen Bereich verfügen, informieren Sie sich unter [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer über das Azure-Portal](../../role-based-access-control/quickstart-assign-role-user-portal.md).

#### <a name="arg-query-doesnt-return-expected-machines"></a>Die ARG-Abfrage gibt nicht die erwarteten Computer zurück.

Führen Sie die Schritte unten aus, um festzustellen, ob Ihre Abfragen ordnungsgemäß funktionieren.

1. Führen Sie im Azure-Portal auf dem Blatt „Resource Graph-Explorer“ eine ARG-Abfrage aus, die wie unten veranschaulicht formatiert ist. Diese Abfrage imitiert die Filter, die Sie beim Erstellen der dynamischen Gruppe in der Updateverwaltung ausgewählt haben. Siehe [Verwenden dynamischer Gruppen mit der Updateverwaltung](../update-management/configure-groups.md).

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Beispiel:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Überprüfen Sie, ob die gesuchten Computer in den Abfrageergebnissen aufgeführt sind.

3. Wenn die Computer nicht aufgeführt sind, liegt wahrscheinlich ein Problem mit dem in der dynamischen Gruppe ausgewählten Filter vor. Passen Sie die Gruppenkonfiguration entsprechend den jeweiligen Anforderungen an.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker ist auf Computern nicht installiert.

Computer werden in Ergebnissen einer ARG-Abfrage aufgeführt, sie sind jedoch nicht in der Vorschau der dynamischen Gruppe aufgelistet. In diesem Fall sind die Computer möglicherweise nicht als Hybrid Worker festgelegt und können daher keine Updateverwaltungs- und Azure Automation-Aufträge ausführen. Gehen Sie wie folgt vor, um sicherzustellen, dass die erwarteten Computer als Hybrid Runbook Worker eingerichtet sind:

1. Wechseln Sie im Azure-Portal zum Automation-Konto für einen Computer, der nicht ordnungsgemäß angezeigt wird.

2. Wählen Sie unter **Prozessautomatisierung** die Option **Hybrid Worker-Gruppen** aus.

3. Wählen Sie die Registerkarte **Hybrid Worker-Systemgruppen** aus.

4. Überprüfen Sie, ob der Hybrid Worker für diesen Computer vorhanden ist.

5. Wenn der Computer nicht als Hybrid Worker eingerichtet ist, nehmen Sie mithilfe der Anweisungen unter [Automatisieren von Ressourcen im Datencenter oder in der Cloud mit Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) die erforderlichen Anpassungen vor.

6. Legen Sie den Computer als Mitglied der Hybrid Runbook Worker-Gruppe fest.

7. Wiederholen Sie die obigen Schritte für alle Computer, die nicht in der Vorschau angezeigt werden.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Szenario: Komponenten für die Updateverwaltung sind aktiviert, während weiterhin angezeigt wird, dass die VM konfiguriert wird

### <a name="issue"></a>Problem

Die folgende Nachricht wird nach 15 Minuten nach Beginn der Bereitstellung auch weiterhin auf einem Computer angezeigt:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Ursache

Dieser Fehler kann aus den folgenden Gründe auftreten:

* Die Kommunikation mit dem Automation-Konto wird blockiert.

* Es ist ein doppelter Computername mit unterschiedlichen Quellcomputer-IDs vorhanden. Dieses Szenario tritt auf, wenn ein virtueller Computer mit einem bestimmten Computernamen in verschiedenen Ressourcengruppen erstellt wird und Meldungen an den gleichen Logistics Agent-Arbeitsbereich im Abonnement sendet.

* Das bereitzustellende VM-Image stammt möglicherweise von einem geklonten Computer, der nicht über die Systemvorbereitung (Sysprep) mit installiertem Log Analytics-Agent für Windows vorbereitet wurde.

### <a name="resolution"></a>Lösung

Um die Bestimmung des genauen Problems mit der VM zu unterstützen, führen Sie in dem Log Analytics-Arbeitsbereich, der mit Ihrem Automation-Konto verknüpft ist, die folgende Abfrage aus.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Kommunikation mit dem Automation-Konto blockiert

Rufen Sie den Abschnitt [Netzwerkplanung](../update-management/overview.md#ports) auf, um zu ermitteln, welche Adressen und Ports zugelassen werden müssen, damit die Updateverwaltung funktioniert.

#### <a name="duplicate-computer-name"></a>Doppelter Computername

Benennen Sie Ihre VMs um, um eindeutige Namen in ihrer Umgebung sicherzustellen.

#### <a name="deployed-image-from-cloned-machine"></a>Bereitgestelltes Image von geklontem Computer

Wenn Sie ein geklontes Image verwenden, weisen verschiedene Computernamen die gleiche Quellcomputer-ID auf. In diesem Fall:

1. Entfernen Sie in Ihrem Log Analytics-Arbeitsbereich den virtuellen Computer aus der gespeicherten Suche für die Bereichskonfiguration `MicrosoftDefaultScopeConfig-Updates`, sofern er angezeigt wird. Gespeicherte Suchen finden Sie unter **Allgemein** in Ihrem Arbeitsbereich.

2. Führen Sie das folgende Cmdlet aus.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Führen Sie `Restart-Service HealthService` aus, um den Integritätsdienst neu zu starten. Mit diesem Vorgang wird der Schlüssel neu erstellt und eine neue UUID generiert.

4. Wenn dieser Ansatz nicht funktioniert, führen Sie zuerst Sysprep auf dem Image aus, und installieren Sie dann den Log Analytics-Agent für Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Szenario: Sie erhalten einen Fehler aufgrund eines verknüpften Abonnements, wenn Sie eine Updatebereitstellung für Computer in einem anderen Azure-Mandanten erstellen.

### <a name="issue"></a>Problem

Sie erhalten beim Versuch, eine Updatebereitstellung für Computer in einem anderen Azure-Mandanten zu erstellen, die folgende Fehlermeldung:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Ursache

Dieser Fehler tritt bei der Erstellung einer Updatebereitstellung auf, bei der Azure-VMs eines anderen Mandanten in eine Updatebereitstellung einbezogen werden.

### <a name="resolution"></a>Lösung

Verwenden Sie die folgende Problemumgehung, um die Planung durchzuführen. Sie können das Cmdlet [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) mit dem `ForUpdateConfiguration`-Parameter verwenden, um einen Zeitplan zu erstellen. Verwenden Sie dann das Cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration), und übergeben Sie die Computer im anderen Mandanten an den Parameter `NonAzureComputer`. Dies wird anhand des folgenden Beispiels veranschaulicht:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Szenario: Unerklärliche Neustarts

### <a name="issue"></a>Problem

Auch wenn Sie die Option **Steuerelement neu starten** auf **Nie neu starten** festgelegt haben, werden die Computer nach der Installation von Updates immer noch neu gestartet.

### <a name="cause"></a>Ursache

Windows Update lässt sich über mehrere Registrierungsschlüssel modifizieren, mit denen das Neustartverhalten geändert werden kann.

### <a name="resolution"></a>Lösung

Überprüfen Sie die unter [Konfigurieren automatischer Updates durch Bearbeiten der Registrierung](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) und [Zum Verwalten des Neustarts verwendete Registrierungsschlüssel](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) aufgeführten Registrierungsschlüssel, um sicherzustellen, dass Ihre Computer ordnungsgemäß konfiguriert sind.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Szenario: Für einen Computer in einer Updatebereitstellung wird die Fehlermeldung „Fehler beim Starten“ angezeigt.

### <a name="issue"></a>Problem

Ein Computer zeigt einen `Failed to start`-Status an. Wenn Sie sich die Detailinformationen für den Computer ansehen, wird der folgende Fehler angezeigt:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Ursache

Dieser Fehler kann aus einem der folgenden Gründe auftreten:

* Der Computer ist nicht mehr vorhanden.
* Der Computer ist ausgeschaltet und nicht erreichbar.
* Für den Computer besteht ein Netzwerkverbindungsproblem. Daher ist der Hybrid Worker auf dem Computer nicht erreichbar.
* Ein Update des Log Analytics-Agents hat zu einer Änderung der Quellcomputer-ID geführt.
* Die Ausführung des Updates wurde gedrosselt, wenn das Limit von 200 gleichzeitigen Aufträgen in einem Automation-Konto erreicht wurde. Jede Bereitstellung kann als Auftrag betrachtet werden, und jeder Computer in einer Updatebereitstellung zählt als Auftrag. Jeder andere Automatisierungsauftrag oder jede Updatebereitstellung, der bzw. die in Ihrem Automation-Konto ausgeführt wird, wird auf das Limit für gleichzeitige Aufträge angerechnet.

### <a name="resolution"></a>Lösung

Verwenden Sie [dynamische Gruppen](../update-management/configure-groups.md) (falls vorhanden) für Ihre Updatebereitstellungen. Außerdem können Sie die folgenden Schritte ausführen.

1. Vergewissern Sie sich, dass der Computer oder Server die [Anforderungen](../update-management/overview.md#client-requirements) erfüllt.
2. Überprüfen Sie die Konnektivität mit dem Hybrid Runbook Worker mithilfe der Problembehandlung für den Hybrid Runbook Worker-Agent. Informationen zur Problembehandlung finden Sie unter [Beheben von Problemen mit dem Update-Agent](update-agent-issues.md).

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Szenario: Updates werden ohne Bereitstellung installiert.

### <a name="issue"></a>Problem

Wenn Sie einen Windows-Computer in der Updateverwaltung registrieren, sehen Sie Updates, die ohne Bereitstellung installiert werden.

### <a name="cause"></a>Ursache

Unter Windows werden Updates automatisch installiert, sobald sie verfügbar sind. Dieses Verhalten kann verwirrend sein, wenn Sie ein Update nicht für die Bereitstellung auf dem Computer eingeplant haben.

### <a name="resolution"></a>Lösung

Der `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`-Registrierungsschlüssel hat die Standardeinstellung „4“: `auto download and install`.

Für Updateverwaltungsclients sollten Sie diesen Schlüssel auf „3“ festlegen: `auto download but do not auto install`.

Weitere Informationen finden Sie unter [Automatische Updates konfigurieren](/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Szenario: Computer ist bereits bei einem anderen Konto registriert.

### <a name="issue"></a>Problem

Sie erhalten die folgende Fehlermeldung:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Ursache

Der Computer wurde bereits in einem anderen Arbeitsbereich für die Updateverwaltung bereitgestellt.

### <a name="resolution"></a>Lösung

1. Befolgen Sie die Schritte unter [Im Portal werden unter „Updateverwaltung“ keine Computer angezeigt](#nologs), um sicherzustellen, dass der Computer Meldungen an den richtigen Arbeitsbereich zurückgibt.
2. Bereinigen Sie Artefakte auf dem Computer durch [Löschen der Hybrid Runbook-Gruppe](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group), und wiederholen Sie den Vorgang.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Szenario: Der Computer kann nicht mit dem Dienst kommunizieren.

### <a name="issue"></a>Problem

Sie erhalten eine der folgenden Fehlermeldungen:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Ursache

Möglicherweise wird die Netzwerkkommunikation durch einen Proxy, ein Gateway oder eine Firewall blockiert.

### <a name="resolution"></a>Lösung

Überprüfen Sie Ihr Netzwerk, und stellen Sie sicher, dass die entsprechenden Ports und Adressen zugelassen sind. Unter [Netzwerkanforderungen](../automation-hybrid-runbook-worker.md#network-planning) finden Sie eine Liste der Ports und Adressen, die von Updateverwaltung und Hybrid Runbook Workers benötigt werden.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Szenario: Selbstsigniertes Zertifikat kann nicht erstellt werden.

### <a name="issue"></a>Problem

Sie erhalten eine der folgenden Fehlermeldungen:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Ursache

Der Hybrid Runbook Worker konnte kein selbstsigniertes Zertifikat generieren.

### <a name="resolution"></a>Lösung

Überprüfen Sie, ob das Systemkonto über Lesezugriff auf den Ordner **C:\ProgramData\Microsoft\Crypto\RSA** verfügt, und versuchen Sie es noch mal.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Szenario: Beim geplanten Update ist ein MaintenanceWindowExceeded-Fehler aufgetreten.

### <a name="issue"></a>Problem

Das Standardwartungsfenster für Updates beträgt 120 Minuten. Sie können das Wartungsfenster auf maximal sechs Stunden oder 360 Minuten erhöhen.

### <a name="resolution"></a>Lösung

Um zu ermitteln, warum dieses Problem während der Ausführung eines Updates nach dem erfolgreichen Start auftritt, [überprüfen Sie die Auftragsausgabe](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) des von der Ausführung betroffenen Computers. Möglicherweise finden Sie spezifische Fehlermeldungen von Ihren Computern, die Sie untersuchen können, um dann Maßnahmen zu ergreifen.  

Bearbeiten Sie alle fehlgeschlagenen, geplanten Updatebereitstellungen, und vergrößern Sie das Wartungsfenster.

Weitere Informationen zu Wartungsfenstern finden Sie unter [Installieren von Updates](../update-management/deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Szenario: Computer wird als „Nicht bewertet“ mit einer HRESULT-Ausnahme angezeigt.

### <a name="issue"></a>Problem

* Für manche Ihrer Computer wird `Not assessed` unter **Konformität** und darunter eine Ausnahmemeldung angezeigt.
* Im Portal wird ein HRESULT-Fehlercode angezeigt.

### <a name="cause"></a>Ursache

Der Update-Agent (Windows Update-Agent unter Windows, der Paket-Manager für eine Linux-Distribution) ist nicht ordnungsgemäß konfiguriert. Für die Updateverwaltung wird der Update-Agent des Computers benötigt, um die erforderlichen Updates, den Status des Patches und die Ergebnisse der bereitgestellten Patches bereitzustellen. Ohne diese Informationen kann die Updateverwaltung keine richtigen Meldungen für die Patches zurückgeben, die benötigt bzw. installiert werden.

### <a name="resolution"></a>Lösung

Versuchen Sie, Updates lokal auf dem Computer auszuführen. Wenn dieser Vorgang fehlschlägt, bedeutet dies normalerweise, dass ein Konfigurationsfehler beim Update-Agent vorliegt.

Dieses Problem wird häufig durch Probleme mit der Netzwerkkonfiguration und der Firewall verursacht. Führen Sie die folgenden Prüfungen aus, um das Problem zu beheben.

* Lesen Sie für Linux die entsprechende Dokumentation, um sicherzustellen, dass Sie den Netzwerkendpunkt Ihres Paketrepositorys erreichen können.

* Überprüfen Sie bei Windows Ihre Agent-Konfiguration, wie unter [Updates werden nicht vom Intranetendpunkt heruntergeladen (WSUS/SCCM) ](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm) aufgeführt.

  * Wenn die Computer für Windows Update konfiguriert sind, stellen Sie sicher, dass Sie die in [Probleme im Zusammenhang mit HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy) beschriebenen Endpunkte erreichen können.
  * Wenn die Computer für Windows Server Update Services (WSUS) konfiguriert sind, stellen Sie sicher, dass Sie den über den [WUServer-Registrierungsschlüssel](/windows/deployment/update/waas-wu-settings) konfigurierten WSUS-Server erreichen können.

Wird ein HRESULT angezeigt, doppelklicken Sie auf die rot angezeigte Ausnahme, um die vollständige Ausnahmemeldung anzuzeigen. Suchen Sie in der folgenden Tabelle nach möglichen Lösungen oder empfohlenen Aktionen.

|Ausnahme  |Lösung oder Aktion  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Suchen Sie den entsprechenden Fehlercode in der [Windows Update-Fehlercodeliste](https://support.microsoft.com/help/938205/windows-update-error-code-list), um weitere Details zur Ursache der Ausnahme zu erfahren.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Diese Fehler deuten auf Netzwerkkonnektivitätsprobleme hin. Stellen Sie sicher, dass Ihr Computer über Netzwerkkonnektivität zur Updateverwaltung verfügt. Im Abschnitt [Netzwerkplanung](../update-management/overview.md#ports) finden Sie eine Liste der erforderlichen Ports und Adressen.        |
|`0x8024001E`| Der Updatevorgang konnte nicht abgeschlossen werden, weil der Dienst oder das System heruntergefahren wurden.|
|`0x8024002E`| Der Windows Update-Dienst ist deaktiviert.|
|`0x8024402C`     | Wenn Sie einen WSUS-Server verwenden, stellen Sie sicher, dass die Registrierungswerte für `WUServer` und `WUStatusServer` unter dem Registrierungsschlüssel `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` den richtigen WSUS-Server angeben.        |
|`0x80072EE2`|Es liegt ein Netzwerkkonnektivitätsproblem oder ein Problem bei der Kommunikation mit einem konfigurierten WSUS-Server vor. Überprüfen Sie die WSUS-Einstellungen, und stellen Sie sicher, dass der Client auf den Dienst zugreifen kann.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Stellen Sie sicher, dass der Windows Update-Dienst (wuauserv) ausgeführt wird und nicht deaktiviert ist.        |
|`0x80070005`| Ein Fehler vom Typ „Zugriff verweigert“ kann einen der folgenden Gründe haben:<br> Infizierter Computer<br> Windows Update wurde nicht ordnungsgemäß konfiguriert.<br> Dateiberechtigungsfehler im Ordner „%WinDir%\SoftwareDistribution“<br> Auf dem Systemlaufwerk (C:) ist nicht genügend Speicherplatz vorhanden.
|Weitere generische Ausnahmen     | Suchen Sie im Internet nach möglichen Lösungen, und arbeiten Sie mit Ihrem lokalen IT-Support zusammen.         |

In der Datei **%Windir%\Windowsupdate.log** können Sie ebenfalls mögliche Ursachen finden. Weitere Informationen zum Lesen des Protokolls finden Sie unter [Lesen der Datei „Windowsupdate.log“](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Darüber hinaus können Sie die [Windows Update-Problembehandlung](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) herunterladen und ausführen, um zu überprüfen, ob auf dem Computer Probleme mit Windows Update bestehen.

> [!NOTE]
> In der Dokumentation zur [Windows Update-Problembehandlung](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) ist angegeben, dass sie für Windows-Clients verwendet wird. Sie funktioniert aber auch auf Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Szenario: Updateausführung gibt den Status „Fehler“ zurück (Linux).

### <a name="issue"></a>Problem

Die Ausführung eines Updates wird gestartet, wobei jedoch Fehler auftreten.

### <a name="cause"></a>Ursache

Mögliche Ursachen:

* Der Paket-Manager ist fehlerhaft.
* Der Update-Agent (WUA für Windows, distributionsspezifischer Paket-Manager für Linux) ist falsch konfiguriert.
* Bestimmte Pakete haben Probleme mit cloudbasiertem Patching.
* Der Computer ist nicht erreichbar.
* Updates enthielten Abhängigkeiten, die nicht aufgelöst wurden.

### <a name="resolution"></a>Lösung

Wenn während der Ausführung eines Updates nach dem erfolgreichen Start Fehler auftreten, [überprüfen Sie die Auftragsausgabe](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) des von der Ausführung betroffenen Computers. Möglicherweise finden Sie spezifische Fehlermeldungen von Ihren Computern, die Sie untersuchen können, um dann Maßnahmen zu ergreifen. Updateverwaltung setzt voraus, dass der Paket-Manager für eine erfolgreiche Bereitstellung von Updates fehlerfrei ist.

Wenn bestimmte Patches, Pakete oder Updates unmittelbar vor dem Fehlschlagen des Auftrags angezeigt werden, können Sie versuchen, diese Elemente aus der nächsten Updatebereitstellung [auszuschließen](../update-management/deploy-updates.md#schedule-an-update-deployment). Informationen zum Sammeln von Protokollinformationen aus Windows Update finden Sie unter [Windows Update-Protokolldateien](/windows/deployment/update/windows-update-logs).

Wenn Sie ein Patchproblem nicht beheben können, erstellen Sie eine Kopie der Datei **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log**, und bewahren Sie sie für Problembehandlungszwecke auf, bevor die nächste Bereitstellung von Updates beginnt.

## <a name="patches-arent-installed"></a>Patches sind nicht installiert.

### <a name="machines-dont-install-updates"></a>Computer installieren keine Updates.

Versuchen Sie, Updates direkt auf dem Computer auszuführen. Wenn der Computer die Updates nicht anwenden kann, sehen Sie sich die [Liste mit potenziellen Fehlern im Handbuch zur Problembehandlung](#hresult) an.

Wenn Updates lokal ausgeführt werden, versuchen Sie, den Agent auf dem Computer zu entfernen und neu zu installieren, bevor Sie die Anweisungen unter [Entfernen einer VM für die Updateverwaltung](../update-management/remove-vms.md) ausführen.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Ich weiß, dass Updates verfügbar sind, diese werden auf meinen Computern aber nicht als verfügbar angezeigt.

Dies geschieht häufig, wenn Computer so konfiguriert sind, dass sie Updates von WSUS oder Microsoft Endpoint Configuration Manager erhalten, aber WSUS und Configuration Manager die Updates nicht genehmigt haben.

Sie können überprüfen, ob Computer für WSUS und SCCM konfiguriert sind. Vergleichen Sie dazu den `UseWUServer`-Registrierungsschlüssel mit den Registrierungsschlüsseln im Abschnitt [Konfigurieren von automatischen Updates durch Bearbeiten der Registrierung](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s) dieses Artikels.

Wenn Updates in WSUS nicht genehmigt sind, werden sie nicht installiert. Sie können durch die Ausführung der folgenden Abfrage in Log Analytics nach nicht genehmigten Updates suchen.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Updates werden als installiert angezeigt, ich kann sie aber nicht auf meinem Computer finden.

Updates werden oft durch andere Updates ersetzt. Weitere Informationen finden Sie unter [Update wird ersetzt](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) in der Anleitung für die Windows Update-Problembehandlung.

### <a name="installing-updates-by-classification-on-linux"></a>Installieren von Updates durch Klassifizierung unter Linux

Die Bereitstellung von Updates für Linux durch Klassifizierung („Kritische und Sicherheitsupdates“) hat wichtige Einschränkungen, insbesondere für CentOS. Diese Einschränkungen sind auf der [Übersichtsseite für die Updateverwaltung](../update-management/overview.md#linux) dokumentiert.

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 fehlt dauerhaft.

KB2267602 ist das [Windows Defender-Definitionsupdate](https://www.microsoft.com/wdsi/definitions). Es wird täglich aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt wird oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten.

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit.
* Erstellen Sie einen Azure-Supportfall. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten** aus.
