---
title: Delete-Aktivität in Azure Data Factory
description: Hier erfahren Sie, wie Sie mit der Delete-Aktivität in Azure Data Factory Dateien in verschiedenen Dateispeichern löschen.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 3021d29f472dbbf43ae53981287b1f4676e8f932
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392713"
---
# <a name="delete-activity-in-azure-data-factory"></a>Delete-Aktivität in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Sie können mit der Delete-Aktivität in Azure Data Factory Dateien oder Ordner aus lokalen Speichern oder Cloudspeichern löschen. Verwenden Sie diese Aktivität zum Bereinigen oder Archivieren von Dateien, wenn diese nicht mehr benötigt werden.

> [!WARNING]
> Gelöschte Dateien oder Ordner können nicht wiederhergestellt werden (es sei denn, für den Speicher ist vorläufiges Löschen aktiviert). Gehen Sie mit Bedacht vor, wenn Sie Dateien oder Ordner mit der Delete-Aktivität löschen.

## <a name="best-practices"></a>Bewährte Methoden

Nachfolgend sind einige Empfehlungen für die Verwendung der Delete-Aktivität aufgeführt:

-   Sichern Sie Ihre Dateien, bevor Sie sie mit der Delete-Aktivität löschen, falls Sie die Dateien später wiederherstellen müssen.

-   Stellen Sie sicher, dass Data Factory über Schreibberechtigungen verfügt, um Ordner oder Dateien aus dem Speicher zu löschen.

-   Stellen Sie sicher, dass Sie keine Dateien löschen, die zur gleichen Zeit geschrieben werden. 

-   Verwenden Sie beim Löschen von Dateien oder Ordnern aus einem lokalen System unbedingt mindestens Version 3.14 der selbstgehosteten Integration Runtime.

## <a name="supported-data-stores"></a>Unterstützte Datenspeicher

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Dateisystem](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)
-   [HDFS](connector-hdfs.md)

