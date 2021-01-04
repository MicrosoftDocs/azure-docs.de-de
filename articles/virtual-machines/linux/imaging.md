---
title: Überblick über das Erstellen von Linux-Images für Azure
description: Hier erfahren Sie, wie Sie Ihre Linux-VM-Images zur Verwendung in Azure bereitstellen oder neue Images erstellen.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: e364578cdec8696688cf19e14fd0529f1ca3fbb3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842616"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Bereitstellen und Erstellen von Linux-Images in Azure

In diesem Überblick werden die grundlegenden Konzepte für die Imageerstellung und für die erfolgreiche Erstellung und Verwendung von Linux-Images in Azure behandelt. Vor dem Bereitstellen eines benutzerdefinierten Image in Azure müssen Sie sich mit den für Sie verfügbaren Typen und Optionen vertraut machen.

In diesem Artikel werden die Entscheidungspunkte und Anforderungen für Images sowie wichtige Konzepte erläutert, damit Sie diese befolgen und Ihre eigenen benutzerdefinierten Images für Ihre Spezifikation erstellen können.

## <a name="difference-between-managed-disks-and-images"></a>Unterschied zwischen verwalteten Datenträgern und Images


Azure ermöglicht Ihnen das Bereitstellen einer virtuellen Festplatte (Virtual Hard Disk, VHD) auf der Plattform, die Sie als [verwalteten Datenträger](../faq-for-disks.md#managed-disks) oder als Quelle für ein Image verwenden können. 

Verwaltete Azure-Datenträger sind einzelne VHDs. Sie können entweder anhand einer vorhandenen VHD einen verwalteten Datenträger erstellen oder einen leeren verwalteten Datenträger von Grund auf erstellen. Sie können VMs von verwalteten Datenträgern erstellen, indem Sie den Datenträger an den virtuellen Computer anfügen, aber Sie können nur eine VHD mit einem virtuellen Computer verwenden. Sie können keine Betriebssystemeigenschaften ändern. Azure versucht lediglich, den virtuellen Computer zu aktivieren und mit diesem Datenträger zu starten. 

Azure-Images können aus mehreren Betriebssystemdatenträgern und Datenträgern bestehen. Wenn Sie ein verwaltetes Image zum Erstellen eines virtuellen Computers verwenden, erstellt die Plattform eine Kopie des Image und verwendet diese, um den virtuellen Computer zu erstellen. Verwaltete Images unterstützen also die Wiederverwendung desselben Image für mehrere VMs. Azure bietet auch erweiterte Verwaltungsfunktionen für Images wie die globale Replikation und die Versionsverwaltung über [Shared Image Gallery](shared-image-galleries.md). 



## <a name="generalized-and-specialized"></a>Generalisierte und spezialisierte Images

Azure bietet zwei Haupttypen von Images, generalisierte und spezialisierte Images. Die Begriffe „generalisiert“ und „spezialisiert“ sind ursprünglich Windows-Begriffe, die zu Azure migriert wurden. Diese Typen definieren den Umgang der Plattform mit dem virtuellen Computer, wenn er eingeschaltet wird. Beide Typen haben Vorteile und Nachteile, und für sie gelten bestimmte Voraussetzungen. Bevor Sie beginnen, müssen Sie wissen, welchen Imagetyp Sie benötigen. Nachstehend finden Sie eine Zusammenfassung der Szenarien und des jeweils auszuwählenden Typs:

| Szenario      | Imagetyp  | Speicheroptionen |
| ------------- |:-------------:| :-------------:| 
| Sie möchten ein Image erstellen, das für die Verwendung durch mehrere VMs konfiguriert werden kann, und Sie möchten den Hostnamen festlegen, einen Administratorbenutzer hinzufügen und weitere Aufgaben beim ersten Start ausführen können. | Generalisiert | Shared Image Gallery oder eigenständige verwaltete Images |
| Erstellen eines Image anhand einer VM-Momentaufnahme oder einer Sicherung | Spezialisiert |Shared Image Gallery oder ein verwalteter Datenträger |
| Schnelles Erstellen eines Image, das keine Konfiguration zum Erstellen mehrerer VMs erfordert |Spezialisiert |Gemeinsamer Image-Katalog |


### <a name="generalized-images"></a>Generalisierte Images

Bei einem generalisierten Image handelt es sich um ein Image, für das die Einrichtung beim ersten Start abgeschlossen werden muss. Beispielsweise legen Sie beim ersten Start den Hostnamen, den Administratorbenutzer und andere VM-spezifische Konfigurationen fest. Dies ist nützlich, wenn das Image mehrfach wiederverwendet werden soll und wenn bei der Erstellung Parameter übergeben werden sollen. Wenn das generalisierte Image den Azure-Agent enthält, verarbeitet der Agent die Parameter und signalisiert der Plattform, dass die Erstkonfiguration abgeschlossen wurde. Dieser Vorgang wird als [Bereitstellung](./provisioning.md) bezeichnet. 

Für die Bereitstellung ist es erforderlich, dass ein Bereitsteller im Image enthalten ist. Es gibt zwei Bereitsteller:
- [Azure Linux-Agents](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

Dabei handelt es sich um [Voraussetzungen](./create-upload-generic.md) für das Erstellen eines Image.


### <a name="specialized-images"></a>Spezialisierte Images
Dies sind vollständig konfigurierte Images, die keine VM- und speziellen Parameter benötigen. Die Plattform schaltet den virtuellen Computer nur ein, und Sie müssen für Eindeutigkeit innerhalb des virtuellen Computers sorgen (z. B. einen Hostnamen festlegen), um DNS-Konflikte in demselben VNET zu vermeiden. 

Für diese Images sind keine Bereitstellungs-Agents erforderlich. Möglicherweise möchten Sie jedoch Funktionen für den Umgang mit Erweiterungen nutzen. Sie können den Linux-Agent installieren, aber die Bereitstellungsoption deaktivieren. Auch wenn Sie keinen Bereitstellungs-Agent benötigen, muss das Image die [Voraussetzungen](./create-upload-generic.md) für Azure-Images erfüllen.


## <a name="image-storage-options"></a>Imagespeicheroptionen
Für die Bereitstellung Ihres Linux-Image stehen Ihnen zwei Optionen zur Verfügung:

- Verwaltete Images für die einfache VM-Erstellung in einer Entwicklungs- und Testumgebung.
- [Shared Image Gallery](shared-image-galleries.md) für das bedarfsorientierte Erstellen und Freigeben von Images.


### <a name="managed-images"></a>Verwaltete Images

Verwaltete Images können verwendet werden, um mehrere VMs zu erstellen, für sie gelten jedoch viele Einschränkungen. Verwaltete Images können nur anhand einer generalisierten Quelle (VM oder VHD) erstellt werden. Sie können nur zum Erstellen von VMs in derselben Region verwendet werden, und sie können nicht abonnement- und mandantenübergreifend freigegeben werden.

Verwaltete Images können in Entwicklungs- und Testumgebungen verwendet werden, in denen Sie einige einfache generalisierte Images zur Verwendung in einer einzelnen Region und in einem einzigen Abonnement benötigen. 

### <a name="azure-shared-image-gallery-sig"></a>Azure Shared Image Gallery (SIG)

Die Verwendung von [Katalogen mit freigegebenen Images](shared-image-galleries.md) (Shared Image Galleries, SIGs) wird für das bedarfsorientierte Erstellen, Verwalten und Freigeben von Images empfohlen. Kataloge mit freigegebenen Images unterstützen Sie dabei, Ihre Images zu strukturieren und zu organisieren.  

- Unterstützung von generalisierten und spezialisierten Images
- Unterstützung von Images der Generation 1 und 2
- Globale Replikation von Images
- Versionsverwaltung und Gruppierung von Images zur einfacheren Verwaltung.
- Hochverfügbare Images mit zonenredundantem Speicher (Zone Redundant Storage, ZRS) in Regionen, die Verfügbarkeitszonen unterstützen. ZRS bietet bessere Ausfallsicherheit bei zonenbezogenen Fehlern.
- Freigeben über Abonnements hinweg und sogar zwischen Active Directory-Mandanten (AD) über Azure RBAC
- Skalieren Ihrer Bereitstellungen mit Imagereplikaten in jeder Region.

Im Allgemeinen erstellen Sie einen SIG, der aus folgenden Elementen besteht:
- Imagedefinitionen: Dabei handelt es sich um Container, die Gruppen von Images enthalten.
- Imageversionen: Dies sind die eigentlichen Images.



## <a name="hyper-v-generation"></a>Hyper-V Generation

Azure unterstützt Hyper-V Generation 1 (Gen1) und Generation 2 (Gen2). Gen2 ist die neueste Generation, die zusätzliche Funktionalität gegenüber Gen1 bietet, beispielsweise mehr Arbeitsspeicher, Intel Software Guard Extensions (Intel SGX) und virtualisierten persistenten Speicher (virtualized Persistent Memory, vPMEM). VMs der Generation 2, die lokal ausgeführt werden, weisen auch einige Features auf, die in Azure noch nicht unterstützt werden. Weitere Informationen finden Sie im Abschnitt „Features und Funktionen“. Weitere Informationen finden Sie in [diesem Artikel](../generation-2.md). Erstellen Sie Gen2-Images, wenn Sie diese zusätzliche Funktionalität benötigen.

Wenn Sie dennoch ein eigenes Image erstellen müssen, stellen Sie sicher, dass es die [Imagevoraussetzungen](./create-upload-generic.md) erfüllt, und laden Sie das Image in Azure hoch. Verteilungsspezifische Anforderungen:


- [CentOS-basierte Verteilungen](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES &amp; openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie eine [Shared Image Gallery-Instanz erstellen](tutorial-custom-images.md).