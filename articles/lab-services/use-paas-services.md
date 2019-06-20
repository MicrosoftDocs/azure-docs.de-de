---
title: Verwenden von Platform-as-a-Service-Diensten (PaaS) in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Platform-as-a-Service-Dienste (PaaS) in Azure DevTest Labs verwenden können.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 865ae0b3f7a7965698a67183a4c820ba71f49cd8
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833912"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Verwenden von Platform-as-a-Service-Diensten (PaaS) in Azure DevTest Labs
PaaS wird in DevTest Labs über das Umgebungenfeature unterstützt. In DevTest Labs werden Umgebungen von vorkonfigurierten Azure Resource Manager-Vorlagen in einem Git-Repository unterstützt. Umgebungen können sowohl PaaS- als auch IaaS-Ressourcen enthalten. Sie ermöglichen Ihnen die Erstellung komplexer Systeme, die Azure-Ressourcen wie virtuelle Computer, Datenbanken, virtuelle Netzwerke und Web-Apps enthalten können, die zum Zusammenwirken angepasst sind. Diese Vorlagen erlauben konsistente Bereitstellung und verbesserte Verwaltung von Umgebungen mithilfe der Quellcodeverwaltung. 

Ein ordnungsgemäß eingerichtetes System ermöglicht folgende Szenarien: 

- Entwickler haben unabhängige und mehrere Umgebungen
- Asynchrones Testen mit unterschiedlichen Konfigurationen
- Integration in Staging- und Produktionspipelines ohne Vorlagenänderungen
- Sowohl Entwicklungscomputer als auch Umgebungen im gleichen Lab verbessern mühelose Verwaltung und Kostenberichterstattung.  

Der DevTest Labs-Ressourcenanbieter erstellt die Ressourcen im Namen des Labbenutzers, sodass der Labbenutzer keine zusätzlichen Berechtigungen benötigt, um die PaaS-Ressourcen verwenden zu können. Die folgende Abbildung zeigt einen Service Fabric-Cluster als Umgebung im Lab.

![Service Fabric-Cluster als Umgebung](./media/create-environment-service-fabric-cluster/cluster-created.png)

