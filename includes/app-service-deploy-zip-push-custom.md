---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177894"
---
## <a name="deployment-customization"></a>Anpassung von Bereitstellungen

Der Bereitstellungsprozess geht von der Annahme aus, dass die ZIP-Datei, die Sie per Push übertragen, eine ausführungsbereite App enthält. Standardmäßig werden keine Anpassungen ausgeführt. Um den gleichen Buildprozess zu ermöglichen, der Ihnen bei Continuous Integration zur Verfügung steht, fügen Sie Ihren Anwendungseinstellungen Folgendes hinzu:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Wenn Sie die ZIP-basierte Push-Bereitstellung verwenden, ist diese Einstellung standardmäßig **false**. Der Standardwert für Bereitstellungen mithilfe von Continuous Integration ist **true**. Wenn Sie den Wert auf **true** festlegen, werden Ihre bereitstellungsbezogenen Einstellungen während der Bereitstellung verwendet. Sie können diese Einstellungen entweder als App-Einstellungen oder in einer DEPLOYMENT-Konfigurationsdatei konfigurieren, die sich im Stammordner Ihrer ZIP-Datei befindet. Weitere Informationen finden Sie unter [Repository and deployment-related settings](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) (Repository- und bereitstellungsbezogene Einstellungen) in der Bereitstellungsreferenz.