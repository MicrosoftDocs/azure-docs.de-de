---
title: Installation von SAP HANA auf virtuellen Azure-Computern | Microsoft-Dokumentation
description: Handbuch für die Installation von SAP HANA auf Azure-VMs
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 83c149e92276265b5b5236ae56c475d7307c18e4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093909"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Installation von SAP HANA auf virtuellen Azure-Computern
## <a name="introduction"></a>Einführung
Dieses Handbuch hilft Ihnen dabei, die richtigen Ressourcen für die erfolgreiche Bereitstellung von HANA auf virtuellen Azure-Computern aufzufinden. In diesem Handbuch wird auf Dokumentationsressourcen verwiesen, die Sie vor der Installation von SAP HANA auf einem virtuellen Azure-Computer überprüfen müssen. Sie sind dann in der Lage, die richtigen Schritte auszuführen, um am Ende eine unterstützte Konfiguration von SAP HANA auf Azure-VMs zu erhalten.  

> [!NOTE]
> In dieser Anleitung werden Bereitstellungen von SAP HANA auf Azure-VMs beschrieben. Informationen zum Bereitstellen von SAP HANA in HANA (große Instanzen) finden Sie unter [Installieren und Konfigurieren von SAP HANA (große Instanzen) in Azure](./hana-installation.md).
 
