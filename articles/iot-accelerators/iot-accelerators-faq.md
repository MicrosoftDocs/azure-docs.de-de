---
title: Häufig gestellte Fragen zu IoT-Solution Accelerators (Azure) | Microsoft-Dokumentation
description: In diesem Artikel werden die häufig gestellten Fragen zu IoT-Solution Accelerators beantwortet. Er enthält Links zu den GitHub-Repositorys.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903988"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Häufig gestellte Fragen zu IoT-Solution Accelerators

Informationen finden Sie auch in den [häufig gestellten Fragen zur verbundenen Factory](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Wo finde ich den Quellcode für die Solution Accelerators?

Der Quellcode wird in den folgenden GitHub-Repositorys gespeichert:

* [Solution Accelerator für verbundene Factorys](https://github.com/Azure/azure-iot-connected-factory)
* [Solution Accelerator für die Gerätesimulation](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Wo finde ich die Solution Accelerators für Remoteüberwachung und Predictive Maintenance?

Mit Stand vom 10. Dezember 2020 wurden die Accelerators für Remoteüberwachung und Predictive Maintenance von der Website der [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) entfernt und stehen nicht mehr für neue Bereitstellungen zur Verfügung. Die GitHub-Repository für die beiden Accelerators wurden archiviert. Der Code ist weiterhin für alle zugänglich, aber die Repositorys nehmen keine neuen Beiträge an.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Was geschieht mit meinen vorhandenen Remoteüberwachungs- und Predictive Maintenance-Bereitstellungen?

Auf vorhandene Bereitstellungen hat das Entfernen der Solution Accelerators für Remoteüberwachung und Predictive Maintenance keine Auswirkungen, sie funktionieren weiterhin. Auch geforkte Repositorys sind nicht betroffen. Die Masterrepositorys auf GitHub wurden archiviert.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Wie stelle ich den Solution Accelerator für die Gerätesimulation bereit?

Informationen zum Bereitstellen des Solution Accelerators für die Gerätesimulation finden Sie im GitHub-Repository zur [Gerätesimulation](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md).

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Wo finde ich Informationen zu den entfernten Solution Accelerators?

Weitere Informationen finden Sie auf den folgenden Seiten der Website für die früheren Versionen:

* [Remoteüberwachung](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Predictive Maintenance](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Welche SDKs kann ich zum Entwickeln von Geräteclients für die Solution Accelerators verwenden?

Links zu den IoT-Geräte-SDKs in verschiedenen Sprachen (C, .NET, Java, Node.js, Python) finden Sie im GitHub-Repository [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks).

Wenn Sie das DevKit-Gerät verwenden, finden Sie Ressourcen und Beispiele im GitHub-Repository [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk).

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Ich bin Dienstadministrator und möchte die Verzeichniszuordnung zwischen meinem Abonnement und einen bestimmten Azure AD-Mandanten ändern. Wie führe ich diese Aufgabe aus?

Informationen dazu finden Sie unter [Hinzufügen eines vorhandenen Abonnements zu Ihrem Azure AD-Verzeichnis](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Ich möchte bei der Anmeldung über ein Organisationskonto einen Dienstadministrator oder Co-Administrator ändern.

Lesen Sie den Supportartikel [Ändern des Dienstadministrators und des Co-Administrators bei Anmeldung über ein Organisationskonto](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Warum wird dieser Fehler angezeigt? „Ihr Konto hat nicht die erforderlichen Berechtigungen zum Erstellen einer Lösung. Wenden Sie sich an den Administrator Ihres Kontos, oder versuchen Sie es mit einem anderen Konto.“

Orientieren Sie sich an folgendem Diagramm:

![Flussdiagramm für Berechtigungen](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Wenn der Fehler auch nach der Überprüfung noch angezeigt wird und Sie globaler Administrator des Azure AD-Mandanten und Co-Administrator des Abonnements sind, können Sie Ihren Kontoadministrator bitten, den Benutzer zu entfernen und die erforderlichen Berechtigungen in dieser Reihenfolge neu zuzuweisen. Fügen Sie zuerst den Benutzer als globalen Administrator hinzu, und fügen Sie den Benutzer dann als Co-Administrator für das Azure-Abonnement hinzu. Verwenden Sie [Hilfe und Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls die Probleme weiterhin bestehen.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Weshalb wird dieser Fehler angezeigt, wenn ich ein Azure-Abonnement habe? „Zum Erstellen vorkonfigurierter Lösungen ist ein Azure-Abonnement erforderlich. In nur wenigen Minuten können Sie ein kostenloses Testkonto erstellen.“

Wenn Sie sicher sind, dass Sie über ein Azure-Abonnement verfügen, überprüfen Sie die Mandantenzuordnung für Ihr Abonnement, und prüfen Sie, ob der richtige Mandant in der Dropdownliste ausgewählt wurde. Überprüfen Sie anschließend anhand des obigen Diagramms die Zuordnung Ihres Abonnements und dieses Azure AD-Mandanten.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Was ist der Unterschied zwischen dem Löschen einer Ressourcengruppe im Azure-Portal und dem Klicken auf „Löschen“ für einen Solution Accelerator unter azureiotsolutions.com?

* Wenn Sie den Solution Accelerator unter [azureiotsolutions.com](https://www.azureiotsolutions.com/) löschen, werden alle Ressourcen gelöscht, die beim Erstellen des Solution Accelerators bereitgestellt wurden. Wenn Sie dieser Ressourcengruppe weitere Ressourcen hinzugefügt haben, werden diese ebenfalls gelöscht.
* Wenn Sie die Ressourcengruppe im [Azure-Portal](https://portal.azure.com) löschen, werden nur die Ressourcen in dieser Ressourcengruppe gelöscht. Sie müssen auch die Azure Active Directory-Anwendung löschen, die dem Solution Accelerator zugeordnet ist.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kann ich meine bestehenden Investitionen in Azure IoT-Solution Accelerators weiterhin nutzen?

Ja. Jede aktuell vorhandene Lösung wird in Ihrem Azure-Abonnement weiterhin ausgeführt, und der Quellcode bleibt in GitHub verfügbar.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Wie viele IoT Hub-Instanzen kann ich in einem Abonnement bereitstellen?

Standardmäßig können Sie [10 IoT Hubs pro Abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) bereitstellen. Sie können ein [Azure-Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) erstellen, um dieses Limit zu erhöhen. Da jeder Solution Accelerator einen neuen IoT Hub bereitstellt, können Sie somit in einem Abonnement nur bis zu zehn Solution Accelerators bereitstellen.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Wie viele Instanzen von Cosmos DB kann ich in einem Abonnement bereitstellen?

50. Sie können ein [Azure-Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) erstellen, um dieses Limit zu erhöhen. Standardmäßig können Sie jedoch nur 50 Cosmos DB-Instanzen pro Abonnement bereitstellen.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kann ich einen Solution Accelerator erstellen, wenn ich Microsoft Azure für DreamSpark besitze?

> [!NOTE]
> Microsoft Azure für DreamSpark wird jetzt als Microsoft Imagine für Schüler und Studenten bezeichnet.

Derzeit können Sie mit einem Konto für [Microsoft Azure für DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) keinen Solution Accelerator erstellen. Sie können jedoch in wenigen Minuten ein [kostenloses Testkonto für Azure](https://azure.microsoft.com/free/) einrichten, mit dem Sie einen Solution Accelerator erstellen können.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Gewusst wie: Löschen eines Azure AD-Mandanten

Informationen hierzu finden Sie im Blogbeitrag von Eric Golpe: [Walkthrough of Deleting an Azure AD Tenant](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant) (Exemplarische Vorgehensweise zum Löschen eines Azure AD-Mandanten).

### <a name="next-steps"></a>Nächste Schritte

Sie können auch einige andere Features und Funktionen der IoT-Solution Accelerators ausprobieren:

* [Bereitstellen des Solution Accelerators für Connected Factory](quickstart-connected-factory-deploy.md)
* [Sicherheit im Internet der Dinge von Anfang an](../iot-fundamentals/iot-security-ground-up.md)