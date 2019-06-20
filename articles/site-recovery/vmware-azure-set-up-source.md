---
title: Einrichten der Quellumgebung für die VMware-Replikation in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Einrichtung Ihrer lokalen Umgebung für das Replizieren von virtuellen VMware-Computern in Azure mithilfe von Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 075f86b24e2915d9689db8097889a830bade74c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723425"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Einrichten der Quellumgebung für die Replikation von VMware in Azure

Dieser Artikel beschreibt die Einrichtung Ihrer lokalen Quellumgebung für das Replizieren von virtuellen VMware-Computern in Azure. Er enthält die Schritte zum Auswählen Ihres Replikationsszenarios, Einrichten eines lokalen Computers als Site Recovery-Konfigurationsserver und automatischen Ermitteln von lokalen VMs. 

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie Folgendes bereits durchgeführt haben:

- Planen der Bereitstellung mithilfe des [Azure Site Recovery-Bereitstellungsplaners](site-recovery-deployment-planner.md). Dadurch können Sie basierend auf der täglichen Datenänderungsrate genügend Bandbreite zuordnen, um den gewünschten RPO-Wert (Recovery Point Objective) zu erzielen.
- [Einrichten von Ressourcen](tutorial-prepare-azure.md) im [Azure-Portal](https://portal.azure.com)
- [Vorbereiten lokaler VMware-Server](vmware-azure-tutorial-prepare-on-premises.md) mit Angabe eines dedizierten Kontos für die automatische Ermittlung

## <a name="choose-your-protection-goals"></a>Auswählen der Schutzziele

1. Wählen Sie unter **Recovery Services-Tresore** den Tresornamen aus. In diesem Szenario verwenden wir **ContosoVMVault**.
2. Wählen Sie unter **Erste Schritte** die Option „Site Recovery“ aus. Klicken Sie anschließend auf **Infrastruktur vorbereiten**.
3. Wählen Sie in **Schutzziel** > **Wo befinden sich Ihre Computer?** die Option **Lokal** aus.
4. Wählen Sie in **Wohin möchten Sie Ihre Computer replizieren?** die Option **Nach Azure** aus.
5. Wählen Sie in **Sind Ihre Computer virtualisiert?** die Option **Ja, mit VMware vSphere Hypervisor** aus. Wählen Sie dann **OK**aus.

## <a name="set-up-the-configuration-server"></a>Einrichten des Konfigurationsservers

Sie können den Konfigurationsserver mithilfe einer OVA-Vorlage (Open Virtualization Application) als lokale VMware-VM einrichten. [Erfahren Sie mehr](concepts-vmware-to-azure-architecture.md) zu den Komponenten, die auf der VMware-VM installiert werden.

1. Informieren Sie sich über die [Voraussetzungen](vmware-azure-deploy-configuration-server.md#prerequisites) für die Bereitstellung des Konfigurationsservers.
2. [Überprüfen Sie die Kapazität](vmware-azure-deploy-configuration-server.md#capacity-planning) für die Bereitstellung.
3. Führen Sie den [Download](vmware-azure-deploy-configuration-server.md#download-the-template) und [Import](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) der OVA-Vorlage durch, um eine lokale VMware-VM einzurichten, auf der der Konfigurationsserver ausgeführt wird. Bei der mit der Vorlage bereitgestellten Lizenz handelt es sich um eine Evaluierungslizenz, die 180 Tage lang gültig ist. Nach Ablauf dieses Zeitraums muss der Kunde die Windows-Version mit einer käuflich erworbenen Lizenz aktivieren.
4. Aktivieren Sie die VMware-VM, und [registrieren](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) Sie sie im Recovery Services-Tresor.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Vom Antivirenprogramm ausgeschlossene Azure Site Recovery-Ordner

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Aktive Antivirensoftware auf dem Quellcomputer

Ist auf dem Quellcomputer Antivirensoftware aktiviert, muss der Installationsordner ausgeschlossen werden. Schließen Sie daher für die reibungslose Replikation den Ordner *C:\ProgramData\ASR\agent* aus.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Aktive Antivirensoftware auf dem Konfigurationsserver

Schließen Sie für eine reibungslose Replikation und zur Vermeidung von Konnektivitätsproblemen die folgenden Ordner von der Antivirensoftware aus:

- C:\Programme\Microsoft Azure Recovery Services Agent
- C:\Programme\Microsoft Azure Site Recovery Provider
- C:\Programme\Microsoft Azure Site Recovery Configuration Manager 
- C:\Programme\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Programme (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Installationsverzeichnis des ASR-Servers. Beispiel:  E:\Programme (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Aktive Antivirensoftware auf Prozessserver/Masterziel mit horizontaler Skalierung

Schließen Sie die folgenden Ordner von der Antivirensoftware aus:

1. C:\Programme\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Installationsverzeichnis des ASR-Prozessservers mit Lastenausgleich. Beispiel: C:\Programme (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Nächste Schritte
[Einrichten Ihrer Zielumgebung](./vmware-azure-set-up-target.md) 
