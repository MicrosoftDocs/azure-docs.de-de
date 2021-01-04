---
title: Verwenden von PerfInsights Linux in Microsoft Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Leistungsprobleme bei virtuellen Linux-Computern mit PerfInsights behandeln.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: f785f44b88c7f0d5f0b6f43114070888bb23146d
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302480"
---
# <a name="how-to-use-perfinsights"></a>Verwenden von PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) ist ein Diagnosetool zur Selbsthilfe, das Diagnosedaten sammelt und analysiert. Außerdem erstellt das Tool einen Bericht, der beim Beheben von Leistungsproblemen mit virtuellen Linux-Computern in Azure helfen kann. PerfInsights kann auf unterstützten virtuellen Computern als eigenständiges Tool oder direkt über das Portal (unter Verwendung der [Leistungsdiagnose für virtuelle Azure-Computer](performance-diagnostics.md)) ausgeführt werden.

Wenn bei virtuellen Computern Leistungsprobleme auftreten, führen Sie dieses Tool aus, bevor Sie sich an den Support wenden.

## <a name="supported-troubleshooting-scenarios"></a>Unterstützte Problembehandlungsszenarien

PerfInsights kann verschiedene Arten von Informationen erfassen und analysieren. Allgemeine Szenarien werden in den folgenden Abschnitten beschrieben.

### <a name="quick-performance-analysis"></a>Schnelle Leistungsanalyse

In diesem Szenario werden grundlegende Informationen wie Speicher- und Hardwarekonfiguration Ihres virtuellen Computers sowie verschiedene Protokolle erfasst. Hierzu zählen:

- Informationen zum Betriebssystem

- Informationen zu PCI-Geräten

- Allgemeine Protokolle des Gastbetriebssystems

- Konfigurationsdateien

- Speicherinformationen

- Konfiguration virtueller Azure-Computer (erfasst mithilfe von [Azure Instance Metadata Service](../windows/instance-metadata-service.md))

- Liste aktiver Prozesse sowie Datenträger-, Arbeitsspeicher- und CPU-Auslastung

- Netzwerkinformationen

Dies ist eine passive Sammlung von Informationen, die das System nicht beeinträchtigen sollte.

>[!Note]
>Das Szenario der schnellen Leistungsanalyse ist automatisch in jedem der folgenden Szenarien enthalten:

### <a name="performance-analysis"></a>Leistungsanalyse

Dieses Szenario ähnelt der schnellen Leistungsanalyse, ermöglicht jedoch die Erfassung von Diagnoseinformationen für einen längeren Zeitraum.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Von PerfInsights gesammelte Informationen

Es werden Informationen zum virtuellen Linux-Computer, zum Betriebssystem, zu Blockgeräten, zu Komponenten mit hohem Ressourcenverbrauch und zur Konfiguration sowie verschiedene Protokolle gesammelt. Im Anschluss finden Sie ausführlichere Informationen:

- Betriebssystem
  - Linux-Distribution und -Version
  - Informationen zum Kernel
  - Treiberinformationen

- Hardware
  - PCI-Geräte [`*`]

- Prozesse und Arbeitsspeicher
  - Liste der Prozesse (Taskname, verwendeter Arbeitsspeicher, geöffnete Dateien)
  - Gesamter, verfügbarer und freier physischer Speicher
  - Gesamter, verfügbarer und freier Auslagerungsspeicher
  - Erfassung der CPU-Auslastung und der prozessspezifischen CPU-Auslastung im Abstand von fünf Sekunden (zur Profilerstellung)
  - Erfassung der prozessspezifischen Arbeitsspeicherauslastung im Abstand von fünf Sekunden (zur Profilerstellung)

- Netzwerk  
  - Liste der Netzwerkadapter mit Adapterstatistik
  - Netzwerkroutingtabelle
  - Geöffnete Ports und Status

- Storage
  - Liste der Blockgeräte
  - Liste der Partitionen
  - Liste der Bereitstellungspunkte
  - MDADM-Volumeinformationen
  - LVM-Volumeinformationen
  - Erfassung auf allen Datenträgern im Abstand von fünf Sekunden (zur Profilerstellung)

- Protokolle
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/sysstat or /var/log/sa [`**`]
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[Erweiterungsordner]/\*log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Ausgabe von „journalctl“ für die letzten fünf Tage

