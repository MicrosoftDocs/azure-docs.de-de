---
title: Continuous Integration und Continuous Delivery in Azure Data Factory
description: Erfahren Sie, wie Sie Continuous Integration und Continuous Delivery verwenden, um Data Factory-Pipelines aus einer Umgebung (Entwicklung, Test, Produktion) in eine andere zu verschieben.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: c0d3ba8d9bea9fade58ed4a65c6d3ae43ef6acb3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383601"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Continuous Integration und Continuous Delivery in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Übersicht

Bei Continuous Integration wird jede Änderung, die an Ihrer Codebasis vorgenommen wird, automatisch und so früh wie möglich getestet.  Der Continuous Delivery-Prozess folgt auf das Testen während des Continuous Integration-Prozesses. Änderungen werden dabei in ein Staging- oder Produktionssystem gepusht.

In Azure Data Factory bedeuten Continuous Integration und Continuous Delivery (CI/CD), dass Data Factory-Pipelines von einer Umgebung (Entwicklung, Test, Produktion) in eine andere verschoben werden. Azure Data Factory nutzt [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md) zum Speichern der Konfiguration ihrer verschiedenen ADF-Entitäten (Pipelines, Datasets, Datenflüsse usw.). Es gibt zwei empfohlene Methoden zum Höherstufen einer Data Factory in eine andere Umgebung:

-    Automatisierte Bereitstellung über Integration von Data Factory in [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)
-    Manuelles Hochladen einer Resource Manager-Vorlage über Integration der Data Factory-Benutzeroberfläche in Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-Lebenszyklus

Unten ist eine Beispielübersicht des CI/CD-Lebenszyklus in einer Azure Data Factory-Instanz mit Konfiguration per Azure Repos Git angegeben. Weitere Informationen zum Konfigurieren eines Git-Repositorys finden Sie unter [Quellcodeverwaltung in Azure Data Factory](source-control.md).

1.  Eine Data Factory-Entwicklungsinstanz wird erstellt und mit Azure Repos Git konfiguriert. Alle Entwickler sollten über die Berechtigung zum Erstellen von Data Factory-Ressourcen wie Pipelines und Datasets verfügen.

