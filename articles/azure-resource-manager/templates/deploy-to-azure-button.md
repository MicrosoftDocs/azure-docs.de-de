---
title: Schaltfläche zum Bereitstellen in Azure
description: Verwenden Sie diese Schaltfläche, um Azure Resource Manager-Vorlagen aus einem GitHub-Repository bereitzustellen.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 65891cace1cb17614abbfe091e1592d6f13feff4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185724"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Verwenden einer Bereitstellungsschaltfläche zum Bereitstellen von Vorlagen aus einem GitHub-Repository

In diesem Artikel wird beschrieben, wie Sie die Schaltfläche **In Azure bereitstellen** verwenden, um Vorlagen aus einem GitHub-Repository bereitzustellen. Sie können die Schaltfläche direkt zur Datei „README.md“ in Ihrem GitHub-Repository hinzufügen. Oder Sie können die Schaltfläche zu einer Webseite hinzufügen, die auf das Repository verweist.

Der Bereitstellungsumfang wird durch das Vorlagenschema bestimmt. Weitere Informationen finden Sie unter

* [resource groups](deploy-to-resource-group.md)
* [subscriptions](deploy-to-subscription.md)
* [Verwaltungsgruppen](deploy-to-management-group.md)
* [tenants](deploy-to-tenant.md)

## <a name="use-common-image"></a>Verwenden eines einheitlichen Bilds

Verwenden Sie das folgende Bild, wenn Sie die Schaltfläche zu Ihrer Webseite oder Ihrem Repository hinzufügen:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Das Bild sieht so aus:

![Schaltfläche zum Bereitstellen in Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Erstellen einer URL zum Bereitstellen einer Vorlage

Beginnen Sie mit der unformatierten URL für die Vorlage in Ihrem Repository, um die URL für Ihre Vorlage zu erstellen: Klicken Sie auf **Roh**, um die unformatierte URL anzuzeigen.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="Schaltfläche „Roh“ anklicken":::

Die URL hat folgendes Format:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Konvertieren Sie dann die URL in einen URL-codierten Wert. Sie können einen Onlineencoder verwenden oder einen Befehl ausführen. Das folgende PowerShell-Beispiel zeigt, wie ein Wert URL-codiert wird.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

Die Beispiel-URL weist mit URL-Codierung folgenden Wert auf.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Jeder Link beginnt mit der gleichen Basis-URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Fügen Sie Ihren URL-codierten Link zur Vorlage am Ende der Basis-URL hinzu.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Damit ist die URL für Ihren Link vollständig.

Die Vorlage wird in der Regel in einem öffentlichen Repository gehostet. Bei Verwendung eines privaten Repositorys müssen Sie ein Token für den Zugriff auf den unformatierten Inhalt der Vorlage einschließen. Das von GitHub generierte Token ist nur für kurze Zeit gültig. Der Link müsste daher häufig aktualisiert werden.

Wenn Sie [Git mit Azure Repos](/azure/devops/repos/git/) anstelle eines GitHub-Repositorys verwenden, können Sie die Schaltfläche „Bereitstellung in Azure“ dennoch verwenden. Sorgen Sie dafür, dass es sich bei Ihrem Repository um ein öffentliches handelt. Verwenden Sie den [Items-Vorgang](/rest/api/azure/devops/git/items/get), um die Vorlage abzurufen. Ihre Anforderung sollte das folgende Format aufweisen:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Codieren Sie diese Anforderungs-URL.

## <a name="create-deploy-to-azure-button"></a>Erstellen der Schaltfläche für die Bereitstellung in Azure

Fügen Sie zum Schluss Link und Bild zusammen.

Um die Schaltfläche über Markdown in die README.md-Datei in Ihrem GitHub-Repository oder einer Webseite hinzuzufügen, verwenden Sie folgenden Befehl:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Verwenden Sie für HTML folgenden Code:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Bei Git mit einem Azure-Repository weist die Schaltfläche das folgende Format auf:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Um die vollständige Lösung zu testen, klicken Sie auf die folgende Schaltfläche:

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Das Portal zeigt einen Bereich an, indem Sie ganz einfach Parameterwerte angeben können. Die Parameter sind mit den Standardwerten aus der Vorlage vorab ausgefüllt.

![Verwenden des Portals für die Bereitstellung](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Vorlagen finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](template-syntax.md).
