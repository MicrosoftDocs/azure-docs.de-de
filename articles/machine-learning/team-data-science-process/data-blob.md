---
title: Verarbeiten von Azure-Blobdaten mit erweiterter Analyse – Team Data Science-Prozess
description: Erfahren Sie, wie Sie mithilfe der erweiterten Analyse Daten untersuchen und Features aus Daten generieren, die in Azure Blob Storage gespeichert sind.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a91c4d9f5dcdcee436f2dbf012eb5485b7a92192
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60398541"
---
# <a name="heading"></a>Verarbeiten von Azure-Blobdaten mit erweiterter Analyse
In diesem Dokument werden das Durchsuchen von Daten und Generieren von Funktionen aus Daten in Azure Blob Storage beschrieben. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Laden der Daten in ein Pandas-DataFrame
Um ein Dataset zu untersuchen und zu bearbeiten, muss es aus der Blobquelle in eine lokale Datei heruntergeladen werden, die anschließend in ein Pandas-DataFrame geladen werden kann. Nachfolgend sehen Sie für dieses Verfahren erforderlichen Schritte:

1. Laden Sie die Daten mithilfe des Blobdiensts und folgenden Python-Beispielcodes aus dem Azure-Blob herunter. Ersetzen Sie die Variablen im Code durch die für Ihre Umgebung geltenden Werte: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Lesen Sie die Daten aus der heruntergeladenen Datei in ein Pandas-DataFrame ein.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Sie können nun die Daten durchsuchen und Funktionen mit diesem DataSet generieren.

## <a name="blob-dataexploration"></a>Durchsuchen von Daten
Hier sind einige Beispiele für Möglichkeiten zum Durchsuchen von Daten mithilfe von Pandas:

1. Überprüfen der Anzahl von Zeilen und Spalten: 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Überprüfen der ersten oder letzten Zeilen im DataSet:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Überprüfen des Datentyps der einzelnen importierten Spalten:
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Überprüfen der Basisstatistiken für die Spalten im DataSet:
   
        dataframe_blobdata.describe()
5. Überprüfen der Anzahl der Einträge für jeden Spaltenwert:
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Zählen der fehlenden Werte im Vergleich zur tatsächlichen Anzahl der Einträge in den einzelnen Spalten:
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Löschen fehlender Werte in einer bestimmten Spalte aus den Daten:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Alternatives Ersetzen der fehlenden Werte mit der "mode"-Funktion:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Erstellen eines Histogramms mithilfe der Variablenanzahl von Gruppen zur Darstellung der Verteilung einer Variablen:    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Überprüfen der Korrelationen zwischen Variablen mithilfe eines Punktdiagramms oder einer integrierten "correlation"-Funktion:
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Generieren von Funktionen
Sie können Funktionen wie folgt mithilfe von Python generieren:

### <a name="blob-countfeature"></a>Indikatorwertbasiertes Generieren von Funktionen
Kategorische Funktionen können wie folgt erstellt werden:

1. Untersuchen Sie die Verteilung der Kategoriespalte:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generieren Sie Indikatorwerte für alle Spaltenwerte:
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Führen Sie die Indikatorspalte mit dem ursprünglichen DataFrame zusammen: 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Entfernen Sie die ursprüngliche Variable:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Gruppenbasierte Funktionsgenerierung
Zum Generieren von klassifizierten Funktionen gehen Sie wie folgt vor:

1. Fügen Sie eine Folge von Spalten zum Klassifizieren einer numerischen Spalte hinzu:
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konvertieren Sie die Klassifizierung in eine Folge von booleschen Variablen:
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Führen Sie zum Schluss die Platzhaltervariablen mit dem ursprünglichen DataFrame zusammen:
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Zurückschreiben von Daten in das Azure-Blob und Verwenden in Azure Machine Learning
Nachdem Sie die Daten untersucht und die erforderlichen Features erstellt haben, können Sie die Daten (als Stichproben oder Features) mithilfe der folgenden Schritte in ein Azure-Blob hochladen und in Azure Machine Learning verwenden: Beachten Sie, dass zusätzliche Features auch in Azure Machine Learning Studio erstellt werden können. 

1. Schreiben Sie den DataFrame in eine lokale Datei:
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Laden Sie die Daten in ein Azure-Blob hoch:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Die Daten können nun wie im folgenden Screenshot gezeigt mit dem Modul [Import Data][import-data] von Azure Machine Learning aus dem Blob gelesen werden:

![Reader-Blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

