---
title: 'Schnellstart: Bereitstellen von Azure API for FHIR mithilfe des Azure-Portals'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals Azure API for FHIR bereitstellen und Einstellungen konfigurieren.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023464"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Schnellstart: Bereitstellen von Azure API for FHIR mithilfe des Azure-Portals

In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure API for FHIR mithilfe des Azure-Portals bereitstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-new-resource"></a>Neue Ressource erstellen

Öffnen Sie das [Azure-Portal](https://portal.azure.com), und klicken Sie auf **Ressource erstellen**.

![Erstellen einer Ressource](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Suchen nach Azure API for FHIR

Sie können nach Azure API for FHIR suchen, indem Sie „FHIR“ ins Suchfeld eingeben:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Suchen nach APIs für das Gesundheitswesen":::

## <a name="create-azure-api-for-fhir-account"></a>Erstellen eines Azure API for FHIR-Kontos

Wählen Sie **Erstellen** aus, um ein neues Azure API for FHIR-Konto zu erstellen:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Erstellen eines Azure API for FHIR-Kontos":::

## <a name="enter-account-details"></a>Eingeben der Kontodetails

Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe, wählen Sie einen Namen für das Konto aus, und klicken Sie abschließend auf **Überprüfen + erstellen**:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Details der neuen API für das Gesundheitswesen":::

Bestätigen Sie die Erstellung, und warten Sie auf die Bereitstellung der FHIR-API.

## <a name="additional-settings-optional"></a>Zusätzliche Einstellungen (optional)

Sie können auch auf **Weiter: Zusätzliche Einstellungen** klicken, um die Authentifizierungseinstellungen anzuzeigen. In der Standardkonfiguration für Azure API for FHIR [werden Datenebenenrollen mithilfe von Azure RBAC zugewiesen](configure-azure-rbac.md). In diesem Modus wird die Autorität für den FHIR-Dienst auf den Azure Active Directory-Mandanten des Abonnements festgelegt:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Standard-Authentifizierungseinstellungen":::

Beachten Sie, dass das Feld für die Eingabe zulässiger Objekt-IDs abgeblendet ist, da in diesem Fall Azure RBAC verwendet wird, um Rollenzuweisungen zu konfigurieren.

Wenn Sie den FHIR-Dienst für die Verwendung eines externen oder sekundären Azure Active Directory-Mandanten konfigurieren möchten, können Sie die Autorität ändern und Objekt-IDs für Benutzer und Gruppen eingeben, denen Zugriff auf den Server gewährt werden soll. Weitere Informationen finden Sie unter [Konfigurieren der lokalen RBAC für FHIR](configure-local-rbac.md).

## <a name="fetch-fhir-api-capability-statement"></a>Abrufen der FHIR-API-Funktionsbestätigung

Um zu überprüfen, ob das neue FHIR-API-Konto bereitgestellt wurde, rufen Sie eine Funktionsbestätigung ab, indem Sie in einem Browser zu `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` navigieren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, die Azure API for FHIR-Instanz und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe mit dem Azure API for FHIR-Konto und anschließend **Ressourcengruppe löschen** aus. Dann bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Azure API for FHIR in Ihrem Abonnement bereitgestellt. Wenn Sie zusätzliche Einstellungen in Azure API for FHIR festlegen möchten, fahren Sie mit der Schrittanleitung für zusätzliche Einstellungen fort. Wenn Sie Azure API for FHIR verwenden möchten, informieren Sie sich ausführlicher über das Registrieren von Anwendungen.

>[!div class="nextstepaction"]
>[Zusätzliche Einstellungen in Azure API for FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Registrieren der Azure Active Directory-Apps für Azure API for FHIR](fhir-app-registration.md)
