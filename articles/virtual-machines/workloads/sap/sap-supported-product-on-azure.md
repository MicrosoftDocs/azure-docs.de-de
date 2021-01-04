---
title: 'SAP in Azure: Welche SAP-Software wird in Azure unterstützt?'
description: Es wird beschrieben, welche SAP-Software für die Bereitstellung in Azure unterstützt wird.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a02c30423ab3c6de87eac7d34b5f0004e54a8d5d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957808"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Welche SAP-Software wird für Azure-Bereitstellungen unterstützt?
In diesem Artikel wird beschrieben, wie Sie ermitteln können, welche SAP-Software für Azure-Bereitstellungen unterstützt wird und welche Betriebssystemversionen bzw. DBMS-Releases benötigt werden.

Um ermitteln zu können, ob Ihre aktuelle SAP-Software unterstützt wird und welche Betriebssystemversionen und DBMS-Releases für Ihre SAP-Software in Azure unterstützt werden, benötigen Sie Zugriff auf Folgendes:

- SAP-Supporthinweise
- SAP-Produktverfügbarkeitsmatrix



## <a name="general-restrictions-for-sap-workload"></a>Allgemeine Einschränkungen für SAP-Workloads
Azure IaaS-Dienste, die für SAP-Workloads verwendet werden können, sind auf x86-64- bzw. x64-Hardware beschränkt. Es sind keine Sparc- oder Power CPU-Angebote verfügbar, die für SAP-Workloads gelten. Kunden mit Anwendungen unter Betriebssystemen, die auf Hardwarearchitekturen wie IBM Mainframe oder AS400 basieren, oder mit den Betriebssystemen HP-UX, Solaris oder AIX müssen ihre SAP-Anwendungen, einschließlich des DBMS, auf eines der folgenden Betriebssysteme umstellen:

- Windows Server 64 Bit für die x86-64-Plattform
- SUSE Linux 64 Bit für die x86-64-Plattform
- Red Hat Linux 64 Bit für die x86-64-Plattform
- Oracle Linux 64 Bit für die x86-64-Plattform

In Kombination mit SAP-Software werden keine anderen Betriebssystemversionen oder Linux-Distributionen unterstützt. Die genauen Details zu bestimmten Versionen und Fällen sind weiter unten dokumentiert.


## <a name="you-start-here"></a>Einstieg
Der Ausgangspunkt für Sie ist [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533). Wenn Sie diesen SAP-Hinweis von oben nach unten durchgehen, werden verschiedene Bereiche mit unterstützter Software und entsprechenden VMs angezeigt.

Im ersten Abschnitt sind die Mindestanforderungen für Betriebssystemversionen aufgeführt, die mit SAP-Software auf Azure-VMs allgemein unterstützt werden. Wenn Sie diese Mindestanforderungen nicht erfüllen und ältere Versionen dieser Betriebssysteme ausführen, müssen Sie Ihre Betriebssystemversion auf eine dieser Mindestversionen bzw. aktuellere Versionen aktualisieren. Es stimmt, dass von Azure ältere Versionen von einigen dieser Betriebssysteme im Allgemeinen unterstützt werden. Die angegebenen Einschränkungen bzw. Mindestversionen basieren aber auf durchgeführten Tests und Qualifizierungen, die nicht weiter auf frühere Versionen ausgeweitet werden. 


> [!NOTE]
>Es gibt einige spezifische VM-Typen, große HANA-Instanzen bzw. SAP-Workloads, für die aktuellere Betriebssystemversionen benötigt werden. Fälle wie dieser werden im gesamten Dokument erwähnt. Ähnliche Fälle sind entweder in den SAP-Hinweisen oder anderen SAP-Veröffentlichungen eindeutig dokumentiert.

Im folgenden Abschnitt sind allgemeine SAP-Plattformen aufgelistet, für die für die unterstützten Versionen und – was noch wichtiger ist – für die unterstützten SAP-Kernel Unterstützung vorhanden ist. Es sind NetWeaver/ABAP- oder Java-Stapel aufgeführt, die unterstützt werden UND für die Kernel-Mindestversionen benötigt werden. Die aktuelleren ABAP-Stapel werden unter Azure unterstützt. Kernel-Mindestversionen sind nicht erforderlich, da Änderungen für Azure ab dem Beginn der Entwicklung der neueren Stapel implementiert wurden.

Sie müssen Folgendes überprüfen:

