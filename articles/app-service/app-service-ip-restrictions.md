---
title: Restricciones de IP de Azure App Service | Microsoft Docs
description: Uso de restricciones de IP con Azure App Service
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 22e05af889b4e792dcc6f6fc438e8a58674b9f0e
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restricciones de IP estáticas de Azure App Service #

Las restricciones de IP le permiten definir una lista de direcciones IP que tienen permiso para acceder a su app. La lista de direcciones IP permitidas puede incluir direcciones IP individuales o un intervalo de direcciones IP definido por una máscara de subred.

Cuando se genera una solicitud para la aplicación desde un cliente, la dirección IP se evalúa con la lista de direcciones IP permitidas. Si la dirección IP no está en la lista, la aplicación responde con un código de estado [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Las restricciones de IP se definen en el archivo web.config que la aplicación consume en el runtime. En determinadas circunstancias, algún módulo se podría ejecutar antes que la lógica de las restricciones de IP en la canalización HTTP. Cuando esto sucede, la solicitud genera un error con un código de error HTTP diferente.

Las restricciones de IP se evalúan en las mismas instancias de plan de App Service asignado a su aplicación.

Para agregar una regla de restricción de IP a la aplicación, use el menú para abrir **Red**>**Restricciones de IP** y haga clic en **Configurar restricciones de IP**.

![Restricciones de IP] (media/app-service-ip-restrictions/ip-restrictions.png)

Desde aquí, puede revisar la lista de reglas de restricción de IP definidas para la aplicación.

![lista de restricciones de IP](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Puede hacer clic en **[+] Agregar** para agregar una nueva regla de restricciones de IP.

![agregar restricciones de IP](media/app-service-ip-restrictions/add-ip-restrictions.png)
