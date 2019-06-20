---
title: Konfigurieren des Azure Log Analytics-Agents für Windows-Hybridcomputer | Microsoft-Dokumentation
description: In diesem Schnellstart erfahren Sie, wie der Log Analytics-Agent für Windows bereitgestellt wird, der auf Computern außerhalb von Azure ausgeführt wird, und wie Sie die Sammlung von Daten mit Log Analytics aktivieren.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 15885137b9559bf34fb2b985398401af09caa629
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602978"
---
# <a name="configure-the-log-analytics-agent-for-windows-computers-in-a-hybrid-environment"></a>Konfigurieren des Log Analytics-Agents für Windows-Computer in einer Hybridumgebung
[Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) kann Daten direkt von Ihren physischen oder virtuellen Windows-Computern zur detaillierten Analyse und Korrelation in einem einzelnen Repository sammeln. Log Analytics kann Daten aus einem Rechenzentrum oder einer anderen Cloudumgebung sammeln. Dieser Schnellstart zeigt Ihnen, wie Sie in wenigen einfachen Schritten Daten von Ihrem Windows-Computer konfigurieren und sammeln.  Informationen zu virtuellen Azure-Windows-Computern finden Sie unter [Sammeln von Daten zu virtuellen Azure-Computern](../../azure-monitor/learn/quick-collect-azurevm.md).  

