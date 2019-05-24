---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114673"
---
Der Speicheremulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel für die Authentifizierung mit einem gemeinsam verwendeten Schlüssel. Dieses Konto und dieser Schlüssel sind die einzigen gemeinsam verwendeten Anmeldeinformationen, die für den Speicheremulator verwendet werden dürfen. Sie lauten wie folgt:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Der vom Speicheremulator unterstützte Authentifizierungsschlüssel ist lediglich für das Testen der Funktionalität des Clientauthentifizierungscodes vorgesehen. Er erfüllt keinerlei Sicherheitszwecke. Sie können das in der Produktion verwendete Speicherkonto und den zugehörigen Schlüssel nicht mit dem Speicheremulator verwenden. Es ist nicht ratsam, das Entwicklungskonto mit Produktionsdaten zu verwenden.
> 
> Der Speicheremulator unterstützt nur Verbindungen über HTTP. Das empfohlene Protokoll für den Zugriff auf Ressourcen in einem in der Produktionsumgebung verwendeten Azure-Speicherkonto ist aber HTTPS.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Herstellen einer Verbindung mit dem Emulatorkonto über eine Verknüpfung
Am einfachsten können Sie über Ihre Anwendung eine Verbindung mit dem Speicheremulator herstellen, indem Sie eine Verbindungszeichenfolge in der Konfigurationsdatei Ihrer Anwendung konfigurieren, die auf die Verknüpfung `UseDevelopmentStorage=true` verweist. Eine Verbindungszeichenfolge, die auf den Speicheremulator in einer *app.config*-Datei verweist, kann beispielsweise wie folgt aussehen: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Herstellen einer Verbindung mit dem Emulatorkonto mithilfe des bekannten Kontonamens und Schlüssels
Um eine Verbindungszeichenfolge zu erstellen, die auf den Emulator-Kontonamen und -schlüssel verweist, müssen Sie die Endpunkte für jeden Dienst, den Sie über den Emulator verwenden möchten, in der Verbindungszeichenfolge angeben. Dies ist erforderlich, damit die Verbindungszeichenfolge auf die Emulator-Endpunkte verweist. Diese unterscheiden sich von den Endpunkten eines Produktions-Speicherkontos. Beispielsweise wird der Wert der Verbindungszeichenfolge wie folgt aussehen:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Dieser Wert stimmt mit der oben dargestellten Verknüpfung `UseDevelopmentStorage=true` überein.

#### <a name="specify-an-http-proxy"></a>Angeben eines HTTP-Proxys
Sie können auch einen HTTP-Proxy angeben, der beim Testen des Dienstes anhand des Speicheremulators verwendet werden soll. Dies kann zum Beobachten von HTTP-Anforderungen und -Antworten nützlich sein, während Sie Vorgänge anhand der Speicherdienste debuggen. Fügen Sie der Verbindungszeichenfolge die Option `DevelopmentStorageProxyUri` hinzu, um einen Proxy anzugeben, und legen Sie ihren Wert auf den Proxy-URI fest. Hier sehen Sie beispielsweise eine Verbindungszeichenfolge, die auf den Speicheremulator verweist und einen HTTP-Proxy konfiguriert:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

