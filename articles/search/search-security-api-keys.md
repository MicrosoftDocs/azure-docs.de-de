---
title: Authentifizierung mit API-Schlüssel
titleSuffix: Azure Cognitive Search
description: Ein API-Schlüssel steuert den eingehenden Zugriff auf den Dienstendpunkt. Administratorschlüssel gewähren Schreibzugriff. Abfrageschlüssel können nur für Lesezugriff erstellt werden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 8b2e85744923fb2e7e474e049df1536aebc56f3c
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536932"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Erstellen und Verwalten von API-Schlüsseln für einen Azure Cognitive Search-Dienst

Für alle Anforderungen an einen Suchdienst wird ein schreibgeschützter API-Schlüssel benötigt, der speziell für Ihren Dienst generiert wurde. Dieser API-Schlüssel ist der einzige Authentifizierungsmechanismus für auf Ihren Suchdienstendpunkt eingehende Anforderungen und ist für jede Anforderung erforderlich. 

+ In [REST-Lösungen](search-get-started-rest.md) wird der `api-key` in der Regel im Anforderungsheader angegeben.

+ In [.NET-Lösungen](search-howto-dotnet-sdk.md) wird ein Schlüssel oft als Konfigurationseinstellung angegeben und dann als [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)-Objekt übergeben.

