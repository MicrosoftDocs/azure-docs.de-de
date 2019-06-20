---
title: 'Aktivieren von Azure Monitor für VMs (Vorschauversion): Übersicht | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie Azure Monitor für VMs bereitstellen und konfigurieren und welche Systemanforderungen erfüllt werden müssen.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 76d18b6a942ed9b8c6871b0ff7cbc1c83917ada4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66130463"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Aktivieren von Azure Monitor für VMs (Vorschauversion): Übersicht

Dieser Artikel enthält eine Übersicht über die verfügbaren Optionen zum Einrichten von Azure Monitor für VMs für die Überwachung der Integrität und Leistung. Darüber hinaus wird beschrieben, wie Sie Anwendungsabhängigkeiten ermitteln, die auf virtuellen Azure-Computern und in VM-Skalierungsgruppen, auf lokalen VMs oder auf in anderen Cloudumgebungen gehosteten VMs ausgeführt werden.  

Sie aktivieren Azure Monitor für VMs mithilfe einer der folgenden Methoden:

* Aktivieren Sie einen einzelnen virtuellen Azure-Computer bzw. eine VM-Skalierungsgruppe, indem Sie direkt auf der VM oder in der VM-Skalierungsgruppe die Option **Insights (Vorschau)** wählen.
* Aktivieren Sie zwei oder mehr Azure-VMs und VM-Skalierungsgruppen, indem Sie Azure Policy verwenden. Durch diese Methode werden die erforderlichen Abhängigkeiten von bestehenden und neuen virtuellen Computern und Skalierungsgruppen installiert und richtig konfiguriert. Nicht konforme VMs und Skalierungsgruppen werden gemeldet, sodass Sie entscheiden können, ob Sie sie aktivieren und wie Sie den Zustand korrigieren möchten.
* Aktivieren Sie mindestens zwei virtuelle Azure-Computer oder VM-Skalierungsgruppen über ein bestimmtes Abonnement oder eine bestimmte Ressourcengruppe mithilfe von PowerShell.
* Aktivieren Sie die Überwachung von virtuellen Computern oder physischen Computern, die in Ihrem Unternehmensnetzwerk oder einer anderen Cloudumgebung gehostet werden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Sie die Informationen in den folgenden Abschnitten verstanden haben.

### <a name="log-analytics"></a>Log Analytics

Azure Monitor für VMs unterstützt einen Log Analytics-Arbeitsbereich in den folgenden Regionen:

- USA, Westen-Mitte
- USA (Ost)
- Kanada, Mitte<sup>1</sup>
- Vereinigtes Königreich, Süden<sup>1</sup>
- Europa, Westen
- Asien, Südosten<sup>1</sup>

<sup>1</sup> Diese Region unterstützt derzeit das Integritätsfeature von Azure Monitor für VMs nicht.

>[!NOTE]
>Die Bereitstellung von virtuellen Azure-Computern kann aus jeder Region erfolgen, und es besteht keine Einschränkung auf die Regionen, die den Log Analytics-Arbeitsbereich unterstützen.
>

