---
title: 'ML Studio (Classic): REST-API-Fehlercodes – Azure'
description: Diese Fehlercodes können für den Vorgang eines Azure Machine Learning-Webdiensts zurückgegeben werden.
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c5a457a673500c542dd6130d5cfadd73ab677f7b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518350"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klassisch): Fehlercodes der REST-API

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Die folgenden Fehlercodes können für den Vorgang eines Azure Machine Learning Studio-Webdiensts (klassisch) zurückgegeben werden.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-Statuscode 400)
 
Invalid argument provided. (Ungültiges Argument angegeben.)
 
Diese Klasse von Fehlern bedeutet, dass ein angegebenes Argument ungültig war. Dies können Anmeldeinformationen oder der Speicherort von Azure-Speicher für Daten sein, die an den Webdienst übergeben werden. Das Feld mit dem „Code“ des Fehlers im Abschnitt „Details“ enthält Informationen dazu, welches Argument ungültig war.
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| BadParameterValue | The parameter value supplied does not satisfy the parameter rule on the parameter (Der angegebene Parameterwert erfüllt die Vorgaben der Parameterregel im Parameter nicht.) |
| BadSubscriptionId | The subscription Id that is used to score is not the one present in the resource (Die Abonnement-ID, die zum Bewerten verwendet wird, ist nicht die ID, die in der Ressource vorhanden ist.) |
| BadVersionCall | Invalid version parameter was passed during the API call: (Während des API-Aufrufs wurde ein ungültiger Versionsparameter übergeben:) {0}. Check the API help page for passing the correct version and try again. (Ermitteln Sie auf der API-Hilfeseite die richtige Version, und wiederholen Sie den Vorgang.) |
| BatchJobInputsNotSpecified | The following required input(s) were not specified with the request: (Die folgenden erforderlichen Eingaben wurden nicht mit der Anforderung angegeben:) {0}. Please ensure all input data is specified and try again. (Stellen Sie sicher, dass alle Eingabedaten angegeben sind, und wiederholen Sie den Vorgang.) |
| BatchJobInputsTooManySpecified | The request specified more inputs than defined in the service. (In der Anforderung wurden mehr Eingaben angegeben, als im Dienst definiert sind.) List of accepted input(s): (Liste mit akzeptierten Eingaben:) {0}. Please ensure all input data is specified correctly and try again. (Stellen Sie sicher, dass alle Eingabedaten richtig angegeben wurden, und wiederholen Sie den Vorgang.) |
| BlobNameTooLong | Azure blob storage path provided for diagnostic output is too long: (Der Azure Blob Storage-Pfad für die Diagnoseausgabe ist zu lang:) {0}. Shorten the path and try again. (Kürzen Sie den Pfad, und wiederholen Sie den Vorgang.) |
| BlobNotFound | Unable to access the provided Azure blob - (Auf das angegebene Azure-Blob kann nicht zugegriffen werden –) {0}.  Azure error message: (Azure-Fehlermeldung:) {1}. |
| ContainerIsEmpty | No Azure storage container name was provided. (Es wurde kein Azure-Speichercontainername angegeben.) Provide a valid container name and try again. (Geben Sie einen gültigen Containernamen an, und wiederholen Sie den Vorgang.) |
| ContainerSegmentInvalid | Invalid container name. (Der Containername ist ungültig.) Provide a valid container name and try again. (Geben Sie einen gültigen Containernamen an, und wiederholen Sie den Vorgang.) |
| ContainerValidationFailed | Blob container validation failed with this error: (Bei der Überprüfung des Blobcontainers ist dieser Fehler aufgetreten:) {0}. |
| DataTypeNotSupported | Unsupported data type provided. (Es wurde ein nicht unterstützter Datentyp angegeben.) Provide valid data type(s) and try again. (Stellen Sie gültige Datentypen bereit, und wiederholen Sie den Vorgang.) |
| DuplicateInputInBatchCall | The batch request is invalid. (Die Batchanforderung ist ungültig.) Cannot specify both single and multiple input at the same time. (Einfach- und Mehrfacheingaben können nicht gleichzeitig angegeben werden.) Remove one of these items from the request and try again. (Entfernen Sie eines dieser Elemente aus der Anforderung, und wiederholen Sie den Vorgang.) |
| ExpiryTimeInThePast | Expiry time provided is in the past: (Die angegebene Ablaufzeit liegt in der Vergangenheit:) {0}. Provide a future expiry time in UTC and try again. (Geben Sie eine Ablaufzeit (in UTC) an, die in der Zukunft liegt, und wiederholen Sie den Vorgang.) To never expire, set expiry time to NULL. (Legen Sie die Ablaufzeit auf NULL fest, wenn der Zeitraum niemals ablaufen soll.) |
| IncompleteSettings | Diagnostics settings are incomplete. (Die Diagnoseeinstellungen sind unvollständig.) |
| InputBlobRelativeLocationInvalid | No Azure storage blob name provided. (Es wurde kein Azure-Speicherblobname angegeben.) Provide a valid blob name and try again. (Geben Sie einen gültigen Blobnamen an, und wiederholen Sie den Vorgang.) |
| InvalidBlob | Invalid blob specification for blob: (Ungültige Blobspezifikation für Blob:) {0}. Verify that connection string / relative path or SAS token specification is correct and try again. (Vergewissern Sie sich, dass die Verbindungszeichenfolge bzw. der relative Pfad oder die SAS-Tokenspezifikation richtig ist, und wiederholen Sie den Vorgang.) |
| InvalidBlobConnectionString | The connection string specified for one of the input/output blobs in invalid: (Die Verbindungszeichenfolge, die für ein Eingabe-/Ausgabeblob angegeben wurde, ist ungültig:) {0}. Please correct this and try again. (Beheben Sie dies, und wiederholen Sie den Vorgang.) |
| InvalidBlobExtension | The blob reference: {0} has an invalid or missing file extension. (Der Blobverweis {0} verfügt über eine ungültige oder fehlende Dateierweiterung.) Supported file extensions for this output type are: (Unterstützte Dateierweiterungen für diesen Ausgabetyp sind:) „{1}“. |
| InvalidInputNames | Invalid service input name(s) specified in the request: (In der Anforderung wurden ungültige Diensteingabenamen angegeben:) {0}. Please map the input data to the correct service inputs and try again. (Ordnen Sie die Eingabedaten den richtigen Diensteingaben zu, und wiederholen Sie den Vorgang.) |
| InvalidOutputOverrideName | Invalid output override name: (Ungültiger Ausgabeüberschreibungsname:) {0}. The service does not have an output node with this name. (Der Dienst weist keinen Ausgabeknoten mit diesem Namen auf.) Please pass in a correct output node name to override (case sensitivity applies). (Übergeben Sie einen korrekten Ausgabeknotennamen für die Überschreibung (Groß-/Kleinschreibung wird berücksichtigt).) |
| InvalidQueryParameter | Invalid query parameter (Ungültiger Abfrageparameter) '{0}'. {1} |
| MissingInputBlobInformation | Missing Azure storage blob information. (Fehlende Informationen für das Azure-Speicherblob.) Provide a valid connection string and relative path or URI and try again. (Geben Sie eine gültige Verbindungszeichenfolge und einen relativen Pfad oder einen URI ein, und wiederholen Sie den Vorgang.) |
| MissingJobId | No job Id provided. (Es wurde keine Auftrags-ID angegeben.) A job Id is returned when a job was submitted for the first time. (Eine Auftrags-ID wird zurückgegeben, wenn ein Auftrag zum ersten Mal übermittelt wurde.) Verify the job Id is correct and try again. (Stellen Sie sicher, dass die Auftrags-ID korrekt ist, und wiederholen Sie den Vorgang.) |
| MissingKeys | No Keys provided or one of Primary or Secondary Key is not provided. (Es wurden keine Schlüssel oder kein Primär- oder Sekundärschlüssel angegeben.) |
| MissingModelPackage | No model package Id or model package provided. (Es wurde keine Modellpaket-ID oder kein Modellpaket angegeben.) Provide a valid model package Id or model package and try again. (Geben Sie eine gültige Modellpaket-ID oder ein Modellpaket an, und wiederholen Sie den Vorgang.) |
| MissingOutputOverrideSpecification | The request is missing the blob specification for output override (Für die Anforderung fehlt die Blobspezifikation für die Ausgabeüberschreibung) {0}. Please specify a valid blob location with the request, or remove the output specification if no location override is desired. (Geben Sie einen gültigen Blobspeicherort für die Anforderung an, oder entfernen Sie die Ausgabespezifikation, wenn keine Speicherortüberschreibung gewünscht wird.) |
| MissingRequestInput | The web service expects an input, but no input was provided. (Der Webdienst erwartet eine Eingabe, aber es ist keine Eingabe erfolgt.) Ensure valid inputs are provided based on the published input ports in the model and try again. (Stellen Sie sicher, dass basierend auf den veröffentlichten Eingabeports im Modell gültige Eingaben bereitgestellt werden, und wiederholen Sie den Vorgang.) |
| MissingRequiredGlobalParameters | Not all required web service parameter(s) provided. (Es wurden nicht alle erforderlichen Webdienstparameter angegeben.) Verify the parameter(s) expected for the module(s) are correct and try again. (Vergewissern Sie sich, dass die für die Module erwarteten Parameter korrekt sind, und wiederholen Sie den Vorgang.) |
| MissingRequiredOutputOverrides | When calling an encrypted service endpoint it is mandatory to pass in output overrides for all the service's outputs. (Beim Aufrufen eines verschlüsselten Dienstendpunkts ist es obligatorisch, Ausgabeüberschreibungen für alle Ausgaben des Diensts zu übergeben.) Missing overrides at this time for these outputs: (Derzeit fehlende Überschreibungen für diese Ausgaben:) {0} |
| MissingWebServiceGroupId | No web service group Id provided. (Keine Webdienstgruppen-ID angegeben.) Provide a valid web service group Id and try again. (Geben Sie eine gültige Webdienstgruppen-ID ein, und wiederholen Sie den Vorgang.) |
| MissingWebServiceId | No web service Id provided. (Keine Webdienst-ID angegeben.) Provide a valid web service Id and try again. (Geben Sie eine gültige Webdienst-ID an, und wiederholen Sie den Vorgang.) |
| MissingWebServicePackage | No web Service package provided. (Kein Webdienstpaket angegeben.) Provide a valid web service package and try again. (Geben Sie ein gültiges Webdienstpaket an, und wiederholen Sie den Vorgang.) |
| MissingWorkspaceId | No workspace Id provided. (Keine Arbeitsbereich-ID angegeben.) Provide a valid workspace Id and try again. (Geben Sie eine gültige Arbeitsbereich-ID an, und wiederholen Sie den Vorgang.) |
| ModelConfigurationInvalid | Invalid model configuration in the model package. (Ungültige Modellkonfiguration im Modellpaket.) Ensure the model configuration contains output endpoint(s) definition, std error endpoint, and std out endpoint and try again. (Stellen Sie sicher, dass die Modellkonfiguration eine Ausgabeendpunkt-Definition, einen stderror-Endpunkt und einen stdout-Endpunkt enthält, und wiederholen Sie den Vorgang.) |
| ModelPackageIdInvalid | Invalid model package Id. (Ungültige Modellpaket-ID.) Verify that the model package Id is correct and try again. (Stellen Sie sicher, dass die Modellpaket-ID korrekt ist, und wiederholen Sie den Vorgang.) |
| RequestBodyInvalid | No request body provided or error in deserializing the request body. (Kein Anforderungstext angegeben, oder Fehler beim Deserialisieren des Anforderungstexts.) |
| RequestIsEmpty | No request provided. (Keine Anforderung angegeben.) Provide a valid request and try again. (Geben Sie eine gültige Anforderung an, und wiederholen Sie den Vorgang.) |
| UnexpectedParameter | Unexpected parameters provided. (Unerwartete Parameter angegeben.) Verify all parameter names are spelled correctly, only expected parameters are passed, and try again. (Vergewissern Sie sich, dass alle Parameternamen richtig geschrieben sind und nur erwartete Parameter übergeben werden, und wiederholen Sie den Vorgang.) |
| UnknownError | Unknown error. (Unbekannter Fehler.) |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Cannot change concurrent requests requirements for {0} web service. (Die Vorgaben für gleichzeitige Anforderungen für den Webdienst {0} können nicht geändert werden.) |
| WebServiceIdInvalid | Invalid web service id provided. (Ungültige Webdienst-ID angegeben.) Web service id should be a valid guid. (Die Webdienst-ID muss eine gültige GUID sein.) |
| WebServiceTooManyConcurrentRequestRequirement | Cannot set concurrent request requirement to more than {0}. (Für gleichzeitige Anforderungen kann die Vorgabe nicht auf einen höheren Wert als {0} festgelegt werden.) |
| WebServiceTypeInvalid | Invalid web service type provided. (Ungültigen Webdiensttyp bereitgestellt.) Verify the valid web service type is correct and try again. (Vergewissern Sie sich, dass der Webdiensttyp korrekt ist, und wiederholen Sie den Vorgang.) Valid web service types: (Gültige Webdiensttypen:) {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-Statuscode 400)
 
