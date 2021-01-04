---
title: Arbeiten mit der vorherigen Version von Azure Migrate
description: Hier wird das Arbeiten mit der vorherigen Version von Azure Migrate beschrieben.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 4fda6c51121838bfa1f3624759b1230d8554d573
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753942"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Arbeiten mit der vorherigen Version von Azure Migrate

Dieser Artikel enthält Informationen zum Arbeiten mit der vorherigen Version von Azure Migrate. 


Es sind zwei Versionen des Azure Migrate-Diensts verfügbar:

- **Aktuelle Version**: Verwenden Sie diese Version, um Azure Migrate-Projekte zu erstellen, lokale Computer zu ermitteln und Bewertungen und Migrationen zu orchestrieren. [Erfahren Sie mehr](whats-new.md) über die Neuerungen in dieser Version.
- **Vorherige Version**: Wenn Sie zurzeit die Vorgängerversion von Azure Migrate verwenden (es wird nur die Bewertung von lokalen VMware-VMs unterstützt), sollten Sie ab sofort die aktuelle Version verwenden. Die Projekte der Vorgängerversion werden in diesem Artikel als „klassische“ Projekte bezeichnet. Wenn Sie noch immer Azure Migrate-Projekte verwenden müssen, die in der vorherigen Version erstellt wurden, sind die folgenden Vorgänge möglich bzw. nicht möglich:
    - Migrationsprojekte können nicht mehr erstellt werden.
    - Es wird empfohlen, keine neuen Ermittlungen auszuführen.
    - Sie können weiterhin auf vorhandene Projekte zugreifen.
    - Sie können weiterhin Bewertungen ausführen.
    

## <a name="upgrade-between-versions"></a>Upgrade zwischen Versionen

Sie können keine Projekte oder Komponenten aus der vorherigen Version in die neue Version aktualisieren. Sie müssen [ein neues Azure Migrate-Projekt](create-manage-projects.md) erstellen und ihm [Bewertungs- und Migrationstools](./create-manage-projects.md) hinzufügen. Verwenden Sie die Tutorials, um zu verstehen, wie die verfügbaren Bewertungs- und Migrationstools verwendet werden. Wenn Sie einen Log Analytics-Arbeitsbereich mit einem klassischen Projekt verknüpft haben, können Sie ihn an ein Projekt der aktuellen Version anfügen, nachdem Sie das klassische Projekt gelöscht haben.

## <a name="find-projects-from-previous-version"></a>Suchen nach Projekten aus der vorherigen Version

Suchen Sie folgendermaßen nach Projekten aus der vorherigen Version:

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie dann nach **Azure Migrate**. Wählen Sie diese Option aus. 
2. Im Azure Migrate-Dashboard sind eine Benachrichtigung und ein Link für den Zugriff auf alte Azure Migrate-Projekte vorhanden.
3. Klicken Sie auf den Link, um klassische Projekte zu öffnen.

## <a name="delete-projects-from-previous-version"></a>Löschen von Projekten aus der vorherigen Version

Suchen Sie folgendermaßen nach Projekten aus der vorherigen Version, und löschen Sie sie:

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie dann nach **Azure Migrate**. Wählen Sie diese Option aus. 
2. Im Azure Migrate-Dashboard sind eine Benachrichtigung und ein Link für den Zugriff auf alte Azure Migrate-Projekte vorhanden.
3. Klicken Sie auf den Link, um klassische Projekte zu öffnen.
4. Wählen Sie das Projekt aus, das Sie löschen möchten, und löschen Sie es. 


## <a name="create-an-assessment"></a>Erstellen einer Bewertung

Nach der Ermittlung der virtuellen Computer im Portal können Sie sie gruppieren und Bewertungen erstellen.

- Nachdem die virtuellen Computer im Portal ermittelt wurden, können Sie sofort Bewertungen vom Typ „Wie lokal“ erstellen.
- Bei leistungsbasierten Bewertungen empfiehlt es sich, mindestens einen Tag zu warten, bis eine leistungsbasierte Bewertung erstellt wird, um zuverlässige Größenempfehlungen zu erhalten.

Erstellen Sie eine Bewertung wie folgt:

