---
title: 'Schnellstart: Sammeln von Daten von einem Linux-Hybridcomputer mit Azure Monitor'
description: In dieser Schnellstartanleitung erfahren Sie, wie der Log Analytics-Agent für Linux-Computer bereitgestellt wird, die außerhalb von Azure ausgeführt werden, und wie Sie die Sammlung von Daten mit Azure Monitor-Protokollen aktivieren.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 4527d871df3667f483efd529d93f044377f34651
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186183"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Schnellstart: Sammeln von Daten von einem Linux-Computer in einer Hybridumgebung mit Azure Monitor

[Azure Monitor](../overview.md) kann Daten direkt von Ihren physischen oder virtuellen Linux-Computern in Ihrer Umgebung zur detaillierten Analyse und Korrelation in einem Log Analytics-Arbeitsbereich sammeln. Wenn Sie den [Log Analytics-Agent](../platform/log-analytics-agent.md) installieren, kann Azure Monitor Daten aus einem Datencenter oder einer anderen Cloudumgebung sammeln. Diese Schnellstartanleitung zeigt Ihnen, wie Sie in wenigen einfachen Schritten Daten von Ihrem Linux-Server konfigurieren und sammeln. Informationen zu virtuellen Azure-Linux-Computern finden Sie unter [Sammeln von Daten über virtuelle Azure-Computer](./quick-collect-azurevm.md).  

