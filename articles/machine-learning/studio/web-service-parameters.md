---
title: 'Webdienstparameter: Azure Machine Learning Studio | Microsoft-Dokumentation'
description: 'Anleitung: Verwenden von Azure Machine Learning Webdienstparametern, um das Verhalten des Modells beim Zugriff auf den Webdienst zu ändern.'
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: a236043d5622e5a2e1ffd572c887fb5ffac2174a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345426"
---
# <a name="use-azure-machine-learning-studio-web-service-parameters"></a>Verwenden von Azure Machine Learning Studio-Webdienstparametern
Ein Azure Machine Learning-Webdienst wird erstellt, indem ein Experiment veröffentlicht wird, das Module mit konfigurierbaren Parametern enthält. In einigen Fällen kann es vielleicht erforderlich sein, das Verhalten des Moduls zu ändern, während der Webdienst ausgeführt wird. *Webdienstparameter* erlauben Ihnen, diese Aufgabe auszuführen. 

Ein typisches Beispiel ist das Einrichten des Moduls [Import Data][reader], damit die Benutzer des veröffentlichten Webdiensts beim Zugreifen auf den Webdienst eine andere Datenquelle angeben können. Ein weiteres Beispiel ist die Konfiguration des Moduls [Export Data][writer], damit ein anderes Ziel angegeben werden kann. Zu weiteren Beispielen zählen die Änderung der Anzahl von Bits für das Modul [Feature Hashing][feature-hashing] oder der Anzahl der gewünschten Funktionen für das Modul [Filter-Based Feature Selection][filter-based-feature-selection]. 

Sie können Webdienstparameter definieren und einem oder mehreren Modulparametern im Experiment zuordnen, und Sie können angeben, ob diese Parameter erforderlich oder optional sind. Benutzer des Webdiensts können dann Werte für diese Parameter bereitstellen, wenn sie den Webdienst aufrufen. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Festlegen und Verwenden von Webdienstparametern
Sie definieren einen Webdienstparameter, indem Sie auf das Symbol neben dem Parameter für ein Modul klicken und "Set as web service parameter" (Als Webdienstparameter festlegen) auswählen. Daraufhin wird ein neuer Webdienstparameter erstellt und mit diesem Modulparameter verbunden. Wenn auf den Webdienst zugegriffen wird, kann der Benutzer einen Wert für den Webdienstparameter angeben, der dann auf den Modulparameter angewendet wird.

Nachdem Sie einen Web Service-Parameter definiert haben, steht dieser für andere Modulparameter im Experiment zur Verfügung. Sie können einen Webdienstparameter, den Sie definiert und einem Modulparameter zugeordnet haben, auch für einen Parameter eines anderen Moduls verwenden, sofern der betreffende Parameter denselben Typ von Wert erwartet. Wenn der Webdienstparameter z. B. ein numerischer Wert ist, kann er nur für Modulparameter verwendet werden, die einen numerischen Wert erwarten. Wenn der Benutzer einen Wert für den Webdienstparameter festlegt, wird dieser auf alle zugehörigen Modulparameter angewendet.

Sie können entscheiden, ob Sie einen Standardwert für den Webdienstparameter angeben. Wenn Sie dies tun, ist der Parameter für die Benutzer des Webdiensts optional. Geben Sie keinen Standardwert an, dann muss der Benutzer einen Wert eingeben, wenn auf den Webdienst zugegriffen wird.

Die API-Dokumentation für den Webdienst enthält Informationen, denen die Webdienstbenutzer entnehmen können, wie der Webdienstparameter beim Zugriff auf den Webdienst programmgesteuert angegeben werden kann.

> [!NOTE]
> Die API-Dokumentation für einen klassischen Webdienst wird über den Link **API-Hilfeseite** im **DASHBOARD** des Webdiensts in Machine Learning Studio bereitgestellt. Die API-Dokumentation für einen neuen Webdienst wird über das [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart)-Portal auf den Seiten **Consume** und **Swagger API** für den Webdienst bereitgestellt.
> 
> 

## <a name="example"></a>Beispiel
Nehmen wir beispielsweise an, wir hätten ein Experiment mit einem Modul [Export Data][writer], das Informationen an Azure Blob Storage sendet. Wir definieren einen Webdienstparameter namens "Blob path", mit dem die Benutzer des Webdienst beim Zugriff auf den Dienst den Blob-Speicherpfad ändern können.

1. Klicken Sie in Machine Learning Studio auf das Modul [Export Data][writer], um es auszuwählen. Die Moduleigenschaften werden rechts neben dem Canvas mit dem Experiment im Eigenschaftenbereich angezeigt.
2. Geben Sie den Speichertyp an:
   
   * Wählen Sie unter **Please specify data destination**(Datenziel angeben) das Ziel "Azure Blob Storage" (Azure-Blob-Speicher" aus.
   * Wählen Sie unter **Please specify authentication type**(Authentifizierungstyp angeben) die Option "Account" (Konto) aus.
   * Geben Sie die Kontoinformationen für Azure Blob Storage ein. 

3. Klicken Sie auf das Symbol rechts neben **Path to blob beginning with container parameter**(Blob-Pfad beginnt mit Containerparameter). Es sieht folgendermaßen aus:
   
   ![Symbol für Webdienstparameter](./media/web-service-parameters/icon.png)
   
   Wählen Sie "Set as web service parameter" (Als Webdienstparameter festlegen) aus.
   
   Ein Eintrag wird unter **Web Service Parameters** (Webdienstparameter) am unteren Rand des Eigenschaftenbereichs mit dem Namen "Path to blob beginning with container" (Blob-Pfad beginnt mit Container) hinzugefügt. Dies ist der Webdienstparameter, der nun diesem Parameter des Moduls [Export Data][writer] zugeordnet ist.
4. Wenn Sie den Webdienstparameter umbenennen möchten, klicken Sie auf den Namen, geben Sie "Blob-Pfad" ein, und drücken Sie die **EINGABETASTE** . 
5. Um einen Standardwert für den Webdienstparameter bereitzustellen, klicken Sie auf das Symbol rechts neben dem Namen, wählen Sie „Provide default value“ (Standardwert bereitstellen) aus, geben Sie einen Wert ein (z.B. „container1/output1.csv“), und drücken Sie die **EINGABETASTE**.
   
   ![Webdienstparameter](./media/web-service-parameters/parameter.png)
6. Klicken Sie auf **Ausführen**. 
7. Klicken Sie auf **Deploy Web Service**, und wählen Sie **Deploy Web Service [Classic]** oder **Deploy Web Service [New]** aus, um den Webdienst bereitzustellen.

> [!NOTE] 
> Zum Bereitstellen eines neuen Webdiensts müssen Sie über ausreichende Berechtigungen in dem Abonnement verfügen, an das Sie den Webdienst bereitstellen. Weitere Informationen finden Sie unter [Verwalten eines Webdiensts im Azure Machine Learning Web Services-Portal](manage-new-webservice.md). 

Benutzer des Webdiensts können jetzt beim Zugreifen auf den Webdienst ein neues Ziel für das Modul [Export Data][writer] angeben.

## <a name="more-information"></a>Weitere Informationen
Ein ausführlicheres Beispiel finden Sie unter dem Eintrag [Web Service Parameters](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) im [Machine Learning Blog](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Weitere Informationen über den Zugriff auf einen Machine Learning-Webdienst finden Sie unter [Nutzen eines veröffentlichten Webdiensts für maschinelles Lernen](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

