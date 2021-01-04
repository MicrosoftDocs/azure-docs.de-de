---
title: 'Tutorial: Bereinigen von Ressourcen | Azure'
description: In diesem Tutorial erfahren Sie, wie Sie die Azure-Ressourcen bereinigen, die beim Erstellen der Web-App zugeordnet wurden.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: bf03fe9d920298d49e79a9a0febf7e09e94eb6ff
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905518"
---
# <a name="tutorial-clean-up-resources"></a>Tutorial: Bereinigen von Ressourcen

Wenn Sie alle Schritte in diesem mehrteiligen Tutorial ausgeführt haben, haben Sie einen App-Dienst, einen App Service-Hostingplan und ein Speicherkonto in einer Ressourcengruppe erstellt. Außerdem haben Sie eine App-Registrierung in Azure Active Directory erstellt. Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie diese und die App-Registrierung, sodass keine Gebühren mehr anfallen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Löschen der Azure-Ressourcen, die im Rahmen des Tutorials erstellt wurden

## <a name="delete-the-resource-group"></a>Löschen der Ressourcengruppe

Wählen Sie im [Azure-Portal](https://portal.azure.com) im Portalmenü die Option **Ressourcengruppen** und dann die Ressourcengruppe aus, die Ihren App-Dienst und den App Service-Plan enthält.

Wählen Sie **Ressourcengruppe löschen** aus, um die Ressourcengruppe und alle Ressourcen zu löschen.

:::image type="content" alt-text="Screenshot: Löschen der Ressourcengruppe" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Die Ausführung dieses Befehls kann mehrere Minuten dauern.

## <a name="delete-the-app-registration"></a>Löschen der App-Registrierung

Wählen Sie im Menü des Portals **Azure Active Directory** > **App-Registrierungen** aus. Wählen Sie anschließend die von Ihnen erstellte Anwendung aus.
:::image type="content" alt-text="Screenshot: Auswählen der App-Registrierung" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Wählen Sie in der Übersicht der App-Registrierungen die Option **Löschen** aus.
:::image type="content" alt-text="Screenshot: Löschen der App-Registrierung" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Löschen der Azure-Ressourcen, die im Rahmen des Tutorials erstellt wurden

Informieren Sie sich darüber, wie Sie für eine [.NET Core-App](tutorial-dotnetcore-sqldb-app.md), [Python-App](tutorial-python-postgresql-app.md), [Java-App](tutorial-java-spring-cosmosdb.md) oder [Node.js-App](tutorial-nodejs-mongodb-app.md) eine Verbindung mit einer Datenbank herstellen.