---
title: Von Azure unterstützte Distributionen von Linux | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über unterstützte Linux-Distributionen für Azure, einschließlich Leitfäden für Ubuntu, CentOS, Oracle und SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: szark
ms.openlocfilehash: a1be0b6870882d3c7b0281dec7933e87c50e49de
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834569"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Von Azure unterstützte Linux-Distributionen
Partner stellen Linux-Images im Azure Marketplace bereit. Wir arbeiten mit verschiedenen Linux-Communitys zusammen, um der Liste der unterstützten Distributionen weitere Varianten hinzuzufügen. In der Zwischenzeit gilt für Distributionen, die nicht in Marketplace verfügbar sind: Sie können stets Ihr eigenes Linux verwenden, sofern Sie die Richtlinien unter [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) einhalten.

## <a name="supported-distributions-and-versions"></a>Unterstützte Distributionen und Versionen
In der folgenden Tabelle finden Sie die auf Azure unterstützten Linux-Verteilungen und -Versionen. Ausführlichere Informationen zur Unterstützung von Linux- und Open-Source-Technologie in Microsoft Azure finden Sie unter [Unterstützung für Linux und Open-Source-Technologie unter Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure).

Die Treiber für die Linux-Integrationsdienste (Linux Integration Services, LIS) für Hyper-V und Azure sind Kernelmodule, die Microsoft direkt für den Linux-Upstream-Kernel bereitstellt.  Einige LIS-Treiber sind standardmäßig in den Kernel der Distribution integriert. Ältere Distributionen, die auf Red Hat Enterprise (RHEL)/CentOS basieren, stehen als separater Download unter [Linux Integration Services Version 4.2 for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106) zur Verfügung. Unter [Linux-Kernelanforderungen](create-upload-generic.md#linux-kernel-requirements) finden Sie weitere Informationen zu den LIS-Treibern.

Der Azure Linux-Agent ist bereits in den Images im Azure Marketplace vorinstalliert und i.d.R. im Paketrepository der Distribution verfügbar. Quellcode finden Sie unter [GitHub](https://github.com/azure/walinuxagent).


| Distribution | Version | Treiber | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+ |CentOS 6.3: [LIS-Download](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: Im Kernel |Paket: Im [Repository](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) unter „WALinuxAgent“ <br/>Quellcode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Im Kernel |Quellcode: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+ |Im Kernel |Paket: Im Repository unter „waagent“ <br/>Quellcode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Im Kernel |Paket: Im Repository unter „WALinuxAgent“ <br/>Quellcode: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 oder höher, 7.1 oder höher, 8.0 oder höher |Im Kernel |Paket: Im Repository unter „WALinuxAgent“ <br/>Quellcode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES für SAP<br>11 SP4<br>12 SP1 oder höher<br>15|Im Kernel |Paket:<p> Für 11 im [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools)-Repository<br>Für 12 im Modul „Public Cloud“ unter „python-azure-agent“ enthalten<br/>Quellcode: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |Im Kernel |Paket: Im Repository [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) unter „python-azure-agent“ <br/>Quellcode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |Im Kernel |Paket: Im Repository unter „walinuxagent“ <br/>Quellcode: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Informationen zur erweiterten Unterstützung für Ubuntu 12.04 und 14.04 finden Sie hier: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm) (Erweiterte Ubuntu-Sicherheitswartung).


## <a name="image-update-cadence"></a>Imageaktualisierungsrhythmus
Azure fordert, dass die Herausgeber der unterstützten Linux-Distributionen regelmäßig in einem vierteljährlichen oder häufigeren Rhythmus ihre Images im Azure Marketplace mit den neuesten Patches und Sicherheitsupdates aktualisieren. Aktualisierte Images im Azure Marketplace sind automatisch für Kunden als neue Versionen einer Image-SKU verfügbar. Weitere Informationen zum Suchen nach Linux-Images: [Suchen von Linux-VM-Images im Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Weitere Links
 - [Lebenszyklus von öffentlichen SUSE-Cloudimages](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Optimierte Azure-Kernel

Azure arbeitet eng mit verschiedenen unterstützten Linux-Distributionen zusammen, um die im Azure Marketplace veröffentlichten Images zu optimieren. Ein Aspekt dieser Zusammenarbeit ist die Entwicklung von für die Azure-Plattform optimierten Linux-Kerneln, die als vollständig unterstützte Komponenten der Linux-Distribution bereitgestellt werden. Die für Azure optimierten Kernel integrieren neue Features und Leistungsverbesserungen und sind im Vergleich zu den in den Distributionen verfügbaren Standard- oder generischen Kerneln häufiger (in der Regel quartalsweise) verfügbar.

In den meisten Fällen sind diese Kernel in den Standardimages im Azure Marketplace vorinstalliert, sodass Azure-Kunden sofort die Vorteile dieser optimierten Kernel nutzen können. Weitere Informationen zu diesen für Azure optimierten Kerneln finden Sie unter den folgenden Links:

 - Für Azure optimierter CentOS-Kernel – verfügbar über die CentOS-SIG zur Virtualisierung ([weitere Informationen](https://wiki.centos.org/SpecialInterestGroup/Virtualization))
 - Debian-Cloudkernel – verfügbar mit den „Backports“-Images für Debian 10 und Debian 9 in Azure ([weitere Informationen](https://wiki.debian.org/Cloud/MicrosoftAzure))
 - Für Azure optimierter SLES-Kernel ([weitere Informationen](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/))
 - Für Azure optimierter Ubuntu-Kernel ([weitere Informationen](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel))


## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Von der CoreOS-Website:

*CoreOS steht für Sicherheit, Konsistenz und Zuverlässigkeit. CoreOS installiert Paketen nicht über yum oder apt, sondern verwaltet Ihre Dienste mithilfe von Linux-Containern mit einem höheren Maß an Abstraktion. Der Code eines einzelnen Diensts und alle Abhängigkeiten sind in einem Container verpackt, der auf einem oder mehreren CoreOS-Computern ausgeführt werden kann.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ ist ein unabhängiges Beratungs- und Dienstleistungsunternehmen, das sich auf die Entwicklung und Implementierung von professionellen Lösungen mithilfe von kostenloser Software spezialisiert hat. Als führender Open Source-Experte verfügt Credativ über internationale Anerkennung bei zahlreichen IT-Abteilungen, die den Credativ-Support nutzen. Gemeinsam mit Microsoft bereitet Credativ derzeit entsprechende Debian-Images für Debian 8 (Jessie) und Debian vor Version 7 (Wheezy) vor. Beide Images wurden speziell für die Ausführung auf Azure entwickelt und können einfach über die Plattform verwaltet werden. Über die eigenen Open Source Support Center unterstützt Credativ außerdem die langfristige Wartung und Aktualisierung der Debian-Images für Azure.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle verfolgt die Strategie, ein breites Spektrum an Lösungen für öffentliche und private Clouds zu bieten. Im Rahmen dieser Strategie können Kunden flexibel wählen, wie sie Oracle-Software in Oracle-Clouds und anderen Clouds bereitstellen möchten. Aufgrund der Partnerschaft zwischen Oracle und Microsoft haben Kunden die Möglichkeit, Oracle-Software in öffentlichen und privaten Clouds von Microsoft bereitzustellen und können dabei auf die Zertifizierung und den Support von Oracle vertrauen.  Das Engagement von Oracle und die Investition in öffentliche und private Cloudlösungen von Oracle bleibt unverändert.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Als weltweit führender Anbieter von Open Source-Lösungen unterstützt Red Hat mehr als 90 % der Fortune 500-Unternehmen dabei, geschäftliche Probleme zu lösen, ihre IT- und Unternehmensstrategien abzustimmen und sich auf die Zukunft der Technologie vorzubereiten. Red Hat bietet dazu sichere Lösungen über ein offenes Geschäftsmodell und ein kostengünstiges, zuverlässiges Abonnementmodell an.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server unter Azure ist eine bewährte Plattform, die hervorragende Zuverlässigkeit und Sicherheit für Cloud Computing bietet. Die vielseitige Linux-Plattform SUSE lässt sich nahtlos in Azure-Cloud-Dienste integrieren, um eine einfach zu verwaltende Cloudumgebung bereitzustellen. Mit mehr als 9.200 zertifizierten Anwendungen von über 1.800 unabhängigen Softwareanbietern für SUSE Linux Enterprise Server stellt SUSE zudem sicher, dass vorhandene im Rechenzentrum unterstützte Arbeitsauslastungen vertrauensvoll unter Azure bereitgestellt werden können.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Engineering und Open Community Governance von Canonical steigern den Erfolg von Ubuntu bei Clients, Servern und Cloud Computing, einschließlich persönlicher Clouddienste für Endkunden. Aus der Vision von Canonical von einer einheitlichen, kostenlosen Plattform in Ubuntu vom Telefon bis zur Cloud resultiert eine Familie kohärenter Schnittstellen für Telefon, Tablet, TV und Desktop. Diese Vision macht Ubuntu zur ersten Wahl für verschiedene Institutionen – von Anbietern öffentlicher Clouds bis zu den Herstellern von Unterhaltungselektronik – und zu einem Favoriten bei individuellen Technologen.

Mit Entwicklern und Engineering Centers überall auf der Welt ist Canonical hervorragend positioniert, um Partnerschaften mit Hardwareherstellern, Inhaltsanbietern und Softwareentwicklern einzugehen, um Ubuntu-Lösungen auf den Markt zu bringen – von PCs bis hin zu Servern und portablen Geräten.
