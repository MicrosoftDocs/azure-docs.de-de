---
title: Ausführliche technische Informationen zur Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager
description: Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: d73ad3235e5ff2c9dbf0cca546308469ef6b5ac0
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887038"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager

> [!IMPORTANT]
> Derzeit nutzen etwa 90 % der IaaS-VMs [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Seit dem 28. Februar 2020 gelten klassische VMs als veraltet. Sie werden am 1. März 2023 vollständig eingestellt. [Erfahren Sie mehr]( https://aka.ms/classicvmretirement) zu dieser Einstellung und den [Auswirkungen auf Sie](./classic-vm-deprecation.md#how-does-this-affect-me).

Wir betrachten nun genau die Migration vom klassischen Azure-Bereitstellungsmodell zum Azure Resource Manager-Bereitstellungsmodell. Ressourcen betrachten wir auf Ressourcen- und Featureebene, damit Sie verstehen, wie die Azure-Plattform Ressourcen zwischen den beiden Bereitstellungsmodellen migriert. Weitere Informationen finden Sie im Artikel zur Dienstankündigung: [Plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md).


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager
Zunächst einmal ist es wichtig, den Unterschied zwischen Vorgängen auf der Datenebene und Vorgängen auf der Verwaltungsebene für IaaS-Ressourcen (Infrastructure-as-a-Service) zu verstehen.

* *Verwaltungs-/Steuerungsebene* beschreibt die Aufrufe, die auf der Verwaltungs-/Steuerungsebene oder bei der API zum Ändern von Ressourcen eingehen. Beispielsweise werden ausgeführte Ressourcen durch Vorgänge verwaltet wie das Erstellen eines virtuellen Computers, das Neustarten eines virtuellen Computers und das Aktualisieren eines virtuellen Netzwerks mit einem neuen Subnetz. Die Verbindungsherstellung mit den virtuellen Computern ist davon nicht direkt betroffen.
* *Datenebene* (Anwendung) beschreibt die Laufzeit der Anwendung selbst und umfasst die Interaktion mit Instanzen, die nicht über die Azure-API abgewickelt werden. Das Zugreifen auf Ihre Website oder das Abrufen von Daten aus einer ausgeführten SQL Server-Instanz oder von einem MongoDB-Server sind Beispiele für Interaktionen mit der Datenebene oder Anwendung. Weitere Beispiele wären etwa das Kopieren eines Blobs aus einem Speicherkonto und das Zugreifen auf eine öffentliche IP-Adresse, um per Remotedesktopprotokoll (RDP) oder Secure Shell (SSH) eine Verbindung mit dem virtuellen Computer herzustellen. Mit diesen Vorgängen wird die Ausführung der Anwendung für die Bereiche Compute, Netzwerk und Speicher sichergestellt.

Die Datenebene für das klassische Bereitstellungsmodell und für Resource Manager-Stapel ist identisch. Der Unterschied besteht darin, dass Microsoft die Darstellung der Ressourcen aus dem klassischen Bereitstellungsmodell im Zuge der Migration in die Darstellung des Resource Manager-Stapels umwandelt. Infolgedessen sind für die Verwaltung der Ressourcen im Resource Manager-Stapel neue Tools, APIs und SDKs erforderlich.

![Diagramm zur Veranschaulichung des Unterschieds zwischen Verwaltungs-/Steuerungsebene und Datenebene](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> In einigen Migrationsszenarien hält die Azure-Plattform die virtuellen Computer an, hebt ihre Zuordnung auf und startet sie neu. Dies hat einen kurzen Ausfall der Datenebene zur Folge.
>

## <a name="the-migration-experience"></a>Migrationsvorgang
Vorbereitung der Migration:

* Stellen Sie sicher, dass die Ressourcen, die Sie migrieren möchten, keine nicht unterstützten Features oder Konfigurationen verwenden. Gewöhnlich erkennt die Plattform diese Probleme und generiert einen Fehler.
* Wenn Sie über virtuelle Computer verfügen, die nicht in einem virtuellen Netzwerk enthalten sind, werden diese während der Vorbereitung beendet, und die Zuordnung wird aufgehoben. Wenn Sie die öffentliche IP-Adresse nicht verlieren möchten, sollten Sie die IP-Adresse ggf. reservieren, bevor Sie den Vorbereitungsvorgang starten. Wenn sich die virtuellen Computer in einem virtuellen Netzwerk befinden, werden sie nicht beendet, und die Zuordnung wird nicht aufgehoben.
* Planen Sie die Migration für einen Zeitraum außerhalb der Geschäftszeiten, um Raum für unerwartete Fehler zu lassen, die während der Migration unter Umständen auftreten können.
* Laden Sie die aktuelle Konfiguration Ihrer virtuellen Computer mit PowerShell, CLI-Befehlen (Befehlszeilenschnittstelle) oder REST-APIs herunter, um die Überprüfung zu vereinfachen, nachdem der Vorbereitungsschritt abgeschlossen ist.
* Aktualisieren Sie Ihre Skripts für die Automatisierung und Operationalisierung, um sie an das Resource Manager-Bereitstellungsmodell anzupassen, bevor Sie die Migration starten. Sie können optional GET-Vorgänge durchführen, wenn sich die Ressourcen im Zustand „Vorbereitet“ befinden.
* Werten Sie die Richtlinien für die rollenbasierte Zugriffsteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) aus, die für die IaaS-Ressourcen im klassischen Bereitstellungsmodell konfiguriert sind, und stellen Sie einen Plan für die Zeit nach der Migration auf.

Der Migrationsworkflow sieht wie folgt aus:

![Diagramm zur Veranschaulichung des Migrationsworkflows](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Die in den folgenden Abschnitten beschriebenen Vorgänge sind alle idempotent. Sollte ein Problem auftreten, das nicht auf ein nicht unterstütztes Feature oder auf einen Konfigurationsfehler zurückzuführen ist, wiederholen Sie den Vorbereitungs-, Abbruch- oder Commitvorgang. Azure versucht, die Aktion erneut auszuführen.
>
>

### <a name="validate"></a>Überprüfen
Der Überprüfungsvorgang ist der erste Schritt im Migrationsprozess. Das Ziel dieses Schritts besteht darin, den Zustand der zu migrierenden Ressourcen im klassischen Bereitstellungsmodell zu analysieren. Der Vorgang ermittelt, ob die Ressourcen für die Migration bereit sind („Erfolgreich“ oder „Fehler“).

Sie wählen das virtuelle Netzwerk oder einen Clouddienst aus (sofern es kein virtuelles Netzwerk ist), das bzw. den Sie für die Migration überprüfen möchten. Falls die Ressource nicht migriert werden kann, gibt Azure die Gründe dafür an.

#### <a name="checks-not-done-in-the-validate-operation"></a>Nicht in der Überprüfung enthaltene Überprüfungen

Bei der Überprüfung wird nur der Zustand der Ressourcen im klassischen Bereitstellungsmodell analysiert. Dabei können alle Fehler und nicht unterstützten Szenarien ermittelt werden, die auf verschiedene Konfigurationen im klassischen Bereitstellungsmodell zurückzuführen sind. Es können jedoch nicht alle Fehler ermittelt werden, die der Azure Resource Manager-Stapel unter Umständen während der Migration für die Ressourcen verursacht. Diese Probleme werden erst überprüft, wenn die Ressourcen im nächsten Schritt der Migration (Vorbereitung) transformiert werden. Die folgende Tabelle enthält alle Probleme, die im Rahmen des Überprüfungsvorgangs nicht überprüft werden:


|Bei der Überprüfung nicht ausgeführte Netzwerküberprüfungen|
|-|
|Ein virtuelles Netzwerk mit ER- und VPN-Gateway|
|Eine getrennte Virtual Network-Gatewayverbindung|
|Alle ER-Verbindungen wurden vorab zum Azure Resource Manager-Stapel migriert.|
|Azure Resource Manager-Kontingentüberprüfungen für Netzwerkressourcen. Beispiele: statische öffentliche IP-Adresse, dynamische öffentliche IP-Adressen, Load Balancer, Netzwerksicherheitsgruppen, Routingtabellen und Netzwerkschnittstellen. |
| Alle Lastenausgleichsregeln sind in der gesamten Bereitstellung/im gesamten virtuellen Netzwerk gültig. |
| In Konflikt stehende private IP-Adressen zwischen virtuellen Computern mit dem Status „Beendet (Zuordnung aufgehoben)“ im gleichen virtuellen Netzwerk |

### <a name="prepare"></a>Vorbereiten
Der Vorbereitungsvorgang ist der zweite Schritt im Migrationsprozess. Das Ziel dieses Schritts besteht darin, die Transformation der IaaS-Ressourcen von Ressourcen des klassischen Bereitstellungsmodells zu Resource Manager-Ressourcen zu simulieren. Zur Veranschaulichung werden sie nebeneinander dargestellt.

> [!NOTE] 
> Die Ressourcen des klassischen Bereitstellungsmodells bleiben bei diesem Schritt unverändert. Dieser Schritt kann also problemlos ausgeführt werden, wenn Sie die Migration ausprobieren möchten. 

Sie wählen das virtuelle Netzwerk oder den Clouddienst aus (sofern es kein virtuelles Netzwerk ist), das bzw. den Sie für die Migration vorbereiten möchten.

* Falls die Ressource nicht migriert werden kann, beendet Azure den Migrationsprozess und gibt an, warum der Vorbereitungsvorgang nicht erfolgreich war.
* Wenn die Ressource migriert werden kann, sperrt Azure zunächst die Vorgänge der Verwaltungsebene für die zu migrierenden Ressourcen. Beispielsweise können Sie einem in der Migration befindlichen virtuellen Computer keinen Datenträger hinzuzufügen.

Anschließend startet Azure für die zu migrierenden Ressourcen die Migration der Metadaten vom klassischen Bereitstellungsmodell zu Resource Manager.

Nach Abschluss der Vorbereitung können Sie die Ressourcen sowohl im klassischen Bereitstellungsmodell als auch im Resource Manager-Bereitstellungsmodell visualisieren. Für jeden Clouddienst im klassischen Bereitstellungsmodell erstellt die Azure-Plattform einen Ressourcengruppennamen nach dem Muster `cloud-service-name>-Migrated`.

> [!NOTE]
> Der Name einer für migrierte Ressourcen erstellten Ressourcengruppe kann nicht frei gewählt werden und lautet immer „-Migrated“. Nach Abschluss der Migration können Sie die Ressourcen jedoch mithilfe des Verschiebefeatures von Azure Resource Manager in eine beliebige Ressourcengruppe verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Die beiden folgenden Screenshots zeigen das Ergebnis nach einem erfolgreichen Vorbereitungsvorgang. Der erste Screenshot zeigt eine Ressourcengruppe, die den ursprünglichen Clouddienst enthält. Der zweite Screenshot zeigt die neue Ressourcengruppe („-Migrated“) mit den entsprechenden Azure Resource Manager-Ressourcen.

![Screenshot des ursprünglichen Clouddiensts](./media/migration-classic-resource-manager/portal-classic.png)

![Screenshot der Azure Resource Manager-Ressourcen aus dem Vorbereitungsvorgang](./media/migration-classic-resource-manager/portal-arm.png)

Hier sehen Sie, wie Ihre Ressourcen nach Abschluss der Vorbereitungsphase aussehen. Wie Sie sehen, ist die Ressource auf der Datenebene identisch. Sie ist sowohl in der Verwaltungsebene (klassisches Bereitstellungsmodell) als auch in der Steuerungsebene (Resource Manager) vorhanden.

![Diagramm der Vorbereitungsphase](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuelle Computer, die sich nicht in einem virtuellen Netzwerk des klassischen Bereitstellungsmodells befinden, werden in dieser Migrationsphase beendet, und ihre Zuordnung wird aufgehoben.
>

### <a name="check-manual-or-scripted"></a>Überprüfung (manuell oder per Skript)
Im Überprüfungsschritt können Sie bei Bedarf die Konfiguration verwenden, die Sie zuvor heruntergeladen haben, um zu überprüfen, ob die Migration korrekt aussieht. Alternativ können Sie sich auch beim Portal anmelden und einzelne Eigenschaften und Ressourcen prüfen, um sicherzustellen, dass die Migration der Metadaten in Ordnung ist.

Wenn Sie ein virtuelles Netzwerk migrieren, wird der Großteil der Konfiguration für virtuelle Computer nicht neu gestartet. Für Anwendungen auf diesen virtuellen Computern können Sie überprüfen, ob die Anwendung noch ausgeführt wird.

Sie können Ihre Skripts für die Überwachung und den Betrieb testen, um zu ermitteln, ob die virtuellen Computer wie erwartet ausgeführt werden und ob die aktualisierten Skripts ordnungsgemäß funktionieren. GET-Vorgänge werden nur dann unterstützt, wenn sich die Ressourcen im Status „Vorbereitet“ befinden.

Es gibt kein festes Zeitfenster, in dem Sie die Migration committen müssen. Sie können sich in diesem Zustand so viel Zeit wie nötig lassen. Die Verwaltungsebene ist aber für diese Ressourcen gesperrt, bis Sie entweder einen Abbruch (abort) oder ein Commit (commit) durchführen.

Falls Probleme auftreten, können Sie die Migration immer abbrechen und zurück zum klassischen Bereitstellungsmodell wechseln. Nach dem Wechsel zurück öffnet Azure die Vorgänge der Verwaltungsebene für die Ressourcen, damit Sie den normalen Betrieb für diese virtuellen Computer im klassischen Bereitstellungsmodell wieder aufnehmen können.

### <a name="abort"></a>Abbruch
Hierbei handelt es sich um einen optionalen Schritt, mit dem Sie Ihre Änderungen am klassischen Bereitstellungsmodell zurücksetzen und die Migration beenden können. Dieser Vorgang löscht die im vorherigen Vorbereitungsschritt erstellten Resource Manager-Metadaten für Ihre Ressourcen. 

![Diagramm des Abbruchschritts](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Dieser Vorgang kann nicht mehr ausgeführt werden, nachdem Sie den Commitvorgang ausgelöst haben.     
>

### <a name="commit"></a>Commit
Nach Abschluss der Überprüfung können Sie einen Commit für die Migration durchführen. Die Ressourcen werden nicht mehr im klassischen Bereitstellungsmodell angezeigt und stehen nur noch im Resource Manager-Bereitstellungsmodell zur Verfügung. Die migrierten Ressourcen können nur im neuen Portal verwaltet werden.

> [!NOTE]
> Dies ist ein idempotenter Vorgang. Sollte er nicht erfolgreich sein, wiederholen Sie ihn. Wenn der Fehler weiterhin auftritt, erstellen Sie ein Support Ticket, oder erstellen Sie ein Forum auf [Microsoft Q&A](/answers/index.html)
>
>

![Diagramm des Commitschritts](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Flussdiagramm der Migration

Hier sehen Sie ein Flussdiagramm für die Migration:

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Umwandlung klassischer Ressourcen in Resource Manager-Ressourcen
Die folgende Tabelle zeigt die Darstellung der Ressourcen im klassischen Bereitstellungsmodell und in Resource Manager. Andere Features und Ressourcen werden derzeit nicht unterstützt.

| Klassische Darstellung | Resource Manager-Darstellung | Notizen |
| --- | --- | --- |
| Name des Clouddiensts |DNS-Name |Während der Migration wird für jeden Clouddienst eine neue Ressourcengruppe mit dem Benennungsmuster `<cloudservicename>-migrated`erstellt. Diese Ressourcengruppe enthält alle Ihre Ressourcen. Der Name des Clouddiensts wird als DNS-Name verwendet, der der öffentlichen IP-Adresse zugeordnet ist. |
| Virtueller Computer |Virtueller Computer |VM-spezifische Eigenschaften werden unverändert migriert. Bestimmte osProfile-Informationen (etwa der Computername) werden im klassischen Bereitstellungsmodell nicht gespeichert und bleiben daher nach der Migration leer. |
| An virtuellen Computer angefügte Datenträgerressourcen |An virtuellen Computer angefügte implizite Datenträger |Datenträger werden im Resource Manager-Bereitstellungsmodell nicht als Ressourcen oberster Ebene modelliert. Sie werden als implizite Datenträger unter dem virtuellen Computer migriert. Derzeit werden nur Datenträger unterstützt, die mit einem virtuellen Computer verbunden sind. Resource Manager-VMs können nun Speicherkonten im klassischen Bereitstellungsmodell verwenden, sodass die Datenträger komfortabel ohne jegliche Updates migriert werden können. |
| VM-Erweiterungen |VM-Erweiterungen |Mit Ausnahme von XML-Erweiterungen werden alle Ressourcenerweiterungen aus dem klassischen Bereitstellungsmodell migriert. |
| Zertifikate für virtuelle Computer |Zertifikate in Azure Key Vault |Wenn ein Clouddienst Dienstzertifikate enthält, erstellt die Migration pro Clouddienst jeweils eine neue Azure Key Vault-Instanz und verschiebt die Zertifikate in den Schlüsseltresor. Die VMs werden aktualisiert, um auf die Zertifikate aus dem Schlüsseltresor zu verweisen. <br><br> Löschen Sie den Schlüsseltresor nicht. Andernfalls tritt unter Umständen ein VM-Fehler auf. |
| WinRM-Konfiguration |WinRM-Konfiguration unter osProfile |Die Konfiguration der Windows-Remoteverwaltung wird im Rahmen der Migration unverändert verschoben. |
| „Availability-set“-Eigenschaft |„Availability-set“-Ressource | Im klassischen Bereitstellungsmodell handelt es sich bei der Angabe der Verfügbarkeitsgruppe um eine Eigenschaft auf dem virtuellen Computer. Bei der Migration werden Verfügbarkeitsgruppen zu einer Ressource der obersten Ebene. Folgende Konfigurationen werden nicht unterstützt: mehrere Verfügbarkeitsgruppen pro Clouddienst sowie eine Kombination aus mindestens einer Verfügbarkeitsgruppe und virtuellen Computern, die keiner Verfügbarkeitsgruppe in einem Clouddienst angehören. |
| Netzwerkkonfiguration auf einem virtuellen Computer |Primäre Netzwerkschnittstelle |Die Netzwerkkonfiguration auf einem virtuellen Computer wird nach der Migration als primäre Netzwerkschnittstellenressource dargestellt. Bei virtuellen Computern, die sich nicht in einem virtuellen Netzwerk befinden, ändert sich im Zuge der Migration die interne IP-Adresse. |
| Mehrere Netzwerkschnittstellen auf einem virtuellen Computer |Netzwerkschnittstellen |Sind einem virtuellen Computer mehrere Netzwerkschnittstellen zugeordnet, wird jede Netzwerkschnittstelle im Rahmen der Migration zu einer Ressource der obersten Ebene (einschließlich aller Eigenschaften). |
| Endpunktsatz mit Lastenausgleich |Load Balancer |Im klassischen Bereitstellungsmodell wurde von der Plattform für jeden Clouddienst ein impliziter Load Balancer zugewiesen. Im Zuge der Migration wird eine neue Load Balancer-Ressource erstellt, und der Lastenausgleichs-Endpunktsatz wird zu Load Balancer-Regeln. |
| Eingehende NAT-Regeln |Eingehende NAT-Regeln |Auf dem virtuellen Computer definierte Eingabeendpunkte werden bei der Migration in Netzwerkadressenübersetzungs-Eingangsregeln für den Load Balancer umgewandelt. |
| VIP-Adresse |Öffentliche IP-Adresse mit DNS-Name |Die virtuelle IP-Adresse wird zu einer öffentlichen IP-Adresse und dem Load Balancer zugeordnet. Eine virtuelle IP-Adresse kann nur migriert werden, wenn ihr ein Eingabeendpunkt zugewiesen ist. |
| Virtuelles Netzwerk |Virtuelles Netzwerk |Das virtuelle Netzwerk wird mit allen zugehörigen Eigenschaften in das Resource Manager-Bereitstellungsmodell migriert. Eine neue Ressourcengruppe namens `-migrated`wird erstellt. |
| Reservierte IP-Adressen |Öffentliche IP-Adresse mit statischer Zuordnungsmethode |Dem Load Balancer zugeordnete reservierte IP-Adressen werden zusammen mit dem Clouddienst oder virtuellen Computer migriert. Nicht zugeordnete reservierte IP-Adressen können mithilfe von [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip?view=azuresmps-4.0.0) migriert werden.  |
| Öffentliche IP-Adresse pro virtuellem Computer |Öffentliche IP-Adresse mit dynamischer Zuordnungsmethode |Die dem virtuellen Computer zugeordnete öffentliche IP-Adresse wird in eine öffentliche IP-Adressressource mit statischer Zuordnungsmethode konvertiert. |
| NSGs |NSGs |Einem Subnetz zugeordnete Netzwerksicherheitsgruppen (Network Security Groups, NSGs) werden im Rahmen der Migration für das Resource Manager-Bereitstellungsmodell geklont. Die NSG aus dem klassischen Bereitstellungsmodell wird bei der Migration nicht entfernt. Während der Migration werden jedoch Vorgänge auf Verwaltungsebene für die NSG blockiert. Nicht zugeordnete NSGs können mithilfe von [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup?view=azuresmps-4.0.0) migriert werden.|
| DNS-Server |DNS-Server |DNS-Server, die einem virtuellen Netzwerk oder dem virtuellen Computer zugeordnet sind, werden im Rahmen der entsprechenden Ressourcenmigration zusammen mit allen Eigenschaften migriert. |
| UDRs |UDRs |Einem Subnetz zugeordnete benutzerdefinierte Routen (User-Defined Routes, UDRs) werden im Rahmen der Migration für das Resource Manager-Bereitstellungsmodell geklont. Die UDR aus dem klassischen Bereitstellungsmodell wird bei der Migration nicht entfernt. Während der Migration werden Vorgänge auf Verwaltungsebene für die UDR blockiert. Nicht zugeordnete UDRs können mithilfe von [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable?view=azuresmps-4.0.0) migriert werden. |
| IP-Weiterleitungseigenschaft für die Netzwerkkonfiguration eines virtuellen Computers |IP-Weiterleitungseigenschaft für die Netzwerkschnittstellenkarte |Die IP-Weiterleitungseigenschaft für einen virtuellen Computer wird im Zuge der Migration in eine Eigenschaft der Netzwerkschnittstelle konvertiert. |
| Load Balancer mit mehreren IP-Adressen |Load Balancer mit mehreren öffentlichen IP-Ressourcen |Jede öffentliche, dem Load Balancer zugeordnete IP-Adresse wird in eine öffentliche IP-Ressource konvertiert und nach der Migration dem Load Balancer zugeordnet. |
| Interne DNS-Namen für den virtuellen Computer |Interne DNS-Namen für die Netzwerkschnittstellenkarte |Während der Migration werden die internen DNS-Suffixe für die virtuellen Computer in eine schreibgeschützte Eigenschaft mit dem Namen InternalDomainNameSuffix auf der NIC migriert. Das Suffix bleibt nach der Migration unverändert, und die Auflösung des virtuellen Computers sollte weiterhin funktionieren. |
| Gateway des virtuellen Netzwerks |Gateway des virtuellen Netzwerks |Eigenschaften des Gateways des virtuellen Netzwerks werden unverändert migriert. Die dem Gateway zugeordnete VIP bleibt ebenfalls unverändert. |
| Standort des lokalen Netzwerks |Lokales Netzwerkgateway |Die Eigenschaften des lokalen Netzwerkstandorts werden unverändert in eine neue Ressource (das so genannte lokale Netzwerkgateway) migriert. Sie repräsentiert lokale Adresspräfixe und die Remotegateway-IP-Adresse. |
| Verbindungsverweise |Verbindung |Konnektivitätsverweise zwischen dem Gateway und dem lokalen Netzwerkstandort in der Netzwerkkonfiguration werden nach der Migration durch eine neue Ressource namens „Verbindung“ repräsentiert. Alle Eigenschaften von Konnektivitätsverweisen in Netzwerkkonfigurationsdateien werden unverändert in die Verbindungsressource kopiert. Im klassischen Bereitstellungsmodell wird die Konnektivität zwischen virtuellen Netzwerken durch Erstellung zweier IPsec-Tunnel zu lokalen Netzwerkstandorten erreicht, die die virtuellen Netzwerke repräsentieren. Dies wird im Resource Manager-Modell zu einer VNet-zu-VNet-Verbindung transformiert, ohne dass lokale Netzwerkgateways erforderlich sind. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Änderungen an der Automatisierung und an den Tools nach der Migration
Im Rahmen der Migration Ihrer Ressourcen vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell müssen Sie Ihre vorhandene Automatisierung oder die vorhandenen Tools aktualisieren, um sicherzustellen, dass sie nach der Migration weiterhin funktionieren.


## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell](migration-classic-resource-manager-ps.md)
* [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Befehlszeilenschnittstelle](migration-classic-resource-manager-cli.md)
* [Migrieren von VPN Gateways aus dem klassischen Modell zu Resource Manager](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Migrieren von ExpressRoute-Verbindungen und zugeordneten virtuellen Netzwerken vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell](../expressroute/expressroute-migration-classic-resource-manager.md)
* [Communitytools zur Unterstützung beim Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Überprüfen der häufigsten Fehler bei der Migration](migration-classic-resource-manager-errors.md)
* [Antworten auf die am häufigsten gestellten Fragen zum Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-faq.md)