## <a name="syntax"></a>Syntax

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "storeSettings": {
            "type": "<source type>",
            "recursive": true/false,
            "maxConcurrentConnections": <number>
        },
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Typeigenschaften

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| dataset | Stellt die Datasetreferenz zur Ermittlung der zu löschenden Dateien oder Ordner bereit. | Ja |
| recursive | Gibt an, ob die Dateien rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelöscht werden.  | Nein. Der Standardwert lautet `false`. |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig zum Löschen von Ordnern oder Dateien mit einem Speicher hergestellt werden können   |  Nein. Der Standardwert lautet `1`. |
| enablelogging | Gibt an, ob die Namen der gelöschten Ordner oder Dateien aufgezeichnet werden müssen. Bei „true“ müssen Sie zusätzlich ein Speicherkonto zum Speichern der Protokolldatei angeben, damit Sie anhand der Protokolldatei das Verhalten der Delete-Aktivität nachverfolgen können. | Nein |
| logStorageSettings | Gilt nur bei folgender Angabe: enablelogging = true.<br/><br/>Eine Gruppe von Speichereigenschaften, mit denen Sie angeben können, wo die Protokolldatei mit den Namen der Ordner oder Dateien gespeichert werden soll, die mit der Delete-Aktivität gelöschten wurden. | Nein |
| linkedServiceName | Gilt nur bei folgender Angabe: enablelogging = true.<br/><br/>Der verknüpfte [Azure Storage](connector-azure-blob-storage.md#linked-service-properties)-, [Azure Data Lake Store Gen1](connector-azure-data-lake-store.md#linked-service-properties)- oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)-Dienst zum Speichern der Protokolldatei, die die Namen der durch die Delete-Aktivität gelöschten Ordner oder Dateien enthält. Beachten Sie, dass bei der Konfiguration die gleiche Art von Integration Runtime verwendet werden muss wie bei der Delete-Aktivität für die Dateilöschung. | Nein |
| path | Gilt nur bei folgender Angabe: enablelogging = true.<br/><br/>Der Pfad zum Speichern der Protokolldatei in Ihrem Speicherkonto. Wenn Sie keinen Pfad angeben, erstellt der Dienst automatisch einen Container. | Nein |

## <a name="monitoring"></a>Überwachung

Es gibt zwei Stellen, an denen Sie die Ergebnisse der Delete-Aktivität anzeigen und überwachen können: 
-   In der Ausgabe der Delete-Aktivität
-   In der Protokolldatei

### <a name="sample-output-of-the-delete-activity"></a>Beispielausgabe der Delete-Aktivität

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Beispielprotokolldatei der Delete-Aktivität

| Name | Category | Status | Fehler |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Datei | Deleted |  |
| test2/hello789.txt | Datei | Deleted |  |
| test2/test3/hello000.txt | Datei | Deleted |  |
| test2/test3/zzz.json | Datei | Deleted |  |

## <a name="examples-of-using-the-delete-activity"></a>Beispiele zur Verwendung der Delete-Aktivität

### <a name="delete-specific-folders-or-files"></a>Löschen bestimmter Ordner oder Dateien

Der Speicher hat die folgenden Ordnerstruktur:

Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Nun verwenden Sie die Delete-Aktivität, um Ordner oder Dateien durch die Kombination verschiedener Eigenschaftswerte aus dem Dataset und der Delete-Aktivität zu löschen:

| folderPath | fileName | recursive | Output |
|:--- |:--- |:--- |:--- |
| Root/ Folder_A_2 | NULL | False | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/ Folder_A_2 | NULL | True | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Root/ Folder_A_2 | *.txt | False | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/ Folder_A_2 | *.txt | True | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Regelmäßiges Bereinigen der zeitpartitionierten Ordner oder Dateien

Sie können eine Pipeline erstellen, um die zeitpartitionierten Ordner oder Dateien in regelmäßigen Abständen zu bereinigen.  Die Ordnerstruktur sieht zum Beispiel wie folgt aus: `/mycontainer/2018/12/14/*.csv`.  Sie können mithilfe einer ADF-Systemvariablen aus dem Zeitplantrigger bestimmen, welche Ordner oder Dateien in den einzelnen Pipelineausführungen gelöscht werden sollen. 

#### <a name="sample-pipeline"></a>Beispiel-Pipeline

```json
{
    "name":"cleanup_time_partitioned_folder",
    "properties":{
        "activities":[
            {
                "name":"DeleteOneFolder",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"PartitionedFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "TriggerTime":{
                                "value":"@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type":"Expression"
                            }
                        }
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    }
                }
            }
        ],
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>Beispieldataset

```json
{
    "name":"PartitionedFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@dataset().TriggerTime",
                    "type":"Expression"
                },
                "container":{
                    "value":"mycontainer",
                    "type":"Expression"
                }
            }
        }
    }
}
```

#### <a name="sample-trigger"></a>Beispielauslöser

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Bereinigen der abgelaufenen Dateien, die zuletzt vor dem 1. Januar 2018 geändert wurden

Sie können eine Pipeline erstellen, um die alten oder abgelaufenen Dateien mithilfe des folgenden Dateiattributfilters im Dataset zu bereinigen: LastModified.  

#### <a name="sample-pipeline"></a>Beispiel-Pipeline

```json
{
    "name":"CleanupExpiredFiles",
    "properties":{
        "activities":[
            {
                "name":"DeleteFilebyLastModified",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"BlobFilesLastModifiedBefore201811",
                        "type":"DatasetReference"
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true,
                        "modifiedDatetimeEnd":"2018-01-01T00:00:00.000Z"
                    }
                }
            }
        ],
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>Beispieldataset