Invalid user argument provided. (Ungültiges Benutzerargument angegeben.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| InputMismatchError | Input data does not match input port schema. (Die Eingabedaten stimmen nicht mit dem Eingabeportschema überein.) |
| InputParseError | Parsing of input vector failed. (Fehler beim Analysieren des Eingabevektors.)  Verify the input vector has the correct number of columns and data types. (Stellen Sie sicher, dass der Eingabevektor über die richtige Anzahl von Spalten und Datentypen verfügt.)  Weitere Details: {0}. |
| MissingRequiredGlobalParameters | Parameter(s) expected by the web service are missing. (Die vom Webdienst erwarteten Parameter fehlen.) Verify all the required parameters expected by the web service are correct and try again. (Stellen Sie sicher, dass alle erforderlichen Parameter, die vom Webdienst erwartet werden, korrekt sind, und wiederholen Sie den Vorgang.) |
| UnexpectedParameter | Verify only the required parameters expected by the web service are passed and try again. (Stellen Sie sicher, dass nur die erforderlichen Parameter, die vom Webdienst erwartet werden, übergeben werden, und wiederholen Sie den Vorgang.) |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-Statuscode 400)
 
The request is invalid in the current context. (Die Anforderung ist im aktuellen Kontext ungültig.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| CannotStartJob | The job cannot be started because it is in {0} state. (Der Auftrag kann nicht gestartet werden, da er sich im Status {0} befindet.) |
| IncompatibleModel | The model is incompatible with the request version. (Das Modell ist mit der Anforderungsversion inkompatibel.) The request version only supports single datatable output models. (Die Anforderungsversion unterstützt nur einzelne DataTable-Ausgabemodelle.) |
| MultipleInputsNotAllowed | The model does not allow multiple inputs. (Für das Modell sind mehrere Eingaben unzulässig.) |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-Statuscode 400)
 
