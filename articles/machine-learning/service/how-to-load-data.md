---
title: 'Laden: Datenvorbereitung mit dem Python SDK'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie mehr über das Laden von Daten mit dem Azure Machine Learning Data Prep SDK. Sie können verschiedene Typen von Eingabedaten laden, Datendateitypen und -parameter angeben oder die intelligente SDK-Lesefunktionalität verwenden, um einen Dateityp automatisch erkennen zu lassen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 02/22/2019
ms.custom: seodec18
ms.openlocfilehash: fef3281f1f4e727b58878439e3f6456fee3b6241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752933"
---
# <a name="load-and-read-data-with-the-azure-machine-learning-data-prep-sdk"></a>Laden und Lesen von Daten mit dem Azure Machine Learning Data Prep SDK
In diesem Artikel lernen Sie verschiedene Methoden zum Laden von Daten mit dem Azure Machine Learning Data Prep SDK kennen.  Das SDK unterstützt mehrere Datenerfassungsfeatures, z. B.:

* Laden von Daten aus einer Vielzahl von Dateitypen mit Analyseparameter-Rückschluss (Codierung, Trennzeichen, Header)
* Typkonvertierung unter Verwendung der Ermittlung beim Laden der Datei
* Verbindungsunterstützung für MS SQL Server und Azure Data Lake Storage

> [!Important]
> Probieren Sie beim Erstellen einer neuen Projektmappe die [Azure Machine Learning-Datasets](how-to-explore-prepare-data.md) (Vorschauversion) für die Datenuntersuchung und -vorbereitung aus. Datasets ist die nächste Version des SDK für die Datenvorbereitung und bietet erweiterte Funktionen zum Verwalten von Datasets in KI-Lösungen.
> Wenn Sie das `azureml-dataprep`-Paket verwenden, um einen Datenfluss mit Ihren Transformationen zu erstellen, anstatt das `azureml-datasets`-Paket zum Erstellen eines Datensatzes zu verwenden, können Sie später keine Snapshots oder Datensätze mit Versionsangabe verwenden.

Die folgende Tabelle zeigt eine Auswahl von Funktionen, die zum Laden von Daten aus gängigen Dateitypen verwendet werden.

