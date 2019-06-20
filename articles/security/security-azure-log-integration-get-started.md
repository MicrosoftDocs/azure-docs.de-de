---
title: Erste Schritte mit Azure Log Integration | Microsoft Docs
description: Erfahren Sie, wie Sie den Azure Log Integration-Dienst installieren und Protokolle aus Azure Storage sowie Azure-Überwachungsprotokolle und Azure Security Center-Warnungen integrieren.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: b1df35c82ffb8b98f8b7bf86c21694b62b3804e4
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298295"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration mit Azure-Diagnoseprotokollierung und Windows-Ereignisweiterleitung


>[!IMPORTANT]
> Das Feature Azure Log Integration gilt ab dem 15.06.2019 als veraltet. AzLog-Downloads wurden am 27. Juni 2018 deaktiviert. Um Unterstützung bei der künftigen Vorgehensweise zu erhalten, lesen Sie den Beitrag [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Verwenden von Azure Monitor für die Integration mit SIEM-Tools). 

Sie sollten Azure Log Integration nur nutzen, wenn Ihr SIEM-Anbieter (Security Incident and Event Management) keinen [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md)-Connector zur Verfügung stellt.

Azure Log Integration macht Azure-Protokolle für Ihr SIEM verfügbar, damit Sie ein einheitliches Sicherheitsdashboard für alle Ihre Ressourcen erstellen können.
Um weitere Informationen zum Status eines Azure Monitor-Connectors zu erhalten, wenden Sie sich an den SIEM-Anbieter.

> [!IMPORTANT]
> Wenn Ihr Hauptinteresse das Erfassen von Protokollen für virtuelle Computer ist, wird diese Option von den Lösungen der meisten SIEM-Anbieter abgedeckt. Das Verwenden des Connectors des SIEM-Anbieters ist immer die bevorzugte Alternative.

Dieser Artikel unterstützt Sie bei den ersten Schritten mit Azure Log Integration. Sein Schwerpunkt liegt auf dem Azure Log Integration-Dienst und dem Integrieren des Diensts in die Azure-Diagnose. Der Azure Log Integration-Dienst erfasst dann Protokollinformationen von Windows-Ereignissen aus dem Windows-Sicherheitsereigniskanal von virtuellen Computern, die in Azure IaaS (Infrastructure-as-a-Service) bereitgestellt werden. Dies ähnelt der *Ereignisweiterleitung*, die Sie möglicherweise lokal verwenden.

> [!NOTE]
> Die Integration der Ausgabe von Azure Log Integration mit einem SIEM erfolgt durch das SIEM selbst. Weitere Informationen finden Sie unter [Integration von Azure Log Integration in das lokale SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Der Azure Log Integration-Dienst wird auf einem physischen oder virtuellen Computer mit Windows Server 2008 R2 oder höher ausgeführt (Windows Server 2016 oder Windows Server 2012 R2 wird bevorzugt).

Ein physischer Computer kann lokal oder an einem Hostingstandort ausgeführt werden. Wenn Sie den Azure Log Integration-Dienst auf einem virtuellen Computer ausführen, kann sich der virtuelle Computer an einem lokalen Standort oder in einer öffentlichen Cloud (z.B. in Microsoft Azure) befinden.

Der physische oder virtuelle Computer, der den Azure-Protokollintegrationsdienst ausführt, erfordert Netzwerkverbindungen mit der öffentlichen Azure-Cloud. Dieser Artikel enthält Details zu der erforderlichen Konfiguration.

## <a name="prerequisites"></a>Voraussetzungen

Für die Installation von Azure Log Integration sind mindestens die folgenden Elemente erforderlich:

* Ein **Azure-Abonnement**. Wenn Sie keines besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/)registrieren.
* Ein **Speicherkonto**, das für die Windows Azure-Diagnoseprotokollierung (WAD) verwendet werden kann. Sie können ein vorkonfiguriertes Speicherkonto auswählen oder ein neues Speicherkonto erstellen. Später in diesem Artikel wird beschrieben, wie das Speicherkonto konfiguriert wird.

  > [!NOTE]
  > Je nach Szenario ist möglicherweise kein Speicherkonto erforderlich. Für das in diesem Artikel beschriebene Azure-Diagnoseszenario ist ein Speicherkonto erforderlich.

