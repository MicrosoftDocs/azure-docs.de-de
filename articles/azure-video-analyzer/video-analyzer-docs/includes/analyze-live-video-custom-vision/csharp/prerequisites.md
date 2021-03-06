---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: d4a2c269c2dfe66dd0d3271b3eef8fc8de32e4a5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383742"
---
Dies sind die Voraussetzungen für dieses Tutorial:


* Ein Azure-Konto das ein aktives Abonnement beinhaltet. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keines besitzen.

    > [!NOTE]    
    > Sie benötigen ein Azure-Abonnement, in dem Sie sowohl auf die Rolle [Mitwirkender](../../../../../role-based-access-control/built-in-roles.md#contributor) als auch auf die Rolle [Benutzerzugriffsadministrator](../../../../../role-based-access-control/built-in-roles.md#user-access-administrator) zugreifen können. Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen diese Berechtigungen erteilt.
* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    
    > [!TIP]
    > Bei der Installation von Azure IoT Tools werden Sie unter Umständen aufgefordert, Docker zu installieren. Sie können diese Aufforderung ignorieren.    
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!Important]
> Von diesem Custom Vision-Modul werden nur **Intel x86- und amd64-Architekturen** unterstützt. Überprüfen Sie die Architektur Ihres Edgegeräts an, bevor Sie fortfahren.

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]
