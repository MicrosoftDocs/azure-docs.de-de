---
title: Isolation in der öffentlichen Azure-Cloud | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Azure die Isolation gegen bösartige und nicht bösartige Benutzer sicherstellt und Architekten eine Auswahl von Isolationsoptionen bietet.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c06fb0830ae709918b668ed60efbaaf47a63ce84
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842837"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolation in der öffentlichen Azure-Cloud

Mit Azure können Sie Anwendungen und virtuelle Computer (VMs) auf gemeinsam genutzter physischer Infrastruktur ausführen. Einer der wichtigsten ökonomischen Gründe für die Ausführung von Anwendungen in einer Cloudumgebung ist die Möglichkeit, die Kosten für gemeinsame Ressourcen auf mehrere Kunden zu verteilen. Diese Nutzung der Mehrinstanzenfähigkeit sorgt für eine Verbesserung der Effizienz, indem das Ressourcen-Multiplexing für verschiedene Kunden zu niedrigen Kosten durchgeführt wird. Leider ist dies auch mit dem Risiko verbunden, physische Server und andere Infrastrukturressourcen für die Ausführung Ihrer sensiblen Anwendungen und VMs freizugeben, die sich ggf. im Besitz eines unbekannten und potenziell böswilligen Benutzers befinden.

In diesem Artikel wird beschrieben, wie Azure die Isolation gegenüber böswilligen und unbedenklichen Benutzern ermöglicht, und er dient als Anleitung für die Erstellung von Cloudlösungen, indem für IT-Architekten verschiedene Isolationsoptionen angeboten werden.

## <a name="tenant-level-isolation"></a>Isolation auf Mandantenebene

Einer der Hauptvorteile des Cloud Computing ist das Konzept der gemeinsamen, allgemeinen Infrastruktur für eine größere Zahl von Kunden, mit dem Kostenersparnisse realisiert werden können. Dieses Konzept wird als Mehrinstanzenfähigkeit bezeichnet. Microsoft arbeitet ständig daran sicherzustellen, dass die mehrinstanzenfähige Architektur von Microsoft Cloud Azure die geltenden Standards in Bezug auf Sicherheit, Vertraulichkeit, Datenschutz, Integrität und Verfügbarkeit unterstützt.

An einem cloudaktivierten Arbeitsplatz kann ein Mandant als ein Client oder eine Organisation definiert werden, die eine bestimmte Instanz dieses Clouddienstes besitzt und verwaltet. Bei der von Microsoft Azure bereitgestellten Identitätsplattform ist ein Mandant einfach eine dedizierte Instanz von Azure Active Directory (Azure AD), die Ihre Organisation erhält und besitzt, wenn sie sich für einen Microsoft-Clouddienst registriert.

Jedes Azure AD-Verzeichnis ist eindeutig und von anderen Azure AD-Verzeichnissen getrennt. Genau so wie ein Bürogebäude, das ein sicherer Ort nur für Ihre Organisation ist, soll auch ein Azure AD-Verzeichnis ein sicheres Anlagegut nur für Ihre Organisation darstellen. Die Azure AD-Architektur isoliert Kundendaten und Identitätsinformationen und verhindert deren Vermischung. Dies bedeutet, dass Benutzer und Administratoren eines Azure AD-Verzeichnisses nicht versehentlich oder böswillig auf Daten in einem anderen Verzeichnis zugreifen können.

### <a name="azure-tenancy"></a>Azure-Mandant