Module execution encountered an internal library error. (Bei der Modulausführung ist ein interner Bibliotheksfehler aufgetreten.)
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-Statuscode 400)
 
Module execution encountered an error. (Bei der Modulausführung ist ein Fehler aufgetreten.)
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-Statuscode 400)
 
Invalid web service package. (Ungültiges Webdienstpaket.) Verify the web service package provided is correct and try again. (Stellen Sie sicher, dass das angegebene Webdienstpaket korrekt ist, und wiederholen Sie den Vorgang.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| FormatError | The web service package is malformed. (Das Webdienstpaket ist nicht wohlgeformt.) Details: {0} |
| RuntimesError | The web service package graph is invalid. (Der Webdienstpaket-Graph ist ungültig.) Details: {0} |
| ValidationError | The web service package graph is invalid. (Der Webdienstpaket-Graph ist ungültig.) Details: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Unauthorized (HTTP-Statuscode 401)
 
Request is unauthorized to access resource. (Die Anforderung ist nicht berechtigt, auf die Ressource zuzugreifen.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| AdminRequestUnauthorized | Nicht autorisiert |
| ManagementRequestUnauthorized | Nicht autorisiert |
| ScoreRequestUnauthorized | Invalid credentials provided. (Es wurden ungültige Anmeldeinformationen angegeben.) |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-Statuscode 404)
 
Die Ressource wurde nicht gefunden.
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| ModelPackageNotFound | Model package not found. (Modellpaket nicht gefunden.) Verify the model package Id is correct and try again. (Stellen Sie sicher, dass die Modellpaket-ID korrekt ist, und wiederholen Sie den Vorgang.) |
| WebServiceIdNotFoundInWorkspace | Web service under this workspace not found. (Der Webdienst wurde unter diesem Arbeitsbereich nicht gefunden.) There is a mismatch between the webServiceId and the workspaceId. (Es besteht ein Konflikt zwischen webServiceId und workspaceId.) Verify the web service provided is part of the workspace and try again. (Stellen Sie sicher, dass der angegebene Webdienst Teil des Arbeitsbereichs ist, und wiederholen Sie den Vorgang.) |
| WebServiceNotFound | Web service not found. (Webdienst nicht gefunden.) Verify the web service Id is correct and try again. (Stellen Sie sicher, dass die Webdienst-ID korrekt ist, und wiederholen Sie den Vorgang.) |
| WorkspaceNotFound | Workspace not found. (Arbeitsbereich nicht gefunden.) Verify the workspace Id is correct and try again. (Stellen Sie sicher, dass die Arbeitsbereich-ID korrekt ist, und wiederholen Sie den Vorgang.) |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-Statuscode 408)
 
