---
title: DNS-Alias
description: Ihre Anwendungen können eine Verbindung mit einem Alias für den Namen Ihres Servers für Azure SQL-Datenbank herstellen. In der Zwischenzeit können Sie SQL-Datenbank, auf die der Alias verweist, jederzeit ändern, um Tests und andere Vorgänge zu vereinfachen.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 25c2157a8de237d0ec66caa72d59c810d419ac76
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989121"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-Alias für Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL-Datenbank weist einen DNS-Server (Domain Name System) auf. PowerShell und REST-APIs akzeptieren [Aufrufe zum Erstellen und Verwalten von DNS-Aliasen](#anchor-powershell-code-62x) für den Namen Ihres [logischen SQL -Servers](logical-servers.md).

Anstelle des Servernamens kann ein *DNS-Alias* verwendet werden. Clientprogramme können den Alias in den Verbindungszeichenfolgen angeben. Der DNS-Alias stellt eine Übersetzungsebene bereit, die Ihre Clientprogramme an verschiedene Server umleiten kann. Durch diese Ebene wird Ihnen die Schwierigkeit erspart, alle Clients und deren Verbindungszeichenfolgen zu suchen und zu bearbeiten.

Zu den allgemeinen Verwendungsmöglichkeiten für einen DNS-Alias zählen Folgende:

- Erstellen Sie einen einprägsamen Namen für einen Server.
- Bei der Erstentwicklung kann Ihr Alias auf einen Testserver verweisen. Wenn die Anwendung online geschaltet wird, können Sie den Alias auf den Produktionsserver verweisen. Für den Übergang zwischen Test und Produktion sind keine Änderungen an den Konfigurationen mehrerer Clients erforderlich, die mit dem Server verbunden sind.
- Nehmen Sie an, dass die einzige Datenbank in Ihrer Anwendung zu einem anderen Server migriert wurde. Sie können den Alias ändern, ohne die Konfigurationen mehrerer Clients ändern zu müssen.
- Bei einem regionalen Ausfall verwenden Sie eine geografische Wiederherstellung, um Ihre Datenbank auf einem anderen Server und in einer anderen Region wiederherzustellen. Sie können Ihren vorhandenen Alias so ändern, dass er auf den neuen Server verweist, damit die vorhandene Clientanwendung eine neue Verbindung damit herstellen kann.

## <a name="domain-name-system-dns-of-the-internet"></a>Internet-DNS (Domain Name System)

Das Internet basiert auf dem DNS. Das DNS übersetzt die Anzeigenamen in den Namen Ihres Servers.

## <a name="scenarios-with-one-dns-alias"></a>Szenarien mit einem DNS-Alias

Nehmen Sie an, Sie müssen Ihr System auf einen neuen Server umstellen. Bisher mussten Sie jede Verbindungszeichenfolge in jedem Clientprogramm suchen und aktualisieren. Doch nun muss nur eine Aliaseigenschaft aktualisiert werden, wenn in den Verbindungszeichenfolgen ein DNS-Alias enthalten ist.

Das DNS-Aliasfeature von Azure SQL-Datenbank kann in folgenden Szenarien hilfreich sein:

### <a name="test-to-production"></a>Test bis Produktion

Bei der Entwicklung der Clientprogramme verwenden Sie einen DNS-Alias in den Verbindungszeichenfolgen. Sie ändern die Eigenschaften des Alias dahingehend, dass sie auf eine Testversion Ihres Servers verweisen.

Wenn das neue System später in der Produktion online geschaltet wird, können Sie die Eigenschaften des Alias dahingehend aktualisieren, dass sie auf den Produktionsserver verweisen. Es sind keine Änderung an den Clientprogrammen erforderlich.

### <a name="cross-region-support"></a>Regionsübergreifende Unterstützung

Bei einer Notfallwiederherstellung wird Ihr Server möglicherweise in eine andere geografische Region verschoben. Für ein System, bei dem ein DNS-Alias verwendet wurde, kann die Anforderung zum Suchen und Aktualisieren aller Verbindungszeichenfolgen für sämtliche Clients vermieden werden. Stattdessen können Sie einen Alias dahingehend aktualisieren, dass er auf den neuen Server verweist, der nun Ihre Azure SQL-Datenbank hostet.

## <a name="properties-of-a-dns-alias"></a>Eigenschaften eines DNS-Alias

Die folgenden Eigenschaften gelten für sämtliche DNS-Aliase Ihres Servers:

- *Eindeutiger Name:* Jeder Aliasname, den Sie erstellen, ist auf allen Servern eindeutig (ebenso wie Servernamen).
- *Server ist erforderlich:* Ein DNS-Alias kann nur dann erstellt werden, wenn er auf genau einen Server verweist und der Server bereits vorhanden ist. Ein aktualisierter Alias muss immer genau auf einen vorhandenen Server verweisen.
  - Wenn Sie einen Server löschen, löscht das Azure-System auch alle DNS-Aliase, die auf den Server verweisen.
- *Nicht an eine beliebige Region gebunden:* DNS-Aliase sind nicht an eine Region gebunden. DNS-Aliase können dahingehend aktualisiert werden, dass sie auf einen Server in einer beliebigen geografischen Region verweisen.
  - Wenn ein Alias jedoch für den Verweis auf einen anderen Server aktualisiert wird, müssen beide Server im selben Azure-*Abonnement* vorhanden sein.
- *Berechtigungen:* Für die Verwaltung eines DNS-Alias benötigt der Benutzer die Berechtigungen *Servermitwirkender* oder höhere Berechtigungen. Weitere Informationen finden Sie unter [Erste Schritte mit der rollenbasierten Zugriffssteuerung in Azure im Azure-Portal](../../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Verwalten von DNS-Aliase

REST-APIs und PowerShell-Cmdlets werden Ihnen für die programmgesteuerte Verwaltung Ihrer DNS-Aliase zur Verfügung gestellt.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST-APIs für die Verwaltung Ihrer DNS-Aliase

Die Dokumentation für die REST-APIs finden Sie an folgendem Webspeicherort:

- [Azure SQL-Datenbank-REST-API](/rest/api/sql/)

Darüber hinaus können sich die REST-APIs in GitHub unter folgendem Link befinden:

- [Azure SQL-Datenbank, DNS-Alias-REST-APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell für die Verwaltung Ihrer DNS-Aliase

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Es sind PowerShell-Cmdlets verfügbar, die die REST-APIs aufrufen.

Ein Codebeispiel von PowerShell-Cmdlets, das für die Verwaltung von DNS-Aliase zu verwenden ist, ist unter folgendem Link dokumentiert:

- [PowerShell für den DNS-Alias für Azure SQL-Datenbank](dns-alias-powershell-create.md)

Die im Codebeispiel verwendeten Cmdlets lauten wie folgt:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Erstellt einen neuen DNS-Alias im Dienstsystem von Azure SQL-Datenbank. Der Alias verweist auf Server 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Ruft alle DNS-Aliase auf, die Server 1 zugewiesen sind, und listet diese auf.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Ändert den Servernamen, für dessen Referenzierung der Alias konfiguriert ist, aus Server 1 in Server 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Entfernt den DNS-Alias von Server 2 anhand des Aliasnamens.

## <a name="limitations-during-preview"></a>Einschränkungen in der Vorschauversion

Für einen DNS-Alias gelten gegenwärtig die folgenden Einschränkungen:

- *Verzögerung von bis zu 2 Minuten:* Es dauert bis zu zwei Minuten, bis ein DNS-Alias aktualisiert oder entfernt wird.
  - Abgesehen von einer kurzen Verzögerung stellt der Alias den Verweis von Clientverbindungen mit dem Legacyserver umgehend ein.
- *DNS-Suche:* Zurzeit besteht die einzige autoritative Möglichkeit darin, anhand einer [DNS-Suche](/windows-server/administration/windows-commands/nslookup) zu überprüfen, auf welchen Server ein bestimmter DNS-Alias verweist.
- _Tabellenüberwachung wird nicht unterstützt:_ Sie können keinen DNS-Alias für einen Server verwenden, für den die *Tabellenüberwachung* für eine Datenbank aktiviert ist.
  - Die Tabellenüberwachung wurde eingestellt.
  - Es wird empfohlen, auf [Blobüberwachung](../../azure-sql/database/auditing-overview.md) umzusteigen.

## <a name="related-resources"></a>Zugehörige Ressourcen

- Informationen zur Notfallwiederherstellung finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- [Azure REST-API-Referenz](/rest/api/azure/)
- [API für Server-DNS-Aliase](/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>Nächste Schritte

- [PowerShell für den DNS-Alias für Azure SQL-Datenbank](dns-alias-powershell-create.md)