---
title: Bereitstellen von Avere vFXT für Azure
description: Schritte zum Bereitstellen des Avere vFXT-Clusters in Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: v-erkell
ms.openlocfilehash: 7ded66c29f12b8f68746726ca6c126bffbc51f0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60410270"
---
# <a name="deploy-the-vfxt-cluster"></a>Bereitstellen des vFXT-Clusters

Dieses Verfahren führt Sie durch die Verwendung des Bereitstellungsassistenten aus dem Azure Marketplace. Der Assistent stellt den Cluster automatisch mit einer Azure Resource Manager-Vorlage bereit. Nachdem Sie die Parameter in das Formular eingegeben und auf **Erstellen** geklickt haben, führt Azure diese Schritte automatisch aus:

* Erstellen des Clustercontrollers, der eine einfache VM ist, die die Software enthält, die für die Bereitstellung und Verwaltung des Clusters erforderlich ist.
* Einrichten der Ressourcengruppe und der virtuellen Netzwerkinfrastruktur, einschließlich der Erstellung der neuen Elemente.
* Erstellen der Clusterknoten-VMs, und ihre Konfiguration als Avere-Cluster.
* Erstellen eines neuen Azure Blob-Containers, und seine Konfiguration als Cluster-Kernspeichereinheit, falls erforderlich.

Nachdem Sie die Anweisungen in diesem Dokument befolgt haben, verfügen Sie über ein virtuelles Netzwerk, ein Subnetz, einen Controller und einen vFXT-Cluster, wie in der folgenden Abbildung dargestellt. Diese zeigt die optionalen Azure Blob-Kernspeichereinheit, die einen neuen Blob-Speichercontainer enthält, der in einem nicht dargestellten Speicherkonto enthalten ist, und einen Dienstendpunkt für Microsoft Storage innerhalb des Subnetzes. 

![Screenshot: Abbildung mit drei konzentrischen Rechtecken mit Avere-Clusterkomponenten. Das äußere Rechteck trägt die Bezeichnung „Ressourcengruppe“ und enthält ein Sechseck mit der Bezeichnung „Blob-Speicher (optional)“. Das mittlere Rechteck trägt die Bezeichnung „Virtuelles Netzwerk: 10.0.0.0/16“ und enthält selbst keine spezifischen Komponenten. Das innerste Rechteck trägt die Bezeichnung „Subnetz:10.0.0.0/24“ und enthält eine VM mit der Bezeichnung „Clustercontroller“, einen Stapel aus drei VMs mit der Bezeichnung „vFXT-Knoten (vFXT-Cluster)“, und ein Sechseck mit der Bezeichnung „Dienstendpunkt“. Ein Pfeil verbindet den Dienstendpunkt, der sich innerhalb des Subnetzes befindet, mit dem Blob-Speicher, der sich in der Ressourcengruppe außerhalb des Subnetzes und außerhalb von „vnet“ befindet. Der Pfeil überschneidet dabei jeweils die rechte Seite der Rechtecke für das Subnetz und das virtuellen Netzwerk.](media/avere-vfxt-deployment.png)  

Vergewissern Sie sich vor der Verwendung der Erstellungsvorlage, dass diese Voraussetzungen erfüllt sind:  