Der Vorgang konnte nicht innerhalb der zulässigen Zeit abgeschlossen werden.
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| RequestCanceled | Request was canceled by the client. (Die Anforderung wurde vom Client abgebrochen.) |
| ScoreRequestTimeout | Execution request timed out. (Zeitüberschreitung für die Ausführung der Anforderung.) |
 
## <a name="conflict-http-status-code-409"></a>Conflict (HTTP-Statuscode 409)
 
Die Ressource ist bereits vorhanden.
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Invalid output parameter name. (Ungültiger Ausgabeparametername.) Try using the metadata editor module to rename columns and try again. (Versuchen Sie, das Metadaten-Editor-Modul zu verwenden, um Spalten umzubenennen, und wiederholen Sie den Vorgang.) |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-Statuscode 413)
 
The model had exceeded the memory quota assigned to it. (Für das Modell wurde das zugewiesene Arbeitsspeicherkontingent überschritten.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| OutOfMemoryLimit | The model consumed more memory than was appropriated for it. (Das Modell hat mehr Arbeitsspeicher als vorgesehen verbraucht.) Maximum allowed memory for the model is {0} MB. (Der maximal zulässige Arbeitsspeicher für das Modell beträgt {0} MB.) Please check your model for issues. (Überprüfen Sie das Modell auf Probleme.) |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-Statuscode 500)
 