Informationen zur unterstützten Konfiguration finden Sie unter [Unterstützte Windows-Betriebssysteme](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) und [Netzwerkfirewall-Konfiguration](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs
1. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie im Suchfeld **Log Analytics** ein. Die Liste wird während Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics** aus:

    ![Azure-Portal](media/quick-collect-windows-computer/azure-portal-01.png)
  
2. Wählen Sie **Erstellen** aus, und geben Sie folgende Informationen ein:

   * Geben Sie einen Namen für den neuen **Log Analytics-Arbeitsbereich** ein. Beispiel: **DefaultLAWorkspace**.
   * Wählen Sie ein **Abonnement** aus, mit dem eine Verknüpfung erstellt werden soll. Wenn die Standardauswahl nicht dem gewünschten entspricht, wählen Sie in der Liste ein anderes aus.
   * Wählen Sie für **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, die virtuelle Azure-Computer enthält.  
   * Wählen Sie den **Speicherort** für die Bereitstellung Ihrer virtuellen Computer aus. Hier ist eine Liste der [Regionen, in denen Log Analytics verfügbar ist](https://azure.microsoft.com/regions/services/).  
   * Wenn Sie einen Arbeitsbereich in einem Abonnement erstellen, das nach dem 2. April 2018 erstellt wurde, wird für den Arbeitsbereich automatisch der Tarif **Pro GB** verwendet. Sie können den Tarif nicht auswählen. Wenn Sie einen Arbeitsbereich für ein Abonnement erstellen, das vor dem 2. April 2018 erstellt oder mit einer vorhandenen EA-Registrierung verknüpft wurde, wählen Sie den gewünschten Tarif aus. Informationen zu Tarifen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Erstellen von Log Analytics-Ressourcen](media/quick-collect-windows-computer/create-loganalytics-workspace-02.png)<br>  

3. Wählen Sie nach dem Bereitstellen der erforderlichen Informationen im **Log Analytics-Arbeitsbereich** die Option **OK** aus.  

Die Informationen werden überprüft, und der Arbeitsbereich wird erstellt. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

## <a name="get-the-workspace-id-and-key"></a>Abrufen von Arbeitsbereichs-ID und Schlüssel
Vor der Installation von Microsoft Monitoring Agent für Windows benötigen Sie die Arbeitsbereichs-ID und den Schlüssel für Ihren Log Analytics-Arbeitsbereich. Der Setup-Assistent benötigt diese Informationen, um den Agent ordnungsgemäß zu konfigurieren und sicherzustellen, dass er mit Log Analytics kommunizieren kann.  

1. Wählen Sie in der linken oberen Ecke des Azure-Portals **Alle Dienste** aus. Geben Sie im Suchfeld **Log Analytics** ein. Die Liste wird während Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
2. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den zuvor erstellten Arbeitsbereich aus. (Möglicherweise haben Sie ihm den Namen **DefaultLAWorkspace** gegeben.)
3. Wählen Sie **Erweiterte Einstellungen** aus:

    ![Log Analytics: erweiterte Einstellungen](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
  
4. Wählen Sie **Verbundene Quellen** und dann **Windows Server** aus.
5. Kopieren Sie die Werte rechts von **Arbeitsbereichs-ID** und **Primärschlüssel**. Fügen Sie sie in Ihrem bevorzugten Editor ein.

## <a name="install-the-agent-for-windows"></a>Installieren des Agents für Windows
Mit den folgenden Schritten wird der Agent für Log Analytics in Azure und Azure Government installiert und konfiguriert. Sie verwenden das Setupprogramm für Microsoft Monitoring Agent, um den Agent auf Ihrem Computer zu installieren.

1. Wählen Sie als Nächstes auf der Seite **Windows Server** für **Windows-Agent herunterladen** die Version aus, die Sie herunterladen möchten. Wählen Sie die entsprechende Version für die Prozessorarchitektur Ihres Windows-Betriebssystems aus.
2. Führen Sie Setup aus, um den Agent auf Ihrem Computer zu installieren.
2. Wählen Sie auf der Seite **Willkommen** die Option **Weiter** aus.
3. Lesen Sie die Seite **Lizenzbedingungen** durch, und klicken Sie anschließend auf **Ich stimme zu**.
4. Auf der Seite **Zielordner** können Sie den Standardinstallationsordner entweder ändern oder beibehalten. Klicken Sie anschließend auf **Weiter**.
5. Stellen Sie über die Seite **Agent-Setupoptionen** eine Verbindung zwischen dem Agent und Azure Log Analytics her, und wählen Sie anschließend **Weiter** aus.
6. Führen Sie auf der Seite **Azure Log Analytics** die folgenden Schritte aus:
   1. Fügen Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** ein, die Sie zuvor kopiert haben. Wenn der Computer einem Log Analytics-Arbeitsbereich in Azure Government unterstellt sein soll, wählen Sie in der Liste **Azure Cloud** die Option **Azure US-Regierung** aus.  
   2. Wenn der Computer über einen Proxyserver mit dem Log Analytics-Dienst kommunizieren muss, klicken Sie auf **Erweitert**, und stellen Sie die URL sowie die Portnummer des Proxyservers bereit. Wenn der Proxyserver eine Authentifizierung erfordert, geben Sie den Benutzernamen und das Kennwort für die Authentifizierung beim Proxyserver ein, und wählen Sie dann **Weiter** aus.  
7. Wählen Sie **Weiter** aus, nachdem Sie die Konfigurationseinstellungen hinzugefügt haben:

    ![Einrichten von Microsoft Monitoring Agent](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

8. Überprüfen Sie Ihre Auswahl auf der Seite **Bereit zum Installieren**, und klicken Sie dann auf **Installieren**.
9. Wählen Sie auf der Seite **Die Konfiguration wurde erfolgreich abgeschlossen** die Option **Fertig stellen** aus.

Wenn Installation und Einrichtung abgeschlossen sind, wird Microsoft Monitoring Agent in der Systemsteuerung angezeigt. Sie können Ihre Konfiguration überprüfen und sicherstellen, dass der Agent mit Log Analytics verbunden ist. Nach der Verbindung zeigt der Agent auf der Registerkarte **Azure Log Analytics** diese Meldung an: **Der Microsoft Monitoring Agent hat erfolgreich eine Verbindung mit dem Microsoft Log Analytics-Dienst hergestellt.**<br><br> ![MMA-Verbindungsstatus](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Sammeln von Ereignis- und Leistungsdaten
Log Analytics kann angegebene Ereignisse aus dem Windows-Ereignisprotokoll und Leistungsindikatoren für längerfristige Analysen und Berichte sammeln. Es kann auch Maßnahmen ergreifen, wenn eine bestimmte Bedingung erkannt wird. Führen Sie diese Schritte aus, um die Sammlung von Ereignissen aus dem Ereignisprotokoll von Windows sowie mehreren allgemeinen Leistungsindikatoren zu konfigurieren.  

1. Wählen Sie in der linken oberen Ecke des Azure-Portals **Weitere Dienste** aus. Geben Sie im Suchfeld **Log Analytics** ein. Die Liste wird während Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
2. Wählen Sie **Erweiterte Einstellungen** aus:

    ![Log Analytics: erweiterte Einstellungen](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
 
3. Wählen Sie **Daten** und dann **Windows-Ereignisprotokolle**.  
4. Sie können ein Ereignisprotokoll hinzufügen, indem Sie den Namen des Protokolls eingeben. Geben Sie **System** ein, und wählen Sie dann das Pluszeichen **+** aus.  
5. Wählen Sie in der Tabelle die Schweregrade **Fehler** und **Warnung** aus.
6. Wählen Sie im oberen Bereich der Seite **Speichern** aus.
7. Wählen Sie **Windows-Leistungsindikatoren** aus, um die Sammlung von Leistungsindikatoren auf einem Windows-Computer zu aktivieren.
8. Wenn Sie die Windows-Leistungsindikatoren zum ersten Mal für einen neuen Log Analytics-Arbeitsbereich konfigurieren, haben Sie die Möglichkeit, schnell mehrere allgemeine Indikatoren zu erstellen. Jede Option wird mit einem Kontrollkästchen aufgeführt:

    ![Windows-Leistungsindikatoren](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Wählen Sie **Ausgewählte Leistungsindikatoren hinzufügen** aus. Die Leistungsindikatoren werden hinzugefügt und mit einem Stichprobenintervall von zehn Sekunden voreingestellt.

9. Wählen Sie im oberen Bereich der Seite **Speichern** aus.

## <a name="view-collected-data"></a>Anzeigen der gesammelten Daten
Jetzt haben Sie die Datensammlung aktiviert und können eine einfache Protokollsuche ausführen, um einige Daten vom Zielcomputer anzuzeigen.  

1. Wählen Sie im Azure-Portal im ausgewählten Arbeitsbereich die Kachel **Protokolle** aus.  
2. Geben Sie im Bereich **Protokollsuche** im Abfragefeld die Zeichenfolge **Perf** ein, und klicken Sie über dem Abfragefeld auf **Ausführen**:
 
    ![Log Analytics-Protokollsuche](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Die Abfrage in dieser Abbildung gibt beispielsweise 735 Leistungsdatensätze zurück:

    ![Log Analytics: Ergebnis der Protokollsuche](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie können den Agent vom Computer entfernen und den Log Analytics-Arbeitsbereich löschen, wenn Sie sie nicht mehr benötigen.  

Gehen Sie wie folgt vor, um den Agent zu entfernen:

1. Öffnen Sie die Systemsteuerung.
2. Öffnen Sie **Programme und Features**.
3. Wählen Sie in **Programme und Features** die Option **Microsoft Monitoring Agent** aus, und wählen Sie **Deinstallieren** aus.

Um den zuvor erstellten Log Analytics-Arbeitsbereich zu löschen, wählen Sie ihn aus, und wählen Sie dann auf der Ressourcenseite **Löschen** aus:

![Löschen des Log Analytics-Arbeitsbereichs](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nächste Schritte
Jetzt sammeln Sie Betriebs- und Leistungsdaten von Ihrem Windows-Computer und können problemlos damit beginnen, *kostenlos* Daten zu untersuchen, zu analysieren und Aktionen mit ihnen auszuführen.  

Um zu erfahren, wie Sie die Daten anzeigen und analysieren, fahren Sie mit dem Tutorial fort:

> [!div class="nextstepaction"]
> [Anzeigen oder Analysieren der Daten in Log Analytics](tutorial-viewdata.md)
