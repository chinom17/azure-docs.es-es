---
title: "Implementación de plantillas con Visual Studio en Azure Stack | Microsoft Docs"
description: Aprenda a implementar las plantillas con Visual Studio en Azure Stack.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implementación de plantillas en Azure Stack con Visual Studio

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Use Visual Studio para implementar plantillas de Azure Resource Manager en el Kit de desarrollo de Azure Stack.

1. [Instale Azure Stack y conéctese](azure-stack-install-visual-studio.md) a esta infraestructura con Visual Studio.
2. Abra Visual Studio.
3. Haga clic en **Archivo**, en **Nuevo** y, en el cuadro de diálogo **Nuevo proyecto**, haga clic en **Grupo de recursos de Azure**.
4. Escriba un **nombre** para el nuevo proyecto y, después, haga clic en **Aceptar**.
5. En el cuadro de diálogo **Seleccionar plantilla de Azure**, cambie la lista desplegable *Mostrar plantillas de esta ubicación* a **Azure Stack Quickstart** (Inicio rápido de Azure Stack).
6. Haga clic en **101-create-storage-account** y, después, haga clic en **Aceptar**.  
7. En el nuevo proyecto, puede ver una lista de plantillas disponibles expandiendo el nodo **Plantillas** en el panel **Explorador de soluciones**.
8. En el panel **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto, haga clic en **Implementar** y, finalmente, en **Nueva implementación**.
9. En el cuadro de diálogo **Implementar en grupo de recursos**, en la lista desplegable **Suscripción**, seleccione la suscripción de Microsoft Azure Stack.
10. En la lista **Grupo de recursos** , elija un grupo de recursos existente o cree uno nuevo.
11. En la lista **Ubicación del grupo de recursos**, elija una ubicación y haga clic en **Implementar**.
12. En el cuadro de diálogo **Editar parámetros**, escriba los valores para los parámetros (que varían según la plantilla) y haga clic en **Guardar**.

## <a name="next-steps"></a>pasos siguientes
[Implementación de plantillas con la línea de comandos](azure-stack-deploy-template-command-line.md)

[Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)

