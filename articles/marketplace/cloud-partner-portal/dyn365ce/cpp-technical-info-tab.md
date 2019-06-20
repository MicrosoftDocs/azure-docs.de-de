---
title: Dynamics 365 for Customer Engagement – Registerkarte „Technische Informationen“ | Azure Marketplace
description: Hier erfahren Sie, wie Sie die technischen Informationen für eine Dynamics 365 for Customer Engagement-Anwendung auf AppSource Marketplace angeben.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: df7f3be5a92a183176da7851ce1943793b8b57d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942397"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 for Customer Engagement – Registerkarte „Technische Informationen“

Auf der Registerkarte **Technische Informationen** der Seite **Neues Angebot** können Sie ausführliche Informationen zu Ihrer Dynamics 365 for Customer Engagement-Anwendung einschließlich CRM-Paket und Marketinglogoressourcen angeben.  Diese Registerkarte ist in vier Abschnitte unterteilt: **Anwendungsinfo**, **CRM-Paket**, **Verfügbarkeit von CRM-Paketen** und **Marketingartefakte**. Mit einem an den Feldnamen angefügten Sternchen (*) wird angegeben, dass eine Eingabe erforderlich ist. 


## <a name="application-info-section"></a>Abschnitt „Anwendungsinfo“

In diesem Abschnitt geben Sie Details über Ihre Dynamics 365-Anwendung an.

![Abschnitt „Anwendungsinfo“ der Registerkarte „Technische Informationen“](./media/dynce-technical-info-tab1.png)

Diese Felder sind in der folgenden Tabelle beschrieben. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|      Feld                    |    BESCHREIBUNG                  |
|    ---------                  |  ---------------                |
|   Basislizenzmodell\*          |  Das Lizenzmodell bestimmt, wie Kunden im Dynamics 365 Admin Center Ihrer Anwendung zugewiesen werden. Die **Ressourcenlizenzierung** ist instanzbasiert, wohingegen **Benutzerlizenzen** zugewiesen werden, je eine pro Mandant.  |
|  Ausgehender S2S- und CRM Secure Store-Zugriff\* |  Ermöglicht die Konfiguration von CRM Secure Store- oder ausgehendem Server-zu-Server-Zugriff (S2S). *Dieses Feature erfordert eine spezielle Berücksichtigung durch das Dynamics 365-Team während der Zertifizierungsphase.* Microsoft kontaktiert Sie, um zusätzliche Schritte ausführen, die dieses Feature unterstützen.  |
| Abonnieren von CRM-Lebenszyklusereignissen\* | Für die Integration in Dynamics 365-Lebenszyklusereignisse müssen Sie einen dedizierten Dienst bieten, der über eine spezielle Vereinbarung bei Microsoft registriert ist. *Dieses Feature erfordert eine spezielle Berücksichtigung durch das Dynamics 365-Team während der Zertifizierungsphase.* Sie werden kontaktiert, um zusätzliche Schritte ausführen, die diese Fähigkeit unterstützen.  |
| Anwendungskonfigurations-URL | URL der Webseite, die dem Benutzer ermöglicht, die Anwendung zu konfigurieren |
| Gilt für Dynamics 365-Produkte  | Wählen Sie die Dynamics 365-Produkte aus, für die dieses Angebot gilt. Dieses Angebot wird unter ausgewählten Produkten in AppSource angezeigt.  |
| Ausschließliche Marketingänderung         | Wenn Sie diese Option auf „Ja“ setzen, bedeutet dies, dass bei dem vorhandenen Angebot nur Marketing-/beschreibende Änderungen vorgenommen wurden.  Mit solchen Änderungen kann das Angebot Zertifizierungs- und Bereitstellungsphasen umgehen.  |
|  |  |


## <a name="crm-package-section"></a>Abschnitt „CRM-Paket“

In diesem Abschnitt geben Sie Details über Ihre AppSource-Paketdatei an.  Diese Informationen werden von den Dynamics 365-Überprüfungs- und Zertifizierungsteams verwendet.

![Abschnitt „CRM-Paket“ der Registerkarte „Technische Informationen“](./media/dynce-technical-info-tab2.png)

Diese Felder sind in der folgenden Tabelle beschrieben.  Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|      Feld                    |    BESCHREIBUNG                  |
|    ---------                  |  ---------------                |
|  Dateiname des Pakets\*     |  Dateiname des Pakets (ZIP).  Dieser Name ist *nicht* öffentlich und wird intern vom Dynamics 365-Zertifizierungsteam verwendet.  |
|  URL des Paketspeicherorts\*      |  URL eines Azure Storage-Kontos, das die hochgeladene Paketdatei enthält. Diese URL sollte einen schreibgeschützten SAS-Schlüssel enthalten, damit unser Team Ihr Paket zur Überprüfung auswählen kann.  |
| Mehrere CRM-Pakete\*     | Wählen Sie NUR dann „Ja“ aus, wenn Sie mehrere Versionen von CRM mit verschiedenen Paketen unterstützen.  Jede Version verfügt über eine entsprechende Paketdatei ein, die Sie einzeln erstellen müssen.  |
| Szenario und Anwendungsfallressource\*   | Ermöglicht das Hochladen eines funktionalen Spezifikationsdokuments für Ihre Anwendung zur Verwendung durch das Dynamics 365-Überprüfungsteam.  Das bevorzugte Format für diese Spezifikation ist die [E2E-Szenariovorlage](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>Abschnitt „Verfügbarkeit von CRM-Paketen“

Wählen Sie in diesem Abschnitt aus, in welchen geografischen Regionen Ihre Anwendung für Kunden verfügbar sein wird.  Die Bereitstellung in den folgenden unabhängigen Regionen *erfordert eine spezielle Berechtigung und Überprüfung* während des Zertifizierungsprozesses: [Deutschland](https://docs.microsoft.com/azure/germany/), [Cloud for US Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) und TIP.


## <a name="marketing-artifacts-section"></a>Abschnitt „Marketingartefakte“

In diesem Abschnitt müssen Sie ein Anwendungslogo hochladen, das zur Darstellung Ihres Pakets im AppSource Marketplace verwendet wird.  Das Logobild muss im PNG-Format und 255 x 115 Pixel groß sein.


## <a name="next-steps"></a>Nächste Schritte

Sie sollten die [Registerkarte „Testversion“](./cpp-testdrive-tab.md) ausfüllen, um eine Demo der Anwendung anzubieten.
