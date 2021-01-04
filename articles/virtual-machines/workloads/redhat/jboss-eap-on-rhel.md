---
title: 'Schnellstart: Bereitstellen von JBoss Enterprise Application (EAP) unter Red Hat Enterprise Linux (RHEL) für virtuelle Azure-Computer und VM-Skalierungsgruppen'
description: Es wird beschrieben, wie Sie Java-Unternehmensanwendungen mithilfe von Red Hat JBoss EAP auf virtuellen Azure-Computern bzw. in VM-Skalierungsgruppen mit RHEL bereitstellen.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: bab84b12c871c621b5a317ba8b47f9b18c91bff3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500188"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Bereitstellen von Java-Unternehmensanwendungen in Azure mit JBoss EAP unter Red Hat Enterprise Linux

Die Azure-Schnellstartvorlagen in diesem Artikel zeigen Ihnen, wie Sie [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) mit [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) auf virtuellen Azure-Computern (VMs) und in VM-Skalierungsgruppen bereitstellen können. Sie werden eine Java-Beispiel-App verwenden, um die Bereitstellung zu überprüfen. 

JBoss EAP ist eine Open-Source-Anwendungsserverplattform. Sie ermöglicht für Ihre Java-Anwendungen Sicherheit, Skalierbarkeit und hohe Leistung auf Unternehmensniveau. RHEL ist eine Open-Source-Betriebssystemplattform. Sie ermöglicht das Skalieren von vorhandenen Apps und den Rollout neuer Technologien für alle Umgebungen. 

In JBoss EAP und RHEL ist alles enthalten, was Sie zum Erstellen, Ausführen, Bereitstellen und Verwalten von Java-Unternehmensanwendungen in einer Umgebung benötigen. Diese Kombination ist eine hervorragende Open-Source-Lösung für lokale, virtuelle Umgebungen und private, öffentliche oder Hybrid Clouds.

## <a name="prerequisites"></a>Voraussetzungen 

* Ein Azure-Konto mit einem aktiven Abonnement. Aktivieren Sie Ihre [Azure-Gutschriften für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), oder [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/pricing/free-trial), damit Sie über ein Azure-Abonnement verfügen.

