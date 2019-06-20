---
title: Überwachen von Azure-Diensten und -Anwendungen mit Grafana
description: Es wird beschrieben, wie Sie Azure Monitor- und Application Insights-Daten weiterleiten, um sie in Grafana anzuzeigen.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: ''
ms.openlocfilehash: e9a20aba84e79e87f84d63e4bdae3ba1aac062f5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387190"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Überwachen Ihrer Azure-Dienste in Grafana
Sie können Azure-Dienste und -Anwendungen jetzt mit [Grafana](https://grafana.com/) überwachen, indem Sie das [Azure Monitor-Datenquellen-Plug-In](https://grafana.com/plugins/grafana-azure-monitor-datasource) verwenden. Das Plug-In sammelt Daten zur Anwendungsleistung, die von Azure Monitor erfasst wurden, einschließlich verschiedener Protokolle und Metriken. Sie können diese Daten dann in Ihrem Grafana-Dashboard anzeigen.

Das Plug-In befindet sich derzeit in der Vorschauphase.

Führen Sie die folgenden Schritte aus, um einen Grafana-Server einzurichten und Dashboards für Metriken und Protokolle von Azure Monitor zu erstellen.

## <a name="set-up-a-grafana-server"></a>Einrichten eines Grafana-Servers

### <a name="set-up-grafana-locally"></a>Lokales Einrichten von Grafana
Um einen lokalen Grafana-Server einzurichten, müssen Sie [Grafana herunterladen und in Ihrer lokalen Umgebung installieren](https://grafana.com/grafana/download). Um die Azure Monitor-Integration des Plug-Ins zu verwenden, installieren Sie Grafana 5.3 oder eine höhere Version.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Einrichten von Grafana in Azure über den Azure Marketplace
1. Navigieren Sie zum Azure Marketplace, und wählen Sie Grafana von Grafana Labs aus.

2. Geben Sie Namen und Details an. Erstellen Sie eine neue Ressourcengruppe. Bewahren Sie die Informationen auf, die Sie für den VM-Benutzernamen, das VM-Kennwort und das Grafana-Serveradministratorkennwort angeben.  

3. Wählen Sie die VM-Größe und ein Speicherkonto aus.

4. Konfigurieren Sie die Einstellungen für die Netzwerkkonfiguration.

5. Zeigen Sie die Zusammenfassung an, und wählen Sie **Erstellen**, nachdem Sie die Nutzungsbedingungen akzeptiert haben.

6. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**. Eine Liste mit den neu erstellten Ressourcen wird angezeigt.

    ![Grafana-Ressourcengruppenobjekte](media/grafana-plugin/grafana1.png)

    Wenn Sie die Netzwerksicherheitsgruppe auswählen (hier *grafana-nsg*), sehen Sie, dass Port 3000 zum Zugreifen auf den Grafana-Server verwendet wird.

7. Rufen Sie die öffentliche IP-Adresse Ihres Grafana-Servers ab: Gehen Sie zurück zur Ressourcenliste, und wählen Sie **öffentliche IP-Adresse** aus.

## <a name="sign-in-to-grafana"></a>Anmelden an Grafana

1. Verwenden Sie die IP-Adresse Ihres Servers, und öffnen Sie die Anmeldeseite unter *http://\<IP-Adresse\>:3000* oder *\<DNS-Name>\:3000* in Ihrem Browser. 3000 ist zwar der Standardport, aber beachten Sie, dass Sie bei der Einrichtung möglicherweise einen anderen Port ausgewählt haben. Es sollte eine Anmeldeseite für den zuvor erstellten Grafana-Server angezeigt werden.

    ![Grafana-Anmeldebildschirm](./media/grafana-plugin/grafana-login-screen.png)

2. Melden Sie sich mit dem Benutzernamen *admin* und dem zuvor erstellten Grafana-Serveradministratorkennwort an. Falls Sie eine lokale Einrichtung verwenden, lautet das Standardkennwort *admin*. Bei Ihrer ersten Anmeldung werden Sie aufgefordert, es zu ändern.

## <a name="configure-data-source-plugin"></a>Konfigurieren des Datenquellen-Plug-Ins

Nach der erfolgreichen Anmeldung sollte zu sehen sein, dass das Azure Monitor-Datenquellen-Plug-In bereits vorhanden ist.

![Azure Monitor-Plug-In in Grafana](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Wählen Sie **Datenquelle hinzufügen** aus, um die Azure Monitor-Datenquelle hinzuzufügen und zu konfigurieren.

2. Wählen Sie einen Namen für die Datenquelle, und wählen Sie in der Dropdownliste **Azure Monitor** als Typ aus.

3. Erstellen Sie einen Dienstprinzipal: Grafana verwendet zum Herstellen einer Verbindung mit Azure Monitor-APIs und zum Sammeln von Daten einen Azure Active Directory-Dienstprinzipal. Sie müssen einen Dienstprinzipal erstellen (oder einen vorhandenen Dienstprinzipal verwenden), um den Zugriff auf Ihre Azure-Ressourcen zu verwalten.
    * Informationen zum Erstellen eines Dienstprinzipals finden Sie in [dieser Anleitung](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Kopieren und speichern Sie Ihre Mandanten-ID (Verzeichnis-ID), Client-ID (Anwendungs-ID) und den geheimen Clientschlüssel (Wert des Anwendungsschlüssels).
    * Unter [Zuweisen der Anwendung zu einer Rolle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) finden Sie Informationen zum Zuweisen der Rolle „Leser“ zur Azure Active Directory-Anwendung auf der zu überwachenden Abonnement-, Ressourcengruppen- oder Ressourcenebene. 
    Für die Log Analytics-API ist die [Rolle „Log Analytics-Leser“](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) erforderlich, welche die Berechtigungen der Rolle "Leser" enthält und ergänzt.

4. Geben Sie die Verbindungsdetails zu den APIs an, die Sie verwenden möchten. Sie können eine Verbindung zu allen oder nur zu einigen herstellen. 
    * Wenn Sie eine Verbindung mit Azure Monitor zum Sammeln sowohl von Metriken als auch von Protokollen herstellen, können Sie die gleichen Anmeldeinformationen verwenden, indem Sie **Gleiche Details wie Azure Monitor-API** auswählen.
    * Bei der Konfiguration des Plug-Ins können Sie angeben, welche Azure-Cloud (öffentlich, Azure US Government, Azure Deutschland oder Azure China) das Plug-In überwachen soll.
    * Bei Verwendung von Application Insights können Sie auch Ihre Application Insights-API und die Anwendungs-ID einbinden, um Application Insights-basierte Metriken zu sammeln. Weitere Informationen finden Sie unter [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (Abrufen Ihres API-Schlüssels und der Anwendungs-ID).

        > [!NOTE]
        > Einige Datenquellenfelder sind anders benannt als die entsprechenden Azure-Einstellungen:
        > * Die Mandanten-ID ist die Azure-Verzeichnis-ID
        > * Die Client-ID ist die Azure Active Directory-Anwendungs-ID
        > * Der geheime Clientschlüssel ist der Wert des Azure Active Directory-Anwendungsschlüssels

5. Bei Verwendung von Application Insights können Sie auch Ihre Application Insights-API und die Anwendungs-ID einbinden, um Application Insights-basierte Metriken zu sammeln. Weitere Informationen finden Sie unter [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (Abrufen Ihres API-Schlüssels und der Anwendungs-ID).

6. Wählen Sie **Speichern** aus, und Grafana testet die Anmeldeinformationen für jede API. Es wird in etwa folgende Meldung angezeigt:  
    ![Grafana-Datenquellenkonfiguration genehmigt](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Erstellen eines Grafana-Dashboards

1. Wechseln Sie zur Grafana-Startseite, und wählen Sie **Neues Dashboard** aus.

2. Wählen Sie im neuen Dashboard die Option **Graph**. Sie können auch andere Diagrammoptionen ausprobieren, aber in diesem Artikel wird *Graph* als Beispiel verwendet.

3. Im Dashboard wird ein leeres Diagramm angezeigt. Klicken Sie auf den Bereichstitel, und wählen Sie **Bearbeiten**, um die Details der Daten einzugeben, die Sie in diesem Graphdiagramm darstellen möchten.
    ![Neuer Grafana-Graph](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Wählen Sie die Azure Monitor-Datenquelle aus, die Sie konfiguriert haben.
   * Wählen Sie **Azure Monitor** im Dropdownmenü „Dienst“ aus, um Azure Monitor-Metrikdaten zu sammeln. Es wird eine Liste der Selektoren angezeigt wird, in der Sie die Ressourcen und die Metrik auswählen können, die in diesem Diagramm überwacht werden sollen. Verwenden Sie den Namespace **Microsoft.Compute/VirtualMachines**, um Metriken von einem virtuellen Computer zu sammeln. Nachdem Sie die virtuellen Computer und die Metriken ausgewählt haben, können Sie damit beginnen, die Daten im Dashboard anzuzeigen.
     ![Grafana-Diagrammkonfiguration für Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Sammeln von Azure Monitor-Protokolldaten: Wählen Sie **Azure Log Analytics** in der Dropdownliste mit Diensten aus. Wählen Sie den Arbeitsbereich aus, den Sie abfragen möchten, und legen Sie den Text der Abfrage fest. Sie können eine bereits erstellte Protokollabfrage hierhin kopieren oder eine neue Abfrage erstellen. Während Sie die Abfrage eingeben, wird IntelliSense aktiv und schlägt Optionen zur automatischen Vervollständigung vor. Wählen Sie den Visualisierungstyp **Zeitreihe,** **Tabelle** aus, und starten Sie die Abfrage.
    
     > [!NOTE]
     >
     > Die Standardabfrage, die im Lieferumfang des Plug-Ins enthalten ist, verwendet zwei Makros: $__timeFilter() und $__interval. 
     > Anhand dieser Makros kann Grafana dynamisch den Zeitbereich und das Aggregationsintervall berechnen, wenn Sie einen Teil des Diagramms vergrößern. Sie können diese Makros entfernen und einen Standardzeitfilter wie *TimeGenerated > ago(1h)* verwenden, was allerdings bedeutet, dass die Vergrößerungsfunktion im Diagramm nicht unterstützt wird.
    
     ![Grafana-Diagrammkonfiguration für Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Unten ist ein einfaches Dashboard mit zwei Diagrammen angegeben. Im linken Diagramm wird der CPU-Prozentsatz der beiden VMs angezeigt. Im rechten Diagramm werden die Transaktionen in einem Azure Storage-Konto unterteilt nach Transaktions-API-Typ angezeigt.
    ![Grafana-Beispiel mit zwei Diagrammen](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Optional: Überwachen Ihrer benutzerdefinierten Metriken auf demselben Grafana-Server

Sie können auch Telegraf und InfluxDB installieren, um sowohl benutzerdefinierte als auch Agent-basierte Metriken derselben Grafana-Instanz zu sammeln und auszugeben. Es gibt viele Datenquellen-Plug-Ins, die Sie verwenden können, um diese Metriken in einem Dashboard zusammenzufassen.

Sie können diese Einrichtung auch wiederverwenden, um Metriken von Ihrem Prometheus-Server einzubeziehen. Verwenden Sie das Prometheus-Datenquellen-Plug-In im Plug-In-Katalog von Grafana.

Hier sind einige gute Referenzartikel zur Verwendung von Telegraf, InfluxDB, Prometheus und Docker angegeben:
 - [How To Monitor System Metrics with the TICK Stack on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04) (Überwachen von Systemmetriken per TICK-Stapel unter Ubuntu 16.04)

 - [A monitoring solution for Docker hosts, containers, and containerized services](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/) (Eine Überwachungslösung für Docker-Hosts, -Container und -Dienste in Containern)

Hier ist eine Abbildung mit einem vollständigen Grafana-Dashboard angegeben, das Metriken aus Azure Monitor und Application Insights enthält.
![Grafana-Beispiel für Metriken](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Erweiterte Features von Grafana

### <a name="variables"></a>Variables
Einige Abfragewerte können über Dropdownmenüs auf der Benutzeroberfläche ausgewählt und in der Abfrage aktualisiert werden. Stellen Sie sich als Beispiel folgende Abfrage vor:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Sie können eine Variable konfigurieren, mit der alle verfügbaren **Lösungs**werte aufgelistet werden, und anschließend Ihre Abfrage so aktualisieren, dass diese Variable verwendet wird.
Klicken Sie zum Erstellen einer neuen Variable auf dem Dashboard oben rechts auf die Schaltfläche „Einstellungen“, wählen Sie **Variablen** und dann **Neu** aus.
Definieren Sie auf der Seite mit den Variablen die Datenquelle und die Abfrage, die zum Abrufen der Werteliste ausgeführt werden soll.
![Konfigurieren von Variablen in Grafana](./media/grafana-plugin/grafana-configure-variable-dark.png)

Passen Sie nach der Erstellung die Abfrage so an, dass die ausgewählten Werte verwendet werden. Ihre Diagramme zeigen eine entsprechende Reaktion:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Verwenden von Variablen in Grafana](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Erstellen von Dashboard-Wiedergabelisten

Eines der vielen nützlichen Features von Grafana ist die Dashboard-Wiedergabeliste. Sie können mehrere Dashboards erstellen und einer Wiedergabeliste hinzufügen und dann ein Intervall für die Anzeige jedes Dashboards konfigurieren. Wählen Sie **Play** (Wiedergabe), um den Durchlauf der Dashboards zu verfolgen. Sie können sie beispielsweise auf einem großen Monitor an der Wand als „Statusübersicht“ für Ihre Gruppe anzeigen.

![Grafana-Beispiel für eine Wiedergabeliste](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Grafana-Umgebung in Azure eingerichtet haben, werden Ihnen für ausgeführte virtuelle Computer (unabhängig davon, ob Sie diese nutzen oder nicht) Gebühren berechnet. Bereinigen Sie die in diesem Artikel erstellten Ressourcen, um zu verhindern, dass zusätzliche Gebühren anfallen.

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen** und dann auf **Grafana**.
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld **Grafana** ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte
* [Überblick über Metriken in Microsoft Azure](data-platform.md)

