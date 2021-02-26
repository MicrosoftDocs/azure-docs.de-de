---
title: Problembehandlung bei Änderungen auf einem virtuellen Azure-Computer in Azure Automation | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Probleme bei Änderungen auf einem virtuellen Azure-Computer beheben.
services: automation
ms.subservice: change-inventory-management
keywords: Änderung, Nachverfolgung, Änderungsnachverfolgung, Bestand, Automation
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a223ac4296dd160bbdd904e1d3443552d4f49a23
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587931"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Problembehandlung bei Änderungen auf einem virtuellen Azure-Computer

In diesem Tutorial wird beschrieben, wie Sie die Problembehandlung für Änderungen auf einem virtuellen Azure-Computer durchführen. Indem Sie die Änderungsnachverfolgung und den Bestand aktivieren, können Sie Änderungen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern nachverfolgen.
Die Identifizierung dieser Konfigurationsänderungen ermöglicht die Erkennung von betriebsbezogenen Problemen in Ihrer Umgebung.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der Lösung für Änderungsnachverfolgung und Bestand für einen virtuellen Computer
> * Durchsuchen von Änderungsprotokollen nach beendeten Diensten
> * Konfigurieren der Änderungsnachverfolgung
> * Aktivieren der Aktivitätsprotokollverbindung
> * Auslösen eines Ereignisses
> * Anzeigen von Änderungen
> * Konfigurieren von Warnungen

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* Ein [Automation-Konto](./index.yml) für die Watcher- und Aktionsrunbooks und den Watchertask.
* Ein [virtueller Computer](../virtual-machines/windows/quick-create-portal.md) zur Aktivierung für das Feature.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="enable-change-tracking-and-inventory"></a>Aktivieren der Lösung für Änderungsnachverfolgung und Bestand

In diesem Tutorial müssen Sie zuerst die Änderungsnachverfolgung und die Bestandsfunktion aktivieren. Wenn Sie das Feature bereits aktiviert haben, ist dieser Schritt nicht erforderlich.

>[!NOTE]
>Wenn die Felder abgeblendet sind, ist ein anderes Automation-Feature für die VM aktiviert, und Sie müssen denselben Arbeitsbereich und dasselbe Automation-Konto verwenden.

1. Wählen Sie die Option **Virtuelle Computer** und dann in der Liste einen virtuellen Computer (VM) aus.
2. Wählen Sie im Menü auf der linken Seite unter **Vorgänge** die Option **Bestand** aus. Die Seite „Bestand“ wird geöffnet.

    ![Änderung aktivieren](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Wählen Sie den Arbeitsbereich [Log Analytics](../azure-monitor/logs/log-query-overview.md) aus. Dieser Arbeitsbereich sammelt Daten, die durch Features wie „Änderungsnachverfolgung“ und „Bestand“ generiert werden. Der Arbeitsbereich ist ein zentraler Ort zum Überprüfen und Analysieren von Daten aus mehreren Quellen.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Wählen Sie das Automation-Konto aus, das Sie verwenden möchten.

5. Konfigurieren Sie den Speicherort für die Bereitstellung.

5. Klicken Sie auf **Aktivieren**, um das Feature für Ihre VM bereitzustellen. 

Während des Setups wird der virtuelle Computer mit dem Log Analytics-Agent für Windows und einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) bereitgestellt. Das Aktivieren der Änderungsnachverfolgung und des Bestands kann bis zu 15 Minuten dauern. Während dieses Zeitraums sollten Sie das Browserfenster nicht schließen.

Nachdem das Feature aktiviert wurde, werden Informationen zur installierten Software und Änderungen der VM-Datenflüsse zu Azure Monitor-Protokollen angezeigt.
Es kann zwischen 30 Minuten und 6 Stunden dauern, bis die Daten für die Analyse verfügbar sind.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Verwenden von Änderungsnachverfolgung und Bestand in Azure Monitor-Protokollen

Bei Änderungsnachverfolgung und Bestand werden Protokolldaten generiert, die an Azure Monitor-Protokolle gesendet werden. Um die Protokolle per Ausführung von Abfragen zu durchsuchen, wählen Sie oben auf der Seite „Änderungsnachverfolgung“ die Option **Log Analytics** aus. Die Daten der Änderungsnachverfolgung werden unter dem Typ `ConfigurationChange` gespeichert.

