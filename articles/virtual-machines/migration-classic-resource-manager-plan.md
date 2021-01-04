---
title: Planen der Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager
description: In diesem Artikel erfahren Sie mehr zum Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager unter Linux.
services: virtual-machines-linux
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 271a20c5c9b6e157343246afb79f40894fe8a097
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94904627"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager-in-linux"></a>Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager unter Linux

> [!IMPORTANT]
> Derzeit nutzen etwa 90 % der IaaS-VMs [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Seit dem 28. Februar 2020 gelten klassische VMs als veraltet. Sie werden am 1. März 2023 vollständig eingestellt. [Erfahren Sie mehr]( https://aka.ms/classicvmretirement) zu dieser Einstellung und den [Auswirkungen auf Sie](classic-vm-deprecation.md#how-does-this-affect-me).

Azure Resource Manager bietet zwar zahlreiche praktische Features, die Migration muss jedoch sorgfältig geplant werden, damit alles reibungslos funktioniert. Eine gründliche Planung gewährleistet, dass beim Ausführen der Migrationsaktivitäten keine Probleme auftreten.

> [!NOTE]
> An der folgenden Anleitung haben das Azure Customer Advisory Team und die Cloudlösungsarchitekten maßgeblich mitgewirkt, die Kunden bei der Migration umfangreicher Umgebungen zu unterstützen. Das Dokument wird immer wieder mit neuen Erfolgsmustern aktualisiert. Prüfen Sie daher von Zeit zu Zeit, ob neue Empfehlungen verfügbar sind.

Die Migration lässt sich in vier allgemeine Phasen unterteilen:

![Migrationsphasen](./media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planen

### <a name="technical-considerations-and-tradeoffs"></a>Technische Aspekte und Kompromisse

Abhängig von technischen Anforderungen, Größe, Geografie und Betriebsverfahren sind ggf. folgende Aspekte zu berücksichtigen:

1. Warum soll in Ihrer Organisation Azure Resource Manager verwendet werden?  Was sind die geschäftlichen Gründe für eine Migration?
2. Was sind die technischen Gründe für Azure Resource Manager?  Welche anderen Azure-Dienste möchten Sie nutzen (sofern zutreffend)?
3. Welche Anwendung ist (bzw. welche VM-Gruppen) sind Teil der Migration?
4. Welche Szenarien werden mit der Migrations-API unterstützt?  Informationen zu nicht unterstützten Features und Konfigurationen finden Sie [hier](migration-classic-resource-manager-overview.md).
5. Unterstützen Ihre Betriebsteams nun sowohl Anwendungen/virtuelle Computer unter dem klassischen Bereitstellungsmodell als auch Anwendungen/virtuelle Computer unter dem Azure Resource Manager-Bereitstellungsmodell?
6. Welche Auswirkungen hat Azure Resource Manager auf Ihre Bereitstellungs-, Verwaltungs-, Überwachungs- und Berichterstellungsprozesse für virtuelle Computer?  Müssen Ihre Bereitstellungsskripts aktualisiert werden?
7. Wie sieht der Kommunikationsplan zur Benachrichtigung der Stakeholder (Endbenutzer, Anwendungsbesitzer und Infrastrukturbesitzer) aus?
8. Ist ein Wartungszeitraum erforderlich, in dem die Anwendung für Endbenutzer und Anwendungsbesitzer nicht verfügbar ist (abhängig von der Komplexität der Umgebung)?  Falls ja: Wie lang?
9. Welche Trainingsmaßnahmen sind geplant, um zu gewährleisten, dass die Stakeholder mit Azure Resource Manager vertraut sind und die Lösung optimal verwenden können?
10. Welche Programm- oder Projektverwaltung ist für die Migration geplant?
11. Welche Zeitpläne sind für die Azure Resource Manager-Migration und für verwandte Technologieroadmaps vorgesehen?  Sind sie optimal aufeinander abgestimmt?

### <a name="patterns-of-success"></a>Erfolgsmuster

Erfolgreiche Kunden verfügen über sorgfältig ausgearbeitete Pläne, in denen die oben genannten Fragen erläutert, dokumentiert und geregelt werden.  Stellen Sie sicher, dass Sponsoren und Stakeholder mit den Migrationsplänen vertraut sind.  Informieren Sie sich über Ihre Migrationsoptionen. Dazu empfiehlt es sich, die folgenden Dokumente zu lesen:

* [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell](migration-classic-resource-manager-ps.md)
* [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Befehlszeilenschnittstelle](migration-classic-resource-manager-cli.md)
* [Communitytools zur Unterstützung beim Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Überprüfen der häufigsten Fehler bei der Migration](migration-classic-resource-manager-errors.md)
* [Antworten auf die am häufigsten gestellten Fragen zum Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-faq.md)

### <a name="pitfalls-to-avoid"></a>Vermeidbare Fehler

- Mangelnde Planung.  Die technologischen Migrationsschritte sind erprobt, und das Ergebnis ist vorhersagbar.
- Annahme, dass die von der Plattform unterstützte Migrations-API für sämtliche Szenarien geeignet ist. Informieren Sie sich über [nicht unterstützte Features und Konfigurationen](migration-classic-resource-manager-overview.md), um zu verstehen, welche Szenarien unterstützt werden.
- Keine Planung für den möglichen Ausfall der Anwendung für Endbenutzer.  Planen Sie einen angemessenen Puffer ein, um Endbenutzer angemessen darüber zu informieren, dass die Anwendung möglicherweise eine Weile nicht verfügbar ist.


## <a name="lab-test"></a>Lab-Test

**Replizieren Ihrer Umgebung und Ausführen einer Testmigration**
  > [!NOTE]
  > Die Erstellung eines exakten Replikats Ihrer vorhandenen Umgebung erfolgt mithilfe eines von der Community bereitgestellten Tools, für das Microsoft offiziell keinen Support bietet. Der Schritt ist daher **optional**, stellt aber die beste Möglichkeit dar, um ohne Auswirkungen auf Ihre Produktionsumgebungen Probleme zu ermitteln. Falls die Verwendung eines von der Community bereitgestellten Tools für Sie keine Option ist, lesen Sie weiter unten die Empfehlung „Probelauf mit Überprüfung/Vorbereitung/Abbruch“.
  >

  Mithilfe eines Lab-Tests Ihres exakten Szenarios (Compute, Netzwerk und Speicher) lässt sich am besten eine reibungslose Migration gewährleisten. Vorteile:

- Eine vollständig separate Lab-Umgebung oder eine vorhandene produktionsfremde Umgebung zu Testzwecken. Wir empfehlen ein vollständig separates Lab, das wiederholt migriert und destruktiv geändert werden kann.  Skripts zum Erfassen/Aktualisieren von Metadaten aus den echten Abonnements finden Sie weiter unten.
- Es empfiehlt sich, das Lab in einem separaten Abonnement zu erstellen. Der Grund: Das Lab wird wiederholt vernichtet, und die Verwendung eines separaten, isolierten Abonnements macht es unwahrscheinlicher, dass versehentlich echte Inhalte gelöscht werden.

  Hierzu kann das AsmMetadataParser-Tool verwendet werden. Weitere Informationen zu diesem Tool finden Sie [hier](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset).

### <a name="patterns-of-success"></a>Erfolgsmuster

Die folgenden Probleme wurden in vielen größeren Migrationen festgestellt. Hierbei handelt es sich nicht um eine vollständige Liste. Ausführlichere Informationen finden Sie in den [nicht unterstützten Features und Konfigurationen](migration-classic-resource-manager-overview.md). Es empfiehlt sich, diese technischen Probleme (sofern Sie denn bei Ihnen auftreten) vor der Migration zu beseitigen, um einen möglichst reibungslosen Ablauf zu gewährleisten.

- **Probelauf mit Überprüfung/Vorbereitung/Abbruch:** Der vielleicht wichtigste Schritt für eine erfolgreiche Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager. Die Migrations-API umfasst drei Hauptschritte: Überprüfung, Vorbereitung und Commit. Bei der Überprüfung wird der Zustand Ihrer klassischen Umgebung gelesen und ein Ergebnis mit allen Problemen zurückzugeben. Da einige Probleme jedoch möglicherweise im Azure Resource Manager-Stapel vorliegen, werden bei der Überprüfung nicht alle Probleme erkannt. Diese Probleme werden im Rahmen der Vorbereitung (dem nächsten Schritt des Migrationsprozesses) ermittelt. Bei der Vorbereitung werden die Metadaten aus dem klassischen Bereitstellungsmodell in Azure Resource Manager migriert, ohne jedoch ein Commit für die Datenbewegung auszuführen. Auf der Seite des klassischen Bereitstellungsmodells wird nichts entfernt oder verändert. Im Rahmen des Probelaufs wird die Migration vorbereitet und die Migrationsvorbereitung anschließend abgebrochen (**kein Commit ausgeführt**). Der Probelauf mit Überprüfung/Vorbereitung/Abbruch dient dazu, alle Metadaten des Azure Resource Manager-Stapels anzuzeigen und zu untersuchen (*programmgesteuert oder im Portal*), sich zu vergewissern, dass alles ordnungsgemäß migriert wird, und ggf. technische Probleme zu beheben.  Außerdem können Sie so die Migrationsdauer besser einschätzen und Ausfallzeiten entsprechend planen.  Das Verfahren mit Überprüfung/Vorbereitung/Abbruch verursacht keinerlei Ausfälle für Benutzer, und die Anwendungsnutzung wird nicht beeinträchtigt.
  - Die im Anschluss aufgeführten Punkte müssen vor dem Probelauf behandelt werden. Bei einem Probelauf werden Sie aber auch zuverlässig auf diese Vorbereitungsschritte hingewiesen, falls noch Handlungsbedarf besteht. Bei der Enterprise-Migration hat sich der Probelauf als sichere und praktische Methode zur Gewährleistung der Migrationsbereitschaft erwiesen.
  - Während der Vorbereitungsphase ist die Steuerungsebene (Azure-Verwaltungsvorgänge) für das gesamte virtuelle Netzwerk gesperrt, sodass während des Überprüfungs-/Vorbereitungs-/Abbruchsvorgangs keine Änderungen an den VM-Metadaten vorgenommen werden können.  Die Anwendungsfunktionen (RD, VM-Verwendung usw.) sind davon jedoch nicht betroffen.  Die Benutzer der virtuellen Computer werden den Probelauf nicht bemerken.

- **ExpressRoute-Leitungen und VPN:** ExpressRoute-Gateways mit Autorisierungslinks können derzeit nicht ohne Ausfallzeit migriert werden. Informationen zur Umgehung dieses Problems finden Sie unter [Migrieren von ExpressRoute-Verbindungen und zugeordneten virtuellen Netzwerken vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell](../expressroute/expressroute-migration-classic-resource-manager.md).

- **VM-Erweiterungen:** Erweiterungen für virtuelle Computer können eines der größten Hindernisse für die Migration ausgeführter virtueller Computer darstellen. Korrekturmaßnahmen für VM-Erweiterungen können bis zu zwei Tage in Anspruch nehmen. Berücksichtigen Sie dies bei der Planung.  Zur Meldung des Status der VM-Erweiterungen von ausgeführten virtuellen Computern wird ein funktionierender Azure-Agent benötigt. Wenn für einen ausgeführten virtuellen Computer ein Fehlerstatus zurückgegeben wird, wird die Migration angehalten. Der Agent selbst muss sich für die Migration nicht in einem funktionsfähigen Zustand befinden, aber wenn auf dem virtuellen Computer Erweiterungen vorhanden sind, kann die Migration nur fortgesetzt werden, wenn ein funktionierender Agent und eine ausgehende Internetverbindung (mit DNS) verfügbar sind.
  - Wenn die Konnektivität mit einem DNS-Server während der Migration unterbrochen wird, müssen alle VM-Erweiterungen bis auf BGInfo v1\* zunächst von jeder VM entfernt werden, bevor die Migration vorbereitet wird, und nach der Azure Resource Manager-Migration wieder zu der VM hinzugefügt werden.  **Dies gilt nur für ausgeführte virtuelle Computer.**  Bei virtuellen Computern mit dem Status „Beendet (Zuordnung aufgehoben)“ müssen VM-Erweiterungen nicht entfernt werden. **Hinweis:** Viele Erweiterungen wie Azure-Diagnose und Security Center-Überwachung werden nach der Migration automatisch neu installiert und können daher problemlos entfernt werden.
  - Vergewissern Sie sich zudem, dass ausgehender Internetzugriff nicht durch Netzwerksicherheitsgruppen (NSGs) eingeschränkt wird. Das kann bei bestimmten NSG-Konfigurationen der Fall sein. Ausgehender Internetzugriff (und DNS) ist für die Migration von VM-Erweiterungen zu Azure Resource Manager erforderlich.
  - Von der BGInfo-Erweiterung gibt es zwei Versionen: v1 und v2.  Wenn der virtuelle Computer über das Azure-Portal oder mithilfe von PowerShell erstellt wurde, verfügt er wahrscheinlich über die v1-Version der Erweiterung. Diese Erweiterung muss nicht entfernt werden und wird von der Migrations-API übersprungen (nicht migriert). Wenn der klassische virtuelle Computer hingegen über das neue Azure-Portal erstellt wurde, verfügt er wahrscheinlich über die JSON-basierte v2-Version von BGInfo. Diese Version kann zu Azure Resource Manager migriert werden. Dazu muss der Agent jedoch funktionieren und über ausgehenden Internetzugriff (und DNS) verfügen.
  - **Korrekturoption 1:** Wenn Sie wissen, dass Ihre virtuellen Computer nicht über ausgehenden Internetzugriff verfügen, kein funktionierender DNS-Dienst verfügbar ist und auf den virtuellen Computern keine funktionierenden Azure-Agents vorhanden sind, deinstallieren Sie alle VM-Erweiterungen im Rahmen der Migration vor der Vorbereitungsphase, und installieren Sie sie nach der Migration neu.
  - **Korrekturoption 2:** Falls sich die VM-Erweiterungen als zu große Hürde erweisen, können Sie alle virtuellen Computer vor der Migration herunterfahren bzw. freigeben. Migrieren Sie die virtuellen Computer mit aufgehobener Zuordnung, und starten Sie sie anschließend auf der Azure Resource Manager-Seite wieder. Der Vorteil: Die VM-Erweiterungen werden migriert. Der Nachteil: Alle öffentlich zugänglichen virtuellen IP-Adressen gehen verloren (was unter Umständen den Start verhindert), und die virtuellen Computer werden heruntergefahren, wodurch aktive Anwendungen natürlich erheblich stärker beeinträchtigt werden.

    > [!NOTE]
    > Falls für die ausgeführten virtuellen Computer, die migriert werden, eine Azure Security Center-Richtlinie konfiguriert ist, muss die Sicherheitsrichtlinie vor dem Entfernen von Erweiterungen beendet werden. Andernfalls wird die Sicherheitsüberwachungserweiterung automatisch wieder auf dem virtuellen Computer installiert, nachdem sie entfernt wurde.

- **Verfügbarkeitsgruppen:** Damit ein virtuelles Netzwerk (VNET) zu Azure Resource Manager migriert werden kann, müssen sich die im klassischen Bereitstellungsmodell (Clouddienst) enthaltenen virtuellen Computer entweder alle in der gleichen Verfügbarkeitsgruppe befinden, oder keiner der virtuellen Computer darf einer Verfügbarkeitsgruppe angehören. Die Verwendung mehrerer Verfügbarkeitsgruppen im Clouddienst ist mit Azure Resource Manager nicht kompatibel und führt dazu, dass die Migration angehalten wird.  Darüber hinaus dürfen sich nicht einige virtuelle Computer in einer Verfügbarkeitsgruppe befinden und andere nicht. Zur Behebung dieses Problems müssen Sie Ihren Clouddienst korrigieren oder umgestalten.  Dies kann sehr zeitaufwendig sein. Planen Sie daher genügend Zeit ein.

- **Web-/Workerrollenbereitstellungen:** Cloud Services mit Web- und Workerrollen können nicht zu Azure Resource Manager migriert werden. Die Web-/Workerrollen müssen vor Beginn der Migration aus dem virtuellen Netzwerk entfernt werden.  Dazu können die Instanzen der Web-/Workerrollen in ein separates klassisches virtuelles Netzwerk verschoben werden, das ebenfalls mit einer ExpressRoute-Leitung verknüpft ist. Alternativ können Sie den Code zu neueren PaaS-App Services migrieren. (Diese Diskussion ist jedoch nicht Gegenstand dieses Dokuments.) Erstellen Sie im ersteren Fall ein neues klassisches virtuelles Netzwerk, verschieben Sie die Web-/Workerrollen in dieses neue virtuelle Netzwerk (oder stellen Sie sie dort neu bereit), und löschen Sie anschließend die Bereitstellungen aus dem zu verschiebenden virtuellen Netzwerk. Codeänderungen sind nicht erforderlich. Mit dem neuen [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md) können Sie das klassische virtuelle Netzwerk, das die Web-/Workerrollen enthält, mit anderen virtuellen Netzwerken in der gleichen Azure-Region – beispielsweise das zu migrierende virtuelle Netzwerk – zusammenfassen (**nach Abschluss der Migration des virtuellen Netzwerks, da virtuelle Netzwerke mit Peering nicht migriert werden können**). So können Sie die gleichen Funktionen ohne Leistungsverlust und ohne Wartezeit/Beeinträchtigung der Bandbreite bereitstellen. Dank des [Peerings in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md) können Web-/Workerrollenbereitstellungen nun problemlos korrigiert werden, sodass sie die Migration zu Azure Resource Manager nicht blockieren.

- **Azure Resource Manager-Kontingente:** In Azure-Regionen gelten für das klassische Bereitstellungsmodell und für Azure Resource Manager separate Kontingente/Grenzwerte. In einem Migrationsszenario wird zwar keine neue Hardware genutzt *(wir tauschen vorhandene virtuelle Computer des klassischen Bereitstellungsmodells gegen virtuelle Computer des Azure Resource Manager-Bereitstellungsmodells)* , vor der Migration müssen aber trotzdem Azure Resource Manager-Kontingente mit ausreichender Kapazität vorhanden sein. Im Anschluss sind die wichtigsten Grenzwerte aufgeführt, die nach unserer Erfahrung Probleme verursachen können.  Erstellen Sie ein kontingentbezogenes Supportticket, um die Grenzwerte zu erhöhen.

    > [!NOTE]
    > Diese Grenzwerte müssen in der Region erhöht werden, in der sich die aktuelle Umgebung befindet, die Sie migrieren möchten.
    >

  - Netzwerkschnittstellen
  - Load Balancer
  - Öffentliche IP-Adressen
  - Statische öffentliche IP-Adressen
  - Kerne
  - Netzwerksicherheitsgruppen
  - Routingtabellen

    Die aktuellen Azure Resource Manager-Kontingente können mithilfe der folgende Befehle mit der neuesten Version der Azure CLI überprüft werden:

    **Compute** *(Kerne, Verfügbarkeitsgruppen)*

    ```azurecli
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **Netzwerk** *(virtuelle Netzwerke, statische öffentliche IP-Adressen, öffentliche IP-Adressen, Netzwerksicherheitsgruppen, Netzwerkschnittstellen, Lastenausgleichsmodule, Routingtabellen)*

    ```azurecli
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Speicher** *(Speicherkonto)*

    ```azurecli
    az storage account show-usage
    ```

- **Drosselungslimits der Azure Resource Manager-API:** Ab einer gewissen Umgebungsgröße (beispielsweise > 400 virtuelle Computer in einem VNET). Sie erreichen unter Umständen die API-Standarddrosselungslimits für Schreibvorgänge (derzeit **1200 Schreibvorgänge/Stunde**) in Azure Resource Manager. Erstellen Sie vor der Migration ein Supportticket, um dieses Limit für Ihr Abonnement zu erhöhen.

- **VM-Status „Timeout bei der Bereitstellung“:** Falls ein virtueller Computer den Status **Timeout bei der Bereitstellung** besitzt, muss dies vor der Migration behoben werden. Dazu müssen Sie die Bereitstellung des virtuellen Computers aufheben und ihn erneut bereitstellen (ihn also löschen, den Datenträger aufbewahren und den virtuellen Computer neu erstellen). Dies ist immer mit Ausfallzeiten verbunden.

- **VM-Status „RoleStateUnknown“:** Falls die Migration aufgrund einer Fehlermeldung vom Typ **Unbekannter Rollenstatus** angehalten wird, überprüfen Sie den virtuellen Computer mithilfe des Portals, und vergewissern Sie sich, dass er ausgeführt wird. Dieser Fehler verschwindet in der Regel nach ein paar Minuten von selbst (also ohne Korrekturmaßnahme). Häufig handelt es sich um einen vorübergehenden Fehler, der während eines VM-Vorgangs vom Typ **Start**, **Stopp**, **Neustart** auftritt. **Empfohlene Vorgehensweise:** Warten Sie einige Minuten, und führen Sie die Migration dann erneut aus.

- **Kein Fabric-Cluster vorhanden:** In manchen Fällen können bestimmte virtuelle Computer aus diversen merkwürdigen Gründen nicht migriert werden. Bekannt ist beispielsweise ein Fall, in dem der virtuelle Computer vor Kurzem (etwa innerhalb der letzten Woche) erstellt und zufällig in einem Azure-Cluster platziert wurde, der noch nicht für Azure Resource Manager-Workloads bereit ist.  Sie erhalten die Fehlermeldung **Kein Fabric-Cluster vorhanden**, und der virtuelle Computer kann nicht migriert werden. Dieses spezielle Problem verschwindet in der Regel, wenn Sie ein paar Tage warten, da Azure Resource Manager zeitnah für den Cluster aktiviert wird. Als sofortige Problemumgehung können Sie jedoch für den Computer `stop-deallocate` ausführen, mit der Migration fortfahren und den virtuellen Computer nach der Migration in Azure Resource Manager wieder starten.

### <a name="pitfalls-to-avoid"></a>Vermeidbare Fehler

- Verzichten Sie nicht auf Migrationsprobeläufe mit Überprüfung/Vorbereitung/Abbruch.
- Die meisten (wenn nicht gar alle) potenziellen Probleme kommen im Zuge der Überprüfungs-/Vorbereitungs-/Abbruchschritte ans Licht.

## <a name="migration"></a>Migration

### <a name="technical-considerations-and-tradeoffs"></a>Technische Aspekte und Kompromisse

Sie haben sich nun durch die bekannten Probleme für Ihre Umgebung gearbeitet und sind somit bereit.

Beachten Sie bei den tatsächlichen Migrationen folgende Punkte:

1. Planen Sie das virtuelle Netzwerk (die kleinste Migrationseinheit) mit zunehmender Priorität.  Kümmern Sie sich zuerst um die einfachen virtuellen Netzwerke, und widmen Sie sich dann den komplizierteren virtuellen Netzwerken.
2. Die meisten Kunden verfügen über produktionsfremde Umgebungen und über Produktionsumgebungen.  Heben Sie sich die Produktionsumgebungen bis zum Schluss auf.
3. **(OPTIONAL)** Planen Sie für die Wartung eine Ausfallzeit mit genügend Puffer für den Fall, dass unerwartete Probleme auftreten.
4. Kommunizieren Sie mit Ihren Supportteams, und stimmen Sie sich ab, falls Probleme auftreten.

### <a name="patterns-of-success"></a>Erfolgsmuster

Ziehen Sie vor einer echten Migration die technische Anleitung aus dem Abschnitt „Lab-Test“ (weiter oben) heran, und ergreifen Sie entsprechende Korrekturmaßnahmen.  Mit angemessenen Tests ist die Migration eigentlich keine große Sache.  Bei Produktionsumgebungen ist unter Umständen zusätzliche Unterstützung hilfreich – etwa durch einen zuverlässigen Microsoft-Partner oder im Rahmen von Microsoft Premier-Diensten.

### <a name="pitfalls-to-avoid"></a>Vermeidbare Fehler

Unzureichendes Testen kann zu Problemen und Verzögerungen bei der Migration führen.  

## <a name="beyond-migration"></a>Nach der Migration

### <a name="technical-considerations-and-tradeoffs"></a>Technische Aspekte und Kompromisse

Nachdem Sie nun Azure Resource Manager verwenden, sollten Sie die Plattform auch optimal nutzen.  In der [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md) finden Sie Informationen zu weiteren Vorteilen.

Ziehen Sie Folgendes in Betracht:

- Bündeln Sie die Migration mit anderen Aktivitäten.  Die meisten Kunden entscheiden sich für ein Zeitfenster für die Anwendungswartung.  Wenn Sie zu dieser Kategorie gehören, können Sie die Ausfallzeit zur Aktivierung weiterer Azure Resource Manager-Funktionen wie Verschlüsselung und Migration zu Managed Disks nutzen.
- Sehen Sie sich noch einmal die technischen und geschäftlichen Gründe für Azure Resource Manager an. Aktivieren Sie die zusätzlichen, Azure Resource Manager-exklusiven Dienste, die für Ihre Umgebung geeignet sind.
- Modernisieren Sie Ihre Umgebung mit PaaS-Diensten.

### <a name="patterns-of-success"></a>Erfolgsmuster

Entscheiden Sie ganz bewusst, welche Dienste Sie nun in Azure Resource Manager aktivieren möchten.  Viele Kunden finden Folgendes für ihre Azure-Umgebungen hilfreich:

- [Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/overview.md).
- [Azure Resource Manager-Vorlagen zur einfacheren und präziseren Bereitstellung](../azure-resource-manager/templates/overview.md).
- [Tags](../azure-resource-manager/management/tag-resources.md).
- [Aktivitätssteuerung](../azure-resource-manager/management/view-activity-logs.md)
- [Azure-Richtlinien](../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Vermeidbare Fehler

Rufen Sie sich noch einmal die Gründe für die Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager ins Gedächtnis.  Was waren die ursprünglichen geschäftlichen Gründe? Haben Sie Ihr geschäftliches Ziel erreicht?


## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell](migration-classic-resource-manager-ps.md)
* [Communitytools zur Unterstützung beim Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Überprüfen der häufigsten Fehler bei der Migration](migration-classic-resource-manager-errors.md)
* [Antworten auf die am häufigsten gestellten Fragen zum Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-faq.md)