## <a name="prerequisites"></a>Voraussetzungen
Darüber hinaus wird in dieser Anleitung vorausgesetzt, dass Sie mit Folgendem vertraut sind:
* SAP HANA und SAP NetWeaver und deren lokale Installation.
* Installieren und Ausführen von SAP HANA und von SAP-Anwendungsinstanzen in Azure.
* Die folgenden dokumentierten Konzepte und Prozeduren:
   * Planen der Bereitstellung von SAP in Azure, einschließlich der Planung für Azure Virtual Network und der Verwendung von Azure Storage. Siehe [SAP NetWeaver in Azure Virtual Machines – Planungs- und Implementierungshandbuch](./planning-guide.md)
   * Bereitstellungsprinzipien und Möglichkeiten der Bereitstellungen von VMs in Azure. Siehe [Bereitstellung von Azure Virtual Machines für SAP](./deployment-guide.md)
   * Konzepte zur Hochverfügbarkeit für SAP HANA gemäß [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Ausführliche Anleitung vor der Bereitstellung
In diesem Abschnitt werden die verschiedenen Schritte aufgelistet, die Sie vor der Installation von SAP HANA auf einem virtuellen Azure-Computer ausführen müssen. Die Schritte sind nummeriert und sollten in dieser Reihenfolge befolgt werden:

1. Nicht alle möglichen Bereitstellungsszenarien werden in Azure unterstützt. Sie sollten daher das Dokument [SAP-Workload in Szenarien mit Unterstützung von virtuellen Azure-Computern](./sap-planning-supported-configurations.md) für das Szenario lesen, das Sie für Ihre SAP HANA Bereitstellung geplant haben. Wenn das Szenario nicht aufgeführt ist, müssen Sie davon ausgehen, dass es nicht getestet wurde und daher nicht unterstützt wird.
2. Wenn Sie eine grobe Vorstellung von den Arbeitsspeicheranforderungen für Ihre SAP HANA-Bereitstellung haben, müssen Sie eine passende Azure-VM suchen. Nicht alle für SAP NetWeaver zertifizierten VMs (siehe [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533)) sind für SAP HANA zertifiziert. Die ausschlaggebende Quelle für SAP HANA-zertifizierte Azure-VMs ist die Website des [SAP HANA-Hardwareverzeichnisses](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Die Einheiten, die mit **S** beginnen, sind Einheiten vom Typ [HANA (große Instanzen)](./hana-overview-architecture.md) und keine Azure-VMs.
3. Verschiedene Azure-VM-Typen weisen unterschiedliche Mindestanforderungen an Betriebssystemreleases für SUSE Linux oder Red Hat Linux auf. Auf der Website des [SAP HANA-Hardwareverzeichnisses](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) können Sie auf einen Eintrag in der Liste der für SAP HANA zertifizierten Einheiten klicken, um ausführliche Daten zu dieser Einheit zu erhalten. Neben der unterstützten HANA-Workload werden die Betriebssystemreleases aufgelistet, die mit diesen Einheiten für SAP HANA unterstützt werden.
4. Bezüglich der Betriebssystemreleases müssen Sie bestimmte Mindestkernelreleases berücksichtigen. Diese Mindestreleases sind in den folgenden SAP-Supporthinweisen dokumentiert:
    - [SAP-Supporthinweis Nr. 2814271 – SAP HANA Backup fails on Azure with Checksum Error](https://launchpad.support.sap.com/#/notes/2814271) (Prüfsummenfehler bei SAP HANA-Sicherung in Azure)
    - [SAP-Supporthinweis Nr. 2753418 – Potential Performance Degradation Due to Timer Fallback](https://launchpad.support.sap.com/#/notes/2753418) (Potenzielle Leistungsbeeinträchtigung aufgrund eines Zeitgeberfallbacks)
    - [SAP-Supporthinweis Nr. 2791572 – Performance Degradation Because of Missing VDSO Support For Hyper-V in Azure](https://launchpad.support.sap.com/#/notes/2791572) (Leistungsbeeinträchtigung aufgrund von fehlender VDSO-Unterstützung für Hyper-V in Azure)
4. Entsprechend des Betriebssystemreleases, das für den Typ des ausgewählten virtuellen Computers unterstützt wird, müssen Sie überprüfen, ob die gewünschte SAP HANA-Version von dieser Version des Betriebssystems unterstützt wird. In [SAP-Supporthinweis Nr. 2235581](https://launchpad.support.sap.com/#/notes/2235581) finden Sie eine Unterstützungsmatrix für SAP HANA-Releases mit den verschiedenen Betriebssystemreleases.
5. Wenn Sie eine gültige Kombination aus Azure-VM-Typ, Betriebssystemrelease und SAP HANA-Release gefunden haben, sollten Sie die SAP-Produktverfügbarkeitsmatrix konsultieren. In der SAP-Verfügbarkeitsmatrix können Sie herausfinden, ob das SAP-Produkt, das Sie für Ihre SAP HANA-Datenbank ausführen möchten, unterstützt wird.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Ausführliche Überlegungen für VM-Bereitstellung und Gastbetriebssystem
In dieser Phase müssen Sie die Schritte zum Bereitstellen der VMs für die Installation von HANA durchlaufen und schließlich das ausgewählte Betriebssystem nach der Installation optimieren.

1. Wählen Sie das Basisimage im Azure-Katalog aus. Wenn Sie ein eigenes Betriebssystemimage für SAP HANA erstellen möchten, müssen Sie alle Pakete kennen, die für eine erfolgreiche SAP HANA-Installation erforderlich sind. Andernfalls wird empfohlen, die SUSE- und Red Hat-Images für SAP oder SAP HANA aus dem Azure-Imagekatalog zu verwenden. Diese Images enthalten die Pakete, die für eine erfolgreiche HANA-Installation erforderlich sind. Basierend auf Ihrem Supportvertrag mit dem Betriebssystemanbieter müssen Sie ein Image auswählen, bei dem Sie eine eigene Lizenz bereitstellen. Sie können auch ein Betriebssystemimage auswählen, das Support einschließt.
2. Wenn Sie ein Gastbetriebssystem-Image ausgewählt haben, für das eine eigene Lizenz erforderlich ist, müssen Sie das Betriebssystemimage in Ihrem Abonnement registrieren, damit Sie die neuesten Patches herunterladen und anwenden können. Für diesen Schritt ist Zugriff auf das öffentliche Internet erforderlich. Dies gilt nicht, wenn Sie eine private Instanz beispielsweise eines SMT-Servers in Azure einrichten.
3. Entscheiden Sie sich für eine Netzwerkkonfiguration der VM. Weitere Informationen finden Sie im Dokument [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](./hana-vm-operations.md). Beachten Sie, dass Sie in Azure virtuellen Netzwerkkarten keine Netzwerkdurchsatzkontingente zuweisen können. Die Weiterleitung von Datenverkehr über verschiedene vNICs beruht folglich ausschließlich auf Sicherheitserwägungen. Sie müssen selbst einen sinnvollen Kompromiss zwischen der Komplexität der Datenverkehrsweiterleitung über mehrere vNICs und den durch Sicherheitsaspekte erzwungenen Anforderungen finden.
3. Wenden Sie die neuesten Patches auf das Betriebssystem an, nachdem die VM bereitgestellt und registriert wurde. Die Registrierung sollte über Ihr eigenes Abonnement erfolgen, oder wenn Sie ein Image mit Betriebssystemunterstützung auswählen, sollte der virtuelle Computer bereits Zugriff auf die Patches haben. 
4. Wenden Sie die für SAP HANA erforderlichen Optimierungen an. Diese Optimierungen sind in den folgenden SAP-Supporthinweisen aufgeführt:

    - [SAP-Supporthinweis Nr. 2694118 – Hochverfügbarkeits-Add-On für Red Hat Enterprise Linux in Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP-Supporthinweis Nr. 1984787 – SUSE LINUX Enterprise Server 12: Installationshinweise](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP-Supporthinweis Nr. 2578899 – SUSE Linux Enterprise Server 15: Installationshinweis](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation und Upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP support note #2292690 - SAP HANA DB: Recommended OS Settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) (2292690 – SAP HANA DB: empfohlene Betriebssystemeinstellungen für RHEL 7) 
    -  [SAP-Supporthinweis Nr. 2772999 – Red Hat Enterprise Linux 8.x: Installation und Konfiguration](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP-Supporthinweis Nr. 2777782 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP-Supporthinweis Nr. 2455582 – Linux: Running SAP applications compiled with GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582) (Linux – Ausführen von mit GCC 6.x kompilierten SAP-Anwendungen)
    -  [SAP-Supporthinweis Nr. 2382421 – Optimieren der Netzwerkkonfiguration auf HANA- und Betriebssystemebene](https://launchpad.support.sap.com/#/notes/2382421)

1. Wählen Sie den Azure-Speichertyp für SAP HANA aus. In diesem Schritt müssen Sie sich für ein Speicherlayout für die SAP HANA-Installation entscheiden. Sie verwenden entweder angefügte Azure-Datenträger oder native Azure-NFS-Freigaben. Die unterstützten Azure-Speichertypen und die zulässigen Kombinationen verschiedener Azure-Speichertypen sind unter [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md) dokumentiert. Nehmen Sie als Erstes die dokumentierten Konfigurationen vor. Für Nicht-Produktionssysteme können Sie möglicherweise einen niedrigeren Durchsatz oder IOPS-Wert konfigurieren. Für Produktionszwecke müssen Sie möglicherweise einen etwas höheren Durchsatz und IOPS-Wert konfigurieren.
2. Stellen Sie sicher, dass Sie für Ihre Volumes, die die DBMS-Transaktionsprotokolle oder -Wiederholungsprotokolle enthalten, die [Azure-Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) konfigurieren, wenn Sie virtuelle Computer der M-Serie oder der Mv2-Serie verwenden. Beachten Sie die Einschränkungen für Schreibbeschleunigung in der Dokumentation.
2. Überprüfen Sie, ob der [beschleunigte Netzwerkbetrieb von Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) auf den bereitgestellten VMs aktiviert ist.

> [!NOTE]
> Nicht alle in den verschiedenen sap-tune-Profilen enthaltenen oder in den Anmerkungen beschriebenen Befehle können in Azure erfolgreich ausgeführt werden. Befehle, die den Energiemodus von VMs ändern würden, geben in der Regel einen Fehler zurück, da der Energiemodus der zugrunde liegenden Azure-Hosthardware nicht geändert werden kann.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Ausführliche spezifische Vorbereitung für virtuelle Azure-Computer
Eine der Besonderheiten von Azure ist die Installation einer Azure-VM-Erweiterung, die Überwachungsdaten für den SAP-Host-Agent bereitstellt. Die Details zur Installation dieser Überwachungserweiterung sind in folgenden SAP-Hinweisen dokumentiert:

-  In [SAP-Hinweis Nr. 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) wird die erweiterte Überwachung von SAP mit Linux-VMs unter Azure erläutert. 
-  [SAP-Hinweis Nr. 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) enthält Informationen zu SAPOSCOL unter Linux. 
-  [SAP-Hinweis Nr. 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) enthält wichtige Überwachungsmetriken für SAP in Microsoft Azure.
-  [Azure Virtual Machines – Bereitstellung für SAP NetWeaver](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA-Installation
Wenn Sie die virtuellen Azure-Computer bereitgestellt und die Betriebssysteme installiert und konfiguriert haben, können Sie SAP HANA gemäß der SAP-Installation installieren. Als Einstieg in die Dokumentation bietet sich die SAP-Website für [HANA-Ressourcen](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd) an.

Informationen zu SAP HANA-Konfigurationen für horizontales Skalieren mit direkt angefügten Azure Storage Premium- oder Ultra-Datenträgern finden Sie in den entsprechenden Einzelheiten im Dokument [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out).


## <a name="additional-resources-for-sap-hana-backup"></a>Weitere Ressourcen für die SAP HANA-Sicherung
Informationen zum Sichern von SAP HANA-Datenbanken auf Azure-VMs finden Sie hier:
* [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](./sap-hana-backup-guide.md)
* [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die Dokumentation:

- [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](./hana-vm-operations.md)
- [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md)
