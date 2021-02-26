---
title: Ändern der Einstellungen für eine Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie als Administrator Ihre Azure IoT Central-Anwendung verwalten, indem Sie den Anwendungsnamen und die URL ändern, das Image hochladen und eine Anwendung löschen.
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fde4f236a48e00b20817a812810fc3ad7d4b227f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521882"
---
# <a name="change-iot-central-application-settings"></a>Ändern der Einstellungen für eine Azure IoT Central-Anwendung



In diesem Artikel wird beschrieben, wie Sie als Administrator Anwendungen verwalten können, indem Sie den Anwendungsnamen und die URL ändern, das Image hochladen und eine Anwendung in ihrer Azure IoT Central-Anwendung löschen.

Um auf den Abschnitt **Verwaltung** zugreifen und diesen verwenden zu können, muss Ihnen die Rolle **Administrator** für eine Azure IoT Central-Anwendung zugewiesen sein. Wenn Sie eine Azure IoT Central-Anwendung erstellen, wird Ihnen automatisch die Rolle **Administrator** für die jeweilige Anwendung zugewiesen.

## <a name="change-application-name-and-url"></a>Ändern von Anwendungsnamen und -URLs

Auf der Seite **Anwendungseinstellungen** können Sie den Namen und die URL Ihrer Anwendung ändern. Wählen Sie im Anschluss **Speichern** aus.

![Seite „Anwendungseinstellungen“](media/howto-administer/image0-a.png)

Wenn Ihr Administrator ein benutzerdefiniertes Design für Ihre Anwendung erstellt, enthält diese Seite eine Option zum Ausblenden des **Anwendungsnamens** in der Benutzeroberfläche. Diese Option ist hilfreich, wenn das Anwendungslogo im benutzerdefinierten Design den Anwendungsnamen enthält. Weitere Informationen finden Sie unter [Anpassen der Azure IoT Central-Benutzeroberfläche](./howto-customize-ui.md).

> [!Note]
> Wenn Sie Ihre URL ändern, kann Ihre alte URL von einem anderen Azure IoT Central-Kunden verwendet werden. In diesem Fall kann diese nicht mehr von Ihnen verwendet werden. Wenn Sie Ihre URL ändern, funktioniert die alte URL nicht mehr, und Sie müssen Ihre Benutzer über die neue zu verwendende URL benachrichtigen.

## <a name="delete-an-application"></a>Löschen einer Anwendung

Verwenden Sie die Schaltfläche **Löschen**, um Ihre IoT Central-Anwendung dauerhaft zu löschen. Durch diese Aktion werden alle Daten im Zusammenhang mit der Anwendung endgültig gelöscht.

> [!Note]
> Zum Löschen einer Anwendung benötigen Sie auch Berechtigungen zum Löschen von Ressourcen im Azure-Abonnement, das Sie beim Erstellen der Anwendung gewählt haben. Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

## <a name="manage-programmatically"></a>Programmgesteuertes Verwalten

Azure Resource Manager SDK-Pakete für IoT Central sind für Node, Python, C#, Ruby, Java und Go verfügbar. Mit diesen Paketen können Sie IoT Central-Anwendungen erstellen, auflisten, aktualisieren oder löschen. Die Pakete enthalten Hilfsprogramme zum Verwalten von Authentifizierung und Fehlerbehandlung.

Beispiele zur Verwendung der Azure Resource Manager SDKs finden Sie unter [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples).

Weitere Informationen finden Sie in den folgenden GitHub-Repositorys und -Paketen:

| Sprache | Repository | Paket |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die Verwaltung Ihrer Azure IoT Central-Anwendung erfahren haben, wird im nächsten Schritt empfohlen, sich mit dem [Verwalten von Benutzern und Rollen](howto-manage-users-roles.md) in Azure IoT Central vertraut zu machen.
