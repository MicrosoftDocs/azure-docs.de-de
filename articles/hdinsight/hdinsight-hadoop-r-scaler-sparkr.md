---
title: Verwenden von ScaleR und SparkR mit Azure HDInsight
description: Verwenden von ScaleR und SparkR für die Datenbearbeitung und Modellentwicklung mit ML Services unter Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/26/2019
ms.openlocfilehash: c12398ceacf8495a05037422a6501dc8138abc10
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628693"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Kombinieren von ScaleR und SparkR in HDInsight

In diesem Dokument wird erläutert, wie Flugverspätungen mithilfe eines logistischen **ScaleR** -Regressionsmodells vorhergesagt werden können. Das Beispiel nutzt Flugverspätungs- und Wetterdaten, die mit **SparkR** verknüpft werden.

Obwohl beide Pakete in der Spark-Ausführungs-Engine von Apache Hadoop ausgeführt werden, sind sie für die In-Memory-Datenfreigabe gesperrt, da sie jeweils eigene Spark-Sitzungen erfordern. Bis dieses Problem in einer der nächsten Versionen von ML Server behoben wird, besteht die Problemumgehung darin, nicht überlappende Spark-Sitzungen zu verwenden und Daten mithilfe von Zwischendateien auszutauschen. Die folgenden Anweisungen zeigen, dass diese Anforderungen einfach zu erfüllen sind.

