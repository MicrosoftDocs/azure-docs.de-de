---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 04/25/2019
ms.author: kgremban
ms.openlocfilehash: 485a76cb91e5146f59e6be592ffd9cbba68e585a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146704"
---
## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

In diesem Tutorial verwenden Sie die Azure IoT Tools-Erweiterung zum Entwickeln eines Moduls und zum Erstellen eines **Containerimages** aus den Dateien. Danach pushen Sie dieses Image in ein **Repository**, in dem Ihre Images gespeichert und verwaltet werden. Abschließend stellen Sie Ihr Image aus der Registrierung zur Ausführung auf dem IoT Edge-Gerät bereit.

Sie können eine beliebige Docker-kompatible Registrierung zum Speichern Ihrer Containerimages verwenden. Zwei beliebte Docker-Registrierungsdienste sind [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) und [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In diesem Tutorial wird Azure Container Registry verwendet.

Sollten Sie noch keine Containerregistrierung besitzen, führen Sie die folgenden Schritte aus, um eine neue Containerregistrierung in Azure zu erstellen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Optionen **Ressource erstellen** > **Container** > **Container Registry** aus.

2. Geben Sie die folgenden Werte an, um Ihre Containerregistrierung zu erstellen:

   | Feld | Wert |
   | ----- | ----- |
   | Registrierungsname | Geben Sie einen eindeutigen Namen an. |
   | Abonnement | Wählen Sie ein Abonnement aus der Dropdownliste aus. |
   | Ressourcengruppe | Es wird empfohlen, die gleiche Ressourcengruppe für alle Testressourcen zu verwenden, die Sie während der IoT Edge-Schnellstarts und -Tutorials erstellen. Beispielsweise **IoTEdgeResources**. |
   | Location | Wählen Sie einen Standort in Ihrer Nähe aus. |
   | Administratorbenutzer | Legen Sie **Aktivieren** fest. |
   | SKU | Wählen Sie **Basic** aus. |

5. Klicken Sie auf **Erstellen**.

6. Navigieren Sie nach der Erstellung der Containerregistrierung zu dieser Registrierung, und klicken Sie anschließend auf **Zugriffsschlüssel**.

7. Kopieren Sie die Werte für **Anmeldeserver**, **Benutzername** und **Kennwort**, und speichern Sie sie an einem geeigneten Speicherort. Sie verwenden diese Werte in diesem Tutorial, um Zugriff auf die Containerregistrierung zu gewähren.

   ![Kopieren des Anmeldeservers, des Benutzernamens und des Kennworts für die Containerregistrierung](./media/iot-edge-create-container-registry/registry-access-key.png)