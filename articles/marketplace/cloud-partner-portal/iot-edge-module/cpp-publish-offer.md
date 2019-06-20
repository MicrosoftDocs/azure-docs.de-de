---
title: Veröffentlichen eines Azure IoT Edge-Modulangebots | Azure Marketplace
description: Hier erfahren Sie, wie Sie ein IoT Edge-Modulangebot veröffentlichen.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: c853bd3bad9f02f6824c26fb5d18e9e59d921fe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942043"
---
# <a name="publish-iot-edge-module-offer"></a>Veröffentlichen eines IoT Edge-Modulangebots

 Nachdem Sie die Informationen auf der Seite **Neues Angebot** angegeben haben, um ein neues Angebot zu erstellen, können Sie das Angebot veröffentlichen. Klicken Sie auf **Veröffentlichen**, um den Veröffentlichungsprozess zu starten.

Das folgende Diagramm zeigt die wichtigsten Schritte des Veröffentlichungsprozesses, die ausgeführt werden müssen, um ein Angebot live zu schalten.

![Veröffentlichungsschritte für ein IoT Edge-Modulangebot](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Ausführliche Beschreibung der Veröffentlichungsschritte

Die folgende Tabelle enthält eine Beschreibung der einzelnen Veröffentlichungsschritte sowie die voraussichtliche maximale Dauer des jeweiligen Schritts.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Veröffentlichungsschritt**           | **Time**    | **Beschreibung**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Überprüfen der Voraussetzungen         | 15 Min.   | Die Angebotsinformationen und -einstellungen werden überprüft.                        |
| Zertifizierung                  | Zwei Wochen | Das Angebot wird vom Azure-Zertifizierungsteam analysiert. Dabei wird das Angebot auf Viren, Schadsoftware, Sicherheitskonformität und Sicherheitsprobleme untersucht. Darüber hinaus wird überprüft, ob das IoT Edge-Modulangebot alle Berechtigungskriterien erfüllt. (Weitere Informationen finden Sie unter [IoT Edge module publishing prerequisites](./cpp-prerequisites.md) (Voraussetzungen für die Veröffentlichung von IoT Edge-Modulen) sowie unter [Vorbereiten der technischen Ressourcen für das IoT Edge-Modul](./cpp-create-technical-assets.md).) Sollte ein Problem gefunden werden, erhalten Sie entsprechendes Feedback. |
| Verpackung | 1 Stunde  | Die technischen Ressourcen des Angebots werden für die Verwendung durch die Kunden gepackt, und die Leadsysteme werden konfiguriert und eingerichtet. |
|  Bestätigung durch den Herausgeber             |  -        | Letzte Überprüfung und Bestätigung durch den Herausgeber, bevor das Angebot live geschaltet wird. Sie können Ihr Angebot in den ausgewählten Abonnements (in den Schritten für die Angebotsinformationen) bereitstellen, um sicherzugehen, dass es alle Ihre Anforderungen erfüllt.  Klicken Sie auf **Live schalten**, damit der nächste Schritt für Ihr Angebot ausgeführt werden kann. |
| Verpackung                 | 1 Stunde | Das fertige Angebot wird in den Produktionssystemen und Regionen des Marketplace repliziert. | 
| Live                           | 4 Tage |Das Angebot wird veröffentlicht, in den erforderlichen Regionen repliziert und für die Öffentlichkeit verfügbar gemacht. |

Es kann bis zu zehn Werktage dauern, bis der Veröffentlichungsprozess abgeschlossen ist und das Angebot veröffentlicht wird. Nach Abschluss des Veröffentlichungsprozesses wird Ihr IoT Edge-Modulangebot im [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) gelistet.

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren eines vorhandenen IoT Edge-Modulangebots](./cpp-update-existing-offer.md)
