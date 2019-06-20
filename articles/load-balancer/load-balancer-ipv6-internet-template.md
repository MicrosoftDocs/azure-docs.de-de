---
title: Bereitstellen einer Load Balancer-Lösung mit Internetzugriff über IPv6 – Azure-Vorlage
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie IPv6-Unterstützung für Azure Load Balancer und virtuelle Computer mit Lastenausgleich bereitstellen.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, Azure Load Balancer, dualer Stapel, öffentliche IP, natives IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4a8c7309a07238ef3410e42c3d631ad525f023cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61216793"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Bereitstellen einer Load Balancer-Lösung mit Internetzugriff über IPv6 mithilfe einer Vorlage

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure-Befehlszeilenschnittstelle](load-balancer-ipv6-internet-cli.md)
> * [Vorlage](load-balancer-ipv6-internet-template.md)



Ein Azure Load Balancer ist ein Layer-4-Load Balancer (TCP, UDP). Der Load Balancer sorgt für Hochverfügbarkeit, indem er eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer verteilt, die in einer Gruppe für den Lastenausgleich definiert wurden. Der Azure Load Balancer kann diese Dienste auch auf mehreren Ports, mehreren IP-Adressen oder beidem leisten.

## <a name="example-deployment-scenario"></a>Beispielszenario für die Bereitstellung

Das folgende Diagramm veranschaulicht die Lösung mit Lastenausgleich, die mithilfe der in diesem Artikel beschriebenen Beispielvorlage bereitgestellt wird.

