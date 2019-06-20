---
title: Herstellen einer Verbindung zwischen Configuration Manager und Azure Monitor | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Schritte zum Verbinden von Configuration Manager mit einem Arbeitsbereich in Azure Monitor und zum Beginnen der Analyse von Daten.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 26ddb0cdd2728f9dff5d45494a14841cdc1a20cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922882"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Herstellen einer Verbindung zwischen Configuration Manager und Azure Monitor
Sie können Ihre System Center Configuration Manager-Umgebung mit Azure Monitor verbinden, um die Daten der Gerätesammlung zu synchronisieren und in Azure Monitor und Azure Automation auf diese Sammlungen zu verweisen.  

## <a name="prerequisites"></a>Voraussetzungen

Azure Monitor unterstützt System Center Configuration Manager Current Branch ab Version 1606.  

## <a name="configuration-overview"></a>Konfigurationsübersicht
In den folgenden Schritten werden die Schritte zum Konfigurieren der Configuration Manager-Integration in Azure Monitor zusammengefasst.  

1. Registrieren Sie im Azure-Portal den Configuration Manager als Webanwendung und/oder Web API-App, und stellen Sie sicher, dass Sie die Client-ID und den geheimen Clientschlüssel aus der Registrierung von Azure Active Directory haben. Weitere Informationen zum Durchführen dieses Schritts finden Sie unter [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../../active-directory/develop/howto-create-service-principal-portal.md).
2. Im Azure-Portal [erteilen Sie Configuration Manager (der registrierten Web-App) die Zugriffsberechtigung für Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. Im Configuration Manager fügen Sie eine Verbindung mit dem Assistenten zum Hinzufügen von OMS-Verbindungen hinzu.
4. Im Configuration Manager aktualisieren Sie die Verbindungseigenschaften, wenn das Kennwort oder der geheime Clientschlüssel abläuft oder verloren geht.
5. [Laden und installieren Sie den Microsoft Monitoring Agent](#download-and-install-the-agent) auf dem Computer, auf dem die Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts ausgeführt wird. Der Agent sendet die Configuration Manager-Daten zum Log Analytics-Arbeitsbereich in Azure Monitor.
6. In Azure Monitor [importieren Sie Sammlungen von Configuration Manager](#import-collections) als Computergruppen.
7. In Azure Monitor zeigen Sie Daten von Configuration Manager als [Computergruppen](computer-groups.md) an.

Weitere Informationen zum Herstellen einer Verbindung zwischen Configuration Manager und Azure Monitor finden Sie unter [Synchronisieren von Daten aus Configuration Manager mit Azure Log Analytics](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Bereitstellen von Configuration Manager mit Berechtigungen für Log Analytics
Im Folgenden weisen Sie in Ihrem Log Analytics-Arbeitsbereich dem AD-Anwendungs- und Dienstprinzipal, den Sie zuvor für den Configuration Manager erstellt haben, die Rolle *Mitwirkender* zu.  Wenn Sie noch keinen Arbeitsbereich haben, lesen Sie zunächst [Erstellen eines Arbeitsbereichs in Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md), ehe Sie fortfahren.  Auf diese Weise kann Configuration Manager Ihren Log Analytics-Arbeitsbereich authentifizieren und darauf zugreifen.  

> [!NOTE]
> Sie müssen im Log Analytics-Arbeitsbereich Berechtigungen für Configuration Manager festlegen. Andernfalls erhalten Sie eine Fehlermeldung, wenn Sie den Konfigurations-Assistenten in Configuration Manager verwenden.
>

1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
2. Wählen Sie in der Liste mit den Log Analytics-Arbeitsbereichen den zu ändernden Arbeitsbereich aus.
3. Klicken Sie im linken Bereich auf **Zugriffssteuerung (IAM)** .
4. Klicken Sie auf der Seite „Zugriffssteuerung (IAM)“ auf **Rollenzuweisung hinzufügen**. Der Bereich **Rollenzuweisung hinzufügen** wird angezeigt.
5. Wählen Sie im Bereich **Rollenzuweisung hinzufügen** in der Dropdownliste **Rolle** die Rolle **Mitwirkender** aus.  
6. Wählen Sie unter der Dropdownliste **Zugriff zuweisen zu** die zuvor in AD erstellte Configuration Manager-Anwendung, und klicken Sie dann auf **OK**.  

## <a name="download-and-install-the-agent"></a>Herunterladen und Installieren des Agents
Lesen Sie den Artikel [Verbinden von Windows-Computern mit Azure Monitor in Azure](agent-windows.md), um sich über die verfügbaren Methoden zur Installation des Microsoft Monitoring-Agents auf dem Computer zu informieren, auf dem sich die Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts befindet.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Hinzufügen einer Log Analytics-Verbindung zu Configuration Manager
Zum Hinzufügen einer Log Analytics-Verbindung muss für die Configuration Manager-Umgebung ein [Dienstverbindungspunkt](https://technet.microsoft.com/library/mt627781.aspx) für den Onlinemodus konfiguriert sein.

1. Wählen Sie im Arbeitsbereich **Verwaltung** des Configuration Manager **OMS-Connector**. Daraufhin wird der **Assistent zum Hinzufügen einer Log Analytics-Verbindung** geöffnet. Klicken Sie auf **Weiter**.

   >[!NOTE]
   >OMS wird jetzt als Log Analytics bezeichnet, das ein Feature von Azure Monitor ist.
   
2. Auf dem Bildschirm **Allgemein** vergewissern Sie sich, dass Sie die folgenden Aktionen durchgeführt haben und dass Sie für jedes Element Detailinformationen haben, und wählen Sie anschließend **Weiter** aus.

   1. Im Azure-Portal haben Sie Configuration Manager als Webanwendung und/oder Web API-App registriert, und Sie haben die [Client-ID von der Registrierung](../../active-directory/develop/quickstart-register-app.md) vorliegen.
   2. Im Azure-Portal haben Sie einen geheimen App-Schlüssel für die registrierte Anwendung in Azure Active Directory erstellt.  
   3. Im Azure-Portal haben Sie der registrierten Web-App eine Zugriffsberechtigung für den Log Analytics-Arbeitsbereich in Azure Monitor erteilt.  
      ![Verbindung mit Log Analytics: Seite „Allgemein“ des Assistenten](./media/collect-sccm/sccm-console-general01.png)
3. Auf dem Bildschirm **Azure Active Directory** konfigurieren Sie Ihre Einstellungen für die Verbindung mit dem Log Analytics-Arbeitsbereich durch Angabe von **Mandant**, **Client-ID**, und des **geheimen Clientschlüssels**. Klicken Sie dann auf **Weiter**.  
   ![Verbindung mit Log Analytics: Seite „Azure Active Directory“ des Assistenten](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Wenn Sie alle anderen Verfahren erfolgreich abgeschlossen haben, erscheinen die Informationen auf dem Bildschirm **OMS-Verbindungskonfiguration** automatisch auf dieser Seite. Informationen für die Verbindungseinstellungen sollten für **Azure-Abonnement**, **Azure-Ressourcengruppe**, und **Operations Management Suite-Arbeitsbereich** angezeigt werden.  
   ![Verbindung mit Log Analytics: Seite „Log Analytics-Verbindung“ des Assistenten](./media/collect-sccm/sccm-wizard-configure04.png)
5. Der Assistent stellt eine Verbindung mit dem Log Analytics-Arbeitsbereich mithilfe der Informationen her, die Sie eingegeben haben. Wählen Sie die Gerätesammlungen, die Sie zur Synchronisierung mit dem Dienst verwenden möchten, und klicken Sie anschließend auf **Hinzufügen**.  
   ![Sammlungen auswählen](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Überprüfen Sie die Verbindungseinstellungen auf dem Bildschirm **Zusammenfassung**, und wählen Sie **Weiter** aus. Der Bildschirm **Status** zeigt den Verbindungsstatus, sollte **vollständig** sein.

> [!NOTE]
> Sie müssen die obersten Ebene in Ihrer Hierarchie mit Azure Monitor verbinden. Wenn Sie einen eigenständigen primären Standort mit Azure Monitor verbinden und dann eine zentrale Verwaltung der Umgebung hinzufügen, müssen Sie die Verbindung innerhalb der neuen Hierarchie löschen und neu erstellen.
>
>

Nachdem Sie Configuration Manager mit Azure Monitor verknüpft haben, können Sie Sammlungen hinzufügen oder entfernen und die Eigenschaften der Verbindung anzeigen.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualisieren von Verbindungseigenschaften des Log Analytics-Arbeitsbereichs
Wenn das Kennwort oder der geheime Clientschlüssel abläuft oder verloren geht, müssen Sie die Log Analytics-Verbindungseigenschaften manuell aktualisieren.

1. Navigieren Sie im Configuration Manager zu **Clouddienste**, und wählen Sie dann **OMS-Connector** zum Öffnen der Seite **OMS-Verbindungseigenschaften** aus.
2. Klicken Sie auf dieser Seite auf die Registerkarte **Azure Active Directory** zur Anzeige von **Mandant**, **Client-ID**, **Ablauf des geheimen Clientschlüssels**. **Überprüfen Sie** Ihren **geheimen Clientschlüssel**, falls er abgelaufen ist.

## <a name="import-collections"></a>Importieren von Sammlungen
Nachdem Sie eine Log Analytics-Verbindung mit Configuration Manager hinzugefügt und den Agent auf dem Computer mit der Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts installiert haben, müssen als Nächstes Sammlungen aus Configuration Manager als Computergruppen in Azure Monitor importiert werden.

Nachdem Sie die Erstkonfiguration für den Import von Gerätesammlungen aus Ihrer Hierarchie abgeschlossen haben, werden alle drei Stunden die Mitgliedschaftsinformationen zur Sammlung abgerufen, um die Mitgliedschaft aktuell zu halten. Sie können diese Einstellung jederzeit deaktivieren.

1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.
2. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den Arbeitsbereich aus, in dem Configuration Manager registriert ist.  
3. Wählen Sie **Erweiterte Einstellungen**.
4. Wählen Sie hierzu **Computergruppen** und anschließend **SCCM** aus.  
5. Wählen Sie **Configuration Manager-Sammlungsmitgliedschaften importieren**aus, und klicken Sie anschließend auf **Speichern**.  
   ![Computergruppen – Registerkarte „SCCM“](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Anzeigen von Daten aus Configuration Manager
Nachdem Sie eine Log Analytics-Verbindung mit Configuration Manager hinzugefügt und den Agent auf dem Computer mit der Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts installiert haben, werden Daten vom Agent an den Log Analytics-Arbeitsbereich in Azure Monitor gesendet. In Azure Monitor werden die Configuration Manager-Sammlungen als [Computergruppen](../../azure-monitor/platform/computer-groups.md) angezeigt. Die Anzeige von Gruppen wählen Sie auf der Seite **Configuration Manager** unter **Einstellungen\Computergruppen** aus.

Nach dem Import der Sammlungen können Sie sehen, wie viele Computer mit Sammlungsmitgliedschaften erkannt wurden. Sie sehen auch die Anzahl der Sammlungen, die importiert wurden.

![Computergruppen – Registerkarte „SCCM“](./media/collect-sccm/sccm-computer-groups02.png)

Wenn Sie auf eine klicken, wird die Suche geöffnet, wobei entweder alle importierten Gruppen oder alle Computer angezeigt werden, die zu jeder Gruppe gehören. Mithilfe der [Protokollsuche](../../azure-monitor/log-query/log-query-overview.md) können Sie detaillierte Analysen für Configuration Manager-Daten starten.

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie [Protokollsuche](../../azure-monitor/log-query/log-query-overview.md) für ausführliche Informationen zu den Configuration Manager-Daten.
