---
title: Synchronisieren von Inhalten aus einem Cloudordner – Azure App Service
description: Erfahren Sie, wie Sie Ihre App in Azure App Service durch Inhaltssynchronisierung aus einem Cloudordner bereitstellen.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 65f372196671e95f7d9af7f47011e9ca1f9de316
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769616"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronisieren von Inhalt aus einem Cloudordner in Azure App Service
Dieser Artikel zeigt Ihnen, wie Sie Ihre Inhalte in Dropbox und auf OneDrive mit [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) synchronisieren. 

Die bedarfsabhängige Bereitstellung einer Inhaltssynchronisierung wird von der [Kudu-Bereitstellungs-Engine](https://github.com/projectkudu/kudu/wiki) in App Service unterstützt. Sie können mit Ihrem App-Code und Inhalt in einem festgelegten Cloudordner arbeiten und dann mit einem Klick auf eine Schaltfläche eine Synchronisierung mit App Service durchführen. Zur Inhaltssynchronisierung wird der Kudu-Buildserver verwendet. 

## <a name="enable-content-sync-deployment"></a>Aktivieren der Bereitstellung einer Inhaltssynchronisierung

Um die Inhaltssynchronisierung zu aktivieren, navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer App Service-App-Seite.

Klicken Sie im linken Menü auf **Bereitstellungscenter** > **OneDrive** oder **Dropbox** > **Autorisieren**. Befolgen Sie die Eingabeaufforderungen zur Autorisierung. 

![](media/app-service-deploy-content-sync/choose-source.png)

Die Autorisierung bei OneDrive oder Dropbox muss nur einmal erfolgen. Wenn Sie bereits autorisiert sind, klicken Sie einfach auf **Weiter**. Sie können das autorisierte OneDrive- oder Dropbox-Konto ändern, indem Sie auf **Konto ändern** klicken.

![](media/app-service-deploy-content-sync/continue.png)

Wählen Sie auf der Seite **Konfigurieren** den Ordner aus, den Sie synchronisieren möchten. Dieser Ordner wird unter dem folgenden festgelegten Inhaltspfad in OneDrive oder Dropbox erstellt. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Klicken Sie abschließend auf **Weiter**.

Überprüfen Sie auf der Seite **Zusammenfassung** die Optionen, und klicken Sie dann auf **Fertig stellen**.

## <a name="synchronize-content"></a>Synchronisieren von Inhalten

Wenn Sie Inhalte in Ihrem Cloudordner mit App Service synchronisieren möchten, kehren Sie zur Seite **Bereitstellungscenter** zurück, und klicken Sie auf **Synchronisieren**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Infolge grundlegender Unterschiede bei den APIs wird **OneDrive for Business** derzeit nicht unterstützt. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Deaktivieren der Bereitstellung einer Inhaltssynchronisierung

Um die Inhaltssynchronisierung zu deaktivieren, navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer App Service-App-Seite.

Klicken Sie im linken Menü auf **Bereitstellungscenter** > **Trennen**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen über ein lokales Git-Repository](deploy-local-git.md)
