---
title: Machine Learning- und Data Science-Tools – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Machine Learning-Tools und -Frameworks, die auf der Data Science Virtual Machine vorinstalliert sind.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: e8876306e4ffbd0fa9a8aafc6d5d757fd3c9c614
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502090"
---
# <a name="machine-learning-and-data-science-tools"></a>Machine Learning- und Data Science-Tools
Virtuelle Data Science-Computer in Azure verfügen über ein umfangreiches Angebot an Tools und Bibliotheken für Machine Learning (ML) in gängigen Sprachen wie Python, R und Julia. 

Hier finden Sie einige der ML-Tools und -Bibliotheken für virtuelle Data Science-Computer. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) (SDK)
|    |           |
| ------------- | ------------- |
| Was ist das?   |   Azure Machine Learning ist ein Clouddienst, den Sie zum Entwickeln und Bereitstellen von ML-Modellen verwenden können. Sie können Ihre Modelle beim Erstellen, Trainieren, Skalieren und Verwalten mit dem Python SDK nachverfolgen. Stellen Sie Modelle als Container bereit, und führen Sie sie in der Cloud, lokal oder in Azure IoT Edge aus.   |
| Unterstützte Editionen     | Windows (Conda-Umgebung: AzureML), Linux (Conda-Umgebung: py36)    |
| Typische Verwendung      | Allgemeine ML-Plattform      |
| Konfiguration/Installation      |  Installiert mit GPU-Unterstützung   |
| Verwendung/Ausführung      | Als Python SDK und Azure-Befehlszeilenschnittstelle. Aktivieren Sie die Conda-Umgebung `AzureML` für die Windows-Edition *oder* `py36` für die Linux-Edition.      |
| Link zu Beispielen      | Exemplarische Jupyter-Notebooks finden Sie im Verzeichnis `AzureML` unter „notebooks“.  |
| Verwandte Tools      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Was ist das?   |    XGBoost ist eine schnelle, portable und verteilte Gradient Boosting-Bibliothek (GBDT, GBRT oder GBM) für Python, R, Java, Scala, C++ und mehr. Sie eignet sich für einen einzelnen Computer, für Hadoop und für Spark.    |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeine ML-Bibliothek      |
| Konfiguration/Installation      |  Installiert mit GPU-Unterstützung   |
| Verwendung/Ausführung      | Als Python-Bibliothek (2.7 und 3.5), R-Paket und pfadbasiertes Befehlszeilentool (`C:\dsvm\tools\xgboost\bin\xgboost.exe` für Windows, `/dsvm/tools/xgboost/xgboost` für Linux)    |
| Links zu Beispielen      | Beispiele stehen auf dem virtuellen Computer unter `/dsvm/tools/xgboost/demo` (Linux) bzw. unter `C:\dsvm\tools\xgboost\demo` (Windows) zur Verfügung.   |
| Verwandte Tools      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Was ist das?   |   Vowpal Wabbit (VW) ist eine schnelle, Open-Source-basierte Out-of-Core-Lernsystembibliothek.    |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeine ML-Bibliothek      |
| Konfiguration/Installation      |  MSI-Installationsprogramm (Windows), apt-get (Linux) |
| Verwendung/Ausführung      | Als pfadbasiertes Befehlszeilentool (`C:\Program Files\VowpalWabbit\vw.exe` unter Windows, `/usr/bin/vw` unter Linux)    |
| Link zu Beispielen      | [Beispiele für VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Verwandte Tools      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Was ist das?   |  Weka ist eine Sammlung von ML-Algorithmen für Data Mining. Die Algorithmen können entweder direkt auf ein Dataset angewendet oder über Ihren eigenen Java-Code aufgerufen werden. Weka enthält Tools für Datenvorverarbeitung, Klassifizierung, Regression, Clustering, Zuordnungsregeln und Visualisierung. |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeines ML-Tool     |
| Verwendung/Ausführung      | Unter Windows finden Sie Weka im Startmenü. Unter Linux finden Sie Weka nach der Anmeldung mit X2Go unter **Anwendungen** > **Entwicklung** > **Weka**. |
| Link zu Beispielen      | [Beispiele für Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Verwandte Tools      |LightGBM, Rattle, XGBoost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Was ist das?   |   Rattle ist eine grafische Benutzeroberfläche für Data Mining unter Verwendung von R.   |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeines UI-basiertes Data Mining-Tool für R    |
| Verwendung/Ausführung      | UI-Tool. Starten Sie unter Windows eine Eingabeaufforderung, und führen Sie R und anschließend `rattle()` (innerhalb von R) aus. Stellen Sie unter Linux eine Verbindung über X2Go her, starten Sie ein Terminal, und führen Sie R und anschließend `rattle()` (innerhalb von R) aus. |
| Link zu Beispielen      | [Rattle](https://togaware.com/onepager/) |
| Verwandte Tools      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Was ist das?   | LightGBM ist ein schnelles, verteiltes Gradient Boosting-Hochleistungsframework (GBDT, GBRT, GBM oder MART) auf der Grundlage von Entscheidungsstrukturalgorithmen. Es wird für Rangfolgen, Klassifizierungen und viele andere ML-Aufgaben verwendet.    |
| Unterstützte Versionen      | Windows, Linux    |
| Typische Verwendung      | Allgemeines Gradient-Boosted-Framework      |
| Konfiguration/Installation      | Unter Windows ist LightGBM als Python-Paket installiert. Unter Linux befindet sich die über die Befehlszeile ausführbare Datei unter `/opt/LightGBM/lightgbm`. Außerdem sind das R-Paket und Python-Pakete installiert.     |
| Link zu Beispielen      | [Leitfaden zu LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Verwandte Tools      | MXNet, XGBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Was ist das?   | H2O ist eine Open-Source-KI-Plattform für verteiltes, schnelles und skalierbares In-Memory-ML.  |
| Unterstützte Versionen      | Linux   |
| Typische Verwendung      | Universelles verteiltes und skalierbares ML   |
| Konfiguration/Installation      | H2O ist unter `/dsvm/tools/h2o` installiert.      |
| Verwendung/Ausführung      | Stellen Sie über X2Go eine Verbindung mit dem virtuellen Computer her. Starten Sie ein neues Terminal, und führen Sie `java -jar /dsvm/tools/h2o/current/h2o.jar` aus. Stellen Sie dann über einen Webbrowser eine Verbindung mit `http://localhost:54321` her.      |
| Link zu Beispielen      | Beispiele stehen auf dem virtuellen Computer in Jupyter im Verzeichnis `h2o` zur Verfügung.      |
| Verwandte Tools      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Für virtuelle Data Science-Computer stehen noch weitere ML-Bibliotheken wie etwa das beliebte `scikit-learn`-Paket zur Verfügung, das im Rahmen der auf virtuellen Data Science-Computern installierten Anaconda Python-Distribution bereitgestellt wird. Führen Sie die entsprechenden Paket-Manager aus, um sich die Liste mit den verfügbaren Paketen für Python, R und Julia anzusehen.