```json
{
    "name":"BlobFilesLastModifiedBefore201811",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"*",
                "folderPath":"mydirectory",
                "container":"mycontainer"
            }
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Verschieben von Dateien durch Verkettung der Copy-Aktivität und der Delete-Aktivität

Sie können eine Datei verschieben. Nutzen Sie dazu die Copy-Aktivität zum Kopieren einer Datei und anschließend die Delete-Aktivität zum Löschen einer Datei in einer Pipeline.  Wenn Sie mehrere Dateien verschieben möchten, können Sie wie im Beispiel weiter unten die folgenden Aktivitäten verwenden: GetMetadata-Aktivität, Filter-Aktivität, ForEach-Aktivität, Copy-Aktivität und Delete-Aktivität:

> [!NOTE]
> Lassen Sie Vorsicht walten, wenn Sie den gesamten Ordner verschieben möchten, indem Sie ein Dataset nur mit einem Ordnerpfad definieren und anschließend eine Copy- und eine Delete-Aktivität verwenden, um auf das gleiche Dataset zu verweisen, das einen Ordner darstellt. In diesem Fall müssen Sie sicherstellen, dass zwischen dem Kopiervorgang und dem Löschvorgang KEINE neuen Dateien im Ordner platziert werden.  Wenn neue Dateien zu dem Zeitpunkt im Ordner platziert werden, zu dem die Copy-Aktivität gerade abgeschlossen, die Delete-Aktivität jedoch noch nicht gestartet wurde, werden diese neuen Dateien, die noch NICHT ins Ziel kopiert wurden, unter Umständen von der Delete-Aktivität durch Löschung des gesamten Ordners gelöscht. 

#### <a name="sample-pipeline"></a>Beispiel-Pipeline

```json
{
    "name":"MoveFiles",
    "properties":{
        "activities":[
            {
                "name":"GetFileList",
                "type":"GetMetadata",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"OneSourceFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "Container":{
                                "value":"@pipeline().parameters.SourceStore_Location",
                                "type":"Expression"
                            },
                            "Directory":{
                                "value":"@pipeline().parameters.SourceStore_Directory",
                                "type":"Expression"
                            }
                        }
                    },
                    "fieldList":[
                        "childItems"
                    ],
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    },
                    "formatSettings":{
                        "type":"BinaryReadSettings"
                    }
                }
            },
            {
                "name":"FilterFiles",
                "type":"Filter",
                "dependsOn":[
                    {
                        "activity":"GetFileList",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('GetFileList').output.childItems",
                        "type":"Expression"
                    },
                    "condition":{
                        "value":"@equals(item().type, 'File')",
                        "type":"Expression"
                    }
                }
            },
            {
                "name":"ForEachFile",
                "type":"ForEach",
                "dependsOn":[
                    {
                        "activity":"FilterFiles",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('FilterFiles').output.value",
                        "type":"Expression"
                    },
                    "batchCount":20,
                    "activities":[
                        {
                            "name":"CopyAFile",
                            "type":"Copy",
                            "dependsOn":[

                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "source":{
                                    "type":"BinarySource",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageReadSettings",
                                        "recursive":false,
                                        "deleteFilesAfterCompletion":false
                                    },
                                    "formatSettings":{
                                        "type":"BinaryReadSettings"
                                    },
                                    "recursive":false
                                },
                                "sink":{
                                    "type":"BinarySink",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageWriteSettings"
                                    }
                                },
                                "enableStaging":false,
                                "dataIntegrationUnits":0
                            },
                            "inputs":[
                                {
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs":[
                                {
                                    "referenceName":"OneDestinationFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.DestinationStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.DestinationStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name":"DeleteAFile",
                            "type":"Delete",
                            "dependsOn":[
                                {
                                    "activity":"CopyAFile",
                                    "dependencyConditions":[
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "dataset":{
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                },
                                "logStorageSettings":{
                                    "linkedServiceName":{
                                        "referenceName":"BloblinkedService",
                                        "type":"LinkedServiceReference"
                                    },
                                    "path":"container/log"
                                },
                                "enableLogging":true,
                                "storeSettings":{
                                    "type":"AzureBlobStorageReadSettings",
                                    "recursive":true
                                }
                            }
                        }
                    ]
                }
            }
        ],
        "parameters":{
            "SourceStore_Location":{
                "type":"String"
            },
            "SourceStore_Directory":{
                "type":"String"
            },
            "DestinationStore_Location":{
                "type":"String"
            },
            "DestinationStore_Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-datasets"></a>Beispieldatasets

Von der GetMetadata-Aktivität verwendetes Dataset zum Enumerieren der Dateiliste:

```json
{
    "name":"OneSourceFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Von der Copy-Aktivität und der Delete-Aktivität verwendetes Dataset für die Datenquelle:

```json
{
    "name":"OneSourceFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Von der Copy-Aktivität verwendetes Dataset für das Datenziel:

```json
{
    "name":"OneDestinationFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Sie erhalten die Vorlage zum Verschieben von Dateien auch [hier](solution-template-move-files.md).

## <a name="known-limitation"></a>Bekannte Einschränkung

-   Die Delete-Aktivität unterstützt das Löschen von Ordnerliste, die durch Platzhalterzeichen beschrieben werden, nicht.

-   Stellen Sie bei Verwendung des Dateiattributfilters in der Löschaktivität modifiedDatetimeStart und modifiedDatetimeEnd zum Auswählen von zu löschenden Dateien sicher, dass folgende Zeichenfolge auch in der Löschaktivität festgelegt ist: "wildcardFileName": "*"

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verschieben von Dateien in Azure Data Factory.

-   [Tool zum Kopieren von Daten in Azure Data Factory](copy-data-tool.md)