Execution encountered an internal error. (Bei der Ausführung ist ein interner Fehler aufgetreten.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | The container process crashed with system error (Der Containerprozess ist mit einem Systemfehler abgestürzt.) |
| ContainerProcessTerminatedWithUnknownError | The container process crashed with unknown error (Der Containerprozess ist mit einem unbekannten Fehler abgestürzt.) |
| ContainerValidationFailed | Blob container validation failed with this error: (Bei der Überprüfung des Blobcontainers ist dieser Fehler aufgetreten:) {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | No arguments provided. (Keine Argumente angegeben.) Verify that valid arguments are passed and try again. (Vergewissern Sie sich, dass gültige Argumente übergeben werden, und wiederholen Sie den Vorgang.) |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port id={0} has an unsupported data type: {1}. (Port id={0} weist einen nicht unterstützten Datentyp auf: {0}.) |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger generation failed, Details: (Fehler bei Swagger-Generierung, Details:) {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Unknown job status code (Unbekannter Auftragsstatuscode) {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, Details: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-Statuscode 500)
 
Execution encountered an internal error. (Bei der Ausführung ist ein interner Fehler aufgetreten.) System low on memory. (Das System verfügt nicht über genügend Arbeitsspeicher.) Wiederholen Sie den Vorgang.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-Statuscode 500)
 
Invalid model package. (Ungültiges Modellpaket.) Verify the model package provided is correct and try again. (Stellen Sie sicher, dass das angegebene Modellpaket korrekt ist, und wiederholen Sie den Vorgang.)
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-Statuscode 500)
 
Invalid web service package. (Ungültiges Webdienstpaket.) Verify the web package provided is correct and try again. (Stellen Sie sicher, dass das angegebene Webpaket korrekt ist, und wiederholen Sie den Vorgang.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| ModuleError | The web service package graph is invalid. (Der Webdienstpaket-Graph ist ungültig.) Details: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-Statuscode 503)
 
The request cannot execute as the containers are being initialized. (Die Anforderung kann nicht ausgeführt werden, da die Container initialisiert werden.)
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-Statuscode 503)
 
