---
title: Fehler des Typs „Ressource nicht gefunden“
description: Beschreibt, wie Sie Fehler beheben, wenn eine Ressource nicht gefunden wird. Der Fehler kann auftreten, wenn Sie eine Azure Resource Manager-Vorlage bereitstellen oder Verwaltungsaktionen durchführen.
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.openlocfilehash: 40f3c2ac61b6a6d5fdbcefdf62761668b013b1db
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526245"
---
# <a name="resolve-resource-not-found-errors"></a>Beheben von Fehlern des Typs „Ressource nicht gefunden“

In diesem Artikel wird der angezeigte Fehler beschrieben, wenn eine Ressource während eines Vorgangs nicht gefunden wird. Normalerweise wird dieser Fehler beim Bereitstellen von Ressourcen angezeigt. Dieser Fehler wird auch angezeigt, wenn Sie Verwaltungsaufgaben ausführen und Azure Resource Manager die erforderliche Ressource nicht finden kann. Wenn Sie z. B. versuchen, Tags zu einer Ressource hinzuzufügen, die nicht vorhanden ist, wird dieser Fehler angezeigt.

## <a name="symptom"></a>Symptom

Es gibt zwei Fehlercodes, die anzeigen, dass die Ressource nicht gefunden wurde. Der Fehler **NotFound** gibt ein Ergebnis ähnlich dem folgenden zurück:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Der Fehler **ResourceNotFound** gibt ein Ergebnis ähnlich dem folgenden zurück:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Ursache

Resource Manager muss die Eigenschaften für eine Ressource abrufen, kann die Ressource aber in Ihrem Abonnement nicht finden.

## <a name="solution-1---check-resource-properties"></a>Lösung 1: Überprüfen der Ressourceneigenschaften

Wenn dieser Fehler beim Ausführen einer Verwaltungsaufgabe auftritt, überprüfen Sie die Werte, die Sie für die Ressource angeben. Die drei zu überprüfenden Werte sind:

* Ressourcenname
* Ressourcengruppenname
* Subscription

