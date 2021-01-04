---
title: Migrieren von älteren Azure DNS Private Zones zum neuen Ressourcenmodell
titleSuffix: Azure DNS
description: In dieser Anleitung wird Schritt für Schritt erläutert, wie ältere private DNS-Zonen zum aktuellen Ressourcenmodell migriert werden.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 63bd1d01adf66f33a8ee5349f35063473429b007
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964778"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrieren von älteren Azure DNS Private Zones zum neuen Ressourcenmodell

Während der öffentlichen Vorschauphase wurden private DNS-Zonen erstellt, indem die Ressource „dnszones“ mit Festlegung der „zoneType“-Eigenschaft auf „Private“ verwendet wurde. Zonen dieser Art werden nach dem 31. Dezember 2019 nicht mehr unterstützt und müssen zum allgemein verfügbaren Ressourcenmodell migriert werden, bei dem anstelle von „dnszones“ der Ressourcentyp „privateDnsZones“ verwendet wird. Der Migrationsprozess ist einfach, und wir haben ein PowerShell-Skript bereitgestellt, um diesen Prozess zu automatisieren. In dieser Anleitung wird Schritt für Schritt beschrieben, wie Sie Azure DNS Private Zones zum neuen Ressourcenmodell migrieren.

Führen Sie den unten angegebenen Befehl in der Azure CLI aus, um die Ressourcen von „dnszones“ zu ermitteln, für die eine Migration erforderlich ist.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die aktuelle Version von Azure PowerShell installiert haben. Weitere Informationen zu Azure PowerShell (Az) und zur Installation finden Sie unter https://docs.microsoft.com/powershell/azure/new-azureps-module-az.

Vergewissern Sie sich, dass das Az.PrivateDns-Modul für Azure PowerShell installiert ist. Um dieses Modul zu installieren, öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten (Administratormodus), und geben Sie folgenden Befehl ein:

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Der Migrationsprozess ist vollständig automatisiert. Es wird nicht davon ausgegangen, dass Ausfallzeiten entstehen. Wenn Sie jedoch Azure DNS Private Zones (Vorschauversion) in einer wichtigen Produktionsumgebung verwenden, sollten Sie den nachfolgenden Migrationsprozess in einem geplanten Wartungszeitfenster ausführen. Ändern Sie die Konfiguration und die Datensatzgruppen der privaten DNS-Zonen nicht, während das Migrationsskript ausgeführt wird.

## <a name="installing-the-script"></a>Installieren des Skripts

Öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten (Administratormodus), und führen Sie folgenden Befehl aus:

```powershell
install-script PrivateDnsMigrationScript
```

Geben Sie „A“ ein, wenn Sie zur Installation des Skripts aufgefordert werden.

![Installieren des Skripts](./media/private-dns-migration-guide/install-migration-script.png)

Sie können die aktuelle Version des PowerShell-Skripts unter https://www.powershellgallery.com/packages/PrivateDnsMigrationScript auch manuell abrufen.

>[!IMPORTANT]
>Das Migrationsskript darf nicht in der Azure Cloud Shell ausgeführt werden, sondern dies muss auf einer VM oder einem lokalen Computer mit Internetverbindung erfolgen.

## <a name="running-the-script"></a>Ausführen des Skripts

Führen Sie den folgenden Befehl aus, um das Skript auszuführen.

```powershell
PrivateDnsMigrationScript.ps1
```

