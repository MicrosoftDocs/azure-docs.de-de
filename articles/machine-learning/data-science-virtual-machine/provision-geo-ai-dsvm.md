---
title: Bereitstellen einer Data Science-VM für geografische KI in Azure – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Data Science-VM für geografische KI erstellen und konfigurieren. Die Data Science-VM für geografische KI bietet Ihnen die Tools zum Erstellen von KI und ML-Lösungen mit geografischen Daten.
keywords: Deep Learning, KI, Data Science-Tools, virtueller Data Science-Computer, räumliche Analysen
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
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: dde9b71c3615a592f8c08e040c5e9ba7bc756bd6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578552"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Bereitstellen einer Data Science-VM für geografische KI in Azure 

Die Data Science-VM für geografische KI (Geo-DSVM) ist eine Erweiterung der verbreiteten [Azure Data Science-VM](https://aka.ms/dsvm), die speziell zur Kombination von KA und geografischen Analysen konfiguriert ist. Die geografischen Analysen auf dem virtuellen Computer werden durch [ArcGIS Pro](https://www.arcgis.com/features/index.html) ermöglicht. Die Data Science-VM ermöglicht das schnelle Trainieren von Modellen für maschinelles Lernen und selbst von Deep Learning-Modellen mit Daten, die um geografische Daten ergänzt wurden. Sie wird nur auf der Windows 2016-DSVM unterstützt. 

Die Geo-DSVM enthält mehrere Tools für KI, darunter:

- GPU-Editionen von verbreiteten Deep Learning-Frameworks wie Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer 
- Tools zum Abrufen und Vorverarbeiten von Bild- und Textdaten 
- Tools für Entwicklungsaktivitäten wie Microsoft R Server Developer Edition, Anaconda Python, Jupyter Notebooks für Python und R, IDEs für Python und R, SQL-Datenbanken
- Die Desktopsoftware ArcGIS Pro von ESRI in Verbindung mit Python- und R-Schnittstellen, die die räumlichen Daten aus Ihren KI-Anwendungen verarbeiten können 
 

## <a name="create-your-geo-ai-data-science-vm"></a>Erstellen einer Data Science-VM für geografische KI

Im Folgenden wird das Verfahren zum Erstellen einer Instanz der Data Science-VM für geografische KI beschrieben: 


1. Wechseln Sie im [Azure-Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)zur Auflistung der virtuellen Computer.
2. Wählen Sie unten auf der Seite die Schaltfläche **Erstellen** aus, um einen Assistenten aufzurufen.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Der Assistent zum Erstellen der Geo-DSVM erfordert **Eingaben** für jeden der **vier Schritte**, die in der Abbildung rechts aufgeführt sind. Die zum Konfigurieren der einzelnen Schritte erforderlichen Eingaben sind:



   - **Grundlagen**

      1. **Name**: Der Name des Data Science-Servers, den Sie erstellen.

      2. **Benutzername**: Die ID des Administratoranmeldekontos.

      3. **Kennwort**: Das Kennwort des Administratorkontos.

      4. **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus, über das der Computer erstellt und abgerechnet wird.

      5. **Ressourcengruppe**: Sie können eine neue Ressourcengruppe erstellen oder eine **leere** in Ihrem Abonnement vorhandene Azure-Ressourcengruppe verwenden.

      6. **Standort**: Wählen Sie das Datencenter aus, das am besten geeignet ist. Normalerweise ist es das Rechenzentrum, in dem der größte Teil Ihrer Daten gespeichert ist oder das Ihrem Standort am nächsten ist, um den schnellsten Netzwerkzugriff zu erreichen. Wenn Sie Deep Learning-Vorgänge mit GPUs durchführen möchten, müssen Sie eine der Regionen in Azure auswählen, die GPU-VM-Instanzen der NC-Serie bietet. Zurzeit werden die folgenden Standorte unterstützt, die über GPU-VMs verfügen: **USA, Osten, USA, Norden-Mitte, USA, Süden-Mitte, USA, Westen 2, Europa, Norden, Europa, Westen**. Die neueste Liste finden Sie auf der Seite [Azure-Produkte nach Region](https://azure.microsoft.com/regions/services/). Suchen Sie hier unter **Compute** nach **NC-Serie**. 


   - **Einstellungen**: Wählen Sie eine Größe für GPU-VMs der NC-Serie aus, wenn Sie vorhaben, Deep Learning auf GPUs in Ihrer Geo-DSVM auszuführen. Andernfalls können Sie eine auf einer CPU basierende Instanz auswählen.  Erstellen Sie ein Speicherkonto für Ihre VM. 
   
   - **Zusammenfassung**: Stellen Sie sicher, dass alle eingegebenen Informationen richtig sind.

   - **Kaufen**: Klicken Sie auf **Kaufen**, um die Bereitstellung zu starten. Es wird ein Link zu den allgemeinen Geschäftsbedingungen bereitgestellt. Für die VM gelten keine über die Computekosten für die Servergröße, die Sie im Schritt **Größe** ausgewählt haben, hinausgehenden Kosten. 
 
>[!NOTE]
> Die Bereitstellung sollte ungefähr 20 bis 30 Minuten dauern. Der Status der Bereitstellung wird im Azure-Portal angezeigt.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Zugreifen auf die Data Science-VM für geografische KI

 Sobald Ihre VM erstellt wurde, können Sie beginnen, die installierten und vorkonfigurierten Tools zu verwenden. Für viele der Tools gibt es Desktopsymbole und Kacheln im Startmenü. Sie können sich mithilfe von Remotedesktop mit den Anmeldeinformationen des Administratorkontos anmelden, das Sie zuvor im Abschnitt **Grundlagen** konfiguriert haben. 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Verwenden der auf dem virtuellen Computer installierten Software ArcGIS Pro

Auf der Geo-DSVM ist bereits ArcGIS Pro Desktop vorinstalliert, und die Umgebung ist für die Arbeit mit den Tools der DSVM vorkonfiguriert. Beim Starten von ArcGIS werden Sie aufgefordert, eine Anmeldung für Ihr ArcGIS-Konto anzugeben. Wenn Sie bereits über ein ArcGIS-Konto verfügen und Lizenzen für die Software besitzen, können Sie Ihre vorhandenen Anmeldeinformationen verwenden.  

![ArcGIS-Anmeldung](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Andernfalls können Sie sich für ein neues ArcGIS-Konto und eine Lizenz registrieren oder eine [kostenlose Testversion](https://www.arcgis.com/features/free-trial.html) erhalten. 

![Kostenlose Testversion von ArcGIS](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Sobald Sie sich für ein kostenpflichtiges oder kostenloses ArcGIS-Testkonto registriert haben, können Sie ArcGIS Pro für Ihr Konto autorisieren. Führen Sie hierzu die Anweisungen unter [Getting Started with ArcGIS Pro (Erste Schritte mit ArcGIS Pro)](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf) aus. 

Nachdem Sie sich mit Ihrem ArcGIS-Konto bei ArcGIS Pro Desktop angemeldet haben, können Sie die auf dem virtuellen Computer installierten und konfigurierten Data Science-Tools für Ihre geografischen Analysen und Ihre Projekte für maschinelles Lernen verwenden.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich in folgenden Themen über die ersten Schritte mit der Data Science-VM für geografische KI:

* [Verwenden der Data Science-VM für geografische KI](use-geo-ai-dsvm.md)