- Ob die von Ihnen ausgeführten SAP-Anwendungen durch die angegebenen Mindestversionen abgedeckt sind. Falls nicht, müssen Sie eine neue Zielversion definieren und in der SAP-Produktverfügbarkeitsmatrix überprüfen, welche Betriebssystembuilds und DBMS-Kombinationen für die neue Zielversion unterstützt werden. Ob Sie die richtige Betriebssystem- und DBMS-Version auswählen können
- Ob Sie Ihre SAP-Kernel bei einer Umstellung auf Azure aktualisieren müssen
- Ob Sie SAP-Supportpakete aktualisieren müssen. Dies gilt vor allem für Basissupportpakete, die ggf. für Fälle erforderlich sein können, in denen Sie die Umstellung auf ein aktuelleres DBMS-Release durchführen müssen.


Der nächste Abschnitt enthält ausführlichere Informationen zu weiteren SAP-Produkten und DBMS-Releases, die von SAP in Azure für Windows und Linux unterstützt werden. 

> [!NOTE]
> Die einzelnen DBMS-Mindestreleases sind sorgfältig ausgewählt und spiegeln ggf. nicht immer das gesamte Spektrum der DBMS-Releases wider, die von den unterschiedlichen DBMS-Anbietern in Azure unterstützt werden. Es wurden viele Aspekte von SAP-Workloads berücksichtigt, um diese Mindestversionen zu definieren. Für ältere DBMS-Releases werden keine Test- und Qualifizierungsmaßnahmen durchgeführt. 

> [!NOTE]
> Die aufgeführten Mindestversionen sind frühere Versionen von Betriebssystemen und Datenbankreleases. Wir empfehlen Ihnen dringend, jeweils die neuesten Betriebssystemversionen und Datenbankreleases zu verwenden. Für aktuellere Betriebssysteme und Datenbankreleases wurde in vielen Fällen die Ausführung in einer öffentlichen Cloud berücksichtigt und der Code angepasst, um diese Art der Ausführung (speziell in Azure) zu optimieren.

