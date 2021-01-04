---
title: Apps und Dienstprinzipale in Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Hier finden Sie Informationen zur Beziehung zwischen Anwendungsobjekten und Dienstprinzipalobjekten in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: e4325303f5a10fa1df670495dd6d8190167182e8
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861101"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory

In diesem Artikel werden die Anwendungsregistrierung, Anwendungsobjekte und Dienstprinzipale in Azure Active Directory mit ihren Eigenschaften, Verwendungsmöglichkeiten und den wechselseitigen Beziehungen beschrieben. Außerdem wird ein Beispielszenario mit mehreren Mandanten vorgestellt, um die Beziehung zwischen dem Anwendungsobjekt einer Anwendung und den entsprechenden Dienstprinzipalobjekten zu veranschaulichen.

## <a name="application-registration"></a>Anwendungsregistrierung
Um die Identitäts- und Zugriffsverwaltungsfunktionen an Azure AD zu delegieren, muss eine Anwendung bei einem Azure AD-[Mandanten](developer-glossary.md#tenant) registriert werden. Wenn Sie Ihre Anwendung bei Azure AD registrieren, erstellen Sie eine Identitätskonfiguration für Ihre Anwendung, die die Integration in Azure AD ermöglicht. Wenn Sie eine App im [Azure-Portal][AZURE-Portal] registrieren, wählen Sie aus, ob es sich um einen Mandanten (Zugriff nur in Ihrem Mandanten möglich) oder um mehrere Mandanten (Zugriff in anderen Mandanten möglich) handelt. Optional können Sie auch einen Umleitungs-URI festlegen, an den das Zugriffstoken gesendet wird.

:::image type="content" source="media/app-objects-and-service-principals/app-registration.png" alt-text="Screenshot des Bereichs „Anwendung registrieren“ im Azure-Portal":::

Wenn Sie die App-Registrierung abgeschlossen haben, verfügen Sie über eine global eindeutige Instanz der App (das Anwendungsobjekt), die sich in Ihrem Basismandanten oder -verzeichnis befindet.  Außerdem verfügen Sie über eine global eindeutige ID für Ihre App (App- oder Client-ID).  Im Portal können Sie dann Geheimnisse oder Zertifikate und Geltungsbereiche hinzufügen, damit Ihre App funktioniert. Sie können auch das Branding Ihrer App im Anmeldedialogfeld anpassen und vieles mehr.

Wenn Sie eine Anwendung im Portal registrieren, werden automatisch ein Anwendungsobjekt sowie ein Dienstprinzipalobjekt in Ihrem Basismandanten erstellt.  Wenn Sie eine Anwendung mit den Microsoft Graph-APIs registrieren/erstellen, wird das Dienstprinzipalobjekt in einem separaten Schritt erstellt.

## <a name="application-object"></a>Anwendungsobjekt
Eine Azure AD-Anwendung wird durch ein einziges, eindeutiges Anwendungsobjekt definiert, das sich im Azure AD-Mandanten befindet, bei dem die Anwendung registriert wurde (der sogenannte „Basis“-Mandant der Anwendung).  Ein Anwendungsobjekt wird als Vorlage bzw. Blaupause zum Erstellen eines oder mehrerer Dienstprinzipalobjekte verwendet.  Ein Dienstprinzipal wird in jedem Mandanten erstellt, in dem die Anwendung verwendet wird. Vergleichbar einer Klasse in der objektorientierten Programmierung hat das Anwendungsobjekt einige statische Eigenschaften, die auf alle erstellten Dienstprinzipale (oder Anwendungsinstanzen) angewendet werden.

Das Anwendungsobjekt beschreibt drei Aspekte einer Anwendung: das Ausstellen von Token durch den Dienst für den Zugriff auf die Anwendung, die Ressourcen, auf die die Anwendung möglicherweise zugreifen muss, und die Aktionen, die die Anwendung ausführen kann.

Das Blatt **App-Registrierungen** im [Azure-Portal][AZURE-Portal] wird zum Auflisten und Verwalten der Anwendungsobjekte in Ihrem Basismandanten verwendet.

![Blatt „App-Registrierungen“](./media/app-objects-and-service-principals/app-registrations-blade.png)

Die [Application-Entität][MS-Graph-App-Entity] von Microsoft Graph definiert das Schema für die Eigenschaften eines Anwendungsobjekts.

## <a name="service-principal-object"></a>Dienstprinzipalobjekt
Um auf Ressourcen zugreifen zu können, die von einem Azure AD-Mandanten geschützt werden, muss die Entität, die Zugriff benötigt, durch einen Sicherheitsprinzipal dargestellt werden. Diese Anforderung gilt für Benutzer (Benutzerprinzipal) und Anwendungen (Dienstprinzipal). Der Sicherheitsprinzipal definiert die Zugriffsrichtlinie und Berechtigungen für den Benutzer/die Anwendung im Azure AD-Mandanten. Dies aktiviert die Kernfunktionen wie die Authentifizierung des Benutzers/der Anwendung während der Anmeldung und die Autorisierung beim Zugriff auf Ressourcen.

Ein Dienstprinzipal ist die lokale Darstellung (bzw. Anwendungsinstanz) eines globalen Anwendungsobjekts in einem einzelnen Mandanten oder Verzeichnis. Ein Dienstprinzipal ist eine konkrete Instanz, die aus dem Anwendungsobjekt erstellt wird und bestimmte Eigenschaften von diesem Anwendungsobjekt erbt.  Ein Dienstprinzipal wird in jedem Mandanten erstellt, in dem die Anwendung verwendet wird, und verweist auf das global eindeutige Anwendungsobjekt.  Das Dienstprinzipalobjekt definiert, was die App tatsächlich im jeweiligen Mandanten ausführen kann, wer auf die App zugreifen kann und auf welche Ressourcen die App zugreifen kann.

Wenn eine Anwendung die Berechtigung zum Zugriff auf Ressourcen in einem Mandanten erhält (bei der Registrierung oder [Zustimmung](developer-glossary.md#consent)), wird ein Dienstprinzipalobjekt erstellt. Sie können auch mit [Azure PowerShell](howto-authenticate-service-principal-powershell.md), der [Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli), [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http), im [Azure-Portal][AZURE-Portal] und mit anderen Tools ein Dienstprinzipalobjekt in einem Mandanten erstellen.  Wenn Sie das Portal verwenden, wird beim Registrieren einer Anwendung automatisch ein Dienstprinzipal erstellt.

Auf dem Blatt **Unternehmensanwendungen** im Portal werden die Dienstprinzipale in einem Mandanten aufgelistet und verwaltet. Sie können die Berechtigungen des Dienstprinzipals, die vom Benutzer genehmigten Berechtigungen, die Benutzer, die diese Genehmigung erteilt haben, die Anmeldeinformationen und mehr anzeigen.

![Blatt „Unternehmens-Apps“](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

Die [ServicePrincipal-Entität][MS-Graph-Sp-Entity] von Microsoft Graph definiert das Schema für die Eigenschaften eines Dienstprinzipalobjekts.

## <a name="relationship-between-application-objects-and-service-principals"></a>Beziehung zwischen Anwendungsobjekten und Dienstprinzipalen

Das Anwendungsobjekt ist die *globale* Darstellung Ihrer Anwendung für die Verwendung bei allen Mandanten. Der Dienstprinzipal ist die *lokale* Darstellung für die Verwendung in einem bestimmten Mandanten.

Das Anwendungsobjekt fungiert als Vorlage, aus der allgemeine und Standardeigenschaften zur Verwendung im entsprechenden Dienstprinzipal *abgeleitet* werden. Für ein Anwendungsobjekt bestehen daher eine 1:1-Beziehung mit der Softwareanwendung und eine 1:viele-Beziehung mit den entsprechenden Dienstprinzipalobjekten.

Ein Dienstprinzipal muss in jedem Mandanten erstellt werden, in dem die Anwendung verwendet wird. Er ermöglicht, eine Identität für die Anmeldung und/oder den Zugriff auf vom Mandanten gesicherte Ressourcen einzurichten. Eine Einzelmandantenanwendung weist nur einen Dienstprinzipal (im eigenen Heimmandanten) auf, der während der Anwendungsregistrierung erstellt und für die Verwendung autorisiert wird. Für eine Webanwendung/API mit mehreren Mandanten muss außerdem in jedem Mandanten ein Dienstprinzipal erstellt werden, in dem ein Benutzer aus diesem Mandanten der Verwendung zugestimmt hat.

> [!NOTE]
> Alle Änderungen, die Sie an Ihrem Anwendungsobjekt vornehmen, werden auch in seinem Dienstprinzipalobjekt ausschließlich im Home-Mandanten der Anwendung widergespiegelt (der Mandant, für den die Registrierung durchgeführt wurde). Bei mehrinstanzenfähigen Anwendungen werden Änderungen am Anwendungsobjekt erst dann in den Dienstprinzipalobjekten von Endkundenmandanten übernommen, wenn der Zugriff über den [Anwendungszugriffsbereich](https://myapps.microsoft.com) entfernt und wieder gewährt wird.
>
> Beachten Sie außerdem, dass native Anwendungen standardmäßig als mehrinstanzenfähig registriert werden.

## <a name="example"></a>Beispiel

Das folgende Diagramm veranschaulicht die Beziehung zwischen einem Anwendungsobjekt der Anwendung und den entsprechenden Dienstprinzipalobjekten im Kontext einer mehrinstanzenfähigen Beispielanwendung mit dem Namen **HR App**. In diesem Beispielszenario werden drei Azure AD-Mandanten verwendet:

- **Adatum:** Der Mandant, der von dem Unternehmen verwendet wird, das die **Personal-App** entwickelt hat
- **Contoso:** Der Mandant, der von der Organisation Contoso (einem Endkunden der **Personal-App**) verwendet wird
- **Fabrikam:** Der von der Organisation Fabrikam (ebenfalls ein Endkunde der **Person-App**) verwendete Mandant

![Beziehung zwischen einem App-Objekt und einem Dienstprinzipalobjekt](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Dieses Beispielszenario:

| Schritt | BESCHREIBUNG |
|------|-------------|
| 1    | Schritt 1 ist der Prozess zur Erstellung der Anwendungs- und Dienstprinzipalobjekte im Home-Mandanten der Anwendung. |
| 2    | Nachdem die Administratoren von Contoso und Fabrikam die Zustimmung erteilt haben, wird im Azure AD-Mandanten des Unternehmens ein Dienstprinzipalobjekt erstellt und mit den Berechtigungen versehen, die vom Administrator gewährt wurden. Beachten Sie auch, dass die HR App so konfiguriert bzw. entworfen werden kann, dass die Zustimmung für die individuelle Nutzung durch die Benutzer erteilt wird. |
| 3    | Die Endkundenmandanten der Personalanwendung (Contoso und Fabrikam) erhalten jeweils ein eigenes Dienstprinzipalobjekt. Jedes Objekt repräsentiert die Verwendung einer Instanz der Anwendung zur Laufzeit, gesteuert durch die Berechtigungen, die vom jeweiligen Administrator per Zustimmung erteilt wurden. |

## <a name="next-steps"></a>Nächste Schritte

- Sie können den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) verwenden, um die Anwendungs- und die Dienstprinzipalobjekte abzufragen.
- Sie können auf das Anwendungsobjekt einer Anwendung über die Microsoft Graph-API, den Anwendungsmanifest-Editor im [Azure-Portal][AZURE-Portal] oder [Azure AD PowerShell-Cmdlets](/powershell/azure/) zugreifen, wie von der OData-[Application-Entität][MS-Graph-App-Entity] dargestellt.
- Sie können auf das Dienstprinzipalobjekt einer Anwendung über die Microsoft Graph-API oder [Azure AD PowerShell-Cmdlets](/powershell/azure/) zugreifen, wie von der OData-[ServicePrincipal-Entität][MS-Graph-Sp-Entity] dargestellt.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