Die Bezeichnung „Azure-Mandant“ (Azure-Abonnement) bezieht sich auf eine Kunden-/Abrechnungsbeziehung und einen eindeutigen [Mandanten](../../active-directory/develop/quickstart-create-new-tenant.md) in [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Die Isolation auf Mandantenebene wird in Microsoft Azure mithilfe von Azure Active Directory und der damit verbundenen [rollenbasierten Zugriffssteuerung von Azure](../../role-based-access-control/overview.md) erreicht. Jedes Azure-Abonnement ist mit einem Azure Active Directory-Verzeichnis (AD) verknüpft.

Benutzer, Gruppen und Anwendungen aus diesem Verzeichnis können Ressourcen im Azure-Abonnement verwalten. Sie können diese Zugriffsrechte mit dem Azure-Portal, Azure-Befehlszeilentools und Azure-Verwaltungs-APIs zuweisen. Ein Azure AD-Mandant ist logisch isoliert und nutzt Sicherheitsgrenzen, sodass kein Kunde auf andere Mandanten zugreifen oder diese kompromittieren kann – weder in böswilliger Absicht noch versehentlich. Azure AD wird auf „Bare-Metal“-Servern ausgeführt, die in einem abgetrennten Netzwerksegment isoliert sind, für das per Paketfilterung auf Hostebene und über die Windows-Firewall unerwünschte Verbindungen und Datenübertragungen blockiert werden.

- Zugriff auf Daten in Azure AD erfordert die Benutzerauthentifizierung über einen Sicherheitstokendienst (Security Token Service, STS). Informationen zu Vorhandensein, Aktivierungsstatus und Rolle des Benutzers verwendet das Autorisierungssystem, um festzustellen, ob der Benutzer in dieser Sitzung für den angeforderten Zugriff auf den Zielmandanten autorisiert ist.

![Azure-Mandant](./media/isolation-choices/azure-isolation-fig1.png)

- Mandanten sind diskrete Container, zwischen denen keine Beziehung besteht.

- Für Mandanten ist der Zugriff untereinander nur möglich, wenn dies vom Mandantenadministrator durch einen Verbund oder die Bereitstellung von Benutzerkonten anderer Mandanten gewährt wird.

- Der physische Zugriff auf die Server des Azure AD-Diensts und der direkte Zugriff auf die Back-End-Systeme von Azure AD ist eingeschränkt.

- Azure AD-Benutzer haben keinen Zugriff auf physische Assets oder Standorte und können daher auch nicht die logischen Azure RBAC-Richtlinien umgehen. Diese sind unten beschrieben.

Für Diagnose- und Wartungszwecke ist ein Betriebsmodell mit einem System für Just-in-Time-Rechteerweiterungen erforderlich und wird entsprechend verwendet. Mit Azure AD Privileged Identity Management (PIM) wird das Konzept der „berechtigten Administratoren“ eingeführt. [Berechtigte Administratoren](../../active-directory/privileged-identity-management/pim-configure.md) sollten Benutzer sein, die von Zeit zu Zeit (aber nicht jeden Tag) Zugriff mit erhöhten Rechten benötigen. Die Rolle ist inaktiv, bis der Benutzer Zugriff benötigt. Dann wird eine Aktivierung ausgeführt, und der Benutzer wird für einen zuvor festgelegten Zeitraum zu einem aktiven Administrator.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

In Azure Active Directory wird jeder Mandant in einem eigenen geschützten Container mit Richtlinien und Berechtigungen gehostet, wobei der Container im alleinigen Besitz des Mandanten ist und von diesem verwaltet wird.

Das Konzept der Mandantencontainer ist auf allen Ebenen tief in den Verzeichnisdienst eingebettet – von Portalen bis hin zu persistentem Speicher.

Auch wenn Metadaten mehrerer Azure Active Directory-Mandanten auf demselben physischen Datenträger gespeichert sind, besteht keine Beziehung zwischen den Containern, sondern es gilt nur, was vom Verzeichnisdienst definiert wird. Dies wird wiederum vom Mandantenadministrator vorgegeben.

### <a name="azure-role-based-access-control-azure-rbac"></a>Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)

Die [rollenbasierte Zugriffssteuerung von Azure](../../role-based-access-control/overview.md) (Azure RBAC) unterstützt Sie beim Freigeben der unterschiedlichen Komponenten, die in einem Azure-Abonnement verfügbar sind, indem eine präzise Zugriffsverwaltung für Azure ermöglicht wird. Mithilfe von Azure RBAC können Sie die Aufgabenbereiche in Ihrer Organisation unterteilen und den Zugriff jeweils in Abhängigkeit davon gewähren, was Benutzer zum Erledigen ihrer Arbeit benötigen. Anstatt allen Benutzern uneingeschränkte Berechtigungen für das Azure-Abonnement oder Ressourcen zu gewähren, können Sie die Berechtigungen auf bestimmte Aktionen beschränken.

Azure RBAC verfügt über drei grundlegende Rollen, die für alle Ressourcentypen gelten:

- **Besitzer** verfügen über vollständigen Zugriff auf alle Ressourcen, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren.

- **Mitwirkende** können alle Arten von Azure-Ressourcen erstellen und verwalten, aber keinen anderen Personen Zugriff gewähren.

- **Leser** können vorhandene Azure-Ressourcen anzeigen.

![Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](./media/isolation-choices/azure-isolation-fig3.png)

Die verbleibenden Azure-Rollen in Azure ermöglichen die Verwaltung von bestimmten Azure-Ressourcen. Mit der Rolle „Mitwirkender von virtuellen Computern“ können Benutzer beispielsweise virtuelle Computer erstellen und verwalten. Sie haben damit keinen Zugriff auf das virtuelle Azure-Netzwerk oder das Subnetz, mit dem der virtuelle Computer verbunden ist.

Unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md) sind die Rollen aufgeführt, die in Azure verfügbar sind. Hierbei werden die Vorgänge und der Bereich angegeben, die mit jeder integrierten Rolle für Benutzer gewährt werden. Wenn Sie Ihre eigenen Rollen definieren möchten, um eine noch bessere Kontrolle zu haben, helfen Ihnen die Informationen zum Erstellen von [benutzerdefinierten Rollen in Azure RBAC](../../role-based-access-control/custom-roles.md)weiter.

Weitere Beispiele für Azure Active Directory-Funktionen sind:

