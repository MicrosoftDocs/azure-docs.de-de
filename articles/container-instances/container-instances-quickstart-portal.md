---
title: 'Schnellstart: Bereitstellen von Docker-Containern in einer Containerinstanz – Portal'
description: In diesem Schnellstart verwenden Sie das Azure-Portal, um schnell eine containerbasierte Web-App bereitzustellen, die in einer isolierten Azure-Containerinstanz ausgeführt wird.
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004805"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Schnellstart: Bereitstellen einer Containerinstanz in Azure mithilfe des Azure-Portals

Führen Sie mithilfe von Azure Container Instances serverlose Docker-Container schnell und einfach in Azure aus. Sie stellen eine Anwendung bedarfsgesteuert in einer Containerinstanz bereit, wenn Sie keine vollständige Containerorchestrierungsplattform wie Azure Kubernetes Service benötigen.

In diesem Schnellstart stellen Sie mithilfe des Azure-Portals einen isolierten Docker-Container bereit und machen seine Anwendung über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verfügbar. Nachdem Sie einige Einstellungen konfiguriert und den Container bereitgestellt haben, können Sie zur ausgeführten Anwendung navigieren:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Mit Azure Container Instances bereitgestellte App im Browser":::

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][azure-free-account] erstellen, bevor Sie beginnen.

## <a name="create-a-container-instance"></a>Erstellen einer Containerinstanz

Klicken Sie auf **Ressource erstellen** > **Container** > **Container Instances**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Beginnen Sie, eine neue Containerinstanz im Azure-Portal zu erstellen":::

Geben Sie auf der Seite **Allgemeine Informationen** die folgenden Werte in die Textfelder für **Ressourcengruppe**, **Containername** und **Containerimage** ein. Behalten Sie für die anderen Werte die Standardwerte bei, und klicken Sie auf **OK**.

* Ressourcengruppe: **Neue erstellen** > `myresourcegroup`
* Containername: `mycontainer`
* Imagequelle: **Schnellstartimages**
* Containerimage: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Konfigurieren grundlegender Einstellungen für eine neue Containerinstanz im Azure-Portal":::

Verwenden Sie für diesen Schnellstart die Standardeinstellungen, um das öffentliche Microsoft-Image `aci-helloworld` bereitzustellen. Dieses Linux-Beispielimage verpackt eine kleine in Node.js geschriebene Web-App, die eine statische HTML-Seite bedient. Sie können auch eigene Containerimages verwenden, die in Azure Container Registry, Docker Hub oder anderen Registrierungen gespeichert sind.

Geben Sie auf der Seite **Netzwerk** eine **DNS-Namensbezeichnung** für Ihren Container an. Der Name muss in der Azure-Region, in der Sie die Containerinstanz erstellen, eindeutig sein. Ihr Container ist öffentlich unter `<dns-name-label>.<region>.azurecontainer.io` erreichbar. Falls die Fehlermeldung „DNS-Namensbezeichnung ist nicht verfügbar.“ angezeigt wird, sollten Sie eine andere DNS-Namensbezeichnung verwenden.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Konfigurieren von Netzwerkeinstellungen für eine neue Containerinstanz im Azure-Portal":::

Behalten Sie für die anderen Einstellungen die Standardwerte bei, und klicken Sie auf **Überprüfen + Erstellen**.

Nach Abschluss der Überprüfung wird eine Zusammenfassung der Containereinstellungen angezeigt. Wählen Sie **Erstellen** aus, um Ihre Anforderung zur Containerbereitstellung zu übermitteln.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Zusammenfassung der Einstellungen für eine neue Containerinstanz im Azure-Portal":::

Wenn die Bereitstellung startet, wird eine Benachrichtigung mit dem Hinweis angezeigt, dass die Bereitstellung durchgeführt wird. Eine weitere Benachrichtigung wird angezeigt, nachdem die Containergruppe bereitgestellt wurde.

Öffnen Sie die Übersicht für die Containergruppe, indem Sie zu **Ressourcengruppen** > **myresourcegroup** > **mycontainer** navigieren. Beachten Sie den **FQDN** (vollqualifizierter Domänenname) der Containerinstanz sowie den **Status**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Übersicht über die Containergruppe im Azure-Portal":::

Wenn der **Status** den Wert *Ausgeführt* erreicht hat, navigieren Sie in Ihrem Browser zum FQDN des Containers.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Mit Azure Container Instances bereitgestellte App im Browser":::

Glückwunsch! Mit wenigen Konfigurationsschritten haben Sie eine öffentlich zugängliche Anwendung in Azure Container Instances bereitgestellt.

## <a name="view-container-logs"></a>Containerprotokolle anzeigen

Die Protokolle für eine Containerinstanz sind hilfreich, wenn Sie Probleme mit dem Container oder mit der ausgeführten Anwendung behandeln.

Klicken Sie zum Anzeigen der Protokolle des Containers unter **Einstellungen** auf **Container** und dann auf **Protokolle**. Daraufhin sollte die HTTP GET-Anforderung angezeigt werden, die beim Betrachten der Anwendung in Ihrem Browser generiert wurde.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Containerprotokolle im Azure-Portal":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Container nicht mehr benötigen, klicken Sie für die Containerinstanz *mycontainer* auf **Übersicht** und anschließend auf **Löschen**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Löschen der Containerinstanz im Azure-Portal":::

Klicken Sie im Bestätigungsdialogfeld auf **Ja**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Löschen der Bestätigung einer Containerinstanz im Azure-Portal":::

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Azure-Containerinstanz aus einem öffentlichen Microsoft-Image erstellt. Fahren Sie mit dem Azure Container Instances-Tutorial fort, wenn Sie ein Containerimage erstellen und über eine private Azure-Containerregistrierung bereitstellen möchten.

> [!div class="nextstepaction"]
> [Azure Container Instances-Tutorial](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/