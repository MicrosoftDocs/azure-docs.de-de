---
title: Sammeln von Daten aus CollectD in Azure Monitor | Microsoft-Dokumentation
description: CollectD ist ein Open-Source-Linux-Deamon, der regelmäßig Daten aus Anwendungen und Informationen aus der Betriebssystemebene sammelt.  In diesem Artikel erfahren Sie, wie Sie Daten aus CollectD in Azure Monitor sammeln.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 2118f137f2c0d32f891a170c3509bceee7ba13ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764945"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Sammeln von Daten aus CollectD mithilfe von Linux-Agents in Azure Monitor
[CollectD](https://collectd.org/) ist ein Open-Source-Linux-Deamon, der regelmäßig Leistungsmetriken aus Anwendungen und Informationen aus der Betriebssystemebene sammelt. Beispiele für diese Anwendungen sind Java Virtual Machine (JVM), MySQL Server und Nginx. In diesem Artikel erfahren Sie, wie Sie in Azure Monitor Leistungsdaten aus CollectD sammeln.

Eine vollständige Liste der verfügbaren Plug-Ins finden Sie unter [Plug-In-Tabelle](https://collectd.org/wiki/index.php/Table_of_Plugins).

![CollectD (Übersicht)](media/data-sources-collectd/overview.png)

Die folgende CollectD-Konfiguration ist im Log Analytics-Agent für Linux enthalten und dient dazu, CollectD-Daten an den Log Analytics-Agent für Linux zu übermitteln.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Sollten Sie eine ältere collectD-Version als 5.5 verwenden, müssen Sie stattdessen die folgende Konfiguration verwenden.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Die CollectD-Konfiguration verwendet das standardmäßige `write_http`-Plug-In, um Leistungsmetrikdaten über Port 26000 an den Log Analytics-Agent für Linux zu senden. 

> [!NOTE]
> Falls notwendig kann dieser Port individuell konfiguriert werden.

Der Log Analytics-Agent für Linux lauscht an Port 26000 auf CollectD-Metriken und konvertiert diese dann in Azure Monitor-Schemametriken. Im Folgenden wird der Log Analytics-Agent für die Linux-Konfiguration `collectd.conf` beschrieben.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Unterstützte Versionen
- Azure Monitor unterstützt derzeit CollectD Version 4.8 und höhere Versionen.
- Für die CollectD-Metriksammlung ist der Log Analytics-Agent für Linux v1.1.0-217 oder höher erforderlich.


## <a name="configuration"></a>Konfiguration
Im Folgenden werden die grundlegenden Schritte für die Konfiguration des Sammelns von CollectD-Daten in Azure Monitor beschrieben.

1. Konfigurieren Sie CollectD mithilfe des Plug-Ins „write_http“ zum Senden von Daten an den Log Analytics-Agent für Linux.  
2. Konfigurieren Sie den Log Analytics-Agent für Linux als Listener, der am entsprechenden Port auf CollectD-Daten lauscht.
3. Starten Sie CollectD und den Log Analytics-Agent für Linux neu.

### <a name="configure-collectd-to-forward-data"></a>Konfigurieren Sie CollectD zum Weiterleiten von Daten 

1. Um Daten aus CollectD an den Log Analytics-Agent für Linux übermitteln zu können, muss `oms.conf` zum Konfigurationsverzeichnis von CollectD hinzugefügt werden. Das Ziel dieser Datei richtet sich nach dem Linux-Distributionsverzeichnis auf Ihrem Computer.

    Ihr CollectD-Konfigurationsverzeichnis befindet sich in /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Ihr CollectD-Konfigurationsverzeichnis befindet sich in /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Für CollectD-Versionen vor 5.5 müssen Sie die Tags in `oms.conf` wie oben beschrieben ändern.
    >

2. Kopieren Sie collectd.conf in das gewünschte omsagent-Konfigurationsverzeichnis des Arbeitsbereichs.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Starten Sie CollectD und den Log Analytics-Agent für Linux mit den folgenden Befehlen neu.

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Sammeln von CollectD-Metriken für die Azure Monitor-Schemakonvertierung
Um ein vertrautes Modell zwischen den bereits vom Log Analytics-Agent für Linux gesammelten Infrastrukturmetriken und den neuen von CollectD gesammelten Metriken zu erhalten, wird die folgende Schemazuordnung verwendet:

| CollectD-Metrikfeld | Azure Monitor-Feld |
|:--|:--|
| `host` | Computer |
| `plugin` | Keine |
| `plugin_instance` | Instanzname<br>Wenn **Plugin_instance**=*null,* gilt InstanceName = " *_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Wenn **type_instance**=*null,* gilt CounterName=**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | Keine |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten. 
* Verwenden Sie [benutzerdefinierte Felder](custom-fields.md) , um Daten aus Syslog-Datensätzen in einzelnen Feldern zu analysieren.
