---
title: Data Science Virtual Machine-Pools – Azure | Microsoft-Dokumentation
description: Bereitstellen von Pools von Data Science VM-Instanzen als freigegebene Ressource für ein Team
keywords: Deep Learning, KI, Data Science-Tools, virtueller Data Science-Computer, räumliche Analysen, Data Science-Teamprozess
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: gokuma
ms.openlocfilehash: 5cce7f691204a0fd116627fadde1076a4505fcb2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502276"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Erstellen eines freigegebenen Pools von Data Science Virtual Machine-Instanzen

In diesem Artikel erfahren Sie, wie Sie einen freigegebenen Pool von Data Science Virtual Machine-Instanzen (DSVMs) für die Verwendung durch ein Team erstellen können. Die Vorteile der Verwendung eines freigegebenen Pools sind die bessere Ressourcennutzung, die Vereinfachung von Freigaben und Zusammenarbeit und die effektivere Verwaltung der DSVM-Ressourcen. 

Zum Erstellen eines DSVM-Pools können Sie zahlreiche Methoden und Technologien verwenden. Die Schwerpunkte dieses Artikels liegen auf Pools für interaktive VMs. Eine alternative verwaltete Computeinfrastruktur ist Azure Machine Learning Compute. Weitere Informationen finden Sie unter [Einrichten von Computezielen](../service/how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Interaktiver VM-Pool

Ein Pool von interaktiven VMs, die vom gesamten KI/Data Science-Team gemeinsam genutzt werden, ermöglicht Benutzern das Anmelden an einer verfügbaren DSVM-Instanz, statt eine dedizierte Instanz für jede Gruppe von Benutzern vorzuhalten. Dieses Setup verbessert die Verfügbarkeit und unterstützt eine effektivere Nutzung der Ressourcen. 

Die Technologie, die Sie zum Erstellen eines interaktiven VM-Pools verwenden, sind [VM-Skalierungsgruppen von Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Mit Skalierungsgruppen können Sie eine Gruppe von identischen virtuellen Computern mit Lastenausgleich und automatischer Skalierung erstellen und verwalten. 

Der Benutzer meldet sich bei der primären IP-Adresse oder DNS-Adresse des Pools an. Die Skalierungsgruppe leitet die Sitzung automatisch an eine verfügbare DSVM in der Skalierungsgruppe weiter. Da sich der Benutzer unabhängig von der VM, an der er sich angemeldet hat, eine ähnliche Umgebung wünscht, stellen alle Instanzen der VM in der Skalierungsgruppe ein freigegebenes Netzlaufwerk wie eine Azure Files- oder eine NFS-Freigabe bereit. Der freigegebene Arbeitsbereich des Benutzers wird normalerweise im freigegebenen Dateispeicher gespeichert, der in alle Instanzen eingebunden ist. 

Eine Azure Resource Manager-Beispielvorlage, mit der eine Skalierungsgruppe mit Ubuntu-DSVM-Instanzen erstellt wird, finden Sie auf [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ein Beispiel der [Parameterdatei](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) für die Azure Resource Manager-Vorlage wird am gleichen Ort bereitgestellt. 

Sie können die Skalierungsgruppe mit der Azure Resource Manager-Vorlage erstellen, indem Sie mit Azure CLI Werte für die Parameterdatei angeben. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Die vorangehenden Befehle setzen voraus, das Folgendes vorhanden ist:
* Eine Kopie der Parameterdatei mit den für Ihre Instanz der Skalierungsgruppe angegebenen Werten
* Die Anzahl der VM-Instanzen
* Zeiger auf die Azure Files-Freigabe
* Anmeldeinformationen für das in jede VM eingebundene Speicherkonto 

Auf die Parameterdatei wird in den Befehlen lokal verwiesen. Sie können Parameter auch inline übergeben oder zu deren Eingabe im Skript auffordern.  

Die vorangegangene Vorlage ermöglicht den SSH- und den JupyterHub-Port von der Front-End-VM-Skalierungsgruppe zum Back-End-Pool der Ubuntu-DSVM-Instanzen. Als Benutzer melden Sie sich einfach an der VM über SSH oder JupyterHub auf dem normalen Weg an. Weil die VM-Instanzen dynamisch zentral hoch- und herunterskaliert werden können, muss jeder Zustand in der bereitgestellten Azure Files-Freigabe gespeichert werden. Der gleiche Ansatz kann zum Erstellen eines Pools von Windows-DSVM-Instanzen verwendet werden. 

Das [Skript, das Azure Files einbindet](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh), steht auch auf GitHub im „Azure DataScienceVM“-Repository zur Verfügung. Das Skript bindet die Azure Files-Freigabe am angegebenen Bereitstellungspunkt in der Parameterdatei ein. Zudem erstellt das Skript auch Softlinks auf das eingebundene Laufwerk im Startverzeichnis des ursprünglichen Benutzers. Ein benutzerspezifisches Notebook-Verzeichnis innerhalb der Azure Files-Freigabe ist mit dem Verzeichnis `$HOME/notebooks/remote` verknüpft, sodass Benutzer auf ihre Jupyter Notebook-Instanzen zugreifen, sie ausführen und speichern können. Sie können die gleiche Konvention verwenden, wenn Sie zusätzliche Benutzer auf der VM erstellen, um den Jupyter-Arbeitsbereich der einzelnen Benutzer auf die Azure Files-Freigabe zu verweisen. 

VM-Skalierungsgruppen unterstützen die automatische Skalierung. Sie können Regeln festlegen, wann Sie weitere Instanzen erstellen und wann Sie diese zentral herunterskalieren möchten. Beispielsweise können Sie die Anzahl der Instanzen auf Null reduzieren, um Kosten für die Nutzung der Cloudhardware zu sparen, wenn die VMs überhaupt nicht verwendet werden. Die Dokumentationsseiten zu VM-Skalierungsgruppen bieten ausführliche Schritte für die [automatische Skalierung](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten einer gemeinsamen Identität](dsvm-common-identity.md)
* [Sicheres Speichern von Anmeldeinformationen für den Zugriff auf Cloudressourcen](dsvm-secure-access-keys.md)















