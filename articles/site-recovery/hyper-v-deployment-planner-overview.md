---
title: Informationen zum Azure Site Recovery-Bereitstellungsplaner für die Notfallwiederherstellung von Hyper-V-VMs in Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über den Azure Site Recovery-Bereitstellungsplaner für die Hyper-V-Notfallwiederherstellung in Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/18/2019
ms.author: mayg
ms.openlocfilehash: a793de302eb0833c959f2486fc2cda1f2eec4674
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65149124"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Informationen zum Azure Site Recovery-Bereitstellungsplaner für die Hyper-V-Notfallwiederherstellung in Azure

Dieser Artikel ist der Leitfaden zum Azure Site Recovery-Bereitstellungsplaner für Bereitstellungen von „Hyper-V zu Azure“ in der Produktion.

Bevor Sie mit dem Schützen von virtuellen Hyper-V-Computern (VMs) mit Site Recovery beginnen, sollten Sie basierend auf Ihrer täglichen Datenänderungsrate genügend Bandbreite zuordnen, um die RPO-Vorgabe (Recovery Point Objective) zu erfüllen. Außerdem ist es ratsam, für jedes Volume des Hyper-V-Speichers lokal genügend freien Speicherplatz zuzuordnen.

Außerdem ist es erforderlich, den richtigen Typ und die richtige Anzahl von Azure-Zielspeicherkonten zu erstellen. Sie erstellen entweder Standard- oder Storage Premium-Konten und berücksichtigen dabei für Ihre Quellproduktionsserver den Zuwachs, der sich im Laufe der Zeit durch eine vermehrte Nutzung ergeben kann. Sie wählen den Speichertyp pro VM basierend auf den Workloadmerkmalen, z.B. Lese/Schreib-E/A-Vorgänge pro Sekunde (IOPS) oder Datenänderung, und den Azure Site Recovery-Grenzwerten aus. 

Der Azure Site Recovery-Bereitstellungsplaner ist ein Befehlszeilentool für Notfallwiederherstellungsszenarien für „Hyper-V zu Azure“ und „VMware zu Azure“. Sie können mit diesem Tool per Remoteverbindung die Profilerstellung für mehrere Hyper-V-VMs durchführen (ohne jegliche Auswirkung auf die Produktion), um die Anforderungen an die Bandbreite und den Azure-Speicher zu ermitteln und für einen erfolgreichen Ablauf der Replikation und von Testfailovern bzw. Failovern zu sorgen. Das Tool kann ausgeführt werden, ohne dass lokal Azure Site Recovery-Komponenten installiert werden müssen. Um genaue Ergebnisse zum erzielten Durchsatz zu erhalten, empfehlen wir Ihnen aber, den Planer unter Windows Server auszuführen. Dieser Server sollte die gleiche Hardwarekonfiguration wie einer der Hyper-V-Server aufweisen, die Sie zum Aktivieren des Schutzes per Notfallwiederherstellung in Azure verwenden. 

Das Tool umfasst die folgenden Details:

**Kompatibilitätsbewertung**

* Bewertung der Eignung von virtuellen Computern basierend auf Datenträgeranzahl, Datenträgergröße, IOPS, Änderungsrate und einigen VM-Merkmalen

**Vergleich von erforderlicher Netzwerkbandbreite und RPO-Bewertung**

* Geschätzte erforderliche Netzwerkbandbreite für die Deltareplikation
* Durchsatz für Azure Site Recovery vom lokalen Standort zu Azure
* RPO, die für eine bestimmte Bandbreite erzielt werden kann
* Auswirkung auf die gewünschte RPO, wenn eine niedrigere Bandbreite bereitgestellt wird

    
**Azure-Infrastrukturanforderungen**

* Speichertypanforderung (Standard- oder Storage Premium-Konto) für jede VM
* Gesamtzahl von Standard- und Storage Premium-Konten zur Einrichtung für die Replikation
* Vorschläge für die Benennung von Speicherkonten gemäß Azure Storage-Vorgabe
* Anordnung von Speicherkonten für alle VMs
* Anzahl von einzurichtenden Azure-Kernen vor dem Testfailover oder Failover für das Abonnement
* Empfohlene Azure-VM-Größe für jede lokale VM