1. [Neues Abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Berechtigungen des Abonnementbesitzers](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kontingent für den vFXT-Cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Storage-Dienstendpunkt (bei Bedarf)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – Wird für Bereitstellungen benötigt, bei der ein vorhandenes virtuelles Netzwerk verwendet und ein Blob-Speicher erstellt wird.

Weitere Informationen zu den Schritten und der Planung der Clusterbereitstellung finden Sie unter [Planen Ihres Avere vFXT-Systems](avere-vfxt-deploy-plan.md) und [Übersicht über die Bereitstellung](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Erstellen des Avere vFXT for Azure

Rufen sie die Erstellungsvorlage im Azure-Portal auf, indem Sie nach Avere suchen und „Avere vFXT für Azure ARM-Vorlage“ auswählen. 

![Browserfenster mit dem Azure-Portal mit Breadcrumbs „Neu > Marketplace > Alles“. Auf der Seite „Alles“ enthält das Suchfeld den Begriff „avere“ und das zweite Ergebnis, „Avere vFXT für Azure ARM-Vorlage“, ist rot hervorgehoben.](media/avere-vfxt-template-choose.png)

Nachdem Sie die Details auf der Seite „Avere vFXT für Azure ARM-Vorlage“ gelesen haben, klicken Sie auf **Erstellen**, um zu beginnen. 

![Azure Marketplace mit der Anzeige der ersten Seite der Bereitstellungvorlage](media/avere-vfxt-deploy-first.png)

Die Vorlage ist in vier Schritte unterteilt – zwei Seiten zur Informationsbeschaffung sowie Validierungs- und Bestätigungsschritte. 

* Seite 1 konzentriert sich auf die Einstellungen für die Clustercontroller-VM. 
* Seite 2 sammelt Parameter für die Erstellung des Clusters und der zugehörigen Ressourcen wie Subnetze und Speicher. 
* Auf Seite 3 werden die Einstellungen zusammengefasst und die Konfiguration überprüft. 
* Seite 4 erläutert die Softwarebedingungen und ermöglicht es Ihnen, den Erstellungsprozess des Clusters zu starten. 

## <a name="page-one-parameters---cluster-controller-information"></a>Parameter auf Seite 1 – Clustercontrollerinformationen

Die erste Seite der Bereitstellungsvorlage sammelt Informationen über den Clustercontroller. 

![Erste Seite der Bereitstellungsvorlage](media/avere-vfxt-deploy-1.png)

Geben Sie die folgenden Informationen ein:

* **Name des Clustercontrollers** – Legen Sie den Namen für die Clustercontroller-VM fest.

* **Controllerbenutzername** – Geben Sie den Root-Benutzernamen für die Clustercontroller-VM ein. 

* **Authentifizierungstyp** – Wählen Sie entweder das Kennwort oder die Authentifizierung mit öffentlichem SSH-Schlüssel für die Verbindung mit dem Controller. Die Authentifizierungsmethode mit öffentlichem SSH-Schlüssel wird empfohlen. Lesen Sie dazu [Erstellen und Verwenden von SSH-Schlüsseln](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows), wen Sie Hilfe benötigen.

* **Kennwort** oder **Öffentlicher SSH-Schlüssel** – Je nach Authentifizierungstyp, den Sie ausgewählt haben, müssen Sie einen öffentlichen RSA-Schlüssel oder ein Kennwort in den nächsten Feldern angeben. Diese Anmeldeinformationen werden in Kombination mit dem Benutzernamen verwenden, der zuvor bereitgestellt wurde.

* **Abonnement** – Wählen Sie das Avere vFXT-Abonnement aus. 

* **Ressourcengruppe** – Wählen Sie eine vorhandene leere Ressourcengruppe für den Avere vFXT-Cluster aus, oder klicken Sie auf „Neu erstellen“, und geben Sie einen neuen Ressourcengruppennamen ein. 

* **Speicherort** – Wählen Sie den Azure-Speicherort für Ihren Cluster und die Ressourcen.

Klicken Sie anschließend auf **OK**. 

> [!NOTE]
> Wenn Sie möchten, dass der Clustercontroller eine öffentlich zugängliche IP-Adresse hat, erstellen Sie ein neues virtuelles Netzwerk für den Cluster, anstatt ein bestehendes Netzwerk auszuwählen. Diese Einstellung finden Sie auf der zweiten Seite.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parameter auf Seite 2 – vFXT-Clusterinformationen

Auf der zweiten Seite der Bereitstellungsvorlage können Sie unter anderem Clustergröße, Knotentyp, Cachegröße und Speicherparameter festlegen. 

![Zweite Seite der Bereitstellungsvorlage](media/avere-vfxt-deploy-2.png)

* **Anzahl der Avere vFXT-Clusterknoten** – Wählen Sie die Anzahl der zu verwendenden Knoten im Cluster aus. Der Mindestwert ist drei Knoten, der Maximalwert 12. 

* **Cluster-Verwaltungskennwort** – Erstellen Sie das Kennwort für die Clusterverwaltung. Dieses Passwort wird mit dem Benutzernamen ```admin``` verwendet, um sich bei der Clustersteuerung anzumelden, um den Cluster zu überwachen und Einstellungen zu konfigurieren.

* **Avere vFXT-Clustername** – Geben Sie dem Cluster einen eindeutigen Namen. 

* **Größe** – Dieser Bereich zeigt den VM-Typen an, der für die Clusterknoten verwendet wird. Obwohl es nur eine empfohlene Option gibt, öffnet der Link **Größe ändern** eine Tabelle mit Details zu diesem Instanztyp und einen Link zu einem Preisrechner.  

* **Cachegröße pro Knoten** – Der Clustercache ist auf die Clusterknoten verteilt, sodass die gesamte Cachegröße auf Ihrem Avere vFXT-Cluster die Cachegröße pro Knoten multipliziert mit der Anzahl der Knoten ist. 

  Die empfohlene Konfiguration sind 4 TB pro Knoten für Standard_E32s_v3-Knoten.

* **Virtuelles Netzwerk** – Definieren Sie ein neues VNET als Host für den Cluster, oder wählen Sie ein vorhandenes VNET aus, das den Voraussetzungen entspricht, die in [Planen des Avere vFXT-Systems](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure) beschrieben sind. 

  > [!NOTE]
  > Wenn Sie ein neues VNet erstellen, wird dem Clustercontroller eine öffentliche IP-Adresse zugewiesen, sodass Sie auf das neue private Netzwerk zugreifen können. Wenn Sie ein bestehendes Vnet auswählen, wird der Clustercontroller ohne eine öffentliche IP-Adresse konfiguriert. 
  > 
  > Eine öffentlich sichtbare IP-Adresse auf dem Clustercontroller ermöglicht einen einfacheren Zugriff auf den vFXT-Cluster, birgt aber ein leichtes Sicherheitsrisiko. 
  >  * Eine öffentliche IP-Adresse auf dem Clustercontroller ermöglicht es Ihnen, ihn als Jump-Host zu verwenden, um sich von außerhalb des privaten Subnetzes mit dem Avere vFXT-Cluster zu verbinden.
  >  * Wenn Sie keine öffentliche IP-Adresse auf dem Controller einrichten, müssen Sie für den Zugriff auf den Cluster einen anderen Jump-Host, eine VPN-Verbindung oder ExpressRoute verwenden. Erstellen Sie z. B. den Controller in einem virtuellen Netzwerk, in dem bereits eine VPN-Verbindung konfiguriert ist.
  >  * Wenn Sie einen Controller mit einer öffentlichen IP-Adresse erstellen, sollten Sie den virtuellen Controllercomputer mit einer Netzwerksicherheitsgruppe schützen. Standardmäßig erstellt die Avere vFXT for Azure-Bereitstellung eine Netzwerksicherheitsgruppe und beschränkt den eingehenden Zugriff nur auf Port 22 für Controller mit öffentlichen IP-Adressen. Sie können das System weiter schützen, indem Sie den Zugriff auf Ihren Bereich von IP-Quelladressen sperren, d.h. nur Verbindungen von Computern erlauben, die Sie für den Clusterzugriff verwenden möchten.

  Die Bereitstellungsvorlage konfiguriert auch das neue VNET mit einem Speicherdienstendpunkt für Azure Blob Storage und mit einer Netzwerkzugriffssteuerung, die nur für IPs des Clustersubnetzes gesperrt ist. 

* **Subnetz** – Wählen Sie ein Subnetz aus Ihrem vorhandenen virtuellen Netzwerk aus, oder erstellen Sie ein neues. 

* **Create and use blob storage** (Blob-Speicher erstellen und verwenden) – Wählen Sie **true** aus, um einen neuen Azure Blob-Container zu erstellen und ihn als Back-End-Speicher für den neuen Avere vFXT-Cluster zu konfigurieren. Diese Option erstellt auch ein neues Speicherkonto innerhalb der Ressourcengruppe des Clusters sowie einen Microsoft Storage Service-Endpunkt innerhalb des Clustersubnetzes. 
  
  Wenn Sie ein vorhandenes virtuelles Netzwerk zur Verfügung stellen, muss es einen Speicherdienstendpunkt haben, bevor Sie den Cluster erstellen. Weitere Informationen finden Sie unter [Planen des Avere vFXT-Systems](avere-vfxt-deploy-plan.md).

  Legen Sie für dieses Feld **false** fest, wenn Sie keinen neuen Container erstellen möchten. In diesem Fall müssen Sie den Speicher nach dem Erstellen des Clusters anfügen und konfigurieren. Anweisungen finden Sie unter [Konfigurieren von Speicher](avere-vfxt-add-storage.md). 

* **(New) Storage account** ((Neues )Speicherkonto) – Wenn Sie einen neuen Azure Blob-Container erstellen, geben Sie einen Namen für das neue Speicherkonto ein. 

## <a name="validation-and-purchase"></a>Überprüfung und Kauf

Auf Seite 3 wird die Konfiguration zusammengefasst und die Parameter werden überprüft. Wenn die Überprüfung erfolgreich war, klicken Sie auf **OK**, um fortzufahren. 

![Dritte Seite der Bereitstellungsvorlage – Überprüfung](media/avere-vfxt-deploy-3.png)

Geben Sie auf Seite 4 alle erforderlichen Kontaktinformationen ein, und klicken Sie auf die Schaltfläche **Erstellen**, um den Bedingungen zustimmen, und erstellen die Avere vFXT for Azure-Cluster. 

![Vierte Seite der Bereitstellungsvorlage – Bedingungen, Schaltfläche „Erstellen“](media/avere-vfxt-deploy-4.png)

Die Bereitstellung des Clusters dauert 15 bis 20 Minuten.

## <a name="gather-template-output"></a>Sammeln der Vorlagenausgabe

Wenn die Avere vFXT-Vorlage die Erstellung des Clusters abgeschlossen hat, gibt sie einige wichtige Informationen über den neuen Cluster aus. 

> [!TIP]
> Stellen Sie sicher, dass Sie die Verwaltungs-IP-Adresse aus der Vorlagenausgabe kopieren. Sie benötigen diese Adresse zum Verwalten des Clusters.

Gehen Sie folgendermaßen vor, um diese Informationen zu suchen:

1. Navigieren Sie zu der Ressourcengruppe für Ihren Clustercontroller.

1. Klicken Sie auf der linken Seite auf **Bereitstellungen** und dann auf **microsoft-avere.vfxt-template**.

   ![Portalseite „Ressourcengruppe“ mit auf der linken Seite ausgewählten Bereitstellungen und der Anzeige von „microsoft-avere.vfxt-template“ in einer Tabelle unter dem Bereitstellungsnamen](media/avere-vfxt-outputs-deployments.png)

1. Klicken Sie auf der linken Seite auf **Ausgaben**. Kopieren Sie die Werte in die einzelnen Felder. 

   ![Ausgabeseite mit der Anzeige von Werten für SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs und MGMT_IP in Feldern rechts neben den Bezeichnungen](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Nächster Schritt

Nachdem der Cluster ausgeführt wird und Sie seine Verwaltungs-IP-Adresse kennen, können Sie sich mit dem [Clusterkonfigurationstool verbinden](avere-vfxt-cluster-gui.md), um den Support zu aktivieren, bei Bedarf Speicher hinzuzufügen und andere Clustereinstellungen anzupassen.