## <a name="oracle-dbms-support"></a>Oracle-DBMS-Unterstützung
Betriebssysteme, Oracle DBMS-Releases und Oracle-Funktionen, die in Azure unterstützt werden, sind unter [SAP-Supporthinweis Nr. 2039619](https://launchpad.support.sap.com/#/notes/2039619) noch einmal gesondert angegeben. Die wichtigsten Informationen dieses Hinweises sind:

- Die unterstützte Oracle-Mindestversion auf Azure-VMs, die für NetWeaver zertifiziert sind, ist „Oracle 11g Release 2 Patchset 3 (11.2.0.4)“.
- Als Gastbetriebssysteme sind nur Windows und Oracle Linux qualifiziert. Die genauen Versionen des Betriebssystems und die zugehörigen DBMS-Mindestreleases sind im Hinweis aufgeführt.
- Die Unterstützung von Oracle Linux erstreckt sich auch auf den Oracle-DBMS-Client. Dies bedeutet, dass alle SAP-Komponenten, z. B. Dialoginstanzen des ABAP- oder Java-Stapels, auch unter Oracle Linux ausgeführt werden müssen. Nur auf SAP-Komponenten innerhalb eines SAP-Systems dieser Art, für die keine Verbindung mit dem Oracle-DBMS hergestellt wird, kann ein anderes Linux-Betriebssystem ausgeführt werden.
- Keine Unterstützung von Oracle RAC 
- Oracle ASM wird für einige Fälle unterstützt. Details sind im Hinweis enthalten.
- Nicht auf Unicode basierende SAP-Systeme werden nur mit Anwendungsservern unterstützt, auf denen das Windows-Gastbetriebssystem ausgeführt wird. Das Gastbetriebssystem des DBMS kann Oracle Linux oder Windows sein. Der Grund für diese Einschränkung wird klar, wenn Sie sich die SAP-Produktverfügbarkeitsmatrix (PAM) ansehen. Keine Veröffentlichung von Nicht-Unicode-SAP-Kernel für Oracle Linux durch SAP

Da Sie die DBMS-Releases kennen, die für die Azure-Zielinfrastruktur unterstützt werden, müssen Sie die SAP-Produktverfügbarkeitsmatrix darauf überprüfen, ob die erforderlichen Betriebssystemversionen und das DBMS für die SAP-Produktversionen unterstützt werden, die Sie ausführen möchten. 


## <a name="sap-hana-support"></a>SAP HANA-Unterstützung
In Azure gibt es zwei Dienste, die zum Ausführen einer HANA-Datenbank genutzt werden können:

- Azure Virtual Machines
- [HANA (große Instanzen)](./hana-overview-architecture.md)

Für die Ausführung von SAP HANA müssen bei SAP zahlreichere und höhere Infrastrukturanforderungen als für NetWeaver oder andere SAP-Anwendungen und das DBMS erfüllt werden. Aus diesem Grund ist nur eine geringere Anzahl von Azure-VMs für die Ausführung des SAP HANA DBMS qualifiziert. Die Liste mit den für SAP HANA unterstützten Azure-Infrastrukturkomponenten finden Sie im so genannten [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> Die Einheiten, die mit dem Buchstaben „S“ beginnen, sind Einheiten vom Typ [HANA (große Instanzen)](./hana-overview-architecture.md). 

> [!NOTE]
> Für SAP gilt keine spezifische Zertifizierung, die von den SAP HANA-Hauptversionen abhängig ist. Entgegen der allgemeinen Meinung enthält die Spalte **Zertifizierungsszenario** in den [HANA-zertifizierten IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) **keine Angabe zu den zertifizierten HANA-Haupt- oder -Nebenversionen**. Sie müssen davon ausgehen, dass alle aufgeführten Einheiten für HANA 1.0 und HANA 2.0 verwendet werden können, sofern die zertifizierten Betriebssystemversionen für die jeweiligen Einheiten auch von HANA 1.0-Versionen unterstützt werden. 

Für die Nutzung von SAP HANA gelten unter Umständen andere Betriebssystem-Mindestversionen als für die allgemeinen NetWeaver-Fälle. Sie müssen die unterstützten Betriebssysteme für jede Einheit einzeln überprüfen, da diese variieren können. Klicken Sie hierfür jeweils auf eine Einheit. Es werden weitere Details angezeigt. Eine der aufgeführten Angaben ist die Liste mit den unterschiedlichen Betriebssystemen, die für die jeweilige Einheit unterstützt werden.

> [!NOTE]
> Einheiten vom Typ „SAP HANA in Azure (große Instanzen)“ sind in Bezug auf die unterstützten Betriebssysteme restriktiver als Azure-VMs. Andererseits kann es auch sein, dass von Azure-VMs aktuellere Betriebssystemversionen erzwungen werden. Dies gilt besonders für einige größere VM-Einheiten, für die Änderungen von Linux-Kerneln erforderlich waren.

Nachdem Sie das unterstützte Betriebssystem für die Azure-Infrastruktur kennen, müssen Sie [SAP-Supporthinweis Nr. 2235581](https://launchpad.support.sap.com/#/notes/2235581) auf die genauen SAP HANA-Releases und -Patchebenen prüfen, die für Ihre jeweiligen Azure-Einheiten unterstützt werden. 

> [!IMPORTANT]
> Der Schritt zum Überprüfen der genauen SAP HANA-Releases und der unterstützten Patchebenen ist sehr wichtig. In vielen Fällen hängt die Unterstützung einer bestimmten Betriebssystemversion von einer bestimmten Patchebene der ausführbaren SAP HANA-Dateien ab.

Sie kennen die spezifischen HANA-Releases, die Sie für die jeweils gewünschte Azure-Infrastruktur ausführen können. Als Nächstes müssen Sie in der SAP-Produktverfügbarkeitsmatrix ermitteln, ob Einschränkungen für die SAP-Produktreleases gelten, die die von Ihnen herausgefilterten HANA-Releases unterstützen.


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Zertifizierte Azure-VMs und Einheiten vom Typ „HANA (große Instanzen)“ und Durchsatz von Geschäftstransaktionen
Neben dem Evaluieren der unterstützten Betriebssystemversionen, DBMS-Releases und abhängigen SAP-Supportsoftware-Releases für Azure-Infrastruktureinheiten müssen Sie diese Einheiten anhand des Durchsatzes von Geschäftstransaktionen qualifizieren. Dies wird von SAP durch die Einheit „SAP“ ausgedrückt. Alle SAP-Größenanpassungen hängen von SAPS-Berechnungen ab. Beim Evaluieren von vorhandenen SAP-Systemen können Sie mit Unterstützung Ihres Infrastrukturanbieters den SAPS-Wert der Einheiten berechnen. Dies gilt sowohl für die DBMS-Ebene als auch für die Anwendungsschicht. In anderen Fällen, in denen neue Funktionen erstellt werden, kann anhand eines Größenanpassungsvorgangs mit SAP ermittelt werden, welche SAPS-Zahlen für die Anwendungsschicht und die DBMS-Ebene erforderlich sind. Als Infrastrukturanbieter muss Microsoft die Charakterisierung des SAP-Durchsatzes für die verschiedenen Einheiten bereitstellen, die über eine NetWeaver- bzw. eine HANA-Zertifizierung verfügen.

Für Azure-VMs sind diese SAPS-Durchsatzzahlen im [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533) dokumentiert. Für Einheiten vom Typ „HANA (große Instanzen)“ sind die SAPS-Durchsatzzahlen im [SAP-Supporthinweis Nr. 2316233](https://launchpad.support.sap.com/#/notes/2316233) dokumentiert.

Gemäß [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533) gilt Folgendes:

- **Für Azure-VMs der M-Serie und Mv2-Serie gelten andere Betriebssystem-Mindestversionen als für andere Azure-VM-Typen**. Die Anforderung in Bezug auf aktuellere Betriebssystemversionen basiert auf den Änderungen, die die verschiedenen Anbieter von Betriebssystemen für ihre Versionen vornehmen mussten, um für ihre Betriebssysteme die Ausführung unter den spezifischen Azure-VM-Typen zu ermöglichen oder die Leistung und den Durchsatz von SAP-Workloads auf diesen VM-Typen zu optimieren.
- Es sind zwei Tabellen vorhanden, in denen verschiedene VM-Typen angegeben sind. In der zweiten Tabelle ist der SAPS-Durchsatz für Azure-VM-Typen angegeben, die nur Azure-Standardspeicher unterstützen. Die DBMS-Bereitstellung auf den Einheiten, die in der zweiten Tabelle des Hinweises angegeben sind, wird nicht unterstützt.


## <a name="other-sap-products-supported-on-azure"></a>Andere unterstützte SAP-Produkte in Azure
Im Allgemeinen wird davon ausgegangen, dass aufgrund des derzeitigen Stands von Hyperscale-Clouds wie Azure der größte Teil der SAP-Software in Azure ohne Funktionsprobleme ausgeführt werden kann. Trotzdem und im Gegensatz zur Private Cloud-Visualisierung wird die Unterstützung der unterschiedlichen SAP-Produkte von SAP weiterhin explizit für die einzelnen Anbieter von Hyperscale-Clouds angegeben. Aus diesem Grund gibt es für die einzelnen SAP-Produkte unterschiedliche SAP-Supporthinweise zur Unterstützung für Azure. 

Für die Business Objects BI-Plattform enthält [SAP-Supporthinweis Nr. 2145537](https://launchpad.support.sap.com/#/notes/2145537) eine Liste mit den SAP Business Objects-Produkten, die in Azure unterstützt werden. Falls Sie Fragen zu Komponenten oder Kombinationen von Softwarereleases und Betriebssystemversionen haben, die nicht angegeben sind bzw. unterstützt werden und die aktueller als die aufgeführten Mindestversionen sind, müssen Sie eine SAP-Supportanfrage für die jeweilige Komponente erstellen.

Für Business Objects Data Services wird in [SAP-Supporthinweis Nr. 22288344](https://launchpad.support.sap.com/#/notes/2288344) beschrieben, welche Mindestunterstützung für SAP Data Services bei der Ausführung in Azure besteht. 

> [!NOTE]
> Wie im SAP-Supporthinweis angegeben, müssen Sie in der SAP-Produktverfügbarkeitsmatrix (PAM) die richtige Supportpaketebene für die Unterstützung in Azure ermitteln.

Die Unterstützung für SAP Datahub/Vora in Azure Kubernetes Services (AKS) ist im [SAP-Supporthinweis Nr. 2464722](https://launchpad.support.sap.com/#/notes/2464722) beschrieben.

Die Unterstützung für SAP BPC 10.1 SP08 ist im [SAP-Supporthinweis Nr. 2451795](https://launchpad.support.sap.com/#/notes/2451795) beschrieben.

Die Unterstützung für SAP Hybris Commerce Platform in Azure ist in der [Hybris-Dokumentation](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) beschrieben. Die Liste der unterstützten DBMS für SAP Hybris Commerce Platform lautet wie folgt:

- SQL Server und Oracle auf der Windows-Betriebssystemplattform. Es gelten die gleichen Mindestreleases wie für SAP NetWeaver. Weitere Informationen finden Sie im [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533).
- SAP HANA unter Red Hat und SUSE Linux. Es sind für SAP HANA zertifizierte VM-Typen erforderlich, wie zuvor in [diesem Dokument](#sap-hana-support) beschrieben. SAP (Hybris) Commerce Platform gilt als OLTP-Workload.
- SQL Azure DB ab SAP (Hybris) Commerce Platform Version 1811




## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die Informationen zu den nächsten Schritten unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](./planning-guide.md).