**Anforderungen an die lokale Infrastruktur**
* Erforderlicher freier Speicherplatz auf jedem Volume des Hyper-V-Speichers für die erfolgreiche anfängliche Replikation und Deltareplikation, um sicherzustellen, dass es durch die VM-Replikation für Ihre Produktionsanwendungen nicht zu unerwünschten Ausfallzeiten kommt
* Für Hyper-V-Replikation muss die maximale Kopierhäufigkeit festgelegt werden.

**Anleitung zur Batchverarbeitung für die erste Replikation** 
* Anzahl von VM-Batches für die Produktion
* Liste mit VMs in jedem Batch
* Reihenfolge, in der die einzelnen Batches geschützt werden
* Geschätzter Zeitaufwand für die erste Replikation der einzelnen Batches

**Geschätzte Kosten für Notfallwiederherstellung in Azure**
* Geschätzte Gesamtkosten für Notfallwiederherstellung in Azure: Compute-, Speicher-, Netzwerk- und Azure Site Recovery-Lizenzkosten
* Ausführliche Kostenanalyse pro VM



>[!IMPORTANT]
>
>Da die Nutzung m Laufe der Zeit voraussichtlich zunehmen wird, werden alle vorherigen Toolberechnungen mit einem Zuwachsfaktor von 30% in Bezug auf die Workloadmerkmale durchgeführt, und für alle Profilerstellungsmetriken (Lese/Schreib-IOPS, Datenänderung usw.) wird das 95. Perzentil verwendet. Beide Elemente (für die Berechnung des Zuwachsfaktors und des Perzentils) sind konfigurierbar. Weitere Informationen zum Zuwachsfaktor finden Sie im Abschnitt „Informationen zum Zuwachsfaktor“. Weitere Informationen zum Perzentilwert finden Sie im Abschnitt „Für die Berechnung verwendeter Perzentilwert“.
>

## <a name="support-matrix"></a>Unterstützungsmatrix

| | **VMware zu Azure** |**Hyper-V in Azure**|**Azure zu Azure**|**Hyper-V zum sekundären Standort**|**VMware zum sekundären Standort**
--|--|--|--|--|--
Unterstützte Szenarien |Ja|Ja|Nein|Ja*|Nein
Unterstützte Version | vCenter 6.7, 6.5, 6.0 oder 5.5| Windows Server 2016, Windows Server 2012 R2 | Nicht verfügbar |Windows Server 2016, Windows Server 2012 R2|Nicht verfügbar
Unterstützte Konfiguration|vCenter, ESXi| Hyper-V-Cluster, Hyper-V-Host|Nicht verfügbar|Hyper-V-Cluster, Hyper-V-Host|Nicht verfügbar|
Anzahl von Servern, für die pro ausgeführter Azure Site Recovery-Bereitstellungsplaner-Instanz die Profilerstellung durchgeführt werden kann |Einzeln (für VMs, die zu einem vCenter Server oder einem ESXi-Server gehören, kann die Profilerstellung auf einmal durchgeführt werden)|Mehrere (für VMs mehrerer Hosts oder Hostcluster kann die Profilerstellung auf einmal durchgeführt werden)| Nicht verfügbar |Mehrere (für VMs mehrerer Hosts oder Hostcluster kann die Profilerstellung auf einmal durchgeführt werden)| Nicht verfügbar

*Das Tool ist hauptsächlich für das Notfallwiederherstellungsszenario „Hyper-V zu Azure“ bestimmt. Für die Notfallwiederherstellung vom Typ „Hyper-V zum sekundären Standort“ kann es nur zum besseren Verständnis von quellseitigen Empfehlungen eingesetzt werden, z.B. erforderliche Netzwerkbandbreite, erforderlicher freier Speicherplatz auf den einzelnen Hyper-V-Quellservern und Batchverarbeitungszahlen und Batchdefinitionen der ersten Replikation.  Ignorieren Sie die Azure-Empfehlungen und Kosten im Bericht. Außerdem gilt der Vorgang „Durchsatzberechnung“ nicht für das Notfallwiederherstellungsszenario „Hyper-V zu sekundärem Standort“.

