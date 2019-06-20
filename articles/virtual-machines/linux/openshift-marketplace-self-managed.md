---
title: Bereitstellen eines selbstverwalteten OpenShift Container Platform-Marketplace-Angebots in Azure | Microsoft-Dokumentation
description: Bereitstellen eines selbstverwalteten OpenShift Container Platform-Marketplace-Angebots in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 9b981924dcaf715dd1d05d452b756a40b63f8dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233084"
---
# <a name="configure-prerequisites"></a>Konfigurieren der Voraussetzungen

Vor dem Verwenden des Marketplace-Angebots zum Bereitstellen eines selbstverwalteten OpenShift Container Platform-Clusters in Azure sind einige erforderliche Komponenten zu konfigurieren.  Im Artikel zu [erforderlichen Komponenten für OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) finden Sie Anweisungen zum Erstellen eines SSH-Schlüssels (ohne Passphrase), des Azure-Schlüsseltresors, des Schlüsseltresor-Geheimnisses und eines Dienstprinzipals.

 
## <a name="deploy-using-the-marketplace-offer"></a>Bereitstellen mithilfe des Marketplace-Angebots

Die einfachste Möglichkeit, einen selbstverwalteten OpenShift Container Platform-Cluster in Azure bereitzustellen, stellt das [Azure Marketplace-Angebot](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview) dar.

Dies ist zwar die einfachste Option, sie bietet jedoch begrenzte Anpassungsmöglichkeiten. Das Marketplace-Angebot stellt OpenShift Container Platform 3.11.82 bereit und umfasst die folgenden Konfigurationsoptionen:

- **Masterknoten**: Drei Masterknoten mit konfigurierbarem Instanztyp.
- **Infrastrukturknoten:** Drei Infrastrukturknoten mit konfigurierbarem Instanztyp.
- **Knoten**: Knotenanzahl (zwischen eins und neun) und Instanztyp sind konfigurierbar.
- **Datenträgertyp**: Managed Disks wird verwendet.
- **Netzwerk**: Unterstützung eines neuen oder bereits vorhandenen Netzwerks sowie eines benutzerdefinierten CIDR-Bereichs.
- **CNS:** CNS kann aktiviert werden.
- **Metriken**: Hawkular Metrics kann aktiviert werden.
- **Protokollierung:** EFK-Protokollierung kann aktiviert werden.
- **Azure-Cloudanbieter**: Standardmäßig aktiviert, kann deaktiviert werden.