Mit der folgenden Log Analytics-Beispielabfrage werden alle Windows-Dienste zurückgegeben, die beendet wurden.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Weitere Informationen zur Ausführung von Abfragen und zum Durchsuchen von Protokolldateien in Azure Monitor-Protokollen finden Sie unter [Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md).

## <a name="configure-change-tracking"></a>Konfigurieren der Änderungsnachverfolgung

Bei der Änderungsnachverfolgung wählen Sie die zu sammelnden und nachzuverfolgenden Dateien und Registrierungsschlüssel mit **Einstellungen bearbeiten** oben auf der Seite „Änderungsnachverfolgung“ Ihrer VM aus. Sie können Windows-Registrierungsschlüssel, Windows-Dateien oder Linux-Dateien zur Nachverfolgung auf der Seite „Arbeitsbereichskonfiguration“ hinzufügen.

> [!NOTE]
> Für die Bestandsfunktion und Änderungsnachverfolgung werden dieselben Sammlungseinstellungen verwendet, und die Einstellungen werden auf einer Arbeitsbereichsebene konfiguriert.

### <a name="add-a-windows-registry-key"></a>Hinzufügen eines Windows-Registrierungsschlüssels

1. Wählen Sie auf der Registerkarte **Windows-Registrierung** die Option **Hinzufügen**. 

1. Geben Sie auf der Seite „Windows-Registrierung für Änderungsnachverfolgung hinzufügen“ die Informationen zu dem Schlüssel ein, der nachverfolgt werden soll, und klicken Sie auf **Speichern**.

    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |Aktiviert     | Bestimmt, ob die Einstellung angewendet wird        |
    |Item Name     | Anzeigename der nachzuverfolgenden Datei        |
    |Group     | Ein Gruppenname für die logische Gruppierung von Dateien        |
    |Windows-Registrierungsschlüssel   | Der zu überprüfende Pfad für die Datei. Beispiel: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Hinzufügen einer Windows-Datei

1. Wählen Sie auf der Registerkarte **Windows-Dateien** die Option **Hinzufügen**. 

1. Geben Sie auf der Seite „Windows-Datei für Änderungsnachverfolgung hinzufügen“ die Informationen zu der Datei oder dem Verzeichnis ein, die bzw. das nachverfolgt werden soll, und klicken Sie auf **Speichern**.

    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |Aktiviert     | Bestimmt, ob die Einstellung angewendet wird        |
    |Item Name     | Anzeigename der nachzuverfolgenden Datei        |
    |Group     | Ein Gruppenname für die logische Gruppierung von Dateien        |
    |Pfad eingeben     | Der zu überprüfende Pfad für die Datei, z.B. „c:\temp\\\*.txt“<br>Sie können auch Umgebungsvariablen verwenden, beispielsweise „%winDir%\System32\\\*.*“.         |
    |Rekursion     | Bestimmt, ob beim Suchen nach dem nachzuverfolgenden Element die Rekursion verwendet wird        |
    |Hochladen von Dateiinhalt für alle Einstellungen| Aktiviert oder deaktiviert den Upload des Dateiinhalts für nachverfolgte Änderungen. Verfügbare Optionen: **True** und **False**.|

### <a name="add-a-linux-file"></a>Hinzufügen einer Linux-Datei

1. Wählen Sie auf der Registerkarte **Linux-Dateien** die Option **Hinzufügen**. 

