---
title: Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor | Microsoft-Dokumentation
description: Zur Bewahrung Ihrer bisherigen Investitionen in System Center Operations Manager sowie zur Nutzung erweiterter Funktionen mit Log Analytics können Sie Operations Manager mit Ihrem Arbeitsbereich verknüpfen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2020
ms.openlocfilehash: 2a4f24da51b9e9e78c3df3e7d1437a380306e300
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95975570"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Zur Bewahrung Ihrer bisherigen Investitionen in [System Center Operations Manager](/system-center/scom/key-concepts?view=sc-om-1807) sowie zur Nutzung erweiterter Funktionen mit Azure Monitor können Sie Operations Manager mit Ihrem Log Analytics-Arbeitsbereich verknüpfen. Sie können also von den Möglichkeiten der Azure Monitor-Protokollfunktion profitieren und weiterhin Operations Manager nutzen, um Folgendes zu erreichen:

* Integritätsüberwachung für Ihre IT-Dienste mit Operations Manager
* Verwaltung der Integration mit Ihren ITSM-Lösungen (mit Unterstützung von Incident Management und Problemverwaltung)
* Verwaltung des Lebenszyklus von Agents, die auf lokalen und öffentlichen Cloud-IaaS-VMs bereitgestellt und mit Operations Manager überwacht werden

Die Verknüpfung mit System Center Operations Manager kommt Ihrer Dienstbetriebsstrategie zugute, da Sie dadurch beim Sammeln, Speichern und Analysieren von Daten aus Operations Manager von der Geschwindigkeit und Effizienz von Azure Monitor profitieren. Azure Monitor-Protokollabfragen sind beim Korrelieren und Ermitteln problembedingter Fehler sowie beim Ermitteln von Wiederholungen hilfreich und unterstützen somit Ihren vorhandenen Problemverwaltungsprozess. Die Flexibilität der Abfrageengine bei der Untersuchung von Leistungs-, Ereignis- und Warnungsdaten sowie umfassende Dashboards und Berichtsfunktionen zur sinnvollen Betrachtung der Daten machen Azure Monitor zu einer optimalen Ergänzung von Operations Manager.

Die Agents, die Daten an die Verwaltungsgruppe von Operations Manager melden, sammeln Daten von Ihren Servern auf der Grundlage der [Log Analytics-Datenquellen](agent-data-sources.md) und -Lösungen, die Sie in Ihrem Arbeitsbereich aktiviert haben. Je nach aktivierter Lösung werden die Daten entweder direkt von einem Operations Manager-Verwaltungsserver oder (aufgrund des Umfangs der Daten, die im mit einem Agent verwalteten System gesammelt werden) direkt vom Agent an einen Log Analytics-Arbeitsbereich gesendet. Der Verwaltungsserver leitet die Daten direkt an den Dienst weiter. Sie werden nicht in die Betriebs- oder Data Warehouse-Datenbank geschrieben. Sollte die Verbindung zwischen einem Verwaltungsserver und Azure Monitor getrennt werden, werden die Daten lokal zwischengespeichert, bis die Kommunikation wiederhergestellt ist. Wenn der Verwaltungsserver aufgrund einer geplanten Wartung oder eines ungeplanten Ausfalls offline ist, wird die Verbindung mit Azure Monitor von einem anderen Verwaltungsserver in der Verwaltungsgruppe übernommen.  

Das nachstehende Diagramm zeigt die Verbindung zwischen den Verwaltungsservern und Agents in einer Verwaltungsgruppe von System Center Operations Manager und Azure Monitor, einschließlich der Richtung und der Ports.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Wenn es laut Ihren IT-Sicherheitsrichtlinien unzulässig ist, dass Computer in Ihrem Netzwerk eine Internetverbindung herstellen, können die Verwaltungsserver so konfiguriert werden, dass sie eine Verbindung mit dem Log Analytics-Gateway herstellen, um Konfigurationsinformationen zu empfangen und gesammelte Daten abhängig von den aktivierten Lösungen zu senden. Weitere Informationen und Schritte zum Konfigurieren der Operations Manager-Verwaltungsgruppe für die Kommunikation mit Azure Monitor über ein Log Analytics-Gateway finden Sie unter [Verbinden von Computern mit Azure Monitor über das Log Analytics-Gateway](./gateway.md).  

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, überprüfen Sie die folgenden Anforderungen.

