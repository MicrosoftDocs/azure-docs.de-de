---
title: Includedatei
description: Includedatei
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: d2312fac64515756f5ed2e0feb22fdc6b7205376
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125187"
---
**Letzte Aktualisierung des Dokuments**: 14 Mai. 2019 19:00 Uhr MEZ.

Eine öffentlich gemachte [neue Art von CPU-Sicherheitsrisiken](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), die als „Seitenkanalangriffe mit spekulativer Ausführung“ bezeichnet wird, hatte Fragen von Kunden zur Folge, die sich mehr Klarheit wünschen.  

Microsoft hat Maßnahmen zur Minderung dieser Risiken für alle Clouddienste bereitgestellt. Die Infrastruktur, in der Azure ausgeführt und Kundenworkloads voneinander isoliert werden, ist geschützt. Dies bedeutet, dass ein potenzieller Angreifer, der dieselbe Infrastruktur nutzt, Ihre Anwendung über diese Sicherheitsrisiken nicht angreifen kann.

In Azure wird nach Möglichkeit die [Wartung mit Speicherbeibehaltung](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) genutzt, um die Auswirkungen für Kunden möglichst gering zu halten und auf Neustarts verzichten zu können. In Azure werden diese Methoden eingesetzt, wenn systemweite Updates am Host vorgenommen werden, um unsere Kunden zu schützen.