Dieses Beispiel wurde ursprünglich bei einem Vortrag von Mario Inchiosa und Roni Burd auf der Strata 2016 vorgestellt. Sie finden diesen Vortrag unter [Building a Scalable Data Science Platform with R(Erstellen einer skalierbaren Data Science-Plattform mit R)](https://channel9.msdn.com/blogs/Cloud-and-Enterprise-Premium/Building-A-Scalable-Data-Science-Platform-with-R-and-Hadoop).

Der Code wurde ursprünglich für ML Server unter Spark in einem HDInsight-Cluster in Azure geschrieben. Das Konzept der Kombination von SparkR und ScaleR in einem Skript gilt jedoch auch im Kontext lokaler Umgebungen.

Für die Schritte in diesem Dokument setzen wir einen mittleren Wissensstand in Bezug auf R und die [ScaleR](/machine-learning-server/r/concept-what-is-revoscaler)-Bibliothek von ML Server voraus. Sie erhalten im Verlauf des Szenarios auch eine Einführung in [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html).

## <a name="the-airline-and-weather-datasets"></a>Datasets mit Fluglinien- und Wetterdaten

Die Flugdaten stehen in den [Archiven der US-Regierung](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236) zur Verfügung. Sie sind auch in der ZIP-Datei [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip) verfügbar.

Die Wetterdaten können als ZIP-Dateien mit unformatiertem Inhalt für den jeweiligen Monat aus dem [Repository der National Oceanic and Atmospheric Administration](https://www.ncdc.noaa.gov/orders/qclcd/) heruntergeladen werden. Laden Sie in diesem Beispiel die Daten für Mai 2007 bis Dezember 2012 herunter. Verwenden Sie die stündlichen Datendateien und die Datei `YYYYMMMstation.txt` in jedem der ZIP-Archive.

## <a name="setting-up-the-spark-environment"></a>Einrichten der Spark-Umgebung

Richten Sie mithilfe des folgenden Codes die Spark-Umgebung ein:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Fügen Sie als Nächstes dem Suchpfad für R-Pakete `Spark_Home` hinzu. Durch Hinzufügen dieses Elements zum Suchpfad können Sie SparkR verwenden und eine SparkR-Sitzung initialisieren:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Vorbereiten der Wetterdaten

Zur Vorbereitung der Wetterdaten unterteilen Sie diese in die Spalten, die für die Modellierung erforderlich sind: 

- „Visibility“
- „DryBulbCelsius“
- „DewPointCelsius“
- „RelativeHumidity“
- „WindSpeed“
- „Altimeter“

Fügen Sie anschließend einen der Wetterstation zugeordneten Flughafencode ein, und konvertieren Sie die Messwerte von der Ortszeit in UTC.

Beginnen Sie, indem Sie eine Datei zum Zuordnen der Wetterstationsinformationen (WBAN) zu einem Flughafencode erstellen. Der unten angegebene Code bewirkt Folgendes: Alle Dateien mit den unformatierten stündlichen Wetterdaten werden gelesen, entsprechende Teilmengen werden den erforderlichen Spalten hinzugefügt, die Zuordnungsdatei mit den Wetterdaten wird verknüpft, Datum und Uhrzeit von Messungen werden an UTC angepasst, und anschließend wird eine neue Version der Datei geschrieben:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Importieren der Fluglinien- und Wetterdaten in Spark-Dataframes

Nun verwenden wir die SparkR-Funktion [read.df()](https://spark.apache.org/docs/latest/api/R/read.df.html) zum Importieren der Wetter- und Flugliniendaten in Spark-Dataframes. Diese Funktion wird – wie viele andere Spark-Methoden auch – zeitverzögert ausgeführt. Dies bedeutet, dass sie zur Ausführung in die Warteschlange eingereiht werden, aber erst dann ausgeführt werden, wenn dies erforderlich ist.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Datenbereinigung- und transformation

Als Nächstes führen wir einige Bereinigungsschritte an den Flugliniendaten durch, die wir importiert haben, und benennen die Spalten um. Wir behalten nur die erforderlichen Variablen bei und runden die geplanten Abflugzeiten auf die nächste Stunde ab. Damit ermöglichen wir das Zusammenführen mit den aktuellsten Wetterdaten beim Abflug:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Wir führen einige ähnliche Vorgänge für die Wetterdaten durch:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Verknüpfen von Wetter- und Flugliniendaten

Wir verwenden jetzt die SparkR-Funktion [join()](https://spark.apache.org/docs/latest/api/R/join.html), um eine linke äußere Verknüpfung der Fluglinien- und Wetterdaten nach den Werten „AirportID“ und „datetime“ für den Abflug zu erstellen. Mit der äußeren Verknüpfung können wir alle Datensätze der Flugliniendaten auch dann beibehalten, wenn keine passenden Wetterdaten vorhanden sind. Nach der Verknüpfung entfernen wir einige redundante Spalten und benennen die beibehaltenen Spalten um, um das Dataframepräfix für den eingehenden Datenverkehr zu entfernen, das durch die Verknüpfung eingefügt wurde.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

Auf ähnliche Weise verknüpfen wir die Wetter- und Flugliniendaten basierend auf den Werten „AirportID“ und „datetime“ für die Ankunft.

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Speichern von Ergebnissen in einer CSV-Datei für den Austausch mit ScaleR

Dies sind alle Verknüpfungen, die für SparkR eingerichtet werden müssen. Wir speichern die Daten des endgültigen Spark-Dataframes „joinedDF5“ in einer CSV-Datei für die Eingabe in ScaleR und schließen dann die SparkR-Sitzung. Wir weisen SparkR explizit an, die sich ergebende CSV-Datei in 80 separaten Partitionen zu speichern, um für eine ausreichende Parallelität bei der ScaleR-Verarbeitung zu sorgen:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Durchführen des Imports in XDF zur Verwendung durch ScaleR

Es ist möglich, die CSV-Datei mit den verknüpften Fluglinien- und Wetterdaten in unveränderter Form für die Modellierung über eine ScaleR-Textdatenquelle zu verwenden. Wir importieren sie aber zunächst in XDF, da dies effizienter ist, wenn im DataSet mehrere Vorgänge ausgeführt werden:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Aufteilen von Daten für Trainings- und Testzwecke

Wir verwenden rxDataStep, um die Daten von 2012 für Testzwecke abzutrennen, und verwenden den Rest für Trainingszwecke:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Trainieren und Testen eines logistischen Regressionsmodells

Wir sind jetzt bereit zum Erstellen eines Modells. Um den Einfluss der Wetterdaten auf verspätete Ankunftszeiten zu erkennen, verwenden wir die ScaleR-Routine für die logistische Regression. Wir modellieren damit, ob eine Ankunftsverspätung von mehr als 15 Minuten vom Wetter an den Abflug- und Ankunftsflughäfen beeinflusst wird:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Als Nächstes möchten wir herausfinden, wie das Ergebnis für die Testdaten aussieht, indem wir einige Vorhersagen treffen und uns die ROC- und AUC-Daten ansehen.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Andere Bewertungsmöglichkeiten

Sie können das Modell auch zum Bewerten von Daten auf einer anderen Plattform verwenden. Dazu speichern Sie es in einer RDS-Datei und übertragen und importieren diese Datei anschließend in die Zielbewertungsumgebung, z. B. Microsoft SQL Server R Services. Dabei sollte sichergestellt werden, dass die Faktorebenen der Daten, die bewertet werden sollen, mit den Faktorebenen übereinstimmen, auf deren Grundlage das Modell erstellt wurde. Diese Übereinstimmung kann erreicht werden, indem die Spalteninformationen, die den Modellierungsdaten zugeordnet sind, mit der ScaleR-Funktion `rxCreateColInfo()` extrahiert und gespeichert und dann zu Vorhersagezwecken auf die Eingabedatenquelle angewendet werden. Im folgenden Codebeispiel speichern wir einige Zeilen des Testdatasets und extrahieren und verwenden die Spalteninformationen aus diesem Beispiel im Vorhersageskript:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben wir gezeigt, wie die Verwendung von SparkR für die Datenbearbeitung mit ScaleR für die Modellentwicklung in Hadoop Spark kombiniert werden kann. Dieses Szenario erfordert die Verwendung separater Spark-Sitzungen, wobei nur jeweils eine Sitzung gleichzeitig ausgeführt werden darf, und den Austausch von Daten über die CSV-Dateien. Dieser Prozess ist nicht schwierig und wird in einem der nächsten ML Services-Releases noch einmal vereinfacht. Es wird dann möglich sein, dass SparkR und ScaleR eine Spark-Sitzung und somit auch Spark-Dataframes gemeinsam nutzen.

## <a name="next-steps-and-more-information"></a>Nächste Schritte und weitere Informationen

- Weitere Informationen zur Verwendung von ML Server auf Apache Spark finden Sie im [Leitfaden zu den ersten Schritten](/machine-learning-server/r/how-to-revoscaler-spark).

- Informationen zu ML Services in HDInsight finden Sie in der [Übersicht über ML Services in HDInsight](r-server/r-server-overview.md).

Weitere Informationen zur Verwendung von SparkR finden Sie unter:

- [Apache SparkR-Dokument](https://spark.apache.org/docs/2.1.0/sparkr.html) (in englischer Sprache).

- [SparkR Overview](/azure/databricks/spark/latest/sparkr/overview) (SparkR – Übersicht)
