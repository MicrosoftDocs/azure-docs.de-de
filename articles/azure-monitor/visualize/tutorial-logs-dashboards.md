---
title: Erstellen und Freigeben von Dashboards von Azure Log Analytics-Daten | Microsoft-Dokumentation
description: In diesem Tutorial wird beschrieben, wie in Log Analytics-Dashboards alle gespeicherten Protokollabfragen visualisiert werden können, um Ihnen eine zentrale Übersicht über Ihre Umgebung zu ermöglichen.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: e268ae3a48117d7b1cf6ad00b22cbe3e4d37b5f4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597514"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Erstellen und Freigeben von Dashboards von Log Analytics-Daten

Log Analytics-Dashboards können alle Ihre gespeicherten Protokollabfragen visualisieren und Ihnen so die Möglichkeit geben, IT-Betriebsdaten in der Organisation zu suchen, korrelieren und freizugeben.  Dieses Tutorial behandelt das Erstellen einer Protokollabfrage, die ein freigegebenes Dashboard unterstützt, auf das Ihr IT-Betriebssupportteam zugreift.  Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines freigegebenen Dashboards im Azure-Portal
> * Visualisieren einer Leistungsprotokollabfrage 
> * Hinzufügen einer Protokollabfrage zu einem freigegebenen Dashboard 
> * Anpassen einer Kachel in einem freigegebenen Dashboard

Für das Beispiel in diesem Tutorial muss ein virtueller Computer vorhanden sein, der [mit dem Log Analytics-Arbeitsbereich verbunden](../vm/quick-collect-azurevm.md) ist.  
 
## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an. 

## <a name="create-a-shared-dashboard"></a>Erstellen eines freigegebenen Dashboards
Wählen Sie **Dashboard** aus, um ihr standardmäßiges [Dashboard](../../azure-portal/azure-portal-dashboards.md) zu öffnen. Ihr Dashboard wird sich vom folgenden Beispiel unterscheiden.

