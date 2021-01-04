---
title: Unterstützung für Quellzuordnungen für JavaScript-Anwendungen – Azure Monitor Application Insights
description: Hier erfahren Sie, wie Sie mithilfe von Application Insights Quellzuordnungen in den eigenen Speicherkonto-Blobcontainer hochladen.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6403ecd534d17298a10ffcf8f089a2b010fa3a51
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536454"
---
# <a name="source-map-support-for-javascript-applications"></a>Unterstützung für Quellzuordnungen für JavaScript-Anwendungen

Application Insights unterstützt das Hochladen von Quellzuordnungen in den eigenen Speicherkonto-Blobcontainer.
Mithilfe von Quellzuordnungen kann die Minimierung von Aufruflisten auf der Seite mit End-to-End-Transaktionsdetails aufgehoben werden. Die Minimierung aller vom [JavaScript SDK][ApplicationInsights-JS] oder [Node.js SDK][ApplicationInsights-Node.js] gesendeten Ausnahmen kann mit Quellzuordnungen aufgehoben werden.

![Aufheben der Minimierung einer Aufrufliste durch Verknüpfung mit einem Speicherkonto](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Erstellen eines neuen Speicherkontos und eines Blobcontainers

Wenn Sie bereits über ein vorhandenes Speicherkonto oder einen Blobcontainer verfügen, können Sie diesen Schritt überspringen.

1. [Erstellen eines neuen Speicherkontos][create storage account]
2. [Erstellen Sie einen Blobcontainer][create blob container] in Ihrem Speicherkonto. Denken Sie daran, die „öffentliche Zugriffsebene“ auf `Private` festzulegen, um sicherzustellen, dass die Quellzuordnungen nicht öffentlich zugänglich sind.

> [!div class="mx-imgBorder"]
>![Containerzugriffsebene muss auf „Privat“ festgelegt werden](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Übertragen der Quellzuordnungen per Push an Ihren Blobcontainer

Sie sollten die Continuous Deployment-Pipeline in Ihr Speicherkonto integrieren, indem Sie das Konto so konfigurieren, dass die Quellzuordnungen automatisch in den konfigurierten Blobcontainer hochgeladen werden.

Quellzuordnungsdateien können in Ihren Blob Storage-Container mit derselben Ordnerstruktur hochgeladen werden, mit der sie kompiliert und bereitgestellt wurden. Ein gängiger Anwendungsfall ist es, einen Bereitstellungsordner seine Version voranzustellen, z. B. `1.2.3/static/js/main.js`. Beim Deminimieren über einen Azure Blob-Container namens `sourcemaps` wird versucht, eine Quellzuordnungsdatei abzurufen, die sich unter `sourcemaps/1.2.3/static/js/main.js.map` befindet.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Hochladen von Quellzuordnungen über Azure Pipelines (empfohlen)

Wenn Sie Azure Pipelines zum kontinuierlichen Erstellen und Bereitstellen Ihrer Anwendung verwenden, fügen Sie der Pipeline eine Aufgabe vom Typ [Azure-Dateikopiervorgang][azure file copy] hinzu, um die Quellzuordnungen automatisch hochzuladen.

> [!div class="mx-imgBorder"]
> ![Hinzufügen einer Aufgabe vom Typ „Azure-Dateikopiervorgang“ zur Pipeline, um die Quellzuordnungen in Azure Blob Storage hochzuladen](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Konfigurieren der Application Insights-Ressource mit einem Speicherkonto für Quellzuordnungen

### <a name="from-the-end-to-end-transaction-details-page"></a>Auf der Seite mit End-to-End-Transaktionsdetails

Auf der Registerkarte für End-to-End-Transaktionsdetails können Sie auf *Minimierung aufheben* klicken. Daraufhin wird eine Aufforderung zur Konfiguration angezeigt, sofern Ihre Ressource nicht konfiguriert ist.

1. Zeigen Sie im Portal die Details einer Ausnahme an, die minimiert ist.
2. Klicken auf *Minimierung aufheben*
3. Wenn Ihre Ressource nicht konfiguriert ist, wird eine Meldung angezeigt, in der Sie zur Konfiguration aufgefordert werden.

### <a name="from-the-properties-page"></a>Auf der Eigenschaftenseite

Wenn Sie das Speicherkonto oder den Blobcontainer, das bzw. der mit Ihrer Application Insights-Ressource verknüpft ist, konfigurieren oder ändern möchten, können Sie dazu die Registerkarte *Eigenschaften* der Application Insights-Ressource aufrufen.

1. Navigieren Sie zur Registerkarte *Eigenschaften* Ihrer Application Insights-Ressource.
2. Klicken Sie auf *Blobcontainer für Quellzuordnungen ändern*.
3. Wählen Sie einen anderen Blobcontainer als Container für Quellzuordnungen aus.
4. Klicken Sie auf `Apply`.

> [!div class="mx-imgBorder"]
> ![Neukonfiguration des ausgewählten Azure-Blobcontainers durch Navigieren zum Blatt „Eigenschaften“](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Problembehandlung

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>Erforderliche Einstellungen der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) für Ihren Blobcontainer

Jeder Benutzer im Portal, der dieses Feature verwendet, muss dem Blobcontainer mindestens als [Storage-Blobdatenleser][storage blob data reader] zugewiesen sein. Sie müssen diese Rolle jedem Benutzer zuweisen, der die Quellzuordnungen über dieses Feature verwendet.

> [!NOTE]
> Je nachdem, wie der Container erstellt wurde, ist diese Rolle Ihnen oder Ihrem Team möglicherweise nicht automatisch zugewiesen.

### <a name="source-map-not-found"></a>Quellzuordnung wurde nicht gefunden

1. Vergewissern Sie sich, dass die entsprechende Quellzuordnung in den richtigen Blobcontainer hochgeladen wird.
2. Überprüfen Sie, ob die Quellzuordnungsdatei nach der JavaScript-Datei benannt ist, der sie zugeordnet ist, und das Suffix `.map` aufweist.
    - Beispielsweise wird mit `/static/js/main.4e2ca5fa.chunk.js` nach dem Blob mit dem Namen `main.4e2ca5fa.chunk.js.map` gesucht.
3. Überprüfen Sie die Konsole Ihres Browsers, um festzustellen, ob Fehler protokolliert werden. Nehmen Sie diese in ein mögliches Supportticket auf.

## <a name="next-steps"></a>Nächste Schritte

* [Aufgabe „Azure-Dateikopiervorgang“](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