1. Geben Sie auf der Seite „Linux-Datei für Änderungsnachverfolgung hinzufügen“ die Informationen zu der Datei oder dem Verzeichnis ein, die bzw. das nachverfolgt werden soll, und klicken Sie auf **Speichern**.

    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |Aktiviert     | Bestimmt, ob die Einstellung angewendet wird        |
    |Item Name     | Anzeigename der nachzuverfolgenden Datei        |
    |Group     | Ein Gruppenname für die logische Gruppierung von Dateien        |
    |Pfad eingeben     | Der zu überprüfende Pfad für die Datei, z.B. „/etc/*.conf“       |
    |Pfadtyp     | Typ des nachzuverfolgenden Elements (mögliche Werte sind „File“ und „Directory“)        |
    |Rekursion     | Bestimmt, ob beim Suchen nach dem nachzuverfolgenden Element die Rekursion verwendet wird        |
    |Sudo verwenden     | Diese Einstellung bestimmt, ob „sudo“ bei der Suche nach dem Element verwendet wird         |
    |Links     | Diese Einstellung bestimmt, wie symbolische Verknüpfungen beim Durchlaufen von Verzeichnissen behandelt werden<br> **Ignore**: Symbolische Links werden ignoriert, und die referenzierten Dateien/Verzeichnisse werden nicht einbezogen.<br>**Follow**: Folgt den symbolischen Links während der Rekursion und bindet auch die referenzierten Dateien/Verzeichnisse ein.<br>**Manage**: Folgt den symbolischen Links und ermöglicht eine Änderung von zurückgegebenen Inhalten.      |
    |Hochladen von Dateiinhalt für alle Einstellungen| Aktiviert oder deaktiviert den Upload des Dateiinhalts für nachverfolgte Änderungen. Verfügbare Optionen: „True“ oder „False“.|

   > [!NOTE]
   > Der Wert **Manage** wird für die **Links**-Eigenschaft nicht empfohlen. Das Abrufen von Dateiinhalten wird nicht unterstützt.

## <a name="enable-activity-log-connection"></a>Aktivieren der Aktivitätsprotokollverbindung

1. Wählen Sie auf Ihrer VM auf der Seite „Änderungsnachverfolgung“ die Option **Aktivitätsprotokollverbindung verwalten** aus. 

2. Klicken Sie auf der Azure Activity-Protokollseite auf **Verbinden**, um Änderungsnachverfolgung und Bestand mit dem Azure Activity-Protokoll für Ihre VM zu verbinden.

3. Navigieren Sie auf die Seite „Übersicht“ Ihrer VM, und wählen Sie **Beenden** aus, um die VM zu beenden. 

4. Wählen Sie nach der entsprechenden Aufforderung **Ja**, um die VM zu beenden. 

5. Wenn die Zuordnung des virtuellen Computers aufgehoben ist, wählen Sie **Starten** aus, um ihn neu zu starten. Beim Beenden und Starten einer VM wird im Aktivitätsprotokoll dazu ein Ereignis protokolliert. 

## <a name="view-changes"></a>Anzeigen von Änderungen

1. Navigieren Sie zurück zum Bereich „Änderungsnachverfolgung“, und wählen Sie unten im Bereich die Registerkarte **Ereignisse** aus. 

2. Nach einer Weile werden Ereignisse der Änderungsnachverfolgung im Diagramm und in der Tabelle angezeigt. Im Diagramm werden Änderungen angezeigt, die im Laufe der Zeit durchgeführt wurden. Im Liniendiagramm oben werden Ereignisse des Azure Activity-Protokolls angezeigt. Jede Zeile mit Balkendiagrammen steht für einen eigenen Änderungstyp, der nachverfolgt werden kann. Diese Typen sind Linux-Daemons, Dateien, Windows-Registrierungsschlüssel, Software und Windows-Dienste. Die Registerkarte „Änderung“ zeigt die Details für die angezeigten Änderungen, wobei die neueste Änderung zuerst angezeigt wird.

    ![Anzeigen von Ereignissen im Portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Beachten Sie, dass es mehrere Änderungen am System gegeben hat, einschließlich Änderungen an Diensten und Software. Sie können die Filter oben auf der Seite nutzen, um die Ergebnisse nach **Änderungstyp** oder Zeitraum zu filtern.

    ![Liste der Änderungen an der VM](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Wählen Sie eine **WindowsServer**-Änderung aus. Durch diese Auswahl wird das Fenster „Änderungsdetails“ geöffnet, das Details zur Änderung und die Werte vor und nach der Änderung anzeigt. Für diese Instanz wurde der Dienst „Software Protection“ beendet.

    ![Anzeigen von Details zu den Änderungen im Portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Konfigurieren von Warnungen

Das Anzeigen von Änderungen im Azure-Portal kann praktisch sein, es ist jedoch nützlicher, über Änderungen (etwa einen beendeten Dienst) informiert zu werden. Lassen Sie uns eine Warnung für einen beendeten Dienst hinzufügen. 

1. Wechseln Sie im Azure-Portal zu **Monitor**. 

2. Wählen Sie **Warnungen** unter **Gemeinsame Dienste** aus, und klicken Sie auf **+ Neue Warnungsregel**.

3. Klicken Sie auf **Auswählen**, um eine Ressource auszuwählen. 

4. Wählen Sie auf der Seite „Ressource auswählen“ im Dropdownmenü **Nach Ressourcentyp filtern** die Option **Log Analytics** aus. 

5. Wählen Sie Ihren Log Analytics-Arbeitsbereich aus, und klicken Sie dann auf **Fertig**.

    ![Auswählen einer Ressource](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Klicken Sie auf **Bedingung hinzufügen**.

7. Wählen Sie in der Tabelle auf der Seite „Signallogik konfigurieren“ die Option **Benutzerdefinierte Protokollsuche** aus. 

8. Geben Sie im Textfeld „Suchabfrage“ die folgende Abfrage ein:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    Diese Abfrage gibt die Computer zurück, auf denen der W3SVC-Dienst im angegebenen Zeitraum beendet wurde.

9. Geben Sie für **Schwellenwert** unter **Warnungslogik** den Wert **0** ein. Klicken Sie abschließend auf **Fertig**.

    ![Konfigurieren der Signallogik](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. Wählen Sie unter **Aktionsgruppen** die Option **Neu erstellen** aus. Eine Aktionsgruppe ist eine Gruppe mit Aktionen, die Sie übergreifend für mehrere Warnungen verwenden können. Dies können beispielsweise E-Mail-Benachrichtigungen, Runbooks, Webhooks und vieles mehr sein. Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/alerts/action-groups.md).

11. Geben Sie unter **Warnungsdetails definieren** einen Namen und eine Beschreibung für die Warnung ein. 

12. Legen Sie **Schweregrad** auf **Information (Schweregrad 2)** , **Warnung (Schweregrad 1)** oder **Kritisch (Schweregrad 0)** fest.

13. Geben Sie im Feld **Name der Aktionsgruppe** einen Namen für die Warnung und darunter einen Kurznamen ein. Der Kurzname wird anstelle eines vollständigen Aktionsgruppennamens verwendet, wenn Benachrichtigungen mithilfe dieser Gruppe gesendet werden.

14. Geben Sie für **Aktionen** einen Namen für die Aktion ein, z. B. **E-Mail-Administratoren**. 

15. Wählen Sie unter **AKTIONSTYP** die Option **E-Mail/SMS/Push/Sprachanruf** aus. 

16. Wählen Sie unter **DETAILS** die Option **Details bearbeiten** aus.

    ![Hinzufügen einer Aktionsgruppe](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. Geben Sie im Bereich „E-Mail/SMS/Push/Sprachanruf“ einen Namen ein, aktivieren Sie das Kontrollkästchen **E-Mail**, und geben Sie dann eine gültige E-Mail-Adresse ein. Wenn Sie fertig sind, klicken Sie im Bereich auf **OK**, und klicken Sie dann auf der Seite „Aktionsgruppe hinzufügen“ auf **OK**.

18. Wählen Sie **Aktionen anpassen** aus, um den Betreff der Warnungs-E-Mail anzupassen. 

19. Wählen Sie für **Regel erstellen** die Option **E-Mail-Betreff** aus, und wählen Sie dann **Warnungsregel erstellen**. Die Warnung informiert Sie, wenn die Bereitstellung eines Updates erfolgreich war. Außerdem wird angegeben, welche Computer Teil der durchgeführten Updatebereitstellung waren. Die folgende Abbildung zeigt eine Beispiel-E-Mail, die bei Beendigung des W3SVC-Diensts eingeht.

    ![Screenshot: E-Mail-Benachrichtigung, die bei Beendigung des W3SVC-Diensts eingeht](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren der Lösung für Änderungsnachverfolgung und Bestand für einen virtuellen Computer
> * Durchsuchen von Änderungsprotokollen nach beendeten Diensten
> * Konfigurieren der Änderungsnachverfolgung
> * Aktivieren der Aktivitätsprotokollverbindung
> * Auslösen eines Ereignisses
> * Anzeigen von Änderungen
> * Konfigurieren von Warnungen

Fahren Sie mit der Übersicht über das Feature für die Änderungsnachverfolgung und den Bestand fort, um mehr darüber zu erfahren.

> [!div class="nextstepaction"]
> [Übersicht über Änderungsnachverfolgung und Bestand](change-tracking/overview.md)
