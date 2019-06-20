---
title: Konfigurieren von SKUs für ein Azure-Anwendungsangebot | Azure Marketplace
description: Vorgehensweise zum Konfigurieren der SKUs für eine verwaltete Azure-Anwendung und eine Azure-Lösungsvorlage.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: ef4ea2419c64d0376023ea5d291460df48a51c63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943441"
---
# <a name="azure-application-skus-tab"></a>Registerkarte „SKUs“ für Azure-Anwendungen

In diesem Artikel ist beschrieben, wie Sie die Registerkarte „SKUs“ verwenden, um SKUs für Ihr Azure-Anwendung zu erstellen. 

> [!IMPORTANT]
> Die Schritte zum Konfigurieren einer SKU sind für ein Angebot einer verwalteten Anwendung und ein Angebot einer Lösungsvorlage unterschiedlich. Diese Unterschiede sind in diesem Artikel erläutert. 

## <a name="configure-azure-application-skus"></a>Konfigurieren der SKUs einer Azure-Anwendung

### <a name="create-a-new-sku"></a>Erstellen einer neuen SKU

Führen Sie die folgenden Schritte aus, um eine neue SKU zu erstellen:

1. Wählen Sie die Registerkarte **SKUs** aus.
2. Wählen Sie unter „SKUs“ die Option **+ Neue SKU** aus.

    ![Eingabeaufforderung für neue SKU](./media/azureapp-plus-sku.png)

3. Geben Sie im Popupfenster „Neue SKU“ eine **SKU-ID** ein. Diese ID darf höchstens 50 Zeichen lang sein und nur klein geschriebene alphanumerische Zeichen, Bindestriche und Unterstriche enthalten. Die SKU-ID darf nicht mit einem Gedankenstrich enden.
4. Die SKU-ID ist in Produkt-URLs, Resource Manager-Vorlagen (sofern zutreffend) und Abrechnungsberichten sichtbar. Sie können diese ID nicht mehr ändern, nachdem das Angebot veröffentlicht wurde.

### <a name="sku-details-for-a-solution-template"></a>SKU-Details für eine Lösungsvorlage

In der nächsten Bildschirmaufnahme ist das „SKU-Details“-Formular für eine Lösungsvorlage gezeigt.

![„SKU-Details“-Formular für eine Lösungsvorlage](./media/azureapp-sku-details-solutiontemplate.png)

Geben Sie die folgenden SKU-Werte an.  Die mit einem Sternchen gekennzeichneten Felder müssen ausgefüllt werden.

|    Feld         |       BESCHREIBUNG                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | Ein Titel für die SKU. Dieser Titel wird im Katalog für dieses Element angezeigt.   |
| **Zusammenfassung\***    | Eine kurze Beschreibung der SKU. (Die maximale Länge beträgt 100 Zeichen.)  |
| **Beschreibung\*** | Eine detaillierte Beschreibung der SKU. Einfaches HTML wird unterstützt.                 | 
| **SKU-Typ\***   | Typ der Azure-Anwendungslösung, wählen Sie ***Lösungsvorlage** für dieses Szenario aus. |
| **Cloudverfügbarkeit\*** | Der Standort der SKU. Der Standardwert ist **Azure, öffentlich**.  <b/>   **Öffentliches Azure**: Die App wird für Kunden in allen öffentlichen Azure-Regionen bereitgestellt, die über Marketplace-Integration verfügen.  <b/>   **Azure Government Cloud**: Die App wird in der Azure Government Cloud bereitgestellt. Den Herausgebern wird von Microsoft empfohlen, vor der Veröffentlichung in [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) zu testen und zu überprüfen, ob ihre Lösung in dieser Umgebung erwartungsgemäß funktioniert. Fordern Sie zum Bereitstellen und Testen ein [Testkonto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/) an.  |
| **Ist dies eine private SKU?\*** | Wählen Sie **Ja** aus, wenn diese SKU nur für eine ausgewählte Gruppe von Kunden verfügbar ist. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government ist eine Government-Community-Cloud mit kontrolliertem Zugriff für Kunden der US-Behörden auf Bundes-, Bundesstaats-, Gemeinde- und Stammesgebietsebene UND für Partner, die Dienste für diese Entitäten erbringen können.


