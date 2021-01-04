---
title: Docker-Pullbefehl für den „Textanalyse für Gesundheit“-Container
titleSuffix: Azure Cognitive Services
description: Docker-Pullbefehl für „Textanalyse für Gesundheit“-Container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965162"
---
Füllen Sie das [Formular zum Anfordern von Cognitive Services](https://aka.ms/csgate) aus, und übermitteln Sie es, um Zugriff auf Textanalyse for Health (Public Preview) anzufordern.  Diese Anwendung gilt sowohl für den Container als auch für die öffentliche Vorschau der gehosteten Web-API.
Im Formular müssen Sie Informationen über Sie selbst, Ihr Unternehmen und das Benutzerszenario eintragen, für das Sie den Container verwenden möchten. Nach der Übermittlung des Formulars überprüft das Azure Cognitive Services-Team das Formular, um sicherzustellen, dass Sie die Kriterien für den Zugriff auf die private Containerregistrierung erfüllen.

> [!IMPORTANT]
> * Im Formular müssen Sie eine E-Mail-Adresse angeben, die einer Azure-Abonnement-ID zugeordnet ist.
> * Die Azure-Ressource, die Sie zum Ausführen des Containers verwenden, muss mit der genehmigten Azure-Abonnement-ID erstellt worden sein. 
> * Überprüfen Sie Ihre E-Mail-Adresse (Posteingang und Junk-Ordner) auf Updates zum Status Ihrer Anwendung von Microsoft.

Nachdem die Genehmigung erfolgt ist, erhalten Sie eine E-Mail mit den Anmeldeinformationen für den Zugriff auf die private Containerregistrierung.  Stellen Sie mit dem Docker-Anmeldebefehl mit Anmeldeinformationen, die in ihrer Onboarding-E-Mail bereitgestellt werden, eine Verbindung mit unserer privaten Containerregistrierung für Cognitive Services-Container her.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Laden Sie mit dem Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) dieses Containerimage aus unserer privaten Containerregistrierung herunter.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
