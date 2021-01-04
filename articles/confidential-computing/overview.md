---
title: 'Azure Confidential Computing: Übersicht'
description: Enthält eine Übersicht über Azure Confidential Computing (ACC).
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 44479db6b29075e87b0d2dcef6f0d9bc1881738c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560824"
---
# <a name="confidential-computing-on-azure"></a>Confidential Computing unter Azure

Mit Azure Confidential Computing können Sie Ihre vertraulichen Daten isolieren, während sie in der Cloud verarbeitet werden. Confidential Computing wird in vielen Branchen wie folgt genutzt, um die eigenen Daten zu schützen:

- Schützen von Finanzdaten
- Schützen von Patientendaten
- Ausführen von Machine Learning-Prozessen für vertrauliche Daten
- Ausführen von Algorithmen für verschlüsselte Datasets aus mehreren Quellen


## <a name="overview"></a>Übersicht
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Wir wissen, wie wichtig der Schutz Ihrer Clouddaten für Sie ist. Wir nehmen Ihre Bedenken ernst. Hier sind einige Fragen aufgeführt, die unsere Kunden in Bezug auf die Verlagerung von sensiblen Workloads in die Cloud ggf. stellen: 

- Wie kann ich sicherstellen, dass Microsoft keinen Zugriff auf unverschlüsselte Daten hat?
- Wie kann ich Sicherheitsbedrohungen durch privilegierte Administratoren innerhalb meines Unternehmens verhindern?
- Welche weiteren Möglichkeiten habe ich, um zu verhindern, dass Dritte Zugriff auf vertrauliche Kundendaten haben?

Mit Microsoft Azure können Sie Ihre Angriffsfläche verkleinern, um einen höheren Schutz von Daten zu erzielen. Azure verfügt bereits über viele Tools zum Schützen von [**ruhenden Daten**](../security/fundamentals/encryption-atrest.md), indem Modelle wie die clientseitige und serverseitige Verschlüsselung genutzt werden. Darüber hinaus können in Azure Verfahren genutzt werden, um [**Daten während der Übertragung**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) zu verschlüsseln. Hierfür werden sichere Protokolle wie TLS und HTTPS verwendet. Auf dieser Seite wird noch eine dritte Möglichkeit zum Verschlüsseln von Daten vorgestellt: die Verschlüsselung von **Daten in Gebrauch**.

## <a name="introduction-to-confidential-computing"></a>Einführung in Confidential Computing  

Confidential Computing ist ein branchenüblicher Begriff, der vom [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC) definiert wurde. Diese Vereinigung hat es sich zur Aufgabe gemacht, Definitionen für die Einführung des Confidential Computing festzulegen und die Nutzung zu fördern. Das CCC definiert Confidential Computing wie folgt: Der Schutz verwendeter Daten durch Ausführen von Berechnungen in einer hardwarebasierten vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE).

Eine TEE ist eine Umgebung, mit der die ausschließliche Ausführung von autorisiertem Code erzwungen wird. Für alle Daten in der TEE ist es nicht möglich, dass sie mit Code, der sich außerhalb dieser Umgebung befindet, gelesen oder manipuliert werden. 

### <a name="lessen-the-need-for-trust"></a>Weniger Vertrauen erforderlich
Das Ausführen von Workloads in der Cloud erfordert Vertrauen. Sie bringen dieses Vertrauen mehreren Anbietern entgegen, um die Verwendung verschiedener Komponenten Ihrer Anwendung zu ermöglichen.


**App-Softwarehersteller:** Sie können Software vertrauen, indem Sie Anwendungssoftware lokal, unter Verwendung von Open-Source-Lösungen oder durch interne Entwicklung bereitstellen.

**Hardwareanbieter:** Sie können Hardware vertrauen, indem Sie lokale oder interne Hardware verwenden. 

**Infrastrukturanbieter:** Sie können Cloudanbietern vertrauen oder Ihre eigenen lokalen Rechenzentren verwalten.


Azure Confidential Computing macht es einfacher, dem Cloudanbieter zu vertrauen, da sie ihm bei verschiedenen Aspekten der Compute-Cloudinfrastruktur weniger Vertrauen entgegenbringen müssen. Azure Confidential Computing minimiert das notwendige Vertrauen für den Betriebssystemkernel des Hosts, für den Hypervisor, für den VM-Administrator und für den Hostadministrator.

