---
title: Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory
description: Hier finden Sie Informationen zur Beziehung zwischen Anwendungsobjekten und Dienstprinzipalobjekten in Azure Active Directory.
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47626cbd8d5b8a5ddf7cc5c6ea42fb9453e5bfb2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540669"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory

In einigen Fällen kann der Begriff „Anwendung“ im Kontext von Azure Active Directory (Azure AD) falsch verstanden werden. Dieser Artikel veranschaulicht die konzeptuellen und konkreten Aspekte der Azure AD-Anwendungsintegration und erläutert die Registrierung und Zustimmung zu einer [mehrinstanzenfähigen Anwendung](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Übersicht

Eine Anwendung, die in Azure AD integriert ist, hat Auswirkungen über die Software hinaus. „Anwendung“ wird häufig als konzeptioneller Begriff verwendet und bezieht sich nicht nur auf die Anwendungssoftware, sondern auch auf die Azure AD-Registrierung und die Rolle bei „Konversationen“ zur Authentifizierung/Autorisierung zur Laufzeit.

Der Definition nach kann eine Anwendung in den folgenden Rollen ausgeführt werden:

- [Clientrolle](developer-glossary.md#client-application) (Nutzung einer Ressource)
- [Ressourcenserverrolle](developer-glossary.md#resource-server) (APIs werden für Clients verfügbar gemacht.)
- Clientrolle und Ressourcenserverrolle

Das Konversationsprotokoll ist durch einen [Ablauf zur Erteilung einer OAuth 2.0-Autorisierung](developer-glossary.md#authorization-grant) definiert und ermöglicht dem Client bzw. der Ressource das Zugreifen auf bzw. das Schützen von Daten einer Ressource.

In den folgenden Abschnitten sehen Sie, wie das Azure AD-Anwendungsmodell eine Anwendung beim Entwurf und zur Laufzeit darstellt.

## <a name="application-registration"></a>Anwendungsregistrierung

Wenn Sie eine Azure AD-Anwendung im [Azure-Portal][AZURE-Portal] registrieren, werden im Azure AD-Mandanten zwei Objekte erstellt:

- ein Anwendungsobjekt und
- ein Dienstprinzipalobjekt

### <a name="application-object"></a>Anwendungsobjekt

Eine Azure AD-Anwendung ist durch ein eindeutiges Anwendungsobjekt definiert. Es befindet sich im Azure AD-Mandanten, in dem die Anwendung registriert ist und der als „Heimmandant“ der Anwendung bezeichnet wird. Die [Application-Entität][MS-Graph-App-Entity] von Microsoft Graph definiert das Schema für die Eigenschaften eines Anwendungsobjekts.

### <a name="service-principal-object"></a>Dienstprinzipalobjekt

Um auf Ressourcen zugreifen zu können, die von einem Azure AD-Mandanten geschützt werden, muss die Entität, die Zugriff benötigt, durch einen Sicherheitsprinzipal dargestellt werden. Dies gilt für Benutzer (Benutzerprinzipal) und Anwendungen (Dienstprinzipal).

Der Sicherheitsprinzipal definiert die Zugriffsrichtlinie und Berechtigungen für den Benutzer/die Anwendung im Azure AD-Mandanten. Dies aktiviert die Kernfunktionen wie die Authentifizierung des Benutzers/der Anwendung während der Anmeldung und die Autorisierung beim Zugriff auf Ressourcen.

Wenn eine Anwendung die Berechtigung zum Zugriff auf Ressourcen in einem Mandanten erhält (bei der Registrierung oder [Zustimmung](developer-glossary.md#consent)), wird ein Dienstprinzipalobjekt erstellt. Die [ServicePrincipal-Entität][MS-Graph-Sp-Entity] von Microsoft Graph definiert das Schema für die Objekteigenschaften eines Dienstprinzipalobjekts.

### <a name="application-and-service-principal-relationship"></a>Beziehung zwischen Anwendung und Dienstprinzipal

Stellen Sie sich das Anwendungsobjekt als *globale* Darstellung Ihrer Anwendung zur Verwendung über alle Mandanten hinweg und den Dienstprinzipal als *lokale* Darstellung zur Verwendung in einem bestimmten Mandanten vor.

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

![Beziehung zwischen einem Anwendungsobjekt und einem Dienstprinzipalobjekt](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Dieses Beispielszenario:

| Schritt | BESCHREIBUNG |
|------|-------------|
| 1    | Schritt 1 ist der Prozess zur Erstellung der Anwendungs- und Dienstprinzipalobjekte im Home-Mandanten der Anwendung. |
| 2    | Nachdem die Administratoren von Contoso und Fabrikam die Zustimmung erteilt haben, wird im Azure AD-Mandanten des Unternehmens ein Dienstprinzipalobjekt erstellt und mit den Berechtigungen versehen, die vom Administrator gewährt wurden. Beachten Sie auch, dass die HR App so konfiguriert bzw. entworfen werden kann, dass die Zustimmung für die individuelle Nutzung durch die Benutzer erteilt wird. |
| 3    | Die Endkundenmandanten der Personalanwendung (Contoso und Fabrikam) erhalten jeweils ein eigenes Dienstprinzipalobjekt. Jedes Objekt repräsentiert die Verwendung einer Instanz der Anwendung zur Laufzeit, gesteuert durch die Berechtigungen, die vom jeweiligen Administrator per Zustimmung erteilt wurden. |

## <a name="next-steps"></a>Nächste Schritte

- Sie können den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) verwenden, um die Anwendungs- und die Dienstprinzipalobjekte abzufragen.
- Sie können auf das Anwendungsobjekt einer Anwendung über die Microsoft Graph-API, den Anwendungsmanifest-Editor im [Azure-Portal][AZURE-Portal] oder [Azure AD-PowerShell-Cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0) zugreifen, wie von der OData-[Application-Entität][MS-Graph-App-Entity] dargestellt.
- Sie können auf das Dienstprinzipalobjekt einer Anwendung über die Microsoft Graph-API oder [Azure AD-PowerShell-Cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0) zugreifen, wie von der OData-[ServicePrincipal-Entität][MS-Graph-Sp-Entity] dargestellt.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