- [Metadaten der Azure-VM-Instanz](../windows/instance-metadata-service.md)

>[!Note]
>[`*`] Für Debian- und SLES-Distributionen werden noch keine PCI-Informationen erfasst.
> 
>[`**`] /var/log/sysstat or /var/log/sa enthält die SAR-Dateien (System Activity Report), die vom sysstat-Paket gesammelt werden. Wenn das sysstat-Paket nicht auf der VM installiert ist, empfiehlt das Tool PerfInsights die Installation.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Ausführen von PerfInsights Linux auf Ihrem virtuellen Computer

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Voraussetzungen zum Ausführen des Tools

#### <a name="tool-requirements"></a>Toolanforderungen

- Dieses Tool muss auf dem virtuellen Computer ausgeführt werden, auf dem das Leistungsproblem besteht.
- Auf der VM muss Python 3.x oder Python 2.7 installiert sein.

- Aktuell werden folgende Distributionen unterstützt:

    | Distribution               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux Server        | 6.10 [`*`], 7.3, 7.6, 7.5 |
    | CentOS                     | 6.5 [`*`], 7.6                                    |
    | RHEL                       | 7.6, 7.7, 7.8, 7.9                                |
    | Ubuntu                     | 14.04, 16.04, 18.04, 20.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Siehe [Bekannte Probleme](#known-issues)

### <a name="known-issues"></a>Bekannte Probleme

- Für RHEL 8 ist Python nicht standardmäßig installiert. Um PerfInsights Linux ausführen zu können, müssen Sie zunächst Python 2.7 installieren.

- Das Sammeln von Gast-Agent-Informationen ist unter CentOS 6.x möglicherweise nicht erfolgreich.

- Für Debian-basierte Distributionen werden keine PCI-Geräteinformationen erfasst.

- Für einige Distributionen werden teilweise LVM-Informationen erfasst.

### <a name="how-do-i-run-perfinsights"></a>Ausführen von PerfInsights

Sie können PerfInsights auf einem virtuellen Computer ausführen, indem Sie Azure Performance Diagnostics über das Azure-Portal installieren. Sie können PerfInsights auch als eigenständiges Tool ausführen.

>[!Note]
>PerfInsights sammelt einfach Daten und analysiert sie. Dazu werden keinerlei Änderungen am System vorgenommen.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Installieren und Ausführen von PerfInsights über das Azure-Portal

Weitere Informationen zu dieser Option finden Sie unter [Leistungsdiagnose für virtuelle Azure-Computer](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Ausführen von PerfInsights im eigenständigen Modus

Gehen Sie wie folgt vor, um das Tool PerfInsights auszuführen:

1. Laden Sie [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) in einen Ordner auf Ihrem virtuellen Computer herunter, und extrahieren Sie den Inhalt über das Terminal mithilfe der folgenden Befehle:

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navigieren Sie zum Ordner mit der Datei `perfinsights.py`, und führen Sie anschließend `perfinsights.py` aus, um die verfügbaren Befehlszeilenparameter anzuzeigen.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Screenshot: Befehlszeilenausgabe von PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Die grundlegende Syntax für die Ausführung von PerfInsights-Szenarien lautet:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Mit dem folgenden Beispiel können Sie eine schnelle Leistungsanalyse für eine Minute ausführen und die Ergebnisse im Ordner „/tmp/output“ erstellen:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Mit dem folgenden Beispiel können Sie fünf Minuten lang eine Leistungsanalyse ausführen und den resultierenden Tarball in das Speicherkonto hochladen:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Vor dem Ausführen eines Szenarios wird der Benutzer von PerfInsights aufgefordert, dem Freigeben von Diagnoseinformationen und den Lizenzbedingungen zuzustimmen. Verwenden Sie die Option **„-a“ oder „--accept-disclaimer-and-share-diagnostics“**, um diese Eingabeaufforderungen zu überspringen.
    >
    >Wenn Sie über ein aktives Microsoft-Supportticket verfügen und PerfInsights gemäß Anweisung des zuständigen Supporttechnikers ausführen, geben Sie mithilfe der Option **„-s“ oder „--support-request“** die Nummer des Supporttickets an.

Nach Abschluss der Ausführung wird in dem Ordner von PerfInsights eine neue TAR-Datei angezeigt (es sei denn, es wurde ein Ausgabeordner angegeben). Die Datei heißt **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.tar.gz**. Sie können diese Datei zur Analyse an den Support-Agent senden oder den in der Datei enthaltenen Bericht öffnen, um Ergebnisse und Empfehlungen anzuzeigen.

## <a name="review-the-diagnostics-report"></a>Überprüfen des Diagnoseberichts

Die Datei **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.tar.gz** enthält einen HTML-Bericht mit den Ergebnissen von PerfInsights. Erweitern Sie zum Anzeigen des Berichts die Datei **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.tar.gz**, und öffnen Sie dann die Datei **PerfInsights Report.html**.

### <a name="overview-tab"></a>Registerkarte „Übersicht“

Die Registerkarte **Übersicht** enthält grundlegende Ausführungsdetails und Informationen zum virtuellen Computer. Auf der Registerkarte **Ergebnisse** finden Sie eine Zusammenfassung der Empfehlungen aus den verschiedenen Abschnitten des PerfInsights-Berichts.

![Screenshot: Registerkarte „Übersicht“ des PerfInsights-Berichts.](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Screenshot: Registerkarte „Linux“ des PerfInsights-Berichts](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Bei Ergebnissen, die als „Hoch“ eingestuft werden, handelt es sich um bekannte Probleme, die zu Leistungsproblemen führen können. Ergebnisse vom Typ „Mittel“ sind suboptimale Konfigurationen, die nicht unbedingt Leistungsprobleme verursachen. Ergebnisse, die als „Niedrig“ eingestuft werden, dienen lediglich zur Information.

Sehen Sie sich die Empfehlungen und Links zu allen hohen und mittleren Ergebnissen an. Dort erhalten Sie Informationen zu ihren möglichen Auswirkungen auf die Leistung sowie zu bewährten Vorgehensweisen für leistungsoptimierte Konfigurationen.

### <a name="cpu-tab"></a>Registerkarte „CPU“

Die Registerkarte **CPU** enthält Informationen zur systemweiten CPU-Auslastung während der Ausführung von PerfInsights. Informationen zu Zeiträumen mit hoher CPU-Auslastung und zu den lange ausgeführten Komponenten mit der höchsten CPU-Auslastung sind hilfreich, um Probleme im Zusammenhang mit hoher CPU-Auslastung zu beheben.

![Screenshot: Registerkarte „CPU“ des PerfInsights-Berichts](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Registerkarte „Speicher“

Im Abschnitt **Ergebnisse** werden verschiedene Ergebnisse und Empfehlungen zum Speicher angezeigt.

Auf der Registerkarte **Blockgeräte** sowie in anderen verwandten Abschnitten wie **Partitionen**, **LVM** und **MDADM** erfahren Sie, wie Blockgeräte konfiguriert sind und miteinander in Verbindung stehen.

![Screenshot: Registerkarte „Speicher“](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Screenshot: Registerkarte „MDADM“](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Registerkarte „Linux“

Die Registerkarte **Linux** enthält Informationen zur Hardware und zum Betriebssystem Ihres virtuellen Computers. Hierzu zählen eine Liste mit aktiven Prozessen sowie Informationen zu Gast-Agent, PCI, CPU, Treibern und LIS-Treibern.

![Screenshot: Registerkarte „Linux“](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Nächste Schritte

Sie können Diagnoseprotokolle und Berichte an den Microsoft-Support zur weiteren Prüfung hochladen. Die Mitarbeiter des Microsoft-Supports fordern Sie möglicherweise auf, die von PerfInsights generierte Ausgabe zu übermitteln, um zur Problembehandlung beizutragen.

Der folgende Screenshot zeigt eine Nachricht, die der an Sie gesendeten Nachricht ähnelt:

![Screenshot der Beispielnachricht vom Microsoft-Support](media/how-to-use-perfinsights-linux/support-email.png)

Befolgen Sie die Anweisungen in der Nachricht, um auf den Arbeitsbereich für die Dateiübertragung zuzugreifen. Zur Erhöhung der Sicherheit müssen Sie das Kennwort bei der ersten Verwendung ändern.

Nach der Anmeldung wird ein Dialogfeld zum Hochladen der von PerfInsights erfassten Datei **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.tar.gz** angezeigt.
