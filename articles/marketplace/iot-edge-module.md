---
title: Azure IoT Edge-Module
description: Das Angebot für IoT Edge-Module im Azure Marketplace für Herausgeber von Apps und Diensten.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/22/2018
ms.author: qianw211
ms.openlocfilehash: c734461f1a57c100b4a00ab5c309659f8b0e903a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964020"
---
# <a name="iot-edge-modules"></a>IoT Edge-Module

Die [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/)-Plattform wird von Azure Cloud unterstützt.  Diese Plattform ermöglicht es Benutzern, Cloud-Workloads zum direkten Ausführen auf IoT-Geräten bereitzustellen.  Mit einem IoT Edge-Modul können Workloads offline ausgeführt und Datenanalysen lokal durchgeführt werden. Mit diesem Angebot können Sie Bandbreite sparen sowie lokale und vertrauliche Daten schützen. Zudem bietet es Antwortzeiten mit geringer Latenz.  Von diesen vorgefertigten Workloads können Sie ab sofort profitieren. Bislang sind nur wenige Erstanbieterlösungen von Microsoft verfügbar.  Und so mussten Sie bisher Zeit und Ressourcen in die Erstellung eigener benutzerdefinierter IoT-Lösungen investieren.

Dank der Einführung der [IoT Edge-Module im Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1) gibt es ab sofort einen zentralen Ort, an dem Herausgeber ihre Lösungen für die IoT-Zielgruppe anbieten und verkaufen können. Letztlich können IoT-Entwickler hier Funktionen zur Beschleunigung ihrer Lösungsentwicklung finden und erwerben.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Die wichtigsten Vorteile von IoT Edge-Modulen im Azure Marketplace:

| **Für Herausgeber**    | **Für Kunden (IoT-Entwickler)**  |
| :------------------- | :-------------------|
| Sie erreichen Millionen von Entwicklern, die IoT Edge-Lösungen erstellen und bereitstellen möchten.  | Erstellen Sie eine IoT Edge-Lösung mit der Gewissheit, sichere und getestete Komponenten zu verwenden. |
| Sie müssen Ihre Lösung nur einmal veröffentlichen. Diese kann dann auf jeder IoT Edge-Hardware ausgeführt werden, die Container unterstützt. | Verkürzen Sie die Markteinführungszeit, indem Sie hier nach IoT Edge-Modulen von Erst- und Drittanbietern für spezifische Anforderungen suchen und diese bereitstellen. |
| Profitieren Sie von flexiblen Abrechnungsoptionen. <ul> <li> Kostenlos und BYOL (Bring Your Own License) </li> </ul> | Kaufen Sie mit den Abrechnungsmodellen Ihrer Wahl. <ul> <li> Kostenlos und BYOL (Bring Your Own License) </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Was ist ein IoT Edge-Modul?

Azure IoT Edge ermöglicht Ihnen die Bereitstellung und Verwaltung von Geschäftslogik im Edge in Form von Modulen. Azure IoT Edge-Module sind die kleinste von IoT Edge verwaltete Recheneinheit. Sie können Microsoft-Dienste (wie Azure Stream Analytics), Dienste von Drittanbietern oder Ihren eigenen lösungsspezifischen Code enthalten. Weitere Informationen zu IoT Edge-Modulen finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Was ist der Unterschied zwischen Container-Angebotstyp und einem IoT Edge-Modul-Angebotstyp?**

Beim IoT Edge-Modul-Angebotstyp handelt es sich um einen speziellen Containertyp, der auf einem IoT Edge-Gerät ausgeführt wird. Er wird mit Standardkonfigurationseinstellungen zur Ausführung im IoT Edge-Kontext ausgeliefert und verwendet optional das IoT Edge-Modul SDK zur Integration in die IoT Edge-Laufzeit.

## <a name="publishing-your-iot-edge-module"></a>Veröffentlichen eines IoT Edge-Moduls

**Auswählen der passenden Storefront**

IoT Edge-Module werden nur im Azure Marketplace veröffentlicht, in AppSource sind sie nicht verfügbar.  Weitere Informationen zu den Unterschieden und Zielgruppen der jeweiligen Storefronts finden Sie unter [Bestimmung Ihrer Veröffentlichungsoption](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Abrechnungsoptionen**

Derzeit unterstützt der Marketplace **kostenlose** und an eigene Lizenzen gebundene Abrechnungsoptionen (**BYOL, Bring Your Own License**) für IoT Edge-Module.
 
**Veröffentlichungsoptionen**

Für IoT Edge-Module sollten Sie jedenfalls die Veröffentlichungsoption **Transact** auswählen.  Weitere Informationen zu Veröffentlichungsoptionen finden Sie unter [Auswählen einer Veröffentlichungsoption](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).  

## <a name="eligibility-criteria"></a>Wer kann IoT Edge-Module erwerben?

Für IoT Edge-Modul-Angebote gelten die Bestimmungen der Microsoft Azure Marketplace-Vereinbarungen und -Richtlinien.  Darüber hinaus müssen für IoT Edge-Module zusätzliche Voraussetzungen und technische Anforderungen erfüllt sein.  

**Voraussetzungen**

Wenn Sie ein IoT Edge-Modul im Azure Marketplace veröffentlichen möchten, müssen Sie folgende Voraussetzungen erfüllen:

- Sie müssen über Zugriff auf das Cloud-Partnerportal (CPP) verfügen. Weitere Informationen hierzu finden Sie unter [Veröffentlichungsleitfaden für Azure Marketplace und AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Sie müssen Ihr IoT Edge-Modul in einer Azure Container Registry hosten. 
- Sie müssen unter anderem folgende Metadaten Ihres IoT Edge-Moduls bereithalten: 
    - Titel
    - Beschreibung (im HTML-Format)
    - Logo (PNG-Format und feste Bildgrößen wie 40 x 40 px, 90 x 90 px, 115 x 115 px, 255 x 115 px)
    - Nutzungsbedingungen und Datenschutzrichtlinie
    - Standardmodulkonfiguration (Route, gewünschte Eigenschaften von Modulzwillingen, createOptions, Umgebungsvariable)
    - Dokumentation
    - Supportkontakte

**Technische Anforderungen**

Die wichtigsten technischen Voraussetzungen für die Zertifizierung und Veröffentlichung eines IoT Edge-Moduls im Azure Marketplace finden Sie unter [Vorbereiten der technischen Ressourcen für das IoT Edge-Modul](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Dokumentation und Ressourcen

[Create an IoT Edge module offer (Erstellen eines IoT Edge-Modul-Angebots)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer): Beschreibung, wie mit dem Cloud Partner Portal ein neues IoT Edge-Modul-Angebot erstellt wird

## <a name="next-steps"></a>Nächste Schritte

Falls Sie dies noch nicht getan haben,

- Registrieren Sie sich beim [Microsoft Partner Network](https://partner.microsoft.com/membership).
- Erstellen Sie ein [Microsoft-Konto](https://account.microsoft.com/account/) (für Azure Marketplace-Transaktionsangebote erforderlich; für andere Benutzer empfohlen).
- Übermitteln Sie das [Marketplace-Registrierungsformular](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv). Weitere Informationen finden Sie unter [Erstellen eines Partner Center-Kontos](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

Wenn Sie registriert sind und ein neues Angebot erstellen oder an einem vorhandenen arbeiten,

- Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an, um Ihr Angebot zu erstellen oder zu vervollständigen.
- Unter [Übersicht über das Veröffentlichen von IoT Edge-Modulangeboten](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) finden Sie Informationen zum Veröffentlichen eines IoT Edge-Modulangebots.
