---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: 44969063765099d350e21abfbd07792891443911
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673147"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Skapa och aktivera en virtuell miljö

I en lämplig mapp kör du följande kommandon för att skapa och aktivera en virtuell miljö med `.venv`namnet. Se till att använda python 3,8, 3,7 eller 3,6, som stöds av Azure Functions.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Om python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Du kör alla efterföljande kommandon i den här aktiverade virtuella miljön. (Du avslutar den virtuella miljön genom att `deactivate`köra.)
::: zone-end