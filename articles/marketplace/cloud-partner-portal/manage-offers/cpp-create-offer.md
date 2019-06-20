---
title: Erstellen von Marketplace-Angeboten | Azure Marketplace
description: Erstellen von Angeboten in Azure Marketplace und AppSource über das Cloud-Partnerportal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pabutler
ms.openlocfilehash: cc0d8e68578500d4fa5f1b167790d60c8f7c5067
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942157"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Erstellen von Azure Marketplace- und AppSource-Angeboten

Ein wesentlicher Zweck des Cloud-Partnerportals besteht darin, es Herausgebern zu ermöglichen, Angebote für Microsoft Azure Marketplace und AppSource zu erstellen (und dann zu veröffentlichen).  Dieser Vorgang beginnt immer mit der Auswahl des gewünschten Angebotstyps aus dem Menü [Neues Angebot](../portal-tour/cpp-new-offer-menu.md).  Als Reaktion darauf wird die entsprechende Seite **Neues Angebot** für diesen Angebotstyp angezeigt.  Die folgende Abbildung zeigt beispielsweise die Standardseite **Neues Angebot** für einen Azure-Anwendungstyp.

![Standardseite „Neues Angebot“](./media/new-offer-page.png)

In der horizontalen Menüleiste, die oben auf dieser Seite angezeigt wird, stehen zwei Registerkarten zur Auswahl: 
- Registerkarte **Editor**: Ermöglicht die Eingabe von Informationen und das Hochladen von Objekten für die neue Angebotsinstanz.  Diese Registerkarte wird standardmäßig angezeigt.
- Registerkarte **Status**: Gibt den Veröffentlichungsstatus an und listet alle Validierungs- und Prüfungsprobleme auf. 

Wenn Sie ein Angebot erstellen, verwenden Sie die Registerkarte **Editor**, um Informationen über dieses Angebot einzugeben. 

## <a name="editing-operations"></a>Bearbeitungsvorgänge

Die horizontale Symbolleiste, die sich über dem Dateneingabebereich befindet, zeigt die folgenden Schaltflächen an:

|   Taste    |   Zweck                                                          |
|   ------    |  --------                                                          |
| **Speichern**    | Speichert alle kürzlich vorgenommenen Änderungen am Dateneintrag.  Sie müssen die Änderungen manuell speichern, bevor Sie die Seite verlassen, da Ihre Änderungen sonst verloren gehen. | 
| **Verwerfen** | Verwirft die letzten Änderungen am Dateneintrag (seit dem letzten Speichern).             |
| **Vergleichen** | Vergleicht den Stand des aktuellen Angebots mit dem veröffentlichten Angebot.  Wird nur aktiviert, nachdem ein Angebot erfolgreich veröffentlicht wurde.  |
| **Veröffentlichen** | Startet den Veröffentlichungsvorgang für dieses Angebot.                       |
| **Löschen**  | Löscht dieses Angebot, nachdem es erstellt, aber bevor es veröffentlicht wurde. |
|   |   |


## <a name="editing-tabs"></a>Registerkarten für die Bearbeitung

Wenn Sie ein Angebot erstellen, geben Sie erforderliche und optionale Daten in jeder Registerkarte in der linken vertikalen Spalte der Seite **Neues Angebot** ein.  Zur Datenerfassung werden Standardsteuerelemente der Benutzeroberfläche (z. B. Textfelder, Dropdownmenüs und Kontrollkästchen) angezeigt.  Die genaue Auswahl der Bearbeitungsregisterkarten hängt zwar vom Angebotstyp ab, in der folgenden Tabelle sind jedoch einige der häufigsten Registerkarten aufgelistet.

|      Registerkartenname       |   Zweck                                                            |
|      --------       |   -------                                                            |
| **Angebotseinstellungen**  | Erfasst Informationen zum Angebot und zur Identität des Herausgebers.                    |
| **SKUs**            | Definiert die technischen und geschäftlichen Merkmale für jede SKU-Version (Stock Keeping Unit) Ihres Angebots. |
| **Testversion**      | Definiert für diejenigen Typen, die diese optionale Funktion unterstützen, eine Demo für Ihr Angebot.  Weitere Informationen finden Sie unter [Was ist die Testversion?](../test-drive/what-is-test-drive.md)  |
| **Marketplace** oder **Storefront** | Erfasst die Texte, Dokumente und Bilder zur Listung des Angebots auf dem Marketplace. |
| **Unterstützung**         | Erfasst Kontaktinformationen für Kunden-, Entwicklungs- und Onlinesupport  |
|  |  |

Der Inhalt der ähnlich benannten Registerkarten kann je nach Angebotstyp unterschiedlich sein.  Angebotsspezifische Details zu diesen Registerkarten finden Sie im Abschnitt „Angebot erstellen“ für jeden Angebotstyp.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie ein Angebot erstellt und gespeichert haben und bevor oder nachdem Sie es veröffentlicht haben, können Sie [den Status des Angebots einsehen](./cpp-view-status-offer.md).