| Dateityp | Funktion | Referenzlink |
|-------|-------|-------|
|Beliebig|`auto_read_file()`|[Referenz](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Text|`read_lines()`|[Referenz](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-lines-path--filepath--header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[Referenz](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[Referenz](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Feste Breite|`read_fwf()`|[Referenz](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[Referenz](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Automatisches Laden von Daten

Um Daten automatisch ohne Angabe des Dateityps zu laden, verwenden Sie die `auto_read_file()`-Funktion. Der Typ der Datei und die Argumente, die erforderlich sind, um sie zu lesen, werden automatisch abgeleitet.

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Diese Funktion ist nützlich, um Dateityp, Codierung und andere Analyseargumente von einem geeigneten Einstiegspunkt aus automatisch zu ermitteln. Die Funktion führt auch automatisch die folgenden Schritte aus, die häufig beim Laden durch Trennzeichen getrennter Daten ausgeführt werden:

* Ableiten und Festlegen von Trennzeichen
* Überspringen leerer Datensätze am Anfang der Datei
* Ableiten und Festlegen der Kopfzeile

Wenn Sie den Dateityp vorab kennen und explizit steuern möchten, wie er analysiert wird, verwenden Sie die dateispezifischen Funktionen.

## <a name="load-text-line-data"></a>Laden von Textzeilendaten

Verwenden Sie zum Einlesen von einfachen Textdaten in einen Dataflow `read_lines()` ohne Angabe optionaler Parameter.

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||Liniendiagramm|
|----|-----|
|0|Datum \|\| Mindesttemperatur \|\| Maximaltemperatur|
|1|2015-07-1 \|\|  -4.1 \|\|  10.0|
|2|2015-07-2 \|\|  -0.8 \|\|  10.8|


Führen Sie nach der Datenerfassung den folgenden Code aus, um das Dataflowobjekt in einen Pandas-Datenrahmen zu konvertieren.

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Laden von CSV-Daten

Wenn Sie durch Trennzeichen getrennte Dateien lesen, kann die zugrunde liegende Laufzeitumgebung die Analyseparameter ableiten (z. B. Trennzeichen, Codierung, Angabe, ob Header verwendet werden sollen). Führen Sie den folgenden Code aus, um zu versuchen, eine CSV-Datei zu lesen, indem Sie nur den Speicherort angeben.

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |


Definieren Sie den Parameter `skip_rows`, um Zeilen während des Ladenvorgangs auszuschließen. Dieser Parameter überspringt das Laden von in der CSV-Datei absteigenden Zeilen (unter Verwendung eines einsbasierten Index).

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |

Führen Sie den folgenden Code aus, um die Spaltendatentypen anzuzeigen.

```python
dflow.dtypes
```
Ausgabe:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Standardmäßig wird der Datentyp vom Azure Machine Learning Data Prep SDK nicht geändert. Die Datenquelle, über den der Lesevorgang erfolgt, ist eine Textdatei. Das SDK liest also alle Werte als Zeichenfolgen. In diesem Beispiel sollten die numerischen Spalten als Zahlen analysiert werden. Legen Sie den Parameter `inference_arguments` auf `InferenceArguments.current_culture()` fest, um die Spaltentypen während des Dateilesevorgangs automatisch abzuleiten und zu konvertieren.

```
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
Ausgabe:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Einige der Spalten wurden ordnungsgemäß als numerisch erkannt, und deren Typ wird auf `float64` festgelegt.

## <a name="use-excel-data"></a>Verwenden von Excel-Daten

Das SDK enthält eine `read_excel()`-Funktion zum Laden von Excel-Dateien. Standardmäßig lädt die Funktion das erste Blatt in der Arbeitsmappe. Um ein bestimmtes Blatt anzugeben, das geladen werden soll, legen Sie den `sheet_name`-Parameter auf den Zeichenfolgenwert des Blattnamens fest.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine| |
|1|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine| |
|2|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine| |
|3|RANK|Titel|Studio|Worldwide|Domestic / %|Column1|Overseas / %|Column2|Year^| |
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

Die Ausgabe zeigt, dass die Daten im zweiten Blatt drei leere Zeilen vor den Headern aufwiesen. Die Funktion `read_excel()` enthält optionale Parameter zum Überspringen von Zeilen und Verwenden von Headern. Führen Sie den folgenden Code aus, um die ersten drei Zeilen zu überspringen und die vierte Zeile als Header zu verwenden.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||RANK|Titel|Studio|Worldwide|Domestic / %|Column1|Overseas / %|Column2|Year^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997^|

## <a name="load-fixed-width-data-files"></a>Laden von Datendateien mit fester Breite

Zum Laden von Dateien mit fester Breite geben Sie eine Liste mit Zeichenoffsets an. Bei der ersten Spalte wird immer davon ausgegangen, dass sie beim Offset Null (0) beginnt.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|


Um die Headererkennung zu vermeiden und die richtigen Daten zu analysieren, übergeben Sie `PromoteHeadersMode.NONE` an den `header`-Parameter.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||


## <a name="load-sql-data"></a>Laden von SQL-Daten

Das SDK kann auch Daten aus einer SQL-Quelle laden. Derzeit wird nur Microsoft SQL Server unterstützt. Um Daten von SQL Server zu lesen, erstellen Sie ein [`MSSQLDataSource`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py)-Objekt, das die Verbindungsparameter enthält. Der Kennwortparameter von `MSSQLDataSource` akzeptiert ein [`Secret`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret)-Objekt. Sie können ein Geheimnisobjekt auf zwei Arten erstellen:

* Registrieren Sie das Geheimnis und dessen Wert mit der Ausführungs-Engine.
* Erstellen Sie das Geheimnis mit nur einer `id` (wenn der Wert des Geheimnis bereits in der Ausführungsumgebung registriert ist) unter Verwendung von `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Nachdem Sie ein Datenquellobjekt erstellt haben, können Sie mit dem Lesen von Daten aus der Abfrageausgabe fortfahren.

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |ProductID|NAME|ProductNumber|Farbe|StandardCost|ListPrice|Größe|Weight|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|Schwarz|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Keine|Keine|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|Rot|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Keine|Keine|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|Rot|13.0863|34.99|Keine|Keine|35|33|2005-07-01 00:00:00+00:00|Keine|Keine|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|


## <a name="use-azure-data-lake-storage"></a>Verwenden von Azure Data Lake Storage

Es gibt zwei Möglichkeiten, mit dem das SDK das erforderliche OAuth-Token für den Zugriff auf Azure Data Lake Storage abrufen kann:

* Abrufen des Zugriffstokens von einer aktuellen Sitzung der Azure CLI-Anmeldung des Benutzers
* Verwenden eines Dienstprinzipals und eines Zertifikats als Geheimnis

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Verwenden eines Zugriffstokens aus einer aktuellen Azure CLI-Sitzung

Führen Sie auf dem lokalen Computer den folgenden Befehl aus.

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> Wenn Ihr Benutzerkonto Mitglied von mehr als einem Azure-Mandanten ist, müssen Sie den Mandanten in Form des AAD-URL-Hostnamens angeben.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Erstellen eines Dienstprinzipals über die Azure CLI

Erstellen Sie mit der Azure CLI einen Dienstprinzipal und das entsprechende Zertifikat. Dieser Dienstprinzipal wird mit der `reader`Rolle konfiguriert, wobei der Bereich ausschließlich auf das Azure Data Lake Storage-Konto „dpreptestfiles“ beschränkt ist.

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Dieser Befehl gibt die `appId` und den Pfad zur Zertifikatdatei (normalerweise im Ordner „home“) aus. Die CRT-Datei enthält das öffentliche Zertifikat sowie den privaten Schlüssel im PEM-Format.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Verwenden Sie zum Konfigurieren der ACL für das Azure Data Lake Storage-Dateisystem die objectId des Benutzers. In diesem Beispiel wird der Dienstprinzipal verwendet.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Um den Zugriff auf `Read` und `Execute` für das Azure Data Lake Storage-Dateisystem zu konfigurieren, konfigurieren Sie die ACL für Ordner und Dateien separat. Dies ist darauf zurückzuführen, dass das zugrunde liegende HDFS-ACL-Modell keine Vererbung unterstützt.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Abrufen eines OAuth-Zugriffstokens

Erstellen Sie mithilfe des Pakets `adal` (`pip install adal`) einen Authentifizierungskontext auf dem MSFT-Mandanten, und rufen Sie ein OAuth-Zugriffstoken ab. Bei ADLS muss die Ressource in der Tokenanforderung für „https:\//datalake.azure.net“ sein, die sich von den meisten anderen Azure-Ressourcen unterscheidet.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Website|street|city|County|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Caledonia|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|https://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Six Mile|||
|4|1010691|10th Street Community Farmers Market|https://agrimissouri.com/... |10th Street and Poplar|Lamar|Barton|

## <a name="next-steps"></a>Nächste Schritte

* Ein Beispiel zum Lösen eines spezifischen Szenarios finden Sie im [Tutorial](tutorial-data-prep.md) zum Azure Machine Learning-Data Prep SDK.