* JBoss EAP-Installation Sie benötigen ein Red Hat-Konto mit RHSM-Berechtigung (Red Hat Subscription Management) für JBoss EAP. Mit dieser Berechtigung können Sie die für Red Hat getestete und zertifizierte JBoss EAP-Version herunterladen.  

  Wenn Sie nicht über eine EAP-Berechtigung verfügen, sollten sie sich zunächst ein [JBoss EAP-Evaluierungsabonnement](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) beschaffen. Greifen Sie zum Erstellen eines neuen Red Hat-Abonnements auf das [Red Hat-Kundenportal](https://access.redhat.com/) zu, und richten Sie ein Konto ein.

* Die [Azure CLI](/cli/azure/overview)

* RHEL-Optionen Wählen Sie zwischen „Nutzungsbasierte Zahlung“ und „Bring-Your-Own-Subscription (BYOS)“. Bei BYOS müssen Sie Ihr RHEL Gold Image für [Red Hat Cloud Access](https://access.redhat.com/) aktivieren, bevor Sie die Schnellstartvorlage bereitstellen.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE und Jakarata EE: Anwendungsmigration

### <a name="migrate-to-jboss-eap"></a>Migrieren zu JBoss EAP
JBoss EAP 7.2 und 7.3 sind zertifizierte Implementierungen der Spezifikationen von Java Enterprise Edition (Java EE) 8 und Jakarta EE 8. JBoss EAP verfügt über vorkonfigurierte Optionen für Features, z. B. Hochverfügbarkeitsclustering, Messaging und verteiltes Caching. Hiermit können Benutzer Anwendungen schreiben, bereitstellen und ausführen, indem sie die verschiedenen APIs und Dienste von JBoss EAP verwenden.  

Weitere Informationen zu JBoss EAP finden Sie in der [Einführung zu JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) bzw. der [Einführung zu JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Anwendungen von JBoss EAP

* **Anwendungen von Webdiensten** Webdienste bieten eine Standardmethode für die Interoperabilität zwischen Softwareanwendungen. Jede Anwendung kann auf verschiedenen Plattformen und Frameworks ausgeführt werden. Mit diesen Webdiensten wird die Kommunikation für interne und heterogene Subsysteme ermöglicht. 

  Weitere Informationen finden Sie unter [Entwickeln von Webdienstanwendungen in EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) bzw. [Entwickeln von Webdienstanwendungen in EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **EJB-Anwendungen (Enterprise Java Beans)** EJB 3.2 ist eine API für die Entwicklung von verteilten, transaktionalen, sicheren und portablen Java EE- und Jakarta EE-Anwendungen. Bei EJB werden serverseitige Komponenten (als Enterprise Beans bezeichnet) verwendet, um die Geschäftslogik einer Anwendung entkoppelt zu implementieren und so deren Wiederverwendung zu fördern. 

  Weitere Informationen finden Sie unter [Entwickeln von EJB-Anwendungen in EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) bzw. [Entwickeln von EJB-Anwendungen in EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Hibernate-Anwendungen** Entwickler und Administratoren können mit JBoss EAP Anwendungen vom Typ „Java Persistence API (JPA)“ und „Hibernate“ entwickeln und bereitstellen. Hibernate Core ist ein Framework für die objektrelationale Zuordnung für die Java-Sprache. Hiermit kann ein objektorientiertes Domänenmodell einer relationalen Datenbank zugeordnet werden, damit Anwendungen die direkte Interaktion mit der Datenbank vermeiden können. 

  Mit Hibernate Entity Manager werden die Programmierschnittstellen und Lebenszyklusregeln implementiert, die in der [JPA 2.1-Spezifikation](https://www.jcp.org/en/jsr/overview) definiert sind. Zusammen mit Hibernate Annotations implementiert dieser Wrapper zusätzlich zum ausgereiften Hibernate Core-Framework eine vollständige (und eigenständige) JPA-Lösung. 
  
  Weitere Informationen zu Hibernate finden Sie unter [JPA unter EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) bzw. [Jakarta Persistence unter EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
Das [Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) ist ein Migrationstool für Java-Anwendungsserver. Verwenden Sie dieses Tool für die Migration von einem anderen App-Server zu JBoss EAP. Hierfür können Sie IDE-Plug-Ins für [Eclipse IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) und [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) für Java verwenden. 

MTA ist ein kostenloses Open-Source-Tool mit folgenden Features:
* Automatisierung der Anwendungsanalyse
* Schätzung des Supportaufwands
* Beschleunigung der Codemigration
* Unterstützung der Containerisierung
* Integration in Azure Workload Builder

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrieren von JBoss EAP aus der lokalen Umgebung zu Azure
Das Azure Marketplace-Angebot von JBoss EAP unter RHEL wird auf Azure-VMs in weniger als 20 Minuten installiert und bereitgestellt. Sie können auf diese Angebote im [Azure Marketplace](https://azuremarketplace.microsoft.com/) zugreifen.

Dieses Azure Marketplace-Angebot umfasst verschiedene Kombinationen von EAP- und RHEL-Versionen, damit Ihre jeweiligen Anforderungen erfüllt werden. JBoss EAP ist immer BYOS, aber für das RHEL-Betriebssystem können Sie zwischen BYOS oder PAYG wählen. Das Azure Marketplace-Angebot umfasst Planoptionen für JBoss EAP unter RHEL in Form von eigenständigen oder gruppierten VMs:

* JBoss EAP 7.2 auf RHEL 7.7-VM (nutzungsbasiert)
* JBoss EAP 7.2 auf RHEL 8.0-VM (nutzungsbasiert)
* JBoss EAP 7.3 auf RHEL 8.0-VM (nutzungsbasiert)
* JBoss EAP 7.2 auf RHEL 7.7-VM (BYOS)
* JBoss EAP 7.2 auf RHEL 8.0-VM (BYOS)
* JBoss EAP 7.3 auf RHEL 8.0-VM (BYOS)

Zusammen mit Azure Marketplace-Angeboten können Sie Schnellstartvorlagen verwenden, damit Sie mit Ihrer Azure-Migrationsjourney beginnen können. Diese Schnellstarts enthalten vordefinierte ARM-Vorlagen (Azure Resource Manager) und Skripts zum Bereitstellen von JBoss EAP unter RHEL mit verschiedenen Konfigurationen und Versionskombinationen. Sie erhalten hierbei Folgendes:

* Ein Load Balancer
* Eine private IP-Adresse für Lastenausgleich und VMs
* Ein virtuelles Netzwerk mit einem einzelnen Subnetz
* Eine VM-Konfiguration (Cluster oder eigenständig)
* Eine Java-Beispielanwendung

Die Lösungsarchitektur für diese Vorlagen umfasst Folgendes:

* JBoss-EAP auf einem eigenständigen virtuellen RHEL-Computer
* Gruppierung von JBoss EAP auf mehreren virtuellen RHEL-Computern
* Gruppierung von JBoss EAP über Azure-VM-Skalierungsgruppen

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migration der Linux-Workload für JBoss EAP
Mit Azure Workload Builder wird der Proof-of-Concept-, Evaluierungs- und Migrationsprozess für lokale Java-Apps zu Azure vereinfacht. Workload Builder ist in das Ermittlungstool von Azure Migrate integriert, um JBoss EAP-Server identifizieren zu können. Anschließend wird das Ansible-Playbook für die JBoss EAP-Serverbereitstellung dynamisch generiert. Hierbei wird das MTA-Tool von Red Hat verwendet, um Server von anderen App-Servern zu JBoss EAP zu migrieren. 

Die Schritte zur Vereinfachung der Migration umfassen Folgendes:
1. **Auswertung**: Evaluieren Sie JBoss EAP-Cluster mithilfe einer Azure-VM oder einer VM-Skalierungsgruppe.
1. **Bewertung**: Überprüfen Sie Anwendungen und Infrastrukturen.
1. **Konfiguration der Infrastruktur**: Erstellen Sie ein Profil für die Workload.
1. **Bereitstellung und Tests** Stellen Sie die Workload bereit, und migrieren und testen Sie sie anschließend.
1. **Konfiguration nach der Bereitstellung**: Integration mit Daten, Überwachung, Sicherheit, Sicherung und mehr.

## <a name="server-configuration-choice"></a>Auswahl für Serverkonfiguration

Für die Bereitstellung des virtuellen RHEL-Computers können Sie entweder PAYG oder BYOS auswählen. Für Images aus dem [Azure Marketplace](https://azuremarketplace.microsoft.com) wird standardmäßig die nutzungsbasierte Zahlung (PAYG) verwendet. Stellen Sie einen virtuellen RHEL-Computer mit BYOS bereit, falls Sie über ein eigenes RHEL-Betriebssystemimage verfügen. Stellen Sie sicher, dass Ihr RHSM-Konto über eine BYOS-Berechtigung per Cloud Access verfügt, bevor Sie die VM oder die VM-Skalierungsgruppe bereitstellen.

JBoss EAP enthält leistungsstarke Verwaltungsfunktionen sowie Funktionen und APIs für seine Anwendungen. Diese Verwaltungsfunktionen unterscheiden sich in Abhängigkeit davon, welcher Betriebsmodus zum Starten von JBoss EAP verwendet wird. Unterstützung ist für RHEL und Windows Server vorhanden. JBoss EAP verfügt über einen Betriebsmodus für eigenständige Server, um die Verwaltung diskreter Instanzen zu ermöglichen. Darüber hinaus ist auch ein Betriebsmodus vom Typ „Verwaltete Domäne“ verfügbar, in dem Gruppen von Instanzen von einem zentralen Kontrollpunkt aus verwaltet werden können. 

> [!NOTE]
> Mit JBoss EAP verwaltete Domänen werden in Microsoft Azure nicht unterstützt, da die Azure-Infrastrukturdienste das Feature für Hochverfügbarkeit verwalten. 

Die Umgebungsvariable `EAP_HOME` gibt den Pfad zur JBoss EAP-Installation an. Verwenden Sie den folgenden Befehl, um den JBoss EAP-Dienst im eigenständigen Modus zu starten:

```
$EAP_HOME/bin/standalone.sh
```
    
In diesem Startskript wird die Datei „EAP_HOME/bin/standalone.conf“ zum Festlegen einiger Standardeinstellungen verwendet, z. B. JVM-Optionen. Sie können die Einstellungen in dieser Datei anpassen. JBoss EAP verwendet standardmäßig die Konfigurationsdatei „standalone.xml“ zum Starten im eigenständigen Modus, aber Sie können zum Starten auch einen anderen Modus verwenden. 

Ausführliche Informationen zu den verfügbaren eigenständigen Konfigurationsdateien und deren Verwendung finden Sie unter [Konfigurationsdateien für eigenständige Server für EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) bzw. [Konfigurationsdateien für eigenständige Server für EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

Verwenden Sie das Argument `--server-config`, um JBoss EAP mit einer anderen Konfiguration zu starten. Beispiel:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Verwenden Sie das Argument `--help`, um eine vollständige Liste aller verfügbaren Startskriptargumente und ihrer Zwecke zu erhalten. Weitere Informationen finden Sie unter [Serverlaufzeitargumente in EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) oder [Serverlaufzeitargumente in EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP kann auch im Clustermodus ausgeführt werden. Das JBoss EAP-Clustermessaging ermöglicht die Gruppierung von JBoss EAP-Messagingservern zur gemeinsamen Verarbeitung der Nachrichtenworkload. Bei jedem aktiven Knoten im Cluster handelt es sich um einen aktiven JBoss EAP-Messagingserver, der seine eigenen Nachrichten verwaltet und seine eigenen Verbindungen verarbeitet. Weitere Informationen finden Sie unter [Clusterübersicht in EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) oder [Clusterübersicht in EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Hinweise zum Support und zu Abonnements
Diese Schnellstartvorlagen werden wie folgt angeboten: 

- Das RHEL-Betriebssystem wird mit nutzungsbasierter Zahlung oder BYOS basierend auf dem Red Hat Gold Image-Modell angeboten.
- JBoss EAP wird nur mit BYOS angeboten.

#### <a name="using-rhel-os-with-the-payg-model"></a>Verwenden des RHEL-Betriebssystems mit nutzungsbasierter Zahlung (PAYG)

Standardmäßig wird für diese Schnellstartvorlagen das On-Demand-Image RHEL 7.7 oder 8.0 mit nutzungsbasierter Zahlung aus dem Azure Marketplace verwendet. Für Images mit nutzungsbasierter Zahlung fallen stündliche Gebühren für RHEL-Abonnements zusätzlich zu den üblichen Compute-, Netzwerk- und Speicherkosten an. Die Instanz wird gleichzeitig auch unter Ihrem Red Hat-Abonnement registriert. Dies bedeutet, dass Sie eine Ihrer Berechtigungen verwenden. 

Dieses Image mit nutzungsbasierter Zahlung führt zu einer „doppelten Abrechnung“. Sie können dieses Problem vermeiden, indem Sie Ihr eigenes RHEL-Image erstellen. Weitere Informationen finden Sie bei Red Hat im KB-Artikel (Wissensdatenbank) zum Thema [Bereitstellen einer RHEL-VM für Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Sie können auch Ihr RHEL Gold Image für [Red Hat Cloud Access](https://access.redhat.com/) aktivieren.

Ausführliche Informationen zu den Preisen für VMs mit nutzungsbasierter Zahlung finden Sie unter [Red Hat Enterprise Linux – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Für die Nutzung von RHEL mit dem Modell für nutzungsbasierte Zahlung benötigen Sie ein Azure-Abonnement mit der angegebenen Zahlungsmethode für [RHEL 7.7 im Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) bzw. [RHEL 8.0 im Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Bei diesen Angeboten muss eine Zahlungsmethode im Azure-Abonnement angegeben werden.

#### <a name="using-rhel-os-with-the-byos-model"></a>Verwenden des RHEL-Betriebssystems mit dem BYOS-Modell

Für die Verwendung von BYOS für das RHEL-Betriebssystem müssen Sie über ein gültiges Red Hat-Abonnement mit Berechtigungen zur Nutzung des RHEL-Betriebssystems in Azure verfügen. Erfüllen Sie die folgenden Voraussetzungen, bevor Sie das RHEL-Betriebssystem mit dem BYOS-Modell bereitstellen:

1. Stellen Sie sicher, dass Sie unter Ihrem Red Hat-Abonnement über Berechtigungen für das RHEL-Betriebssystem und für JBoss EAP verfügen.
2. Führen Sie für Ihre Azure-Abonnement-ID die Autorisierung zur Nutzung von RHEL-Images mit BYOS durch. Halten Sie sich an die [RHSM-Dokumentation (Red Hat Subscription Management)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs), um diesen Prozess durchzuführen. Dies umfasst die folgenden Schritte:

   1. Aktivieren Sie Microsoft Azure in Ihrem Red Hat Cloud Access-Dashboard als Anbieter.

   1. Fügen Sie Ihre Azure-Abonnement-IDs hinzu.

   1. Aktivieren Sie neue Produkte für Cloud Access unter Microsoft Azure.
    
   1. Aktivieren Sie Red Hat Gold Images für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Red Hat Gold-Images unter Microsoft Azure](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).

   1. Warten Sie ab, bis die Red Hat Gold Images in Ihrem Azure-Abonnement verfügbar sind. Diese Images sind normalerweise innerhalb von drei Stunden nach der Übermittlung der Daten verfügbar.
    
3. Akzeptieren Sie die Azure Marketplace-Geschäftsbedingungen für RHEL-Images mit BYOS. Sie können diesen Prozess abschließen, indem Sie die folgenden Azure CLI-Befehle ausführen. Weitere Informationen finden Sie in der Dokumentation unter [Bring-Your-Own-Subscription-Gold-Images für Red Hat Enterprise Linux in Azure](./byos.md). Es ist wichtig, dass Sie die neueste Azure CLI-Version ausführen.

   1. Öffnen Sie eine Azure CLI-Sitzung, und führen Sie die Authentifizierung für Ihr Azure-Konto durch. Weitere Informationen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).

   1. Vergewissern Sie sich, dass die RHEL-Images mit BYOS unter Ihrem Abonnement verfügbar sind, indem Sie den folgenden CLI-Befehl ausführen. Falls Sie hier keine Ergebnisse erhalten, sollten Sie sicherstellen, dass Ihr Azure-Abonnement für RHEL-Images mit BYOS aktiviert ist.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Führen Sie den folgenden Befehl aus, um die Azure Marketplace-Geschäftsbedingungen für RHEL 7.7 BYOS bzw. RHEL 8.0 BYOS zu akzeptieren:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Ihr Abonnement ist nun für die Bereitstellung von RHEL 7.7 oder 8.0 mit BYOS auf virtuellen Azure-Computern bereit.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Verwenden von JBoss EAP mit dem BYOS-Modell

In Azure ist JBoss EAP nur mit dem BYOS-Modell verfügbar. Beim Bereitstellen dieser Vorlage müssen Sie Ihre RHSM-Anmeldeinformationen mit der RHSM-Pool-ID mit gültigen EAP-Berechtigungen angeben. Wenn Sie nicht über EAP-Berechtigungen verfügen, sollten sie sich zunächst ein [JBoss EAP-Evaluierungsabonnement](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) beschaffen.

## <a name="deployment-options"></a>Bereitstellungsoptionen

Sie können die Vorlage auf die folgenden Arten bereitstellen:

- **PowerShell**: Stellen Sie die Vorlage mithilfe der folgenden Befehle bereit: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Informationen zur Installation und Konfiguration von Azure PowerShell finden Sie in der [PowerShell-Dokumentation](/powershell/azure/).  

- **Azure-Befehlszeilenschnittstelle**. Stellen Sie die Vorlage mithilfe der folgenden Befehle bereit:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Ausführliche Informationen zur Installation und Konfiguration der Azure CLI finden Sie unter [Installieren der Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

- **Azure-Portal**. Für die Bereitstellung über das Azure-Portal können Sie die Azure-Schnellstartvorlagen verwenden (wie im nachfolgenden Abschnitt beschrieben). Wählen Sie dann in der Schnellstartanleitung die Schaltfläche **In Azure bereitstellen** oder **Auf GitHub suchen** aus.

## <a name="azure-quickstart-templates"></a>Azure-Schnellstartvorlagen

Sie können zunächst eine der folgenden Schnellstartvorlagen für JBoss EAP unter RHEL verwenden, die Ihrem Bereitstellungsziel entspricht:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP unter RHEL (eigenständige VM)</a>. Hiermit wird eine Webanwendung namens „JBoss-EAP“ unter Azure mit JBoss EAP 7.2 oder 7.3 bereitgestellt, das auf einer VM mit RHEL 7.7 oder 8.0 ausgeführt wird.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP unter RHEL (mehrere gruppierte VMs)</a> Hiermit wird eine Webanwendung mit dem Namen „eap-session-replication“ auf einem JBoss EAP 7.2- oder 7.3-Cluster bereitgestellt, der auf *n* VMs (Wert n) mit RHEL 7.7 oder 8.0 ausgeführt wird. Der Benutzer bestimmt den Wert *n*. Alle VMs werden dem Back-End-Pool eines Lastenausgleichs hinzugefügt.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP unter RHEL (gruppierte VM-Skalierungsgruppe)</a> Hiermit wird eine Webanwendung namens „eap-session-replication“ auf einem JBoss EAP 7.2- oder 7.3-Cluster bereitgestellt, der in VM-Skalierungsgruppen mit RHEL 7.7 oder 8.0 ausgeführt wird.

## <a name="resource-links"></a>Ressourcenlinks

* [Azure-Hybridvorteil](../../windows/hybrid-use-benefit-licensing.md)
* [Konfigurieren einer Java-App für Azure App Service](../../../app-service/configure-language-java.md)
* [JBoss EAP unter Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP unter Azure App Service Linux](../../../app-service/quickstart-java.md)
* [Bereitstellen von JBoss EAP unter Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen: [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Weitere Informationen: [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Weitere Informationen: [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management)
* Weitere Informationen: [Red Hat-Workloads unter Azure](./overview.md)
* Bereitstellung: [JBoss EAP für RHEL-VM oder -VM-Skalierungsgruppe über Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Bereitstellung: [JBoss EAP für RHEL-VM oder -VM-Skalierungsgruppe über Azure-Schnellstartvorlagen](https://aka.ms/Quickstart-JBoss-EAP)