Weitere Informationen dazu, wie die Sicherheit in alle Bereiche von Azure integriert ist, finden Sie auf der Website mit der [Dokumentation zur Azure-Sicherheit](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Seit der ersten Veröffentlichung dieses Dokuments wurden mehrere Varianten dieser Sicherheitsrisikoklasse öffentlich gemacht. Microsoft investiert weiterhin stark in den Schutz seiner Kunden und in die Bereitstellung von Anleitungen. Diese Seite wird aktualisiert, wenn weitere Fehlerbehebungen veröffentlicht werden. 
> 
> Am 14. Mai 2019 machte [Intel](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) eine Reihe von neuen spekulativen Sicherheitsrisiken vom Typ „Seitenkanalangriffe mit spekulativer Ausführung“ öffentlich, die als „Microarchitectural Data Sampling“ (MDS, siehe [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013) im Microsoft-Leitfaden zum Thema Sicherheit) bezeichnet werden und denen mehrere CVEs zugewiesen wurden: 
> - CVE-2019-11091 – Microarchitectural Data Sampling Uncacheable Memory (MDSUM)
> - CVE-2018-12126 – Microarchitectural Store Buffer Data Sampling (MSBDS) 
> - CVE-2018-12127 – Microarchitectural Load Port Data Sampling (MLPDS)
> - CVE-2018-12130 – Microarchitectural Fill Buffer Data Sampling (MFBDS)
>
> Dieses Sicherheitsrisiko betrifft Intel® Core®-Prozessoren und Intel® Xeon®-Prozessoren.  Microsoft Azure hat Betriebssystemupdates veröffentlicht und setzt neuen Microcode, sobald er von Intel zur Verfügung gestellt wird, in unserer gesamten Flotte ein, um unsere Kunden vor diesen neuen Sicherheitsrisiken zu schützen.   Azure arbeitet eng mit Intel zusammen, um den neuen Microcode vor seiner offiziellen Freigabe auf der Plattform zu testen und zu überprüfen. 
>
> **Kunden, die auf ihrem virtuellen Computer nicht vertrauenswürdigen Code ausführen**, müssen Maßnahmen ergreifen, um sich vor diesen Sicherheitsrisiken zu schützen. Dazu müssen sie weiterlesen, um weitere Anleitungen zu allen Sicherheitsrisiken durch Seitenkanalangriffe mit spekulativer Ausführung zu erhalten (Microsoft-Sicherheitsempfehlung ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018) und [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Andere Kunden sollten diese Sicherheitsrisiken aus einer Perspektive der tiefgehende Verteidigung bewerten und die Auswirkungen der gewählten Konfiguration auf Sicherheit und Leistung berücksichtigen.



## <a name="keeping-your-operating-systems-up-to-date"></a>Betriebssysteme auf dem neuesten Stand halten

Ein Betriebssystemupdate ist zwar nicht erforderlich, um Ihre in Azure ausgeführten Anwendungen gegenüber anderen Azure-Kunden zu isolieren, aber es gilt immer als bewährte Methode, die Software auf dem neuesten Stand zu halten. Die aktuellen Sicherheitsrollups für Windows enthalten Maßnahmen zur Risikominderung für mehrere Sicherheitsrisiken vom Typ „Seitenkanalangriffe mit spekulativer Ausführung“. Auch für Linux-Distributionen wurde mehrere Updates zu diesen Sicherheitsrisiken veröffentlicht. Hier sind unsere empfohlenen Maßnahmen zur Aktualisierung Ihres Betriebssystems aufgeführt:

| Angebot | Empfohlene Maßnahme  |
|----------|---------------------|
| Azure Cloud Services  | Aktivieren Sie [automatische Updates](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal), oder stellen Sie sicher, dass das neueste Gastbetriebssystem ausgeführt wird. |
| Virtuelle Azure Linux-Computer | Installieren Sie Updates vom Anbieter Ihres Betriebssystems. Weitere Informationen finden Sie unter [Linux](#linux) in diesem Dokument. |
| Virtuelle Azure Windows-Computer  | Installieren Sie das aktuelle Sicherheitsrollup.
| Weitere Azure PaaS-Dienste | Für Kunden, die diese Dienste verwenden, sind keine Maßnahmen erforderlich. Azure hält Ihre Betriebssystemversionen automatisch auf dem neuesten Stand. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Zusätzliche Anleitungen für den Fall, dass nicht vertrauenswürdiger Code ausgeführt wird 

Für Kunden, die für nicht vertrauenswürdige Benutzer die Ausführung von beliebigem Code zulassen, kann es ratsam sein, für ihre Azure Virtual Machines oder Cloud Services zusätzliche Sicherheitsfunktionen zu implementieren. Diese Features bieten Schutz vor den prozessinternen Offenlegungsvektoren, mit denen mehrere Sicherheitsrisiken vom Typ „Spekulative Ausführung“ beschrieben werden.

Beispielszenarien, für die zusätzliche Sicherheitsfunktionen empfohlen werden:

- Sie lassen zu, dass Code, den Sie nicht als vertrauenswürdig ansehen, auf Ihrer VM ausgeführt wird.  
    - *Beispielsweise lassen Sie für einen Ihrer Kunden das Hochladen einer Binärdatei oder eines Skripts zu, die bzw. das Sie dann in Ihrer Anwendung ausführen*. 
- Sie lassen für Benutzer, die Sie nicht als vertrauenswürdig ansehen, das Anmelden an Ihrer VM mit Konten mit geringen Rechten zu.   
    - *Beispielsweise lassen Sie zu, dass sich ein Benutzer mit geringen Rechten an einer Ihrer VMs per Remotedesktop- oder SSH-Verbindung anmeldet*.  
- Sie lassen für nicht vertrauenswürdige Benutzer den Zugriff auf virtuelle Computer zu, die über die geschachtelte Virtualisierung implementiert werden.  
    - *Beispielsweise steuern Sie den Hyper-V-Host, aber ordnen die VMs nicht vertrauenswürdigen Benutzern zu*. 

Kunden, die kein Szenario mit nicht vertrauenswürdigem Code implementieren, müssen diese zusätzlichen Sicherheitsfunktionen nicht aktivieren. 

## <a name="enabling-additional-security"></a>Aktivieren von zusätzlicher Sicherheit 

Sie können auf Ihrem virtuellen Computer oder in Ihrem Clouddienst zusätzliche Sicherheitsfunktionen aktivieren, wenn Sie nicht vertrauenswürdigen Code ausführen. Stellen Sie gleichzeitig sicher, dass Ihr Betriebssystem auf dem neuesten Stand ist, um Sicherheitsfunktionen auf Ihrem virtuellen Computer oder in Ihrem Clouddienst zu aktivieren.

### <a name="windows"></a>Windows 

Ihr Zielbetriebssystem muss aktuell sein, damit Sie diese zusätzlichen Sicherheitsfunktionen aktivieren können. Für „Seitenkanalangriffe mit spekulativer Ausführung“ sind standardmäßig mehrere Maßnahmen zur Risikominderung aktiviert, aber die hier beschriebenen zusätzlichen Features müssen manuell aktiviert werden und können sich auf die Leistung auswirken. 


**Schritt 1: Deaktivieren von Hyperthreading auf dem virtuellen Computer**: Kunden, die nicht vertrauenswürdigen Code auf einem virtuellen Computer mit Hyperthreading ausführen, müssen das Hyperthreading deaktivieren oder zu einer VM-Größe ohne Hyperthreading wechseln. Um festzustellen, ob Hyperthreading auf Ihrem virtuellen Computer aktiviert ist, verwenden Sie das folgende Skript mit der Windows-Befehlszeile auf dem virtuellen Computer.

Geben Sie `wmic` ein, um die interaktive Benutzeroberfläche aufzurufen. Geben Sie dann den folgenden Befehl ein, um die Menge der physischen und logischen Prozessoren auf dem virtuellen Computer anzuzeigen.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Wenn die Anzahl der logischen Prozessoren höher als die der physischen Prozessoren (Kerne) ist, ist Hyperthreading aktiviert.  Wenn Sie einen virtuellen Computer mit Hyperthreading ausführen, [wenden Sie sich an den Azure-Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical), um das Hyperthreading zu deaktivieren.  Sobald Hyperthreading deaktiviert ist, **fordert der Support zu einem vollständigen Neustart des virtuellen Computers auf**. 


**Schritt 2:** Führen Sie parallel zu Schritt 1 die Anweisungen unter [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) aus, um mit dem [SpeculationControl](https://aka.ms/SpeculationControlPS)-PowerShell-Modul sicherzustellen, dass der Schutz aktiviert ist.

> [!NOTE]
> Wenn Sie dieses Modul bereits zu einem früheren Zeitpunkt heruntergeladen haben, müssen Sie die neueste Version installieren.
>


Die Ausgabe des PowerShell-Skripts muss die folgenden Werte aufweisen, um aktivierte Schutzmaßnahmen gegen diese Sicherheitsrisiken zu überprüfen:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Wenn die Ausgabe `MDS mitigation is enabled: False` anzeigt, [wenden Sie sich an den Azure-Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical), um Informationen zu verfügbaren Gegenmaßnahmen zu erhalten.



**Schritt 3:** Folgen Sie den Anweisungen in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution), um die Betriebssystemunterstützung für KVAS (Kernel Virtual Address Shadowing) und BTI (Branch Target Injection) zu aktivieren, damit Schutz über die `Session Manager`-Registrierungsschlüssel aktiviert wird. Es ist ein Neustart erforderlich.


**Schritt 4:** Für Bereitstellungen, in denen [geschachtelte Virtualisierung](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) verwendet wird (nur D3 und E3): Diese Anweisungen gelten für den virtuellen Computer, den Sie als Hyper-V-Host verwenden.

1.  Befolgen Sie die Anleitung unter [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution), um den Schutz über die `MinVmVersionForCpuBasedMitigations`-Registrierungsschlüssel zu aktivieren.
2.  Legen Sie den Typ des Hypervisorplaners auf `Core` fest, indem Sie [diese Anweisungen](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types) ausführen.


### <a name="linux"></a>Linux

<a name="linux"></a>Für die Aktivierung der internen zusätzlichen Sicherheitsfunktionen ist es erforderlich, dass das Zielbetriebssystem auf dem aktuellsten Stand ist. Einige Maßnahmen zur Risikominderung sind standardmäßig aktiviert. Im folgenden Abschnitt werden die Funktionen beschrieben, die standardmäßig deaktiviert sind bzw. für die eine Hardwareunterstützung (Microcode) benötigt wird. Die Aktivierung dieser Funktionen kann zu einer Beeinträchtigung der Leistung führen. Weitere Informationen finden Sie in der Dokumentation des Anbieters Ihres Betriebssystems.


**Schritt 1: Deaktivieren von Hyperthreading auf dem virtuellen Computer**: Kunden, die nicht vertrauenswürdigen Code auf einem virtuellen Computer mit Hyperthreading ausführen, müssen das Hyperthreading deaktivieren oder zu einem virtuellen Computer ohne Hyperthreading wechseln.  Führen Sie den Befehl `lscpu` auf dem virtuellen Linux-Computer aus, um festzustellen, einen virtuellen Computer mit Hyperthreading ausführen. 

Bei `Thread(s) per core = 2` ist Hyperthreading aktiviert. 

Bei `Thread(s) per core = 1` ist Hyperthreading deaktiviert. 

 
Beispielausgabe für einen virtuellen Computer mit aktiviertem Hyperthreading: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Wenn Sie einen virtuellen Computer mit Hyperthreading ausführen, [wenden Sie sich an den Azure-Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical), um das Hyperthreading zu deaktivieren.  Sobald Hyperthreading deaktiviert ist, **fordert der Support zu einem vollständigen Neustart des virtuellen Computers auf**.


**Schritt 2:** Informationen zum Einleiten von Gegenmaßnahmen für die folgenden Sicherheitsrisiken durch Seitenkanalangriffe mit spekulativer Ausführung finden Sie in der Dokumentation Ihres Betriebssystemanbieters:   
 
- [RedHat und CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel enthält Anleitungen zu den folgenden Seitenkanalangriffen mit spekulativer Ausführung, die zahlreiche moderne Prozessoren betreffen:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 – Branch Target Injection (BTI)  
- CVE-2017-5754 – Kernel Page Table Isolation (KPTI)
- CVE-2018-3639 – Speculative Store Bypass (KPTI) 
 
[L1 Terminal Fault (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 – Intel Software Guard Extensions (Intel SGX)
- CVE-2018-3620 – Betriebssysteme (OS) und System Management Mode (SMM)
- CVE-2018-3646 – betrifft Virtual Machine Manager (VMM)

[Microarchitectural Data Sampling](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 – Microarchitectural Data Sampling Uncacheable Memory (MDSUM)
- CVE-2018-12126 – Microarchitectural Store Buffer Data Sampling (MSBDS)
- CVE-2018-12127 – Microarchitectural Load Port Data Sampling (MLPDS)
- CVE-2018-12130 – Microarchitectural Fill Buffer Data Sampling (MFBDS)








