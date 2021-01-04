---
title: Integration von Cloud Foundry in Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Cloud Foundry die Azure-Dienste verwenden kann, um die Enterprise-Erfahrung zu verbessern
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 897d7f662c4cad4df92eeec66820a0e8cf17b8ad
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040603"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrieren von Cloud Foundry in Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) ist eine PaaS-Plattform, die auf der IaaS-Plattform von Cloudanbietern ausgeführt wird. Sie bietet eine konsistente Anwendungsbereitstellung für mehrere Cloudanbieter. Sie kann auch in unterschiedliche Azure-Dienste integriert werden, wodurch Hochverfügbarkeit auf Unternehmensniveau, Skalierbarkeit und Kostenersparnis garantiert wird.
Es gibt [sechs Subsysteme von Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), die flexibel online skaliert werden können: Routing, Authentifizierung, Verwaltung des Anwendungslebenszyklus, Dienstverwaltung, Messaging und Überwachung. Für jedes Subsystem können Sie die Cloud Foundry-Plattform so konfigurieren, dass sie entsprechende Azure-Dienste verwendet. 

![Cloud Foundry in der Azure-Integrationsarchitektur](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Hochverfügbarkeit und Skalierbarkeit
### <a name="managed-disk"></a>Verwalteter Datenträger
Bosh verwendet die Azure-CPI (Cloud Provider Interface, Cloudanbieterschnittstelle) für die Datenträgererstellungs- und Löschroutinen. Es werden standardmäßig nicht verwaltetet Datenträger verwendet. Kunden müssen Speicherkonten manuell erstellen und anschließend die Konten in CF-Manifestdateien konfigurieren. Der Grund dafür sind die Einschränkung für die Anzahl der Datenträger pro Speicherkonto.
Da nun [verwalteter Datenträger](https://azure.microsoft.com/services/managed-disks/) verfügbar ist, ist auch verwalteter sicherer und zuverlässiger Datenträgerspeicher für virtuelle Computer verfügbar. Kunden müssen für die Skalierung und Hochverfügbarkeit nicht länger mit dem Speicherkonto arbeiten. Azure ordnet die Datenträger automatisch an. Egal ob es sich um eine neue oder vorhandene Bereitstellung handelt, die Azure-CPI behandelt die Erstellung oder Migration des verwalteten Datenträgers während einer CF-Bereitstellung. Sie wird mit PCF 1.11 unterstützt. Sie können auch den Open Source-[Leitfaden für verwaltete Datenträger](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) für Cloud Foundry zu Referenzzwecken lesen. 
### <a name="availability-zone-"></a>Verfügbarkeitszone *
Als native Cloudanwendungsplattform ist Cloud Foundry mit [vier Hochverfügbarkeitsebenen](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html) ausgestattet. Die ersten drei Ebenen von Softwarefehlern können vom CF-System selbst behandelt werden, die Plattformfehlertoleranz wird jedoch von Cloudanbietern bereitgestellt. Die CF-Schlüsselkomponenten sollten mit der Hochverfügbarkeitslösung für Plattformen eines Cloudanbieters geschützt werden. Diese enthält Kacheln von GoRouters, Diego Brains sowie von der CF-Datenbank und des CF-Diensts. In der Standardeinstellung wird die [Azure-Verfügbarkeitsgruppe](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) für die Fehlertoleranz zwischen Clustern in einem Rechenzentrum verwendet.
Die gute Nachricht ist, dass die [Azure-Verfügbarkeitszone](../availability-zones/az-overview.md) nun verfügbar ist, wodurch die Fehlertoleranz auf die nächste Ebene erweitert wird und die Latenzredundanz zwischen verschiedenen Rechenzentren niedrig bleibt.
Die Azure-Verfügbarkeitszone erreicht die Hochverfügbarkeit durch Platzieren von mehreren VMs in mehr als zwei Rechenzentren, wobei jeder VM-Satz mit anderen Sätzen identisch ist. Wenn eine Zone ausfällt, sind die anderen Sätze noch aktiv, da sie vom Ausfall isoliert sind.
> [!NOTE] 
> Die Azure-Verfügbarkeitszone ist noch nicht für alle Regionen verfügbar. Sehen Sie sich die neueste [Ankündigung für die Liste der unterstützten Regionen](../availability-zones/az-overview.md) an. Für die Cloud Foundry-Plattform im Open Source-Modus lesen Sie den Artikel [Azure Availability Zone for open source Cloud Foundry guidance (Azure-Verfügbarkeitszone für die Open Source-Cloud Foundry-Plattform (Leitfaden))](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Netzwerkrouting
Standardmäßig wird der Lastenausgleich von Azure im Tarif „Basic“ für eingehende CF-API/Apps-Anforderungen verwendet, der diese an Gorouters weiterleitet. CF-Komponenten wie Diego Brain, MySQL und ERT können ebenso den Lastenausgleich verwenden, um den Datenverkehr für die Hochverfügbarkeit auszugleichen. Azure stellt außerdem eine Sammlung aus vollständig verwalteten Lastenausgleichslösungen bereit. Wenn Sie nach Informationen zu TLS/SSL-Terminierung („SSL-Auslagerung“) oder Verarbeitung pro HTTP/HTTPS-Anforderungsanwendungsschicht suchen, lesen Sie „Application Gateway“. Weitere Informationen zur Hochverfügbarkeit und zum Lastenausgleich der Skalierbarkeit auf Ebene 4 finden Sie im Abschnitt „Load Balancer Standard“.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](../application-gateway/overview.md) bietet unterschiedliche Lastenausgleichsfunktionen für Ebene 7, einschließlich TLS-Abladung, End-to-End-SSL, Web Application Firewall, auf Cookies basierende Sitzungsaffinität usw. Sie können [Application Gateway auf der Open Source-Cloud Foundry-Plattform konfigurieren](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Sehen Sie sich für PCF die [Versionshinweise zu PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) an, um Informationen zum POC-Test (Proof of Concept) zu erhalten.

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
Azure Load Balancer ist eine Lastenausgleichskomponente der Ebene 4. Sie wird verwendet, um den Datenverkehr zwischen Dienstinstanzen in einer Gruppe mit Lastenausgleich zu verteilen. Die Standardversion stellt zusätzlich zu den Features der Basisversion [erweiterte Features](../load-balancer/load-balancer-overview.md) bereit. Beispiele: 1. Der der maximale Back-End-Pool wird von 100 auf 1000 VMs erweitert.  2. Die Endpunkte unterstützen nun mehrere Verfügbarkeitsgruppen anstatt nur einer einzelnen Verfügbarkeitsgruppe.  3. Zusätzliche Features wie Hochverfügbarkeitsports, umfangreichere Überwachungsdaten usw. Wenn Sie zur Azure-Verfügbarkeitszone wechseln, ist der Load Balancer Standard erforderlich. Es empfiehlt sich, für eine neue Bereitstellung mit dem Azure Load Balancer Standard zu beginnen. 

## <a name="3-authentication"></a>3. Authentifizierung 
[Cloud Foundry User Account and Authentication (Cloud Foundry-Benutzerkonto und -Authentifizierung)](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) ist der zentrale Identitätsverwaltungsdienst für CF und der unterschiedlichen Komponenten dieser Plattform. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. UAA (User Account and Authentication) wird für die Cloud Foundry-Authentifizierung verwendet. Als erweiterte Option unterstützt UAA Azure AD als externer Benutzerspeicher. Azure AD-Benutzer können auf Cloud Foundry über ihre LDAP-Identität zugreifen, auch ohne Cloud Foundry-Konto. Befolgen Sie diese Schritte, um [Azure AD für UAA in PCF zu konfigurieren](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Datenspeicher für das Cloud Foundry-Runtime-System
Cloud Foundry bietet hohe Erweiterbarkeit für die Verwendung des Azure-Blobspeichers oder für Azure MySQL/PostgreSQL-Dienste für den Systemspeicher der Anwendungslaufzeit.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure-Blobspeicher für den Cloudcontroller-Blobstore von Cloud Foundry
Der Cloudcontroller-Blobstore ist ein wichtiger Datenspeicher für Buildpacks, Droplets, Pakete sowie Ressourcenpools. Standardmäßig wird der NFS-Server für den Cloudcontroller-Blobspeicher verwendet. Um einen Single Point of Failure zu vermeiden, verwenden Sie Azure Blob Storage als externen Speicher. Sehen Sie sich die [Cloud Foundry-Dokumentation](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) sowie die [Optionen in Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html) an, um Hintergrundinformationen zu erhalten.

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL als Datenbank für die elastische Cloud Foundry-Datenbank *
Die elastische CF-Runtime erfordert zwei wichtige Systemdatenbanken:
#### <a name="ccdb"></a>CCDB 
Die Cloud Controller-Datenbank.  Der Cloudcontroller stellt REST-API-Endpunkte bereit, damit Clients auf das System zugreifen können. CCDB speichert Tabellen für Organisationen, Bereiche, Dienste, Benutzerrollen usw. für den Cloudcontroller.
#### <a name="uaadb"></a>UAADB 
Die Datenbank für das Benutzerkonto und die Authentifizierung. Sie speichert die der Benutzerauthentifizierung zugeordneten Daten, z.B. verschlüsselte Benutzernamen und -kennwörter.

Es kann standardmäßig eine lokale Systemdatenbank (MySQL) verwendet werden. Nutzen Sie für die Hochverfügbarkeit und Skalierung die von Azure verwalteten MySQL- oder PostgreSQL-Dienste. Lesen Sie die Anweisung zur [Aktivierung von Azure MySQL/PostgreSQL für CCDB, UAADB und andere Systemdatenbanken mit Open Source-Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Open Service Broker
Der Service Broker von Azure bietet konsistente Schnittstellen, um den Zugriff von Anwendungen auf Azure-Dienste zu verwalten. Der neue [Open Service Broker für Azure-Projekte](https://github.com/Azure/open-service-broker-azure) stellt eine einfache Möglichkeit zur Übermittlung von Diensten an Anwendungen für Cloud Foundry, OpenShift und Kubernetes bereit. Unter [Azure Open Service Broker for PCF](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) erhalten Sie Anweisungen zur Bereitstellung auf PCF.

## <a name="6-metrics-and-logging"></a>6. Metriken und Protokollierung
Azure Log Analytics Nozzle ist eine Cloud Foundry-Komponente, die Metriken aus der [Firehose-Komponente von Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) an [Azure Monitor-Protokolle weiterleitet](https://azure.microsoft.com/services/log-analytics/). Mithilfe der Nozzle-Komponente können Sie Integritäts- und Leistungsmetriken für Ihr CF-System über mehrere Bereitstellungen hinweg sammeln, anzeigen und analysieren.
[Hier](./cloudfoundry-oms-nozzle.md) erfahren Sie, wie Sie Azure Log Analytics Nozzle für Open Source- und Pivotal Cloud Foundry-Umgebungen bereitstellen und dann über die Konsole von Azure Monitor-Protokolle auf die Daten zugreifen. 
> [!NOTE]
> Von PCF 2.0 aus werden Bosh-Integritätsmetriken für VMs standardmäßig an Loggregator Firehose weitergeleitet und dann in die Konsole von Azure Monitor-Protokolle integriert.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Kosteneinsparungen
### <a name="cost-saving-for-devtest-environments"></a>Kosteneinsparungen für Dev/Test-Umgebungen
#### <a name="b-series-"></a>B-Serie: *
Während die F- und D-Serien für VMs allgemein für Pivotal Cloud Foundry-Produktionsumgebungen empfohlen werden, enthält die neue „burstfähige“ [B-Serie](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) neue Optionen bereit. Virtuelle Burst-fähige Computer der B-Serie sind ideal für Workloads geeignet, die nicht kontinuierlich die volle Leistung der CPU benötigen. Hierzu zählen beispielsweise Webserver, kleine Datenbanken sowie Entwicklungs- und Testumgebungen. Diese Workloads haben in der Regel kurzfristige Leistungsanforderungen. Im Vergleich zu F1 mit 0,05 USD pro Stunde kostet B1 nur 0,012 USD pro Stunde. Weitere Informationen dazu finden Sie in der vollständigen Liste der [VM-Größen](../virtual-machines/sizes-general.md) und [Preise](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). 
#### <a name="managed-standard-disk"></a>Verwalteter Standarddatenträger: 
Premium-Datenträger wurden für die zuverlässige Leistung in der Produktion empfohlen.  Mithilfe [verwalteter Datenträger](https://azure.microsoft.com/services/managed-disks/) bietet der Standardspeicher ebenfalls ähnliche Zuverlässigkeit bei unterschiedlicher Leistung. Standarddatenträger bieten für eine nicht leistungsabhängige Workload wie DevTest oder eine weniger wichtige Umgebung eine Alternative mit niedrigerem Kostenfaktor.  
### <a name="cost-saving-in-general"></a>Allgemeines zu Kosteneinsparungen 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Hohe Kosteneinsparungen für VMs durch Azure-Reservierungen: 
Heute werden alle CF-VMs über die Preisgestaltung „bei Bedarf“ abgerechnet, obwohl die Umgebungen in der Regel unbegrenzt aktiv sind. Sie können VM-Kapazität für einen Zeitraum von 1–3 Jahren reservieren und so von Rabatten von 45–65 % profitieren. Rabatte werden im Abrechnungssystem angewendet, Ihre Umgebung ändert sich dadurch nicht. Weitere Informationen finden Sie unter [Reservierte Azure-VM-Instanzen (RIs)](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Verwalteter Premium-Datenträger mit verringerten Größen: 
Verwaltete Datenträger unterstützen kleinere Datenträgergrößen, z.B. P4 (32 GB) und P6 (64 GB) für Premium- und Standard-Datenträger. Wenn Sie über kleine Workloads verfügen, können Sie Kosten sparen, wenn Sie von standardmäßigen Premium-Datenträgern zu verwalteten Premium-Datenträgern migrieren.
#### <a name="use-azure-first-party-services"></a>Verwenden des Azure-Erstanbieterdiensts: 
Profitieren Sie zusätzlich zur Hochverfügbarkeit und Zuverlässigkeit (wie weiter oben erwähnt) vom Vorteil des Erstanbieterdiensts von Azure, und sparen Sie langfristig Administrationskosten. 

Pivotal hat eine [Small Footprint-ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) für PCF-Kunden veröffentlicht. Die Komponenten sind in nur 4 VMs zusammengestellt und führen bis zu 2500 Anwendungsinstanzen aus. Die Testversion ist nun über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) verfügbar.

## <a name="next-steps"></a>Nächste Schritte
Azure-Integrationsfeatures sind zuerst auf der [Open Source-Cloud Foundry-Plattform](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/) verfügbar, bevor sie auf Pivotal Cloud Foundry verfügbar sind. Features, die mit einem Sternchen (*) markiert sind, sind noch nicht über PCF verfügbar. Die Cloud Foundry-Integration mit Azure Stack wird in diesem Dokument nicht behandelt.
Für Informationen zur PCF-Unterstützung zu den Features, die mit * markiert sind, oder zur Cloud Foundry-Integration mit Azure Stack, wenden Sie sich an Ihren Pivotal- oder Microsoft-Konto-Manager, um Statusupdates zu erhalten.