Wenn Sie PowerShell oder Azure CLI verwenden, überprüfen Sie, ob Sie den Befehl in dem Abonnement ausführen, das die Ressource enthält. Sie können das Abonnement mit [Set-AzContext-](/powershell/module/Az.Accounts/Set-AzContext) oder [az account set](/cli/azure/account#az-account-set) ändern. Viele Befehle bieten auch einen Abonnementparameter, mit dem Sie ein anderes Abonnement als den aktuellen Kontext angeben können.

Wenn Sie Probleme beim Überprüfen der Eigenschaften haben, melden Sie sich beim [Portal](https://portal.azure.com) an. Suchen Sie die Ressource, die Sie verwenden möchten, und untersuchen Sie den Ressourcennamen, die Ressourcengruppe und das Abonnement.

## <a name="solution-2---set-dependencies"></a>Lösung 2: Festlegen von Abhängigkeiten

Wenn dieser Fehler beim Bereitstellen einer Vorlage auftritt, müssen Sie möglicherweise eine Abhängigkeit hinzufügen. Resource Manager optimiert die Bereitstellung, indem, sofern möglich, gleichzeitig Ressourcen erstellt werden. Wenn eine Ressource nach einer anderen Ressource bereitgestellt werden muss, müssen Sie das **dependsOn**-Element in der Vorlage verwenden. Beim Bereitstellen einer Web-App muss z. B. der App Service-Plan vorhanden sein. Wenn Sie nicht angegeben haben, dass die Web-App vom App Service-Plan abhängig ist, erstellt Resource Manager beide Ressourcen zur gleichen Zeit. Beim Versuch, eine Eigenschaft für die Web-App festzulegen, erhalten Sie eine Fehlermeldung, dass die App Service-Planressource nicht gefunden werden kann, da sie noch nicht vorhanden ist. Sie verhindern diesen Fehler, indem Sie die Abhängigkeit in der Web-App festlegen.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Es ist jedoch ratsam, keine Abhängigkeiten einzurichten, die nicht benötigt werden. Wenn Sie nicht benötigte Abhängigkeiten einrichten, verlängern Sie die Dauer der Bereitstellung, indem Sie verhindern, dass nicht voneinander abhängige Ressourcen parallel bereitgestellt werden. Außerdem besteht die Gefahr, dass Sie Ringabhängigkeiten erstellen, die die Bereitstellung blockieren. Die Funktion [reference](template-functions-resource.md#reference) und die [list*](template-functions-resource.md#list)-Funktionen erstellen eine implizite Abhängigkeit von der angegebenen Ressource, wenn diese Ressource in derselben Vorlage bereitgestellt und mit ihrem eigenen Namen (nicht ihrer Ressourcen-ID) auf sie verwiesen wird. Aus diesem Grund verfügen Sie ggf. über eine höhere Zahl von Abhängigkeiten als in der **dependsOn**-Eigenschaft angegeben. Die Funktion [resourceId](template-functions-resource.md#resourceid) erstellt keine implizite Abhängigkeit und überprüft nicht, ob die Ressource vorhanden ist. Die Funktion [reference](template-functions-resource.md#reference) und die [list*](template-functions-resource.md#list)-Funktionen erstellen keine implizite Abhängigkeit, wenn mit der Ressourcen-ID auf die Ressource verwiesen wird. Um eine implizite Abhängigkeit zu erstellen, übergeben Sie den Namen der Ressource, die in der gleichen Vorlage bereitgestellt wird.

Wenn Abhängigkeitsprobleme auftreten, benötigen Sie Informationen zur Reihenfolge der Ressourcenbereitstellung. So zeigen Sie die Reihenfolge der Vorgänge bei der Bereitstellung an:

1. Wählen Sie den Bereitstellungsverlauf für die Ressourcengruppe aus.

   ![Bereitstellungsverlauf auswählen](./media/error-not-found/select-deployment.png)

2. Wählen Sie aus dem Verlauf eine Bereitstellung und **Ereignisse** aus.

   ![Bereitstellungsereignisse auswählen](./media/error-not-found/select-deployment-events.png)

3. Überprüfen Sie die Abfolge von Ereignissen für jede Ressource. Achten Sie auf den Status der einzelnen Vorgänge. Die folgende Abbildung zeigt beispielsweise drei parallel bereitgestellte Speicherkonten. Beachten Sie, dass die drei Speicherkonten zur selben Zeit gestartet werden.

   ![Parallele Bereitstellung](./media/error-not-found/deployment-events-parallel.png)

   Die nächste Abbildung zeigt drei Speicherkonten, die nicht parallel bereitgestellt werden. Das zweite Speicherkonto ist vom ersten Speicherkonto abhängig, und das dritte Speicherkonto ist vom zweiten Speicherkonto abhängig. Das erste Speicherkonto wird gestartet, akzeptiert und abgeschlossen, bevor das nächste gestartet wird.

   ![Sequenzielle Bereitstellung](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Lösung 3: Abrufen einer externen Ressource

Wenn Sie eine Vorlage bereitstellen und eine Ressource abrufen müssen, die in einem anderen Abonnement oder in einer anderen Ressourcengruppe vorhanden ist, verwenden Sie die [resourceId-Funktion](template-functions-resource.md#resourceid). Diese Funktion kehrt zurück, um den vollqualifizierten Namen der Ressource abzurufen.

Die Abonnement- und Ressourcengruppenparameter in der resourceId-Funktion sind optional. Wenn Sie diese nicht angeben, werden Sie standardmäßig auf das aktuelle Abonnement und die aktuelle Ressourcengruppe festgelegt. Stellen Sie beim Arbeiten mit einer Ressource in einer anderen Ressourcengruppe oder einem anderen Abonnement sicher, dass Sie diese Werte angeben.

Im folgenden Beispiel wird die Ressourcen-ID für eine Ressource abgerufen, die in einer anderen Ressourcengruppe vorhanden ist.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Lösung 4: Abrufen der verwalteten Identität aus der Ressource

Wenn Sie eine Ressource bereitstellen, die implizit eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) erstellt, müssen Sie warten, bis diese Ressource bereitgestellt wurde, bevor Sie Werte für die verwaltete Identität abrufen können. Wenn Sie den Namen der verwalteten Identität an die [Verweis](template-functions-resource.md#reference)funktion (reference) übergeben, versucht Resource Manager, den Verweis aufzulösen, bevor die Ressource und die Identität bereitgestellt sind. Übergeben Sie stattdessen den Namen der Ressource, auf die die Identität angewendet wird. Mit diesem Ansatz wird sichergestellt, dass die Ressource und die verwaltete Identität bereitgestellt sind, bevor Resource Manager die Verweisfunktion auflöst.

Verwenden Sie in der Verweisfunktion `Full`, um alle Eigenschaften, einschließlich der verwalteten Identität, abzurufen.

Das Muster lautet:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Verwenden Sie nicht dieses Muster:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Bei der Vorlage tritt ein Fehler auf.

Um z. B. die Prizipal-ID für eine verwaltete Identität abzurufen, die auf einen virtuellen Computer angewandt wird, verwenden Sie:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Um die Mandanten-ID für eine verwaltete Identität abzurufen, die auf eine VM-Skalierungsgruppe angewandt wird, verwenden Sie:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Lösung 5: Überprüfen von Funktionen

Wenn Sie eine Vorlage bereitstellen, suchen Sie nach Ausdrücken, die die [reference](template-functions-resource.md#reference)- oder [listKeys](template-functions-resource.md#listkeys)-Funktion verwenden. Die von Ihnen bereitgestellten Werte variieren basierend darauf, ob sich die Ressource in der gleichen Vorlage und Ressourcengruppe und im gleichen Abonnement befindet. Vergewissern Sie sich, dass die erforderlichen Parameterwerte für Ihr Szenario angegeben wurden. Befindet sich die Ressource in einer anderen Ressourcengruppe, geben Sie die vollständige Ressourcen-ID an. Verwenden Sie zum Verweisen auf ein Speicherkonto in einer anderen Ressourcengruppe beispielsweise Folgendes:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```