Klickern Sie oben links im Azure-Portal auf **Ressource erstellen**, geben Sie im Suchfeld „openshift container platform“ ein, und drücken Sie die EINGABETASTE.

   ![Suchfeld für neue Ressource](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Auf der Ergebnisseite ist **Red Hat OpenShift Container Platform Self-Managed** in der Liste aufgeführt. 

   ![Suchergebnis für neue Ressourcen](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Klicken Sie auf das Angebot, um die Details zum Angebot anzuzeigen. Klicken Sie zum Bereitstellen dieses Angebots auf **Erstellen**. Die Benutzeroberfläche zum Eingeben der erforderlichen Parameter wird geöffnet. Der erste Bildschirm ist das Blatt **Grundlagen**.

   ![Titelseite des Angebots](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Grundlagen**

Wenn Sie Hilfe zu einem der Eingabeparameter benötigen, zeigen Sie mit dem Mauszeiger auf das ***i*** neben dem betreffenden Parameternamen.

Geben Sie Werte für die Eingabeparameter ein, und klicken Sie auf **OK**.

| Eingabeparameter | Beschreibung des Parameters |
|-----------------------|-----------------|
| VM Admin User Name (VM-Administratorbenutzername) | Der auf allen VM-Instanzen zu erstellende Administratorbenutzer |
| SSH Public Key for Admin User (Öffentlicher SSH-Schlüssel für Administratorbenutzer) | Öffentlicher SSH-Schlüssel zum Anmelden bei der VM – darf keine Passphrase aufweisen |
| Abonnement | Azure-Abonnement, in dem der Cluster bereitgestellt werden soll |
| Ressourcengruppe | Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene leere Ressourcengruppe für Clusterressourcen aus. |
| Location | Azure-Region, in der der Cluster bereitgestellt werden soll |

   ![Blatt „Grundlagen“ für Angebot](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Infrastructure Settings** (Infrastruktureinstellungen)

Geben Sie Werte für die Eingabeparameter ein, und klicken Sie auf **OK**.

| Eingabeparameter | Beschreibung des Parameters |
|-----------------------|-----------------|
| OCP Cluster Name Prefix (OCP-Clusternamenspräfix) | Cluster-Präfix, mit dem Hostnamen für alle Knoten konfiguriert werden. Zwischen 1 und 20 Zeichen |
| Master Node Size (Größe des Masterknotens) | Übernehmen Sie die VM-Standardgröße, oder klicken Sie auf **Größe ändern**, um eine andere VM-Größe auszuwählen.  Wählen Sie eine für Ihre Workload geeignete VM-Größe aus |
| Infrastructure Node Size (Größe des Infrastruktur-Knotens) | Übernehmen Sie die VM-Standardgröße, oder klicken Sie auf **Größe ändern**, um eine andere VM-Größe auszuwählen.  Wählen Sie eine für Ihre Workload geeignete VM-Größe aus |
| Number of Application Nodes (Anzahl der Anwendungsknoten) | Übernehmen Sie die VM-Standardgröße, oder klicken Sie auf **Größe ändern**, um eine andere VM-Größe auszuwählen.  Wählen Sie eine für Ihre Workload geeignete VM-Größe aus |
| Application Node Size (Größe der Anwendungsknoten) | Übernehmen Sie die VM-Standardgröße, oder klicken Sie auf **Größe ändern**, um eine andere VM-Größe auszuwählen.  Wählen Sie eine für Ihre Workload geeignete VM-Größe aus |
| Bastion Host Size (Größe des Bastion-Hosts) | Übernehmen Sie die VM-Standardgröße, oder klicken Sie auf **Größe ändern**, um eine andere VM-Größe auszuwählen.  Wählen Sie eine für Ihre Workload geeignete VM-Größe aus |
| New or Existing Virtual Network (Neues oder vorhandenes virtuelles Netzwerk) | Erstellen Sie ein neues VNET (Standardeinstellung), oder verwenden Sie ein vorhandenes VNET |
| Choose Default CIDR Settings or customize IP Range (CIDR) (CIDR-Standardeinstellungen auswählen oder IP-Bereich anpassen) | Übernehmen Sie die CIDR-Standardbereiche, oder wählen Sie **Custom IP Range** (Benutzerdefinierter IP-Bereich) aus, und geben Sie benutzerdefinierte CIDR-Informationen ein.  Mit den Standardeinstellungen wird ein VNET mit einem CIDR von 10.0.0.0/14 erstellt, ein Master-Subnetz mit 10.1.0.0/16, ein Infra-Subnetz mit 10.2.0.0/16 und ein Compute- und CNS-Subnetz mit 10.3.0.0/16 |
| Key Vault Resource Group Name (Name der Schlüsseltresor-Ressourcengruppe) | Der Name der Ressourcengruppe, die den Schlüsseltresor enthält |
| Schlüsseltresorname | Der Name des Schlüsseltresors, der das Geheimnis mit dem privaten SSH-Schlüssel enthält.  Es sind nur alphanumerische Zeichen und Bindestriche zulässig, die Länge muss zwischen 3 und 24 Zeichen betragen |
| Geheimnisname | Der Name des Geheimnisses, welches den privaten SSH-Schlüssel enthält.  Es sind nur alphanumerische Zeichen und Bindestriche zulässig |

   ![Blatt „Infrastruktur“ für Angebot](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Größe ändern**

Klicken Sie auf ***Größe ändern***, um eine andere VM-Größe auszuwählen.  Das VM-Auswahlfenster wird geöffnet.  Wählen Sie die gewünschte VM-Größe aus, und klicken Sie auf **Auswählen**.

   ![VM-Größe auswählen](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Vorhandenes virtuelles Netzwerk**

| Eingabeparameter | Beschreibung des Parameters |
|-----------------------|-----------------|
| Existing Virtual Network Name (Name des vorhandenen virtuellen Netzwerks) | Name des vorhandenen VNET |
| Subnet name for master nodes (Subnetzname für Master-Knoten) | Name des vorhandenen Subnetzes für Master-Knoten.  Muss mindestens 16 IP-Adressen enthalten und die Anforderungen von RFC 1918 erfüllen |
| Subnet name for infra nodes (Subnetzname für Infra-Knoten) | Name des vorhandenen Subnetzes für Infra-Knoten.  Muss mindestens 32 IP-Adressen enthalten und die Anforderungen von RFC 1918 erfüllen |
| Subnet name for compute and cns nodes (Subnetzname für Compute- und CNS-Knoten) | Name des vorhandenen Subnetzes für Compute- und CNS-Knoten.  Muss mindestens 32 IP-Adressen enthalten und die Anforderungen von RFC 1918 erfüllen |
| Resource Group for the existing Virtual Network (Ressourcengruppe für das vorhandene virtuelle Netzwerk) | Name der Ressourcengruppe, die das vorhandene VNET enthält |

   ![Angebot – Infrastruktureinstellungen für vorhandenes VNET](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Benutzerdefinierter IP-Bereich**

| Eingabeparameter | Beschreibung des Parameters |
|-----------------------|-----------------|
| Address Range for the Virtual Network (Adressbereich für das virtuelle Netzwerk) | Benutzerdefinierter CIDR für das VNET |
| Address Range for the subnet containing the master nodes (Adressbereich für das Subnetz mit den Master-Knoten) | Benutzerdefinierter CIDR für das Master-Subnetz |
| Address Range for the subnet containing the infrastructure nodes (Adressbereich für das Subnetz mit den Infrastruktur-Knoten) | Benutzerdefinierter CIDR für das Infrastruktur-Subnetz |
| Address Range for subnet containing the compute and cns nodes (Adressbereich für das Subnetz mit den Compute- und CNS-Knoten) | Benutzerdefinierter CIDR für die Compute- und CNS-Knoten |

   ![Angebot – Infrastruktureinstellungen, benutzerdefinierter IP-Bereich](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Geben Sie Werte für die Eingabeparameter ein, und klicken Sie auf **OK**.

| Eingabeparameter | Beschreibung des Parameters |
|-----------------------|-----------------|
| OpenShift Admin User Password (OpenShift-Administratorbenutzerkennwort) | Kennwort für den anfänglichen OpenShift-Benutzer.  Dieser Benutzer ist gleichzeitig der Cluster-Administrator. |
| Confirm OpenShift Admin User Password (OpenShift-Administratorbenutzer-Kennwort bestätigen) | Geben Sie hier das Kennwort des OpenShift-Administratorbenutzers erneut ein |
| Red Hat Subscription Manager User Name (Benutzername für Red Hat-Abonnement-Manager) | Benutzername für den Zugriff auf Ihr Red Hat-Abonnement oder Ihre Organisations-ID.  Mit diesen Anmeldeinformationen erfolgt die Registrierung der RHEL-Instanz bei Ihrem Abonnement; sie werden von Microsoft und Red Hat nicht gespeichert |
| Red Hat Subscription Manager User Password (Benutzerkennwort für Red Hat-Abonnement-Manager) | Kennwort für den Zugriff auf Ihr Red Hat-Abonnement oder Aktivierungsschlüssel.  Mit diesen Anmeldeinformationen erfolgt die Registrierung der RHEL-Instanz bei Ihrem Abonnement; sie werden von Microsoft und Red Hat nicht gespeichert |
| Red Hat Subscription Manager OpenShift Pool ID (OpenShift-Pool-ID für Red Hat-Abonnement-Manager) | Pool-ID, die die OpenShift Container Platform-Berechtigung enthält. Stellen Sie sicher, dass Sie über ausreichende OpenShift Container Platform-Berechtigungen für die Installation des Clusters verfügen |
| Red Hat Subscription Manager OpenShift Pool ID for Broker / Master Nodes (OpenShift-Pool-ID für Red Hat-Abonnement-Manager für Broker-/Master-Knoten) | Pool-ID, die OpenShift Container Platform-Berechtigungen für Broker-/Master-Knoten enthält. Vergewissern Sie sich, dass Sie über ausreichende OpenShift Container Platform-Berechtigungen für die Installation des Clusters verfügen. Wenn keine Broker-/Master-Pool-ID verwendet wird, geben Sie die Pool-ID für Anwendungsknoten ein |
| Configure Azure Cloud Provider (Azure-Cloudanbieter konfigurieren) | Konfigurieren Sie OpenShift für die Verwendung des Azure-Cloudanbieters. Erforderlich, wenn Azure-Datenträger für persistente Volumes angefügt werden.  Die Standardeinstellung ist „Ja“. |
| Azure AD Service Principal Client ID GUID (Client-ID-GUID für Azure AD-Dienstprinzipal) | Client-ID-GUID für Azure AD-Dienstprinzipal – auch als AppID bezeichnet. Ist nur erforderlich, wenn „Configure Azure Cloud Provider“ (Azure-Cloudanbieter konfigurieren) auf **Ja** festgelegt ist |
| Azure AD Service Principal Client ID Secret (Client-ID-Geheimnis für Azure AD-Dienstprinzipal) | Client-ID-Geheimnis für Azure AD-Dienstprinzipal. Ist nur erforderlich, wenn „Configure Azure Cloud Provider“ (Azure-Cloudanbieter konfigurieren) auf **Ja** festgelegt ist |
 
   ![Angebot – Blatt „OpenShift“](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Zusätzliche Einstellungen**

Auf dem Blatt „Zusätzliche Einstellungen“ kann CNS für GlusterFS-Speicher, Protokollierung, Metriken und die Router-Unterdomäne konfiguriert werden.  In der Standardeinstellung wird keine dieser Optionen installiert, und „nip.io“ wird als Router-Unterdomäne für Testzwecke verwendet. Durch Aktivieren von CNS werden drei zusätzliche Computeknoten mit drei zusätzlichen angefügten Datenträgern installiert, die GlusterFS-Pods hosten.  

Geben Sie Werte für die Eingabeparameter ein, und klicken Sie auf **OK**.

| Eingabeparameter | Beschreibung des Parameters |
|-----------------------|-----------------|
| Configure Container Native Storage (CNS) (CNS (Container Native Storage) konfigurieren) | Installiert CNS im OpenShift-Cluster und aktiviert es als Speicher. Standardeinstellung, wenn Azure-Anbieter deaktiviert ist |
| Configure Cluster Logging (Cluster-Protokollierung konfigurieren) | Installiert die EFK Protokollfunktion im Cluster.  Legen Sie eine ausreichende Größe der Infra-Knoten für das Hosting von EFK-Pods fest |
| Configure Metrics for the Cluster (Metriken für den Cluster konfigurieren) | Installiert Hawkular-Metriken im OpenShift-Cluster.  Legen Sie eine ausreichende Größe der Infra-Knoten für das Hosting von Hawkular-Metrik-Pods fest |
| Default Router Sub domain (Standardrouter-Unterdomäne) | Wählen Sie „nipio“ für Testzwecke oder „custom“ aus, um eine eigene Unterdomäne für die Produktion einzugeben |
 
   ![Angebot – Blatt für zusätzliche Einstellungen](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Zusätzliche Einstellungen – Weitere Parameter**

| Eingabeparameter | Beschreibung des Parameters |
|-----------------------|-----------------|
| CNS Node Size (CNS-Knotengröße) | Übernehmen Sie die standardmäßige Knotengröße oder wählen Sie **Größe ändern** aus, um eine neue VM-Größe auszuwählen |
| Enter your custom subdomain (Benutzerdefinierte Unterdomäne eingeben) | Dies ist die benutzerdefinierte Routingdomäne zum Verfügbarmachen von Anwendungen über den Router im OpenShift-Cluster.  Sie müssen den entsprechenden DNS-Platzhaltereintrag erstellen] |
 
   ![Angebot – Zusätzliche Einstellungen, CNS-Installation](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Zusammenfassung**

An diesem Punkt wird überprüft, ob das Kernkontingent für die Bereitstellung der Gesamtzahl der VMs ausreicht, die für den Cluster ausgewählt wurden.  Überprüfen Sie alle eingegebenen Parameter.  Wenn die Eingaben zulässig sind, klicken Sie auf **OK**, um fortzufahren.

   ![Blatt „Zusammenfassung“ für Angebot](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Kaufen**

Bestätigen Sie Ihre Kontaktdaten auf der Seite „Kaufen“, und klicken Sie auf **Kaufen**, um den Nutzungsbedingungen zuzustimmen und die Bereitstellung des OpenShift Container Platform-Clusters zu starten.

   ![Blatt „Kaufen“ für Angebot](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Herstellen einer Verbindung mit dem OpenShift-Cluster

Rufen Sie nach Abschluss der Bereitstellung die Verbindung aus dem Ausgabeabschnitt der Bereitstellung ab. Stellen Sie über Ihren Browser mithilfe der **URL für die OpenShift-Konsole** eine Verbindung mit der OpenShift-Konsole her. Sie können auch eine SSH-Verbindung mit dem Bastion-Host herstellen. Im folgenden Beispiel lautet der Administratorbenutzername „clusteradmin“, und der DNS-FQDN der öffentlichen Bastion-IP-Adresse lautet „bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com“:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, OpenShift-Cluster und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) entfernen.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nächste Schritte

- [Aufgaben nach der Bereitstellung](./openshift-post-deployment.md)
- [Behandeln von Problemen beim Bereitstellen von OpenShift in Azure](./openshift-troubleshooting.md)
- [Getting Started with OpenShift Container Platform](https://docs.openshift.com/container-platform) (Erste Schritte mit OpenShift Container Platform)

