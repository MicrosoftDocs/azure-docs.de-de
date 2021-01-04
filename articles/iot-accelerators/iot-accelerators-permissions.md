---
title: Verwenden der Azure IoT Solutions-Website – Azure | Microsoft-Dokumentation
description: Beschreibt, wie Sie die azureiotsolutions.com-Website verwenden können, um Ihren Solution Accelerator bereitzustellen.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: b05ed6e1239721bcf3c1cf33d3ee63a992fd9843
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853225"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Verwenden der azureiotsolutions.com-Website zum Bereitstellen Ihres Solution Accelerators

Sie können Azure IoT Solution Accelerators über [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) für Ihr Azure-Abonnement bereitstellen. AzureIoTSolutions.com hostet sowohl die Microsoft Open Source- als auch die Partner Solution Accelerators. Diese Solution Accelerators entsprechen der [Azure IoT-Referenzarchitektur](/azure/architecture/reference-architectures/iot). Sie können über die Website schnell einen Solution Accelerator als Demo- oder Produktionsumgebung bereitstellen.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Wenn Sie mehr Kontrolle über den Bereitstellungsprozess benötigen, können Sie mithilfe der -Befehlszeilenschnittstelle einen Solution Accelerator bereitstellen.

Sie können die Solution Accelerators in den folgenden Konfigurationen bereitstellen:

* **Standard**: Eine erweiterte Infrastrukturbereitstellung zum Entwickeln einer Produktionsumgebung. Der Azure Container Service stellt die Microservices auf mehreren virtuellen Azure-Computern bereit. Kubernetes orchestriert die Docker-Container, die die einzelnen Microservices hosten.
* **Basic**: Eine kostengünstigere Version für Demonstrationszwecke oder zum Testen einer Bereitstellung. Alle Microservices werden auf einem einzelnen virtuellen Azure-Computer bereitgestellt.
* **Lokal**: Eine lokale Computerbereitstellung für das Testen und die Entwicklung. Bei diesem Ansatz werden die Microservices in einem lokalen Docker-Container bereitgestellt, und die Verbindung mit IoT Hub-, Azure Cosmos DB- und Azure-Speicherdiensten in der Cloud wird hergestellt.

Jeder der Solution Accelerators verwendet eine andere Kombination von Azure-Diensten wie IoT Hub, Azure Stream Analytics und Cosmos DB. Weitere Informationen finden Sie unter [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators), und wählen Sie einen Solution Accelerator aus.

## <a name="sign-in-at-azureiotsolutionscom"></a>Anmelden bei azureiotsolutions.com

Bevor Sie einen Solution Accelerator bereitstellen können, müssen Sie sich mit den einem Azure-Abonnement zugeordneten Anmeldeinformationen bei AzureIoTSolutions.com anmelden. Wenn Ihr Konto mehr als einem Microsoft Azure Active Directory (AD)-Mandanten zugeordnet ist, können Sie das zu verwendende Verzeichnis über das **Kontoauswahl-Dropdown** auswählen.

Ihre Berechtigungen zum Bereitstellen von Solution Accelerators, Verwalten von Benutzern und Verwalten von Azure-Diensten sind abhängig von Ihrer Rolle im ausgewählten Verzeichnis. Die allgemeinen Azure AD-Rollen, die den Solution Accelerators zugeordnet sind, umfassen Folgendes:

* **Globaler Administrator**: Pro Azure AD-Mandant kann es viele [globale Administratoren](../active-directory/roles/permissions-reference.md) geben:

  * Wenn Sie einen Azure AD-Mandanten erstellen, sind Sie standardmäßig der globale Administrator dieses Mandanten.
  * Der globale Administrator kann einen grundlegenden und einen Standard-Solution Accelerator bereitstellen.

* **Domänenbenutzer**: Pro Azure AD-Mandant können viele Domänenbenutzer vorhanden sein. Ein Domänenbenutzer kann einen grundlegenden Solution Accelerator bereitstellen.

* **Gastbenutzer**: Pro Azure AD-Mandant kann es viele Gastbenutzer geben. Gastbenutzer können keinen Solution Accelerator im Azure AD-Mandanten bereitstellen.

Weitere Informationen zu Benutzern und Rollen in Azure AD finden Sie in den folgenden Ressourcen:

* [Erstellen von Benutzern in Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Zuweisen von Benutzern zu Apps](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Wählen Ihres Geräts

Die AzureIoTSolutions.com-Website enthält Links zum [Azure Certified for IoT-Gerätekatalog](https://catalog.azureiotsolutions.com/).

Der Katalog enthält Hunderte von zertifizierten IoT-Hardwaregeräten, die Sie mit Ihren Solution Accelerators verbinden können, um mit der Erstellung Ihrer IoT-Lösung zu beginnen.

![Gerätekatalog](media/iot-accelerators-permissions/devicecatalog.png)

Wenn Sie ein Hardwarehersteller sind, klicken Sie auf **Partner werden**, um mehr über die Partnerschaft mit Microsoft im Certified for IoT-Programm zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Schnellstart an, wenn Sie einen der IoT-Solution Accelerators testen möchten: [Testen einer Lösung für verbundene Factorys](quickstart-connected-factory-deploy.md).