Sie können API-Schlüssel im [Azure-Portal](https://portal.azure.com) oder über [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search) oder [REST-API](/rest/api/searchmanagement/) anzeigen und verwalten.

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Portalseite &gt; Einstellungen abrufen &gt; Abschnitt „Schlüssel“" border="false":::

## <a name="what-is-an-api-key"></a>Was ist ein API-Schlüssel?

Ein API-Schlüssel ist eine eindeutige Zeichenfolge aus zufällig generierten Ziffern und Buchstaben, die bei jeder Anforderung an den Suchdienst übergeben wird. Der Dienst akzeptiert die Anforderung, wenn sowohl die Anforderung selbst als auch der Schlüssel gültig sind. 

Zwei Arten von Schlüsseln werden für den Zugriff auf Ihren Suchdienst verwendet: Administrator (Lesen/Schreiben) und Abfrage (schreibgeschützt).

|Key|BESCHREIBUNG|Einschränkungen|  
|---------|-----------------|------------|  
|Admin|Gewährt Vollzugriff auf alle Vorgänge. Dazu zählen die Dienstverwaltung sowie das Erstellen und Löschen von Indizes, Indexern und Datenquellen.<br /><br /> Für Administratoren werden zwei Schlüssel generiert, wenn der Dienst erstellt wird. Diese werden im Portal als *primärer* bzw. *sekundärer* Schlüssel bezeichnet und können bei Bedarf einzeln neu generiert werden. Durch die Verwendung von zwei Schlüsseln kann weiterhin auf den Dienst zugegriffen werden, während für einen der Schlüssel ein Rollover durchgeführt wird.<br /><br /> Administratorschlüssel werden nur in HTTP-Anforderungsheadern angegeben. Ein API-Schlüssel für Administratoren kann nicht in einer URL platziert werden.|Maximal zwei pro Dienst|  
|Abfrage|Gewährt Lesezugriff auf Indizes und Dokumente. Diese werden in der Regel auf Clientanwendungen verteilt, die Suchanfragen ausgeben.<br /><br /> Abfrageschlüssel werden bei Bedarf erstellt.<br /><br /> Abfrageschlüssel können in einem HTTP-Anforderungsheader für Such-, Vorschlags- oder Recherchevorgänge angegeben werden. Sie können einen Abfrageschlüssel aber auch als Parameter in einer URL übergeben. Je nachdem, wie Ihre Clientanwendung die Anforderung formuliert, ist es unter Umständen einfacher, den Schlüssel als Abfrageparameter zu übergeben:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 pro Dienst|  

 Administrator- und Abfrageschlüssel sind rein optisch nicht zu unterscheiden. Bei beiden Schlüsseln handelt es sich um eine Zeichenfolge mit 32 nach dem Zufallsprinzip generierten alphanumerischen Zeichen. Sollten Sie nicht mehr wissen, welche Art von Schlüssel in Ihrer Anwendung angegeben ist, können Sie [die Schlüsselwerte im Portal überprüfen](https://portal.azure.com).  

> [!NOTE]  
> Aus Sicherheitsgründen sollten im Anforderungs-URI keine vertraulichen Daten wie etwa ein API-Schlüssel (`api-key`) übergeben werden. Aus diesem Grund akzeptiert die kognitive Azure-Suche als `api-key` in der Abfragezeichenfolge nur einen Abfrageschlüssel. Es empfiehlt sich, diese Richtlinie einzuhalten – es sei denn, der Inhalt Ihres Index soll öffentlich verfügbar sein. Wir empfehlen generell, `api-key` als Anforderungsheader zu übergeben.  

## <a name="find-existing-keys"></a>Suchen vorhandener Schlüssel

Zugriffsschlüssel stehen im Portal oder über [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search) oder [REST-API](/rest/api/searchmanagement/) zur Verfügung.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Listen Sie die [Suchdienste](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) für Ihr Abonnement auf.
1. Wählen Sie den Dienst aus, und klicken Sie auf der Seite „Übersicht“ auf **Einstellungen** >**Schlüssel**, um Administrator- und Abfrageschlüssel anzuzeigen.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Portalseite &gt; Einstellungen anzeigen &gt; Abschnitt „Schlüssel“" border="false":::

## <a name="create-query-keys"></a>Erstellen von Abfrageschlüsseln

Abfrageschlüssel werden für den schreibgeschützten Zugriff auf Dokumente in einem Index für die Vorgänge für eine Dokumentensammlung verwendet. Such-, Filter- und Vorschlagsabfragen sind Vorgänge, die einen Abfrageschlüssel verwenden. Alle schreibgeschützten Vorgänge, die Systemdaten oder Objektdefinitionen zurückgeben, z. B. eine Indexdefinition oder einen Indexerstatus, erfordern einen Administratorschlüssel.

Das Einschränken des Zugriffs und der Vorgänge in Client-Apps ist besonders wichtig, um die Suchobjekte in Ihrem Dienst zu schützen. Verwenden Sie für Abfragen aus einer Client-App immer einen Abfrageschlüssel anstelle eines Administratorschlüssels.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Listen Sie die [Suchdienste](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) für Ihr Abonnement auf.
3. Wählen Sie den Dienst aus, und klicken Sie auf der Seite „Übersicht“ auf **Einstellungen** >**Schlüssel**.
4. Klicken Sie auf **Abfrageschlüssel verwalten**.
5. Verwenden Sie einen bereits für Ihren Dienst generierten Abfrageschlüssel, oder erstellen Sie bis zu 50 neue Abfrageschlüssel. Der Standardabfrageschlüssel ist nicht benannt. Sie können zusätzliche Abfrageschlüssel jedoch für eine einfachere Verwaltbarkeit benennen.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Erstellen oder Verwenden von Abfrageschlüsseln" border="false":::

> [!Note]
> Ein Codebeispiel für die Verwendung von Abfrageschlüsseln finden Sie in [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Erneutes Generieren von Administratorschlüsseln

Für jeden Dienst werden zwei Administratorschlüssel erstellt, sodass Sie einen Primärschlüssel mit dem Sekundärschlüssel für Geschäftskontinuität rotieren können.

1. Kopieren Sie den Sekundärschlüssel auf der Seite **Einstellungen** >**Schlüssel**.
2. Aktualisieren Sie für alle Anwendungen die Einstellungen des API-Schlüssels so, dass der Sekundärschlüssel verwendet wird.
3. Generieren Sie den Primärschlüssel neu.
4. Aktualisieren Sie alle Anwendungen so, dass sie den neuen Primärschlüssel verwenden.

Wenn Sie versehentlich beide Schlüssel gleichzeitig neu generieren, wird für alle Clientanforderungen, die diese Schlüssel verwenden, der Fehler „HTTP 403 – Verboten“ zurückgegeben. Allerdings werden die Inhalte nicht gelöscht, und Sie sind nicht dauerhaft gesperrt. 

Sie können weiterhin über das Portal oder programmatisch auf den Dienst zugreifen. Verwaltungsfunktionen werden über eine Abonnement-ID und nicht einen Dienst-API-Schlüssel verwendet und sind daher auch dann verfügbar, wenn Ihre API-Schlüssel nicht verfügbar sind. 

Nach der Erstellung neuer Schlüssel über das Portal oder die Verwaltungsebene wird der Zugriff auf Ihre Inhalte (Indizes, Indexer, Datenquellen, Synonymzuordnungen) wiederhergestellt, sobald Sie die neuen Schlüssel haben und nach Aufforderung angeben.

## <a name="secure-api-keys"></a>Sichern von API-Schlüsseln

Durch [rollenbasierte Berechtigungen](search-security-rbac.md) können Sie die Schlüssel löschen oder lesen, aber Sie können einen Schlüssel nicht durch ein benutzerdefiniertes Kennwort ersetzen oder Active Directory als primäre Authentifizierungsmethode für den Zugriff auf Suchvorgänge verwenden. 

Die Sicherheit der Schlüssel wird erreicht, indem der Zugriff über das Portal oder Resource Manager-Oberflächen (PowerShell oder Befehlszeilenschnittstelle) eingeschränkt wird. Wie bereits erwähnt, können Abonnementadministratoren alle API-Schlüssel anzeigen und neu generieren. Informieren Sie sich über Rollenzuweisungen, damit Sie wissen, wer Zugriff auf die Admin-Schlüssel hat.

+ Klicken Sie im Dashboard für den Dienst auf **Zugriffssteuerung (IAM)** und anschließend auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für Ihren Dienst anzuzeigen.

Mitglieder der folgenden Rollen können Schlüssel anzeigen und neu generieren: Besitzer, Mitwirkender, [Suchdienstmitwirkender](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Weitere Informationen

+ [Sicherheit in der kognitiven Azure-Suche](search-security-overview.md)
+ [Rollenbasierte Azure-Zugriffssteuerung in Azure Cognitive Search](search-security-rbac.md)
+ [Verwalten mit PowerShell](search-manage-powershell.md) 