1. Klicken Sie auf der Seite **Übersicht** des Projekts auf **+ Bewertung erstellen**.
2. Klicken Sie auf **Alle anzeigen**, um die Eigenschaften für die Bewertung zu überprüfen.
3. Erstellen Sie die Gruppe, und geben Sie einen Gruppennamen an.
4. Wählen Sie die Computer aus, die der Gruppe hinzugefügt werden sollen.
5. Klicken Sie auf **Bewertung erstellen**, um die Gruppe und die Bewertung zu erstellen.
6. Zeigen Sie die Bewertung nach der Erstellung in **Übersicht** > **Dashboard** an.
7. Klicken Sie auf **Bewertung exportieren**, um sie als Excel-Datei herunterzuladen.

Eine vorhandene Bewertung können Sie mithilfe des Befehls **Neu berechnen** für die Bewertung mit den neuesten Leistungsdaten aktualisieren.

## <a name="review-an-assessment"></a>Überprüfen einer Bewertung 

Eine Bewertung erfolgt in drei Phasen:

- Eine Bewertung beginnt mit einer Eignungsanalyse, um herauszufinden, ob Computer in Azure kompatibel sind.
- Größenschätzungen.
- Schätzung der monatlichen Kosten.

Ein Computer gelangt nur in die nächste Phase, wenn er die vorherige besteht. Beispiel: Besteht ein Computer die Eignungsprüfung nicht, wird er als ungeeignet für Azure markiert, und die Größen- und Kostenschätzungen werden nicht durchgeführt.


### <a name="review-azure-readiness"></a>Überprüfen der Azure-Bereitschaft

Die Azure-Bereitschaftsansicht in der Bewertung gibt Aufschluss über den Bereitschaftsstatus der einzelnen virtuellen Computer.

**Bereitschaft** | **State** | **Details**
--- | --- | ---
Bereit für Azure | Keine Kompatibilitätsprobleme. Der Computer kann unverändert zu Azure migriert werden, und er wird in Azure mit vollständigem Azure-Support gestartet. | Für virtuelle Computer, die bereit sind, empfiehlt Azure Migrate eine VM-Größe in Azure.
Bedingt bereit für Azure | Der Computer kann ggf. in Azure gestartet werden, verfügt jedoch möglicherweise nicht über vollständigen Azure-Support. Beispielsweise wird ein Computer mit einer älteren Version von Windows Server in Azure nicht unterstützt. | In Azure Migrate werden die Bereitschaftsprobleme erläutert, und es werden Korrekturmaßnahmen bereitstellt.
Nicht bereit für Azure |  Der virtuelle Computer wird nicht in Azure gestartet. Wenn ein virtueller VM-Datenträger beispielsweise größer als 4 TB ist, kann er nicht in Azure gehostet werden. | In Azure Migrate werden die Bereitschaftsprobleme erläutert, und es werden Korrekturmaßnahmen bereitstellt.
Bereitschaft unbekannt | Azure Migrate kann die Azure-Bereitschaft nicht feststellen, normalerweise weil keine Daten verfügbar sind.


#### <a name="azure-vm-properties"></a>Azure-VM-Eigenschaften
Die Bereitschaft berücksichtigt eine Reihe von VM-Eigenschaften, um festzustellen, ob die VM in Azure ausgeführt werden kann.


