---
title: 'ML Studio (Classic): Verwalten von Webdiensten mithilfe von API Management – Azure'
description: Diese Leitfaden zeigt, wie Sie Azure ML-Webdienste mithilfe von API Management verwalten. Verwalten Sie Ihre REST-API-Endpunkte, indem Sie Benutzerzugriff, Nutzungseinschränkungen und Dashboardüberwachung definieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: b00e75c5fda8a05f4ed0f3a756ba20cca570ba5c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305983"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Verwalten von (klassischen) Azure Machine Learning Studio-Webdiensten mit API Management

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


## <a name="overview"></a>Übersicht
Dieser Leitfaden beschreibt die ersten Schritte zur Verwaltung Ihrer (klassischen) Azure Machine Learning Studio-Webdienste mit API Management.

## <a name="what-is-azure-api-management"></a>Was ist Azure API Management?
Azure API Management ist ein Azure-Dienst, mit dem Sie Ihre REST-API-Endpunkte verwalten können, indem Sie Benutzerzugriff, Nutzungseinschränkungen und Dashboardüberwachung definieren. Weitere Informationen finden Sie auf der [API Management-Website](https://azure.microsoft.com/services/api-management/). Informationen zu den ersten Schritten mit Azure API Management finden Sie im [Leitfaden zum Importieren und Veröffentlichen](../../api-management/import-and-publish.md). In diesem Leitfaden (auf dem der vorliegende Leitfaden basiert) werden weitere Themen behandelt, z.B. Benachrichtigungskonfiguration, Tarife, Antwortverarbeitung, Benutzerauthentifizierung, Produkterstellung, Entwicklerabonnements und Nutzungsdashboards.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

* Ein Azure-Konto.
* Ein Azure ML-Konto.
* Den Arbeitsbereich, den Dienst und den API-Schlüssel für ein als Webdienst bereitgestelltes Azure ML-Experiment. Einzelheiten zum Erstellen eines AzureML-Experiments finden Sie im [Schnellstart für Studio](create-experiment.md). Informationen zum Bereitstellen eines (klassischen) Studio-Experiments als Webdienst finden Sie in der [Anleitung zur Bereitstellung von Studio](deploy-a-machine-learning-web-service.md). Hier erfahren Sie, wie Sie ein AzureML-Experiment als Webdienst bereitstellen. Alternativ dazu enthält Anhang A Anweisungen zum Erstellen und Testen eines einfachen Azure ML-Experiments und zum Bereitstellen dieses Experiments als Webdienst.

## <a name="create-an-api-management-instance"></a>Erstellen einer API Management-Instanz

Sie können Ihren Azure Machine Learning-Webdienst mit einer API Management-Instanz verwalten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **+ Ressource erstellen**.
3. Geben Sie im Suchfeld „API Management“ ein, und wählen Sie dann die Ressource „API Management“ aus.
4. Klicken Sie auf **Erstellen**.
5. Der Wert **Name** wird verwendet, um eine eindeutige URL zu erstellen (in diesem Beispiel „demoazureml“).
6. Wählen Sie **Abonnement** , **Ressourcengruppe** und **Region** für Ihre Dienstinstanz aus.
7. Geben Sie einen Wert für **Name der Organisation** ein (in diesem Beispiel „demoazureml“).
8. Geben Sie unter **Administrator-E-Mail** Ihre Adresse ein. Diese E-Mail-Adresse wird für Benachrichtigungen des API Management-Systems verwendet.
9. Klicken Sie auf **Erstellen**.

Es kann bis zu 30 Minuten dauern, bis ein neuer Dienst erstellt wird.

![Screenshot: Das Dialogfeld für den API Management-Dienst mit den erforderlichen Optionen zum Erstellen eines Diensts.](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Erstellen der API
Nach der Erstellung der Dienstinstanz können Sie die API erstellen. Eine API besteht aus einem Satz von Vorgängen, die aus Clientanwendungen aufgerufen werden können. API-Operationen funktionieren als Proxys für vorhandene Webdienste. In diesem Leitfaden werden APIs erstellt, die per Proxy an die vorhandenen Azure ML-Webdienste "RRS" und "BES" weiterleiten.

Gehen Sie wie folgt vor, um die API zu erstellen:

1. Öffnen Sie im Azure-Portal die von Ihnen erstellte Dienstinstanz.
2. Wählen Sie im linken Navigationsbereich die Option **APIs**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klicken Sie auf **API hinzufügen**.
2. Geben Sie unter **Web-API-Name** einen Namen ein (in diesem Beispiel „AzureML Demo API“).
3. Geben Sie unter **Webdienst-URL** den Wert „`https://ussouthcentral.services.azureml.net`“ ein.
4. Geben Sie ein „URL-Suffix der Web-API“ ein. Diese Angabe wird zum letzten Teil der URL, die von Kunden zum Senden von Anforderungen an die Dienstinstanz (in diesem Beispiel „azureml-demo“) verwendet wird.
5. Wählen Sie für **URL-Schema für Web-API** die Option **HTTPS**.
6. Wählen Sie für **Produkte** die Option **Starter**.
7. Klicken Sie auf **Speichern**.


## <a name="add-the-operations"></a>Hinzufügen der Vorgänge

Operationen werden einer API im Herausgeberportal hinzugefügt und in diesem konfiguriert. Klicken Sie zum Zugreifen auf das Herausgeberportal im Azure-Portal für Ihren API Management-Dienst auf **Herausgeberportal** , wählen Sie **APIs** und **Vorgänge** , und klicken Sie dann auf **Vorgang hinzufügen**.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Das Fenster **Neue Operation** wird angezeigt, und die Registerkarte **Signatur** ist standardmäßig ausgewählt.

## <a name="add-rrs-operation"></a>Hinzufügen eines RRS-Vorgangs
Erstellen Sie zunächst einen Vorgang für den Azure ML-RRS-Dienst:

1. Wählen Sie unter **HTTP-Verb** die Option **POST**.
2. Geben Sie unter **URL-Vorlage** den Text „`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`“ ein.
3. Geben Sie einen **Anzeigenamen** ein (in diesem Beispiel „RRS Execute“).

   ![Screenshot: Die Seite „Signatur“, auf der Sie einen Anzeigenamen eingeben können.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klicken Sie links auf **Antworten** > **HINZUFÜGEN** , und wählen Sie **200 OK** aus.
5. Klicken Sie auf **Speichern** , um diesen Vorgang zu speichern.

   ![Screenshot: Die Seite mit dem Vorgang „RRS Execute“ mit der Schaltfläche „Speichern“.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Hinzufügen von BES-Vorgängen

> [!NOTE]
> Für die BES-Vorgänge werden hier keine Screenshots bereitgestellt, da die Vorgehensweise dem Hinzufügen des RRS-Vorgangs sehr ähnlich ist.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Senden eines Batchausführungsauftrags (ohne ihn jedoch zu starten)

1. Klicken Sie auf **Vorgang hinzufügen** , um der API einen BES-Vorgang hinzuzufügen.
2. Wählen Sie unter **HTTP-Verb** die Option **POST**.
3. Geben Sie unter **URL-Vorlage** den Text „`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`“ ein.
4. Geben Sie einen **Anzeigenamen** ein (in diesem Beispiel „BES Submit“).
5. Klicken Sie links auf **Antworten** > **HINZUFÜGEN** , und wählen Sie **200 OK** aus.
6. Klicken Sie auf **Speichern**.

### <a name="start-a-batch-execution-job"></a>Starten eines Batchausführungsauftrags

1. Klicken Sie auf **Vorgang hinzufügen** , um der API einen BES-Vorgang hinzuzufügen.
2. Wählen Sie unter **HTTP-Verb** die Option **POST**.
3. Geben Sie unter **HTTP-Verb** den Text „`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`“ ein.
4. Geben Sie einen **Anzeigenamen** ein (in diesem Beispiel „BES Start“).
6. Klicken Sie links auf **Antworten** > **HINZUFÜGEN** , und wählen Sie **200 OK** aus.
7. Klicken Sie auf **Speichern**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Abrufen des Status oder des Ergebnisses eines Batchausführungsauftrags

1. Klicken Sie auf **Vorgang hinzufügen** , um der API einen BES-Vorgang hinzuzufügen.
2. Wählen Sie unter **HTTP-Verb** die Option **GET**.
3. Geben Sie unter **URL-Vorlage** den Text „`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`“ ein.
4. Geben Sie einen **Anzeigenamen** ein (in diesem Beispiel „BES Status“).
6. Klicken Sie links auf **Antworten** > **HINZUFÜGEN** , und wählen Sie **200 OK** aus.
7. Klicken Sie auf **Speichern**.

### <a name="delete-a-batch-execution-job"></a>Löschen eines Batchausführungsauftrags

1. Klicken Sie auf **Vorgang hinzufügen** , um der API einen BES-Vorgang hinzuzufügen.
2. Wählen Sie **DELETE** als **HTTP-Verb** aus.
3. Geben Sie unter **URL-Vorlage** den Text „`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`“ ein.
4. Geben Sie einen **Anzeigenamen** ein (in diesem Beispiel „BES Delete“).
5. Klicken Sie links auf **Antworten** > **HINZUFÜGEN** , und wählen Sie **200 OK** aus.
6. Klicken Sie auf **Speichern**.

## <a name="call-an-operation-from-the-developer-portal"></a>Aufrufen eines Vorgangs über das Entwicklerportal

Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen. In diesem Schritt rufen Sie die **RRS Execute** -Methode auf, die der **Azure ML-Demo-API** hinzugefügt wurde. 

1. Klicken Sie auf **Entwicklerportal**.

   ![Screenshot: Der Link „Entwicklerportal“.](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klicken Sie im Hauptmenü auf **APIs** und anschließend auf **Azure ML-Demo-API** , um die verfügbaren Vorgänge anzuzeigen.

   ![Screenshot: Der Link „Azure ML-Demo-API“.](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Wählen Sie als Vorgang **RRS Execute** aus. Klicken Sie auf **Testen**.

   ![Screenshot: Das Dialogfeld „Azure ML-Demo-API mit ausgewählter Option „Post RRS Execute“ und der Schaltfläche „Testen“.](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Geben Sie als **Anforderungsparameter** Ihren **Arbeitsbereich** und **Dienst** sowie „2.0“ als **apiversion** und „true“ für **details** ein. Sie finden Ihren **Arbeitsbereich** und **Dienst** im Azure ML-Webdienstdashboard (siehe **Testen des Webdiensts** in Anhang A).

   Klicken Sie für **Anforderungsheader** auf **Header hinzufügen** , und geben Sie „Content-Type“ und „application/json“ ein. Klicken Sie erneut auf **Header hinzufügen** , und geben Sie „Authorization“ und „Bearer *\<your service API-KEY\>* “ ein. Sie finden Ihren API-Schlüssel im Azure ML-Webdienstdashboard (siehe **Testen des Webdiensts** in Anhang A).

   Geben Sie unter **Anforderungstext** den Text `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}` ein.

   ![Screenshot: Die Anforderungsparameter, Anforderungsheader, der Anforderungstext und die Autorisierung für die Azure ML-Demo-API.](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klicken Sie auf **Send**.

   ![Screenshot: Die Schaltfläche zum Senden.](./media/manage-web-service-endpoints-using-api-management/send.png)

Nach dem Aufruf der Operation zeigt das Entwicklerportal die **Angeforderte URL** vom Back-End-Dienst, den **Antwortstatus** , die **Antwortheader** sowie den **Antwortinhalt** an.

![Screenshot: Das Entwicklerportal, in dem der Antwortstatus, die Antwortlatenz, der Antwortheader und der Antwortinhalt angezeigt werden.](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Anhang A – Erstellen und Testen eines einfachen Azure ML-Webdiensts
### <a name="creating-the-experiment"></a>Erstellen des Experiments
Im Folgenden finden Sie die Schritte, die zum Erstellen eines einfachen Azure ML-Experiments und zum Bereitstellen des Experiments als Webdienst erforderlich sind. Der Webdienst akzeptiert als Eingabe eine Spalte mit beliebigem Text und gibt einen Satz von Features zurück, die als Ganzzahlen dargestellt werden. Beispiel:

| Text | Text im Hashformat |
| --- | --- |
| This is a good day |1 1 2 2 0 2 0 1 |

Navigieren Sie in einem Browser Ihrer Wahl zu dieser Adresse: [https://studio.azureml.net/](https://studio.azureml.net/). Geben Sie dort Ihre Anmeldeinformationen ein, um sich anzumelden. Erstellen Sie als Nächstes ein neues Experiment

![Screenshot: Neue Seite mit „EXPERIMENT“ ausgewählt und einer Textsuche.](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Benennen Sie es um in: **SimpleFeatureHashingExperiment**. Erweitern Sie **Saved Datasets** , und ziehen Sie **Book Reviews from Amazon** in Ihr Experiment.

![Screenshot: Beispiele auf der linken Seite und ein SimpleFeatureHashingExperiment-Bereich auf der rechten Seite mit der Anweisung, Elemente hierher zu ziehen.](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Erweitern Sie **Data Transformation** und **Manipulation** , und ziehen Sie **Select Columns in Dataset** in Ihr Experiment. Verbinden Sie **Book Reviews from Amazon** mit **Select Columns in Dataset**.

![Verbinden des Moduls für das Book Reviews-Dataset mit einem Project Columns-Modul](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klicken Sie auf **Select Columns in Dataset**. Klicken Sie dann auf **Launch column selector** , und wählen Sie **Col2** aus. Klicken Sie auf das Häkchen, um diese Änderungen zu übernehmen.

![Auswählen von Spalten mithilfe von Spaltennamen](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Erweitern Sie **Text Analytics** , und ziehen Sie **Feature Hashing** in das Experiment. Verbinden Sie **Select Columns in Dataset** mit **Feature Hashing**.

![Screenshot: Hinzufügen eines Feature Hashing-Elements zum Arbeitsbereich.](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Geben Sie **3** als **Hashing bitsize** ein. Dadurch werden 8 (23) Spalten erstellt.

![Screenshot: Eigenschaften mit ausgewähltem Feature Hashing, und Sie können die Bitgröße für das Hashing eingeben.](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

An diesem Punkt können Sie auf **Run** klicken, um das Experiment zu testen.

![Screenshot: Schaltfläche „Run“.](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Erstellen eines Webdiensts
Nun erstellen Sie einen Webdienst. Erweitern Sie **Web Service** , und ziehen Sie **Input** in das Experiment. Verbinden Sie **Input** mit **Feature Hashing**. Ziehen Sie auch **Output** in Ihr Experiment. Verbinden Sie **Output** mit **Feature Hashing**.

![Screenshot: Arbeitsbereich nach den angegebenen Änderungen.](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klicken Sie auf **Publish web service**.

![Screenshot: Die Schaltfläche „Publish web service“.](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klicken Sie auf **Yes** , um das Experiment zu veröffentlichen.

![Screenshot: Eine Bestätigungsmeldung und die Option zum Veröffentlichen.](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testen des Webdiensts
Ein Azure ML-Webdienst besteht aus RRS-Endpunkten (Request/Response Service, Anforderungs-/Antwortdienst) und BES-Endpunkten (Batch Execution Service, Batchausführungsdienst). RRS dient zur synchronen Ausführung. BES dient zur asynchronen Auftragsausführung. Um Ihren Webdienst mit dem unten aufgeführten Python-Beispielcode zu testen, müssen Sie möglicherweise das Azure-SDK für Python herunterladen und installieren (siehe: [Installieren von Python](/azure/developer/python/azure-sdk-install)).

Sie benötigen auch den **Arbeitsbereich** , den **Dienst** und den **API-Schlüssel** Ihres Experiments für den unten stehenden Beispielcode. Sie finden den Arbeitsbereich und den Dienst, indem Sie im Webdienstdashboard für Ihr Experiment entweder auf **Anforderung/Antwort** oder **Batchausführung** klicken.

![Screenshot: Der Bereich „Anforderungen“, in dem Sie die Werte für Arbeitsbereich und Dienst finden.](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Den **API-Schlüssel** finden Sie, indem Sie im Webdienstdashboard auf Ihr Experiment klicken.

![Screenshot: Das Experiment im Webdienstdashboard, wo Sie den API-Schlüssel finden können.](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testen des RRS-Endpunkts
##### <a name="test-button"></a>Schaltfläche "Test"
Um den RRS-Endpunkt zu testen, können Sie ganz einfach auf dem Webdienstdashboard auf **Test** klicken.

![Screenshot: Das Experiment im Webdienstdashboard, das über die Schaltfläche „Test“ verfügt.](./media/manage-web-service-endpoints-using-api-management/test.png)

Geben Sie für **col2** den Text **This is a good day** ein. Klicken Sie auf das Häkchen.

![Screenshot: Das Dialogfeld zum Eingeben vorherzusagender Daten, in dem Sie Text eingeben können, z. B. „This is a good day“.](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Die Ausgabe sollte folgendermaßen aussehen:

![Screenshot: Das Ergebnis des Experiments, das lautet: „This is a good day“ und mehrere Ziffern in Anführungszeichen.](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Beispielcode
Sie können Ihren RRS-Endpunkt auch mithilfe des Clientcodes testen. Wenn Sie auf dem Dashboard auf **Anforderung/Antwort** klicken und ganz nach unten scrollen, sehen Sie Beispielcode für C#, Python und R. Sie sehen auch die Syntax der RRS-Anforderung einschließlich URI, Header und Text der Anforderung.

Dieser Leitfaden zeigt ein funktionierendes Python-Beispiel. Sie müssen dieses Beispiel mit dem **Arbeitsbereich** , dem **Dienst** und dem **API-Schlüssel** Ihres Experiments bearbeiten.

```python
import urllib2
import json
workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
data = {
"Inputs": {
    "input1": {
        "ColumnNames": ["Col2"],
        "Values": [ [ "This is a good day" ] ]
    },
},
"GlobalParameters": { }
}
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
body = str.encode(json.dumps(data))
try:
    req = urllib2.Request(url, body, headers)
    response = urllib2.urlopen(req)
    result = response.read()
    print "result:" + result
        except urllib2.HTTPError, error:
    print("The request failed with status code: " + str(error.code))
    print(error.info())
    print(json.loads(error.read()))
```

#### <a name="test-bes-endpoint"></a>Testen des BES-Endpunkts
Klicken Sie auf dem Dashboard auf **Batchausführung** , und scrollen Sie bis zum Ende. Sie sehen Beispielcode für C#, Python und R. Sie sehen auch die Syntax der BES-Anforderungen zum Übermitteln, Starten, Abrufen des Status und Löschen eines Auftrags.

Dieser Leitfaden zeigt ein funktionierendes Python-Beispiel. Sie müssen dieses Beispiel mit dem **Arbeitsbereich** , dem **Dienst** und dem **API-Schlüssel** Ihres Experiments bearbeiten. Darüber hinaus müssen Sie den **Speicherkontonamen** , den **Speicherkontoschlüssel** und den **Speichercontainernamen** ändern. Und schließlich müssen Sie den Speicherort der **Eingabedatei** und den Speicherort der **Ausgabedatei** bearbeiten.

```python
import urllib2
import json
import time
from azure.storage import *
workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
service = "<REPLACE WITH YOUR SERVICE ID>"
api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
input_blob_name = "mydatablob.csv"
output_blob_name = "myresultsblob.csv"
def printHttpError(httpError):
print("The request failed with status code: " + str(httpError.code))
print(httpError.info())
print(json.loads(httpError.read()))
return
def saveBlobToFile(blobUrl, resultsLabel):
print("Reading the result from " + blobUrl)
try:
    response = urllib2.urlopen(blobUrl)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
with open(output_file, "wb+") as f:
    f.write(response.read())
print(resultsLabel + " have been written to the file " + output_file)
return
def processResults(result):
first = True
results = result["Results"]
for outputName in results:
    result_blob_location = results[outputName]
    sas_token = result_blob_location["SasBlobToken"]
    base_url = result_blob_location["BaseLocation"]
    relative_url = result_blob_location["RelativeLocation"]
    print("The results for " + outputName + " are available at the following Azure Storage location:")
    print("BaseLocation: " + base_url)
    print("RelativeLocation: " + relative_url)
    print("SasBlobToken: " + sas_token)
    if (first):
        first = False
        url3 = base_url + relative_url + sas_token
        saveBlobToFile(url3, "The results for " + outputName)
return

def invokeBatchExecutionService():
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
print("Uploading the input to blob storage...")
data_to_upload = open(input_file, "r").read()
blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
print "Uploaded the input to blob storage"
input_blob_path = "/" + storage_container_name + "/" + input_blob_name
connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
payload =  {
    "Input": {
        "ConnectionString": connection_string,
        "RelativeLocation": input_blob_path
    },
    "Outputs": {
        "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
    },
    "GlobalParameters": {
    }
}
    body = str.encode(json.dumps(payload))
headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
print("Submitting the job...")
# submit the job
req = urllib2.Request(url + "?api-version=2.0", body, headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
result = response.read()
job_id = result[1:-1] # remove the enclosing double-quotes
print("Job ID: " + job_id)
# start the job
print("Starting the job...")
req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
url2 = url + "/" + job_id + "?api-version=2.0"

while True:
    print("Checking the job status...")
    # If you are using Python 3+, replace urllib2 with urllib.request in the following code
    req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = json.loads(response.read())
    status = result["StatusCode"]
    print "status:" + status
    if (status == 0 or status == "NotStarted"):
        print("Job " + job_id + " not yet started...")
    elif (status == 1 or status == "Running"):
        print("Job " + job_id + " running...")
    elif (status == 2 or status == "Failed"):
        print("Job " + job_id + " failed!")
        print("Error details: " + result["Details"])
        break
    elif (status == 3 or status == "Cancelled"):
        print("Job " + job_id + " cancelled!")
        break
    elif (status == 4 or status == "Finished"):
        print("Job " + job_id + " finished!")
        processResults(result)
        break
    time.sleep(1) # wait one second
return
invokeBatchExecutionService()
```