### <a name="reducing-the-attack-surface"></a>Verkleinern der Angriffsfläche
Die vertrauenswürdige Rechenbasis (Trusted Computing Base, TCB) bezieht sich auf alle Hardware-, Firmware- und Softwarekomponenten eines Systems, die eine sichere Umgebung bereitstellen. Die Komponenten innerhalb der TCB werden als kritisch eingestuft. Ist eine einzelne Komponente innerhalb der TCB kompromittiert, kann die Sicherheit des gesamten Systems gefährdet sein. 

Eine kleinere TCB bedeutet höhere Sicherheit. Es besteht eine geringere Gefahr durch verschiedene Sicherheitsrisiken, Schadsoftware, Angriffe und böswillige Personen. Das Ziel von Azure Confidential Computing besteht darin, die TCB für Ihre Cloudworkloads durch TEEs zu verkleinern. TEEs reduzieren Ihre TCB auf vertrauenswürdige Runtime-Binärdateien, auf vertrauenswürdigen Code und auf vertrauenswürdige Bibliotheken. Wenn Sie die Azure-Infrastruktur und Azure-Dienste für Confidential Computing verwenden, können Sie Microsoft vollständig aus Ihrer TCB entfernen.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Verwenden von Azure für cloudbasiertes Confidential Computing <a id="cc-on-azure"></a>

Mit Azure Confidential Computing können Sie Confidential Computing-Funktionen in einer virtualisierten Umgebung nutzen. Sie können jetzt Tools, Software und Cloudinfrastruktur verwenden, um basierend auf sicherer Hardware Erweiterungen vorzunehmen.  

**Verhindern nicht autorisierter Zugriffe:** Verwenden Sie vertrauliche Daten in der Cloud. Vertrauen Sie darauf, dass Azure den bestmöglichen Datenschutz bietet – mit wenigen bis gar keinen Änderungen im Vergleich zu aktuellen Maßnahmen.

**Einhaltung gesetzlicher Bestimmungen**: Migrieren Sie zur Cloud, und behalten Sie die volle Kontrolle über Daten, um behördliche Vorschriften zum Schutz von persönlichen Informationen und IP-Adressen der Organisation zu erfüllen.

**Sichere Zusammenarbeit ohne Vertrauensvorschuss:** Bewältigen Sie branchenweite aufgabenbezogene Probleme, indem Sie Daten von verschiedenen Organisationen und sogar von Mitbewerbern durchforsten, um umfassende Datenanalysen zu ermöglichen und tiefgreifendere Erkenntnisse zu gewinnen.

**Isolierte Verarbeitung:** Bieten Sie eine neue Reihe von Produkten an, die mittels Blindverarbeitung die Haftbarkeit für private Daten beseitigen. Benutzerdaten können nicht einmal vom Dienstanbieter abgerufen werden. 

## <a name="get-started"></a>Erste Schritte
### <a name="azure-compute"></a>Azure Compute
Erstellen Sie Anwendungen, die auf Confidential Computing-IaaS-Angeboten in Azure basieren.
- Virtuelle Computer (virtual machines, VMs): [DCsv2-Serie](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [Confidential Computing-Knoten in Azure Kubernetes Service (Public Preview)](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure Security 
Gewährleisten Sie die Sicherheit Ihrer Workloads durch Überprüfungsmethoden und hardwaregebundene Schlüsselverwaltung. 
- Nachweis: [Microsoft Azure Attestation (Vorschau)](../attestation/overview.md)
- Schlüsselverwaltung: Verwaltetes HSM (Vorschauversion)

### <a name="develop"></a>Entwickeln
Beginnen Sie mit der Verwendung/Entwicklung enklavefähiger Anwendungen, und stellen Sie vertrauliche Algorithmen über das Framework für vertrauliches Rückschließen bereit.
- Schreiben Sie Anwendungen für die Ausführung auf virtuellen Computern der DCsv2-Serie: [Open Enclave SDK](https://github.com/openenclave/openenclave)
- Vertrauliche ML-Modelle in der ONNX-Runtime: [Vertrauliches Rückschließen (Beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Nächste Schritte

Stellen Sie einen virtuellen Computer der DCsv2-Serie bereit, und installieren Sie das OE SDK darauf.

> [!div class="nextstepaction"]
> [Schnellstart: Bereitstellen eines virtuellen Azure Confidential Computing-Computers im Marketplace](quick-create-marketplace.md)