**Eigenschaft** | **Details** | **Bereitschaft**
--- | --- | ---
**Starttyp** | BIOS wird unterstützt. VM wird nicht unterstützt. | Bedingt bereit, wenn der Starttyp UEFI ist.
**Kerne** | Computerkern <= maximale Anzahl von Kernen (128), die für eine Azure-VM unterstützt werden.<br/><br/> Wenn der Leistungsverlauf verfügbar ist, berücksichtigt Azure Migrate die genutzten Kerne.<br/>Wenn in den Bewertungseinstellungen ein Komfortfaktor festgelegt ist, wird die Anzahl der genutzten Kerne mit dem Komfortfaktor multipliziert.<br/><br/> Wenn kein Leistungsverlauf vorhanden ist, verwendet Azure Migrate die zugeordneten Kerne ohne Anwendung des Komfortfaktors. | Bereit, wenn kleiner als die Grenzwerte oder gleich diesen.
**Memory** | Größe des Computerarbeitsspeichers <= maximal zulässiger Arbeitsspeicher (3.892GB bei Azure M-Serie Standard_M128m&nbsp;<sup>2</sup>) für einen virtuellen Azure-Computer. [Weitere Informationen](../virtual-machines/sizes.md)<br/><br/> Wenn der Leistungsverlauf verfügbar ist, berücksichtigt Azure Migrate dem genutzten Arbeitsspeicher.<br/><br/>Wenn ein Komfortfaktor festgelegt ist, wird der genutzte Arbeitsspeicher mit dem Komfortfaktor multipliziert.<br/><br/> Wenn kein Verlauf vorhanden ist, wird der zugeordnete Arbeitsspeicher ohne Anwendung des Komfortfaktors verwendet.<br/><br/> | Bereit, wenn innerhalb der Grenzen.
**Speicherdatenträger** | Die zugeteilte Größe eines Datenträgers darf höchstens 4 TB (4096 GB) betragen.<br/><br/> An den Computer dürfen einschließlich des Betriebssystem-Datenträgers höchstens 65 Datenträger angefügt sein. | Bereit, wenn innerhalb der Grenzen.
**Netzwerk** | An einen Computer dürfen höchstens 32 NICs angefügt sein. | Bereit, wenn innerhalb der Grenzen.

#### <a name="guest-operating-system"></a>Gastbetriebssystem

Neben den Eigenschaften des virtuellen Computers überprüft Azure Migrate auch das Gastbetriebssystem des lokalen virtuellen Computers, um zu ermitteln, ob der virtuelle Computer in Azure ausgeführt werden kann.

- Azure Migrate überprüft das in vCenter Server angegebene Betriebssystem.
- Da die von Azure Migrate durchgeführte Ermittlung anwendungsbasiert ist, kann nicht überprüft werden, ob das auf dem virtuellen Computer ausgeführte Betriebssystem identisch mit dem in vCenter Server angegebenen Betriebssystem ist.

Die folgende Logik wird verwendet.