### <a name="sku-details-for-managed-application"></a>SKU-Details für eine „Verwaltete Anwendung“

In der nächsten Bildschirmaufnahme ist das „SKU-Details“-Formular für eine „Verwaltete Anwendung“ gezeigt.

   ![SKU-Details für eine verwaltete Anwendung](./media/azureapp-sku-details-managedapplication.png)

Konfigurieren Sie die folgenden SKU-Einstellungen. Die mit einem Sternchen gekennzeichneten Felder müssen ausgefüllt werden.

|    Feld         |       BESCHREIBUNG                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | Ein Titel für die SKU. Dieser Titel wird im Katalog für dieses Element angezeigt.   |
| **Zusammenfassung\***    | Eine kurze Beschreibung der SKU. (Die maximale Länge beträgt 100 Zeichen.)  |
| **Beschreibung\*** | Eine detaillierte Beschreibung der SKU. Einfaches HTML wird unterstützt.                 | 
| **SKU-Typ\***   | Typ der Azure-Anwendungslösung, wählen Sie ***Verwaltete Anwendung** für dieses Szenario aus. 
| **Cloudverfügbarkeit\*** | Der Standort der SKU. Der Standardwert ist **Azure, öffentlich**.  <b/>   **Öffentliches Azure**: Die App wird für Kunden in allen öffentlichen Azure-Regionen bereitgestellt, die über Marketplace-Integration verfügen.  <b/>   **Azure Government Cloud**: Die App wird in der Azure Government Cloud bereitgestellt. Den Herausgebern wird von Microsoft empfohlen, vor der Veröffentlichung in [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) zu testen und zu überprüfen, ob ihre Lösung in dieser Umgebung erwartungsgemäß funktioniert. Fordern Sie zum Bereitstellen und Testen ein [Testkonto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/) an.   Microsoft Azure Government ist eine Government-Community-Cloud mit kontrolliertem Zugriff für Kunden der US-Behörden auf Bundes-, Bundesstaats-, Gemeinde- und Stammesgebietsebene UND für Partner, die Dienste für diese Entitäten erbringen können. |
| **Ist dies eine private SKU?\*** | Wählen Sie **Ja** aus, wenn diese SKU nur für eine ausgewählte Gruppe von Kunden verfügbar ist. |
| **Verfügbarkeit nach Land/Region\*** | Verwenden Sie **Regionen auswählen**, um die Liste der verfügbaren Länder/Regionen anzuzeigen. Aktivieren Sie jedes gewünschte Land/Region, und wählen Sie dann **OK** aus, um Ihre Auswahl zu speichern.  <b/>   ![Liste der Verfügbarkeit nach Land/Region](./media/azure-app-select-country-region.png)  |
| **Alte Preise\*** | Preis für die SKU in USD pro Monat. Die Preise werden in Landeswährung festgelegt, wobei die bei der Konfiguration aktuellen Wechselkurse verwendet werden. Überprüfen Sie diese Einstellungen, da diese letztendlich in Ihrer Verantwortung liegen. Um die Preise der jeweiligen Länder/Regionen einzeln festzulegen oder anzuzeigen, exportieren Sie die Preiskalkulationstabelle, und importieren Sie sie mit den benutzerdefinierten Preisen.  Sie müssen Ihre Preisänderungen speichern, damit die Preisdaten exportiert/importiert werden können.  |
| **Vereinfachte Währungspreise\*** | Preis für die SKU in USD pro Monat. Dieser Wert muss mit dem für „Alte Preise“ identisch sein. Weitere Informationen finden Sie unter [Vereinfachte Währungspreise](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>„Paketdetails“ für eine Lösungsvorlage

   ![Paketdetails für eine Lösungsvorlage](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Geben Sie die folgenden Werte für die **Paketdetails** an.  Die mit einem Sternchen gekennzeichneten Felder müssen ausgefüllt werden.

- **Version\*** : Die Version des Pakets, das Sie hochladen. Versionstags müssen im Format „X.Y.Z“ angegeben werden. „X“, „Y“ und „Z“ sind dabei ganze Zahlen.
- **Paketdatei (.zip)\*** : Dieses Paket enthält die folgenden Dateien, die in einer ZIP-Datei gespeichert sind.
  - MainTemplate.json: Die Vorlagendatei für die Bereitstellung, die zum Bereitstellen der Lösung/Anwendung und zum Erstellen der darin definierten Ressourcen für die Lösung verwendet wird. Weitere Informationen finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json: Diese Datei wird vom Azure-Portal verwendet, um die Benutzeroberfläche für die Bereitstellung dieser Lösung/Anwendung zu generieren. Weitere Informationen finden Sie unter [Erstellen einer Benutzeroberfläche im Azure-Portal für die verwaltete Anwendung](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Dieses Paket sollte alle geschachtelten Vorlagen oder Skripts enthalten, die erforderlich sind, um diese Anwendung erfolgreich bereitzustellen. Die Dateien „MainTemplate.json“ und „createUiDefinition.json“ müssen im Stammverzeichnis enthalten sein.


### <a name="package-details-for-managed-application"></a>„Paketdetails“ für eine „Verwaltete Anwendung“

   ![Paketdetails für eine verwaltete Anwendung](./media/azureapp-sku-pkgdetails-managedapplication.png)

Geben Sie die folgenden Paketdetails an.  Die mit einem Sternchen gekennzeichneten Felder müssen ausgefüllt werden.

- **Version\*** : Die Version des Pakets, das Sie hochladen. Versionstags müssen im Format „X.Y.Z“ angegeben werden. „X“, „Y“ und „Z“ sind dabei ganze Zahlen.
- **Paketdatei (.zip)\*** : Dieses Paket enthält die folgenden Dateien, die in einer ZIP-Datei gespeichert sind.
  - applianceMainTemplate.json: Die Bereitstellungsvorlagendatei, die zum Bereitstellen der Lösung/Anwendung und zum Erstellen der definierten Ressourcen verwendet wird. Weitere Informationen finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - appliancecreateUIDefinition.json: Diese Datei wird vom Azure-Portal verwendet, um die Benutzeroberfläche für die Bereitstellung dieser Lösung/Anwendung zu generieren. Weitere Informationen finden Sie unter [Erstellen einer Benutzeroberfläche im Azure-Portal für die verwaltete Anwendung](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json: Dies ist die Vorlagendatei, die nur die Microsoft.Solution/appliances-Ressource enthält. Weitere Informationen finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Beachten Sie die folgenden Schlüsseleigenschaften dieser Ressource:
    - „kind“: Der Wert muss im Fall einer im Marketplace verwalteten Anwendung „Marketplace“ lauten.
    - „ManagedResourceGroupId“: Die Ressourcengruppe im Abonnement des Kunden, in der alle Ressourcen, die in der Datei „applianceMainTemplate.json“ definiert sind, bereitgestellt werden.
    - „PublisherPackageId“: Die Zeichenfolge, die das Paket eindeutig identifiziert. Dieser Wert muss durch Verketten von [publisherId].[OfferId]-preview[SKUID].[PackageVersion] erstellt werden.

  >[!IMPORTANT] 
  >Dieses Paket sollte alle geschachtelten Vorlagen oder Skripts enthalten, die erforderlich sind, um diese Anwendung erfolgreich bereitzustellen. Diese Dateien müssen sich im Stammordner befinden:  „mainTemplate.json“, „applianceMainTemplate.json“ und „applianceCreateUIDefinition.json“.

- **Mandanten-ID\*** : Die Azure Active Directory-Mandanten-ID Ihrer Organisation.
- **JIT-Zugriff aktivieren?\*** : Wählen Sie **Ja** aus, um Just-In-Time-Verwaltungszugriff für Kundenbereitstellungen zu aktivieren, für die dieses Angebot verwendet wird.

  >[!NOTE] 
  >Wenn Sie JIT aktivieren, müssen Sie die Datei „CreateUiDefinition.json“ aktualisieren, sodass JIT-Zugriff unterstützt wird.

Für eine verwaltete Anwendung müssen Sie „Autorisierung“ und „Richtlinieneinstellungen“ konfigurieren.


#### <a name="authorization"></a>Autorisierung

Fügen Sie den Azure Active Directory-Bezeichner des Benutzers, der Gruppe oder der Anwendung hinzu, dem oder der Sie die Berechtigung für die verwaltete Ressourcengruppe erteilen möchten. Die erteilte Berechtigung ist durch die Rollendefinitions-ID gekennzeichnet. Dies kann ein „Besitzer“, ein „Mitwirkender“ oder eine benutzerdefinierte Rolle sein.


#### <a name="policy-settings"></a>Richtlinieneinstellungen

Fügen Sie die Richtlinien hinzu, denen die verwaltete Anwendung entspricht. Weitere Informationen zu Azure-Ressourcenrichtlinien finden Sie unter [Was ist Azure Policy?](../../../governance/policy/overview.md)

   ![Autorisierung und Richtlinieneinstellungen für eine verwaltete Anwendung](./media/azureapp-sku-details-managedapp-auth-policy.png)

**So erstellen Sie eine neue Autorisierung:**

1. Wählen Sie unter **Autorisierung** die Option **+ Neue Autorisierung** aus.
2. Geben Sie für **Prinzipal-ID** den Azure Active Directory-Bezeichner des Benutzers, der Gruppe oder der Anwendung ein, dem oder der Sie die Berechtigung für die verwaltete Ressourcengruppe erteilen möchten. Die erteilte Berechtigung ist durch die Rollendefinition gekennzeichnet.
3. Für **Rollendefinition** wählen Sie eine der folgenden Optionen in der Dropdownliste aus:  „Besitzer“ oder „Mitwirkender“. Weitere Informationen finden Sie unter [Integrierte Rollen für Azure-Ressourcen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Mehrere Autorisierungen können hinzugefügt werden. Es wird jedoch empfohlen, eine Active Directory-Benutzergruppe zu erstellen und deren ID in der „PrincipalId“ anzugeben. Dies ermöglicht das Hinzufügen weiterer Benutzer zu der Benutzergruppe, ohne dass ein SKU-Update erforderlich ist.

**So erstellen Sie eine neue Richtlinie:**

1. Wählen Sie unter **Richtlinieneinstellungen** die Option **+ Neue Richtlinie** aus.
2. Geben Sie für **Richtlinienname** einen Namen für die Richtlinie ein. Der Name darf maximal 50 Zeichen lang sein.
3. Wählen Sie für **Richtlinien** eine der folgenden Optionen in der Dropdownliste aus. Wählen Sie die Richtlinie aus, die der Datenanbieter aktiviert haben möchte, wenn die Anwendung die Daten verwendet. Weitere Informationen finden Sie unter [Azure Policy-Beispiele](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Richtlinieneinstellungen für eine verwaltete Anwendung](./media/azureapp-sku-policy-settings.png)

4. Für **SKU-Richtlinie** wählen Sie „Free“ oder „Standard“ als SKU-Richtlinientyp aus. Der Standard-SKU ist für Überwachungsrichtlinien erforderlich.


## <a name="next-steps"></a>Nächste Schritte

Sie geben weitere Beschreibungen Ihres Angebots ein und stellen Marketingmittel in der Registerkarte [Marketplace](./cpp-marketplace-tab.md) bereit. 
