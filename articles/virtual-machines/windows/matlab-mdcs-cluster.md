---
title: MATLAB-Cluster auf virtuellen Computern | Microsoft Docs
description: Erstellen Sie unter Verwendung virtueller Microsoft Azure-Computer MATLAB Distributed Computing Server-Cluster für die Ausführung rechenintensiver paralleler MATLAB-Workloads.
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 49824741facc8822a9417306794f1028fc180e16
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60555097"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Erstellen eines MATLAB Distributed Computing Server-Clusters auf virtuellen Azure-Computern
Erstellen Sie unter Verwendung virtueller Microsoft Azure-Computer einen oder mehrere MATLAB Distributed Computing Server-Cluster für die Ausführung rechenintensiver paralleler MATLAB-Workloads. Installieren Sie die MATLAB Distributed Computing Server-Software auf einem virtuellen Computer, der als Basisimage fungiert, und verwenden Sie zum Bereitstellen und Verwalten des Clusters eine Azure-Schnellstartvorlage oder ein Azure PowerShell-Skript (erhältlich auf [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)). Stellen Sie nach dem Bereitstellen eine Verbindung mit dem Cluster her, um Ihre Workloads auszuführen.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Informationen zu MATLAB und MATLAB Distributed Computing Server
[MATLAB](https://www.mathworks.com/products/matlab/) ist eine optimierte Plattform zum Lösen technischer und wissenschaftlicher Aufgaben. MATLAB-Benutzer mit umfangreichen Simulationen und Datenverarbeitungsaufgaben können MathWorks-Produkte mit paralleler Berechnung verwenden und so ihre rechenintensiven Workloads durch die Nutzung von Computeclustern und Grid-Diensten beschleunigen. [Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) können MATLAB-Benutzer Anwendungen parallelisieren und Mehrkernprozessoren, GPUs und Computecluster nutzen. [MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) ermöglicht MATLAB-Benutzern die Nutzung zahlreicher Computer in einem Computecluster.

Durch die Verwendung virtueller Azure-Computer können Sie MATLAB Distributed Computing Server-Cluster erstellen, die alle über die gleichen Mechanismen zum Senden paralleler Aufgaben verfügen wie lokale Cluster. Hierzu zählen etwa interaktive Aufträge, Batchaufträge, unabhängige Aufträge und Kommunikationsaufträge. Die Kombination aus Azure und MATLAB-Plattform bietet gegenüber der Bereitstellung und Verwendung herkömmlicher lokaler Hardware zahlreiche Vorteile: ein breites Größenspektrum für virtuelle Computer, bedarfsbasierte Clustererstellung, sodass nur Kosten für tatsächlich genutzte Computeressourcen anfallen, und die Möglichkeit zum ausgiebigen Testen von Modellen.  

## <a name="prerequisites"></a>Voraussetzungen
* **Clientcomputer** : Sie benötigen einen Windows-basierten Clientcomputer für die Kommunikation mit Azure und dem MATLAB Distributed Computing Server-Cluster nach der Bereitstellung.
* **Azure PowerShell** : Lesen Sie den Artikel [Installieren und konfigurieren Sie Azure PowerShell](/powershell/azure/overview) , um zu erfahren, wie Sie Azure PowerShell auf Ihrem Clientcomputer installieren.
* **Azure-Abonnement** : Falls Sie über kein Abonnement verfügen, können Sie in wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/free/) einrichten. Bei größeren Clustern empfiehlt sich die Verwendung eines Abonnements mit nutzungsbasierter Bezahlung oder einer anderen Kaufoption.
* **vCPU-Kontingent** : Falls Sie einen umfangreichen Cluster oder mehrere MATLAB Distributed Computing Server-Cluster bereitstellen möchten, müssen Sie unter Umständen das vCPU-Kontingent erhöhen. Um ein Kontingent zu erhöhen, können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) .
* **MATLAB-, Parallel Computing Toolbox- und MATLAB Distributed Computing Server-Lizenzen** : In den Skripts wird vorausgesetzt, dass für alle Lizenzen der [MathWorks Hosted License Manager](https://www.mathworks.com/help/install/license-management.html) verwendet wird.  
* **MATLAB Distributed Computing Server-Software** : Wird auf einem virtuellen Computer installiert, der als VM-Basisimage für die virtuellen Clustercomputer fungiert.

## <a name="high-level-steps"></a>Schritte auf oberer Ebene:
Zur Verwendung virtueller Azure-Computer für MATLAB Distributed Computing Server-Cluster müssen im Allgemeinen folgende Schritte ausgeführt werden. Ausführliche Informationen finden Sie in der Begleitdokumentation der Schnellstartvorlage und Skripts auf [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Erstellen eines VM-Basisimages**  

   * Laden Sie die MATLAB Distributed Computing Server-Software auf diesen virtuellen Computer herunter, und installieren Sie sie.

     > [!NOTE]
     > Dieser Vorgang kann mehrere Stunden dauern, muss aber nur einmal für jede verwendete MATLAB-Version ausgeführt werden.   
     >
     >
2. **Erstellen mindestens eines Clusters**  

   * Erstellen Sie auf der Grundlage des VM-Basisimages einen Cluster (entweder mithilfe des bereitgestellten PowerShell-Skripts oder mithilfe der Schnellstartvorlage).   
   * Mit dem bereitgestellten PowerShell-Skript können Sie Cluster aufführen, anhalten, fortsetzen und löschen.

## <a name="cluster-configurations"></a>Clusterkonfigurationen
Mit dem Clustererstellungsskript und der entsprechenden Vorlage können Sie derzeit eine einzelne MATLAB Distributed Computing Server-Topologie erstellen. Bei Bedarf können Sie zusätzliche Cluster mit jeweils unterschiedlicher Anzahl von Worker-VMs, unterschiedlichen VM-Größen usw. erstellen.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-Client und -Cluster in Azure
MATLAB-Clientknoten, MATLAB-Auftragsplaner-Knoten und MATLAB Distributed Computing Server-Knoten (Workerknoten) werden alle als virtuelle Azure-Computer in einem virtuellen Netzwerk konfiguriert, wie in der folgenden Abbildung zu sehen:


* Stellen Sie zum Verwenden des Clusters eine Remotedesktopverbindung mit dem Clientknoten her. Der Clientknoten führt den MATLAB-Client aus.
* Der Clientknoten verfügt über eine Dateifreigabe, auf die alle Worker zugreifen können.
* MathWorks Hosted License Manager wird zur Lizenzüberprüfung für die gesamte MATLAB-Software verwendet.
* Standardmäßig wird auf den Worker-VMs pro vCPU jeweils ein MATLAB Distributed Computing Server-Worker erstellt. Sie können aber eine beliebige Anzahl angeben.

## <a name="use-an-azure-based-cluster"></a>Verwenden eines Azure-basierten Clusters
Genau wie bei anderen Arten von MATLAB Distributed Computing Server-Clustern muss mithilfe des Clusterprofil-Managers des MATLAB-Clients (auf dem virtuellen Clientcomputer) ein MATLAB-Auftragsplaner-Clusterprofil erstellt werden.

![Clusterprofil-Manager](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Bereitstellen und Verwalten von MATLAB Distributed Computing Server-Clustern in Azure finden Sie im [GitHub-Repository](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) mit den Vorlagen und Skripts.
* Auf der [MathWorks-Website](https://www.mathworks.com/) finden Sie eine ausführliche Dokumentation für MATLAB und MATLAB Distributed Computing Server.