* Azure Monitor unterstützt nur System Center Operations Manager 2016 und höher, Operations Manager 2012 SP1 UR6 und höher sowie Operations Manager 2012 R2 UR2 und höher. Proxyunterstützung wurde in Operations Manager 2012 SP1 UR7 und Operations Manager 2012 R2 UR3 hinzugefügt.
* Die Integration von System Center Operations Manager 2016 in die US-Government Cloud erfordert ein aktualisiertes Advisor Management Pack, das im Lieferumfang von Updaterollup 2 oder höher enthalten ist. System Center Operations Manager 2012 R2 erfordert ein aktualisiertes Advisor Management Pack, das im Lieferumfang von Updaterollup 3 oder höher enthalten ist.
* Alle Operations Manager-Agents müssen die Mindestanforderungen in Bezug auf die Unterstützung erfüllen. Überprüfen Sie, ob Agents über das Mindestupdate verfügen. Andernfalls schlägt die Windows-Agent-Kommunikation ggf. fehl, und das Operations Manager-Ereignisprotokoll generiert Fehler.
* Einen Log Analytics-Arbeitsbereich Weitere Informationen finden Sie in der [Übersicht zum Log Analytics-Arbeitsbereich](design-logs-deployment.md).
* Sie authentifizieren sich bei Azure mit einem Konto, das Mitglied der Rolle [Log Analytics-Mitwirkender](manage-access.md#manage-access-using-azure-permissions) ist.

* Unterstützte Regionen: Nur die folgenden Azure-Regionen werden von System Center Operations Manager unterstützt, was die Verbindungsherstellung mit einem Log Analytics-Arbeitsbereich betrifft:
    - USA, Westen-Mitte
    - Australien, Südosten
    - Europa, Westen
    - East US
    - Südostasien
    - Japan, Osten
    - UK, Süden
    - Indien, Mitte
    - Kanada, Mitte
    - USA, Westen 2

>[!NOTE]
>Aktuelle Änderungen an den Azure-APIs werden Kunden daran hindern, die Integration zwischen ihrer Verwaltungsgruppe und Azure Monitor zum ersten Mal erfolgreich zu konfigurieren. Für Kunden, die ihre Verwaltungsgruppe bereits in den Dienst integriert haben, sind Sie nicht betroffen, es sei denn, Sie müssen die vorhandene Verbindung neu konfigurieren.  
>Für die folgenden Versionen von Operations Manager wurde ein neues Management Pack veröffentlicht:
> - Für System Center Operations Manager 2019 ist dieses Management Pack auf den Quellmedien enthalten und wird bei der Einrichtung einer neuen Verwaltungsgruppe oder während eines Upgrades installiert.
>- Das Operations Manager 1801 Management Pack kann auch für Operations Manager 1807 angewendet werden.
>- Laden Sie für System Center Operations Manager 1801 das Management Pack [hier](https://www.microsoft.com/download/details.aspx?id=57173) herunter.
>- Laden Sie für System Center Operations Manager 2016 das Management Pack [hier](https://www.microsoft.com/download/details.aspx?id=57172) herunter.  
>- Laden Sie für System Center Operations Manager 2012 R2 das Management Pack [hier](https://www.microsoft.com/download/details.aspx?id=57171) herunter.  


### <a name="network"></a>Netzwerk

Die folgende Aufstellung enthält die Proxy- und Firewall-Konfigurationsinformationen, die der Operations Manager-Agent, der Verwaltungsserver und die Betriebskonsole benötigen, um mit Azure Monitor zu kommunizieren. Der Datenverkehr von jeder Komponente ist aus dem Netzwerk ausgehender Datenverkehr an Azure Monitor.

|Resource | Portnummer| HTTP-Prüfung umgehen|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Ja|  
|\*.oms.opinsights.azure.com| 443|Ja|  
|\*.blob.core.windows.net| 443|Ja|  
|\*.azure-automation.net| 443|Ja|  
|**Verwaltungsserver**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Ja|  
|\*.ods.opinsights.azure.com| 443| Ja|  
|*.azure-automation.net | 443| Ja|  
|**Operations Manager-Konsole mit Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 und 443||  
|\*.microsoft.com| 80 und 443||  
|\*.microsoftonline.com| 80 und 443||  
|\*.mms.microsoft.com| 80 und 443||  
|login.windows.net| 80 und 443||  
|portal.loganalytics.io| 80 und 443||
|api.loganalytics.io| 80 und 443||
|docs.loganalytics.io| 80 und 443||  

### <a name="tls-12-protocol"></a>TLS 1.2-Protokoll

Um die Sicherheit von Daten bei der Übertragung an Azure Monitor sicherzustellen, wird dringend empfohlen, den Agent und die Verwaltungsgruppe so zu konfigurieren, dass mindestens Transport Layer Security (TLS) 1.2 verwendet wird. Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**. Weitere Informationen finden Sie unter [Senden von Daten über TLS 1.2](./data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor

Führen Sie die folgenden Schritte aus, um Ihre Operations Manager-Verwaltungsgruppe mit einem Ihrer Log Analytics-Arbeitsbereiche zu verbinden.

Bei der ersten Registrierung Ihrer Operations Manager-Verwaltungsgruppe bei einem Log Analytics-Arbeitsbereich ist die Option zum Angeben der Proxykonfiguration für die Verwaltungsgruppe in der Betriebskonsole nicht verfügbar.  Die Verwaltungsgruppe muss erfolgreich beim Dienst registriert werden. Erst dann wird diese Option verfügbar.  Um dies zu umgehen, müssen Sie die Systemproxykonfiguration mit Netsh auf dem System aktualisieren, von dem aus Sie die Betriebskonsole ausführen, um die Integration sowie alle Verwaltungsserver in der Verwaltungsgruppe zu konfigurieren.  

1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten.
   a. Navigieren Sie zu **Start**, und geben Sie **cmd** ein.
   b. Klicken Sie mit der rechten Maustaste auf **Eingabeaufforderung**, und wählen Sie „Als Administrator ausführen“** aus.
1. Geben Sie den folgenden Befehl ein, und drücken Sie die **EINGABETASTE**:

    `netsh winhttp set proxy <proxy>:<port>`

Nachdem Sie die folgenden Schritte zur Integration mit Azure Monitor abgeschlossen haben, können Sie die Konfiguration entfernen, indem Sie `netsh winhttp reset proxy` ausführen und dann über die Option **Proxyserver konfigurieren** in der Betriebskonsole den Log Analytics-Gatewayserver angeben.

1. Wählen Sie in der Operations Manager-Konsole den Arbeitsbereich **Administration** aus.
1. Erweitern Sie den Knoten für Operations Management Suite, und klicken Sie auf **Verbindung**.
1. Klicken Sie auf den Link **Register to Operations Management Suite** (Bei Operations Management Suite registrieren).
1. Klicken Sie auf der Seite des **Assistenten zum Onboarding der Operations Management Suite: Authentifizierung** die E-Mail-Adresse oder Telefonnummer und Kennwort des Administratorkontos ein, das mit Ihrem OMS-Abonnement verknüpft ist, und klicken Sie auf **Anmelden**.

   >[!NOTE]
   >Der Name der Operations Management Suite wurde eingestellt.

1. Nachdem Sie sich erfolgreich authentifiziert haben, werden Sie auf der Seite des **Assistenten zum Onboarding der Operations Management Suite: Arbeitsbereich auswählen** aufgefordert, Ihren Azure-Mandanten, Ihr Abonnement und Ihren Log Analytics-Arbeitsbereich auszuwählen. Falls Sie über mehrere Arbeitsbereiche verfügen, wählen Sie in der Dropdownliste den Arbeitsbereich aus, den Sie in der Operations Manager-Verwaltungsgruppe registrieren möchten, und klicken Sie anschließend auf **Weiter**.

   > [!NOTE]
   > Operations Manager unterstützt immer nur einen einzelnen Log Analytics-Arbeitsbereich. Die Verbindung und Computer, die bei Azure Monitor mit dem vorherigen Arbeitsbereich registriert wurden, werden von Azure Monitor entfernt.
   >
   >
1. Klicken Sie auf der Seite des **Assistenten zum Onboarding der Operations Management Suite: Zusammenfassung** Ihre Einstellungen, und wenn diese richtig sind, klicken Sie auf **Erstellen**.
1. Klicken Sie auf der Seite des **Assistenten zum Onboarding der Operations Management Suite: Fertig stellen** auf **Schließen**.

### <a name="add-agent-managed-computers"></a>Hinzufügen von mit Agent verwalteten Computern

Nach dem Konfigurieren der Integration in Ihren Log Analytics-Arbeitsbereich wird lediglich eine Verbindung mit dem Dienst hergestellt, es werden keine Daten von den Agents gesammelt, die Daten an Ihre Verwaltungsgruppe melden. Dazu müssen Sie zuerst festlegen, welche mit Agents verwalteten Computer Protokolldaten für Azure Monitor erfassen. Sie können die Computerobjekte entweder einzeln oder eine Gruppe mit Windows-Computerobjekten auswählen. Sie können keine Gruppe auswählen, die Instanzen einer anderen Klasse enthält (etwa logische Datenträger oder SQL-Datenbanken).

1. Öffnen Sie die Operations Manager-Konsole, und wählen Sie den Arbeitsbereich **Administration** aus.
1. Erweitern Sie den Knoten für Operations Management Suite, und klicken Sie auf **Verbindung**.
1. Klicken Sie unter der Überschrift „Aktionen“ (rechts im Bereich) auf den Link **Computer/Gruppe hinzufügen** .
1. Im Dialogfeld **Computersuche** können Sie nach Computern oder Gruppen suchen, die von Operations Manager überwacht werden. Wählen Sie Computer oder Gruppen aus – einschließlich Operations Manager-Verwaltungsserver –, für die ein Onboarding in Azure Monitor durchgeführt werden soll, und klicken Sie nacheinander auf **Hinzufügen** und auf **OK**.

Computer und Gruppen, die unter Operations Management Suite zum Sammeln von Daten über den Knoten für verwaltete Computer konfiguriert sind, können in der Betriebskonsole im Arbeitsbereich **Administration** angezeigt werden. Hier können Sie Computer und Gruppen nach Bedarf hinzufügen und entfernen.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurieren von Proxyeinstellungen in der Betriebskonsole

Führen Sie die folgenden Schritte aus, wenn sich zwischen Verwaltungsgruppe und Azure Monitor ein interner Proxyserver befindet. Diese Einstellungen werden zentral über die Verwaltungsgruppe verwaltet und an mit Agent verwaltete Systeme verteilt, die im Bereich für die Protokolldatensammlung für Azure Monitor enthalten sind.  Dies ist von Vorteil, wenn bestimmte Lösungen den Verwaltungsserver umgehen und Daten direkt an den Dienst senden.

1. Öffnen Sie die Operations Manager-Konsole, und wählen Sie den Arbeitsbereich **Administration** aus.
1. Erweitern Sie „Operations Management Suite“, und klicken Sie auf **Verbindungen**.
1. Klicken Sie in der Ansicht „OMS-Verbindung“ auf **Proxyserver konfigurieren**.
1. Auf der Seite des **Assistenten für die Operations Management Suite: Proxyserver** wählen Sie die Option **Proxyserver für Zugriff auf Operations Management Suite verwenden** aus, geben Sie dann die URL mit der Portnummer ein (z. B. http://corpproxy:80 ), und klicken Sie anschließend auf **Fertig stellen**.

Falls Ihr Proxyserver eine Authentifizierung erfordert, führen Sie die folgenden Schritte aus, um Anmeldeinformationen und Einstellungen zu konfigurieren, die an verwaltete Computer weitergegeben werden müssen, die in der Verwaltungsgruppe Daten an Azure Monitor melden.

1. Öffnen Sie die Operations Manager-Konsole, und wählen Sie den Arbeitsbereich **Administration** aus.
1. Wählen Sie unter **RunAs Configuration** (RunAs-Konfiguration) die Option **Profile** aus.
1. Öffnen Sie das Profil **System Center Advisor Run As Profile Proxy** .
1. Klicken Sie im Assistenten für das ausführende Profil auf „Hinzufügen“, um ein ausführendes Konto zu verwenden. Sie können ein [ausführendes Konto](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) erstellen oder ein vorhandenes Konto verwenden. Dieses Konto muss über ausreichende Berechtigungen für die Weiterleitung über den Proxyserver verfügen.
1. Um das zu verwaltende Konto festzulegen, wählen Sie **A selected class, group, or object** (Einer bestimmten Klasse oder Gruppe bzw. eines bestimmten Objekts) aus, und klicken Sie auf **Select** (Auswählen). Klicken Sie anschließend auf **Group** (Gruppe), um das Feld **Group Search** (Gruppensuche) zu öffnen.
1. Suchen Sie nach **Microsoft System Center Advisor-Überwachungsservergruppe**, und wählen Sie sie aus. Klicken Sie nach dem Auswählen der Gruppe auf **OK**, um das Feld **Gruppensuche** zu schließen.
1. Klicken Sie auf **OK**, um das Dialogfeld **Ausführendes Konto hinzufügen** zu schließen.
1. Klicken Sie auf **Speichern** , um den Assistenten abzuschließen und Ihre Änderungen zu speichern.

Nachdem die Verbindung hergestellt wurde und Sie die Agents konfiguriert haben, die Protokolldaten erfassen und an Azure Monitor melden sollen, wird in der Verwaltungsgruppe die folgende Konfiguration angewendet (nicht zwingend in der hier angegebenen Reihenfolge):

* Das ausführende Konto **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** wird erstellt. Es wird mit dem ausführenden Profil **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor – ausführendes Profil – Blob) verknüpft und auf zwei Klassen ausgerichtet: **Collection Server** und **Operations Manager Management Group**.
* Zwei Connectors werden erstellt.  Der erste heißt **Microsoft.SystemCenter.Advisor.DataConnector** und wird automatisch mit einem Abonnement konfiguriert, das alle Warnungen, die von Instanzen aller Klassen in der Verwaltungsgruppe generiert werden, an Azure Monitor weiterleitet. Der zweite Connector heißt **Advisor Connector** und ist für die Kommunikation mit Azure Monitor und für die Weitergabe von Daten zuständig.
* Agents und Gruppen, die Sie zum Sammeln von Daten in der Verwaltungsgruppe ausgewählt haben, werden der **Microsoft System Center Advisor-Überwachungsservergruppe** hinzugefügt.

## <a name="management-pack-updates"></a>Management Pack-Updates

Nach Abschluss der Konfiguration stellt die Operations Manager-Verwaltungsgruppe eine Verbindung mit Azure Monitor her. Der Verwaltungsserver wird mit dem Webdienst synchronisiert und erhält aktualisierte Konfigurationsinformationen in Form von Management Packs für die aktivierten Lösungen, die in Operations Manager integriert werden. Operations Manager sucht nach Updates für diese Management Packs. Verfügbare Updates werden automatisch heruntergeladen und importiert. Dieses Verhalten wird insbesondere durch zwei Regeln gesteuert:

* **Microsoft.SystemCenter.Advisor.MPUpdate**: Aktualisiert die grundlegenden Azure Monitor-Management Packs. Wird standardmäßig alle 12 Stunden ausgeführt.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** : Aktualisiert lösungsbezogene Management Packs, die in Ihrem Arbeitsbereich aktiviert sind. Wird standardmäßig alle fünf Minuten ausgeführt.

Sie können diese beiden Regeln überschreiben und so entweder das automatische Herunterladen verhindern, indem Sie sie deaktivieren, oder die Häufigkeit ändern, mit der der Verwaltungsserver eine Synchronisierung mit Azure Monitor durchführt. So können Sie ermitteln, ob ein neues Management Pack verfügbar ist und heruntergeladen werden muss. Führen Sie die Schritte unter [How to Override a Rule or Monitor (Vorgehensweise: Überschreiben einer Regel oder Überwachung)](/previous-versions/system-center/system-center-2012-R2/hh212869(v=sc.12)) aus, um einen Sekundenwert für den Häufigkeitsparameter (**Frequency**) anzugeben und so den Synchronisierungszeitplan zu ändern, oder ändern Sie den Aktivierungsparameter (**Enabled**), um die Regeln zu deaktivieren. Verwenden Sie als Ziel für die Überschreibungen alle Objekte der Klasse „Operations Manager Management Group“.

Um Ihren vorhandenen Änderungssteuerungsprozess zur Steuerung von Management Pack-Releases in Ihrer Produktionsverwaltungsgruppe weiterhin zu verwenden, können Sie die Regeln deaktivieren und sie zu bestimmten Zeiten, in denen Updates zulässig sind, wieder aktivieren. Falls Ihre Umgebung eine Entwicklungs- oder QA-Verwaltungsgruppe mit Internetzugriff enthält, können Sie diese Verwaltungsgruppe zur Unterstützung dieses Szenarios mit einem Log Analytics-Arbeitsbereich konfigurieren. Dadurch können Sie die iterativen Versionen der Azure Monitor-Management Packs vor der Freigabe für die Produktionsverwaltungsgruppe überprüfen und evaluieren.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Umstellen einer Operations Manager-Gruppe auf einen neuen Log Analytics-Arbeitsbereich

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
1. Klicken Sie im Azure-Portal unten links auf **Weitere Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics** aus, und erstellen Sie dann einen Arbeitsbereich.  
1. Öffnen Sie die Operations Manager-Konsole mit einem Konto, das Mitglied der Administratorrolle von Operations Manager ist, und wählen Sie den Arbeitsbereich **Administration** aus.
1. Erweitern Sie Log Analytics, und wählen Sie **Verbindungen** aus.
1. Wählen Sie in der Mitte des Bereichs den Link **Re-configure Operations Management Suite** (Operations Management Suite neu konfigurieren) aus.
1. Führen Sie die Schritte im **Assistenten zum Integrieren von Log Analytics** aus, und geben Sie die E-Mail-Adresse oder Telefonnummer sowie das Kennwort des mit dem neuen Log Analytics-Arbeitsbereich verknüpften Administratorkontos ein.

   > [!NOTE]
   > Die Seite des **Assistenten zum Onboarding der Operations Management Suite: Arbeitsbereich auswählen** stellt den vorhandenen Arbeitsbereich dar, der verwendet wird.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Überprüfen der Integration von Operations Manager mit Azure Monitor

Verwenden Sie die folgende Abfrage, um die verbundenen Instanzen von Operations Manager abzurufen:

```azurepowershell
union *
| where isnotempty(MG)
| where not(ObjectName == 'Advisor Metrics' or ObjectName == 'ManagedSpace')
| summarize LastData = max(TimeGenerated) by lowerCasedComputerName=tolower(Computer), MG, ManagementGroupName
| sort by lowerCasedComputerName asc
```

## <a name="remove-integration-with-azure-monitor"></a>Entfernen der Integration mit Azure Monitor

Wenn Sie die Integration zwischen der Operations Manager-Verwaltungsgruppe und dem Log Analytics-Arbeitsbereich nicht mehr benötigen, müssen Sie mehrere Schritte ausführen, um die Verbindung und Konfiguration in der Verwaltungsgruppe richtig zu entfernen. Mit dem folgenden Verfahren aktualisieren Sie den Log Analytics-Arbeitsbereich, indem Sie nacheinander den Verweis auf Ihre Verwaltungsgruppe, die Azure Monitor-Connectors und die Management Packs zur Unterstützung der Integration mit dem Dienst löschen.  

Management Packs für die aktivierten Lösungen, die in Operations Manager integriert werden, sowie die für die Unterstützung der Integration mit Azure Monitor erforderlichen Management Packs können in der Verwaltungsgruppe nicht ohne Weiteres gelöscht werden. Dies ist darauf zurückzuführen, dass einige der Azure Monitor-Management Packs Abhängigkeiten von anderen zugehörigen Management Packs aufweisen. Um Management Packs mit einer Abhängigkeit von anderen Management Packs zu löschen, laden Sie das Skript zum [Entfernen eines Management Packs mit Abhängigkeiten](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) vom TechNet Script Center herunter.  

1. Öffnen Sie die Operations Manager-Befehlsshell mit einem Konto, das Mitglied der Administratorrolle von Operations Manager ist.

    > [!WARNING]
    > Vergewissern Sie sich, dass die Namen Ihrer benutzerdefinierten Management Packs nicht das Wort „Advisor“ oder „IntelligencePack“ enthalten, bevor Sie den Vorgang fortsetzen. Andernfalls werden diese mit den folgenden Schritten aus der Verwaltungsgruppe gelöscht.
    >

1. Geben Sie an der Eingabeaufforderung der Befehlsshell `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Geben Sie anschließend `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Zum Entfernen der restlichen Management Packs mit einer Abhängigkeit von anderen System Center Advisor-Management Packs verwenden Sie das Skript *RecursiveRemove.ps1*, das Sie zuvor vom TechNet Script Center heruntergeladen haben.  

    > [!NOTE]
    > Wenn Sie Advisor-Management Packs mit PowerShell entfernen, werden die Microsoft System Center Advisor-Management Packs und die internen Microsoft System Center Advisor-Management Packs nicht automatisch gelöscht.  Versuchen Sie nicht, sie zu löschen.  
    >  

1. Öffnen Sie die Operations Manager-Betriebskonsole mit einem Konto, das Mitglied der Administratorrolle von Operations Manager ist.
1. Wählen Sie unter **Verwaltung** den Knoten **Management Packs** aus, und geben Sie im **Suchfeld** den Text **Advisor** ein. Überprüfen Sie dann, ob die folgenden Management Packs noch in die Verwaltungsgruppe importiert werden:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor – Intern

1. Klicken Sie im Azure-Portal auf die Kachel **Einstellungen**.
1. Wählen Sie die Option **Connected Sources**(Verbundene Quellen).
1. In der Tabelle sollte im Abschnitt „System Center Operations Manager“ der Name der Verwaltungsgruppe angezeigt werden, die Sie aus dem Arbeitsbereich entfernen möchten. Klicken Sie unter der Spalte **Last Data** (Letzte Daten) auf **Entfernen**.  

    > [!NOTE]
    > Der Link zum **Entfernen** wird erst angezeigt, wenn für die Dauer von 14 Tagen keine Aktivität der verbundenen Verwaltungsgruppe erkannt wird.  
    >

1. In einem Fenster werden Sie dazu aufgefordert zu bestätigen, dass Sie die Entfernung durchführen möchten.  Klicken Sie auf **Ja** , um den Vorgang fortzusetzen.

Gehen Sie wie folgt vor, um den Connector „Microsoft.SystemCenter.Advisor.DataConnector“ und den Connector „Advisor Connector“ zu löschen. Speichern Sie das unten angegebene PowerShell-Skript auf Ihrem Computer, und führen Sie es aus, indem Sie die folgenden Beispiele verwenden:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Wenn der Computer, auf dem Sie dieses Skript ausführen, kein Verwaltungsserver ist, sollte darauf die Operations Manager-Befehlsshell installiert sein. Dies hängt von der Version Ihrer Verwaltungsgruppe ab.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Wenn Sie planen, die Verwaltungsgruppe später wieder mit einem Log Analytics-Arbeitsbereich zu verbinden, müssen Sie die Management Pack-Datei `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` erneut importieren. Abhängig von der Version von System Center Operations Manager, der in Ihrer Umgebung bereitgestellt ist, finden Sie diese Datei an folgendem Speicherort:

* Auf dem Quellmedium im Ordner `\ManagementPacks` bei System Center 2016 Operations Manager und höher.
* Unter letzten-Updaterollup, das auf Ihre Verwaltungsgruppe angewendet wurde. Bei Operations Manager 2012 ist `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` der Quellordner und bei 2012 R2 befindet er sich unter `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Nächste Schritte

Informationen über das Hinzufügen von Funktionen und Erfassen von Daten finden Sie unter [Hinzufügen von Azure Monitor-Lösungen aus dem Lösungskatalog](../insights/solutions.md).

