---
title: Authentifizieren der Blobausgabe mit Azure Stream Analytics der verwalteten Identität
description: In diesem Artikel wird die Verwendung von verwalteten Identitäten zum Authentifizieren von Azure Stream Analytics-Aufträgen für die Azure Blob Storage-Ausgabe beschrieben.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/11/2020
ms.openlocfilehash: f1aad2464e4979ecbf35deceaad4de70e05b3004
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573662"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Verwendung von verwalteten Identitäten (Vorschau) zum Authentifizieren von Azure Stream Analytics-Aufträgen für die Azure Blob Storage-Ausgabe

Durch die [Authentifizierung mittels verwalteter Identität](../active-directory/managed-identities-azure-resources/overview.md) (Vorschau) für die Ausgabe an Azure Blob Storage erhalten Stream Analytics-Aufträge direkten Zugriff auf ein Speicherkonto, anstatt eine Verbindungszeichenfolge zu verwenden. Neben der verbesserten Sicherheit ermöglicht Ihnen diese Funktion auch das Schreiben von Daten auf ein Speicherkonto in einem virtuellen Netzwerk (VNET) in Azure.

Dieser Artikel zeigt Ihnen, wie Sie verwaltete Identitäten für die Blobausgabe(n) eines Stream Analytics-Auftrags über das Azure-Portal und mit einer Azure Resource Manager-Bereitstellung aktivieren.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals

1. Erstellen Sie einen neuen Stream Analytics-Auftrag oder öffnen Sie einen vorhandenen im Azure-Portal. Wählen Sie in der Menüleiste auf der linken Bildschirmseite unter **Konfigurieren** die Option **Verwaltete Identität**. Vergewissern Sie sich, dass „Systemseitig zugewiesene verwaltete Identität verwenden“ ausgewählt ist, und klicken Sie dann auf die Schaltfläche **Speichern** am unteren Bildschirmrand.

   ![Konfigurieren einer verwalteten Identität für Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Wählen Sie im Fenster mit den Ausgabeeigenschaften der Azure Blob Storage-Ausgabesenke die Dropdownliste der Authentifizierungsmodi, und wählen Sie **Verwaltete Identität** aus. Informationen zu den anderen Ausgabeeigenschaften finden Sie unter [Grundlegendes zu den Ausgaben von Azure Stream Analytics](./stream-analytics-define-outputs.md). Wenn Sie fertig sind, klicken Sie auf **Speichern**.

   ![Konfigurieren der Azure Blob Storage-Ausgabe](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Nachdem der Auftrag nun erstellt wurde, finden Sie weitere Informationen im Abschnitt [Stream Analytics-Auftrag Zugriff auf Ihr Speicherkonto geben](#give-the-stream-analytics-job-access-to-your-storage-account) in diesem Artikel.

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-Bereitstellung

Mit dem Azure Resource Manager können Sie die Bereitstellung Ihres Stream Analytics-Auftrags vollständig automatisieren. Sie können Resource Manager-Vorlagen entweder mit Azure PowerShell oder der [Azure-Befehlszeilenschnittstelle](/cli/azure/) bereitstellen. In den folgenden Beispielen wird die Azure-Befehlszeilenschnittstelle verwendet.


1. Sie können eine **Microsoft.StreamAnalytics/streamingjobs**-Ressource mit einer verwalteten Identität erstellen, indem Sie die folgende Eigenschaft in den Ressourcenabschnitt Ihrer Resource Manager-Vorlage einfügen:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Diese Eigenschaft weist Azure Resource Manager an, die Identität für Ihren Stream Analytics-Auftrag zu erstellen und zu verwalten. Nachfolgend finden Sie eine exemplarische Resource Manager-Vorlage, die einen Stream Analytics-Auftrag mit aktivierter, verwalteter Identität und eine Blob-Ausgabesenke mit verwalteter Identität bereitstellt:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Der oben genannte Auftrag kann mit dem folgenden Azure CLI-Befehl in der Ressourcengruppe **ExampleGroup** bereitgestellt werden:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Nachdem der Auftrag erstellt wurde, können Sie mit dem Azure Resource Manager die vollständige Definition des Auftrags abrufen.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Der obige Befehl gibt eine Antwort wie die folgende zurück:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Beachten Sie die **principalId** aus der Auftragsdefinition, die die verwaltete Identität Ihres Auftrags innerhalb von Azure Active Directory identifiziert und im nächsten Schritt verwendet wird, um dem Stream Analytics-Auftrag Zugriff auf das Speicherkonto zu gewähren.

3. Nachdem der Auftrag nun erstellt wurde, finden Sie weitere Informationen im Abschnitt [Stream Analytics-Auftrag Zugriff auf Ihr Speicherkonto geben](#give-the-stream-analytics-job-access-to-your-storage-account) in diesem Artikel.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Geben Sie dem Stream Analytics-Auftrag Zugriff auf Ihr Speicherkonto

Es gibt zwei Zugriffsebenen, die Sie wählen können, um Ihren Stream Analytics-Auftrag Folgendes zuzuweisen:

1. **Zugriff auf Containerebene:** Diese Option ermöglicht dem Auftrag den Zugriff auf einen bestimmten vorhandenen Container.
2. **Zugriff auf Kontoebene:** Diese Option gewährt dem Auftrag allgemeinen Zugriff auf das Speicherkonto, einschließlich der Möglichkeit, neue Container zu erstellen.

Wenn Sie den Auftrag nicht benötigen, um Container für Sie zu erstellen, sollten Sie den **Zugriff auf Containerebene** wählen, da diese Option dem Auftrag den erforderlichen Mindestzugang gewährt. Beide Optionen werden im Folgenden für das Azure-Portal und die Befehlszeile erläutert.

### <a name="grant-access-via-the-azure-portal"></a>Zugriff über das Azure-Portal gewähren

#### <a name="container-level-access"></a>Zugriff auf Containerebene

1. Navigieren Sie in Ihrem Speicherkonto zum Konfigurationsbereich des Containers.

2. Wählen Sie auf der linken Seite **Zugriffssteuerung (IAM)** aus.

3. Klicken Sie im Abschnitt „Rollenzuweisung hinzufügen“ auf **Hinzufügen**.

4. Im Bereich „Rollenzuweisung“:

    1. Legen Sie die **Rolle** als „Mitwirkender an Speicherblobdaten“ fest.
    2. Stellen Sie sicher, dass die Dropdownliste **Zugriff zuweisen** auf „Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal“ gesetzt ist.
    3. Geben Sie den Namen Ihres Stream Analytics-Auftrags in das Suchfeld ein.
    4. Wählen Sie Ihren Stream Analytics-Auftrag aus, und klicken Sie auf **Speichern**.

   ![Containerzugriff gewähren](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Zugriff auf Kontoebene

1. Navigieren Sie zum Speicherkonto.

2. Wählen Sie auf der linken Seite **Zugriffssteuerung (IAM)** aus.

3. Klicken Sie im Abschnitt „Rollenzuweisung hinzufügen“ auf **Hinzufügen**.

4. Im Bereich „Rollenzuweisung“:

    1. Legen Sie die **Rolle** als „Mitwirkender an Speicherblobdaten“ fest.
    2. Stellen Sie sicher, dass die Dropdownliste **Zugriff zuweisen** auf „Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal“ gesetzt ist.
    3. Geben Sie den Namen Ihres Stream Analytics-Auftrags in das Suchfeld ein.
    4. Wählen Sie Ihren Stream Analytics-Auftrag aus, und klicken Sie auf **Speichern**.

   ![Zugriff auf das Konto gewähren](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Zugriff über das Befehlszeile gewähren

#### <a name="container-level-access"></a>Zugriff auf Containerebene

Um Zugriff auf einen bestimmten Container zu erhalten, führen Sie den folgenden Befehl mit der Azure CLI aus:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Zugriff auf Kontoebene

Um Zugriff auf das gesamte Konto zu erhalten, führen Sie den folgenden Befehl mit der Azure CLI aus:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Aktivieren des VNET-Zugriffs

Bei der Konfiguration der **Firewalls und virtuellen Netzwerke** Ihres Speicherkonto können Sie optional den Netzwerkdatenverkehr von anderen vertrauenswürdigen Microsoft-Diensten zulassen. Wenn Stream Analytics mit einer verwalteten Identität authentifiziert wird, ist der Nachweis erbracht, dass die Anforderung von einem vertrauenswürdigen Dienst stammt. Nachfolgend finden Sie Anweisungen, um diese VNET-Zugriffsausnahme zu aktivieren.

1.    Navigieren Sie im Konfigurationsbereich des Speicherkontos zum Bereich „Firewalls und virtuelle Netzwerke“.
2.    Stellen Sie sicher, dass die Option „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“ aktiviert ist.
3.    Wenn Sie sie aktiviert haben, klicken Sie auf **Speichern**.

   ![Aktivieren des VNET-Zugriffs](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>Entfernen der verwalteten Identität

Die für einen Stream Analytics-Auftrag erstellte verwaltete Identität wird nur gelöscht, wenn der Auftrag gelöscht wird. Es gibt keine Möglichkeit, die verwaltete Identität zu löschen, ohne den Auftrag zu löschen. Wenn Sie die verwaltete Identität nicht mehr verwenden möchten, können Sie die Authentifizierungsmethode für die Ausgabe ändern. Die verwaltete Identität bleibt weiterhin bestehen, bis der Auftrag gelöscht wird, und wird verwendet, wenn Sie sich noch einmal mithilfe einer verwalteten Identität authentifizieren.

## <a name="limitations"></a>Einschränkungen
Nachfolgend finden Sie die aktuellen Einschränkungen dieses Features:

1. Klassische Azure Storage-Konten.

2. Azure-Konten ohne Azure Active Directory.

3. Mehrinstanzenfähiger Zugriff wird nicht unterstützt. Der für einen bestimmten Stream Analytics-Auftrag erstellte Dienstprinzipal muss in dem Azure Active Directory-Mandanten ausgeführt werden, in dem der Auftrag erstellt wurde, und er kann nicht für eine Ressource in einem anderen Azure Active Directory-Mandanten verwendet werden.

4. [Vom Benutzer zugewiesene Identität](../active-directory/managed-identities-azure-resources/overview.md) wird nicht unterstützt. Das heißt, der Benutzer kann nicht seinen eigenen Dienstprinzipal eingeben, um ihn für seinen Stream Analytics-Auftrag zu verwenden. Der Dienstprinzipal muss von Azure Stream Analytics generiert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu den Ausgaben von Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Benutzerdefinierte Blobausgabepartitionierung in Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)