- Azure AD ermöglicht SSO für SaaS-Anwendungen unabhängig davon, wo sie gehostet werden. Einige Anwendungen bilden einen Verbund mit Azure AD, andere verwenden Kennwort-SSO. Verbundanwendungen können auch die Benutzerbereitstellung und [Kennworttresore](https://www.techopedia.com/definition/31415/password-vault) unterstützen.

- Der Zugriff auf Daten in [Azure Storage](https://azure.microsoft.com/services/storage/) wird über die Authentifizierung gesteuert. Jedes Speicherkonto verfügt über einen Primärschlüssel ([Speicherkontoschlüssel](../../storage/common/storage-account-create.md); Storage Account Key, SAK) und einen sekundären geheimen Schlüssel (Shared Access Signature, SAS).

- Azure AD bietet Identity-as-a-Service durch Verbund (unter Verwendung der [Active Directory-Verbunddienste](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)) sowie Synchronisierung und Replikation mit lokalen Verzeichnissen.

- [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) ist der Dienst für die mehrstufige Authentifizierung, bei der Benutzer Anmeldevorgänge über eine mobile App, einen Telefonanruf oder per SMS bestätigen müssen. Er kann mit Azure AD verwendet werden, um lokale Ressourcen mit dem Azure Multi-Factor Authentication-Server zu schützen, eignet sich aber auch für benutzerdefinierte Anwendungen und Verzeichnisse, die das SDK verwenden.

- Mit [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) können Sie virtuelle Azure-Computer in eine Active Directory-Domäne einbinden, ohne Domänencontroller bereitzustellen. Sie können sich bei diesen virtuellen Computern mithilfe Ihrer Active Directory-Unternehmensanmeldeinformationen anmelden und in die Domäne eingebundene virtuelle Computer mithilfe von Gruppenrichtlinien verwalten, um Sicherheitsbaselines für alle Ihre virtuellen Azure-Computer zu erzwingen.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) bietet einen hoch verfügbaren, globalen Identitätsverwaltungsdienst für kundenorientierte Anwendungen, der für Millionen von Identitäten skaliert werden kann. Er kann über mobile und Webplattformen integriert werden. Ihre Kunden können sich über eine anpassbare Oberfläche bei all Ihren Anwendungen anmelden und zu diesem Zweck entweder vorhandene Konten aus sozialen Netzwerken nutzen oder Anmeldeinformationen festlegen.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolation gegenüber Microsoft-Administratoren und Datenlöschung

Microsoft ergreift strenge Maßnahmen, um Ihre Daten vor unerwünschtem Zugriff und dem Zugriff durch unbefugte Personen zu schützen. Diese auf den Betrieb bezogenen Prozesse und Kontrollen basieren auf den [Nutzungsbedingungen für Online Services](https://aka.ms/Online-Services-Terms), die vertragliche Zusagen zum Zugriff auf Ihre Daten enthalten.

- Microsoft-Techniker haben standardmäßig keinen Zugriff auf Ihre Daten in der Cloud. Sie erhalten nur bei Bedarf und unter Aufsicht der Managementebene Zugriff. Dieser Zugriff wird sorgfältig kontrolliert und protokolliert und widerrufen, wenn er nicht mehr benötigt wird.
- Es kann sein, dass Microsoft andere Unternehmen damit beauftragt, in begrenztem Umfang Dienstleistungen im Namen von Microsoft zu übernehmen. Subunternehmer dürfen nur auf Kundendaten zugreifen, um die von uns in Auftrag gegebenen Dienstleistungen bereitzustellen, und die Nutzung zu anderen Zwecken ist untersagt. Darüber hinaus sind Subunternehmer vertraglich dazu verpflichtet, die Vertraulichkeit der Informationen unserer Kunden zu wahren.

Unternehmensdienstleistungen, die Prüfzertifizierungen unterliegen (z.B. ISO/IEC 27001), werden von Microsoft und akkreditierten Prüfunternehmen regelmäßig überprüft. Diese Überprüfungen werden stichprobenartig durchgeführt, um nachzuweisen, dass der Zugriff nur zu zulässigen geschäftlichen Zwecken erfolgt. Auf Ihre eigenen Kundendaten können Sie jederzeit und aus jedem Grund zugreifen.

Wenn Sie Daten löschen, werden diese Daten von Microsoft Azure gelöscht, einschließlich zwischengespeicherte Daten und Sicherungskopien. Für Dienste innerhalb des Gültigkeitsbereichs wird dieser Löschvorgang innerhalb von 90 Tagen nach dem Ende des Aufbewahrungszeitraums durchgeführt. (Dienste innerhalb des Gültigkeitsbereichs sind im Abschnitt mit den Bedingungen zur Datenverarbeitung in den [Nutzungsbedingungen für Online Services](https://aka.ms/Online-Services-Terms) definiert.)

Wenn für einen zum Speichern verwendeten Datenträger ein Hardwarefehler auftritt, wird er auf sichere Weise [gelöscht oder zerstört](https://microsoft.com/trustcenter/privacy/you-own-your-data), bevor er von Microsoft zwecks Austausch oder Reparatur an den Hersteller zurückgeschickt wird. Die Daten auf dem Laufwerk werden überschrieben, um sicherzustellen, dass die Daten nicht mehr wiederhergestellt werden können.

## <a name="compute-isolation"></a>Computeisolation

Microsoft Azure enthält viele verschiedene cloudbasierte Computingdienste mit einer großen Auswahl an Computeinstanzen und -diensten, die automatisch hoch- und herunterskaliert werden können, um die Anforderungen Ihrer Anwendung bzw. Ihres Unternehmens zu erfüllen. Diese Computeinstanz bzw. der Computedienst ermöglichen die Isolation auf mehreren Ebenen, um Daten zu schützen, ohne dass auf die von Kunden gewünschte Flexibilität bei der Konfiguration verzichtet werden muss.

### <a name="isolated-virtual-machine-sizes"></a>Isolierte Größen virtueller Computer

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Dedizierte Hosts

Neben den im vorherigen Abschnitt beschriebenen isolierten Hosts bietet Azure auch dedizierte Hosts. Bei dedizierten Hosts in Azure handelt es sich um einen Dienst für die Bereitstellung physischer Server, die virtuelle Computer hosten können und für ein einzelnes Azure-Abonnement reserviert sind. Dedizierte Hosts bieten Hardwareisolation auf physischer Serverebene. Es werden keine anderen virtuellen Computer auf Ihren Hosts platziert. Dedizierte Hosts werden in den gleichen Rechenzentren bereitgestellt und nutzen das gleiche Netzwerk und die gleiche zugrunde liegende Speicherinfrastruktur wie andere, nicht isolierte Hosts. Weitere Informationen finden Sie in der umfassenden Übersicht zu [dedizierten Azure-Hosts](../../virtual-machines/dedicated-hosts.md).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V- und Stammbetriebssystem-Isolation zwischen Stamm-VM und Gast-VMs

Die Computeplattform von Azure basiert auf der Virtualisierung von Computern. Dies bedeutet, dass der gesamte Kundencode auf einem virtuellen Hyper-V-Computer ausgeführt wird. Auf jedem Azure-Knoten (bzw. Netzwerkendpunkt) ist ein Hypervisor angeordnet, der direkt über die Hardware ausgeführt wird und einen Knoten in eine variable Anzahl von virtuellen Gastcomputern unterteilt.

![Hyper-V- und Stammbetriebssystem-Isolation zwischen Stamm-VM und Gast-VMs](./media/isolation-choices/azure-isolation-fig4.jpg)

Darüber hinaus weist jeder Knoten eine spezielle Stamm-VM auf, auf der das Hostbetriebssystem ausgeführt wird. Eine kritische Grenze ist die Isolation der Stamm-VM von den Gast-VMs und der Gast-VMs voneinander. Diese Vorgänge werden vom Hypervisor und dem Stammbetriebssystem verwaltet. Der Hypervisor und das Stammbetriebssystem basieren auf mehreren Jahrzehnten Erfahrung, die Microsoft in Bezug auf die Sicherheit von Betriebssystemen besitzt, sowie auf neueren Erfahrungen mit Hyper-V von Microsoft, um eine strenge Isolation von Gast-VMs zu ermöglichen.

Die Azure-Plattform verwendet eine virtualisierte Umgebung. Benutzerinstanzen werden als eigenständige virtuelle Computer ausgeführt, die keinen Zugriff auf einen physischen Hostserver haben.

Der Hypervisor von Azure verhält sich wie ein Microkernel und übergibt alle Hardwarezugriffsanforderungen von virtuellen Gastcomputern an den Host, damit sie über eine Schnittstelle mit gemeinsam genutztem Speicherbereich namens VMBus verarbeitet werden. Dies verhindert, dass Benutzer RAW-Lese-, -Schreib- und -Ausführungszugriff auf das System erhalten, und verringert das Risiko der Freigabe von Systemressourcen.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Erweiterter Algorithmus für VM-Anordnung und Schutz vor Seitenkanalangriffen

Alle VM-übergreifenden Angriffe bestehen aus zwei Schritten: Anordnen einer vom Angreifer gesteuerten VM auf demselben Host wie eine der Ziel-VMs und anschließendes Durchbrechen der Isolationsgrenze, um entweder sensible Informationen des Opfers zu stehlen oder aus Gier oder Lust am Vandalismus die Leistung zu beeinträchtigen. Microsoft Azure bietet Schutz vor beiden Schritten, indem ein erweiterter Algorithmus für die VM-Anordnung und ein Schutzverfahren für alle bekannten Seitenkanalangriffe, einschließlich „Noisy Neighbor“-VMs, bereitgestellt werden.

### <a name="the-azure-fabric-controller"></a>Azure Fabric Controller

Der Azure Fabric Controller ist zuständig für die Zuweisung von Infrastrukturressourcen zu Mandantenworkloads und verwaltet die unidirektionale Kommunikation des Hosts mit den virtuellen Computern. Der Algorithmus für die VM-Anordnung des Azure Fabric Controller ist sehr komplex, und es ist nahezu unmöglich, ihn auf physischer Hostebene vorauszusagen.

![Azure Fabric Controller](./media/isolation-choices/azure-isolation-fig5.png)

Der Azure-Hypervisor erzwingt eine Arbeitsspeicher- und Prozesstrennung zwischen virtuellen Computern und leitet Netzwerkdatenverkehr sicher an die Gastbetriebssystem-Mandanten weiter. Somit ist es nicht mehr möglich, auf VM-Ebene einen Seitenkanalangriff durchzuführen.

In Azure nimmt die Stamm-VM eine besondere Stellung ein: Auf dieser VM wird ein spezielles Betriebssystem (das Stammbetriebssystem) ausgeführt, unter dem ein Fabric-Agent (FA) gehostet wird. FAs werden wiederum zum Verwalten von Gast-Agents (GAs) unter Gastbetriebssystemen auf Kunden-VMs genutzt. FAs dienen außerdem zum Verwalten von Speicherknoten.

Ein Computeknoten besteht aus den folgenden Komponenten: Azure Hypervisor, Stammbetriebssystem/FA und Kunden-VMs/-GAs. FAs werden mit einem Fabric Controller (FC) verwaltet, der außerhalb von Compute- und Speicherknoten angeordnet ist (Compute- und Speichercluster werden von separaten FCs verwaltet). Wenn ein Kunde die Konfigurationsdatei seiner Anwendung während der Ausführung aktualisiert, kommuniziert der FC mit dem FA. Dieser nimmt dann Kontakt mit den GAs auf, die wiederum die Anwendung über die Konfigurationsänderung informieren. Bei einem Hardwarefehler ermittelt der FC automatisch verfügbare Hardware und startet die VM darauf neu.

![Azure Fabric Controller](./media/isolation-choices/azure-isolation-fig6.jpg)

Die Kommunikation von einem Fabric Controller zu einem Agent ist unidirektional. Der Agent implementiert einen per SSL geschützten Dienst, der nur auf Anforderungen vom Controller antwortet. Das Initiieren von Verbindungen mit dem Controller oder anderen privilegierten internen Knoten ist nicht möglich. Der FC behandelt alle Antworten als nicht vertrauenswürdig.

![Fabric Controller](./media/isolation-choices/azure-isolation-fig7.png)

Die Isolation trennt die Stamm-VM von Gast-VMs und die Gast-VMs voneinander. Computeknoten sind außerdem gegenüber Speicherknoten isoliert, um den Schutz zu erhöhen.

Der Hypervisor und das Hostbetriebssystem verfügen über Netzwerkpaketfilter. Hiermit wird sichergestellt, dass nicht vertrauenswürdige virtuelle Computer keinen gefälschten Datenverkehr generieren und keinen nicht für sie bestimmten Datenverkehr empfangen, Datenverkehr an geschützte Infrastrukturendpunkte leiten oder unerwünschten Broadcastdatenverkehr senden bzw. empfangen.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Zusätzliche Regeln des Fabric Controller-Agents zum Isolieren von VMs

Standardmäßig wird beim Erstellen eines virtuellen Computers sämtlicher Datenverkehr blockiert, und der Fabric Controller-Agent konfiguriert anschließend den Paketfilter, um Regeln und Ausnahmen für das Zulassen des autorisierten Datenverkehrs hinzuzufügen.

Es gibt zwei Kategorien von Regeln, die programmiert werden:

- **Computerkonfigurations- oder Infrastrukturregeln:** Standardmäßig ist die gesamte Kommunikation blockiert. Es gibt Ausnahmen, um einem virtuellen Computer das Senden und Empfangen von DHCP- und DNS-Datenverkehr zu ermöglichen. Virtuelle Computer können auch Datenverkehr an das „öffentliche“ Internet und an andere virtuelle Computer in demselben virtuellen Azure-Netzwerk und auf dem Betriebssystem-Aktivierungsserver senden. Die Liste mit zulässigen ausgehenden Zielen der virtuellen Computer enthält keine Azure-Routersubnetze, keine Azure-Verwaltung und keine anderen Microsoft-Komponenten.
- **Rollenkonfigurationsdatei:** Diese definiert die eingehenden Zugriffssteuerungslisten (ACLs) auf der Grundlage des Dienstmodells des Mandanten.

### <a name="vlan-isolation"></a>VLAN-Isolation

Jeder Cluster enthält drei VLANs:

![VLAN-Isolation](./media/isolation-choices/azure-isolation-fig8.jpg)

- Haupt-VLAN: Dient zum Verbinden von nicht vertrauenswürdigen Kundenknoten.
- FC-VLAN: Enthält vertrauenswürdige FCs und unterstützende Systeme.
- Geräte-VLAN: Enthält vertrauenswürdige Netzwerkgeräte und andere Infrastrukturgeräte.

Die Kommunikation ist vom FC-VLAN zum Haupt-VLAN zulässig, kann aber nicht vom Haupt-VLAN zum FC-VLAN initiiert werden. Auch die Kommunikation vom Haupt-VLAN zum Geräte-VLAN ist blockiert. So wird sichergestellt, dass auch bei einer Kompromittierung eines Knotens, auf dem Kundencode ausgeführt wird, kein Angriff auf Knoten in FC- oder Geräte-VLANs erfolgen kann.

## <a name="storage-isolation"></a>Speicherisolation

### <a name="logical-isolation-between-compute-and-storage"></a>Logische Isolation zwischen Compute- und Speicherbereich

Im Rahmen des grundlegenden Designs von Microsoft Azure werden die VM-basierten Computevorgänge von den Speichervorgängen getrennt. Diese Trennung ermöglicht eine unabhängige Skalierung der Computevorgänge und Speichervorgänge, sodass die Umsetzung der Mehrinstanzenfähigkeit und Isolation vereinfacht wird.

Aus diesem Grund wird Azure Storage auf separater Hardware ohne Netzwerkverbindung mit Azure Compute (außer auf logischer Ebene) ausgeführt. Dies bedeutet, dass Speicherplatz bei der Erstellung eines virtuellen Datenträgers nicht für die gesamte Kapazität zugeordnet wird. Stattdessen wird eine Tabelle erstellt, in der Adressen des virtuellen Datenträgers Bereichen auf dem physischen Datenträger zugeordnet werden. Diese Tabelle ist anfänglich leer. **Wenn ein Kunde zum ersten Mal Daten auf den virtuellen Datenträger schreibt, wird Speicherplatz auf dem physischen Datenträger zugeordnet, und in die Tabelle wird ein Verweis darauf eingefügt.**

### <a name="isolation-using-storage-access-control"></a>Isolation mithilfe der Storage-Zugriffssteuerung

Für die **Zugriffssteuerung in Azure Storage** gilt ein einfaches Zugriffssteuerungsmodell. Für jedes Azure-Abonnement kann mindestens ein Speicherkonto erstellt werden. Jedes Speicherkonto verfügt über einen geheimen Schlüssel, der zum Steuern des Zugriffs auf alle Daten des Speicherkontos dient.

![Isolation mithilfe der Storage-Zugriffssteuerung](./media/isolation-choices/azure-isolation-fig9.png)

Der **Zugriff auf Azure Storage-Daten (einschließlich Tabellen)** kann per [SAS](../../storage/common/storage-sas-overview.md)-Token (Shared Access Signature) gesteuert werden. Mit dem Token wird der bereichsbezogene Zugriff gewährt. Die SAS wird mit einer Abfragevorlage (URL) erstellt, die per [SAK](/previous-versions/azure/reference/ee460785(v=azure.100)) (Storage Account Key, Speicherkontoschlüssel) signiert wird. Diese [signierte URL](../../storage/common/storage-sas-overview.md) kann an einen anderen Prozess übergeben (delegiert) werden, mit dem dann die Details der Abfrage eingefügt werden können und der Speicherdienst angefordert werden kann. Mit einer SAS können Sie zeitabhängigen Zugriff auf Clients gewähren, ohne den geheimen Schlüssel des Speicherkontos offenzulegen.

Eine SAS bietet die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte im Speicherkonto zu erteilen. Hierfür müssen Sie nicht Ihre Kontozugriffsschlüssel freigeben.

### <a name="ip-level-storage-isolation"></a>Isolation der Speicherung auf IP-Ebene

Sie können Firewalls einrichten und einen IP-Adressbereich für Ihre vertrauenswürdigen Clients definieren. Mit einem IP-Adressbereich können nur Clients, die über eine IP-Adresse innerhalb des definierten Bereichs verfügen, eine Verbindung mit [Azure Storage](../../storage/blobs/security-recommendations.md) herstellen.

IP-Speicherdaten können vor unbefugten Benutzern mit einem Netzwerkmechanismus geschützt werden, der zum Zuordnen eines dedizierten Tunnels für Datenverkehr an den IP-Speicher verwendet wird.

### <a name="encryption"></a>Verschlüsselung

Azure verfügt über die folgenden Arten von Verschlüsselung zum Schutz von Daten:

- Verschlüsselung während der Übertragung
- Verschlüsselung ruhender Daten

#### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

Verschlüsselung während der Übertragung ist ein Mechanismus zum Schutz der Daten bei der Übertragung über Netzwerke hinweg. Mit Azure Storage können Sie Daten mit folgenden Verfahren schützen:

- [Verschlüsselung auf Transportebene](../../storage/blobs/security-recommendations.md)(etwa HTTPS), wenn Sie Daten in oder aus Azure Storage übertragen.
- [Wire-Verschlüsselung](../../storage/blobs/security-recommendations.md) (etwa SMB 3.0-Verschlüsselung für Azure-Dateifreigaben).
- [Clientseitiger Verschlüsselung](../../storage/blobs/security-recommendations.md), um die Daten zu verschlüsseln, bevor sie in den Speicher übertragen werden, und nach der Übertragung aus dem Speicher zu entschlüsseln.

#### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Für viele Organisationen ist die Verschlüsselung von [ruhenden Daten](isolation-choices.md) ein obligatorischer Schritt in Richtung Datenschutz, Compliance und Datenhoheit. Drei Azure-Features ermöglichen die Verschlüsselung „ruhender“ Daten:

- [Storage Service Encryption](../../storage/blobs/security-recommendations.md) können Sie anfordern, dass der Speicherdienst die Daten beim Schreiben in Azure Storage automatisch verschlüsselt.
- [Client-side Encryption](../../storage/blobs/security-recommendations.md) ermöglicht ebenfalls eine Verschlüsselung ruhender Daten.
- [Azure-Datenträgerverschlüsselung](./azure-disk-encryption-vms-vmss.md) können Sie die Betriebssystemdatenträger und andere Datenträger verschlüsseln, die von einem virtuellen IaaS-Computer verwendet werden.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Mithilfe von [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) für virtuelle Computer können Sie die Sicherheits- und Konformitätsanforderungen Ihrer Organisation erfüllen, indem Sie die Datenträger Ihrer virtuellen Computer (Startdatenträger und allgemeine Datenträger) mit Schlüsseln und Richtlinien verschlüsseln, die Sie über [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) steuern.

Die Disk Encryption-Lösung für Windows basiert auf der [Microsoft BitLocker-Laufwerkverschlüsselung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)), die Linux-Lösung auf [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Die Lösung unterstützt die folgenden Szenarien für virtuelle IaaS-Computer, wenn sie in Microsoft Azure aktiviert sind:

- Integration in Azure Key Vault
- Virtuelle Computer im Standard-Tarif: Virtuelle IaaS-Computer der Serien A, D, DS, G, GS usw.
- Aktivieren der Verschlüsselung auf virtuellen Windows- und Linux-IaaS-Computern
- Deaktivieren der Verschlüsselung auf Betriebssystem- und Datenlaufwerken für virtuelle Windows-IaaS-Computer
- Deaktivieren der Verschlüsselung auf Datenlaufwerken für virtuelle Linux-IaaS-Computer
- Aktivieren der Verschlüsselung auf virtuellen IaaS-Computern mit dem Windows-Clientbetriebssystem
- Aktivieren der Verschlüsselung auf Volumes mit Bereitstellungspfaden
- Aktivieren der Verschlüsselung auf virtuellen Linux-Computern, die mithilfe von [mdadm](https://en.wikipedia.org/wiki/Mdadm) mit Datenträgerstriping (RAID) konfiguriert sind
- Aktivieren der Verschlüsselung auf virtuellen Linux-Computern mit [LVM (Logical Volume Manager)](/windows/win32/fileio/about-volume-management) für Datenträger
- Aktivieren der Verschlüsselung auf virtuellen Windows-Computern, die mithilfe von Speicherplätzen konfiguriert sind
- Alle öffentlichen Azure-Regionen werden unterstützt.

Die Lösung unterstützt nicht die folgenden Szenarien, Features und Technologien:

- IaaS-VMs des Basic-Tarifs
- Deaktivieren der Verschlüsselung auf Betriebssystemlaufwerken für virtuelle Linux-IaaS-Computer
- Virtuelle IaaS-Computer, die mithilfe der klassischen Methode zum Erstellen von virtuellen Computern erstellt werden
- Integration in den lokalen Schlüsselverwaltungsdienst
- Azure Files (freigegebenes Dateisystem), Network File System (NFS), dynamische Volumes und virtuelle Windows-Computer, die mit softwarebasierten RAID-Systemen konfiguriert sind

## <a name="sql-database-isolation"></a>SQL-Datenbank-Isolation

SQL-Datenbank ist ein relationaler Datenbankdienst in der Microsoft Cloud, der auf der marktführenden Microsoft SQL Server-Engine basiert und unternehmenswichtige Workloads verarbeiten kann. Mit der SQL-Datenbank ist eine vorhersagbare Isolation von Daten auf Kontoebene, nach Geografie/Region und nach Netzwerk möglich – nahezu ohne Verwaltungsaufwand.

### <a name="sql-database-application-model"></a>SQL-Datenbank-Anwendungsmodell

[Microsoft SQL-Datenbank](../../azure-sql/database/single-database-create-quickstart.md) ist ein cloudbasierter relationaler Datenbankdienst, der auf SQL Server-Technologien basiert. Es wird ein hoch verfügbarer, skalierbarer, mehrinstanzenfähiger Datenbankdienst bereitgestellt, der von Microsoft in der Cloud gehostet wird.

Aus Anwendungssicht verfügt SQL-Datenbank über die folgende Hierarchie: Für jede Ebene gilt in Bezug auf die untergeordneten Ebenen eine 1:n-Einschlussbeziehung.

![SQL-Datenbank-Anwendungsmodell](./media/isolation-choices/azure-isolation-fig10.png)

Konto und Abonnement sind Konzepte der Microsoft Azure-Plattform für die Abrechnung und Verwaltung.

Logische SQL-Server und -Datenbanken sind auf SQL-Datenbank basierende Konzepte, die mit SQL-Datenbank, bereitgestellten OData- und TSQL-Schnittstellen oder über das Azure-Portal verwaltet werden.

Server in SQL-Datenbank sind keine physischen Instanzen oder VM-Instanzen, sondern Sammlungen mit Datenbanken, für die Verwaltungs- und Sicherheitsrichtlinien gemeinsam genutzt werden, die in so genannten „logischen Masterdatenbanken“ gespeichert sind.

![SQL-Datenbank](./media/isolation-choices/azure-isolation-fig11.png)

Logische Masterdatenbanken umfassen Folgendes:

- SQL-Anmeldungen zum Herstellen der Verbindung mit dem Server
- Firewallregeln

Für abrechnungs- und nutzungsbezogene Informationen für Datenbanken desselben Servers ist nicht garantiert, dass sie sich im Cluster auf derselben physischen Instanz befinden. Stattdessen muss für Anwendungen beim Herstellen der Verbindung der Name der Zieldatenbank angegeben werden.

Aus Kundensicht wird ein Server in einer geografischen Region erstellt, während die tatsächliche Erstellung des Servers in einem der Cluster der Region durchgeführt wird.

### <a name="isolation-through-network-topology"></a>Isolation per Netzwerktopologie

Wenn ein Server erstellt und sein DNS-Name registriert wird, verweist der DNS-Name auf die so genannte „Gateway-VIP“-Adresse im jeweiligen Rechenzentrum, in dem der Server angeordnet wurde.

Hinter der VIP (virtuelle IP-Adresse) befindet sich eine Sammlung von zustandslosen Gatewaydiensten. Im Allgemeinen werden Gateways verwendet, wenn eine Koordination zwischen mehreren Datenquellen (Masterdatenbank, Benutzerdatenbank usw.) erforderlich ist. Mit Gatewaydiensten wird Folgendes implementiert:

- **Proxyfunktion für TDS-Verbindung:** Dieser Vorgang umfasst das Ermitteln der Benutzerdatenbank im Back-End-Cluster, das Implementieren der Anmeldesequenz und das anschließende Weiterleiten der TDS-Pakete an das Back-End (und wieder zurück).
- **Datenbankverwaltung:** Umfasst das Implementieren einer Sammlung von Workflows zur Durchführung von CREATE/ALTER/DROP-Datenbankvorgängen. Die Datenbankvorgänge können aufgerufen werden, indem per „Sniffing“ entweder TDS-Pakete oder explizite OData-APIs ermittelt werden.
- CREATE/ALTER/DROP – Anmelde-/Benutzervorgänge
- Serververwaltungsvorgänge per OData-API

![Isolation per Netzwerktopologie](./media/isolation-choices/azure-isolation-fig12.png)

Die Ebene hinter den Gateways wird als „Back-End“ bezeichnet. Hier werden alle Daten mit hoher Verfügbarkeit gespeichert. Jedes Datenelement gehört einer „Partition“ oder „Failovereinheit“ an, die jeweils über mindestens drei Replikate verfügt. Replikate werden mit der SQL Server-Engine gespeichert und repliziert und von einem Failoversystem verwaltet, das häufig als „Fabric“ bezeichnet wird.

Normalerweise kommuniziert das Back-End-System aus Sicherheitsgründen nicht in ausgehender Richtung mit anderen Systemen. Dies ist auf die Systeme der Front-End-Ebene (Gateway) beschränkt. Die Computer der Gatewayebene verfügen auf den Back-End-Computern über eingeschränkte Rechte, um die Angriffsfläche zu verkleinern und auch auf dieser Ebene Verteidigungsmaßnahmen zu ergreifen.

### <a name="isolation-by-machine-function-and-access"></a>Isolation per Computerfunktion und Zugriff

SQL-Datenbank besteht aus Diensten, die unter verschiedenen Computerfunktionen ausgeführt werden. SQL-Datenbank ist in eine „Back-End“-Clouddatenbank und „Front-End“-Umgebungen (Gateway/Verwaltung) unterteilt, wobei das allgemeine Prinzip gilt, dass Datenverkehr nur an das Back-End fließt (und nicht in umgekehrter Richtung). Die Front-End-Umgebung kann mit der „Außenwelt“ anderer Dienste kommunizieren und verfügt normalerweise nur über eingeschränkte Rechte für das Back-End (ausreichende Rechte zum Aufrufen der erforderlichen Einstiegspunkte).

## <a name="networking-isolation"></a>Netzwerkisolation

Eine Azure-Bereitstellung umfasst mehrere Stufen der Netzwerkisolation. Das folgende Diagramm zeigt verschiedene Stufen der Netzwerkisolation, die Azure den Kunden bietet. Bei diesen Stufen handelt es sich sowohl um nativ in der Azure Platform enthaltene Funktionen als auch um benutzerdefinierte Features. Als erste Grenze gegenüber dem Internet ermöglicht Azure DDoS eine Isolation vor groß angelegten Angriffen gegen Azure. Die nächste Stufe der Isolation sind vom Kunden definierte öffentliche IP-Adressen (Endpunkte), mit denen ermittelt wird, welcher Datenverkehr den Clouddienst zum virtuellen Netzwerk passieren darf. Die native Isolation virtueller Azure-Netzwerke stellt eine vollständige Isolierung von allen anderen Netzwerken sicher und gewährleistet, dass der Datenverkehr nur über vom Benutzer konfigurierte Pfade und Methoden fließt. Diese Pfade und Methoden stellen die nächste Stufe dar, auf der NSGs, UDR und virtuelle Netzwerkgeräte zum Einrichten von Isolationsgrenzen verwendet werden können, um die Anwendungsbereitstellungen im geschützten Netzwerk abzusichern.

![Netzwerkisolation](./media/isolation-choices/azure-isolation-fig13.png)

**Isolation des Datenverkehrs:** Für die Azure-Plattform werden [virtuelle Netzwerke](../../virtual-network/virtual-networks-overview.md) verwendet, um Datenverkehr zu isolieren. Virtuelle Computer in einem virtuellen Netzwerk können nicht direkt mit virtuellen Computern in einem anderen virtuellen Netzwerk kommunizieren – selbst dann nicht, wenn beide virtuellen Netzwerke durch denselben Kunden erstellt werden. Isolation ist eine wichtige Eigenschaft, mit der sichergestellt wird, dass VMs und die Kommunikation von Kunden innerhalb eines virtuellen Netzwerks privat bleiben.

[Subnetze](../../virtual-network/virtual-networks-overview.md) ermöglichen eine zusätzliche Isolationsstufe in virtuellen Netzwerken basierend auf dem IP-Bereich. Mit IP-Adressen im virtuellen Netzwerk können Sie ein virtuelles Netzwerk zu Organisations- und Sicherheitszwecken in mehrere Subnetze unterteilen. VMs und PaaS-Rolleninstanzen, die in (denselben oder unterschiedlichen) Subnetzen in einem VNet bereitgestellt werden, können ohne zusätzliche Konfiguration miteinander kommunizieren. Außerdem können Sie [Netzwerksicherheitsgruppen (NSGs)](../../virtual-network/virtual-networks-overview.md) konfigurieren, um Netzwerkdatenverkehr für eine VM-Instanz anhand von Regeln zuzulassen oder abzulehnen, die in der Zugriffssteuerungsliste (Access Control List, ACL) einer NSG konfiguriert sind. NSGs können Subnetzen oder einzelnen VM-Instanzen innerhalb dieses Subnetzes zugeordnet werden. Wenn eine NSG einem Subnetz zugeordnet ist, gelten die ACL-Regeln für alle VM-Instanzen in diesem Subnetz.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Optionen der Netzwerkisolation für Computer in virtuellen Windows Azure-Netzwerken](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Dies schließt auch das klassische Front-End- und Back-End-Szenario ein, bei dem Computer in einem bestimmten Back-End-Netzwerk oder Unternetzwerk basierend auf einer Zulassungsliste mit IP-Adressen nur für bestimmte Clients oder andere Computer das Herstellen einer Verbindung mit einem bestimmten Endpunkt zulassen.

- Erfahren Sie mehr über die [Isolation von virtuellen Computern in Azure](../../virtual-machines/isolation.md). Azure Compute bietet VM-Größen, die für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt sind.