Informationen zur unterstützten Konfiguration finden Sie unter [Unterstützte Betriebssysteme](../platform/agents-overview.md#supported-operating-systems) und [Netzwerkfirewall-Konfiguration](../platform/log-analytics-agent.md#network-requirements).
 
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an. 

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

1. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.

    ![Suchen eines Log Analytics-Arbeitsbereichs im Azure-Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Wählen Sie die Option **Erstellen** und anschließend Optionen für die folgenden Elemente aus:

   * Geben Sie einen Namen für den neuen **Log Analytics-Arbeitsbereich** ein, z.B. *DefaultLAWorkspace*.  
   * Wählen Sie ein **Abonnement** aus, mit dem eine Verknüpfung erstellt werden soll, indem Sie in der Dropdownliste einen anderen Eintrag auswählen, falls der Standardeintrag nicht geeignet ist.
   * Wählen Sie für **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, die einen oder mehrere virtuelle Azure-Computer enthält.  
   * Wählen Sie den **Speicherort** für die Bereitstellung Ihrer virtuellen Computer aus.  Weitere Informationen finden Sie auf der Seite zur [Verfügbarkeit von Log Analytics in den einzelnen Regionen](https://azure.microsoft.com/regions/services/).
   * Wenn Sie einen Arbeitsbereich in einem neuen Abonnement erstellen, das nach dem 2. April 2018 erstellt wurde, wird automatisch der Tarif *Pro GB* verwendet. In diesem Fall ist keine Tarifauswahloption verfügbar.  Wenn Sie einen Arbeitsbereich für ein Abonnement erstellen, das vor dem 2. April erstellt oder mit einer vorhandenen EA-Registrierung verknüpft wurde, wählen Sie Ihren bevorzugten Tarif aus.  Weitere Informationen zu den einzelnen Tarifen finden Sie unter [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Wählen Sie nach dem Bereitstellen der erforderlichen Informationen im Bereich **Log Analytics-Arbeitsbereich** die Option **OK** aus.  

Die Informationen werden überprüft, und der Arbeitsbereich wird erstellt. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen. 

## <a name="obtain-workspace-id-and-key"></a>Abrufen von Arbeitsbereichs-ID und -Schlüssel

Vor der Installation des Log Analytics-Agents für Linux benötigen Sie die Arbeitsbereichs-ID und den Schlüssel für Ihren Log Analytics-Arbeitsbereich. Diese Informationen sind für das Wrapperskript des Agents erforderlich, um den Agent ordnungsgemäß zu konfigurieren und sicherzustellen, dass er erfolgreich mit Azure Monitor kommunizieren kann.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Wählen Sie in der linken oberen Ecke des Azure-Portals **Alle Dienste** aus. Geben Sie im Suchfeld **Log Analytics** ein. Die Liste wird während Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.

2. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den zuvor erstellten Arbeitsbereich aus. (Möglicherweise haben Sie ihm den Namen **DefaultLAWorkspace** gegeben.)

3. Wählen Sie **Agent-Verwaltung** aus:
 
4. Wählen Sie anschließend **Linux-Server** aus.

5. Die Werte rechts von **Arbeitsbereichs-ID** und **Primärschlüssel**: Kopieren Sie beide Angaben, und fügen Sie sie in den von Ihnen bevorzugten Editor ein.

## <a name="install-the-agent-for-linux"></a>Installieren des Agents für Linux

Die folgenden Schritte konfigurieren das Setup des Agents für Log Analytics in Azure und Azure Government Cloud.  

>[!NOTE]
>Der Log Analytics-Agent für Linux kann nicht für die Berichterstattung für mehrere Log Analytics-Arbeitsbereiche konfiguriert werden.  

Wenn Ihr Linux-Computer über einen Proxyserver mit Log Analytics kommunizieren muss, kann die Proxykonfiguration in der Befehlszeile durch den Zusatz `-p [protocol://][user:password@]proxyhost[:port]` angegeben werden.  Für die *proxyhost*-Eigenschaft kann ein vollqualifizierter Domänenname oder eine IP-Adresse des Proxyservers verwendet werden. 

Beispiel: `https://user01:password@proxy01.contoso.com:30443`

1. Führen Sie den folgenden Befehl aus, und stellen Sie die zuvor kopierte Arbeitsbereichs-ID sowie den Primärschlüssel bereit, um den Linux-Computer so zu konfigurieren, dass er eine Verbindung mit einem Log Analytics-Arbeitsbereich herstellt. Mit dem folgenden Befehl wird der Agent heruntergeladen, die Prüfsumme des Agents überprüft und anschließend der Agent installiert. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Der folgende Befehl enthält den `-p`-Proxyparameter und die Beispielsyntax für den Fall, dass die Authentifizierung für Ihren Proxyserver erforderlich ist:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Führen Sie den folgenden Befehl aus, und stellen Sie die zuvor kopierte Arbeitsbereichs-ID sowie den Primärschlüssel bereit, um den Linux-Computer so zu konfigurieren, dass er eine Verbindung mit einem Log Analytics-Arbeitsbereich in Azure Government Cloud herstellt. Mit dem folgenden Befehl wird der Agent heruntergeladen, die Prüfsumme des Agents überprüft und anschließend der Agent installiert. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Der folgende Befehl enthält den `-p`-Proxyparameter und die Beispielsyntax für den Fall, dass die Authentifizierung für Ihren Proxyserver erforderlich ist:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Starten Sie den Agent neu, indem Sie den folgenden Befehl ausführen: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Sammeln von Ereignis- und Leistungsdaten

Azure Monitor kann Ereignisse aus Linux-Syslog und den von Ihnen angegebenen Leistungsindikatoren für längerfristige Analysen und Berichte sammeln. Es kann auch Maßnahmen ergreifen, wenn eine bestimmte Bedingung erkannt wird. Führen Sie diese Schritte aus, um die Sammlung von Ereignissen aus dem Linux-Syslog sowie mehreren allgemeinen Leistungsindikatoren zu konfigurieren.  

1. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Liste mit den Ressourcen „Log Analytics“ ein. Die Liste wird während Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus. Dann wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den gesuchten Arbeitsbereich und anschließend **Erweiterte Einstellungen** für den **Log Analytics**-Arbeitsbereich aus.

2. Wählen Sie **Daten** und dann **Syslog** aus.  

3. Sie können Syslog hinzufügen, indem Sie den Namen des Protokolls eingeben. Geben Sie **Syslog** ein, und wählen Sie dann das Pluszeichen ( **+** ) aus.  

4. Deaktivieren Sie in der Tabelle die Schweregrade **Info**, **Hinweis** und **Debuggen**. 

5. Wählen Sie ganz oben auf der Seite **Speichern** aus, um die Konfiguration zu speichern.

6. Klicken Sie auf **Linux-Leistungsdaten**, um die Erfassung von Leistungsindikatoren auf einem Linux-Computer zu aktivieren. 

7. Wenn Sie die Linux-Leistungsindikatoren zum ersten Mal für einen neuen Log Analytics-Arbeitsbereich konfigurieren, haben Sie die Möglichkeit, schnell mehrere allgemeine Indikatoren zu erstellen. Diese werden in einer Liste aufgeführt, und neben jedem Indikator finden Sie ein Kontrollkästchen.

    ![In Azure Monitor ausgewählte Standard-Linux-Leistungsindikatoren](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Wählen Sie **Nachstehende Konfiguration auf meine Computer anwenden**  und dann **Ausgewählte Leistungsindikatoren hinzufügen** aus. Sie werden hinzugefügt und mit einem Stichprobenintervall von zehn Sekunden voreingestellt.  

8. Wählen Sie ganz oben auf der Seite **Speichern** aus, um die Konfiguration zu speichern.

## <a name="view-data-collected"></a>Anzeigen gesammelter Daten

Jetzt haben Sie die Datensammlung aktiviert und können ein einfaches Protokollsuchebeispiel ausführen, um einige Daten vom Zielcomputer anzuzeigen.  

1. Wählen Sie im ausgewählten Arbeitsbereich im linken Bereich **Protokolle** aus.

2. Geben Sie auf der Seite „Protokollabfrage“ im Abfrageeditor `Perf` ein, und wählen Sie **Ausführen** aus.
 
    ![Log Analytics-Protokollsuche](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Die Abfrage in der folgenden Abbildung gibt beispielsweise 10.000 Leistungsdatensätze zurück. Ihre Ergebnisse werden deutlich geringer ausfallen.

    ![Log Analytics: Ergebnis der Protokollsuche](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn er nicht mehr benötigt wird, können Sie den Agent vom Linux-Computer entfernen und den Log Analytics-Arbeitsbereich löschen.  

Führen Sie den folgenden Befehl auf dem Linux-Computer aus, um den Agent zu entfernen. Mit dem Argument *--purge* werden der Agent und die dazugehörige Konfiguration vollständig entfernt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Wählen Sie zum Löschen des Arbeitsbereichs den Log Analytics-Arbeitsbereich aus, den Sie zuvor erstellt haben, und wählen Sie auf der Ressourcenseite **Löschen** aus.

![Löschen von Log Analytics-Ressourcen](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nächste Schritte

Jetzt sammeln Sie Betriebs- und Leistungsdaten von Ihrem lokalen Linux-Computer und können problemlos beginnen, *kostenlos* Daten zu untersuchen, zu analysieren und Aktionen an ihnen vorzunehmen.  

Um zu erfahren, wie Sie die Daten anzeigen und analysieren, fahren Sie mit dem Tutorial fort.

> [!div class="nextstepaction"]
> [Anzeigen oder Analysieren der Daten in Log Analytics](../log-query/log-analytics-tutorial.md)