**Betriebssystem** | **Details** | **Bereitschaft**
--- | --- | ---
Windows Server 2016 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure
Windows Server 2012 R2 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure
Windows Server 2012 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure
Windows Server 2008 R2 und alle SPs | Azure bietet vollständige Unterstützung.| Bereit für Azure
Windows Server 2008 (32-Bit und 64-Bit) | Azure bietet vollständige Unterstützung. | Bereit für Azure
Windows Server 2003, 2003 R2 | Nicht mehr unterstützt und benötigen eine [benutzerdefinierte Supportvereinbarung (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) für die Unterstützung in Azure. | Bedingt bereit für Azure, erwägen Sie ein Upgrade des Betriebssystems vor der Migration zu Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Nicht mehr unterstützt. Der Computer kann ggf. in Azure gestartet werden, von Azure wird jedoch keine Unterstützung des Betriebssystems bereitgestellt. | Bedingt bereit für Azure, es empfiehlt sich ein Upgrade des Betriebssystems vor der Migration zu Azure.
Windows Client 7, 8 und 10 | Azure bietet [nur mit Visual Studio-Abonnement](../virtual-machines/windows/client-images.md) Unterstützung. | Bedingt bereit für Azure
Windows 10 Pro Desktop | Azure bietet Unterstützung mit [mehrinstanzenfähigen Hostingrechten](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md). | Bedingt bereit für Azure
Windows Vista, XP Professional | Nicht mehr unterstützt. Der Computer kann ggf. in Azure gestartet werden, von Azure wird jedoch keine Unterstützung des Betriebssystems bereitgestellt. | Bedingt bereit für Azure, es empfiehlt sich ein Upgrade des Betriebssystems vor der Migration zu Azure.
Linux | Azure empfiehlt diese [Linux-Betriebssysteme](../virtual-machines/linux/endorsed-distros.md). Andere Linux-Betriebssysteme können in Azure gestartet werden. Es empfiehlt sich jedoch ein Upgrade des jeweiligen Betriebssystems auf eine unterstützte Version vor der Migration zu Azure. | Bereit für Azure, wenn die Version unterstützt wird.<br/><br/>Bedingt bereit, wenn die Version nicht unterstützt wird.
Andere Betriebssysteme<br/><br/> Beispielsweise Oracle Solaris, Apple macOS usw., FreeBSD usw. | Azure unterstützt diese Betriebssysteme nicht. Der Computer kann in Azure gestartet werden, es wird jedoch keine Unterstützung des Betriebssystems bereitgestellt. | Bedingt bereit für Azure, es empfiehlt sich die Installation eines unterstützten Betriebssystems vor der Migration zu Azure.  
In vCenter Server als **Sonstige** angegebenes Betriebssystem | In diesem Fall kann Azure Migrate das Betriebssystem nicht identifizieren. | Bereitschaft unbekannt. Stellen Sie sicher, dass das auf dem virtuellen Computer ausgeführte Betriebssystem in Azure unterstützt wird.
32-Bit-Betriebssysteme | Der Computer kann in Azure gestartet werden, Azure bietet jedoch möglicherweise keine vollständige Unterstützung. | Bedingt bereit für Azure, ziehen Sie vor der Migration zu Azure das Aktualisieren des Computerbetriebssystems von 32 Bit auf 64 Bit in Betracht.


### <a name="review-sizing"></a>Überprüfen der Dimensionierung

 Die Größenempfehlung von Azure Migrate basiert auf dem in den Bewertungseigenschaften angegebenen Größenkriterium.

- Bei Verwendung der leistungsbasierten Dimensionierung wird bei der Größenempfehlung der Leistungsverlauf der virtuellen Computer (CPU und Arbeitsspeicher) sowie der Datenträger (IOPS und Durchsatz) berücksichtigt.
- Bei Verwendung des Größenkriteriums „Wie lokal“ ist für die Empfehlung in Azure die Größe des lokalen virtuellen Computers ausschlaggebend. Die Datenträgergröße basiert auf dem Speichertyp, der in den Bewertungseigenschaften angegeben wird (Standardwert sind Premium-Datenträger). In Azure Migrate berücksichtigt die Leistungsdaten für den virtuellen Computer und die Datenträger nicht.

### <a name="review-cost-estimates"></a>Überprüfen der Kostenschätzungen

Kostenschätzungen geben die gesamten Compute- und Speicherkosten für den Betrieb der virtuellen Computer in Azure zusammen mit den Details für die einzelnen Computer an.

- Kostenschätzungen werden anhand der Größenempfehlungen für einen virtuellen Computer und der zugehörigen Datenträger und anhand der Bewertungseigenschaften berechnet.
- Die geschätzten monatlichen Kosten für Compute und Speicher werden für alle virtuellen Computer in der Gruppe aggregiert.
- Die Kostenschätzung bezieht sich auf die Ausführung des lokalen virtuellen Computers als virtueller Azure IaaS-Computer (Infrastructure-as-a-Service). Darin werden von Azure Migrate keine PaaS- (Platform-as-a-Service) oder SaaS-Kosten (Software-as-a-Service) berücksichtigt.

### <a name="review-confidence-rating-performance-based-assessment"></a>Überprüfen der Zuverlässigkeitsstufe (leistungsbasierte Bewertung)

Jede leistungsbasierte Bewertung ist einer Zuverlässigkeitsstufe zugeordnet.

- Eine Zuverlässigkeitsstufe reicht von einem Stern bis zu fünf Sternen (ein Stern ist die niedrigste Bewertung, fünf Sterne die höchste).
- Die Zuverlässigkeitsstufe wird einer Bewertung auf der Grundlage der Verfügbarkeit von Datenpunkten zugeordnet, die zum Berechnen der Bewertung erforderlich sind.
- Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von Azure Migrate bereitgestellten Größenempfehlungen besser einschätzen.
- Die Zuverlässigkeitsstufe ist nicht für lokale „Ist-Zustand“-Bewertungen verfügbar.

Azure Migrate benötigt für die leistungsbasierte Dimensionierung die folgenden Informationen:
- Nutzungsdaten für die CPU.
- VM-Arbeitsspeicherdaten.
- Für jeden an den virtuellen Computer angefügten Datenträger werden Informationen zu IOPS und Durchsatzdaten benötigt.
- Azure Migrate benötigt für jeden Netzwerkadapter, der an einen virtuellen Computer angefügt ist, Informationen zur Netzwerkeingabe/-ausgabe.
- Wenn eine der oben genannten Angaben nicht verfügbar ist, sind die Größenempfehlungen (und somit die Zuverlässigkeitsstufe) möglicherweise nicht zuverlässig.


Abhängig vom Prozentsatz der verfügbaren Datenpunkte werden die möglichen Zuverlässigkeitsstufen in der Tabelle zusammengefasst.

**Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
--- | ---
0 % bis 20 % | Ein Stern
21 % bis 40 % | Zwei Sterne
41 % bis 60 % | Drei Sterne
61 % bis 80 % | Vier Sterne
81 % bis 100 % | Fünf Sterne


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Bewertungsprobleme mit Auswirkungen auf die Zuverlässigkeitsstufe

Eine Bewertung kann aus verschiedenen Gründen nicht über alle Datenpunkte verfügen:

- Sie haben für die Dauer der Bewertung keine Profilerstellung für Ihre Umgebung ausgeführt. Wenn Sie z.B. die Bewertung mit einer auf einen Tag festgelegten Leistungsdauer erstellen, müssen Sie bis mindestens einen Tag nach dem Start der Ermittlung warten, bis alle Datenpunkte erfasst wurden.
- Einige virtuelle Computer wurden während des Zeitraums, für den die Bewertung berechnet wird, heruntergefahren. Wenn virtuelle Computer für eine gewisse Zeit heruntergefahren werden, können für diesen Zeitraum keine Leistungsdaten von Azure Migrate gesammelt werden.
- Einige virtuelle Maschinen wurden während des Berechnungszeitraums der Bewertung zwischenzeitlich erstellt. Wenn Sie beispielsweise eine Bewertung mit dem Leistungsverlauf des letzten Monats erstellen, aber vor einer Woche mehrere VMs in der Umgebung erstellt haben, ist der Leistungsverlauf der neuen VMs nicht für die gesamte Dauer gültig.

> [!NOTE]
> Beträgt die Zuverlässigkeitsstufe weniger als fünf Sterne, warten Sie mindestens einen Tag, damit die Appliance ein Profil der Umgebung erstellen kann, und führen Sie dann eine Neuberechnung der Bewertung durch. Wenn Sie dies nicht tun, ist die leistungsbasierte Dimensionierung möglicherweise nicht zuverlässig. Wenn Sie keine Neuberechnung durchführen möchten, empfehlen wir Ihnen, zur lokalen Dimensionierung zu wechseln, indem Sie die Bewertungseigenschaften ändern.



## <a name="create-groups-using-dependency-visualization"></a>Erstellen von Gruppen mit Abhängigkeitsvisualisierung

Zusätzlich zum manuellen Erstellen von Gruppen können Sie Gruppen mithilfe von Abhängigkeitsvisualisierung erstellen.
- Sie verwenden diese Methode normalerweise, wenn Sie Gruppen mit höheren Zuverlässigkeitsgraden bewerten möchten, indem Sie vor dem Durchführen einer Bewertung die Computerabhängigkeiten überprüfen.
- Durch eine Visualisierung der Abhängigkeiten können Sie Ihre Migration zu Azure effizient planen. So können Sie sicherstellen, dass Sie nichts übersehen und dass es bei der Migration zu Azure nicht zu unvorhergesehenen Ausfällen kommt.
- Sie können alle voneinander abhängigen Systeme ermitteln, die gemeinsam migriert werden müssen, und ermitteln, ob ein ausgeführtes System noch benötigt wird oder eher außer Betrieb gesetzt als migriert werden sollte.
- Azure Migrate verwendet die Dienstzuordnungslösung in Azure Monitor, um Abhängigkeitsvisualisierung zu ermöglichen.

> [!NOTE]
> Abhängigkeitsvisualisierung ist in Azure Government nicht verfügbar.

Zum Einrichten von Abhängigkeitsvisualisierung ordnen Sie einen Log Analytics-Arbeitsbereich einem Azure Migrate-Projekt zu, installieren Agents auf Computern, für die Sie Abhängigkeiten visualisieren möchten, und erstellen dann Gruppen mithilfe von Abhängigkeitsinformationen. 



### <a name="associate-a-log-analytics-workspace"></a>Zuordnen eines Log Analytics-Arbeitsbereichs

Wenn Sie Abhängigkeitsvisualisierung verwenden möchten, ordnen Sie einen Log Analytics-Arbeitsbereich einem Migrationsprojekt zu. Sie können einen Arbeitsbereich nur in dem Abonnement erstellen oder anfügen, in dem das Migrationsprojekt erstellt wird.

1. Um einem Projekt einen Log Analytics-Arbeitsbereich anzufügen, klicken Sie unter **Übersicht** > **Zusammenfassung** auf **Erfordert Konfiguration**.
2. Sie können einen neuen Arbeitsbereich erstellen oder einen vorhandenen Arbeitsbereich anfügen:
  - Um einen neuen Arbeitsbereich zu erstellen, geben Sie einen Namen an. Der Arbeitsbereich wird in einer Region in derselben [Azure-Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) erstellt, in der auch das Migrationsprojekt erstellt wurde.
  - Wenn Sie einen vorhandenen Arbeitsbereich anfügen, können Sie zwischen allen verfügbaren Arbeitsbereichen im selben Abonnement wie das Migrationsprojekt auswählen. Nur die Arbeitsbereiche werden aufgeführt, die in einer [unterstützten Dienstzuordnungsregion](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) erstellt wurden. Um einen Arbeitsbereich anzufügen, stellen Sie sicher, dass Sie Lesezugriff auf den Arbeitsbereich besitzen.

> [!NOTE]
> Den einem Migrationsprojekt zugeordneten Arbeitsbereich können Sie nicht ändern.

### <a name="download-and-install-vm-agents"></a>Herunterladen und Installieren von VM-Agents

Nach dem Konfigurieren eines Arbeitsbereichs müssen Sie auf allen lokalen Computern, für die Sie eine Bewertung durchführen möchten, Agents herunterladen und installieren. Falls Sie über Computer ohne Internetverbindung verfügen, ist es außerdem erforderlich, dafür das [Log Analytics-Gateway](../azure-monitor/platform/gateway.md) herunterzuladen und zu installieren.

1. Klicken Sie unter **Übersicht** auf **Verwalten** > **Computer**, und wählen Sie den gewünschten Computer aus.
2. Klicken Sie in der Spalte **Abhängigkeiten** auf **Agents installieren**.
3. Laden Sie auf der Seite **Abhängigkeiten** den Microsoft Monitoring Agent (MMA) herunter, und installieren Sie ihn sowie den Abhängigkeits-Agent auf allen VMs, die Sie bewerten möchten.
4. Kopieren Sie die Arbeitsbereichs-ID und den dazugehörigen Schlüssel. Sie benötigen diese Angaben beim Installieren des MMA auf dem lokalen Computer.

> [!NOTE]
> Um die Installation von Agents zu automatisieren, können Sie ein Bereitstellungstool wie Configuration Manager oder ein Partnertool wie [Intigua](https://www.intigua.com/intigua-for-azure-migration) verwenden, das eine Agent-Bereitstellungslösung für Azure Migrate bereitstellt.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Installieren des MMA-Agents auf einem Windows-Computer

Gehen Sie wie folgt vor, um den Agent auf einem Windows-Computer zu installieren:

1. Doppelklicken Sie auf den heruntergeladenen Agent.
2. Klicken Sie auf der Seite **Willkommen** auf **Weiter**. Klicken Sie auf der Seite **Lizenzbedingungen** auf **Ich stimme zu**, um die Lizenzbedingungen zu akzeptieren.
3. Behalten Sie unter **Zielordner** den Standardinstallationsordner bei, oder ändern Sie ihn, und klicken Sie anschließend auf **Weiter**.
4. Wählen Sie unter **Agent-Setupoptionen** die Optionen **Azure Log Analytics** > **Weiter**.
5. Klicken Sie auf **Hinzufügen**, um einen neuen Log Analytics-Arbeitsbereich hinzuzufügen. Fügen Sie die Arbeitsbereichs-ID und den dazugehörigen Schlüssel ein, die bzw. den Sie im Portal kopiert haben. Klicken Sie auf **Weiter**.

Sie können den Agent über die Befehlszeile oder mithilfe einer automatisierten Methode wie Configuration Manager installieren. [Weitere Informationen](../azure-monitor/platform/log-analytics-agent.md#installation-options) zur Verwendung dieser Methoden zum Installieren des MMA-Agent.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Installieren des MMA-Agents auf einem Linux-Computer

Gehen Sie wie folgt vor, um einen Agent auf einem Linux-Computer zu installieren:

1. Übertragen Sie das entsprechende Paket (x86 oder x64) mithilfe von scp/sftp auf Ihren Linux-Computer.
2. Installieren Sie das Bundle mit dem Argument „--install“.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Erfahren Sie mehr](../azure-monitor/platform/agents-overview.md#supported-operating-systems) über die Liste der Unterstützungen durch den MMA für Linux-Betriebssysteme.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Installieren des MMA-Agents auf einem Computer, der von Operations Manager überwacht wird

Bei Computern, die von System Center Operations Manager 2012 R2 oder höher überwacht werden, besteht keine Notwendigkeit den MMA-Agent zu installieren. Die Dienstzuordnung wird in den MMA von Operations Manager integriert, um die erforderlichen Abhängigkeitsdaten zu erfassen. [Weitere Informationen](../azure-monitor/insights/service-map-scom.md#prerequisites) Der Abhängigkeits-Agent muss installiert werden.

### <a name="install-the-dependency-agent"></a>Installieren des Abhängigkeits-Agents

1. Doppelklicken Sie zum Installieren des Abhängigkeits-Agents auf einem Windows-Computer auf die Setupdatei, und befolgen Sie die Schritte im Assistenten.
2. Installieren Sie den Abhängigkeits-Agent auf einem Linux-Computer als „root“, indem Sie den folgenden Befehl verwenden:

    ```sh InstallDependencyAgent-Linux64.bin```

- Erfahren Sie mehr zur [Unterstützung des Dependency-Agents](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) für die Betriebssysteme Windows und Linux.
- [Erfahren Sie mehr](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent) darüber, wie Sie den Dependency-Agent mithilfe von Skripts installieren können.

>[!NOTE]
> Der angegebene Artikel zu Azure Monitor für VMs enthält eine Übersicht über die Systemvoraussetzungen und die Methoden zur Bereitstellung des Dependency-Agents und gilt auch für die Dienstzuordnungslösung.

### <a name="create-a-group-with-dependency-mapping"></a>Erstellen einer Gruppe mit Abhängigkeitszuordnung

1. Navigieren Sie nach der Installation des Agents zum Portal, und klicken Sie auf **Verwalten** > **Computer**.
2. Suchen Sie nach dem Computer, auf dem Sie die Agents installiert haben.
3. Die Spalte **Abhängigkeiten** für den Computer sollte jetzt als **Abhängigkeiten anzeigen** erscheinen. Klicken Sie auf die Spalte, um die Abhängigkeiten des Computers anzuzeigen.
4. In der Abhängigkeitszuordnung für den Computer werden die folgenden Informationen angezeigt:
    - Eingehende (Clients) und ausgehende (Server) TCP-Verbindungen vom/zum Computer.
        - Die abhängigen Computer, auf denen keine MMA- und Abhängigkeits-Agents installiert sind, gruppiert nach Portnummern.
        - Die abhängigen Computer, auf denen MMA- und Abhängigkeits-Agents installiert sind, als separate Felder.
    - Innerhalb des Computers ausgeführte Prozesse können Sie einblenden, indem Sie das Feld für den jeweiligen Computer erweitern.
    - Computereigenschaften (einschließlich FQDN, Betriebssystem, MAC-Adresse). Sie können auf jedes Computerfeld klicken, um Details anzuzeigen.

4. Sie können Abhängigkeiten für verschiedene Zeiträume anzeigen, indem Sie im Zeitbereich auf die Zeitdauer klicken. Standardmäßig ist ein Bereich von einer Stunde ausgewählt. Sie können den Zeitraum ändern oder das Start- und Enddatum und die Dauer angeben.

   > [!NOTE]
   >    Ein Zeitraum von bis zu einer Stunde wird unterstützt. Verwenden Sie Azure Monitor-Protokolle zum [Abfragen von Abhängigkeitsdaten](./how-to-create-group-machine-dependencies.md) über einen längeren Zeitraum.

5. Nachdem Sie abhängige Computer identifiziert haben, die Sie gruppieren möchten, können Sie die gewünschten Computer mit Strg+Klick auswählen und auf **Computer gruppieren** klicken.
6. Geben Sie einen Gruppennamen an. Stellen Sie sicher, dass die abhängigen Computer von Azure Migrate ermittelt werden.

    > [!NOTE]
    > Wenn ein abhängiger Computer nicht von Azure Migrate ermittelt wurde, können Sie ihn der Gruppe nicht hinzufügen. Um solche Computer zur Gruppe hinzuzufügen, müssen Sie den Ermittlungsprozess mit dem richtigen Bereich erneut in vCenter Server ausführen und sicherstellen, dass der Computer von Azure Migrate ermittelt wird.  

7. Wenn Sie eine Bewertung für diese Gruppe erstellen möchten, aktivieren Sie das Kontrollkästchen, um eine neue Bewertung für die Gruppe zu erstellen.
8. Klicken Sie auf **OK**, um die Gruppe zu speichern.

Nach der Erstellung der Gruppe wird empfohlen, alle Agents auf sämtlichen Computern in der Gruppe zu installieren und die Gruppe zu verfeinern, indem die Abhängigkeiten der gesamten Gruppe visualisiert werden.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Abfragen von Abhängigkeitsdaten aus Azure Monitor-Protokollen

Von der Dienstzuordnung erfasste Abhängigkeitsdaten stehen zur Abfrage im Log Analytics-Arbeitsbereich zur Verfügung, der Ihrem Azure Migrate-Projekt zugeordnet ist. [Erfahren Sie mehr](../azure-monitor/insights/service-map.md#log-analytics-records) über die Dienstzuordnungs-Datentabellen, die in Azure Monitor-Protokollen abgefragt werden sollen. 

So führen Sie die Kusto-Abfragen aus:

1. Navigieren Sie nach der Installation des Agents zum Portal, und klicken Sie auf **Übersicht**.
2. Wechseln Sie in der 0 **Übersicht** zum Abschnitt **Essentials** des Projekts, und klicken Sie auf den Arbeitsbereichsnamen, der neben dem **OMS-Arbeitsbereich** steht.
3. Klicken Sie auf der Log Analytics-Arbeitsbereichsseite auf **Allgemein** > **Protokolle**.
4. Schreiben Sie Ihre Abfrage, um mit Azure Monitor-Protokollen Abhängigkeitsdaten zu sammeln. Beispielabfragen finden Sie im nächsten Abschnitt.
5. Führen Sie Ihre Abfrage aus, indem Sie auf „Ausführen“ klicken. 

[Erfahren Sie mehr](../azure-monitor/log-query/log-analytics-tutorial.md) über das Schreiben von Kusto-Abfragen. 

### <a name="sample-azure-monitor-logs-queries"></a>Beispielabfragen für Azure Monitor-Protokolle

Nachfolgend finden Sie Beispielabfragen, mit denen Sie Abhängigkeitsdaten extrahieren können. Sie können die Abfragen ändern, um Ihre bevorzugten Datenpunkte zu extrahieren. Eine vollständige Liste der Felder in Abhängigkeitsdatensätzen ist [hier](../azure-monitor/insights/service-map.md#log-analytics-records) verfügbar. Weitere Beispielabfragen finden Sie [hier](../azure-monitor/insights/service-map.md#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Zusammenfassen von eingehenden Verbindungen in einer Gruppe von Computern

Die Datensätze in der Tabelle für Verbindungsmetriken „VMConnection“ stellen keine einzelnen physischen Netzwerkverbindungen dar. Mehrere physische Netzwerkverbindungen werden in einer logischen Verbindung gruppiert. Weitere Informationen dazu, wie Daten von physischen Netzwerkverbindungen in einem einzelnen logischen Datensatz in VMConnection aggregiert werden, finden Sie [hier](../azure-monitor/insights/service-map.md#connections). 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Zusammenfassen der gesendeten und empfangenen Daten in eingehenden Verbindungen zwischen einer Gruppe von Computern

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen](migrate-services-overview.md) zur neuesten Version von Azure Migrate.