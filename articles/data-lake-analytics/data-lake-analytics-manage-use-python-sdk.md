---
title: Verwalten von Azure Data Lake Analytics mithilfe von Python
description: In diesem Artikel wird beschrieben, wie Sie Python zum Verwalten von Data Lake Analytics-Konten, -Datenquellen, -Benutzern und -Aufträgen verwenden.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 82007c780a0c9ff3bb2e1a50a4826499f9df9c9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811706"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Verwalten von Azure Data Lake Analytics mithilfe von Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In diesem Artikel wird beschrieben, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mit Python verwalten.

## <a name="supported-python-versions"></a>Unterstützte Python-Versionen

* Verwenden Sie eine 64-Bit-Version von Python.
* Sie können die Python-Standarddistribution unter **[Python.org downloads](https://www.python.org/downloads/)** verwenden. 
* Viele Entwickler bevorzugen die Verwendung der **[Python-Distribution Anaconda](https://www.anaconda.com/download/)** .  
* Für diesen Artikel wurde die Python-Version 3.6 der Python-Standarddistribution verwendet.

## <a name="install-azure-python-sdk"></a>Installieren des Azure Python SDK

Installieren Sie die folgenden Module:

* Das Modul **azure-mgmt-resource** enthält andere Azure-Module, z.B. für Active Directory.
* Das Modul **azure-datalake-store** umfasst die Vorgänge für das Azure Data Lake Store-Dateisystem. 
* Das Modul **azure-mgmt-datalake-store** beinhaltet Kontoverwaltungsvorgänge für Azure Data Lake Store.
* Das Modul **azure-mgmt-datalake-analytics** beinhaltet die Vorgänge für Azure Data Lake Analytics. 

Vergewissern Sie sich zunächst, dass die aktuelle Version von `pip` verwendet wird, indem Sie folgenden Befehl ausführen:

```
python -m pip install --upgrade pip
```

Für dieses Dokument wurde `pip version 9.0.1` verwendet.

Verwenden Sie die folgenden `pip`-Befehle, um die Module über die Befehlszeile zu installieren:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Erstellen eines Python-Skripts

Fügen Sie den folgenden Code in das Skript ein:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Führen Sie dieses Skript aus, um zu überprüfen, ob die Module importiert werden können.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktive Benutzerauthentifizierung mit einem Popup

Diese Methode wird nicht unterstützt.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktive Benutzerauthentifizierung mit einem Gerätecode

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Nicht interaktive Authentifizierung mit SPI und einem Geheimnis

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Nicht interaktive Authentifizierung mit API und einem Zertifikat

Diese Methode wird nicht unterstützt.

## <a name="common-script-variables"></a>Allgemeine Skriptvariablen

Diese Variablen werden in den Beispielen verwendet.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Erstellen der Clients

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Erstellen eines Data Lake Analytics-Kontos

Erstellen Sie zunächst ein Speicherkonto.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Erstellen Sie dann ein ADLA-Konto, das diesen Speicher verwendet.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Übermitteln eines Auftrags

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Warten auf den Abschluss eines Auftrags

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Auflisten von Pipelines und Wiederholungen
Abhängig davon, ob an Ihre Aufträge Pipeline- oder Wiederholungsmetadaten angefügt sind, können Sie Pipelines und Wiederholungen auflisten.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Verwalten von Computerichtlinien

Das DataLakeAnalyticsAccountManagementClient-Objekt enthält Methoden zum Verwalten der Computerichtlinien für ein Data Lake Analytics-Konto.

### <a name="list-compute-policies"></a>Auflisten von Computerichtlinien

Mit dem folgenden Code wird eine Liste von Computerichtlinien für ein Data Lake Analytics-Konto abgerufen.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Erstellen einer Computerichtlinie

Mit dem folgenden Code wird eine neue Computerichtlinie für ein Data Lake Analytics-Konto erstellt, mit der die maximal verfügbaren Analytics-Einheiten für den angegebenen Benutzer auf 50 und die minimale Auftragspriorität auf 250 festgelegt wird.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie dasselbe Tutorial mit anderen Tools verwenden möchten, klicken Sie oben auf der Seite auf die Registerkartenauswahl.
- Informationen zum Erlernen von U-SQL finden Sie unter [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informationen zu Verwaltungsaufgaben finden Sie unter [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md).

