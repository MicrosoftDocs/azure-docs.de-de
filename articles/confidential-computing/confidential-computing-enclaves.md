---
title: Virtuelle Computer für Confidential Computing in Azure
description: Hier erfahren Sie mehr über Intel SGX-Hardware zur Ermöglichung Ihrer Confidential Computing-Workloads.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 565f4971fffde1cbeb2234b43aaad5cce73b5404
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564377"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Virtuelle Azure Confidential Computing-Computer (Virtual Machines, VMs) – Übersicht


Azure ist der erste Cloudanbieter, der Confidential Computing in einer virtualisierten Umgebung anbietet. Wir haben virtuelle Computer entwickelt, die als Abstraktionsschicht zwischen der Hardware und Ihrer Anwendung fungieren. Sie können Workloads bedarfsgesteuert und mit Redundanz- und Verfügbarkeitsoptionen ausführen.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX-fähige virtuelle Computer

Bei virtuellen Azure Confidential Computing-Computern ist ein Teil der CPU-Hardware für einen Teil des Codes und der Daten in Ihrer Anwendung reserviert. Dieser eingeschränkte Teil ist die Enklave. 

![VM-Modell](media/overview/hardware-backed-enclave.png)

Azure Confidential Computing-Infrastruktur umfasst derzeit eine spezielle SKU mit virtuellen Computern (VMs). Diese VMs werden auf Intel-Prozessoren mit Software Guard Extension (Intel SGX) ausgeführt. [Intel SGX](https://intel.com/sgx) ist die Komponente, die den höheren Schutzgrad beim Confidential Computing ermöglicht. 

Azure bietet die [DCsv2-Serie](../virtual-machines/dcv2-series.md), die auf Intel SGX-Technologie basiert, für die Erstellung von hardwarebasierten Enklaven an. Sie können sichere Enklavenanwendungen entwickeln, die auf VMs der DCsv2-Serie ausgeführt werden, um Ihre Anwendungsdaten und den verwendeten Code zu schützen. 

[Lesen Sie mehr](virtual-machine-solutions.md) zur Bereitstellung von virtuellen Azure Confidential Computing-Computern mit hardwarebasierten vertrauenswürdigen Enklaven.

## <a name="enclaves"></a>Enclaves

Enklaven sind geschützte Bereiche des Prozessors und Arbeitsspeichers von Hardware. Es gibt keine Möglichkeit, Daten oder Code innerhalb der Enklave anzuzeigen – auch nicht mit einem Debugger. Wenn mit nicht vertrauenswürdigem Code versucht wird, den Inhalt im Arbeitsspeicher der Enklave zu ändern, wird die Umgebung deaktiviert und die Durchführung der Vorgänge verweigert.

Sie können sich eine Enklave im Wesentlichen wie eine geschützte Box vorstellen. Sie fügen verschlüsselten Code und verschlüsselte Daten in die Blackbox ein. Von außen ist der Blick in das innere der Blackbox nicht möglich. Sie ordnen der Enklave einen Schlüssel zum Entschlüsseln der Daten zu, und die Daten werden dann verarbeitet und wieder verschlüsselt, bevor das Versenden aus der Enklave erfolgt.

Bei jeder Enklave gibt es eine festgelegte Größe von verschlüsseltem Seitencache (Encrypted Page Cache, EPC) zur Bestimmung der Menge an Arbeitsspeicher, die jede Enklave enthalten kann. Größere virtuelle DCsv2-Computer haben mehr EPC-Arbeitsspeicher. Lesen Sie die Seite [DCsv2-Spezifikationen](../virtual-machines/dcv2-series.md), um die maximale EPC-Größe pro VM-Größe zu erfahren.



### <a name="developing-applications-to-run-inside-enclaves"></a>Entwickeln von Anwendungen zur Ausführung innerhalb von Enklaven
Beim Entwickeln von Anwendungen können Sie [Softwaretools](application-development.md) verwenden, um Teile Ihres Codes und Ihrer Daten in der Enklave abzuschirmen. Mit diesen Tools können Sie sicherstellen, dass Ihr Code und Ihre Daten von Personen außerhalb der vertrauenswürdigen Umgebung nicht angezeigt oder geändert werden können. 

## <a name="next-steps"></a>Nächste Schritte
- [Lesen Sie bewährte Methoden](virtual-machine-solutions.md) für die Bereitstellung von Lösungen auf virtuellen Azure Confidential Computing-Computern.
- [Bereitstellen eines virtuellen Computers der DCsv2-Serie](quick-create-portal.md)
- [Entwickeln einer enklavefähigen Anwendung](application-development.md) mit dem OE SDK