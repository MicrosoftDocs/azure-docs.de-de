---
title: Problembehandlung für Azure Data Factory | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie in Azure Data Factory Fehler bei externen Steuerungsaktivitäten beheben können.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c9dd39ffa68d8261f5c5d301d4c351c52b3f27c1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654591"
---
# <a name="troubleshoot-azure-data-factory"></a>Problembehandlung für Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden gängige Methoden zur Problembehandlung für externe Steuerungsaktivitäten in Azure Data Factory beschrieben.

## <a name="connector-and-copy-activity"></a>Connector und Kopieraktivität

Informationen zu Connectorproblemen, wie beispielsweise das Auftreten eines Fehlers bei der Kopieraktivität, finden Sie unter [Problembehandlung für Azure Data Factory-Connectors](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Fehlercode: 3200

- **Meldung**: Fehler 403.

- **Ursache**: `The Databricks access token has expired.`

- **Empfehlung**: Das Azure Databricks-Zugriffstoken ist standardmäßig 90 Tage lang gültig. Erstellen Sie ein neues Token, und aktualisieren Sie den verknüpften Dienst.

### <a name="error-code-3201"></a>Fehlercode: 3201

- **Meldung**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Ursache**: `Bad authoring: Notebook path not specified correctly.`

- **Empfehlung**: Geben Sie den Notebook-Pfad in der Databricks-Aktivität an.

<br/> 

- **Meldung**: `Cluster... does not exist.`

- **Ursache**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Empfehlung**: Überprüfen Sie, ob der Databricks-Cluster vorhanden ist.

<br/> 

- **Meldung**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Ursache**: `Bad authoring.`

- **Empfehlung**: Geben Sie entweder absolute Pfade für die Adressierungsschemas der Arbeitsbereiche oder `dbfs:/folder/subfolder/foo.py` für im Databricks-Dateisystem (Databricks File System, DFS) gespeicherte Dateien an.

<br/> 

- **Meldung**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Ursache**: `Bad authoring.`

- **Empfehlung**: Überprüfen Sie die [Definition des verknüpften Diensts](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Meldung**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Ursache**: `Bad authoring.`

- **Empfehlung**: Überprüfen Sie die [Definition des verknüpften Diensts](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Meldung**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Ursache**: `Bad authoring.`

- **Empfehlung**: Weitere Informationen erhalten Sie in der Fehlermeldung.

<br/>

### <a name="error-code-3202"></a>Fehlercode: 3202

- **Meldung**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Ursache**: `Too many Databricks runs in an hour.`

- **Empfehlung**: Überprüfen Sie alle Pipelines, die diesen Databricks-Arbeitsbereich verwenden, auf ihre Auftragserstellungsrate. Wenn Pipelines insgesamt zu viele Databricks-Ausführungen gestartet haben, migrieren Sie einige Pipelines zu einem neuen Arbeitsbereich.

<br/> 

- **Meldung**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Ursache**: `Authoring error: No value provided for the parameter.`

- **Empfehlung**: Überprüfen Sie den JSON-Code der Pipeline, und vergewissern Sie sich, dass für alle Parameter im Notebook „baseParameters“ ein nicht leerer Wert angegeben ist.

<br/> 

- **Meldung**: `User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not authorized to access cluster.`

- **Ursache:** Der Benutzer, der das Zugriffstoken generiert hat, ist nicht berechtigt, auf den im verknüpften Dienst angegebenen Databricks-Cluster zuzugreifen.

- **Empfehlung**: Stellen Sie sicher, dass der Benutzer über die erforderlichen Berechtigungen im Arbeitsbereich verfügt.

### <a name="error-code-3203"></a>Fehlercode: 3203

- **Meldung**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Ursache:** Der Cluster wurde beendet. Bei interaktiven Clustern ist dieses Problem möglicherweise eine Racebedingung.

- **Empfehlung**: Wenn Sie dieses Problem vermeiden möchten, verwenden Sie Auftragscluster.

### <a name="error-code-3204"></a>Fehlercode: 3204

- **Meldung**: `Job execution failed.`

- **Ursache:** Fehlermeldungen weisen auf verschiedene Probleme hin, z. B. einen unerwarteten Clusterzustand oder eine bestimmte Aktivität. Oft wird keine Fehlermeldung angezeigt.

- **Empfehlung**: –

### <a name="error-code-3208"></a>Fehlercode: 3208

- **Meldung**: `An error occurred while sending the request.`

- **Ursache:** Die Netzwerkverbindung mit dem Databricks-Dienst wurde unterbrochen.

- **Empfehlung**: Wenn Sie eine selbstgehostete Integration Runtime verwenden, stellen Sie sicher, dass die Netzwerkverbindung über die Integration Runtime-Knoten zuverlässig ist. Bei der Verwendung von Azure Integration Runtime funktioniert es in der Regel, den Vorgang zu wiederholen.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Die folgende Tabelle gilt für U-SQL.
 
### <a name="error-code-2709"></a>Fehlercode: 2709

- **Meldung**: `The access token is from the wrong tenant.`

- **Ursache:** Fehlerhafter Azure Active Directory-Mandant (Azure AD).

- **Empfehlung**: Fehlerhafter Azure Active Directory-Mandant (Azure AD).

<br/>

- **Meldung**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Ursache:** Dieser Fehler wird durch die Drosselung von Data Lake Analytics verursacht.

- **Empfehlung**: Verringern Sie die Anzahl der an Data Lake Analytics übermittelten Aufträge. Ändern Sie entweder Data Factory-Trigger und Parallelitätseinstellungen für Aktivitäten, oder erhöhen Sie die Grenzwerte für Data Lake Analytics.

<br/> 

- **Meldung**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Ursache:** Dieser Fehler wird durch die Drosselung von Data Lake Analytics verursacht.

- **Empfehlung**: Verringern Sie die Anzahl der an Data Lake Analytics übermittelten Aufträge. Ändern Sie entweder Data Factory-Trigger und Parallelitätseinstellungen für Aktivitäten, oder erhöhen Sie die Grenzwerte für Data Lake Analytics.

### <a name="error-code-2705"></a>Fehlercode: 2705

- **Meldung**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Ursache:** Der angegebene Dienstprinzipal oder das angegebene Zertifikat hat keinen Zugriff auf die Datei im Speicher.

- **Empfehlung**: Überprüfen Sie, ob der Dienstprinzipal oder das Zertifikat, das der Benutzer für Data Lake Analytics-Aufträge bereitstellt, Zugriff sowohl auf das Data Lake Analytics-Konto als auch die Data Lake Storage-Standardinstanz aus dem Stammordner hat.

### <a name="error-code-2711"></a>Fehlercode: 2711

- **Meldung**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Ursache:** Der angegebene Dienstprinzipal oder das angegebene Zertifikat hat keinen Zugriff auf die Datei im Speicher.

- **Empfehlung**: Überprüfen Sie, ob der Dienstprinzipal oder das Zertifikat, das der Benutzer für Data Lake Analytics-Aufträge bereitstellt, Zugriff sowohl auf das Data Lake Analytics-Konto als auch die Data Lake Storage-Standardinstanz aus dem Stammordner hat.

<br/> 

- **Meldung**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Ursache:** Der Pfad zur U-SQL-Datei ist falsch, oder die Anmeldeinformationen des verknüpften Diensts haben keinen Zugriff.

- **Empfehlung**: Überprüfen Sie den Pfad und die im verknüpften Dienst angegebenen Anmeldeinformationen.

### <a name="error-code-2704"></a>Fehlercode: 2704

- **Meldung**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Ursache:** Der angegebene Dienstprinzipal oder das angegebene Zertifikat hat keinen Zugriff auf die Datei im Speicher.

- **Empfehlung**: Überprüfen Sie, ob der Dienstprinzipal oder das Zertifikat, das der Benutzer für Data Lake Analytics-Aufträge bereitstellt, Zugriff sowohl auf das Data Lake Analytics-Konto als auch die Data Lake Storage-Standardinstanz aus dem Stammordner hat.

### <a name="error-code-2707"></a>Fehlercode: 2707

- **Meldung**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Ursache:** Das Data Lake Analytics-Konto im verlinkten Dienst ist falsch.

- **Empfehlung**: Stellen Sie sicher, dass das richtige Konto angegeben wurde.

### <a name="error-code-2703"></a>Fehlercode: 2703

- **Meldung**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Ursache:** Dieser Fehler stammt aus Data Lake Analytics.

- **Empfehlung**: Der Auftrag wurde an Data Lake Analytics übermittelt, und das Skript konnte nicht ausgeführt werden. Untersuchen Sie dies in Data Lake Analytics. Wechseln Sie im Portal zum Data Lake Analytics-Konto, und suchen Sie unter Angabe der Aktivitätsausführungs-ID von Data Factory (nicht der Pipelineausführungs-ID) nach dem Auftrag. Unter diesem Auftrag finden Sie weitere Informationen zum Fehler und zur entsprechenden Problembehandlung.

   Wenn die Lösung nicht klar ist, wenden Sie sich an das Data Lake Analytics-Supportteam, und geben Sie die Auftrags-URL (Uniform Resource Locator) mit Ihrem Kontonamen und der Auftrags-ID an.
 
## <a name="azure-functions"></a>Azure-Funktionen

### <a name="error-code-3602"></a>Fehlercode: 3602

- **Meldung**: `Invalid HttpMethod: '%method;'.`

- **Ursache:** Die in der Aktivitätsnutzlast angegebene HTTP-Methode wird von der Aktivität der Azure-Funktion nicht unterstützt.

- **Empfehlung**: Die folgenden Http-Methoden werden unterstützt: PUT, POST, GET, DELETE, OPTIONS, HEAD und TRACE.

### <a name="error-code-3603"></a>Fehlercode: 3603

- **Meldung**: `Response Content is not a valid JObject.`

- **Ursache:** Die aufgerufene Azure-Funktion hat keine JSON-Nutzlast in der Antwort zurückgegeben. Die ADF-Aktivität (Azure Data Factory) der Azure-Funktion unterstützt nur JSON-Antwortinhalte.

- **Empfehlung**: Aktualisieren Sie die Azure-Funktion, um eine gültige JSON-Nutzlast zurückzugeben, so wie eine C#-Funktion `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");` zurückgeben kann.

### <a name="error-code-3606"></a>Fehlercode: 3606

- **Meldung**: Azure function activity missing function key. (In der Aktivität der Azure-Funktion fehlt der Funktionsschlüssel.)

- **Ursache:** Die Definition für die Aktivität der Azure-Funktion ist nicht vollständig.

- **Empfehlung**: Überprüfen Sie, ob die JSON-Definition für die Eingabeaktivität der Azure-Funktion die Eigenschaft `functionKey` enthält.

### <a name="error-code-3607"></a>Fehlercode: 3607

- **Meldung**: `Azure function activity missing function name.`

- **Ursache:** Die Definition für die Aktivität der Azure-Funktion ist nicht vollständig.

- **Empfehlung**: Überprüfen Sie, ob die JSON-Definition für die Eingabeaktivität der Azure-Funktion die Eigenschaft `functionName` enthält.

### <a name="error-code-3608"></a>Fehlercode: 3608

- **Meldung**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Ursache:** Die Details der Azure-Funktion in der Aktivitätsdefinition sind möglicherweise falsch.

- **Empfehlung**: Korrigieren Sie die Details der Azure-Funktion, und versuchen Sie es erneut.

### <a name="error-code-3609"></a>Fehlercode: 3609

- **Meldung**: `Azure function activity missing functionAppUrl.`

- **Ursache:** Die Definition für die Aktivität der Azure-Funktion ist nicht vollständig.

- **Empfehlung**: Überprüfen Sie, ob die JSON-Definition für die Eingabeaktivität der Azure-Funktion die Eigenschaft `functionAppUrl` enthält.

### <a name="error-code-3610"></a>Fehlercode: 3610

- **Meldung**: `There was an error while calling endpoint.`

- **Ursache:** Die Funktions-URL ist möglicherweise falsch.

- **Empfehlung**: Überprüfen Sie, ob der Wert für `functionAppUrl` in der JSON-Aktivität richtig ist, und versuchen Sie es erneut.

### <a name="error-code-3611"></a>Fehlercode: 3611

- **Meldung**: `Azure function activity missing Method in JSON.`

- **Ursache:** Die Definition für die Aktivität der Azure-Funktion ist nicht vollständig.

- **Empfehlung**: Überprüfen Sie, ob die JSON-Definition für die Eingabeaktivität der Azure-Funktion die Eigenschaft `method` enthält.

### <a name="error-code-3612"></a>Fehlercode: 3612

- **Meldung**: `Azure function activity missing LinkedService definition in JSON.`

- **Ursache:** Die Definition für die Aktivität der Azure-Funktion ist nicht vollständig.

- **Empfehlung**: Überprüfen Sie, ob die JSON-Definition für die Eingabeaktivität der Azure-Funktion verknüpfte Dienstdetails enthält.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Fehlercode: 4101

- **Meldung**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Ursache:** Ungültiges Format oder fehlende Definition der Eigenschaft `%propertyName;`.

- **Empfehlung**: Überprüfen Sie, ob die Eigenschaft `%propertyName;` für die Aktivität `%activityName;` mit den richtigen Daten definiert wurde.

### <a name="error-code-4110"></a>Fehlercode: 4110

- **Meldung**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Ursache:** Die Definition für die Aktivität „AzureMLExecutePipeline“ ist unvollständig.

- **Empfehlung**: Überprüfen Sie, ob die JSON-Definition für die Aktivität „AzureMLExecutePipeline“ korrekt verknüpfte Dienstdetails enthält.

### <a name="error-code-4111"></a>Fehlercode: 4111

- **Meldung**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Ursache:** Falsche Aktivitätsdefinition.

- **Empfehlung**: Überprüfen Sie, ob die JSON-Definition für die Aktivität „AzureMLExecutePipeline“ korrekt verknüpfte Dienstdetails enthält.

### <a name="error-code-4112"></a>Fehlercode: 4112

- **Meldung**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Ursache:** Ungültiges Format oder fehlende Definition der Eigenschaft „%propertyName;“

- **Empfehlung**: Überprüfen Sie, ob die Eigenschaft `%propertyName;` für den verknüpften Dienst mit den richtigen Daten definiert wurde.

### <a name="error-code-4121"></a>Fehlercode: 4121

- **Meldung**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ursache:** Die für den Zugriff auf Azure Machine Learning verwendeten Anmeldeinformationen sind abgelaufen.

- **Empfehlung**: Überprüfen Sie, ob die Anmeldeinformationen gültig sind, und versuchen Sie es erneut.

### <a name="error-code-4122"></a>Fehlercode: 4122

- **Meldung**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ursache:** Die im verknüpften Dienst für Azure Machine Learning angegebenen Anmeldeinformationen sind ungültig oder haben keine Berechtigung für diesen Vorgang.

- **Empfehlung**: Überprüfen Sie, ob die Anmeldeinformationen im verknüpften Dienst gültig und Berechtigungen für den Zugriff auf Azure Machine Learning vorhanden sind.

### <a name="error-code-4123"></a>Fehlercode: 4123

- **Meldung**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ursache:** Die Eigenschaften der Aktivität, z. B. `pipelineParameters`, sind bei der Azure Machine Learning (ML)-Pipeline ungültig.

- **Empfehlung**: Überprüfen Sie, ob der Wert der Aktivitätseigenschaften mit der erwarteten Nutzlast der veröffentlichten Azure ML-Pipeline übereinstimmt, die im verknüpften Dienst angegeben wird.

### <a name="error-code-4124"></a>Fehlercode: 4124

- **Meldung**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ursache:** Der veröffentlichte Azure ML-Pipelineendpunkt ist nicht vorhanden.

- **Empfehlung**: Überprüfen Sie, ob der veröffentlichte Azure ML-Pipelineendpunkt, der im verknüpften Dienst angegeben wird, in Azure Machine Learning vorhanden ist.

### <a name="error-code-4125"></a>Fehlercode: 4125

- **Meldung**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ursache:** Es gibt einen Serverfehler bei Azure Machine Learning.

- **Empfehlung**: Wiederholen Sie den Vorgang zu einem späteren Zeitpunkt. Sollte das Problem weiterhin auftreten, wenden Sie sich an das Azure Machine Learning-Team.

### <a name="error-code-4126"></a>Fehlercode: 4126

- **Meldung**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Ursache:** Beim Ausführen der Azure ML-Pipeline ist ein Fehler aufgetreten.

- **Empfehlung**: Überprüfen Sie Azure Machine Learning, ob es weitere Fehlerprotokolle gibt. Korrigieren Sie dann die ML-Pipeline.

## <a name="common"></a>Allgemein

### <a name="error-code-2103"></a>Fehlercode: 2103

- **Meldung**: `Please provide value for the required property '%propertyName;'.`

- **Ursache:** Der erforderliche Wert für die-Eigenschaft wurde nicht angegeben.

- **Empfehlung**: Geben Sie den Wert aus der Meldung an, und versuchen Sie es noch mal.

### <a name="error-code-2104"></a>Fehlercode: 2104

- **Meldung**: `The type of the property '%propertyName;' is incorrect.`

- **Ursache:** Der angegebene Eigenschaftentyp ist falsch.

- **Empfehlung**: Korrigieren Sie den Typ der Eigenschaft, und versuchen Sie es erneut.

### <a name="error-code-2105"></a>Fehlercode: 2105

- **Meldung**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Ursache:** Der Wert für die Eigenschaft ist ungültig oder hat nicht das erwartete Format.

- **Empfehlung**: Lesen Sie die Dokumentation zur Eigenschaft, und überprüfen Sie, ob der angegebene Wert das richtige Format und den richtigen Typ enthält.

### <a name="error-code-2106"></a>Fehlercode: 2106

- **Meldung**: `The storage connection string is invalid. %errorMessage;`

- **Ursache:** Die Verbindungszeichenfolge für den Speicher ist ungültig oder hat ein falsches Format.

- **Empfehlung**: Wechseln Sie zum Azure-Portal, und suchen Sie Ihren Speicher. Kopieren Sie dann die Verbindungszeichenfolge, und fügen Sie sie in Ihren verknüpften Dienst ein. Wiederholen Sie anschließend den Vorgang.

### <a name="error-code-2108"></a>Fehlercode: 2108

- **Meldung**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Ursache:** Die Anforderung war aufgrund eines Problems (z. B Netzwerkkonnektivität, DNS-Fehler, Überprüfung des Serverzertifikats oder Timeout) nicht erfolgreich.

- **Empfehlung**: Verwenden Sie Fiddler/Postman, um die Anforderung zu überprüfen.

### <a name="error-code-2110"></a>Fehlercode: 2110

- **Meldung**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Ursache:** Der in der Aktivität angegebene verknüpfte Dienst ist falsch.

- **Empfehlung**: Überprüfen Sie, ob einer der unterstützten Typen für die Aktivität als Typ für den verknüpften Dienst verwendet wird. So kann beispielsweise der Typ für den verknüpften Dienst für HDI-Aktivitäten „HDInsight“ oder „HDInsightOnDemand“ sein.

### <a name="error-code-2111"></a>Fehlercode: 2111

- **Meldung**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Ursache:** Der Typ der angegebenen Eigenschaft ist falsch.

- **Empfehlung**: Korrigieren Sie den Eigenschaftentyp, und versuchen Sie es erneut.

### <a name="error-code-2112"></a>Fehlercode: 2112

- **Meldung**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Ursache:** Der Cloudtyp wird nicht unterstützt oder konnte für den Speicher aus dem Wert für „EndpointSuffix“ nicht ermittelt werden.

- **Empfehlung**: Verwenden Sie einen Speicher in einer anderen Cloud, und versuchen Sie es erneut.

### <a name="error-code-2128"></a>Fehlercode: 2128

- **Meldung**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Ursache:** Netzwerkkonnektivität, DNS-Fehler, Überprüfung des Serverzertifikats oder Timeout

- **Empfehlung**: Überprüfen Sie, ob der Endpunkt, den Sie erreichen möchten, auf Anforderungen reagiert. Dazu können Sie Tools wie Fiddler oder Postman verwenden.

## <a name="custom"></a>Benutzerdefiniert

Die folgende Tabelle gilt für Azure Batch.
 
### <a name="error-code-2500"></a>Fehlercode: 2500

- **Meldung**: `Hit unexpected exception and execution failed.`

- **Ursache**: `Can't launch command, or the program returned an error code.`

- **Empfehlung**: Stellen Sie sicher, dass die ausführbare Datei vorhanden ist. Wenn das Programm gestartet wurde, überprüfen Sie, ob die Dateien *stdout.txt* und *stderr.txt* in das Speicherkonto hochgeladen wurden. Es empfiehlt sich, in den Code zum Debuggen Protokolle einzubeziehen.

### <a name="error-code-2501"></a>Fehlercode: 2501

- **Meldung**: `Cannot access user batch account; please check batch account settings.`

- **Ursache:** Falscher Batch-Zugriffsschlüssel oder Poolname.

- **Empfehlung**: Überprüfen Sie den Poolnamen und den Batch-Zugriffsschlüssel im verknüpften Dienst.

### <a name="error-code-2502"></a>Fehlercode: 2502

- **Meldung**: `Cannot access user storage account; please check storage account settings.`

- **Ursache:** Falscher Speicherkontoname oder Zugriffsschlüssel.

- **Empfehlung**: Überprüfen Sie den Speicherkontonamen und den Zugriffsschlüssel im verknüpften Dienst.

### <a name="error-code-2504"></a>Fehlercode: 2504

- **Meldung**: `Operation returned an invalid status code 'BadRequest'.`

- **Ursache:** Zu viele Dateien in `folderPath` der benutzerdefinierten Aktivität. Die Gesamtgröße von `resourceFiles` darf maximal 32.768 Zeichen betragen.

- **Empfehlung**: Entfernen Sie unnötige Dateien, oder zippen Sie sie. Fügen Sie dann einen „unzip“-Befehl hinzu, um sie zu extrahieren.
   
   Verwenden Sie beispielsweise `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`.

### <a name="error-code-2505"></a>Fehlercode: 2505

- **Meldung**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Ursache:** Benutzerdefinierte Aktivitäten unterstützen nur Speicherkonten, die einen Zugriffsschlüssel verwenden.

- **Empfehlung**: Weitere Informationen erhalten Sie in der Fehlerbeschreibung.

### <a name="error-code-2507"></a>Fehlercode: 2507

- **Meldung**: `The folder path does not exist or is empty: ...`

- **Ursache:** Unter dem angegebenen Pfad sind im Speicherkonto keine Dateien vorhanden.

- **Empfehlung**: „folderPath“ muss die ausführbaren Dateien enthalten, die Sie ausführen möchten.

### <a name="error-code-2508"></a>Fehlercode: 2508

- **Meldung**: `There are duplicate files in the resource folder.`

- **Ursache:** In verschiedenen Unterordnern von „folderPath“ gibt es mehrere Dateien mit demselben Namen.

- **Empfehlung**: Benutzerdefinierte Aktivitäten vereinfachen die Ordnerstruktur unter „folderPath“. Wenn Sie die Ordnerstruktur beibehalten möchten, zippen Sie die Dateien, und extrahieren Sie sie in Azure Batch mit einem Befehl zum Entzippen.
   
   Verwenden Sie beispielsweise `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`.

### <a name="error-code-2509"></a>Fehlercode: 2509

- **Meldung**: `Batch url ... is invalid; it must be in Uri format.`

- **Ursache:** Batch-URLs müssen `https://mybatchaccount.eastus.batch.azure.com` ähneln.

- **Empfehlung**: Weitere Informationen erhalten Sie in der Fehlerbeschreibung.

### <a name="error-code-2510"></a>Fehlercode: 2510

- **Meldung**: `An error occurred while sending the request.`

- **Ursache:** Die Batch-URL ist ungültig.

- **Empfehlung**: Überprüfen Sie die Batch-URL.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Fehlercode: 206

- **Meldung**: `The batch ID for Spark job is invalid. Please retry your job.`

- **Ursache:** Ein internes Problem mit dem Dienst hat diesen Fehler verursacht.

- **Empfehlung**: Dies könnte ein vorübergehendes Problem sein. Wiederholen Sie den Auftrag nach einiger Zeit.

### <a name="error-code-207"></a>Fehlercode: 207

- **Meldung**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Ursache:** Beim Ermitteln der Region aus dem primären Speicherkonto ist ein interner Fehler aufgetreten.

- **Empfehlung**: Versuchen Sie es mit einem anderen Speicher. 

### <a name="error-code-208"></a>Fehlercode: 208

- **Meldung**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Ursache:** Beim Lesen des Dienstprinzipals bzw. beim Instanziieren der MSI-Authentifizierung ist ein interner Fehler aufgetreten.

- **Empfehlung**: Erwägen Sie die Angabe eines Dienstprinzipals, der Berechtigungen zum Erstellen eines HDInsight-Clusters im angegebenen Abonnement hat, und versuchen Sie es erneut. Überprüfen Sie, ob [die verwalteten Identitäten ordnungsgemäß eingerichtet wurden](../hdinsight/hdinsight-managed-identities.md).


### <a name="error-code-2300"></a>Fehlercode: 2300

- **Meldung**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Ursache:** Die Fehlermeldung enthält einen ähnlichen Text wie `The remote name could not be resolved.`. Der angegebene Cluster-URI ist möglicherweise ungültig.

- **Empfehlung**: Vergewissern Sie sich, dass der Cluster nicht gelöscht wurde und der angegebene URI richtig ist. Wenn Sie den URI in einem Browser öffnen, sollte die Ambari-Benutzeroberfläche angezeigt werden. Wenn sich der Cluster in einem virtuellen Netzwerk befindet, sollte der URI der private URI sein. Um es zu öffnen, verwenden Sie einen virtuellen Computer (VM), der zu diesem virtuellen Netzwerk gehört.

   Weitere Informationen finden Sie unter [Herstellen einer direkten Verbindung mit Apache Hadoop-Diensten](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Ursache:** Wenn die Fehlermeldung einen ähnlichen Text wie `A task was canceled.` enthält, war die Auftragserteilung zeitlich begrenzt.

- **Empfehlung**: Das Problem kann entweder ein allgemeines HDInsight-Verbindungsproblem oder ein Netzwerkverbindungsproblem sein. Vergewissern Sie sich zunächst, ob die HDInsight Ambari-Benutzeroberfläche in jedem Browser verfügbar ist. Überprüfen Sie anschließend, ob Ihre Anmeldeinformationen noch gültig sind.
   
   Wenn Sie eine selbstgehostete Integration Runtime (IR) verwenden, führen Sie diesen Schritt auf der VM oder dem Computer aus, auf der bzw. dem die selbstgehostete IR installiert wurde. Übermitteln Sie den Auftrag dann erneut über Data Factory.

   Weitere Informationen finden Sie unter [Ambari-Webbenutzeroberfläche](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui).

 </br>

- **Ursache:** Wenn die Fehlermeldung einen ähnlichen Text wie `User admin is locked out in Ambari` oder `Unauthorized: Ambari user name or password is incorrect` enthält, sind die Anmeldeinformationen für HDInsight falsch oder abgelaufen.

- **Empfehlung**: Korrigieren Sie die Anmeldeinformation, und stellen Sie den verknüpften Dienst erneut bereit. Überprüfen Sie zunächst, ob die Anmeldeinformationen in HDInsight funktionieren. Öffnen Sie dazu den Cluster-URI in einem beliebigen Browser, und versuchen Sie, sich anzumelden. Wenn die Anmeldeinformationen nicht funktionieren, können Sie sie im Azure-Portal zurücksetzen.

   Setzen Sie das Kennwort beim ESP-Cluster über die [Self-Service-Kennwortzurücksetzung](../active-directory/user-help/active-directory-passwords-update-your-own-password.md) zurück.

 </br>

- **Ursache:** Wenn die Fehlermeldung einen ähnlichen Text wie `502 - Web server received an invalid response while acting as a gateway or proxy server` enthält, wird dieser Fehler vom HDInsight-Dienst zurückgegeben.

- **Empfehlung**: Ein Fehler 502 tritt oft auf, wenn Ihr Ambari-Server-Prozess heruntergefahren wurde. Sie können die Ambari-Dienste neu starten, indem Sie den Hauptknoten neu starten.

    1. Stellen Sie mithilfe von SSH eine Verbindung mit einem Ihrer Knoten auf HDInsight her.
    1. Identifizieren Sie den Host Ihres aktiven Hauptknotens durch Ausführung von `ping headnodehost`.
    1. Stellen Sie eine Verbindung mit Ihrem aktiven Hauptknoten per SSH her, da der Ambari-Server sich auf dem Hauptknoten befindet. 
    1. Starten Sie den aktiven Hauptknoten neu.

       Weitere Informationen finden Sie in der Dokumentation zur Problembehandlung bei Azure HDInsight. Beispiel:

       * [Ambari UI 502 error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) (Ambari-Benutzeroberfläche: Fehler 502)
       * [RpcTimeoutException für Apache Spark Thrift-Server](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Behandeln von Fehlern aufgrund eines ungültigen Gateways in Application Gateway](../application-gateway/application-gateway-troubleshooting-502.md)

 </br>

- **Ursache:** Wenn die Fehlermeldung einen ähnlichen Text wie `Unable to service the submit job request as templeton service is busy with too many submit job requests` oder `Queue root.joblauncher already has 500 applications, cannot accept submission of application` enthält, werden zu viele Aufträge gleichzeitig an HDInsight übermittelt.

- **Empfehlung**: Begrenzen Sie die Anzahl der gleichzeitigen Aufträge, die an HDInsight übermittelt werden. Wenn die Aufträge über die gleiche Aktivität übermittelt werden, helfen Ihnen die entsprechenden Informationen in der Data Factory-Aktivität für Parallelität weiter. Ändern Sie die Trigger so, dass die gleichzeitigen Pipelineausführungen über die Zeit verteilt werden.

   Weitere Informationen zum wie im Fehler angegebenen Anpassen von `templeton.parallellism.job.submit` finden Sie in der [HDInsight-Dokumentation](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md).

### <a name="error-code-2301"></a>Fehlercode: 2301

- **Meldung**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Ursache:** Beim HDInsight-Cluster oder -Dienst treten Probleme auf.

- **Empfehlung**: Dieser Fehler tritt auf, wenn ADF beim Versuch, den Status des ausgeführten Auftrags anzufordern, keine Antwort vom HDInsight-Cluster erhält. Dieses Problem könnte im Cluster selbst vorliegen, oder der HDInsight-Dienst ist möglicherweise ausgefallen.

   Informationen finden Sie in der Dokumentation zur HDInsight-Problembehandlung unter https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide, oder wenden Sie sich an den Support, um weitere Unterstützung zu erhalten.

### <a name="error-code-2302"></a>Fehlercode: 2302

- **Meldung**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Ursache:** Der Auftrag wurde an den HDI-Cluster übermittelt, und dort ist ein Fehler aufgetreten.

- **Empfehlung**: 

 1. Überprüfen Sie die Ambari-Benutzeroberfläche mit folgenden Schritten:
    1. Vergewissern Sie sich, dass alle Dienste weiterhin ausgeführt werden.
    1. Überprüfen Sie auf der Ambari-Benutzeroberfläche den Abschnitt „Warnung“ in Ihrem Dashboard.
       1. Weitere Informationen zu Warnungen und den entsprechenden Lösungen finden Sie unter [Verwalten und Überwachen eines Clusters](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Überprüfen Sie Ihren YARN-Arbeitsspeicher. Wenn eine hohe Auslastung des YARN-Arbeitsspeichers vorliegt, wird die Verarbeitung Ihrer Aufträge möglicherweise verzögert. Führen Sie für den Cluster das Hochskalieren durch, um sicherzustellen, dass er über genügend Arbeitsspeicher und Kerne verfügt, falls nicht ausreichend Ressourcen für Ihre Spark-Anwendung bzw. die Aufträge vorhanden sind. 
 1. Führen Sie einen Beispieltestauftrag aus.
    1. Wenn Sie denselben Auftrag im HDInsight-Backend ausführen, überprüfen Sie, ob er erfolgreich war. Beispiele für Beispielausführungen finden Sie unter [Ausführen von MapReduce-Beispielen in HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md). 
 1. Wenn der Auftrag in HDInsight weiterhin fehlgeschlagen ist, überprüfen Sie die Anwendungsprotokolle und Informationen, die dem Support bereitgestellt werden müssen:
    1. Überprüfen Sie, ob der Auftrag an YARN übermittelt wurde. Wurde er nicht an YARN übermittelt, verwenden Sie `--master yarn`.
    1. Wenn die Anwendung die Ausführung beendet hat, erfassen Sie die Start- und Endzeit der YARN-Anwendung. Hat sie die Ausführung nicht beendet, erfassen Sie „Startzeit/Startzeit“.
    1. Überprüfen und erfassen Sie das Anwendungsprotokoll mit `yarn logs -applicationId <Insert_Your_Application_ID>`.
    1. Überprüfen und erfassen Sie die Yarn Resource Manager-Protokolle im Verzeichnis `/var/log/hadoop-yarn/yarn`.
    1. Wenn diese Schritte zur Lösung des Problems nicht ausreichen, wenden Sie sich an das Azure HDInsight-Team, um Unterstützung zu erhalten. Geben Sie dabei die vorstehenden Protokolle und Zeitstempel an.

### <a name="error-code-2303"></a>Fehlercode: 2303

- **Meldung**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Ursache:** Der Auftrag wurde an den HDI-Cluster übermittelt, und dort ist ein Fehler aufgetreten.

- **Empfehlung**: 

 1. Überprüfen Sie die Ambari-Benutzeroberfläche mit folgenden Schritten:
    1. Vergewissern Sie sich, dass alle Dienste weiterhin ausgeführt werden.
    1. Überprüfen Sie auf der Ambari-Benutzeroberfläche den Abschnitt „Warnung“ in Ihrem Dashboard.
       1. Weitere Informationen zu Warnungen und den entsprechenden Lösungen finden Sie unter [Verwalten und Überwachen eines Clusters](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Überprüfen Sie Ihren YARN-Arbeitsspeicher. Wenn eine hohe Auslastung des YARN-Arbeitsspeichers vorliegt, wird die Verarbeitung Ihrer Aufträge möglicherweise verzögert. Führen Sie für den Cluster das Hochskalieren durch, um sicherzustellen, dass er über genügend Arbeitsspeicher und Kerne verfügt, falls nicht ausreichend Ressourcen für Ihre Spark-Anwendung bzw. die Aufträge vorhanden sind. 
 1. Führen Sie einen Beispieltestauftrag aus.
    1. Wenn Sie denselben Auftrag im HDInsight-Backend ausführen, überprüfen Sie, ob er erfolgreich war. Beispiele für Beispielausführungen finden Sie unter [Ausführen von MapReduce-Beispielen in HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md). 
 1. Wenn der Auftrag in HDInsight weiterhin fehlgeschlagen ist, überprüfen Sie die Anwendungsprotokolle und Informationen, die dem Support bereitgestellt werden müssen:
    1. Überprüfen Sie, ob der Auftrag an YARN übermittelt wurde. Wurde er nicht an YARN übermittelt, verwenden Sie `--master yarn`.
    1. Wenn die Anwendung die Ausführung beendet hat, erfassen Sie die Start- und Endzeit der YARN-Anwendung. Hat sie die Ausführung nicht beendet, erfassen Sie „Startzeit/Startzeit“.
    1. Überprüfen und erfassen Sie das Anwendungsprotokoll mit `yarn logs -applicationId <Insert_Your_Application_ID>`.
    1. Überprüfen und erfassen Sie die Yarn Resource Manager-Protokolle im Verzeichnis `/var/log/hadoop-yarn/yarn`.
    1. Wenn diese Schritte zur Lösung des Problems nicht ausreichen, wenden Sie sich an das Azure HDInsight-Team, um Unterstützung zu erhalten. Geben Sie dabei die vorstehenden Protokolle und Zeitstempel an.

### <a name="error-code-2304"></a>Fehlercode: 2304

- **Meldung**: `MSI authentication is not supported on storages for HDI activities.`

- **Ursache:** Die mit Storage verknüpften Dienste, die im verknüpften HDInsight (HDI)-Dienst oder in der HDI verwendet werden, wurden mit einer nicht unterstützten MSI-Authentifizierung konfiguriert.

- **Empfehlung**: Geben Sie vollständige Verbindungszeichenfolgen für Speicherkonten an, die im verknüpften HDI-Dienst oder in der HDI-Aktivität verwendet werden.

### <a name="error-code-2305"></a>Fehlercode: 2305

- **Meldung**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Ursache:** Die Verbindungsinformationen für den HDI-Cluster sind falsch, der angegebene Benutzer hat keine Berechtigungen zum Ausführen der erforderlichen Aktion, oder beim HDInsight-Dienst gibt es Probleme bei der Reaktion auf Anforderungen von ADF.

- **Empfehlung**: Überprüfen Sie, ob die Benutzerinformationen korrekt sind und ob die Ambari-Benutzeroberfläche für den HDI-Cluster in einem Browser auf der VM, auf der die IR installiert ist (bei einer selbstgehosteten IR), oder auf einem beliebigen Computer (bei Azure IR) geöffnet werden kann.

### <a name="error-code-2306"></a>Fehlercode: 2306

- **Meldung**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Ursache:** Der für die Skriptaktion angegebene JSON-Code ist ungültig.

- **Empfehlung**: Sie sollten das Problem mithilfe der Fehlermeldung identifizieren können. Korrigieren Sie die JSON-Konfiguration, und versuchen Sie es erneut.

   Weitere Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter Azure HDInsight-Dienst](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

### <a name="error-code-2310"></a>Fehlercode: 2310

- **Meldung**: `Failed to submit Spark job. Error: '%message;'`

- **Ursache:** ADF hat versucht, mithilfe der Livy-API einen Batch auf einem Spark-Cluster zu erstellen (Livy/Batch), es ist jedoch ein Fehler aufgetreten.

- **Empfehlung**: Beheben Sie das Problem anhand der Fehlermeldung. Wenn die Informationen zur Lösung des Problems nicht ausreichen, wenden Sie sich an das HDI-Team, und geben Sie die Batch-ID und die Auftrags-ID an. Sie finden sie auf der Seite zur ADF-Überwachung in der Ausgabe der Aktivitätsausführung. Sollte eine weitere Problembehandlung erforderlich sein, erfassen Sie das vollständige Protokoll des Batchauftrags.

   Weitere Informationen zum Erfassen des vollständigen Protokolls finden Sie unter [Abrufen des vollständigen Protokolls eines Batch-Auftrags](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Fehlercode: 2312

- **Meldung**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Ursache:** Der Auftrag konnte auf dem HDInsight Spark-Cluster nicht ausgeführt werden.

- **Empfehlung**: Verwenden Sie die Links in der Ausgabe der Aktivitätsausführung auf der Seite zur ADF-Überwachung, um Probleme bei der Ausführung auf dem HDInsight Spark-Cluster zu beheben. Wenn Sie weitere Unterstützung benötigen, wenden Sie sich an das HDInsight-Supportteam.

   Weitere Informationen zum Erfassen des vollständigen Protokolls finden Sie unter [Abrufen des vollständigen Protokolls eines Batch-Auftrags](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Fehlercode: 2313

- **Meldung**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Ursache:** Der Batch wurde auf dem HDInsight Spark-Cluster gelöscht.

- **Empfehlung**: Beheben Sie Probleme mit Batches auf dem HDInsight Spark-Cluster. Wenden Sie sich an den HDInsight-Support, um weitere Unterstützung zu erhalten. 

   Weitere Informationen zum Erfassen des vollständigen Protokolls finden Sie unter [Abrufen des vollständigen Protokolls eines Batch-Auftrags](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job). Geben Sie das vollständige Protokoll für den HDInsight-Support frei, um weitere Unterstützung zu erhalten.

### <a name="error-code-2328"></a>Fehlercode: 2328

- **Meldung**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Ursache:** Die Fehlermeldung sollte Einzelheiten zum Fehler enthalten.

- **Empfehlung**: Sie sollten das Problem mithilfe der Fehlermeldung beheben können.

### <a name="error-code-2329"></a>Fehlercode: 2329

- **Meldung**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Ursache:** Die Fehlermeldung sollte Einzelheiten zum Fehler enthalten.

- **Empfehlung**: Sie sollten das Problem mithilfe der Fehlermeldung beheben können.

### <a name="error-code-2331"></a>Fehlercode: 2331

- **Meldung**: `The file path should not be null or empty.`

- **Ursache:** Der angegebene Dateipfad ist leer.

- **Empfehlung**: Geben Sie einen Pfad für eine vorhandene Datei an.

### <a name="error-code-2340"></a>Fehlercode: 2340

- **Meldung**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Ursache:** Der verknüpfte HDInsightOnDemand-Dienst unterstützt nicht die Ausführung über eine selbstgehostete IR.

- **Empfehlung**: Wählen Sie eine Azure IR-Instanz aus, und versuchen Sie es erneut.

### <a name="error-code-2341"></a>Fehlercode: 2341

- **Meldung**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Ursache:** Die angegebene URL hat nicht das richtige Format.

- **Empfehlung**: Korrigieren Sie die Cluster-URL, und versuchen Sie es erneut.

### <a name="error-code-2342"></a>Fehlercode: 2342

- **Meldung**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Ursache:** Entweder sind die angegebenen Anmeldeinformationen für den Cluster falsch, oder es gibt ein Problem mit der Netzwerkkonfiguration oder -verbindung, oder aber die IR kann keine Verbindung mit dem Cluster herstellen.

- **Empfehlung**: 
    1. Überprüfen Sie, ob die Anmeldeinformationen richtig sind, indem Sie die Ambari-Benutzeroberfläche des HDInsight-Clusters in einem Browser öffnen.
    1. Wenn sich der Cluster im Virtual Network (VNET) befindet und eine selbstgehostete IR verwendet wird, muss die HDI-URL die private URL in VNETS sein, und „-int“ sollte nach dem Clusternamen aufgeführt werden.
    
       Ändern Sie beispielsweise `https://mycluster.azurehdinsight.net/` in `https://mycluster-int.azurehdinsight.net/`. Beachten Sie das `-int` nach `mycluster`, aber vor `.azurehdinsight.net`.
    1. Wenn sich der Cluster im VNET befindet, die selbstgehostete IR verwendet wird und die private URL verwendet wurde, die Verbindung aber trotzdem nicht erfolgreich hergestellt wurde, konnte die VM mit der installierten IR keine Verbindung mit dem HDI-Cluster herstellen. 
    
       Stellen Sie eine Verbindung mit der VM her, auf der die IR installiert wurde, und öffnen Sie die Ambari-Benutzeroberfläche in einem Browser. Verwenden Sie die private URL für den Cluster. Diese Verbindung sollte im Browser funktionieren. Ist dies nicht der Fall, wenden Sie sich an das HDInsight-Supportteam, um weitere Unterstützung zu erhalten.
    1. Wenn keine selbstgehostete IR verwendet wird, sollte der HDI-Cluster öffentlich zugänglich sein. Öffnen Sie die Ambari-Benutzeroberfläche in einem Browser, und überprüfen Sie, ob sie tatsächlich angezeigt wird. Sollten Probleme mit dem Cluster oder mit den Diensten auf dem Cluster auftreten, wenden sie sich an das HDInsight-Supportteam.

       Die im verknüpften ADF-Dienst verwendete HDI-Cluster-URL muss für die ADF IR (selbstgehostet oder Azure IR) zugänglich sein, damit die Testverbindung und Ausführungen erfolgreich sind. Dieser Status kann überprüft werden, indem Sie die URL in einem Browser entweder auf einer VM oder einem beliebigen öffentlichen Computer öffnen.

### <a name="error-code-2343"></a>Fehlercode: 2343

- **Meldung**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Ursache:** Entweder der Benutzername oder das Kennwort ist leer.

- **Empfehlung**: Geben Sie die korrekten Anmeldeinformationen für die Verbindung mit HDI an, und versuchen Sie es erneut.

### <a name="error-code-2345"></a>Fehlercode: 2345

- **Meldung**: `Failed to read the content of the hive script. Error: '%message;'`

- **Ursache:** Die Skriptdatei ist nicht vorhanden, oder ADF konnte keine Verbindung mit dem Speicherort des Skripts herstellen.

- **Empfehlung**: Überprüfen Sie, ob das Skript vorhanden ist und ob der zugehörige verknüpfte Dienst die richtigen Anmeldeinformationen für eine Verbindung hat.

### <a name="error-code-2346"></a>Fehlercode: 2346

- **Meldung**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Ursache:** ADF hat versucht, eine Open Database Connectivity (ODBC)-Verbindung mit dem HDI-Cluster herzustellen, und dabei ist ein Fehler aufgetreten.

- **Empfehlung**: 

   1. Vergewissern Sie sich, dass Sie Ihre ODBC-/Java Database Connectivity (JDBC)-Verbindung ordnungsgemäß eingerichtet haben.
      1. Wenn Sie dasselbe virtuelle Netzwerk verwenden, können Sie diese Verbindung für JDBC abrufen von:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Informationen zur Sicherstellung, dass Sie JDBC korrekt eingerichtet haben, finden Sie unter [Abfragen von Apache Hive über den JDBC-Treiber in HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Lesen Sie für Open Database (ODB) [Tutorial: Abfragen von Apache Hive mit ODBC und PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md), um sicherzustellen, dass Sie es korrekt eingerichtet haben. 
   1. Überprüfen Sie, ob „Hiveserver2“, „Hive-Metastore“ und „Hiveserver2 Interactive“ aktiv sind und funktionieren. 
   1. Überprüfen Sie die Ambari-Benutzeroberfläche (User Interface, UI):
      1. Vergewissern Sie sich, dass alle Dienste weiterhin ausgeführt werden.
      1. Überprüfen Sie auf der Ambari-Benutzeroberfläche den Abschnitt „Warnung“ in Ihrem Dashboard.
         1. Weitere Informationen zu Warnungen und den entsprechenden Lösungen finden Sie unter [Verwalten und Überwachen eines Clusters](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Wenn diese Schritte zur Lösung des Problems nicht ausreichen, wenden Sie sich an das Azure HDInsight-Team.

### <a name="error-code-2347"></a>Fehlercode: 2347

- **Meldung**: `Hive execution through ODBC failed with error message '%message;'.`

- **Ursache:** ADF hat das Hive-Skript über eine ODBC-Verbindung zur Ausführung an den HDI-Cluster gesendet, und beim Skript ist auf dem Cluster ein Fehler aufgetreten.

- **Empfehlung**: 

   1. Vergewissern Sie sich, dass Sie Ihre ODBC-/Java Database Connectivity (JDBC)-Verbindung ordnungsgemäß eingerichtet haben.
      1. Wenn Sie dasselbe virtuelle Netzwerk verwenden, können Sie diese Verbindung für JDBC abrufen von:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Informationen zur Sicherstellung, dass Sie JDBC korrekt eingerichtet haben, finden Sie unter [Abfragen von Apache Hive über den JDBC-Treiber in HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Lesen Sie für Open Database (ODB) [Tutorial: Abfragen von Apache Hive mit ODBC und PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md), um sicherzustellen, dass Sie es korrekt eingerichtet haben. 
   1. Überprüfen Sie, ob „Hiveserver2“, „Hive-Metastore“ und „Hiveserver2 Interactive“ aktiv sind und funktionieren. 
   1. Überprüfen Sie die Ambari-Benutzeroberfläche (User Interface, UI):
      1. Vergewissern Sie sich, dass alle Dienste weiterhin ausgeführt werden.
      1. Überprüfen Sie auf der Ambari-Benutzeroberfläche den Abschnitt „Warnung“ in Ihrem Dashboard.
         1. Weitere Informationen zu Warnungen und den entsprechenden Lösungen finden Sie unter [Verwalten und Überwachen eines Clusters](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Wenn diese Schritte zur Lösung des Problems nicht ausreichen, wenden Sie sich an das Azure HDInsight-Team.

### <a name="error-code-2348"></a>Fehlercode: 2348

- **Meldung**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Ursache:** Die Eigenschaften des verknüpften Speicherdiensts sind nicht richtig festgelegt.

- **Empfehlung**: Im verknüpften Hauptspeicherdienst für HDI-Aktivitäten werden nur vollständige Verbindungszeichenfolgen unterstützt. Vergewissern Sie sich, dass Sie keine MSI-Autorisierungen oder -Anwendungen verwenden.

### <a name="error-code-2350"></a>Fehlercode: 2350

- **Meldung**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Ursache:** Die Anmeldeinformationen, die zum Herstellen einer Verbindung mit dem Speicher angegeben wurden, in dem sich die Dateien befinden sollten, sind falsch, oder die Dateien sind dort nicht vorhanden.

- **Empfehlung**: Dieser Fehler tritt auf, wenn ADF die HDI-Aktivitäten vorbereitet und versucht, Dateien vor Übermittlung des Auftrags an HDI in den Hauptspeicher zu kopieren. Überprüfen Sie, ob die Dateien am angegebenen Speicherort vorhanden sind und ob die Speicherverbindung korrekt ist. ADF-HDI-Aktivitäten unterstützen nicht die MSI-Authentifizierung für Speicherkonten, die mit HDI-Aktivitäten im Zusammenhang stehen. Überprüfen Sie deshalb, ob diese verknüpften Dienste über vollständige Schlüssel verfügen oder Azure Key Vault verwenden.

### <a name="error-code-2351"></a>Fehlercode: 2351

- **Meldung**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Ursache:** Die Datei ist im angegebenen Pfad nicht vorhanden.

- **Empfehlung**: Überprüfen Sie, ob die Datei tatsächlich vorhanden ist und ob der verknüpfte Dienst mit Verbindungsinformationen, die auf diese Datei verweisen, die richtigen Anmeldeinformationen enthält.

### <a name="error-code-2352"></a>Fehlercode: 2352

- **Meldung**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Ursache:** Die Eigenschaften des verknüpften Dateispeicherdiensts sind nicht richtig festgelegt.

- **Empfehlung**: Überprüfen Sie, ob die Eigenschaften des verknüpften Dateispeicherdiensts ordnungsgemäß konfiguriert wurden.

### <a name="error-code-2353"></a>Fehlercode: 2353

- **Meldung**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Ursache:** Die Eigenschaften des verknüpften Skriptspeicherdiensts sind nicht richtig festgelegt.

- **Empfehlung**: Überprüfen Sie, ob die Eigenschaften des verknüpften Skriptspeicherdiensts ordnungsgemäß konfiguriert wurden.

### <a name="error-code-2354"></a>Fehlercode: 2354

- **Meldung**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Ursache:** Der Typ des verknüpften Speicherdiensts wird von der Aktivität nicht unterstützt.

- **Empfehlung**: Überprüfen Sie, ob beim ausgewählten verknüpften Dienst einer der unterstützten Typen für die Aktivität verwendet wird. HDI-Aktivitäten unterstützen die verknüpften Dienste „AzureBlobStorage“ und „AzureBlobFSStorage“.

   Weitere Informationen finden Sie unter [Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern](../hdinsight/hdinsight-hadoop-compare-storage-options.md).

### <a name="error-code-2355"></a>Fehlercode: 2355

- **Meldung**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Ursache:** Der angegebene Wert für `commandEnvironment` ist falsch.

- **Empfehlung**: Überprüfen Sie, ob der angegebene Wert ähnlich wie der folgende ist:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Überprüfen Sie außerdem, ob jede Variable in der Liste nur einmal angezeigt wird.

### <a name="error-code-2356"></a>Fehlercode: 2356

- **Meldung**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Ursache:** Der angegebene Wert für `commandEnvironment` ist falsch.

- **Empfehlung**: Überprüfen Sie, ob der angegebene Wert ähnlich wie der folgende ist:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Überprüfen Sie außerdem, ob jede Variable in der Liste nur einmal angezeigt wird.

### <a name="error-code-2357"></a>Fehlercode: 2357

- **Meldung**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Ursache:** Die bereitgestellten Anmeldeinformationen sind falsch.

- **Empfehlung**: Überprüfen Sie, ob die Verbindungsinformationen in ADLS Gen 1 mit dem Dienst verknüpft sind und ob die Testverbindung erfolgreich ist.

### <a name="error-code-2358"></a>Fehlercode: 2358

- **Meldung**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Ursache:** Der angegebene Wert für die erforderliche Eigenschaft `TimeToLive` hat ein ungültiges Format. 

- **Empfehlung**: Aktualisieren Sie den Wert auf den vorgeschlagenen Bereich, und versuchen Sie es erneut.

### <a name="error-code-2359"></a>Fehlercode: 2359

- **Meldung**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Ursache:** Der angegebene Wert für die Eigenschaft `roles` ist ungültig.

- **Empfehlung**: Ersetzen Sie den Wert durch einen der vorgeschlagenen Werte, und versuchen Sie es erneut.

### <a name="error-code-2360"></a>Fehlercode: 2360

- **Meldung**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Ursache:** Die angegebene Verbindungszeichenfolge für den `HCatalogLinkedService` ist ungültig.

- **Empfehlung**: Ersetzen Sie den Wert durch eine korrekte Azure SQL-Verbindungszeichenfolge, und versuchen Sie es erneut.

### <a name="error-code-2361"></a>Fehlercode: 2361

- **Meldung**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Ursache:** Die Clustererstellung war nicht erfolgreich, und vom HDInsight-Dienst wurde kein Fehler an ADF ausgegeben.

- **Empfehlung**: Öffnen Sie das Azure-Portal, suchen Sie die HDI-Ressource mit dem angegebenen Namen, und überprüfen Sie dann den Bereitstellungsstatus. Wenn Sie weitere Unterstützung benötigen, wenden Sie sich an das HDInsight-Supportteam.

### <a name="error-code-2362"></a>Fehlercode: 2362

- **Meldung**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Ursache:** Der bereitgestellte zusätzliche Speicher war kein Azure-Blobspeicher.

- **Empfehlung**: Geben Sie ein Azure-Blobspeicherkonto als zusätzlichen Speicher für den bedarfsgesteuerten verknüpften HDInsight-Dienst an.

## <a name="web-activity"></a>Webaktivität

### <a name="error-code-2128"></a>Fehlercode: 2128

- **Meldung**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Ursache:** Die Ursache für dieses Problem ist entweder die Netzwerkverbindung, ein DNS-Fehler, eine Überprüfung des Serverzertifikats oder ein Timeout.

- **Empfehlung**: Überprüfen Sie, ob der Endpunkt, den Sie erreichen möchten, auf Anforderungen reagiert. Dazu können Sie Tools wie **„Fiddler“ oder „Postman“** verwenden.

### <a name="error-code-2108"></a>Fehlercode: 2108

- **Meldung**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Ursache:** Die Anforderung war aufgrund eines Problems (z. B Netzwerkkonnektivität, ein DNS-Fehler, eine Überprüfung des Serverzertifikats oder ein Timeout) nicht erfolgreich.

- **Empfehlung**: Verwenden Sie Fiddler/Postman, um die Anforderung zu überprüfen.

#### <a name="more-details"></a>Weitere Informationen
Verwenden Sie **Fiddler** zum Erstellen einer HTTP-Sitzung der überwachten Webanwendung:

1. Laden Sie [Fiddler](https://www.telerik.com/download/fiddler) herunter, und installieren und öffnen Sie die Anwendung.

1. Wenn Ihre Webanwendung HTTPS verwendet, wechseln Sie zu **Tools** > **Fiddler-Optionen** > **HTTPS**.

   1. Wählen Sie auf der Registerkarte „HTTPS“ **Capture HTTPS CONNECTs** (HTTPS CONNECTs erfassen) und **Decrypt HTTPS Traffic** (HTTPS-Datenverkehr entschlüsseln) aus.

      ![Fiddler-Optionen](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Wenn in der Anwendung TLS/SSL-Zertifikate verwendet werden, fügen Sie das Fiddler-Zertifikat Ihrem Gerät hinzu.

   Wechseln Sie zu: **Tools** > **Fiddler-Optionen** > **HTTPS** > **Aktionen** > **Stammzertifikat auf Desktop exportieren**.

1. Deaktivieren Sie die Erfassung, indem Sie zu **Datei** > **Datenverkehr erfassen** wechseln. Oder drücken Sie **F12**.

1. Löschen Sie den Browsercache, sodass alle zwischengespeicherten Elemente gelöscht werden und erneut heruntergeladen werden müssen.

1. Erstellen Sie eine Anforderung:

1. Wählen Sie die Registerkarte **Composer** aus.

   1. Legen Sie die HTTP-Methode und die HTTP-URL fest.
 
   1. Fügen Sie bei Bedarf Header und einen Anforderungstext hinzu.

   1. Wählen Sie **Execute**(Ausführen).

1. Starten Sie die Erfassung des Datenverkehrs wieder, und schließen Sie die entsprechende Transaktion auf Ihrer Seite ab.

1. Wechseln Sie zu: **Datei** > **Speichern** > **Alle Sitzungen**.

Weitere Informationen finden Sie unter [Erste Schritte mit Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="general"></a>Allgemein

### <a name="activity-stuck-issue"></a>Problem mit hängen gebliebener Aktivität

Wenn Sie beobachten, dass die Aktivität viel länger läuft als gewöhnlich und kaum vorankommt, ist sie möglicherweise hängen geblieben. Sie können versuchen, die Aktivität abzubrechen und zu wiederholen, um zu sehen, ob das hilft. Wenn es sich um eine Kopieraktivität handelt, können Sie sich unter [Problembehandlung für die Leistung der Kopieraktivität](copy-activity-performance-troubleshooting.md) über die Leistungsüberwachung und Problembehandlung informieren. Wenn es sich um einen Datenfluss handelt, informieren Sie sich unter [Leistung von Zuordnungsdatenflüssen](concepts-data-flow-performance.md) und im Leitfaden für die Optimierung.

### <a name="payload-is-too-large"></a>Nutzdaten sind zu umfangreich

**Fehlermeldung:** `The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**Ursache:** Die Nutzdaten für jede Aktivitätsausführung enthalten die Aktivitätskonfiguration, die Konfigurationen der zugeordneten Datasets und verknüpften Dienste (sofern vorhanden) sowie einen kleinen Teil der Systemeigenschaften, die pro Aktivitätstyp generiert werden. Der Grenzwert für diese Nutzdatengröße ist 896 KB, wie im Abschnitt [Data Factory-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) beschrieben.

**Empfehlung:** Sie erreichen dieses Limit wahrscheinlich, weil Sie einen oder mehrere große Parameterwerte übergeben, die entweder aus der vorgelagerten Aktivitätsausgabe stammen oder externen Ursprungs sind, insbesondere, wenn Sie tatsächliche Daten über Aktivitäten in der Ablaufsteuerung hinweg übergeben. Überprüfen Sie, ob Sie die Größe großer Parameterwerte reduzieren können, oder optimieren Sie die Pipelinelogik, um zu vermeiden, dass solche Werte über Aktivitäten hinweg übergeben werden, und verarbeiten Sie sie stattdessen in der Aktivität.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

* [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-Videos](https://azure.microsoft.com/resources/videos/index/)
* [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)
