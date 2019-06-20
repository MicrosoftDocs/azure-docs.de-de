---
title: Verwenden des Azure-Portals zum Bereitstellen einer Dienstkatalog-App | Microsoft-Dokumentation
description: Zeigt Anwendern von verwalteten Anwendungen, wie eine Dienstkatalog-App über das Azure-Portal bereitgestellt wird.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60588883"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Bereitstellen einer Dienstkatalog-App über das Azure-Portal

Im [vorhergehenden Schnellstart](publish-managed-app-definition-quickstart.md) haben Sie die Definition einer verwalteten Anwendung veröffentlicht. In diesem Schnellstart erstellen Sie aus dieser Definition eine Dienstkatalog-App.

## <a name="create-service-catalog-app"></a>Erstellen einer Dienstkatalog-App

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie **Ressource erstellen**.

   ![Erstellen einer Ressource](./media/deploy-service-catalog-quickstart/create-new.png)

1. Suchen Sie nach **Verwaltete Dienstkataloganwendung**, und wählen Sie diese Option unter den verfügbaren Optionen aus.

   ![Suchen nach einer Dienstkataloganwendung](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Eine Beschreibung des verwalteten Anwendungsdiensts wird angezeigt. Klicken Sie auf **Erstellen**.

   ![Klicken auf „Erstellen“](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Im Portal werden die Definitionen für verwaltete Anwendungen, auf die Sie Zugriff haben, angezeigt. Wählen Sie unter den verfügbaren Definitionen diejenige aus, die Sie bereitstellen möchten. Verwenden Sie in diesem Schnellstart die Definition **Verwaltetes Speicherkonto**, die Sie im vorherigen Schnellstart erstellt haben. Klicken Sie auf **Erstellen**.

   ![Auswählen einer Definition für die Bereitstellung](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Geben Sie Werte auf der Registerkarte **Grundlagen** an. Wählen Sie das Azure-Abonnement aus, in dem Sie Ihre Dienstkatalog-App bereitstellen möchten. Erstellen Sie eine neue Ressourcengruppe namens **applicationGroup**. Wählen Sie einen Standort für die App aus. Wenn Sie fertig sind, wählen Sie **OK** aus.

   ![Angeben von grundlegenden Werten](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Geben Sie ein Präfix für den Namen des Speicherkontos an. Wählen Sie die Art des zu erstellenden Speicherkontos aus. Wenn Sie fertig sind, wählen Sie **OK** aus.

   ![Angeben von Werten für den Speicher](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Überprüfen Sie die Zusammenfassung. Wählen Sie nach erfolgreicher Validierung **OK** aus, um mit der Bereitstellung zu beginnen.

   ![Anzeigen der Zusammenfassung](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Anzeigen der Ergebnisse

Nachdem die Dienstkatalog-App bereitgestellt wurde, verfügen Sie über zwei neue Ressourcengruppen. In einer Ressourcengruppe befindet sich die Dienstkatalog-App. Die andere Ressourcengruppe enthält die Ressourcen für die Dienstkatalog-App.

1. Zeigen Sie die Ressourcengruppe **applicationGroup** an, um die Dienstkatalog-App zu sehen.

   ![Anzeigen der Anwendung](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Zeigen Sie die Ressourcengruppe **applicationGroup{Hashzeichen}** an, um die Ressourcen für die Dienstkatalog-App zu sehen.

   ![Anzeigen von Ressourcen](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Nächste Schritte

* Unter [Erstellen und Veröffentlichen der Definition einer verwalteten Anwendung](publish-service-catalog-app.md) erfahren Sie, wie Sie die Definitionsdateien für eine verwaltete Anwendung erstellen.
* Für die Azure-Befehlszeilenschnittstelle finden Sie weitere Informationen unter [Bereitstellen einer Dienstkatalog-App mit der Azure-Befehlszeilenschnittstelle](./scripts/managed-application-cli-sample-create-application.md).
* Für PowerShell finden Sie weitere Informationen unter [Bereitstellen einer Dienstkatalog-App mit PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).