Wenn Sie über keinen Arbeitsbereich verfügen, können Sie diesen mit einer der folgenden Methoden erstellen:
* [Die Azure-CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure-Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Wenn Sie im Azure-Portal die Überwachung für einen einzelnen virtuellen Azure-Computer oder eine VM-Skalierungsgruppe aktivieren, können Sie während dieses Vorgangs einen Arbeitsbereich erstellen.

Für das bedarfsorientierte Szenario mit Azure Policy, Azure PowerShell oder Azure Resource Manager-Vorlagen muss für Ihren Log Analytics-Arbeitsbereich zuerst Folgendes konfiguriert werden:

* Installieren Sie die Projektmappen „ServiceMap“ und „InfrastructureInsights“. Sie können diese Installation mit einer bereitgestellten Azure Resource Manager-Vorlage oder auf der Registerkarte **Erste Schritte** mit der Option **Arbeitsbereich konfigurieren** durchführen.
* Konfigurieren Sie den Log Analytics-Arbeitsbereich zur Erfassung von Leistungsindikatoren.

Zum Konfigurieren Ihres Arbeitsbereichs für das bedarfsorientierte Szenario können Sie eine der folgenden Methoden verwenden:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* In Azure Monitor für VMs auf der Seite zur [Richtlinienabdeckung](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) unter der Option **Arbeitsbereich konfigurieren**

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die folgende Tabelle enthält die Windows- und Linux-Betriebssysteme, die für Azure Monitor for VMs unterstützt werden. Eine vollständige Liste mit ausführlicheren Informationen zu den größeren und kleineren Release- und unterstützen Kernelversionen des Linux-Betriebssystems ist weiter unten in diesem Abschnitt angegeben.

|Betriebssystemversion |Leistung |Karten |Health |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> Das Leistungsfeature von Azure Monitor für VMs ist nur über Azure Monitor verfügbar. Es ist nicht verfügbar, wenn Sie direkt aus dem linken Bereich des virtuellen Azure-Computers darauf zugreifen.

>[!NOTE]
>Die folgenden Informationen gelten für die Unterstützung des Linux-Betriebssystems:
> - Es werden nur die Standardversion und SMP-Version des Linux-Kernels unterstützt.
> - Nicht-Standardversionen des Kernels, z. B. PAE (Physical Address Extension) und Xen, werden für keine Linux-Distribution unterstützt. Beispielsweise wird ein System mit der Versionszeichenfolge *2.6.16.21-0.8-xen* nicht unterstützt.
> - Benutzerdefinierte Kernels, einschließlich Neukompilierungen von Standardkernels, werden nicht unterstützt.
> - Der CentOSPlus-Kernel wird unterstützt.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Betriebssystemversion | Kernelversion |
|:--|:--|
| Ubuntu 18.04 | Kernel 4.15.* |
| Ubuntu 16.04.3 | Kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Betriebssystemversion | Kernelversion
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Betriebssystemversion | Kernelversion
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency-Agent

Das Zuordnungsfeature in Azure Monitor für VMs erhält seine Daten vom Microsoft Dependency-Agent. Der Dependency-Agent baut auf dem Log Analytics-Agent auf und ist auf dessen Verbindung mit Log Analytics angewiesen. Daher muss für Ihr System der Log Analytics-Agent installiert und mit dem Dependency-Agent konfiguriert sein.

Wenn Sie Azure Monitor für VMs für eine einzelne Azure-VM aktivieren oder die Methoden für die bedarfsorientierte Bereitstellung verwenden, wird die Azure-VM-Erweiterung für den Dependency-Agent verwendet, um den Agent im Rahmen der Erfahrung zu installieren.

In einer Hybridumgebung können Sie den Dependency-Agent auf zwei Arten herunterladen und installieren: manuell oder mit einer automatisierten Bereitstellungsmethode für virtuelle Computer, die außerhalb von Azure gehostet werden.

In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die vom Zuordnungsfeature in einer Hybridumgebung unterstützt werden.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
|:--|:--|:--|
| Windows-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Windows](../../azure-monitor/platform/log-analytics-agent.md) erfordern Windows-Agents den Microsoft Dependency-Agent. Eine vollständige Liste der Betriebssystemversionen finden Sie unter [Unterstützte Betriebssysteme](#supported-operating-systems). |
| Linux-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Linux](../../azure-monitor/platform/log-analytics-agent.md) erfordern Linux-Agents den Microsoft Dependency-Agent. Eine vollständige Liste der Betriebssystemversionen finden Sie unter [Unterstützte Betriebssysteme](#supported-operating-systems). |
| System Center Operations Manager-Verwaltungsgruppe | Nein | |

Der Dependency-Agent kann von den folgenden Speicherorten heruntergeladen werden:

| Datei | Betriebssystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Um die Features in Azure Monitor für VMs zu aktivieren und darauf zuzugreifen, müssen Ihnen die folgenden Zugriffsrollen zugewiesen werden:

- Sie müssen über die Rolle *Log Analytics-Mitwirkender* verfügen, damit Sie dies aktivieren können.

- Sie müssen über die Rolle *Überwachungsleser* für den virtuellen Azure-Computer verfügen, um Leistungs-, Integritäts- und Zuordnungsdaten anzeigen zu können. Der Log Analytics-Arbeitsbereich muss für Azure Monitor für VMs konfiguriert sein.

Weitere Informationen zur Zugriffssteuerung auf einen Log Analytics-Arbeitsbereich finden Sie unter [Verwalten von Arbeitsbereichen](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Gewusst wie: Aktivieren von Azure Monitor für VMs (Vorschauversion)

Sie aktivieren Azure Monitor für VMs, indem Sie eine der Methoden in der folgenden Tabelle verwenden.

| Bereitstellungszustand | Methode | BESCHREIBUNG |
|------------------|--------|-------------|
| Einzelne Azure-VM oder VM-Skalierungsgruppe | [Direkt über die VM](vminsights-enable-single-vm.md) | Sie können einen einzelnen virtuellen Azure-Computer aktivieren, indem Sie direkt auf der VM oder in der VM-Skalierungsgruppe die Option **Insights (Vorschau)** wählen. |
| Mehrere Azure-VMs oder VM-Skalierungsgruppen | [Azure Policy](vminsights-enable-at-scale-policy.md) | Sie können mehrere Azure-VMs aktivieren, indem Sie Azure Policy und die verfügbaren Richtliniendefinitionen verwenden. |
| Mehrere Azure-VMs oder VM-Skalierungsgruppen | [Azure PowerShell oder Azure Resource Manager-Vorlagen](vminsights-enable-at-scale-powershell.md) | Sie können mehrere Azure-VMs oder VM-Skalierungsgruppen übergreifend für ein angegebenes Abonnement oder eine Ressourcengruppe aktivieren, indem Sie Azure PowerShell oder Azure Resource Manager-Vorlagen verwenden. |
| Hybrid Cloud | [Aktivieren für Hybridumgebungen](vminsights-enable-hybrid-cloud.md) | Sie können die Bereitstellung auf virtuellen oder physischen Computern durchführen, die in Ihrem Rechenzentrum oder in anderen Cloudumgebungen gehostet werden. |

## <a name="performance-counters-enabled"></a>Aktivierte Leistungsindikatoren

Azure Monitor für VMs konfiguriert einen Log Analytics-Arbeitsbereich, um die verwendeten Leistungsindikatoren zu sammeln. In der folgenden Tabelle sind die Objekte und Indikatoren aufgelistet, die alle 60 Sekunden erfasst werden.

### <a name="windows-performance-counters"></a>Windows-Leistungsindikatoren

|Objektname |Name des Leistungsindikators |
|------------|-------------|
|Logischer Datenträger |% freier Speicher |
|Logischer Datenträger |Durchschn. Datenträger s/gelesen |
|Logischer Datenträger |Durchschn. Datenträger s/übertragen |
|Logischer Datenträger |Durchschn. Datenträger s/geschrieben |
|Logischer Datenträger |Datenträger Bytes/s |
|Logischer Datenträger |Byte gelesen/s |
|Logischer Datenträger |Lesevorgänge/s |
|Logischer Datenträger |Übertragungen/s |
|Logischer Datenträger |Byte geschrieben/s |
|Logischer Datenträger |Schreibvorgänge/s |
|Logischer Datenträger |Freie Megabytes |
|Arbeitsspeicher |Verfügbare MB |
|Netzwerkadapter |Empfangene Byte/Sek. |
|Netzwerkadapter |Gesendete Byte/Sek. |
|Prozessor |% Prozessorzeit |

### <a name="linux-performance-counters"></a>Leistungsindikatoren von Linux

|Objektname |Name des Leistungsindikators |
|------------|-------------|
|Logischer Datenträger |% verwendeter Speicher |
|Logischer Datenträger |Byte gelesen/s |
|Logischer Datenträger |Lesevorgänge/s |
|Logischer Datenträger |Übertragungen/s |
|Logischer Datenträger |Byte geschrieben/s |
|Logischer Datenträger |Schreibvorgänge/s |
|Logischer Datenträger |Freie Megabytes |
|Logischer Datenträger |Logischer Datenträger Bytes/s |
|Arbeitsspeicher |Verfügbarer Speicher in MB |
|Netzwerk |Summe empfangene Bytes |
|Netzwerk |Summe übertragene Bytes |
|Prozessor |% Prozessorzeit |

## <a name="diagnostic-and-usage-data"></a>Diagnose- und Nutzungsdaten

Wenn Sie den Azure Monitor-Dienst verwenden, sammelt Microsoft automatisch Nutzungs- und Leistungsdaten. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität des Diensts sicherzustellen und zu verbessern. Zu den Daten vom Zuordnungsfeature gehören Informationen zur Konfiguration Ihrer Software, z. B. Betriebssystem und Betriebssystemversion sowie IP-Adresse, DNS-Name und Name der Arbeitsstation, um exakte und effiziente Funktionen für die Problembehandlung bereitzustellen. Microsoft erfasst weder Namen noch Adressen oder andere Kontaktinformationen.

Weitere Informationen zur Sammlung und Nutzung von Daten finden Sie in den [Datenschutzbestimmungen für Onlinedienste von Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Überwachung für Ihren virtuellen Computer aktiviert ist, stehen diese Informationen für die Analyse mit Azure Monitor für VMs zur Verfügung. Informationen zum Verwenden des Integritätsfeatures finden Sie unter [Azure Monitor für VMs – Integrität anzeigen](vminsights-health.md). Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md).