1.  Ein Entwickler [erstellt einen Featurebranch](source-control.md#creating-feature-branches), um eine Änderung vorzunehmen. Er debuggt die Pipelineausführungen mit seinen neuesten Änderungen. Weitere Informationen zum Debuggen einer Pipelineausführung finden Sie unter [Iteratives Entwickeln und Debuggen mit Azure Data Factory](iterative-development-debugging.md).

1.  Wenn ein Entwickler mit den vorgenommenen Änderungen zufrieden ist, erstellt er einen Pull Request vom Featurebranch zum Main- oder Kollaborationsbranch, um seine Änderungen von anderen Entwicklern überprüfen zu lassen.

1.  Nachdem ein Pull Request genehmigt wurde und Änderungen im Mainbranch zusammengeführt wurden, werden die Änderungen in der Entwicklungsfactory veröffentlicht.

1.  Wenn das Team bereit ist für die Bereitstellung der Änderungen in einer Test- oder UAT-Factory (User Acceptance Tests, Benutzerakzeptanztests), wechselt es zu seinem Azure Pipelines-Release und stellt die gewünschte Version der Entwicklungsfactory für UAT bereit. Diese Bereitstellung erfolgt im Rahmen einer Azure Pipelines-Aufgabe, und dabei kommen Resource Manager-Vorlagenparameter zum Einsatz, um die entsprechende Konfiguration anzuwenden.

1.  Nachdem die Änderungen in der Testfactory überprüft wurden, stellen Sie sie mit der nächsten Aufgabe des Pipelines-Release in der Produktionsfactory bereit.

> [!NOTE]
> Nur die Entwicklungsfactory wird einem Git-Repository zugeordnet. Den Test- und Produktionsfactorys sollte kein Git-Repository zugeordnet sein, und sie sollten nur über eine Azure DevOps-Pipeline oder eine Resource Manager-Vorlage aktualisiert werden.

In der nachstehenden Abbildung sind die verschiedenen Schritte dieses Lebenszyklus hervorgehoben.

![Diagramm von Continuous Integration mit Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatisieren von Continuous Integration mit Azure Pipelines-Releases

Im Folgenden finden Sie eine Anleitung zum Einrichten eines Azure Pipelines-Release, mit dem die Bereitstellung einer Data Factory für mehrere Umgebungen automatisiert wird.

### <a name="requirements"></a>Requirements (Anforderungen)

-   Ein Azure-Abonnement, das mit Visual Studio Team Foundation Server oder Azure Repos verknüpft ist und für das der [Azure Resource Manager-Dienstendpunkt](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager) verwendet wird

-   Eine Data Factory-Instanz mit konfigurierter Azure Repos Git-Integration

-   Ein [Azure-Schlüsseltresor](https://azure.microsoft.com/services/key-vault/) mit den Geheimnissen für jede Umgebung

### <a name="set-up-an-azure-pipelines-release"></a>Einrichten eines Azure-Pipelines-Release

1.  Öffnen Sie in [Azure DevOps](https://dev.azure.com/) das Projekt, das für Ihre Data Factory konfiguriert ist.

1.  Wählen Sie links auf der Seite die Option **Pipelines** und dann **Releases** aus.

    ![Auswählen von „Pipelines“ > „Releases“](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Wählen Sie **Neue Pipeline** oder – falls Pipelines vorhanden sind – die Option **Neu** und dann **Neue Releasepipeline** aus.

1.  Wählen Sie die Vorlage **Leere Stufe** aus.

    ![Auswählen von „Leere Stufe“](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Geben Sie im Feld **Name der Stufe** den Namen der Umgebung ein.

1.  Wählen Sie **Artefakt hinzufügen** und dann das Git-Repository aus, das mit Ihrer Entwicklungs-Data Factory konfiguriert wurde. Wählen Sie den [Branch für die Veröffentlichung](source-control.md#configure-publishing-settings) des Repositorys für den **Standardbranch** aus. Dieser Branch für die Veröffentlichung lautet standardmäßig `adf_publish`. Wählen Sie unter **Standardversion** die Option **Letztes Element aus Standardbranch** aus.

    ![Hinzufügen eines Artefakts](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Fügen Sie eine Azure Resource Manager-Bereitstellungsaufgabe hinzu:

    a.  Wählen Sie in der Stufenansicht die Option **Stufenaufgaben anzeigen** aus.

    ![Stufenansicht](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Erstellen Sie eine neue Aufgabe. Suchen Sie nach **AMR-Vorlagenbereitstellung**, und wählen Sie dann **Hinzufügen** aus.

    c.  Wählen Sie unter der Aufgabe „Bereitstellung“ das Abonnement, die Ressourcengruppe und den Speicherort für das Data Factory-Ziel aus. Geben Sie die Anmeldeinformationen an, falls dies erforderlich ist.

    d.  Wählen Sie in der Liste **Aktion** den Eintrag **Create or update resource group** (Ressourcengruppe erstellen oder aktualisieren) aus.

    e.  Wählen Sie neben dem Feld **Vorlage** die Schaltfläche mit den Auslassungszeichen ( **…** ) aus. Suchen Sie nach der Azure Resource Manager-Vorlage, die in Ihrem Branch für die Veröffentlichung des konfigurierten Git-Repositorys generiert wird. Suchen Sie im Ordner <FactoryName> des Branches „adf_publish“ nach der Datei `ARMTemplateForFactory.json`.

    f.  Wählen Sie **…** neben dem Feld **Vorlagenparameter** aus, um die Parameterdatei auszuwählen. Suchen Sie im Ordner <FactoryName> des Branches „adf_publish“ nach der Datei `ARMTemplateParametersForFactory.json`.

    g.  Wählen Sie **…** neben dem Feld **Vorlagenparameter überschreiben** aus, und geben Sie die gewünschten Parameterwerte für das Ziel „Data Factory“ ein. Geben Sie für Anmeldeinformationen aus Azure Key Vault den Namen des Geheimnisses in doppelten Anführungszeichen ein. Geben Sie als Wert hier beispielsweise Folgendes ein, wenn der Name des Geheimnisses „cred1“ lautet: **"$(cred1)"** .

    h. Wählen Sie unter **Bereitstellungsmodus** die Option **Inkrementell** aus.

    > [!WARNING]
    > Im vollständigen Bereitstellungsmodus werden Ressourcen, die in der Ressourcengruppe vorhanden, aber in der neuen Resource Manager-Vorlage nicht angegeben sind, **gelöscht**. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](../azure-resource-manager/templates/deployment-modes.md).

    ![Data Factory-Bereitstellung in der Produktion](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Speichern Sie die Releasepipeline.

1. Wählen Sie zum Auslösen eines Release die Option **Release erstellen** aus. Informationen zum Automatisieren der Erstellung von Releases finden Sie unter [Azure DevOps-Releasetrigger](/azure/devops/pipelines/release/triggers).

   ![Auswählen von „Release erstellen“](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> In CI/CD-Szenarien muss der Typ der Integration Runtime (IR) in unterschiedlichen Umgebungen identisch sein. Wenn sich in Ihrer Entwicklungsumgebung beispielsweise eine selbstgehostete IR befindet, muss diese IR auch in anderen Umgebungen, z. B. Test- und Produktionsumgebungen, den Typ „Selbstgehostet“ aufweisen. Wenn Sie Integration Runtimes in mehreren Stufen freigeben, müssen Sie die Integration Runtimes in allen Umgebungen, z. B. Entwicklungs-, Test- und Produktionsumgebungen, als „Verknüpft selbstgehostet“ konfigurieren.

### <a name="get-secrets-from-azure-key-vault"></a>Abrufen von Geheimnissen aus Azure Key Vault

Wenn Sie Geheimnisse in einer Azure Resource Manager-Vorlage übergeben möchten, empfehlen wir Ihnen die Verwendung von Azure Key Vault mit dem Azure Pipelines-Release.

Es gibt zwei Möglichkeiten, um Geheimnisse zu verarbeiten:

1.  Fügen Sie die Geheimnisse der Parameterdatei hinzu. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../azure-resource-manager/templates/key-vault-parameter.md).

    Erstellen Sie eine Kopie der Parameterdatei, die in den Branch für die Veröffentlichung hochgeladen wird. Legen Sie die Werte der Parameter, die Sie aus Key Vault abrufen möchten, in diesem Format fest:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Bei dieser Methode wird das Geheimnis per Pullvorgang automatisch aus dem Schlüsseltresor abgerufen.

    Die Parameterdatei muss sich auch im Branch für die Veröffentlichung befinden.

1. Fügen Sie vor der im vorherigen Abschnitt beschriebenen Aufgabe für die Azure Resource Manager-Bereitstellung eine [Azure Key Vault-Aufgabe](/azure/devops/pipelines/tasks/deploy/azure-key-vault) hinzu:

    1.  Erstellen Sie auf der Registerkarte **Tasks** eine neue Aufgabe. Suchen Sie nach **Azure Key Vault**, und fügen Sie die Komponente hinzu.

    1.  Wählen Sie in der Key Vault-Aufgabe das Abonnement aus, unter dem Sie den Schlüsseltresor erstellt haben. Geben Sie bei Bedarf Anmeldeinformationen an, und wählen Sie anschließend den Schlüsseltresor aus.

    ![Hinzufügen einer Key Vault-Aufgabe](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Gewähren von Berechtigungen für den Azure Pipelines-Agent

Unter Umständen tritt bei der Azure Key Vault-Aufgabe ein Fehler vom Typ „Zugriff verweigert“ auf, wenn nicht die richtigen Berechtigungen festgelegt wurden. Laden Sie die Protokolle für das Release herunter, und suchen Sie nach der PS1-Datei, die den Befehl zum Erteilen von Berechtigungen für den Azure Pipelines-Agent enthält. Sie können den Befehl direkt ausführen. Alternativ können Sie die Prinzipal-ID aus der Datei kopieren und die Zugriffsrichtlinie manuell im Azure-Portal hinzufügen. Die Berechtigungen `Get` und `List` sind mindestens erforderlich.

### <a name="updating-active-triggers"></a>Aktualisieren von aktiven Triggern

Für die Bereitstellung kann ein Fehler auftreten, wenn Sie versuchen, aktive Trigger zu aktualisieren. Zum Aktualisieren von aktiven Triggern müssen Sie sie manuell beenden und nach der Bereitstellung wieder starten. Dies ist über eine Azure PowerShell-Aufgabe möglich:

1.  Fügen Sie auf der Registerkarte **Tasks** des Release eine **Azure PowerShell**-Aufgabe hinzu. Wählen Sie die Aufgabenversion „4.*“ aus. 

1.  Wählen Sie das Abonnement aus, in dem sich Ihre Factory befindet.

1.  Wählen Sie **Pfad zur Skriptdatei** als Skripttyp aus. Hierfür müssen Sie Ihr PowerShell-Skript in Ihrem Repository speichern. Das folgende PowerShell-Skript kann zum Beenden von Triggern verwendet werden:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Sie können ähnliche Schritte ausführen (mit der Funktion `Start-AzDataFactoryV2Trigger`), um die Trigger nach der Bereitstellung neu zu starten.

Das Data Factory-Team hat am Ende dieses Artikels ein [Beispielskript für vor und nach der Bereitstellung](#script) hinzugefügt. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Manuelles Höherstufen einer Resource Manager-Vorlage für jede Umgebung

1. Wählen Sie in der Liste **ARM-Vorlage** den Eintrag **Export ARM template** (ARM-Vorlage exportieren) aus, um die Resource Manager-Vorlage für Ihre Data Factory in die Entwicklungsumgebung zu exportieren.

   ![Exportieren einer Resource Manager-Vorlage](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Wählen Sie in den Test- und Produktionsfactorys den Eintrag **Import ARM template** (ARM-Vorlage importieren) aus. Mit dieser Aktion gelangen Sie zum Azure-Portal, in dem sie die exportierte Vorlage importieren können. Wählen Sie **Eigene Vorlage im Editor erstellen** aus, um den Editor für Resource Manager-Vorlagen zu öffnen.

   ![Erstellen einer eigenen Vorlage](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Wählen Sie die Option **Datei laden** und anschließend die generierte Resource Manager-Vorlage aus. Dies ist die Datei **arm_template.json** in der ZIP-Datei, die Sie in Schritt 1 exportiert haben.

   ![Bearbeiten der Vorlage](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Geben Sie im Abschnitt mit den Einstellungen die Konfigurationswerte ein, z. B. die Anmeldeinformationen für verknüpfte Dienste. Wählen Sie anschließend die Option **Kaufen** aus, um die Resource Manager-Vorlage bereitzustellen.

   ![Einstellungsabschnitt](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Verwenden benutzerdefinierter Parameter mit der Resource Manager-Vorlage

Wenn Ihre Entwicklungsfactory ein zugeordnetes Git-Repository hat, können Sie die standardmäßigen Parameter der Resource Manager-Vorlage überschreiben, die durch deren Veröffentlichung oder Export generiert wurden. Die Überschreibung der standardmäßigen Parametrisierungsvorlage kann beispielsweise in folgenden Szenarien erforderlich sein:

* Sie verwenden automatisierte CI/CD und möchten einige Eigenschaften während der Resource Manager-Bereitstellung ändern, die Eigenschaften sind standardmäßig aber nicht parametrisiert.
* Die Resource Manager-Standardvorlage ist aufgrund der Größe Ihrer Factory ungültig, da sie mehr als die maximal zulässige Parameteranzahl (256) enthält.

    In Bezug auf das Limit von 256 für benutzerdefinierte Parameter gibt es drei Optionen:    
  
    * Verwenden Sie die benutzerdefinierte Parameterdatei, und entfernen Sie Eigenschaften, die keine Parametrisierung erfordern, d. h. Eigenschaften, die einen Standardwert beibehalten und somit die Parameteranzahl verringern können.
    * Gestalten Sie die Logik im Datenfluss zur Reduzierung von Parametern um. Wenn z. B. alle Pipelineparameter denselben Wert aufweisen, können Sie stattdessen globale Parameter verwenden.
    * Teilen Sie eine Data Factory in mehrere Datenflüsse auf.

Wenn Sie die standardmäßige Parametrisierungsvorlage überschreiben möchten, navigieren Sie zum Verwaltungshub, und wählen Sie **Parametrisierungsvorlage** im Abschnitt für die Quellcodeverwaltung aus. Wählen Sie **Vorlage bearbeiten** aus, um den Code-Editor für die Parametrisierungsvorlage zu öffnen. 

![Verwalten von benutzerdefinierten Parametern](media/author-management-hub/management-hub-custom-parameters.png)

Beim Erstellen einer benutzerdefinierten Parametrisierungsvorlage wird im Stammordner Ihres Git-Branches eine Datei mit dem Namen **arm-template-parameters-definition.json** erstellt. Sie müssen exakt diesen Dateinamen verwenden.

![Benutzerdefinierte Parameterdatei](media/continuous-integration-deployment/custom-parameters.png)

Beim Veröffentlichen aus dem Kollaborationsbranch liest Data Factory diese Datei und verwendet deren Konfiguration zum Generieren der Eigenschaften, die parametrisiert werden sollen. Sollte keine Datei gefunden werden, wird die Standardvorlage verwendet.

Beim Exportieren einer Resource Manager-Vorlage liest Data Factory diese Datei aus dem Branch, an dem Sie gerade arbeiten, und nicht aus dem Kollaborationsbranch. Sie können die Datei in einem privaten Branch erstellen oder bearbeiten und Ihre Änderungen testen, indem Sie auf der Benutzeroberfläche die Option **Export ARM Template** (ARM-Vorlage exportieren) auswählen. Anschließend können Sie die Datei mit dem Kollaborationsbranch zusammenführen.

> [!NOTE]
> Durch eine benutzerdefinierte Parametrisierungsvorlage wird das ARM-Vorlagenparameterlimit von 256 nicht geändert. Sie können in ihr die parametrisierten Eigenschaften auswählen und ihre Anzahl verringern.

### <a name="custom-parameter-syntax"></a>Benutzerdefinierte Parametersyntax

Nachstehend finden Sie einige Richtlinien, die beim Erstellen der benutzerdefinierten Parameterdatei, **arm-template-parameters-definition.json**, befolgt werden müssen. Die Datei enthält jeweils einen eigenen Abschnitt für die Entitätstypen „trigger“, „pipeline“, „linked service“, „dataset“, „integration runtime“ und „data flow“.

* Geben Sie den Eigenschaftenpfad unter dem relevanten Entitätstyp ein.
* Durch das Festlegen eines Eigenschaftennamens auf `*` geben Sie an, dass alle untergeordneten Eigenschaften parametrisiert werden sollen (nicht rekursiv, sondern nur bis zur ersten Ebene). Sie können auch Ausnahmen für diese Konfiguration angeben.
* Wenn Sie den Wert einer Eigenschaft als Zeichenfolge festlegen, geben Sie damit an, dass die Eigenschaft parametrisiert werden soll. Verwenden Sie das Format `<action>:<name>:<stype>`.
   *  `<action>` kann für eines dieser Zeichen durchgeführt werden:
      * `=` bedeutet, dass der aktuelle Wert als Standardwert für den Parameter beibehalten werden soll.
      * `-` bedeutet, dass der Standardwert für den Parameter nicht beibehalten werden soll.
      * `|` ist ein Sonderfall für Geheimnisse aus Azure Key Vault für Verbindungszeichenfolgen oder Schlüssel.
   * `<name>` ist der Name des Parameters. Wenn dieser Wert leer ist, wird der Name der Eigenschaft verwendet. Beginnt der Wert mit dem Zeichen `-`, wird der Name gekürzt. `AzureStorage1_properties_typeProperties_connectionString` wird beispielsweise in `AzureStorage1_connectionString` gekürzt.
   * `<stype>` ist der Typ des Parameters. Wenn `<stype>` leer ist, wird standardmäßig der Typ `string` verwendet. Unterstützte Werte: `string`, `securestring`, `int`, `bool`, `object`, `secureobject` und `array`.
* Wenn Sie ein Array in der Definitionsdatei angeben, bedeutet dies, dass die entsprechende Eigenschaft in der Vorlage ein Array ist. Data Factory durchläuft alle Objekte im Array anhand der Definition, die im Integration Runtime-Objekt des Arrays angegeben ist. Das zweite Objekt (eine Zeichenfolge) wird zum Namen der Eigenschaft, der bei jeder Iteration als Name für den Parameter verwendet wird.
* Eine Definition kann nicht spezifisch für eine Ressourceninstanz sein. Jede Definition gilt für alle Ressourcen dieses Typs.
* Standardmäßig werden alle sicheren Zeichenfolgen parametrisiert, z. B. Key Vault-Geheimnisse, Verbindungszeichenfolgen, Schlüssel und Token.
 
### <a name="sample-parameterization-template"></a>Exemplarische Parametrisierungsvorlage

Hier ist ein Beispiel dafür angegeben, wie eine Parametrisierungsvorlage aussehen kann:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Im Folgenden wird das Erstellen der obigen Vorlage mit einer Aufschlüsselung nach Ressourcentypen beschrieben.

#### <a name="pipelines"></a>Pipelines
    
* Jede Eigenschaft im Pfad `activities/typeProperties/waitTimeInSeconds` wird parametrisiert. Jede Aktivität in einer Pipeline, die eine Eigenschaft auf Codeebene mit dem Namen `waitTimeInSeconds` enthält (z. B. die Aktivität `Wait`), wird als Zahl mit einem Standardnamen parametrisiert. Die Aktivität verfügt jedoch nicht über einen Standardwert in der Resource Manager-Vorlage. Hierbei handelt es sich um eine erforderliche Eingabe bei der Resource Manager-Bereitstellung.
* Analog dazu wird eine Eigenschaft namens `headers` (etwa in einer Aktivität vom Typ `Web`) mit dem Typ `object` (JObject) parametrisiert. Sie verfügt über einen Standardwert (gleicher Wert wie für die Quellfactory).

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alle Eigenschaften unter dem Pfad `typeProperties` werden mit ihren jeweiligen Standardwerten parametrisiert. Beispielsweise sind unter Eigenschaften vom Typ `IntegrationRuntimes` zwei Eigenschaften vorhanden: `computeProperties` und `ssisProperties`. Beide Eigenschaftentypen werden mit ihren jeweiligen Standardwerten und -typen (Objekt) erstellt.

#### <a name="triggers"></a>Trigger

* Unter `typeProperties` werden zwei Eigenschaften parametrisiert. Die erste ist `maxConcurrency`. Diese Eigenschaft besitzt einen Standardwert und ist vom Typ `string`. Der Standardparametername lautet `<entityName>_properties_typeProperties_maxConcurrency`.
* Die Eigenschaft `recurrence` wird ebenfalls parametrisiert. Darunter werden alle Eigenschaften auf dieser Ebene gemäß Angabe als Zeichenfolgen mit Standardwerten und Parameternamen parametrisiert. Eine Ausnahme ist die `interval`-Eigenschaft, für die beim Parametrisieren der Typ `int` verwendet wird. An den Parameternamen ist das Suffix `<entityName>_properties_typeProperties_recurrence_triggerSuffix` angehängt. Analog dazu ist die Eigenschaft `freq` eine Zeichenfolge und wird als Zeichenfolge parametrisiert. Die Eigenschaft `freq` wird jedoch ohne Standardwert parametrisiert. Der Name wird verkürzt und mit einem Suffix versehen. Beispiel: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Verknüpfte Dienste sind ein Sonderfall. Da verknüpfte Dienste und Datasets eine breite Palette von Typen umfassen, können Sie eine typspezifische Anpassung vornehmen. In diesem Beispiel wird für alle verknüpften Dienste vom Typ `AzureDataLakeStore` eine bestimmte Vorlage angewendet. Für alle anderen Dienste wird eine andere Vorlage angewendet (per `*`).
* Die `connectionString`-Eigenschaft wird als `securestring`-Wert parametrisiert. Sie hat keinen Standardwert. Sie weist einen verkürzten Parameternamen auf, an den das Suffix `connectionString` angehängt ist.
* Die `secretAccessKey`-Eigenschaft ist eine Eigenschaft vom Typ `AzureKeyVaultSecret` (beispielsweise in einem verknüpften Amazon S3-Dienst). Sie wird automatisch als Azure Key Vault-Geheimnis parametrisiert und aus dem konfigurierten Schlüsseltresor abgerufen. Auch der Schlüsseltresor kann parametrisiert werden.

#### <a name="datasets"></a>Datasets

* Für Datasets steht zwar eine typspezifische Anpassung zur Verfügung, aber Sie können die Konfiguration durchführen, ohne dass eine explizite Konfiguration auf der Ebene \* vorhanden sein muss. Im vorherigen Beispiel werden alle Dataseteigenschaften unter `typeProperties` parametrisiert.

### <a name="default-parameterization-template"></a>Standardvorlage für die Parametrisierung

Nachfolgend ist die aktuelle Standardvorlage für die Parametrisierung dargestellt. Falls Sie nur wenige Parameter hinzufügen müssen, ist die direkte Bearbeitung dieser Vorlage ggf. eine gute Idee, weil die vorhandene Parametrisierungsstruktur nicht verloren geht.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Beispiel: Parametrisieren der ID eines vorhandenen interaktiven Azure Databricks-Clusters

Im folgenden Beispiel wird das Hinzufügen eines einzelnen Werts zur Standardvorlage für die Parametrisierung veranschaulicht. Der Parameterdatei soll lediglich die ID eines vorhandenen interaktiven Azure Databricks-Clusters für einen verknüpften Databricks-Dienst hinzugefügt werden. Beachten Sie, dass diese Datei mit der vorherigen Datei nahezu identisch ist. Die einzige Ausnahme ist, dass unter dem Eigenschaftenfeld von `Microsoft.DataFactory/factories/linkedServices` das `existingClusterId`-Element hinzugefügt wurde.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Verknüpfte Resource Manager-Vorlagen

Wenn Sie CI/CD für Ihre Data Factorys eingerichtet haben, werden ggf. die Grenzwerte für Azure Resource Manager-Vorlagen überschritten, wenn die Größe Ihrer Factory zunimmt. Ein Beispiel für eine Beschränkung ist die maximale Anzahl von Ressourcen in einer Resource Manager-Vorlage. Um die Verwendung umfangreicher Factorys zu ermöglichen, während die vollständige Resource Manager-Vorlage für eine Factory generiert wird, werden von Data Factory jetzt verknüpfte Resource Manager-Vorlagen generiert. Mit dieser Funktion wird die gesamte Nutzlast einer Factory auf mehrere Dateien aufgeteilt, damit für Sie keine Einschränkungen aufgrund von Grenzwerten bestehen.

Wenn Sie Git konfiguriert haben, werden die verknüpften Vorlagen zusammen mit den vollständigen Resource Manager-Vorlagen im Branch „adf_publish“ in einem neuen Ordner mit dem Namen „linkedTemplates“ generiert und gespeichert:

![Ordner für verknüpfte Resource Manager-Vorlagen](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Verknüpfte Resource Manager-Vorlagen verfügen normalerweise über eine Mastervorlage und eine Reihe von untergeordneten Vorlagen, die mit der Mastervorlage verknüpft sind. Die übergeordnete Vorlage hat den Namen „ArmTemplate_master.json“, und für untergeordnete Vorlagen werden Namen nach dem Muster „ArmTemplate_0.json“, „ArmTemplate_1.json“ usw. verwendet. 

Um anstelle der vollständigen Resource Manager-Vorlage verknüpfte Vorlagen zu verwenden, sollten Sie den CI/CD-Task so aktualisieren, dass dieser nicht auf „ArmTemplateForFactory.json“ (vollständige Resource Manager-Vorlage), sondern auf „ArmTemplate_master.json“ verweist. Für Resource Manager ist es auch erforderlich, dass Sie die verknüpften Vorlagen in ein Speicherkonto hochladen, damit von Azure während der Bereitstellung darauf zugegriffen werden kann. Weitere Informationen finden Sie unter [Bereitstellen von verknüpften Resource Manager-Vorlagen per VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Denken Sie daran, die Data Factory-Skripts vor und nach dem Bereitstellungstask zu Ihrer CI/CD-Pipeline hinzuzufügen.

Wenn Sie Git nicht konfiguriert haben, können Sie über die Option **Export ARM Template** (ARM-Vorlage exportieren) in der Liste **ARM-Vorlage** auf die verknüpften Vorlagen zugreifen.

## <a name="hotfix-production-environment"></a>Hotfix für Produktionsumgebung

Wenn Sie eine Factory in der Produktionsumgebung bereitstellen und ein sofort zu behebender Fehler vorhanden ist, Sie den aktuellen Kollaborationsbranch aber nicht bereitstellen können, müssen Sie unter Umständen einen Hotfix verwenden. Dieser Ansatz wird als Quick Fix Engineering oder QFE bezeichnet.

1.    Navigieren Sie in Azure DevOps zu dem Release, das für die Produktion bereitgestellt wurde. Suchen Sie nach dem letzten bereitgestellten Commit.

2.    Rufen Sie aus der Commitmeldung die Commit-ID des Kollaborationsbranch ab.

3.    Erstellen Sie aus diesem Commit einen neuen Hotfixbranch.

4.    Navigieren Sie zur Azure Data Factory-Benutzeroberfläche und dort zum neuen Hotfixbranch.

5.    Beheben Sie den Fehler auf der Azure Data Factory-Benutzeroberfläche. Testen Sie die Änderungen.

6.    Wählen Sie nach der Verifizierung der Fehlerbehebung die Option **Export ARM Template** (ARM-Vorlage exportieren) aus, um die Resource Manager-Vorlage für den Hotfix abzurufen.

7.    Checken Sie diesen Build manuell im Branch „adf_publish“ ein.

8.    Wenn Sie die Releasepipeline so konfiguriert haben, dass sie bei Check-Ins in „adf_publish“ automatisch ausgelöst wird, wird automatisch ein neues Release gestartet. Reihen Sie andernfalls manuell ein Release in die Warteschlange ein.

9.    Stellen Sie das Hotfixrelease für die Test- und Produktionsfactorys bereit. Dieses Release enthält die vorherige Nutzlast der Produktionsumgebung sowie die Korrektur, die Sie in Schritt 5 vorgenommen haben.

10.   Fügen Sie dem Entwicklungsbranch die Änderungen aus dem Hotfix hinzu, damit spätere Releases nicht den gleichen Fehler enthalten.

Das folgende Video ist ein ausführliches Videotutorial, in dem Sie erfahren, wie Sie ein Hotfix in Ihren Umgebungen anwenden. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>Anzeigesteuerungs- und Featureflags

Wenn Sie in einem Team arbeiten, gibt es Instanzen, für die Sie Änderungen zusammenführen können, aber nicht möchten, dass sie in Umgebungen mit erhöhten Rechten wie PROD und QA ausgeführt werden. Für dieses Szenario empfiehlt das ADF-Team [das DevOps-Konzept der Verwendung von Featureflags](/azure/devops/migrate/phase-features-with-feature-flags). In ADF können Sie [globale Parameter](author-global-parameters.md) und die [Aktivität „IfCondition“](control-flow-if-condition-activity.md) kombinieren, um Logiksätze auf der Grundlage dieser Umgebungsflags auszublenden.

Informationen zum Einrichten eines Featureflags finden Sie im folgenden Videotutorial:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>Bewährte Methoden für CI/CD

Wenn Sie die Git-Integration mit Ihrer Data Factory verwenden und über eine CI/CD-Pipeline verfügen, die Ihre Änderungen aus der Entwicklungs- in die Test- und dann in die Produktionsumgebung verschiebt, empfehlen wir Ihnen diese bewährten Methoden:

-   **Git-Integration**. Konfigurieren Sie nur Ihre Entwicklungs-Data Factory mit Git-Integration. Änderungen an den Test- und Produktionsumgebungen werden über CI/CD bereitgestellt, und eine Git-Integration wird hierfür nicht benötigt.

-   **Skript für vor und nach der Bereitstellung**. Bevor Sie den Schritt für die Resource Manager-Bereitstellung für CI/CD ausführen, müssen Sie bestimmte Aufgaben erledigen, z. B. das Beenden und erneute Starten von Triggern und das Durchführen einer Bereinigung. Wir empfehlen Ihnen, vor und nach der Bereitstellungsaufgabe PowerShell-Skripts zu verwenden. Weitere Informationen finden Sie unter [Aktualisieren von aktiven Triggern](#updating-active-triggers). Das Data Factory Team hat am Ende dieser Seite [ein Skript](#script) hinzugefügt, das Sie nutzen können.

-   **Integration Runtimes und Freigaben**. Integration Runtimes werden nicht sehr häufig geändert und sind in allen Stufen von CI/CD ähnlich. Daher erwartet Data Factory, dass diese in allen Stufen von CI/CD den gleichen Integration Runtime-Namen bzw. -Typ aufweisen. Wenn Sie Integration Runtimes über alle Stufen hinweg freigeben möchten, können Sie eine ternäre Factory verwenden, die nur die freigegebenen Integration Runtimes enthält. Diese freigegebene Factory können Sie in allen Umgebungen als verknüpften Integration Runtime-Typ verwenden.

-   **Bereitstellung eines verwalteten privaten Endpunkts**. Wenn bereits ein privater Endpunkt in einer Factory vorhanden ist und Sie versuchen, eine ARM-Vorlage bereitzustellen, die einen privaten Endpunkt mit demselben Namen, jedoch mit geänderten Eigenschaften enthält, tritt bei der Bereitstellung ein Fehler auf. Das bedeutet, dass Sie einen privaten Endpunkt erfolgreich bereitstellen können, sofern dieser dieselben Eigenschaften wie der Endpunkt aufweist, der bereits in der Factory vorhanden ist. Wenn eine Eigenschaft in den Umgebungen unterschiedlich ist, können Sie diese überschreiben, indem Sie die Eigenschaft parametrisieren und den entsprechenden Wert während der Bereitstellung angeben.

-   **Key Vault**. Wenn Sie verknüpfte Dienste verwenden, deren Verbindungsinformationen in Azure Key Vault gespeichert sind, wird empfohlen, separate Schlüsseltresore für verschiedene Umgebungen beizubehalten. Sie können auch separate Berechtigungsstufen für jeden Schlüsseltresor konfigurieren. Es kann beispielsweise sein, dass Teammitglieder nicht über Berechtigungen für Produktionsgeheimnisse verfügen sollen. Bei diesem Ansatz empfehlen wir Ihnen, in allen Stufen die gleichen Geheimnisnamen beizubehalten. Wenn Sie die gleichen Geheimnisnamen beibehalten, müssen Sie nicht jede einzelne Verbindungszeichenfolge für alle CI/CD-Umgebungen parametrisieren, weil sich lediglich der Name des Schlüsseltresors ändert, und der ist ein separater Parameter.

-  **Benennen von Ressourcen**. Aufgrund von Einschränkungen bei Resource Manager-Vorlagen können bei der Bereitstellung Probleme auftreten, wenn die Namen Ihrer Ressourcen Leerzeichen enthalten. Daher empfiehlt das Azure Data Factory-Team bei Ressourcennamen die Verwendung der Zeichen „_“ bzw. „-“ anstelle von Leerzeichen. Verwenden Sie beispielsweise „Pipeline_1“ anstelle von „Pipeline 1“.

## <a name="unsupported-features"></a>Nicht unterstützte Funktionen

- Standardmäßig ist für Data Factory ein Cherrypicking von Commits oder die selektive Veröffentlichung von Ressourcen nicht zulässig. Veröffentlichungen umfassen alle in der Data Factory vorgenommenen Änderungen.

    - Data Factory-Entitäten sind voneinander abhängig. Beispielsweise hängen Trigger von Pipelines ab, während Pipelines von Datasets und anderen Pipelines abhängig sind. Die selektive Veröffentlichung einer Teilmenge von Ressourcen kann ggf. zu unerwartetem Verhalten und Fehlern führen.
    - In den seltenen Fällen, in denen Sie eine selektive Veröffentlichung durchführen müssen, können Sie die Verwendung eines Hotfix erwägen. Weitere Informationen finden Sie unter [Hotfix für Produktionsumgebung](#hotfix-production-environment).

- Das Azure Data Factory-Team rät davon ab, Azure RBAC-Steuerelemente einzelnen Entitäten (Pipelines, Datasets usw.) in einer Data Factory zuzuweisen. Wenn ein Entwickler beispielsweise Zugriff auf eine Pipeline oder ein Dataset hat, sollte er in der Lage sein, auf alle Pipelines oder Datasets in der Data Factory zuzugreifen. Wenn Sie der Ansicht sind, dass Sie viele Azure-Rollen innerhalb einer Data Factory implementieren müssen, ziehen Sie die Bereitstellung einer zweiten Data Factory in Betracht.

-   Sie können nicht aus privaten Branches veröffentlichen.

-   Es ist derzeit nicht möglich, Projekte in Bitbucket zu hosten.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Beispielskript für vor und nach der Bereitstellung

Das folgende Beispielskript kann verwendet werden, um Trigger vor der Bereitstellung zu beenden und anschließend neu starten. Außerdem enthält das Skript den Code zum Löschen von Ressourcen, die entfernt wurden. Speichern Sie das Skript in einem Azure DevOps-Git-Repository, und verweisen Sie es über eine Azure PowerShell Aufgabe unter Verwendung von „Version 4.*“.

Wenn Sie ein Skript vor der Bereitstellung ausführen, müssen Sie im Feld **Skriptargumente** eine Variation der folgenden Parameter angeben.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Wenn Sie ein Skript nach der Bereitstellung ausführen, müssen Sie im Feld **Skriptargumente** eine Variation der folgenden Parameter angeben.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell-Aufgabe](media/continuous-integration-deployment/continuous-integration-image11.png)

Hier ist das Skript, das für vor und nach der Bereitstellung verwendet werden kann. Es berücksichtigt gelöschte Ressourcen und Ressourcenverweise.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
