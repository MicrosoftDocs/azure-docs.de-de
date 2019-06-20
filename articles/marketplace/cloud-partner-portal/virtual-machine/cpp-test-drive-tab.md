---
title: Registerkarte „Testversion“ für VMs im Cloud-Partnerportal für Azure Marketplace
description: Enthält eine Beschreibung der Registerkarte „Testversion“, die Sie beim Erstellen eines VM-Angebots für den Azure Marketplace verwenden.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 31c7968d0d96a44ff166444f73807e0ccb5dc583
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938005"
---
# <a name="virtual-machine-test-drive-tab"></a>Registerkarte „Testversion“ für virtuelle Computer

Auf der Registerkarte **Testversion** der Seite **Neues Angebot** können Sie für Ihre potenziellen Kunden eine praktische, interaktive Demonstration der wichtigen Features und Vorteile Ihres Produkts in einem standardisierten Szenario bereitstellen.  „Testversion“ ist ein optionales Feature für Angebotstypen, die Testversionen unterstützen.  Für die richtige Implementierung von „Testversion“ werden unterstützende Ressourcen benötigt.  Weitere Informationen finden Sie im Artikel [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/) (Azure Marketplace-Testversion).  <!--TD: Replace with migrated version of Test Drive article! -->

Klicken Sie zum Aktivieren dieses Features auf der Registerkarte **Testversion** unter **Enable a Test Drive** (Testversion aktivieren) auf **Ja**.  Auf der Registerkarte **Testversion** werden die Felder angezeigt, die bearbeitet werden können.  Mit einem an den Feldnamen angefügten Sternchen (*) wird angegeben, dass eine Eingabe erforderlich ist.

![Registerkarte „Testversion“ des Formulars „Neues Angebot“ für virtuelle Computer](./media/publishvm_007.png)


## <a name="field-values"></a>Feldwerte

In der folgenden Tabelle werden Zweck und Inhalt dieser Felder beschrieben.  Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.


|    Feld                  |       BESCHREIBUNG                                                            |
|  ---------                |     ---------------                                                          |
|  *Details*   |  |
| **Beschreibung\***           | Geben Sie eine Übersicht über Ihr Szenario für die Testversion an. Dieser Text wird dem Benutzer während der Bereitstellung der Testversion angezeigt. Dieses Feld unterstützt das einfache HTML-Format, das Sie nutzen können, wenn Sie formatierte Inhalte bereitstellen möchten.  |
| **Benutzerhandbuch\***           | Laden Sie ein detailliertes Benutzerhandbuch (PDF-Datei) hoch, in dem für Benutzer der Testversion Informationen zur Verwendung Ihrer Lösung bereitgestellt werden.  |
| **Test Drive Demo Video** (Demovideo für Testversion) | Laden Sie ein Video hoch, in dem Ihre Lösung vorgestellt wird.  Wenn Sie diese Option auswählen, müssen Sie einen Namen, die URL zum Video (unter YouTube oder Vimeo gehostet) und eine Miniaturansicht (533x324 Pixel) für das Video bereitstellen. |
| *Technische Konfiguration* |  |
| **Instanzen\***             | Geben Sie die Regionsverfügbarkeit und die relative Verfügbarkeit der VM-Instanz an. (Klicken Sie auf das Infosymbol, um weitere Informationen zu erhalten.)  <br/>Für Testversionssitzungen, die ggf. parallel ausgeführt werden, sollte die Kontingentgrenze für Ihr Abonnement nicht überschritten werden.  Dies wird wie folgt berechnet: [Anzahl von ausgewählten Regionen] x [Instanzen des Typs „Heiß“] + [Anzahl von ausgewählten Regionen] x [Instanzen des Typs „Warm“] + [Anzahl von ausgewählten Regionen] x [Instanzen des Typs „Kalt“] |
| **Dauer der Testversion\***   | Gibt die maximale Sitzungsdauer in Stunden an. Nach Ablauf dieses Zeitraums wird die Testversionssitzung automatisch beendet.  |
|**ARM-Vorlage für Testversion\***| Laden Sie die Azure Resource Manager-Vorlage hoch, die dieser Testversion zugeordnet ist. Weitere Informationen finden Sie unter [Transforming Virtual Machine Deployment Template for Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive) (Transformieren der VM-Bereitstellungsvorlage für die Testversion). |
| **Zugriffsinformationen\***    | Informationen zum Azure Resource Manager-Zugriff und zur Anmeldung an der Testversion als einfacher Text oder im einfachen HTML-Format. |
| *Abonnementdetails für Bereitstellung der Testversion* |  |
| **Azure-Abonnement-ID\*** | Sie können die ID abrufen, indem Sie sich am [Microsoft Azure-Portal](https://ms.portal.azure.com) anmelden und in der Menüleiste auf der linken Seite auf **Abonnements** klicken. (Beispiel: „a83645ac-1234-5ab6-6789-1h234g764ghty“) Dieser Bezeichner sollte eine GUID im Format `a83645ac-1234-5ab6-6789-1h234g764ghty` sein.|
| **Azure AD-Mandanten-ID\***    | Die ID des Azure Active Directory-Mandanten.  Sie können die ID abrufen, indem Sie sich am [Microsoft Azure-Portal](https://ms.portal.azure.com) anmelden und in der Menüleiste auf der linken Seite auf **Azure Active Directory** und dann in der mittleren Menüleiste auf **Eigenschaften** klicken und die **Verzeichnis-ID** aus dem Formular kopieren.  Bei diesem Bezeichner sollte es sich ebenfalls um eine GUID handeln.  Wenn keine Angabe vorhanden ist, müssen Sie eine Mandanten-ID für Ihre Organisation erstellen. |
| **Azure AD-App-ID\***       | Dies ist der Bezeichner für Ihre registrierte Azure-VM-Lösung.  |
| **Azure AD-App-Schlüssel\***      | Der Authentifizierungsschlüssel für Ihre registrierte Lösung. |
|   |   |


## <a name="next-steps"></a>Nächste Schritte

Auf der nächsten Registerkarte [Marketplace](./cpp-marketplace-tab.md) geben Sie Marketinginformationen und rechtliche Informationen zu Ihrer Lösung an.