![Ausführen des Skripts](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Geben Sie die Abonnement-ID ein, und melden Sie sich bei Azure an.

Sie werden aufgefordert, die ID des Abonnements einzugeben, das die zu migrierenden privaten DNS-Zonen umfasst. Sie werden aufgefordert, sich bei Ihrem Azure-Konto anzumelden. Schließen Sie den Anmeldevorgang ab, sodass das Skript auf die privaten DNS-Zonenressourcen im Abonnement zugreifen kann.

![Anmelden an Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Auswählen der zu migrierenden DNS-Zonen

Das Skript ruft die Liste aller privaten DNS-Zonen im Abonnement ab und fordert Sie auf, die Zonen zu bestätigen, die migriert werden sollen. Geben Sie „A“ ein, um alle privaten DNS-Zonen zu migrieren. Nachdem Sie diesen Schritt ausgeführt haben, erstellt das Skript unter Verwendung des neuen Ressourcenmodells neue private DSN-Zonen und kopiert die Daten in die neuen DNS-Zonen. Durch diesen Schritt verändern sich Ihre vorhandenen privaten DNS-Zonen in keiner Weise.

![Auswählen der DNS-Zonen](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Umstellen der DNS-Auflösung auf die neuen DNS-Zonen

Nachdem die Zonen und Datensätze in das neue Ressourcenmodell kopiert wurden, werden Sie vom Skript aufgefordert, die DNS-Auflösung auf die neuen DNS-Zonen umzustellen. In diesem Schritt wird die Zuordnung zwischen den älteren privaten DNS-Zonen und Ihren virtuellen Netzwerken entfernt. Wenn die Verknüpfung der älteren Zonen mit den virtuellen Netzwerken aufgehoben wurde, übernehmen die im vorherigen Schritt erstellten neuen DNS-Zonen automatisch die DNS-Auflösung für diese virtuellen Netzwerke.

Wählen Sie „A“ aus, um die DNS-Auflösung für alle virtuellen Netzwerke umzustellen.

![Umstellen der Namensauflösung](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Überprüfen der DNS-Auflösung

Überprüfen Sie, ob die DNS-Auflösung für die DNS-Zonen wie erwartet funktioniert, bevor Sie fortfahren. Sie können sich bei den virtuellen Azure-Computern anmelden und nslookup-Abfragen für die migrierten Zonen ausführen, um zu überprüfen, ob die DNS-Auflösung funktioniert.

![Überprüfen der Namensauflösung](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Wenn Sie feststellen, dass die DNS-Abfragen nicht aufgelöst werden, warten Sie einige Minuten, und wiederholen Sie dann die Abfragen. Wenn die DNS-Abfragen wie erwartet ausgeführt werden, geben Sie „Y“ ein, wenn Sie aufgefordert werden, das virtuelle Netzwerk aus der privaten DNS-Zone zu entfernen.

![Überprüfen der Namensauflösung](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Wenn die DNS-Auflösung für die migrierten Zonen aus bestimmten Gründen nicht wie erwartet funktioniert, geben Sie im obigen Schritt „N“ ein. Das Skript stellt dann die DNS-Auflösung wieder auf die Legacyzonen um. Erstellen Sie ein Supportticket, dann helfen wir Ihnen bei der Migration Ihrer DNS-Zonen.

## <a name="cleanup"></a>Cleanup

In diesem Schritt werden die älteren DNS-Zonen entfernt. Dieser Schritt sollte nur ausgeführt werden, nachdem Sie überprüft haben, ob die DNS-Auflösung wie erwartet funktioniert. Sie werden aufgefordert, jede private DNS-Zone zu löschen. Geben Sie bei jeder Aufforderung „Y“ ein, nachdem Sie überprüft haben, ob die DNS-Auflösung für die entsprechende Zone ordnungsgemäß funktioniert.

![Bereinigung](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Aktualisieren der Automatisierung

Wenn Sie die Automatisierung mit Vorlagen, PowerShell-Skripts oder benutzerdefiniertem mit dem SDK entwickelten Code verwenden, müssen Sie Ihre Automatisierung so aktualisieren, dass das neue Ressourcenmodell für die privaten DNS-Zonen verwendet wird. Nachfolgend finden Sie Links zur Dokumentation für die Befehlszeilenschnittstelle, PowerShell und das SDK für die neuen DNS-Zonen.
* [Azure DNS Private Zones-REST-API](/rest/api/dns/privatedns/privatezones)
* [Azure DNS Private Zones-Befehlszeilenschnittstelle](/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Azure DNS Private Zones-PowerShell](/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Azure DNS Private Zones-SDK](/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Benötigen Sie weitere Hilfe?

Erstellen Sie ein Supportticket, wenn Sie weitere Hilfe beim Migrationsprozess benötigen oder einen der oben beschriebenen Schritte aus bestimmten Gründen nicht ausführen können. Fügen Sie die vom PowerShell-Skript generierte Transkriptdatei in das Supportticket ein.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie über [Azure PowerShell](./private-dns-getstarted-powershell.md) oder die [Azure-Befehlszeilenschnittstelle](./private-dns-getstarted-cli.md) eine private Zone in Azure DNS erstellen.

* Informieren Sie sich über einige gängige [Szenarien zu privaten Zonen](./private-dns-scenarios.md), die mit privaten Zonen in Azure DNS realisiert werden können.

* Allgemeine Fragen und Antworten zu privaten Zonen in Azure DNS, einschließlich des spezifischen Verhaltens für bestimmte Vorgänge, finden Sie unter [Häufig gestellte Fragen zu privatem Azure DNS](./dns-faq-private.md).

* Weitere Informationen zu DNS-Zonen und -Einträgen finden Sie unter [Übersicht über DNS-Zonen und -Einträge](dns-zones-records.md).

* Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/networking-overview.md) von Azure.