Weitere Informationen zum Einrichten von Umgebungen finden Sie unter [Erstellen Sie Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md). DevTest Labs verfügt über ein öffentliches Repository von Azure Resource Manager-Vorlagen, die Sie zum Erstellen von Umgebungen verwenden können, ohne selbst eine Verbindung mit einer externen GitHub-Quelle herstellen zu müssen. Weitere Informationen zu öffentlichen Umgebungen finden Sie unter [Konfigurieren und Verwenden von Umgebungen in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

In großen Organisationen bieten Entwicklungsteams in der Regel Umgebungen wie angepasste/isolierte Testumgebungen. Möglicherweise gibt es Umgebungen, die innerhalb einer Geschäftseinheit oder einer Abteilung von allen Teams verwendet werden können. Die IT-Abteilung möchte ihre Umgebungen bereitstellen, die von allen Teams in der Organisation verwendet werden können.  

## <a name="customizations"></a>Anpassungen

#### <a name="sandbox"></a>Sandbox 
Der Labbesitzer kann Labumgebungen anpassen, um die Rolle des Benutzers innerhalb der Ressourcengruppe vom **Leser** zum **Mitwirkenden** zu ändern. Diese Funktion befindet sich auf der Seite **Labeinstellungen** unter **Konfiguration und Richtlinien** des Labs. Diese Rollenänderung ermöglicht dem Benutzer, Ressourcen dieser Umgebung hinzuzufügen oder daraus zu entfernen. Wenn Sie den Zugriff weiter einschränken möchten, verwenden Sie Azure-Richtlinien. Diese Funktionalität ermöglicht Ihnen, die Ressourcen oder die Konfiguration ohne Zugriff auf Abonnementebene anzupassen.

#### <a name="custom-tokens"></a>Benutzerdefinierte Token
Es gibt einige benutzerdefinierte Labinformationen, die außerhalb der Ressourcengruppe liegen und sich auf Umgebungen beziehen, auf die die Vorlage zugreifen kann. Hier einige Beispiele: 

- Labnetzwerkidentifizierung
- Location
- Speicherkonto, unter dem die Resource Manager-Vorlagendateien gespeichert werden. 
 
#### <a name="lab-virtual-network"></a>Virtuelles Labnetzwerk
Im Artikel [Herstellen einer Verbindung zwischen einer Umgebung und dem virtuellen Netzwerk Ihres Labs in Azure DevTest Labs](connect-environment-lab-virtual-network.md) wird beschrieben, wie Sie die Resource Manager-Vorlage zur Verwendung mit dem `$(LabSubnetId)`-Token ändern. Beim Erstellen einer Umgebung wird das `$(LabSubnetId)`-Token durch die erste Subnetzmaske ersetzt, bei der die Option zur **Verwendung zum Erstellen eines virtuellen Computers** auf **true** gesetzt ist. So kann unsere Umgebung zuvor erstellte Netzwerke nutzen. Wenn Sie die gleichen Resource Manager-Vorlagen in Staging- und Produktionstestumgebungen verwenden möchten, verwenden Sie `$(LabSubnetId)` als Standardwert in einem Resource Manager-Vorlagenparameter. 

#### <a name="environment-storage-account"></a>Umgebungsspeicherkonto
DevTest Labs unterstützt die Verwendung [geschachtelter Resource Manager-Vorlagen](../azure-resource-manager/resource-group-linked-templates.md). Im Artikel [Bereitstellen geschachtelter Azure Resource Manager-Vorlagen für Testumgebungen](deploy-nested-template-environments.md) wird erläutert, wie Sie mit `_artifactsLocation`- und `_artifactsLocationSasToken`-Token einen URI zu einer Resource Manager-Vorlage im gleichen Ordner, in dem sich die Hauptvorlage befindet, oder in einem geschachtelten Ordner erstellen. Weitere Informationen zu diesen beiden Token finden Sie im Abschnitt **Deployment artifacts** (Bereitstellungsartefakte) von [Azure Resource Manager Templates – Best Practices Guide](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md) (Azure Resource Manager-Vorlagen – Leitfaden zu bewährten Methoden).

## <a name="user-experience"></a>Benutzererfahrung

## <a name="developer"></a>Developer
Entwickler verwenden den gleichen Workflow für das Erstellen eines virtuellen Computers, um eine bestimmte Umgebung zu erstellen. Sie wählen die Umgebung gemäß dem Computerimage aus und geben die von der Vorlage benötigten Informationen ein. Wenn jeder Entwickler eine Umgebung hat, ist die Bereitstellung von Änderungen und verbessertes Debuggen in der inneren Schleife möglich. Die Umgebung kann zu einem beliebigen Zeitpunkt mit der aktuellen Vorlage erstellt werden.  Dieses Feature ermöglicht, Umgebungen zu zerstören und neu zu erstellen, um die Downtime zu verringern, die anfällt, wenn das System manuell erstellt oder nach Fehlertests wiederhergestellt werden muss.  

### <a name="testing"></a>Testen
DevTest Labs-Umgebungen ermöglichen unabhängiges asynchrones Testen spezifischer Codes und Konfigurationen. Gängige Praxis ist, die Umgebung mit dem Code aus dem einzelnen Pull Request einzurichten und automatisiertes Testen zu starten. Sobald das automatisierte Testen abgeschlossen ist, kann jegliches manuelles Testen für die spezifische Umgebung ausgeführt werden. Dieser Prozess erfolgt normalerweise als Teil der CI/CD-Pipeline. 

## <a name="management-experience"></a>Verwaltungserfahrung

### <a name="cost-tracking"></a>Kostenverfolgung
Das Kostenverfolgungsfeature bezieht Azure-Ressourcen in den unterschiedlichen Umgebungen als Teil des gesamten Kostentrends ein. Die Kosten der Ressourcen werden nicht nach verschiedenen Ressourcen innerhalb der Umgebung aufgeschlüsselt, sondern die Gesamtkosten der Umgebung werden angezeigt.

### <a name="security"></a>Sicherheit
Ein ordnungsgemäß konfiguriertes Azure-Abonnement mit DevTest Labs kann [den Zugriff auf Azure-Ressourcen nur über das Lab einschränken](devtest-lab-add-devtest-user.md). Mit Umgebungen kann ein Labbesitzer Benutzern den Zugriff auf PaaS-Ressourcen mit genehmigten Konfigurationen gewähren, ohne den Zugriff auf andere Azure-Ressourcen zu gewähren. In dem Szenario, in dem Labbenutzer Umgebungen anpassen, kann der Labbesitzer den Zugriff als Mitwirkender gewähren. Der Zugriff als Mitwirkender ermöglicht dem Labbenutzer, Azure-Ressourcen nur innerhalb der verwalteten Ressourcengruppe hinzuzufügen oder zu entfernen. Er ermöglicht eine einfachere Nachverfolgung und Verwaltung, als wenn dem Benutzer der Zugriff als Mitwirkender auf das Abonnement ermöglicht würde.

### <a name="automation"></a>Automation
Automatisierung ist eine der wichtigsten Komponenten für ein umfangreiches, effektives Ökosystem. Automatisierung ist erforderlich, um das Abonnements und Labs übergreifende Verwalten oder Nachverfolgen mehrerer Umgebungen durchzuführen.

### <a name="cicd-pipeline"></a>CI/CD-Pipeline
PaaS-Dienste können in DevTest Labs die CI/CD-Pipeline durch fokussierte Bereitstellungen verbessern, in denen der Zugriff über das Lab gesteuert wird.

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Artikel enthalten ausführliche Informationen zu Umgebungen: 

- 
- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
- [Konfigurieren und Verwenden von öffentlichen Umgebungen in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Erstellen einer Umgebung mit einem eigenständigen Service Fabric-Cluster in Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Herstellen einer Verbindung zwischen einer Umgebung und dem virtuellen Netzwerk Ihres Labs in Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrieren von Umgebungen in Ihre Azure DevOps-CI/CD-Pipelines](integrate-environments-devops-pipeline.md)
 





