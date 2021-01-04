---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185370"
---
### <a name="open-the-publish-wizard"></a>Öffnen des Veröffentlichungs-Assistenten

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **SharingService**, und wählen Sie dann **Veröffentlichen** aus.

Der Veröffentlichungs-Assistent wird gestartet. 

Wählen Sie **App Service** > **Veröffentlichen** aus, um den Bereich **App Service erstellen** zu öffnen.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim Azure-Portal an.

Wählen Sie im Bereich **App Service erstellen** die Option **Konto hinzufügen** aus, und melden Sie sich bei Ihrem Azure-Abonnement an. Wenn Sie bereits angemeldet sind, wählen Sie in der Dropdownliste das gewünschte Konto aus.

   > [!NOTE]
   > Wenn Sie bereits angemeldet sind, wählen Sie noch nicht **Erstellen** aus.
   >

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [resource group intro text](resource-group.md)]

Wählen Sie neben **Ressourcengruppe** die Option **Neu** aus.

Nennen Sie die Ressourcengruppe **myResourceGroup**, und wählen Sie anschließend **OK** aus.

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

[!INCLUDE [app-service-plan](app-service-plan.md)]

Wählen Sie neben **Hostingplan** die Option **Neu** aus.

Verwenden Sie im Bereich **Hostingplan konfigurieren** die folgenden Einstellungen:

| Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
|-|-|-|
|App Service-Plan| MySharingServicePlan | Name des App Service-Plans |
| Standort | USA (Westen) | Das Rechenzentrum, in dem die Web-App gehostet wird |
| Size | Kostenlos | Der [Tarif](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), der die Hostingfunktionen festlegt |

Klicken Sie auf **OK**.

### <a name="create-and-publish-the-web-app"></a>Erstellen und Veröffentlichen der Web-App

Geben Sie unter **App-Name** einen eindeutigen App-Namen ein. Gültige Zeichen sind die Buchstaben a-z, die Ziffern 0-9 und Bindestriche (-). Alternativ können Sie den automatisch generierten eindeutigen Namen übernehmen. Die URL der Web-App lautet `https://<app_name>.azurewebsites.net`, wobei `<app_name>` der Name Ihrer App ist.

Wählen Sie **Erstellen** aus, um mit der Erstellung der Azure-Ressourcen zu beginnen.

   Nach Abschluss des Assistenten wird die ASP.NET Core-Web-App in Azure veröffentlicht und anschließend im Standardbrowser geöffnet.

  ![Screenshot: Veröffentlichte ASP.NET-Web-App in Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Der App-Name, den Sie in diesem Abschnitt verwendet haben, wird als URL-Präfix mit dem Format `https://<app_name>.azurewebsites.net` verwendet. Kopieren Sie diese URL zur späteren Verwendung in einen Text-Editor.