Service is temporarily unavailable. (Der Dienst ist vorübergehend nicht verfügbar.)
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| NoMoreResources | No resources available for request. (Keine Ressourcen für Anforderung verfügbar.) |
| RequestThrottled | Request was throttled for {0} endpoint. (Anforderung wurde für Endpunkt {0} gedrosselt.) The maximum concurrency for the endpoint is (Die maximale Parallelität für den Endpunkt beträgt) {1}. |
| TooManyConcurrentRequests | Too many concurrent requests sent. (Zu hohe Zahl von gleichzeitig gesendeten Anforderungen.) |
| TooManyHostsBeingInitialized | Too many hosts being initialized at the same time. (Zu viele Hosts werden zur gleichen Zeit initialisiert.) Consider throttling / retrying. (Erwägen Sie eine Drosselung oder eine Wiederholung des Vorgangs.) |
| TooManyHostsBeingInitializedPerModel | Too many hosts being initialized at the same time. (Zu viele Hosts werden zur gleichen Zeit initialisiert.) Consider throttling / retrying. (Erwägen Sie eine Drosselung oder eine Wiederholung des Vorgangs.) |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-Statuscode 504)
 
Der Vorgang konnte nicht innerhalb der zulässigen Zeit abgeschlossen werden.
 
| Fehlercode | Meldung für den Benutzer |
| ---------- |--------------|
| BackendInitializationTimeout | The web service initialization could not be completed within the permitted time. (Die Webdienstinitialisierung konnte nicht innerhalb der zulässigen Dauer abgeschlossen werden.) |
| BackendScoreTimeout | The web service request execution could not be completed within the permitted time. (Die Ausführung der Webdienstanforderung konnte nicht innerhalb der zulässigen Dauer abgeschlossen werden.) |
