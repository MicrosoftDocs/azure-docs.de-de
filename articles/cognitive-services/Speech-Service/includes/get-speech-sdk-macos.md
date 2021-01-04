---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 963405f0874e23553a118081b080a0f30e1942eb
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135547"
---
Für die Entwicklung für macOS sind drei Speech SDKs verfügbar.

- Das Objective-C Speech SDK ist nativ als CocoaPod-Paket verfügbar.
- Das .NET Speech SDK kann mit **Xamarin.Mac** verwendet werden, da es .NET Standard 2.0 implementiert.
- Das Python Speech SDK ist als PyPI-Modul verfügbar.

> [!TIP]
> Ausführliche Informationen zur Verwendung des Objective-C Speech SDK mit Swift finden Sie unter <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importieren von Objective-C in Swift <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

### <a name="system-requirements"></a>Systemanforderungen

- macOS Version 10.13 oder höher

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Das macOS-CocoaPod-Paket steht zum Download zur Verfügung und kann mit der integrierten Entwicklungsumgebung (Integrated Development Environment, IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (oder höher) <span class="docon docon-navigate-external x-hidden-focus"></span></a> verwendet werden. Laden Sie zunächst die <a href="https://aka.ms/csspeech/macosbinary" target="_blank">Binärdatei für CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span></a> herunter. Extrahieren Sie den Pod in dem Verzeichnis, in dem Sie ihn verwenden möchten, erstellen Sie eine *Podfile* , und listen Sie den `pod` als `target` auf.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.14.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac macht das vollständige macOS SDK für .NET-Entwickler verfügbar, mit dem Sie native Mac-Anwendungen mithilfe von C# erstellen können. Weitere Informationen finden Sie unter <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Quellcode zum Schnellstart für das macOS Speech SDK für Objective-C <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Quellcode zum Schnellstart für das macOS Speech SDK für Swift <span class="docon docon-navigate-external x-hidden-focus"></span></a>