---
title: Lokale Verwendung von Azure Cognitive Services-Containern
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Cognitive Services mithilfe von Docker-Containern lokal verwendet werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 10/22/2020
ms.author: aahi
keywords: lokal, Docker, Container, Kubernetes
ms.openlocfilehash: 56d88b9223a80ea9981e8830cac44bac7c6edb3b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413036"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services-Container

> [!WARNING]
> Am 11. Juni 2020 kündigte Microsoft an, dass keine Technologie zur Gesichtserkennung mehr an die Polizeibehörden in den USA verkauft wird, bis es eine strenge, auf den Menschenrechten basierende Regelung gibt. Daher dürfen Kunden keine Gesichtserkennungsfeatures oder in Azure-Diensten enthaltenen Funktionen (wie Gesichtserkennung oder Video Indexer) verwenden, wenn es sich bei dem Kunden um eine Polizeibehörde in den USA handelt oder wenn der Kunde die Nutzung derartiger Dienste durch oder für eine Polizeibehörde zulässt.

Azure Cognitive Services bietet mehrere [Docker-Container](https://www.docker.com/what-container), mit denen Sie lokal die gleichen APIs verwenden können, die in Azure verfügbar sind. Die Verwendung dieser Container gibt Ihnen die Flexibilität, Cognitive Services aus Compliance-, Sicherheits- oder anderen betrieblichen Gründen näher an Ihre Daten heranzubringen. 

Die Containerunterstützung ist derzeit für einige Dienste von Azure Cognitive Services verfügbar, einschließlich:

> [!div class="checklist"]
> * [Anomalieerkennung][ad-containers]
> * [Read OCR (Optical Character Recognition; optische Zeichenerkennung)][cv-containers]
> * [Räumliche Analyse][spa-containers]
> * [Gesichtserkennung][fa-containers]
> * [Formularerkennung][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Spracherkennungsdienst-API][sp-containers]
> * [Textanalyse][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Die Verwendung von Containern ist ein Ansatz zur Softwareverteilung, bei dem eine Anwendung oder ein Dienst, einschließlich der Abhängigkeiten und der Konfiguration, als Containerimage zusammengepackt wird. Das Containerimage kann mit geringer oder ganz ohne Bearbeitung auf einem Containerhost bereitgestellt werden. Container sind voneinander und vom zugrunde liegenden Betriebssystem isoliert und nehmen weniger Speicher in Anspruch als ein virtueller Computer. Container können für kurzfristige Aufgaben über Containerimages instanziiert und wieder entfernt werden, wenn sie nicht mehr benötigt werden.

Cognitive Services-Ressourcen sind in [Microsoft Azure](https://azure.microsoft.com) verfügbar. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, um Azure-Ressourcen für diese Dienste zu erstellen und zu erkunden.

## <a name="features-and-benefits"></a>Features und Vorteile

- **Unveränderliche Infrastruktur** : Ermöglichen Sie es den DevOps-Teams, einen konsistenten und zuverlässigen Satz bekannter Systemparameter zu nutzen und sich gleichzeitig an Änderungen anzupassen. Container bieten die Flexibilität, sich innerhalb eines vorhersehbaren Ökosystems zu bewegen und Konfigurationsabweichungen zu vermeiden.
- **Kontrolle über Daten:** Wählen Sie aus, wo Ihre Daten von Cognitive Services verarbeitet werden. Dies kann wichtig sein, wenn Sie keine Daten an die Cloud senden können, aber Zugriff auf Cognitive Services-APIs benötigen. Einheitliche Unterstützung in Hybridumgebungen – für Daten, Verwaltung, Identität und Sicherheit.
- **Kontrolle über Modellaktualisierungen:** Flexibilität bei der Versionsverwaltung und Aktualisierung der in den Lösungen eingesetzten Modelle.
- **Portable Architektur:** Ermöglicht die Erstellung einer portablen Anwendungsarchitektur, die in Azure, lokal und am Edge eingesetzt werden kann. Container können direkt für [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml) oder einen [Kubernetes](https://kubernetes.io/)-Cluster mit Bereitstellung in [Azure Stack](/azure-stack/operator) bereitgestellt werden. Weitere Informationen finden Sie unter [Bereitstellen von Kubernetes in Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Hoher Durchsatz/niedrige Latenz:** Bietet Kunden die Möglichkeit, für hohe Durchsatzraten und niedrige Latenzanforderungen zu skalieren, indem Cognitive Services physisch in der Nähe ihrer Anwendungslogik und -daten ausgeführt wird. Container erzwingen keine Obergrenzen für die Transaktionen pro Sekunde (TPS) und können für die zentrale und horizontale Skalierung konfiguriert werden, um bei Bedarf die erforderlichen Hardwareressourcen bereitzustellen.
- **Skalierbarkeit:** Mit der ständig wachsenden Beliebtheit von Software für Containerisierung und Containerorchestrierung wie Kubernetes steht die Skalierbarkeit steht im Vordergrund des technologischen Fortschritts. Aufbauend auf einer skalierbaren Clusterbasis sorgt die Anwendungsentwicklung für eine entsprechende Hochverfügbarkeit.

## <a name="containers-in-azure-cognitive-services"></a>Container in Azure Cognitive Services

Azure Cognitive Services-Container bieten den folgenden Satz von Docker-Containern, von denen jeder eine Teilmenge der Funktionalität von Diensten in Azure Cognitive Services enthält:

| Dienst | Unterstützter Tarif | Container | BESCHREIBUNG |
|--|--|--|--|
| [Anomalieerkennung][ad-containers] | F0, S0 | **Anomalieerkennung** ( [Abbildung](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Die Anomalieerkennungs-API bietet Ihnen die Möglichkeit, Anomalien in Zeitreihendaten durch maschinelles Lernen zu überwachen und zu erkennen.<br>[Zugriff anfordern][request-access] |
| [Maschinelles Sehen][cv-containers] | F0, S1 | **Read** OCR ( [Bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Mit dem Read OCR-Container können Sie gedruckten und handschriftlichen Text aus Bildern und Dokumenten mit Unterstützung für die Dateiformate JPEG, PNG, BMP, PDF und TIFF extrahieren. Weitere Informationen finden Sie in der Dokumentation zur [Lese-API](./computer-vision/concept-recognizing-text.md).<br>[Zugriff anfordern][request-access] |
| [Gesichtserkennung][fa-containers] | F0, S0 | **Gesichtserkennung** | Erkennt menschliche Gesichter in Bildern und identifiziert zugehörige Attribute wie Gesichtsmerkmale (z.B. Nasen und Augen), Geschlecht, Alter und andere vom Computer vorhergesagte Gesichtsmerkmale. Zusätzlich zur Erkennung kann die Gesichtserkennung über eine Zuverlässigkeitsbewertung überprüfen, ob zwei Gesichter in einem oder verschiedenen Bildern identisch sind, oder Gesichter mit einer Datenbank vergleichen, um festzustellen, ob ein ähnliches oder identisches Gesicht bereits vorhanden ist. Sie kann auch ähnliche Gesichter in Gruppen mit gemeinsamen Gesichtsmerkmalen organisieren. |
| [Formularerkennung][fr-containers] | F0, S0 | **Formularerkennung** | Die Formularerkennung wendet Technologien des maschinellen Lernens an, um Schlüssel-Wert-Paare und Tabellen in Formularen zu identifizieren und aus diesen zu extrahieren. |
| [LUIS][lu-containers] | F0, S0 | **LUIS** ( [Image](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Lädt Ihr trainiertes oder veröffentlichtes Language Understanding-Modell (auch als LUIS-App bezeichnet) in einen Docker-Container und ermöglicht den Zugriff auf die Abfragevorhersagen von den API-Endpunkten des Containers. Sie können Abfrageprotokolle vom Container erfassen und wieder in das [LUIS-Portal](https://www.luis.ai) hochladen, um die Vorhersagegenauigkeit der App zu verbessern. |
| [Spracherkennungsdienst-API][sp-containers-stt] | F0, S0 | **Spracherkennung** ( [Abbildung](https://hub.docker.com/_/azure-cognitive-services-speechservices-speech-to-text)) | Wandelt fortlaufende Sprache in Echtzeit in Text um. |
| [Spracherkennungsdienst-API][sp-containers-cstt] | F0, S0 | **Benutzerdefinierte Spracherkennung** ( [Abbildung](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-speech-to-text)) | Wandelt fortlaufende Sprache in Echtzeit in Text um und verwendet dazu ein benutzerdefiniertes Modell. |
| [Spracherkennungsdienst-API][sp-containers-tts] | F0, S0 | **Sprachsynthese** ( [Abbildung](https://hub.docker.com/_/azure-cognitive-services-speechservices-text-to-speech)) | Konvertiert Text in natürlich klingende Sprache. |
| [Spracherkennungsdienst-API][sp-containers-ctts] | F0, S0 | **Benutzerdefinierte Sprachsynthese** ( [Abbildung](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-text-to-speech)) | Konvertiert Text in natürlich klingende Sprache und verwendet dazu ein benutzerdefiniertes Modell. |
| [Spracherkennungsdienst-API][sp-containers-ntts] | F0, S0 | **Neuronale Sprachsynthese** ( [Abbildung](https://hub.docker.com/_/azure-cognitive-services-speechservices-neural-text-to-speech)) | Konvertiert Text mithilfe von Deep Neural Network-Technologie in natürlich klingende Sprache, die eine natürlichere synthetische Sprache ermöglicht. |
| [Textanalyse][ta-containers-keyphrase] | F0, S | **Schlüsselbegriffserkennung** ( [Bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extrahiert die Schlüsselbegriffe, um die wichtigsten Punkte zu ermitteln. Wenn der eingegebene Text beispielsweise „Das Essen war köstlich, und es gab hervorragendes Personal“ lautet, gibt die API die Kernpunkte „Essen“ und „hervorragendes Personal“ zurück. |
| [Textanalyse][ta-containers-language] | F0, S | **Sprachenerkennung** ( [Bild](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Erkennt die Sprache von Eingabetexten für bis zu 120 Sprachen und meldet einen einzigen Sprachcode für jedes Dokument, das auf Anforderung gesendet wird. Der Sprachcode ist mit einem Wert kombiniert, der die Stärke der Bewertung angibt. |
| [Textanalyse][ta-containers-sentiment] | F0, S | **Standpunktanalyse v3** ( [Image](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analysiert unformatierten Text auf Hinweise auf positive oder negative Stimmungen. Von dieser Version der Standpunktanalyse werden für jedes Dokument und jeden darin enthaltenen Satz Stimmungsbezeichnungen (beispielsweise *Positiv* oder *Negativ* ) zurückgegeben. |
| [Textanalyse][ta-containers-health] | F0, S | **Text Analytics for Health** | Extraktion und Bezeichnung medizinischer Informationen aus unstrukturiertem klinischem Text. |
| [Räumliche Analyse][spa-containers] | S0 | **Räumliche Analyse** | Analysiert in Echtzeit gestreamte Videodaten, um räumliche Bezüge zwischen Personen, ihre Bewegungen und ihre Interaktionen mit Objekten in der physischen Umgebung zu verstehen. |

<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Darüber hinaus werden einige Container für Ressourcenschlüssel im [**All-In-One-Angebot**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) von Cognitive Services unterstützt. Sie können eine einzelne All-In-One-Ressource von Cognitive Services erstellen und denselben Abrechnungsschlüssel für alle unterstützten Dienste verwenden:

* Maschinelles Sehen
* Gesicht
* LUIS
* Textanalyse

## <a name="container-availability-in-azure-cognitive-services"></a>Containerverfügbarkeit in Azure Cognitive Services

Azure Cognitive Services-Container sind über Ihr Azure-Abonnement öffentlich zugänglich, und Docker-Containerimages können entweder aus der Microsoft Container Registry oder dem Docker-Hub abgerufen werden. Verwenden Sie den Befehl [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage aus dem entsprechenden Repository herunterzuladen.

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen die folgenden Voraussetzungen erfüllen, bevor Sie die Container der Azure Cognitive Services verwenden können:

**Docker-Engine:** Die Docker-Engine muss lokal installiert sein. Docker stellt Pakete zur Konfiguration der Docker-Umgebung unter [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) und [Windows](https://docs.docker.com/docker-for-windows/) zur Verfügung. Unter Windows muss Docker für die Unterstützung von Linux-Containern konfiguriert werden. Docker-Container können auch direkt für [Azure Kubernetes Service](../aks/index.yml) oder [Azure Container Instances](../container-instances/index.yml) bereitgestellt werden.

Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können.

**Kenntnisse von Microsoft Container Registry und Docker:** Sie sollten über Grundkenntnisse der Konzepte von Microsoft Container Registry und Docker verfügen, einschließlich Registrierungen, Repositorys, Container und Containerimages, und die grundlegenden `docker`-Befehle kennen.

Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).

Einzelne Container können auch ihre eigenen Anforderungen haben, wie z.B. Anforderungen an die Server- und Speicherzuordnung.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Containeranleitungen](containers/container-reuse-recipe.md), die Sie mit Cognitive Services verwenden können.

Installieren und erkunden Sie die Funktionalität der Container in Azure Cognitive Services:

* [Container für die Anomalieerkennung][ad-containers]
* [Container für maschinelles Sehen][cv-containers]
* [Container für die Gesichtserkennung][fa-containers]
* [Container für die Formularerkennung][fr-containers]
* [Container für Language Understanding (LUIS)][lu-containers]
* [Container für die Speech Services-API][sp-containers]
* [Container für die Textanalyse][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u