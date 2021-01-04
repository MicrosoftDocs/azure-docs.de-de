---
title: Problembehandlung beim Status "Heruntergestuft" in Azure Traffic Manager
description: Problembehandlung bei Traffic Manager-Profilen, bei denen der Status "Heruntergestuft" angezeigt wird.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: duau
ms.openlocfilehash: 83dc432a1f88b443d500bf9a977abfed69211156
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003853"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Problembehandlung beim Status "Heruntergestuft" in Traffic Manager

In diesem Artikel wird beschrieben, wie Probleme bei einem Azure Traffic Manager-Profil mit dem Status „Heruntergestuft“ behoben werden können. Der erste Schritt bei der Problembehandlung eines beeinträchtigten Zustands von Azure Traffic Manager besteht darin, die Protokollierung zu aktivieren.  Weitere Informationen erhalten Sie unter [Aktivieren der Ressourcenprotokollierung in Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs). Für dieses Szenario nehmen wir an, Sie haben ein Traffic Manager-Profil konfiguriert, das auf einige Ihrer in cloudapp.net gehosteten Dienste verweist. Wenn die Integrität von Traffic Manager den Status **Heruntergestuft** anzeigt, könnte der Status einer oder mehrerer Endpunkte **Heruntergestuft** sein:

![Heruntergestufter Endpunktstatus](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Wenn die Integrität von Traffic Manager den Status **Inaktiv** anzeigt, sind möglicherweise beide Endpunkte **Deaktiviert**:

![Traffic Manager-Status Inaktiv](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Hinweise zu Traffic Manager-Tests

* In Traffic Manager wird ein Endpunkt nur als ONLINE eingestuft, wenn beim Test eine Antwort „200“ vom Testpfad zurückgegeben wird. Wenn Ihre Anwendung einen anderen HTTP-Antwortcode zurückgibt, sollten Sie diesen Antwortcode zu den [erwarteten Statuscodebereichen](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) Ihres Traffic Manager-Profils hinzufügen.
* Eine 30x-Umleitungsantwort wird als Fehler behandelt, es sei denn, Sie haben sie in den [erwarteten Statuscodebereichen](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) Ihres Traffic Manager-Profils als gültigen Antwortcode angegeben. Traffic Manager testet das Umleitungsziel nicht.
* Bei HTTPs-Tests werden Zertifikatfehler ignoriert.
* Der eigentliche Inhalt des Testpfads ist unerheblich, solange eine Antwort „200“ zurückgegeben wird. Der Test einer URL mit statischem Inhalt wie „/favicon.ico“ ist eine gängige Methode. Dynamische Inhalte wie die ASP-Seiten geben möglicherweise nicht immer „200“ zurück, auch wenn die Anwendung fehlerfrei ist.
* Es wird empfohlen, den Testpfad mit ausreichender Logik festzulegen, sodass ermittelt werden kann, ob die Website aktiv oder inaktiv ist. Durch Festlegen des Pfads im Beispiel oben auf „/favicon.ico“ wird lediglich getestet, ob „w3wp.exe“ antwortet. Mit diesem Test wird jedoch nicht getestet, ob die Webanwendung fehlerfrei ist. Besser wäre es, den Pfad beispielsweise auf „/Probe.aspx“ mit der entsprechenden Logik festzulegen, sodass ermittelt wird, ob die Website fehlerfrei ist. So könnten Sie beispielsweise Leistungsindikatoren für die CPU-Auslastung verwenden oder die Anzahl der fehlgeschlagenen Anforderungen messen. Oder Sie könnten versuchen, auf Datenbankressourcen oder Sitzungszustand zuzugreifen, um sicherzustellen, dass die Webanwendung funktioniert.
* Wenn alle Endpunkte in einem Profil heruntergestuft sind, behandelt Traffic Manager alle Endpunkte als fehlerfrei und leitet den Datenverkehr an alle Endpunkte weiter. Dadurch wird sichergestellt, dass Probleme mit dem Testmechanismus nicht zu einem vollständigen Ausfall des Diensts führen.

## <a name="troubleshooting"></a>Problembehandlung

Zum Beheben eines Testfehlers wird ein Tool benötigt, mit dem der von der Test-URL zurückgegebene HTTP-Statuscode angezeigt wird. Es gibt eine ganze Reihe von Tools, mit denen die unverarbeitete HTTP-Antwort angezeigt werden kann.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Sie können aber auch die Registerkarte „Netzwerk“ der F12-Tools zum Debuggen in Internet Explorer verwenden, um die HTTP-Antworten anzuzeigen.

In diesem Beispiel soll die Antwort der folgenden Test-URL angezeigt werden: http:\//watestsdp2008r2.cloudapp.net:80/Probe. Das folgende PowerShell-Beispiel dient als Veranschaulichung des Problems.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Beispielausgabe:

```output
StatusCode StatusDescription
---------- -----------------
        301 Moved Permanently
```

Wie Sie sehen, haben wir eine Umleitungsantwort erhalten. Wie bereits erwähnt, gelten alle Statuscodes außer „200“ als Fehler. Somit ändert Traffic Manager den Endpunktstatus in „Offline“. Sie können dieses Problem beheben, indem Sie die Konfiguration der Website überprüfen, um sicherzustellen, dass vom Testpfad der richtige Statuscode zurückgegeben wird. Konfigurieren Sie den Traffic Manager-Test so, dass er auf einen Pfad verweist, der eine Antwort „200“ zurückgibt.

Wenn bei dem Test das HTTPS-Protokoll verwendet wird, müssen Sie möglicherweise die Zertifikatsüberprüfung deaktivieren, um SSL/TLS-Fehler während des Tests zu vermeiden. Mit den folgenden PowerShell-Anweisungen können Sie die Zertifikatsüberprüfung für die aktuelle PowerShell-Sitzung deaktivieren:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Nächste Schritte

[Informationen zu Traffic Manager-Routingmethoden für Datenverkehr](traffic-manager-routing-methods.md)

[Was ist Traffic Manager?](traffic-manager-overview.md)

[Cloud Services](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Vorgänge für Traffic Manager (REST-API-Referenz)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-Cmdlets][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