![Load Balancer-Szenarios](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

In diesem Szenario erstellen Sie die folgenden Azure-Ressourcen:

* eine virtuelle Netzwerkschnittstelle für jeden virtuellen Computer mit zugewiesenen IPv4- und IPv6-Adressen
* einen Load Balancer mit Internetzugriff mit je einer öffentlichen IPv4- und IPv6-IP-Adresse
* zwei Lastenausgleichsregeln, um die öffentlichen virtuellen IP-Adressen den privaten Endpunkten zuzuordnen
* eine Verfügbarkeitsgruppe, die die zwei virtuellen Computer enthält
* zwei virtuelle Computer (VMs)

## <a name="deploying-the-template-using-the-azure-portal"></a>Bereitstellen der Vorlage mit dem Azure-Portal

In diesem Artikel wird eine Vorlage verwendet, die im Katalog mit [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) veröffentlicht wurde. Sie können die Vorlage aus dem Katalog herunterladen oder die Bereitstellung in Azure direkt über den Katalog ausführen. In diesem Artikel wird davon ausgegangen, dass Sie die Vorlage auf den lokalen Computer heruntergeladen haben.

1. Öffnen Sie das Azure-Portal, und melden Sie sich mit einem Konto an, das über Berechtigungen zum Erstellen von virtuellen Computern und Netzwerkressourcen in einem Azure-Abonnement verfügt. Wenn Sie keine vorhandenen Ressourcen verwenden, benötigt das Konto darüber hinaus die Berechtigung zum Erstellen einer Ressourcengruppe und eines Speicherkontos.
2. Klicken Sie im Menü auf „+Neu“, und geben Sie „Vorlage“ in das Suchfeld ein. Wählen Sie in den Suchergebnissen „Vorlagenbereitstellung“ aus.

    ![LB-IPv6-Portal – Schritt 2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Klicken Sie auf dem Blatt „Alles“ auf „Vorlagenbereitstellung“.

    ![LB-IPv6-Portal – Schritt 3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klicken Sie auf „Erstellen“.

    ![LB-IPv6-Portal – Schritt 4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klicken Sie auf „Vorlage bearbeiten“. Löschen Sie den vorhandenen Inhalt, kopieren Sie den gesamten Inhalt der Vorlagendatei (einschließlich der Klammern { } am Anfang und Ende), und fügen Sie ihn ein. Klicken Sie dann auf „Speichern“.

    > [!NOTE]
    > Wenn Sie Microsoft Internet Explorer verwenden, wird beim Einfügen ein Dialogfeld angezeigt, mit dem Sie aufgefordert werden, Zugriff auf die Windows-Zwischenablage zuzulassen. Klicken Sie auf „Zugriff zulassen“.

    ![LB-IPv6-Portal – Schritt 5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klicken Sie auf „Parameter bearbeiten“. Geben Sie auf dem Blatt „Parameter“ die Werte gemäß der Anleitung im Abschnitt mit Vorlagenparametern an, und klicken Sie auf „Speichern“, um das Blatt „Parameter“ zu schließen. Wählen Sie auf dem Blatt „Benutzerdefinierte Bereitstellung“ Ihr Abonnement und eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine. Wenn Sie eine Ressourcengruppe erstellen, wählen Sie einen Ort für die Ressourcengruppe aus. Klicken Sie anschließend auf **Rechtliche Bedingungen** und dann für die rechtlichen Bedingungen auf **Kaufen**. Azure beginnt mit der Bereitstellung der Ressourcen. Es dauert einige Minuten, alle Ressourcen bereitzustellen.

    ![LB-IPv6-Portal – Schritt 6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Weitere Informationen zu diesen Parametern finden Sie im Abschnitt [Vorlagenparameter und -variablen](#template-parameters-and-variables) weiter unten in diesem Artikel.

7. Um die von der Vorlage erstellten Ressourcen anzuzeigen, klicken Sie auf „Durchsuchen“, scrollen Sie in der Liste nach unten, bis „Ressourcengruppen“ angezeigt wird, und klicken Sie darauf.

    ![LB-IPv6-Portal – Schritt 7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Klicken Sie auf dem Blatt „Ressourcengruppen“ auf den Namen der Ressourcengruppe, die Sie in Schritt 6 angegeben haben. Eine Liste aller Ressourcen, die bereitgestellt wurden, wird angezeigt. Wenn alles erfolgreich verlaufen ist, sollte unter „Letzte Bereitstellung“ der Status „Erfolgreich“ angezeigt werden. Ist dies nicht der Fall, stellen Sie sicher, dass das verwendete Konto über Berechtigungen zum Erstellen der erforderlichen Ressourcen verfügt.

    ![LB-IPv6-Portal – Schritt 8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Wenn Sie Ihre Ressourcengruppen sofort nach Abschluss von Schritt 6 durchsuchen, wird unter „Letzte Bereitstellung“ der Status „Wird bereitgestellt“ angezeigt, während die Ressourcen bereitgestellt werden.

9. Klicken Sie in der Liste der Ressourcen auf „myIPv6PublicIP“. Sie sehen, dass sie als IP-Adresse eine IPv6-Adresse aufweist und dass der DNS-Name der Wert ist, den Sie für den Parameter „dnsNameforIPv6LbIP“ in Schritt 6 angegeben haben. Diese Ressource ist die öffentliche IPv6-Adresse und der Hostname, auf den Internetclients zugreifen können.

    ![LB-IPv6-Portal – Schritt 9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Wenn die Vorlage erfolgreich bereitgestellt wurde, können Sie die Konnektivität überprüfen, indem Sie die folgenden Aufgaben ausführen:

1. Melden Sie sich beim Azure-Portal an, und stellen Sie eine Verbindung mit den einzelnen durch die Vorlagenbereitstellung erstellten virtuellen Computern her. Wenn Sie einen virtuellen Windows Server-Computer bereitgestellt haben, führen Sie an einer Eingabeaufforderung „ ipconfig /all“ aus. Sie sehen, dass die virtuellen Computer sowohl IPv4- als auch IPv6-Adressen aufweisen. Wenn Sie virtuelle Linux-Computer bereitgestellt haben, müssen Sie das Linux-Betriebssystem mithilfe der Anweisungen für Ihre Linux-Distribution zum Empfangen von dynamischen IPv6-Adressen konfigurieren.
2. Initiieren Sie über einen IPv6-Client mit Internetverbindung eine Verbindung mit der öffentlichen IPv6-Adresse des Load Balancers. Um zu überprüfen, ob der Load Balancer für Lastenausgleich zwischen den beiden virtuellen Computern sorgt, könnten Sie einen Webserver wie Microsoft-Internetinformationsdienste (IIS) auf jedem virtuellen Computer installieren. Die Standardwebseite auf jedem Server kann zur eindeutigen Identifizierung den Text „Server0“ oder „Server1“ enthalten. Öffnen Sie anschließend einen Internetbrowser auf einem IPv6-Client mit Internetverbindung, und navigieren Sie zum Hostnamen, den Sie für den Parameter „dnsNameforIPv6LbIP“ des Load Balancers angegeben haben, um die End-to-End-IPv6-Konnektivität auf jedem virtuellen Computer zu überprüfen. Wenn Sie nur die Webseite von nur einem Server sehen, müssen Sie möglicherweise den Browsercache löschen. Öffnen Sie mehrere private Browsersitzungen. Eine Antwort von jedem Server sollte angezeigt werden.
3. Initiieren Sie über einen IPv4-Client mit Internetverbindung eine Verbindung mit der öffentlichen IPv4-Adresse des Load Balancers. Um zu überprüfen, ob der Load Balancer für den Lastenausgleich zwischen den beiden virtuellen Computer sorgt, könnten Sie dies mit IIS testen, wie in Schritt 2 dargestellt.
4. Starten Sie auf jedem virtuellen Computer eine ausgehende Verbindung mit einem Internetgerät mit IPv6- oder IPv4-Verbindung. In beiden Fällen ist die Quell-IP, die auf dem Zielgerät angezeigt wird, die öffentliche IPv4- oder IPv6-Adresse des Load Balancers.

> [!NOTE]
> ICMP ist für IPv4 und IPv6 im Azure-Netzwerk blockiert. Folglich schlagen ICMP-Tools wie „ping“ immer fehl. Verwenden Sie zum Testen der Konnektivität eine TCP-Alternative wie „TCPing“ oder das PowerShell-Cmdlet „Test-NetConnection“. Beachten Sie, dass die in der Abbildung dargestellten IP-Adressen Beispiele für Werte sind, die Ihnen möglicherweise angezeigt werden. Da die IPv6-Adressen dynamisch zugewiesen werden, sind die Adressen, die Sie erhalten, anders und können je nach Region variieren. Darüber hinaus ist es üblich, dass die öffentlichen IPv6-Adressen des Load Balancers mit einem anderen Präfix beginnen als die privaten IPv6-Adressen im Back-End-Pool.

## <a name="template-parameters-and-variables"></a>Vorlagenparameter und -variablen

Eine Azure Resource Manager-Vorlage enthält mehrere Variablen und Parameter, die Sie an Ihre Bedürfnisse anpassen können. Variablen werden für feste Werte verwendet, die ein Benutzer nicht ändern soll. Parameter werden für Werte verwendet, die ein Benutzer angeben soll, wenn die Vorlage bereitgestellt wird. Die Beispielvorlage ist für das in diesem Artikel beschriebene Szenario konfiguriert. Sie können sie an die Bedürfnisse Ihrer Umgebung anpassen.

Die in diesem Artikel verwendete Beispielvorlage enthält die folgenden Variablen und Parameter:

| Parameter/Variable | Notizen |
| --- | --- |
| adminUsername |Geben Sie den Namen des Administratorkontos an, das zum Anmelden an virtuellen Computern verwendet wird. |
| adminPassword |Geben Sie das Kennwort für das Administratorkonto an, das zum Anmelden an virtuellen Computern verwendet wird. |
| dnsNameforIPv4LbIP |Geben Sie den DNS-Hostnamen an, den Sie als öffentlichen Namen des Load Balancers zuweisen möchten. Dieser Name wird in die öffentliche IPv4-Adresse des Load Balancers aufgelöst. Der Name muss in Kleinbuchstaben geschrieben sein und mit dem regulären Ausdruck übereinstimmen: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Geben Sie den DNS-Hostnamen an, den Sie als öffentlichen Namen des Load Balancers zuweisen möchten. Dieser Name wird in die öffentliche IPv6-Adresse des Load Balancers aufgelöst. Der Name muss in Kleinbuchstaben geschrieben sein und mit dem regulären Ausdruck übereinstimmen: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. Dies kann der gleiche Name wie die IPv4-Adresse sein. Wenn ein Client eine DNS-Abfrage für diesen Namen sendet, gibt Azure die A- und AAAA-Einträge zurück, wenn der gleiche Name verwendet wird. |
| vmNamePrefix |Geben Sie das Präfix für den Namen des virtuellen Computers an. Die Vorlage fügt eine Zahl (0, 1 usw.) an den Namen an, wenn die virtuellen Computer erstellt werden. |
| nicNamePrefix |Geben Sie das Präfix für den Namen der Netzwerkschnittstelle an. Die Vorlage fügt eine Zahl (0, 1 usw.) an den Namen an, wenn die Netzwerkschnittstellen erstellt werden. |
| storageAccountName |Geben Sie den Namen eines vorhandenen Speicherkontos an, oder geben Sie den Namen eines neuen Speicherkontos an, das von der Vorlage erstellt werden soll. |
| availabilitySetName |Geben Sie dann die Namen der Verfügbarkeitsgruppe an, die mit den virtuellen Computern verwendet werden soll. |
| addressPrefix |Das Adresspräfix, das verwendet, um den Adressbereich des virtuellen Netzwerks zu definieren |
| subnetName |Der Name des Subnetzes, das für das VNet erstellt wird |
| subnetPrefix |Das Adresspräfix, das verwendet, um den Adressbereich des Subnetzes zu definieren |
| vnetName |Geben Sie den Namen für das VNet an, das von den virtuellen Computern verwendet wird. |
| ipv4PrivateIPAddressType |Die Zuordnungsmethode, die für die private IP-Adresse verwendet wird (statisch oder dynamisch) |
| ipv6PrivateIPAddressType |Die Zuordnungsmethode, die für die private IP-Adresse verwendet wird (dynamisch) IPv6 unterstützt nur die dynamische Zuordnung. |
| numberOfInstances |Die Anzahl der Load Balancer-Instanzen, die von der Vorlage bereitgestellt werden |
| ipv4PublicIPAddressName |Geben Sie den DNS-Namen an, den Sie zur Kommunikation mit der öffentlichen IPv4-Adresse des Load Balancers verwenden möchten. |
| ipv4PublicIPAddressType |Die Zuordnungsmethode, die für die öffentliche IP-Adresse verwendet wird (statisch oder dynamisch) |
| Ipv6PublicIPAddressName |Geben Sie den DNS-Namen an, den Sie zur Kommunikation mit der öffentlichen IPv6-Adresse des Load Balancers verwenden möchten. |
| ipv6PublicIPAddressType |Die Zuordnungsmethode, die für die öffentliche IP-Adresse verwendet wird (dynamisch) IPv6 unterstützt nur die dynamische Zuordnung. |
| lbName |Geben Sie den Namen des Load Balancers an. Dieser Name wird im Portal angezeigt oder verwendet, wenn darauf mit einem CLI- oder PowerShell-Befehl verwiesen wird. |

Die restlichen Variablen in der Vorlage enthalten abgeleitete Werte, die zugewiesen werden, wenn Azure die Ressourcen erstellt. Ändern Sie diese Variablen nicht.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur JSON-Syntax und den Eigenschaften eines Load Balancers in einer Vorlage finden Sie unter [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