## <a name="prerequisites"></a>Voraussetzungen
Das Tool verfügt für Hyper-V über drei Hauptphasen: Abrufen der VM-Liste, Profilerstellung und Berichterstellung. Es gibt auch noch eine vierte Option, mit der nur der Durchsatz berechnet wird. Die Anforderungen für den Server, auf dem die einzelnen Phasen ausgeführt werden müssen, sind in der folgenden Tabelle angegeben:

| Serveranforderung | BESCHREIBUNG |
|---|---|
|Abrufen der VM-Liste, Profilerstellung und Messung des Durchsatzes |<ul><li>Betriebssystem: Microsoft Windows Server 2016 oder Microsoft Windows Server 2012 R2 </li><li>Computerkonfiguration: 8 vCPUs, 16 GB RAM, HDD mit 300 GB</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ Redistributable für Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Zugriff auf Azure über das Internet von diesem Server</li><li>Azure-Speicherkonto</li><li>Administratorzugriff auf dem Server</li><li>Mindestens 100 GB freier Speicherplatz (bei 1.000 VMs mit durchschnittlich drei Datenträgern, Profilerstellung über 30 Tage)</li><li>Die VM, von der aus Sie das Azure Site Recovery-Bereitstellungsplaner-Tool ausführen, muss der Liste „TrustedHosts“ aller Hyper-V-Server hinzugefügt werden.</li><li>Alle Hyper-V-Server, für die die Profilerstellung durchgeführt werden soll, müssen der Liste „TrustedHosts“ der Client-VM hinzugefügt werden, auf der Sie das Tool ausführen. [Erfahren Sie mehr zum Hinzufügen von Servern zur Liste „TrustedHosts“](#steps-to-add-servers-into-trustedhosts-list). </li><li> Das Tool sollte mit Administratorberechtigungen in PowerShell oder über die Befehlszeilenkonsole auf dem Client ausgeführt werden.</ul></ul>|
| Berichterstellung | Windows-PC oder Windows-Server mit Microsoft Excel 2013 oder höher |
| Benutzerberechtigungen | Administratorkonto zum Zugreifen auf den Hyper-V-Cluster/Hyper-V-Host beim Abrufen der VM-Liste und bei der Profilerstellung.<br>Alle Hosts, für die die Profilerstellung durchgeführt werden muss, sollten über ein Domänenadministratorkonto mit den gleichen Anmeldeinformationen (Benutzername und Kennwort) verfügen.
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Schritte zum Hinzufügen von Servern zur Liste „TrustedHosts“
1.  Für die VM, über die das Tool bereitgestellt werden soll, sollten alle Hosts für die Profilerstellung in der dazugehörigen Liste „TrustedHosts“ aufgeführt sein. Führen Sie auf der VM in einem PowerShell-Fenster mit erhöhten Rechten den folgenden Befehl aus, um den Client der Liste „TrustedHosts“ hinzuzufügen. Auf der VM kann Windows Server 2012 R2 oder Windows Server 2016 ausgeführt werden. 

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

1.  Jeder Hyper-V-Host, für den die Profilerstellung durchgeführt werden soll, sollte über Folgendes verfügen:

    a. Die VM, auf der das Tool ausgeführt werden soll, muss in der Liste „TrustedHosts“ enthalten sein. Führen Sie den folgenden Befehl in einem PowerShell-Fenster mit erhöhten Rechten auf dem Hyper-V-Host aus:

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

    b. PowerShell-Remoting muss aktiviert sein.

            Enable-PSRemoting -Force

## <a name="download-and-extract-the-deployment-planner-tool"></a>Herunterladen und Extrahieren des Bereitstellungsplaner-Tools

1.  [Laden Sie die aktuelle Version des Azure Site Recovery Deployment Planner herunter.](https://aka.ms/asr-deployment-planner)
Das Tool ist in einem ZIP-Ordner enthalten. Das Tool unterstützt sowohl „VMware zu Azure“ als auch „Hyper-V zu Azure“ als Notfallwiederherstellungsszenario. Sie können dieses Tool auch für das Notfallwiederherstellungsszenario „Hyper-V zum sekundären Standort“ verwenden und die Empfehlung zur Azure-Infrastruktur im Bericht ignorieren.

1.  Kopieren Sie den ZIP-Ordner auf die Windows Server-Instanz, unter der Sie das Tool ausführen möchten. Sie können das Tool unter Windows Server 2012 R2 oder Windows Server 2016 ausführen. Der Server muss über Netzwerkzugriff verfügen, um eine Verbindung mit dem Hyper-V-Cluster oder Hyper-V-Host herstellen zu können, in bzw. auf dem die VMs für die Profilerstellung enthalten sind. Wir empfehlen Ihnen, für die VM, auf der das Tool ausgeführt werden soll, die gleiche Hardwarekonfiguration wie für den zu schützenden Hyper-V-Server zu verwenden. Mit dieser Konfiguration wird sichergestellt, das der vom Tool gemeldete erzielte Durchsatz mit dem tatsächlichen Durchsatz übereinstimmt, den Azure Site Recovery während der Replikation erreichen kann. Die Berechnung des Durchsatzes richtet sich nach der verfügbaren Netzwerkbandbreite auf dem Server und der Hardwarekonfiguration (CPU, Speicher usw.) des Servers. Der Durchsatz wird vom Server, auf dem das Tool ausgeführt wird, zu Azure berechnet. Wenn sich die Hardwarekonfiguration des Servers vom Hyper-V-Server unterscheidet, ist der erzielte Durchsatz, der vom Tool gemeldet wird, fehlerhaft.
Empfohlene Konfiguration der VM: 8 vCPUs, 16 GB RAM, HDD mit 300 GB.

1.  Extrahieren Sie den ZIP-Ordner.
Der Ordner enthält mehrere Dateien und Unterordner. Die ausführbare Datei ist die Datei „ASRDeploymentPlanner.exe“ im übergeordneten Ordner.

Beispiel: Kopieren Sie die ZIP-Datei auf das Laufwerk „E:\“, und extrahieren Sie sie. E:\ASR Deployment Planner_v2.3.zip

E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Aktualisieren auf die neueste Version des Bereitstellungsplaners

Die neuesten Updates werden im [Versionsverlauf](site-recovery-deployment-planner-history.md) des Bereitstellungsplaners zusammengefasst.

Wählen Sie eine der folgenden Vorgehensweisen, wenn Sie über eine vorherige Version des Bereitstellungsplaners verfügen:
 * Falls die aktuelle Version keine Fehlerbehebung für die Profilerstellung enthält und die Profilerstellung bereits mit der aktuellen Planner-Version ausgeführt wird, ist es ratsam, die Profilerstellung fortzusetzen.
 * Wenn die aktuelle Version eine Fehlerbehebung für die Profilerstellung enthält, empfehlen wir Ihnen, die Profilerstellung für die aktuelle Version zu beenden und mit der neuen Version neu zu starten.


  >[!NOTE]
  >
  >Übergeben Sie beim Starten der Profilerstellung mit der neuen Version den gleichen Pfad des Ausgabeverzeichnisses, damit die Profildaten vom Tool an die vorhandenen Dateien angefügt werden. Zum Erstellen des Berichts wird ein vollständiger Satz von Profilerstellungsdaten verwendet. Wenn Sie ein anderes Ausgabeverzeichnis übergeben, werden neue Dateien erstellt und alte Profilerstellungsdaten nicht zum Erstellen des Berichts verwendet.
  >
  >Jeder neue Deployment Planner ist ein kumulatives Update der ZIP-Datei. Es ist nicht erforderlich, die neuesten Dateien in den vorherigen Ordner zu kopieren. Sie können einen neuen Ordner erstellen und verwenden.

## <a name="version-history"></a>Versionsverlauf
Die aktuelle Version des Azure Site Recovery-Bereitstellungsplaners ist Version 2.4.
Auf der Seite [ASR Deployment Planner Version History](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) (Azure Site Recovery-Bereitstellungsplaner – Versionsverlauf) finden Sie Informationen zu den Fehlerbehebungen, die in den einzelnen Updates hinzugefügt werden.


## <a name="next-steps"></a>Nächste Schritte
* [Ausführen des Bereitstellungsplaners](site-recovery-hyper-v-deployment-planner-run.md).
