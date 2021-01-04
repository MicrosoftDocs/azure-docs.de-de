---
title: Häufig gestellte Fragen zur Verwendung von ClearDB MySql-Datenbanken mit Azure App Service
description: Antworten auf häufig gestellte Fragen zur Verwendung von ClearDB MySQL-Datenbanken mit Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: 25cc4dfaabee58a47cc33fe4be8dc7375bc804d2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491641"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Häufig gestellte Fragen zur Verwendung von ClearDB MySql-Datenbanken mit Azure App Service
In diesem Dokument werden häufige gestellte Fragen zu Verwendung und Erwerb von ClearDB MySQL-Datenbanken für Azure-Web-Apps beantwortet.

> [!IMPORTANT]
> Am 13. Juni 2018 wurden Azure-basierte Kunden, die zurzeit von Microsoft abgerechnet werden, in ein direktes Abrechnungsmodell mit ClearDB überführt. Die Informationen in diesem Artikel sind jetzt veraltet. Sie können keine ClearDB-Datenbanken mehr erstellen und in Azure erstellte ClearDB-Datenbanken nicht mehr aktualisieren.
>
> Weitere Details und die nächsten Schritte finden Sie unter [Änderungen an den ClearDB-Serviceplänen](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Welche Optionen habe ich für MySQL in Azure?
Unter [ClearDB](https://w2.cleardb.net/) finden Sie die neuesten Informationen zu diesem Dienst. ClearDB ist ein MySQL-Hostingdienst für die Verwaltung Ihrer MySQL-Infrastruktur. 

Sie haben mehrere andere Optionen für das Hosten von MySQL in Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [In einer Azure-VM ausgeführter MySQL-Cluster](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [In einer Azure-VM ausgeführte MySQL-Einzelinstanz](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Benötige ich eine Kreditkarte für die Web-App und MySQL-Vorlage in Azure Marketplace?
Dies hängt vom Typ Ihres Abonnements ab. Hier einige gängige Abonnementtypen:

* [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/): Erfordert eine Kreditkarte. Wenn Sie eine kostenpflichtige MySQL-Datenbank erwerben, wird die Kreditkarte belastet.
* [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/): Bietet Guthaben für die Nutzung mit Microsoft Azure-Diensten, lässt aber nicht den Erwerb von Ressourcen von Drittanbietern zu. Zum Erwerben von Diensten von Drittanbietern oder einer kostenpflichtigen MySQL-Datenbank benötigen Sie ein kreditkartengestütztes Abonnement. Für Web-Apps können Sie eine ClearDB MySQL-Datenbank im Tarif FREE erstellen.
* [MSDN-Abonnement](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) und **MSDN Dev/Test Pay-As-You-Go**: Wie die kostenlose Testversion erfordert ein MSDN-Abonnement, dass Sie über eine Kreditkarte verfügen, um eine kostenpflichtige MySQL-Lösung von ClearDB zu erwerben.
* [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA-Kunden erhalten im Rahmen ihres Vertrags vierteljährlich eine Sammelrechnung für alle Käufe (bei Drittanbietern) über den Azure Marketplace. Die Rechnungsstellung für alle Marketplace-Einkäufe erfolgt außerhalb der Zahlungsverpflichtung. Beachten Sie, dass der Azure Store nicht für Kunden verfügbar ist, die in Aserbaidschan, Kroatien, Norwegen und Puerto Rico registriert sind. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Warum wurden mir 3,50 $ für eine Web-App und MySQL-Vorlage aus Azure Marketplace in Rechnung gestellt?
Die Standardoption Datenbanken ist Titan zum Preis von 3,50 $. Während der Datenbankerstellung zeigen wir die Preise nicht an, und Sie haben ggf. versehentlich eine Datenbank erworben, die Sie nicht wollten. Wir suchen derzeit nach einer Verbesserungsmöglichkeit. Bis dahin müssen Sie alle für die Web-App und Datenbank ausgewählten Tarife prüfen, bevor Sie auf **Erstellen** klicken und mit der Bereitstellung der Ressourcen beginnen.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Ich führe MySQL auf meiner eigenen Azure-VM aus. Kann ich meine Azure-Web-App mit meiner Datenbank verbinden?
Ja. Solange Ihrer Azure-VM Remotezugriff auf Ihre Web-App gewährt wird, können Sie Ihre Web-App mit Ihrer Datenbank verbinden. Weitere Informationen finden Sie unter [Installieren von MySQL auf einem virtuellen Computer](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>In welchen Ländern/Regionen werden ClearDB Premium MySQL-Cluster unterstützt?
ClearDB Premium MySQL-Cluster sind in allen Azure-Regionen weltweit mit Ausnahme von Indien, Australien, Brasilien Süden und China verfügbar.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kann ich vor dem Erstellen einer Datenbank mit einer ClearDB Premium-Clusterlösung einen neuen Cluster erstellen?
Nein, das Erstellen leerer ClearDB-Cluster wird nicht unterstützt. Im Azure-Portal können Sie Datenbanken in einem Cluster erstellen, wobei ggf. gleichzeitig ein neuer Cluster erstellt wird.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Werde ich gewarnt, falls ich versuche, eine ClearDB MySQL-Datenbank zu löschen, die von einer meiner Anwendungen verwendet wird?
Nein, Azure warnt Sie nicht, falls Sie einen Marketplace-Einkauf löschen, von dem Ihre Anwendung abhängt.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>In welchen Regionen können ClearDB-Datenbanken erstellt werden?
Azure Marketplace ist nicht für Kunden verfügbar, die in Aserbaidschan, Kroatien, Norwegen und Puerto Rico registriert sind. ClearDB ist in diesen Regionen nicht verfügbar.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Welche Tarif sollte ich für eine Produktions-Web-App und Datenbank wählen?
Wählen Sie für Web-Apps mindestens den Tarif Basic. Für ClearDB empfehlen wir den Tarif Saturn oder Jupiter. Machen Sie sich mit den Features und Grenzen der einzelnen Tarife sowohl für [Web-Apps](https://azure.microsoft.com/pricing/details/app-service/) als auch für [ClearDB MySQL-Datenbanken](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) vertraut, um den für Sie am besten geeigneten Tarif zu ermitteln.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Wie kann ich meinen ClearDB-Datenbanktarif ändern?
Im [Azure-Portal](https://portal.azure.com) können Sie eine ClearDB-Datenbank mit freigegebenem Hosting hochskalieren. Weitere Informationen finden Sie in [diesem Artikel](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/). Derzeit wird für ClearDB Premium-Cluster im Azure-Portal kein Upgrade unterstützt.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Warum ist meine ClearDB-Datenbank im Azure-Portal nicht sichtbar?
Wenn Sie die ClearDB-Datenbank auf klassische Weise erstellt haben, können Sie die Datenbank nicht im [Azure-Portal](https://portal.azure.com) sehen. Für dieses Szenario gibt es keine Problemumgehung.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Wen bitte ich um Unterstützung, wenn meine Datenbank ausfällt?
Wenden Sie sich bei sämtlichen Datenbankproblemen an den [ClearDB-Support](https://www.cleardb.com/developers/help/support) . Stellen Sie bei Aufforderung Ihre Azure-Abonnementdaten bereit.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kann ich für meine ClearDB MySQL-Datenbank-Clusterlösung zusätzliche Benutzer erstellen?
Nein. Zusätzliche Benutzer können nicht erstellt werden. Sie können aber zusätzliche Datenbanken in Ihrem ClearDB-Datenbankcluster erstellen.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Können Datenbanken der Basic- oder Pro-Serie direkt im ClearDB-Portal aktualisiert werden, ähnlich wie es mit Planetary-Plänen bereits möglich ist?
Ja, Datenbanken der Basic-Serie können direkt aktualisiert werden (Basic 60 bis Basic 500). Datenbanken der Pro-Serie können mit Ausnahme von Pro 60 direkt aktualisiert werden (Pro 125 bis Pro 1000). Ein Upgrade von Pro 60-Datenbanken wird derzeit nicht unterstützt. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Wird meine ClearDB MySQL-Datenbank ebenfalls migriert, wenn ich meine Ressourcen von einem Abonnement zu einem anderen migriere?
Bei der Ressourcenmigration zwischen Abonnements gelten einige [Einschränkungen](azure-resource-manager/management/move-support-resources.md) . Eine ClearDB MySQL-Datenbank ist ein Drittanbieterdienst und wird daher nicht migriert, wenn Sie eine Migration zwischen Azure-Abonnements durchführen. Ihre ClearDB MySQL-Datenbank wird möglicherweise deaktiviert, wenn Sie die Migration der MySQL-Datenbank nicht vor der Migration der Azure-Ressourcen planen und durchführen. Migrieren Sie zunächst manuell Ihre Datenbanken, und führen Sie dann die Azure-Abonnementmigration für Ihre Web-App durch. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Ich habe das Ausgabenlimit für mein Abonnement erreicht. Ich habe das Limit entfernt und mein App Service ist online, aber der Zugriff auf die Datenbank ist nicht möglich. Wie aktiviere ich die ClearDB-Datenbank erneut?
Wenden Sie sich an den [ClearDB-Support](https://www.cleardb.com/developers/help/support), um die Datenbank erneut zu aktivieren. Stellen Sie Ihre Azure-Abonnementdaten und den Datenbanknamen bereit.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kann ich eine ClearDB-Datenbank aus einem kreditkartengestützten Abonnement in ein EA-Abonnement übertragen?
Für vorhandene ClearDB-Datenbanken wird die Kreditkarte genutzt, die den bestehenden Abonnements zugeordnet ist. Wenn Sie ein EA-Abonnement verwenden möchten, müssen Sie Ihre Daten in eine neue Datenbank migrieren:

* Erwerben Sie in Ihrem EA-Abonnement eine neue ClearDB-Datenbank.
* Migrieren Sie Ihre Daten in die neue Datenbank.
* Ändern Sie Ihre Anwendung so, dass die neue Datenbank verwendet wird.
* Löschen Sie Ihre alte ClearDB-Datenbank.

Wenn Sie eine neue Web-App mit MySQL (ClearDB) oder eine MySQL-Datenbank (ClearDB) erstellen, richtet sich die Zahlungsweise für den Dienst nach dem gewählten Abonnement. Bei einem EA-Abonnement wird die Bereitstellung von Drittanbieterdiensten, z.B. ClearDB, im Azure-Portal nicht blockiert. Für EA-Abonnements erfolgt die Berechnung außerhalb der Zahlungsverpflichtung und nachträglich pro Vierteljahr. EA-Kunden müssen eine Zahlungsmethode, z.B. Kreditkarte, für alle im Marketplace erworbenen Dienste von Drittanbietern festlegen.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Wo kann ich die Kosten für ClearDB-Ressourcen in einem EA-Abonnement finden?
Direkten EA-Kunden werden die Azure Marketplace-Gebühren im Enterprise Portal angezeigt. Beachten Sie, dass alle Marketplace-Käufe und deren Nutzung außerhalb der Zahlungsverpflichtung abgerechnet werden, wobei die Rechnungsstellung vierteljährlich und nachträglich erfolgt. EA-Kunden müssen Dienste von Drittanbietern direkt zahlen, was möglich ist, indem sie für ihr EA-Konto eine Zahlungsmethode, z.B. eine Kreditkarte, einrichten.

Indirekte EA-Kunden finden ihre Azure Marketplace-Abonnements im Enterprise Portal auf der Seite **Abonnements verwalten** , wobei die Preise jedoch ausgeblendet sind. Kunden sollten ihren Lizenzierungsdienstanbieter zwecks Informationen zu Marketplace-Gebühren kontaktieren.

Der Zugriff auf Azure Marketplace für Dienste von Drittanbietern kann von Ihren EA-Administratoren für die Azure-Registrierung verwaltet werden. Sie können den Zugriff auf Drittanbieterkäufe über den Store im Enterprise Portal im Abschnitt „Konten“ unter „Konten verwalten“ und „Abonnements“ deaktivieren bzw. erneut aktivieren.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>An wen wende ich mich bei Fragen zu meiner Rechnung für ClearDB-Dienste in meinem EA-Abonnement?
Wenden Sie sich an den [Support für EA-Kunden](https://aka.ms/AzureEntSupport) , wenn Sie Fragen zur Abrechnung über Ihre EA-Registrierung haben. Der Supportteam des EA-Portals beantwortet Ihre Fragen und hilft beim Lösen Ihres Problems.

## <a name="more-information"></a>Weitere Informationen
[Azure Marketplace – häufig gestellte Fragen](https://azure.microsoft.com/marketplace/faq/)