---
title: Verschieben eines Projekts aus der eingeschränkten Testversion in Azure
titlesuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie ein Projekt aus der eingeschränkten Testversion in Azure verschoben wird.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816519"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Verschieben Ihres Projekts aus der eingeschränkten Testversion in Azure

Mit dem Abschluss des Übergangs von Custom Vision Service zu Azure endet der Support für Projekte aus der eingeschränkten Testversion außerhalb von Azure. In diesem Dokument wird gezeigt, wie Sie mit den Custom Vision-APIs Ihr Projekt aus der eingeschränkten Testversion in eine Azure-Ressource kopieren können.

Unterstützung für die Anzeige von Projekten aus der eingeschränkten Testversion auf der [Custom Vision-Website](https://customvision.ai) endete am 25. März 2019. In diesem Dokument erfahren Sie jetzt, wie Sie die Custom Vision-APIs mit einem [Python-Skript für die Migration](https://github.com/Azure-Samples/custom-vision-move-project) (auf GitHub) verwenden können, um Ihr Projekt für eine Azure-Ressource zu duplizieren.

Weitere Informationen, einschließlich wichtiger Fristen im Einstellungsprozess für eingeschränkte Testversionen, finden Sie in den [Versionshinweisen](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) oder in E-Mail-Nachrichten, die an Besitzer von Projekten aus der eingeschränkten Testversion gesendet werden.

Das [Migrationsskript](https://github.com/Azure-Samples/custom-vision-move-project) ermöglicht es Ihnen, ein Projekt neu zu erstellen, indem Sie alle Tags, Regionen und Bilder in Ihrer aktuellen Iteration herunterladen und dann hochladen. Sie erhalten ein neues Projekt in Ihrem neuen Abonnement, das Sie dann trainieren können.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein gültiges Azure-Abonnement in Verbindung mit dem Microsoft-Konto oder AAD-Konto (Azure Active Directory), das Sie für die Anmeldung bei der [Custom Vision-Website](https://customvision.ai) verwenden möchten. 
    - Wenn Sie noch nicht über ein Azure-Konto verfügen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/) kostenlos.
    - Eine Einführung zu den Konzepten der Abonnements und Ressourcen bei Azure finden Sie im [Handbuch für Entwickler in Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Erstellen von Custom Vision-Ressourcen im Azure-Portal

Für die Verwendung des Custom Vision Service mit Azure müssen Sie im [Azure-Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) Custom Vision-Trainings- und -Vorhersageressourcen erstellen. 

Dabei können Sie einer Ressource mehrere Projekte zuordnen. Ausführlichere Informationen hierzu finden Sie unter [Preise und Einschränkungen](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas). Wenn Sie Custom Vision Service weiterhin kostenlos nutzen möchten, können Sie im Azure-Portal die Dienstebene F0 auswählen. 

> [!NOTE]
> Wenn Sie Ihr Custom Vision-Projekt in eine Azure-Ressource verschieben, erbt dieses die der Azure-Ressource zugrunde liegenden [Berechtigungen]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Wenn andere Benutzer in Ihrer Organisation Besitzer der Azure-Ressource sind, in der sich Ihr Projekt befindet, haben diese Benutzer Zugriff auf Ihr Projekt auf der [Custom Vision-Website](https://customvision.ai). Entsprechend werden Ihre Projekte beim Löschen von Ressourcen gelöscht.  

## <a name="find-your-limited-trial-project-information"></a>Suchen von Informationen zum Projekt aus der eingeschränkten Testversion

Um Ihr Projekt zu verschieben, benötigen Sie die _Projekt-ID_ und den _Trainingsschlüssel_ für das zu migrierende Projekt. Wenn diese Informationen nicht vorliegen, besuchen Sie [https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects), um die ID und den Schlüssel für jedes Ihrer Projekte zu erhalten. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Kopieren Ihres Projekts zu Azure mit dem Python-Beispielcode

Folgen Sie den [Anweisungen im Beispielcode](https://github.com/Azure-Samples/custom-vision-move-project), wobei Sie den Schlüssel und Ihre Projekt-ID für die eingeschränkte Testversion als „Quellmaterial“ und den Schlüssel aus der von Ihnen neu erstellten Azure-Ressource als „Ziel“ verwenden.

Standardmäßig werden alle Projekte aus der eingeschränkten Testversion in der Azure-Region „USA, Süden-Mitte“ gehostet.

## <a name="next-steps"></a>Nächste Schritte

Ihr Projekt wurde in eine Azure-Ressource verschoben. Sie müssen Ihre Trainings- und Vorhersageschlüssel in allen Anwendungen, die Sie geschrieben haben, aktualisieren.

Um Ihr Projekt auf der [Custom Vision-Website](https://customvision.ai) anzuzeigen, melden Sie sich mit demselben Konto an, mit dem Sie sich beim Azure-Portal angemeldet haben. Wenn Ihr Projekt nicht angezeigt wird, bestätigen Sie, dass Sie sich im gleichen Verzeichnis auf der [Custom Vision-Website](https://customvision.ai) befinden wie das Verzeichnis, in dem sich Ihre Ressourcen im Azure-Portal befinden. Sowohl im Azure-Portal als auch auf der Website „CustomVision.ai“ können Sie im Dropdownmenü des Benutzers in der oberen rechten Ecke des Bildschirms auswählen.