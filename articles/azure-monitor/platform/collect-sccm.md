---
title: Herstellen einer Verbindung zwischen Configuration Manager und Azure Monitor | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Schritte zum Verbinden von Configuration Manager mit einem Arbeitsbereich in Azure Monitor und zum Beginnen der Analyse von Daten.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 20d55de7ef0f7b7f49b922409f8cb61874fae0f6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008972"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Herstellen einer Verbindung zwischen Configuration Manager und Azure Monitor
Sie können Ihre Microsoft Endpoint Configuration Manager-Umgebung mit Azure Monitor verbinden, um die Daten der Gerätesammlung zu synchronisieren und in Azure Monitor und Azure Automation auf diese Sammlungen zu verweisen.  

## <a name="prerequisites"></a>Voraussetzungen

Azure Monitor unterstützt Configuration Manager Current Branch ab Version 1606.

>[!NOTE]
>Das Feature zum Herstellen einer Verbindung zwischen Configuration Manager und einem Log Analytics-Arbeitsbereich ist optional und standardmäßig nicht aktiviert. Es muss vor der Verwendung aktiviert werden. Weitere Informationen finden Sie unter [Aktivieren optionaler Features von Updates](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Konfigurationsübersicht

In den folgenden Schritten werden die Schritte zum Konfigurieren der Configuration Manager-Integration in Azure Monitor zusammengefasst.  

1. Registrieren Sie Configuration Manager in Azure Active Directory als Webanwendung und/oder Web API-App, und vergewissern Sie sich, dass Sie über die Client-ID und den geheimen Clientschlüssel aus der Registrierung von Azure Active Directory verfügen. Weitere Informationen zum Durchführen dieses Schritts finden Sie unter [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../../active-directory/develop/howto-create-service-principal-portal.md).

2. [Erteilen Sie Configuration Manager (der registrierten Web-App) in Azure Active Directory die Zugriffsberechtigung für Azure Monitor.](#grant-configuration-manager-with-permissions-to-log-analytics)

3. Fügen Sie in Configuration Manager eine Verbindung mithilfe des Assistenten für **Azure-Dienste** hinzu.

4. [Laden Sie den Log Analytics-Agent für Windows herunter, und installieren Sie ihn](#download-and-install-the-agent) auf dem Computer, auf dem die Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts ausgeführt wird. Der Agent sendet die Configuration Manager-Daten zum Log Analytics-Arbeitsbereich in Azure Monitor.

5. In Azure Monitor [importieren Sie Sammlungen von Configuration Manager](#import-collections) als Computergruppen.

6. In Azure Monitor zeigen Sie Daten von Configuration Manager als [Computergruppen](computer-groups.md) an.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Bereitstellen von Configuration Manager mit Berechtigungen für Log Analytics

Im Folgenden weisen Sie in Ihrem Log Analytics-Arbeitsbereich dem AD-Anwendungs- und Dienstprinzipal, den Sie zuvor für den Configuration Manager erstellt haben, die Rolle *Mitwirkender* zu. Wenn Sie noch keinen Arbeitsbereich haben, lesen Sie zunächst [Erstellen eines Arbeitsbereichs in Azure Monitor](../learn/quick-create-workspace.md), ehe Sie fortfahren. Auf diese Weise kann Configuration Manager Ihren Log Analytics-Arbeitsbereich authentifizieren und darauf zugreifen.  

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

Machen Sie sich unter [Verbinden von Windows-Computern mit Azure Monitor in Azure](agent-windows.md) mit den verfügbaren Methoden für die Installation des Log Analytics-Agents auf dem Computer vertraut, auf dem sich die Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts befindet.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Herstellen einer Verbindung zwischen Configuration Manager und einem Log Analytics-Arbeitsbereich

>[!NOTE]
> Zum Hinzufügen einer Log Analytics-Verbindung muss für die Configuration Manager-Umgebung ein [Dienstverbindungspunkt](/configmgr/core/servers/deploy/configure/about-the-service-connection-point) für den Onlinemodus konfiguriert sein.

> [!NOTE]
> Sie müssen die obersten Ebene in Ihrer Hierarchie mit Azure Monitor verbinden. Wenn Sie einen eigenständigen primären Standort mit Azure Monitor verbinden und dann eine zentrale Verwaltung der Umgebung hinzufügen, müssen Sie die Verbindung innerhalb der neuen Hierarchie löschen und neu erstellen.

1. Wählen Sie im Configuration Manager-Arbeitsbereich **Verwaltung** die Option **Cloud Services** und anschließend **Azure-Dienste** aus. 

2. Klicken Sie mit der rechten Maustaste auf **Azure-Dienste**, und wählen Sie **Azure-Dienste konfigurieren** aus. Die Seite **Azure-Dienste konfigurieren** wird angezeigt. 
   
3. Auf dem Bildschirm **Allgemein** vergewissern Sie sich, dass Sie die folgenden Aktionen durchgeführt haben und dass Sie für jedes Element Detailinformationen haben, und wählen Sie anschließend **Weiter** aus.

4. Gehen Sie auf der Seite „Azure-Dienste“ des Assistenten für Azure-Dienste wie folgt vor:

    1. Geben Sie unter **Name** einen Namen für das Objekt in Configuration Manager an.
    2. Geben Sie optional unter **Beschreibung** eine Beschreibung an, um den Dienst leichter identifizieren zu können.
    3. Wählen Sie unter **OMS-Connector** den OMS-Connector für den Azure-Dienst aus.

    >[!NOTE]
    >OMS wird jetzt als Log Analytics bezeichnet, das ein Feature von Azure Monitor ist.

5. Wählen Sie **Weiter** aus, um zur Seite mit den Azure-App-Eigenschaften des Assistenten für Azure-Dienste zu gelangen.

6. Wählen Sie im Assistenten für Azure-Dienste auf der Seite **App** zunächst die Azure-Umgebung aus der Liste aus, und klicken Sie anschließend auf **Importieren**.

7. Geben Sie auf der Seite **Apps importieren** Folgendes an:

    1. Geben Sie unter **Name des Azure AD-Mandanten** den Namen des Azure AD-Mandanten für die App an.

    2. Geben Sie unter **ID des Azure AD-Mandanten** den Azure AD-Mandanten an. Diese Information finden Sie auf der Azure Active Directory-Seite **Eigenschaften**. 

    3. Geben Sie unter **Anwendungsname** den Anwendungsnamen an.

    4. Geben Sie unter **Client-ID** die Anwendungs-ID der zuvor erstellten Azure AD-App an.

    5. Geben Sie unter **Geheimer Schlüssel** den geheimen Clientschlüssel der erstellten Azure AD-App an.

    6. Geben Sie unter **Ablaufdatum für geheimen Schlüssel** das Ablaufdatum Ihres Schlüssels an.

    7. Geben Sie unter **App-ID-URI** den App-ID-URI der zuvor erstellten Azure AD-App an.

    8. Wählen Sie **Überprüfen** aus. Daraufhin sollte in den Ergebnissen auf der rechten Seite **Erfolgreich überprüft.** angezeigt werden.

8. Überprüfen Sie auf der Seite **Konfiguration**, ob die Felder **Azure-Abonnements**, **Azure-Ressourcengruppe** und **Operations Management Suite-Arbeitsbereich** vorab aufgefüllt wurden, um sich zu vergewissern, dass die Azure AD-Anwendung in der Ressourcengruppe über ausreichende Berechtigungen verfügt. Sind die Felder leer, verfügt Ihre Anwendung nicht über die erforderlichen Rechte. Wählen Sie die Gerätesammlungen aus, die erfasst und an den Arbeitsbereich weitergeleitet werden sollen, und wählen Sie anschließend **Hinzufügen** aus.

9. Überprüfen Sie die Optionen auf der Seite **Einstellungen bestätigen**, und wählen Sie **Weiter** aus, um mit dem Erstellen und Konfigurieren der Verbindung zu beginnen.

10. Nach Abschluss der Konfiguration wird die Seite **Abschluss** angezeigt. Klicken Sie auf **Schließen**. 

Nachdem Sie Configuration Manager mit Azure Monitor verknüpft haben, können Sie Sammlungen hinzufügen oder entfernen und die Eigenschaften der Verbindung anzeigen.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualisieren von Verbindungseigenschaften des Log Analytics-Arbeitsbereichs

Wenn das Kennwort oder der geheime Clientschlüssel abläuft oder verloren geht, müssen Sie die Log Analytics-Verbindungseigenschaften manuell aktualisieren.

1. Wählen Sie im Configuration Manager-Arbeitsbereich **Verwaltung** die Option **Clouddienste** und anschließend **OMS-Connector** aus, um die Seite **OMS-Verbindungseigenschaften** zu öffnen.
2. Klicken Sie auf dieser Seite auf die Registerkarte **Azure Active Directory** zur Anzeige von **Mandant**, **Client-ID**, **Ablauf des geheimen Clientschlüssels**. **Überprüfen Sie** Ihren **geheimen Clientschlüssel**, falls er abgelaufen ist.

## <a name="import-collections"></a>Importieren von Sammlungen

Nachdem Sie eine Log Analytics-Verbindung mit Configuration Manager hinzugefügt und den Agent auf dem Computer mit der Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts installiert haben, müssen als Nächstes Sammlungen aus Configuration Manager als Computergruppen in Azure Monitor importiert werden.

Nachdem Sie die Erstkonfiguration für den Import von Gerätesammlungen aus Ihrer Hierarchie abgeschlossen haben, werden alle drei Stunden die Sammlungsinformationen abgerufen, um die Mitgliedschaft auf dem neuesten Stand zu halten. Sie können diese Einstellung jederzeit deaktivieren.

1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.
2. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den Arbeitsbereich aus, in dem Configuration Manager registriert ist.  
3. Wählen Sie **Erweiterte Einstellungen**.
4. Wählen Sie hierzu **Computergruppen** und anschließend **SCCM** aus.  
5. Wählen Sie **Configuration Manager-Sammlungsmitgliedschaften importieren** aus, und klicken Sie anschließend auf **Speichern**.  
   
    ![Screenshot der erweiterten Einstellungen für Computergruppen für SCCM mit der Option „Hiermit werden Configuration Manager-Sammlungsmitgliedschaften importiert“](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Anzeigen von Daten aus Configuration Manager

Nachdem Sie eine Log Analytics-Verbindung mit Configuration Manager hinzugefügt und den Agent auf dem Computer mit der Standortsystemrolle des Configuration Manager-Dienstverbindungspunkts installiert haben, werden Daten vom Agent an den Log Analytics-Arbeitsbereich in Azure Monitor gesendet. In Azure Monitor werden die Configuration Manager-Sammlungen als [Computergruppen](./computer-groups.md) angezeigt. Die Anzeige von Gruppen wählen Sie auf der Seite **Configuration Manager** unter **Einstellungen\Computergruppen** aus.

Nach dem Import der Sammlungen können Sie sehen, wie viele Computer mit Sammlungsmitgliedschaften erkannt wurden. Sie sehen auch die Anzahl der Sammlungen, die importiert wurden.

![Screenshot der erweiterten Einstellungen für Computergruppen für SCCM mit ausgewählter Option „Hiermit werden Configuration Manager-Sammlungsmitgliedschaften importiert“](./media/collect-sccm/sccm-computer-groups02.png)

Wenn Sie auf eine der Optionen klicken, wird die Protokollabfragen-Editor geöffnet, wobei entweder alle importierten Gruppen oder alle Computer angezeigt werden, die zu den einzelnen Gruppen gehören. Mithilfe der [Protokollsuche](../log-query/log-query-overview.md) können Sie eine ausführlichere Analyse der Sammlungsmitgliedschaftsdaten durchführen.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie [Protokollsuche](../log-query/log-query-overview.md) für ausführliche Informationen zu den Configuration Manager-Daten.

