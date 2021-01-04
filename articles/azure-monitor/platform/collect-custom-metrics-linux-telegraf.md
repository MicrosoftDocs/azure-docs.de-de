---
title: Erfassen von benutzerdefinierten Metriken für einen virtuellen Linux-Computer mit dem InfluxData Telegraf-Agent
description: Anweisungen zur Bereitstellung des InfluxData Telegraf-Agents auf einem virtuellen Linux-Computer in Azure und zur Konfiguration des Agents zur Veröffentlichung von Metriken in Azure Monitor
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: b80f27e490dd3b1890eab7740fb4650ba4280abb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008989"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Erfassen von benutzerdefinierten Metriken für einen virtuellen Linux-Computer mit dem InfluxData Telegraf-Agent

Mit Azure Monitor können Sie benutzerdefinierte Metriken über Ihre Anwendungstelemetrie, über einen in Ihren Azure-Ressourcen ausgeführten Agent und sogar über externe Überwachungssysteme erfassen. Diese können dann direkt an Azure Monitor übermittelt werden. Dieser Artikel enthält eine Anleitung für die Bereitstellung des [InfluxData](https://www.influxdata.com/) Telegraf-Agents auf einem virtuellen Linux-Computer in Azure und die Konfiguration des Agents zur Veröffentlichung von Metriken in Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf-Agent 

[Telegraf](https://docs.influxdata.com/telegraf/) ist ein Plug-In-gesteuerter Agent zur Erfassung von Metriken aus über 150 verschiedenen Quellen. Abhängig von den Workloads, die auf Ihrem virtuellen Computer ausgeführt werden, können Sie den Agent für die Nutzung spezieller Eingabe-Plug-Ins konfigurieren, um Metriken zu erfassen. Beispiele sind NGINX, MySQL und Apache. Über Ausgabe-Plug-Ins kann der Agent dann Schreibvorgänge an von Ihnen gewählten Zielen ausführen. Der Telegraf-Agent arbeitet direkt mit der Azure Monitor-REST-API für benutzerdefinierte Metriken zusammen. Er unterstützt ein Azure Monitor-Ausgabe-Plug-In. Über dieses Plug-In kann der Agent workloadspezifische Metriken auf Ihrem virtuellen Linux-Computer erfassen und als benutzerdefinierte Metriken an Azure Monitor übermitteln. 

 ![Übersicht über den Telegraf-Agent](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> Benutzerdefinierte Metriken werden nicht in allen Regionen unterstützt. Die unterstützten Regionen sind [hier](./metrics-custom-overview.md#supported-regions) aufgeführt.

## <a name="send-custom-metrics"></a>Senden benutzerdefinierter Metriken 

Im Rahmen dieses Tutorials wird ein virtueller Linux-Computer mit dem Betriebssystem Ubuntu 16.04 LTS bereitgestellt. Der Telegraf-Agent wird für die meisten Linux-Betriebssysteme unterstützt. Sowohl Debian- als auch RPM-Pakete sind zusammen mit nicht gepackten Linux-Binärdateien über das [InfluxData-Downloadportal](https://portal.influxdata.com/downloads) erhältlich. Weitere Informationen zu Installationsanweisungen und -optionen für Telegraf finden Sie in [dieser Installationsanleitung](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/). 

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

> [!NOTE]  
> Wenn Sie klassische Alarmregeln migrieren und einen vorhandenen virtuellen Linux-Computer verwenden möchten, stellen Sie sicher, dass der virtuelle Computer über eine systemseitig zugewiesene Identität verfügt, die auf **Ein** festgelegt ist.

Erstellen Sie einen neuen virtuellen Linux-Computer: 

1. Wählen Sie im linken Navigationsbereich die Option **Ressource erstellen** aus. 
1. Suchen Sie nach **Virtueller Computer**.  
1. Wählen Sie **Ubuntu 16.04 LTS** und anschließend **Erstellen** aus. 
1. Geben Sie einen VM-Namen an, z. B. **MyTelegrafVM**.  
1. Behalten Sie den Datenträgertyp **SSD** bei. Geben Sie anschließend unter **Benutzername** einen Benutzernamen an (z. B. **azureuser**). 
1. Wählen Sie als **Authentifizierungstyp** die Option **Kennwort** aus. Geben Sie ein Kennwort ein. Dieses Kennwort wird später verwendet, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen. 
1. Wählen Sie **Neue Ressourcengruppe erstellen** aus. Geben Sie einen Namen an (z. B. **myResourceGroup**). Wählen Sie Ihren **Standort** aus. Klicken Sie anschließend auf **OK**. 

    ![Erstellen eines virtuellen Ubuntu-Computers](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Wählen Sie eine Größe für den virtuellen Computer. Sie können beispielsweise nach **Computetyp** oder **Datenträgertyp** filtern. 

    ![Größe des virtuellen Computers für den Telegraf-Agent – Übersicht](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Wählen Sie auf der Seite **Einstellungen** unter **Netzwerk** > **Netzwerksicherheitsgruppe** > **Öffentliche Eingangsports hinzufügen** die Optionen **HTTP** und **SSH (22)** aus. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK**. 

1. Wählen Sie auf der Seite „Zusammenfassung“ die Option **Erstellen** aus, um die Bereitstellung des virtuellen Computers zu starten. 

1. Der virtuelle Computer wird auf dem Dashboard des Azure-Portals angeheftet. Nach Abschluss der Bereitstellung wird automatisch die Zusammenfassung für den virtuellen Computer geöffnet. 

1. Navigieren Sie im Bereich des virtuellen Computers zur Registerkarte **Identität**. Vergewissern Sie sich, dass Ihr virtueller Computer über eine auf **Ein** festgelegte, vom System zugewiesene Identität verfügt. 
 
    ![Identität des virtuellen Telegraf-Computers (Vorschauversion)](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM 

Stellen Sie eine SSH-Verbindung mit dem virtuellen Computer her. Klicken Sie auf der Seite „Übersicht“ des virtuellen Computers auf die Schaltfläche **Verbinden**. 

![Übersichtsseite des virtuellen Telegraf-Computers](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Übernehmen Sie auf der Seite zum **Herstellen der Verbindung mit dem virtuellen Computer** die Standardoptionen, um basierend auf dem DNS-Namen eine Verbindung über Port 22 herzustellen. Unter **Mit lokalem VM-Konto anmelden** wird ein Verbindungsbefehl angezeigt. Wählen Sie die Schaltfläche aus, um den Befehl zu kopieren. Das folgende Beispiel zeigt den Befehl für die SSH-Verbindung: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Fügen Sie den Befehl für die SSH-Verbindung in eine Shell wie Azure Cloud Shell oder Bash unter Ubuntu/Windows ein, oder verwenden Sie einen SSH-Client Ihrer Wahl, um die Verbindung zu erstellen. 

## <a name="install-and-configure-telegraf"></a>Installieren und Konfigurieren von Telegraf 

Führen Sie die folgenden Befehle über Ihre SSH-Sitzung aus, um das Debian-Paket für Telegraf auf dem virtuellen Computer zu installieren: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Die Konfigurationsdatei von Telegraf definiert die Vorgänge von Telegraf. Standardmäßig wird eine Beispielkonfigurationsdatei unter dem Pfad **/etc/telegraf/telegraf/telegraf.conf** installiert. Die Beispielkonfigurationsdatei listet alle möglichen Ein- und Ausgabe-Plug-Ins auf. Wir erstellen jedoch eine benutzerdefinierte Konfigurationsdatei und sorgen dafür, dass der Agent diese Datei verwendet. Hierzu verwenden wir folgende Befehle: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Der obige Code aktiviert lediglich zwei Eingabe-Plug-Ins: **cpu** und **mem**. Sie können weitere Eingabe-Plug-Ins hinzufügen – je nachdem, welche Workload auf Ihrem Computer ausgeführt wird. Beispiele wären Docker, MySQL und NGINX. Eine vollständige Liste der Eingabe-Plug-Ins finden Sie im Abschnitt **Zusätzliche Konfiguration**. 

Damit der Agent die neue Konfiguration verwenden kann, erzwingen wir mithilfe der folgenden Befehle einen Neustart des Agents: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Nun erfasst der Agent die Metriken von jedem der angegebenen Eingabe-Plug-Ins und sendet sie an Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Darstellen der Telegraf-Metriken im Azure-Portal 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). 

1. Navigieren Sie zur neuen Registerkarte **Überwachen**. Wählen Sie dann **Metriken** aus.  

     ![Monitor – Metriken (Vorschauversion)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Wählen Sie in der Ressourcenauswahl Ihren virtuellen Computer aus.

     ![Metrikdiagramm](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Wählen Sie den Namespace **Telegraf/CPU** und die Metrik **usage_system** aus. Sie können nach den Dimensionen dieser Metrik filtern oder eine entsprechende Aufteilung vornehmen.  

     ![Auswählen von Namespace und Metrik](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Zusätzliche Konfiguration 

Die obige exemplarische Vorgehensweise enthält Informationen dazu, wie Sie den Telegraf-Agent konfigurieren, um Metriken aus einigen wenigen grundlegenden Eingabe-Plug-Ins zu erfassen. Der Telegraf-Agent unterstützt über 150 Eingabe-Plug-Ins, von denen wiederum einige zusätzliche Konfigurationsoptionen unterstützen. InfluxData hat eine [Liste der unterstützten Plug-Ins](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) und Anweisungen zu deren [Konfiguration](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/) veröffentlicht.  

Darüber hinaus haben Sie in dieser exemplarischen Vorgehensweise mithilfe des Telegraf-Agents Metriken zu dem virtuellen Computer ausgegeben, auf dem der Agent bereitgestellt wurde. Der Telegraf-Agent kann auch als Collector und zur Weiterleitung von Metriken für andere Ressourcen verwendet werden. Informationen zur Konfiguration des Agent für die Ausgabe von Metriken für andere Azure-Ressourcen finden Sie im Artikel zur [benutzerdefinierten Metrikausgabe von Azure Monitor für Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Wenn die Ressourcengruppe, der virtuelle Computer und die dazugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie hierzu die Ressourcengruppe für den virtuellen Computer und anschließend **Löschen** aus. Bestätigen Sie dann den Namen der zu löschenden Ressourcengruppe. 

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [benutzerdefinierte Metriken](metrics-custom-overview.md).
