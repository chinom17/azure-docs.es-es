---
title: "Tutorial: Integración de Azure Active Directory con Carlson Wagonlit Travel | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Carlson Wagonlit Travel."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2745e165-94ab-43b1-970a-4547b4e5b501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: jeedes
ms.openlocfilehash: 26d141d73dfee5dd47ba09a32262343488487ad1
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-carlson-wagonlit-travel"></a>Tutorial: Integración de Azure Active Directory con Carlson Wagonlit Travel

En este tutorial obtendrá información sobre cómo integrar Carlson Wagonlit Travel con Azure Active Directory (Azure AD).

La integración de Carlson Wagonlit Travel con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Carlson Wagonlit Travel.
- Puede permitir que los usuarios inicien sesión automáticamente en Carlson Wagonlit Travel (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Carlson Wagonlit Travel, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción con un inicio de sesión único de Carlson Wagonlit Travel

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Carlson Wagonlit Travel desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-carlson-wagonlit-travel-from-the-gallery"></a>Agregar Carlson Wagonlit Travel desde la galería
Para configurar la integración de Carlson Wagonlit Travel en Azure AD, deberá agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Carlson Wagonlit Travel desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Carlson Wagonlit Travel**, seleccione **Carlson Wagonlit Travel** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Carlson Wagonlit Travel en la lista de resultados](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Carlson Wagonlit Travel con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Carlson Wagonlit Travel para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Carlson Wagonlit Travel.

Para establecer la relación de vínculo, en Carlson Wagonlit Travel, asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Carlson Wagonlit Travel, es preciso completar los siguientes bloques de compilación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Carlson Wagonlit Travel](#create-a-carlson-wagonlit-travel-test-user)**: para tener un homólogo de Britta Simon en Carlson Wagonlit Travel que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Carlson Wagonlit Travel.

**Para configurar el inicio de sesión único de Azure AD con Carlson Wagonlit Travel, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Carlson Wagonlit Travel** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_samlbase.png)

3. En la sección **Dominio y direcciones URL de Carlson Wagonlit Travel**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Carlson Wagonlit Travel](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_url.png)

    En el cuadro de texto **Identificador**, escriba el valor `cwt-stage`.

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_400.png)

6. Para configurar el inicio de sesión único en **Carlson Wagonlit Travel**, debe enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Carlson Wagonlit Travel](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-carlson-wagonlit-travel-test-user"></a>Crear un usuario de prueba de Carlson Wagonlit Travel

En esta sección, creará el usuario Britta Simon en Carlson Wagonlit Travel. Trabaje con el [equipo de soporte técnico de Carlson Wagonlit Travel](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html) para agregar los usuarios en la plataforma Carlson Wagonlit Travel. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para usar el inicio de sesión único de Azure y concederá acceso a Carlson Wagonlit Travel.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Carlson Wagonlit Travel, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Carlson Wagonlit Travel**.

    ![Vínculo de Carlson Wagonlit Travel en la lista de aplicaciones](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Carlson Wagonlit Travel del panel de acceso, debería iniciar sesión automáticamente en su aplicación Carlson Wagonlit Travel.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_203.png