![Dashboard des Azure-Portals](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Hier können Sie für all Ihre Azure-Ressourcen die Betriebsdaten zusammenstellen, die für die IT-Abteilung am wichtigsten sind, einschließlich Telemetriedaten aus Azure Log Analytics.  Bevor wir die Visualisierung einer Protokollabfrage durchführen, erstellen wir ein Dashboard und geben es frei.  Wir können uns dann auf unser Beispiel einer Leistungsprotokollabfrage konzentrieren, die als Liniendiagramm dargestellt wird, und sie dem Dashboard hinzufügen.  

> [!NOTE]
> Die folgenden Diagrammtypen werden in Azure-Dashboards mithilfe von Protokollabfragen unterstützt:
> - Bereichsdiagramm
> - Spaltendiagramm
> - Kreisdiagramm (wird im Dashboard als Ring dargestellt)
> - Punktdiagramm
> - Zeitdiagramm

Wählen Sie zum Erstellen eines Dashboards die Schaltfläche **Neues Dashboard** neben dem Namen des aktuellen Dashboards aus.

![Erstellen eines neuen Dashboards im Azure-Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Bei dieser Aktion wird ein neues, leeres, privates Dashboard erstellt. Außerdem wird der Anpassungsmodus aktiviert, in dem Sie dem Dashboard einen Namen geben und Kacheln hinzufügen oder neu anordnen können. Bearbeiten Sie den Namen des Dashboards, geben Sie *Beispieldashboard* für dieses Tutorial ein, und wählen Sie dann **Anpassung abgeschlossen**.<br><br> ![Speichern des benutzerdefinierten Azure-Dashboards](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Wenn Sie ein Dashboard erstellen, ist es standardmäßig ein privates Dashboard. Dies bedeutet, dass Sie die einzige Person sind, die es anzeigen kann. Verwenden Sie die Schaltfläche **Freigeben**, die neben den anderen Dashboardbefehlen angezeigt wird, um es auch für andere Benutzer sichtbar zu machen.

![Freigeben eines neuen Dashboards im Azure-Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Sie werden aufgefordert, für die Veröffentlichung des Dashboards ein Abonnement und eine Ressourcengruppe auszuwählen. Während der Veröffentlichung über das Portal werden Sie der Einfachheit halber zu einem Muster geführt, bei dem Sie Dashboards in einer Ressourcengruppe mit dem Namen **Dashboards** anordnen.  Überprüfen Sie das ausgewählte Abonnement, und klicken Sie dann auf **Veröffentlichen**.  Der Zugriff auf die im Dashboard angezeigten Informationen wird mit [rollenbasierter Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) gesteuert.   

## <a name="visualize-a-log-query"></a>Visualisieren einer Protokollabfrage
[Log Analytics](../logs/log-analytics-tutorial.md) ist ein dediziertes Portal, das für die Arbeit mit Protokollabfragen und deren Ergebnissen verwendet wird. Zu den Funktionen zählen die Möglichkeiten, eine Abfrage in mehreren Zeilen zu bearbeiten, Codes selektiv auszuführen, kontextabhängige IntelliSense-Funktionen auszuführen und intelligente Analysen durchzuführen. In diesem Tutorial erstellen Sie mit Log Analytics eine Leistungsansicht in grafischer Form, speichern sie für eine zukünftige Abfrage und heften sie an das zuvor erstellte freigegebene Dashboard.

Öffnen Sie Log Analytics durch Auswahl von **Protokolle** im Azure Monitor-Menü. Zu Beginn wird eine neue leere Abfrage angezeigt.

![Startseite](media/tutorial-logs-dashboards/homepage.png)

Geben Sie die folgende Abfrage ein, um Prozessorauslastungs-Datensätze für Windows- und Linux-Computer gruppiert nach „Computer“ und TimeGenerated in einem visuellen Diagramm angezeigt zurückzugeben. Klicken Sie auf **Ausführen**, um die Abfrage auszuführen und das resultierende Diagramm anzuzeigen.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Speichern Sie die Abfrage durch Auswählen der Schaltfläche **Speichern** oben auf der Seite.

![Speichern der Abfrage](media/tutorial-logs-dashboards/save-query.png)

Geben Sie unter **Abfrage speichern** einen Namen wie z.B. *Azure-VMs – Prozessorauslastung* und eine Kategorie wie z.B. *Dashboards* ein, und klicken Sie dann auf **Speichern**.  Auf diese Weise können Sie eine Bibliothek für allgemeine Abfragen erstellen, die Sie verwenden und ändern können.  Heften Sie dies schließlich mit Auswahl der Schaltfläche **An Dashboard anheften** in der oberen rechten Ecke der Seite an das zuvor erstellte freigegebene Dashboard an, und wählen Sie dann den Dashboardnamen aus.

Jetzt haben Sie eine Abfrage an das Dashboard geheftet und werden feststellen, dass sie mit einem allgemeinen Titel und darunter mit einem Kommentar versehen ist.

![Beispiel eines Azure-Dashboards](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Sie sollten sie mit einem aussagekräftigeren Namen umbenennen, der für die Benutzer, die sie anzeigen, leichter verständlich ist.  Klicken Sie auf die Schaltfläche „Bearbeiten“, um Titel und Untertitel für die Kachel anzupassen, und klicken Sie dann auf **Aktualisieren**.  Ein Banner wird angezeigt, in dem Sie gefragt werden, ob Sie die Änderungen veröffentlichen oder verwerfen möchten.  Klicken Sie auf **Kopie speichern**.  

![Abgeschlossene Konfiguration des Beispieldashboards](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, ein Dashboard im Azure-Portal zu erstellen und ihm eine Protokollabfrage hinzuzufügen.  Fahren Sie mit dem nächsten Tutorial fort, um die unterschiedlichen Reaktionen kennen zu lernen, die Sie auf der Basis von Protokollabfrageergebnissen implementieren können.  

> [!div class="nextstepaction"]
> [Reagieren auf Ereignisse mit Log Analytics-Warnungen](../alerts/tutorial-response.md)