* **Zwei Systeme**: 
  * Ein Computer, der den Azure Log Integration-Dienst ausführt. Dieser Computer erfasst alle Protokollinformationen, die später in Ihr SIEM importiert werden. Für dieses System gilt:
    * Es kann sich an einem lokalen Standort befinden oder in Microsoft Azure gehostet werden.  
    * Es muss eine x64-Version von Windows Server 2008 R2 SP1 oder höher ausführen und über eine Installation von .NET 4.5.1 verfügen. Sie können die installierte .NET-Version wie unter [Feststellen, welche Versionen von .NET-Framework installiert sind](https://msdn.microsoft.com/library/hh925568) beschrieben ermitteln.  
    * Es muss eine Verbindung mit dem Azure Storage-Konto herstellen können, das für die Azure-Diagnoseprotokollierung verwendet wird. Später in diesem Artikel wird beschrieben, wie die Verbindung konfiguriert wird.
  * Ein zu überwachender Computer. Dies ist ein virtueller Computer, der als [virtueller Azure-Computer](../virtual-machines/virtual-machines-windows-overview.md) ausführt wird. Die Protokollierungsinformationen von diesem Computer werden an den Computer mit dem Azure Log Integration-Dienst gesendet.

Eine kurze Demonstration des Vorgangs zum Erstellen eines virtuellen Computers mit dem Azure-Portal finden Sie im folgenden Video:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

Beim Testen können Sie ein beliebiges System verwenden, das die Mindestanforderungen für das Betriebssystem erfüllt. Für eine Produktionsumgebung kann es die Auslastung erforderlich machen, zentrales oder horizontales Hochskalieren einzuplanen.

Sie können mehrere Instanzen des Azure Log Integration-Diensts ausführen. Sie können jedoch nur eine Instanz des Diensts pro physischem oder virtuellem Computer ausführen. Darüber hinaus können Sie Lastenausgleich für Azure-Diagnosespeicherkonten für WAD ausführen. Die Anzahl der Abonnements für die Instanzen richtet sich nach Ihrer Kapazität.

> [!NOTE]
> Zurzeit liegen keine spezifischen Empfehlungen dazu vor, wann Instanzen von Azure Log Integration-Computern (d.h. Computer, auf denen der Azure Log Integration-Dienst ausgeführt wird) oder Speicherkonten bzw. Abonnements horizontal hochskaliert werden müssen. Die Entscheidungen hinsichtlich der Skalierung sollten auf Ihren Beobachtungen zur Leistung in den einzelnen Bereichen basieren.

Sie haben auch die Möglichkeit, den Azure Log Integration-Dienst zentral hochzuskalieren, um die Leistung zu verbessern. Die folgenden Leistungsmetriken können Ihnen dabei helfen, die Größe von Computern zu bestimmen, die Sie dafür auswählen, den Azure Log Integration-Dienst auszuführen:

* Auf einem Computer mit acht Prozessoren (Kernen) kann eine einzelne Azure Log Integration-Instanz pro Tag etwa 24 Millionen Ereignisse (rund eine Million Ereignisse pro Stunde) verarbeiten.
* Auf einem Computer mit vier Prozessoren (Kernen) kann eine einzelne Azure Log Integration-Instanz pro Tag etwa 1,5 Millionen Ereignisse (rund 62.500 Ereignisse pro Stunde) verarbeiten.

## <a name="install-azure-log-integration"></a>Installieren von Azure Log Integration

Führen Sie die Setuproutine durch. Wählen Sie aus, ob Sie Microsoft Telemetrieinformationen zur Verfügung stellen möchten.

Der Azure Log Integration-Dienst erfasst Telemetriedaten des Computers, auf dem er installiert ist.  

Die erfassten Telemetriedaten beinhalten Folgendes:

* Ausnahmen, die während der Ausführung von Azure Log Integration auftreten.
* Metriken zu der Anzahl von verarbeiteten Abfragen und Ereignissen.
* Statistiken zur Verwendung von Azlog.exe-Befehlszeilenoptionen. 

> [!NOTE]
> Es wird empfohlen, dass Sie Microsoft das Sammeln von Telemetriedaten gestatten. Sie können die Erfassung von Telemetriedaten deaktivieren, indem Sie das Kontrollkästchen **Microsoft das Erfassen von Telemetriedaten erlauben** deaktivieren.
>

![Screenshot des Installationsbereichs mit aktiviertem Kontrollkästchen für Telemetrie](./media/security-azure-log-integration-get-started/telemetry.png)


Der Installationsvorgang wird im folgenden Video behandelt:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Schritte nach der Installation und zur Validierung

Nach Abschluss des grundlegenden Setups sind Sie bereit, die auf die Installation folgenden Schritte sowie die Validierungsschritte auszuführen:

1. Starten Sie PowerShell als Administrator. Navigieren Sie dann zu „C:\Programme\Microsoft Azure Log Integration“.
2. Importieren Sie die Azure Log Integration-Cmdlets. Um die Cmdlets zu importieren, führen Sie das Skript `LoadAzlogModule.ps1` aus. Geben Sie `.\LoadAzlogModule.ps1` ein, und drücken Sie dann die EINGABETASTE (beachten Sie die Verwendung von **.\\** in diesem Befehl). Es sollte jetzt etwas angezeigt werden, das der folgenden Abbildung ähnelt:

   ![Screenshot der Ausgabe des LoadAzlogModule.ps1-Befehls](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Im nächsten Schritt müssen Sie Azure Log Integration konfigurieren, um eine bestimmte Azure-Umgebung zu verwenden. Eine *Azure-Umgebung* ist der Typ des Datencenters für die Azure-Cloud, mit dem Sie arbeiten möchten. Obwohl es zu diesem Zeitpunkt mehrere Azure-Umgebungen gibt, sind die relevanten Optionen zurzeit entweder **AzureCloud** oder **AzureUSGovernment**. Führen Sie PowerShell als Administrator aus, und stellen Sie sicher, dass Sie sich in „C:\Programme\Microsoft Azure Log Integration\“ befinden. Führen Sie dann den folgenden Befehl aus:

   `Set-AzlogAzureEnvironment -Name AzureCloud` (für **AzureCloud**)
  
   Wenn Sie die Azure-Cloud der US-Regierung verwenden möchten, würden Sie **AzureUSGovernment** für die Variable **-Name** verwenden. Andere Azure-Clouds werden zurzeit nicht unterstützt.  

   > [!NOTE]
   > Sie erhalten keine Rückmeldung, wenn der Befehl erfolgreich war. 

4. Bevor Sie ein System überwachen können, benötigen Sie den Namen des Speicherkontos, das für die Azure-Diagnose verwendet wird. Navigieren Sie im Azure-Portal zu **Virtuelle Computer**. Suchen Sie den virtuellen Windows-Computer, den Sie überwachen möchten. Wählen Sie im Abschnitt **Eigenschaften** die Option **Diagnoseeinstellungen** aus.  Wählen Sie dann **Agent** aus. Notieren Sie sich den angegebenen Namen des Speicherkontos. Dieser Kontoname ist in einem späteren Schritt erforderlich.

   ![Screenshot des Bereichs „Azure-Diagnoseeinstellungen“](./media/security-azure-log-integration-get-started/storage-account-large.png) 

   ![Screenshot der Schaltfläche „Überwachung auf Gastebene aktivieren“](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Wenn die Überwachung beim Erstellen des virtuellen Computers nicht aktiviert war, können Sie sie wie in der vorherigen Abbildung gezeigt aktivieren.

5. Wechseln Sie nun zurück auf den Azure Log Integration-Computer. Bestätigen Sie auf dem System, auf dem Sie Azure Log Integration installiert haben, dass Sie über eine Verbindung mit dem Speicherkonto verfügen. Der Computer, auf dem der Azure Log Integration-Dienst ausgeführt wird, benötigt Zugriff auf das Speicherkonto, um auf den einzelnen überwachten Systemen von der Azure-Diagnose protokollierte Informationen abzurufen. So überprüfen Sie die Konnektivität: 
   1. [Laden Sie Azure Storage-Explorer herunter](https://storageexplorer.com/).
   2. Schließen Sie das Setup ab.
   3. Wenn die Installation abgeschlossen ist, wählen Sie **Weiter** aus. Lassen Sie das Kontrollkästchen **Microsoft Azure Storage-Explorer starten** aktiviert.  
   4. Melden Sie sich bei Azure an.
   5. Überprüfen Sie, ob das Speicherkonto angezeigt wird, das Sie für die Azure-Diagnose konfiguriert haben: 

   ![Screenshot des Speicherkontos in Storage-Explorer](./media/security-azure-log-integration-get-started/storage-explorer.png)

   1. Einige Optionen werden unter den Speicherkonten angezeigt. Unter **Tabellen** sollte eine Tabelle mit der Bezeichnung **WADWindowsEventLogsTable** angezeigt werden.

   Wenn die Überwachung beim Erstellen des virtuellen Computers nicht aktiviert war, können Sie sie wie weiter oben beschrieben aktivieren.


## <a name="integrate-windows-vm-logs"></a>Integrieren von Windows-VM-Protokollen

In diesem Schritt konfigurieren Sie den Computer, auf dem der Azure Log Integration-Dienst ausgeführt wird, um eine Verbindung mit dem Speicherkonto herzustellen, das die Protokolldateien enthält.

Zum Abschließen dieses Schritts sind einige Angaben erforderlich:  
* **FriendlyNameForSource**: Diesen Anzeigenamen können Sie auf das Speicherkonto anwenden, das Sie für den virtuellen Computer zum Speichern der Informationen aus der Azure-Diagnose konfiguriert haben.
* **StorageAccountName**: Der Name des Speicherkontos, das Sie beim Konfigurieren der Azure-Diagnose angegeben haben.  
* **StorageKey**: Der Speicherschlüssel für das Speicherkonto, in dem die Azure-Diagnoseinformationen für diesen virtuellen Computer gespeichert werden.  

Führen Sie die folgenden Schritte aus, um den Speicherschlüssel abzurufen:
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Wählen Sie im Navigationsbereich **Alle Dienste** aus.
3. Geben Sie **Speicher** in das Feld **Filter** ein. Wählen Sie dann **Speicherkonten** aus.

   ![Screenshot, der Speicherkonten in „Alle Dienste“ zeigt](./media/security-azure-log-integration-get-started/filter.png)

4. Es wird eine Liste aller Speicherkonten angezeigt. Doppelklicken Sie auf das Konto, das Sie zum Protokollieren des Speichers zugewiesen haben.

   ![Screenshot, der eine Liste der Speicherkonten zeigt](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. Wählen Sie unter **Einstellungen** die Option **Zugriffsschlüssel** aus.

   ![Screenshot, der die Option „Zugriffsschlüssel“ im Menü zeigt](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopieren Sie **Schlüssel1**, und speichern Sie ihn dann an einem sicheren Ort, auf den Sie im nächsten Schritt zugreifen können.
7. Öffnen Sie auf dem Server, auf dem Sie Azure Log Integration installiert haben, als Administrator ein Eingabeaufforderungsfenster. (Stellen Sie sicher, dass Sie als Administrator ein Eingabeaufforderungsfenster öffnen und nicht PowerShell).
8. Navigieren Sie zu „C:\Programme\Microsoft Azure Log Integration“.
9. Führen Sie den folgenden Befehl aus: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
   Beispiel:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Wenn die Abonnement-ID im Ereignis-XML angezeigt werden soll, fügen Sie am Ende des Anzeigenamens die Abonnement-ID an:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Beispiel:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Warten Sie bis zu 60 Minuten, und sehen Sie sich dann die aus dem Speicherkonto abgerufenen Ereignisse an. Wählen Sie zum Anzeigen der Ereignisse in Azure Log Integration **Ereignisanzeige** > **Windows-Protokolle** > **weitergeleitete Ereignisse** aus.

Im folgenden Video werden die oben beschriebenen Schritte behandelt:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Wie ist vorzugehen, wenn im Ordner „Weitergeleitete Ereignisse“ keine Daten angezeigt werden?
Sollten im Ordner „Weitergeleitete Ereignisse“ nach einer Stunde immer noch keine Daten angezeigt werden, führen Sie die folgenden Schritte aus:

1. Überprüfen Sie den Computer, der den Azure Log Integration-Dienst ausführt. Vergewissern Sie sich, dass er auf Azure zugreifen kann. Versuchen Sie in einem Browser, zum [Azure-Portal](https://portal.azure.com) zu navigieren, um die Konnektivität zu testen.
2. Vergewissern Sie sich, dass das Benutzerkonto „Azlog“ über Schreibberechtigung für den Ordner „users\Azlog“ verfügt.
   1. Öffnen Sie den Datei-Explorer.
   2. Navigieren Sie zu „C:\Benutzer“.
   3. Klicken Sie mit der rechten Maustaste auf „C:\Benutzer\Azlog“.
   4. Wählen Sie **Sicherheit** aus.
   5. Wählen Sie **NT Service\Azlog** aus. Überprüfen Sie die Berechtigungen für das Konto. Wenn das Konto auf dieser Registerkarte fehlt oder die entsprechenden Berechtigungen nicht angezeigt werden, können Sie dem Konto auf dieser Registerkarte die erforderlichen Berechtigungen erteilen.
3. Wenn Sie den Befehl `Azlog source list` ausführen, stellen Sie sicher, dass das Speicherkonto, das im Befehl `Azlog source add` hinzugefügt wurde, in der Ausgabe aufgeführt wird.
4. Überprüfen Sie unter **Ereignisanzeige** > **Windows-Protokolle** > **Anwendung**, ob von Azure Log Integration möglicherweise Fehler gemeldet wurden.

Wenn während der Installation und Konfiguration Probleme auftreten, können Sie eine [Supportanfrage](../azure-supportability/how-to-create-azure-support-request.md) erstellen. Wählen Sie für diesen Dienst die Option **Protokollintegration** aus.

Eine weitere Supportoption ist das [MSDN-Forum für Azure Log Integration](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Im MSDN-Forum kann die Community Unterstützung bieten, indem sie Fragen beantwortet und Tipps und Tricks zur optimalen Nutzung von Azure Log Integration weitergibt. Das Azure Log Integration-Team überwacht dieses Forum ebenfalls. Die Mitglieder dieses Teams helfen, wann immer sie können.

## <a name="integrate-azure-activity-logs"></a>Integrieren von Azure-Aktivitätsprotokollen

Das Azure-Aktivitätsprotokoll ist ein Abonnementprotokoll, das Einblicke in Ereignisse auf Abonnementebene ermöglicht, die in Azure aufgetreten sind. Dies schließt einen Datenbereich von Azure Resource Manager-Betriebsdaten bis hin zu Aktualisierungen für Dienstintegritätsereignisse ein. Die Azure Security Center-Warnungen sind ebenfalls in diesem Protokoll enthalten.
> [!NOTE]
> Zur Vorbereitung auf die Schritte in diesem Artikel müssen zunächst die Schritte im Artikel [Erste Schritte](security-azure-log-integration-get-started.md) ausgeführt werden.

### <a name="steps-to-integrate-azure-activity-logs"></a>Schritte zum Integrieren von Azure-Aktivitätsprotokollen

1. Öffnen Sie die Eingabeaufforderung, und führen Sie diesen Befehl aus: ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Führen Sie diesen Befehl aus: ```azlog createazureid```

    Dabei werden Sie zur Angabe Ihrer Azure-Anmeldeinformationen aufgefordert. Anschließend wird in den Azure AD-Mandanten, die die Azure-Abonnements hosten, in denen der angemeldete Benutzer als Administrator, Co-Administrator oder Besitzer fungiert, ein Azure Active Directory-Dienstprinzipal erstellt. Handelt es sich bei dem angemeldeten Benutzer nur um einen Gastbenutzer des Azure AD-Mandanten, tritt bei dem Befehl ein Fehler auf. Die Authentifizierung gegenüber Azure wird über Azure AD durchgeführt. Beim Erstellen eines Dienstprinzipals für die Azure-Protokollintegration wird die Azure AD-Identität erstellt, die Lesezugriff auf Azure-Abonnements erhält.
3. Führen Sie den folgenden Befehl aus, um den im vorherigen Schritt erstellten Azure Log Integration-Dienstprinzipal zum Lesen des Aktivitätsprotokolls für das Abonnement zu autorisieren. Sie müssen ein Besitzer des Abonnements sein, um den Befehl ausführen zu können.

   ```Azlog.exe authorize subscriptionId``` Beispiel:

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Überprüfen Sie die folgenden Ordner, um sicherzustellen, dass die JSON-Dateien des Azure Active Directory-Überwachungsprotokolls in ihnen erstellt wurden:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Sie müssen sich an Ihren SIEM-Anbieter wenden, um spezifische Anweisungen zum Übertragen der Informationen in den JSON-Dateien in Ihr SIEM-System (Security Information and Event Management) zu erhalten.

Unterstützung durch die Community ist über das [MSDN-Forum für die Azure-Protokollintegration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration) verfügbar. In diesem Forum kann sich die Community der Azure-Protokollintegration gegenseitig mit Fragen, Antworten, Tipps und Tricks unterstützen. Darüber hinaus überwacht das Team für die Azure-Protokollintegration dieses Forum und bietet nach Möglichkeit entsprechende Hilfe an.

Sie können zudem eine [Supportanfrage](../azure-supportability/how-to-create-azure-support-request.md) öffnen. Wählen Sie „Log Integration“ als Dienst aus, für den Sie Support anfordern.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Log Integration finden Sie in den folgenden Artikeln: Zur Vorbereitung auf die Schritte in diesem Artikel müssen zunächst die Schritte im Artikel „Erste Schritte“ ausgeführt werden.

* [Einführung in Azure Log Integration](security-azure-log-integration-overview.md). Dieser Artikel stellt Azure Log Integration, die wichtigsten Funktionen sowie die Funktionsweise vor.
* [Partner configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Konfigurationsschritte für Partner, in englischer Sprache). In diesem Blogbeitrag erfahren Sie, wie Azure Log Integration für die Partnerlösungen Splunk, HP ArcSight und IBM QRadar konfiguriert wird. Er beschreibt unsere zurzeit geltende Empfehlung für die Konfiguration von SIEM-Komponenten. Holen Sie weitere Informationen bei Ihrem SIEM-Hersteller ein.
* [Azure-Protokollintegration: Häufig gestellte Fragen (FAQ)](security-azure-log-integration-faq.md). In diesen häufig gestellten Fragen finden Sie Antworten zu Azure Log Integration.
* [Integrieren von Azure Security Center-Warnungen mithilfe von Azure Log Integration](../security-center/security-center-integrating-alerts-with-log-integration.md). Dieser Artikel zeigt Ihnen, wie Sie Security Center-Warnungen und Sicherheitsereignisse für virtuelle Computer synchronisieren, die von der Azure-Diagnose und Azure-Aktivitätsprotokollen erfasst werden. Sie synchronisieren die Protokolle mit Azure Monitor-Protokollen oder Ihrer SIEM Lösung.
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Neue Features für die Azure-Diagnose und Azure-Überwachungsprotokolle). In diesem Blogbeitrag werden Azure-Überwachungsprotokolle und andere Features vorgestellt, mit denen Sie sich einen Einblick in die Vorgänge Ihrer Azure-Ressourcen verschaffen können.
