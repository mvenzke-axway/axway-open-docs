---
title: Localization of API Portal - T4 Page Builder template
linkTitle: Localization with T4
weight: 60
date: 2022-12-06T00:00:00.000Z
description: You can localize the API Portal user interface to use another language.
---

API Portal supports localization using a single language or multiple languages. When changing to a single language, existing elements like menus, pages, modules, and so on are translated to the desired language, and no additional copies of those elements are required, whereas when changing to multiple languages, all API Portal elements must be first cloned to each desired language, and then translated. After configuring support for multiple languages, you can enable a language switcher to your API Portal.

The target audience for this document are [Joomla! administrators](/docs/apim_administration/apiportal_admin/apip_overview/#api-portal-users) users.

## Localize API Portal to a single language

You can change the API Portal UI texts from the default language (English) to another language.

### Install a new language

User interface strings are stored in language-specific resource files. To add these resource files for a new language, you must install the language first.

1. In the Joomla! Admin Interface (JAI), click **System > Languages** from the **Install** card.
2. Find and select the language to install, and click **Install**.

### Enable Content language

After a new language is installed, you must enable it.

1. In JAI, click **System > Content Language** from the **Manage** card.
2. Click the icon in the **status** column for the newly installed language to publish it.

### Set a new default language

After a new language is enabled and the content for that language is configured, you must specify the default language in which API Portal will be available for all end users.

1. In JAI, click **System > Languages** from the **Manage** card.
2. Ensure that **Site** is selected at the top of the page.
3. To set the installed language as the default language, click the star button in the row for that language.

   {{< alert title="Tip" color="primary" >}}You can change the default language of JAI regardless your API Portal. Select **Administrator** on the **Languages: Installed** page and click the star button in the row for the required language. You can also select any of the installed languages when you log in to JAI.{{< /alert >}}

### Add a new translated UI string file

API Portal UI strings are not included in the installed language resource files, so you must add the required strings for the new language.

The UI strings are stored in initialization (`.ini`) files. Each language has its own `.ini` file that controls the UI text shown in API Portal. By default, the UI strings are included in English only (see, `en-GB.com_apiportal.ini` file). To change the language shown in API Portal, you must have a translation of the English `.ini` file for that language.

All **File upload** sections have a link from which you can download the English version of the file. After that, you just need to translate the strings and upload the new file. You must ensure that the extension of the file is `.ini`, otherwise the upload will fail.

To upload language files:

1. In JAI, click **Components > API Portal > Additional Settings**.
2. Enable **Upload language file toggle**. The following options are shown:

    * **Language**: Select the language for which you are uploading translations. All installed languages except English are listed.
    * **Language files context**: Select whether the uploaded language files are for the **Site** or **Administrator** part of API Portal.
    * **API Portal component language file**: The main language file for API Portal component.
    * **Overridden language file**: Some texts in API Portal, such as the messages on the login pages, are stored in different files, so you must change and upload them separately.
    * **`*.sys.ini` language file**: This options is shown only when **Administrator** is selected as a language files context. This language file contains translations for the administrator components menu drop-down, menu, and component parameters.
3. Click **Save & Close**.

### Translate custom properties

To translate labels or values of custom properties, you must add a new row to the translation `*.ini` file, with the desired `key/value` pair, and ensure that the value of the key is in capital letters. For example, to translate a custom property with label "Environment" to French, add the following line to the translation file: `ENVIRONMENT="Environnement"`.

### Rename API Portal menu items

The names of the menu items are editable. To manually change and translate them to the new language:

1. In JAI, click **All Menu Items**.
2. Click the menu item name you wish to edit.
3. Change the **Title** field.
4. (Optional) Change the **Alias** field.
   {{< alert title="Note" color="primary" >}}JAI will transliterate _after_ you save your changes, so if for example, you are using accented characters, ensure that the Alias string looks acceptable.{{< /alert >}}
5. Click **Save & Close**.

Repeat these steps for all other menu items.

### Translate static T4 page content

To translate the static content in your T4 Page Builder pages:

1. In JAI, click **Components > T4 Page Builder > All Pages**.
2. Hover the mouse over the page you want to edit and click the **Edit** button that is shown. This opens the T4 Page Builder Editor.
3. Make the necessary changes and click the **Save** button on the top right.

Repeat these steps for all other pages.

### Translate modules

Since modules are language-specific, you must translate each module for the installed language as follows:

1. In JAI, click **System > Site Modules** from the **Manage** card.
2. Click the module you wish to translate.
3. In the **Module** tab, you must manually translate _any_ static strings (for example, the value of **Placeholder** property in **API Portal Search Box** module).
4. From the right sidebar, choose the new language from the **Language** drop-down list.
5. Click **Save & Close**.

Repeat these steps for all the other modules.

## Localize API Portal to multiple languages

You can provide API Portal in multiple languages at the same time to cater for your API consumers from different countries. API consumers can use language-specific URLs to access API Portal in their own language.

Unlike when localizing API Portal to a single language, you are not simply substituting one language with another. Instead, when providing several language versions of your API Portal, you must duplicate the API Portal elements and content for each language in addition to changing the UI texts.

Before you start configuring API Portal to use multiple languages, you must install and configure the new languages, and add their correspondent translated UI strings to API Portal. For more information, see the following sections:

* [Install a new language](#install-a-new-language).
* [Enable Content language](#enable-content-language).
* [Add a new translated UI string file](#add-a-new-translated-ui-string-file).

These sections must be repeated for any additional language you wish to install.

### Enable System language plugins

Joomla! provides two plugins that support multilingual content:

* The **System - Language Filter** plugin displays the content based on the language selection on the website. This plugin must be enabled.
* The **System - Language Code** plugin hides the language code in the HTML code of the website. It plays an important role in the Search Engine Optimization (SEO). This plugin is optional.

The plugins are disabled by default. To enable a plugin:

1. In JAI, click **System > Plugins** from the **Manage** card.
2. Type _Language_ in the search bar.
3. Click the icon in the **status** column for the plugin you wish to enable.

### Translate Articles

To create different language article content, you must add new article for each language.

1. In JAI, click **Content > Articles** and click on the article to be translated.
2. In the top bar, click the arrow of the **Save & Close** button and click **Save as Copy**.
3. Select the language for the article from **Language > drop-down**, then click **Save**.
4. Translate the article.
5. Save the article and publish it

Repeat these steps for each article.

### Duplicate the main menu

You must duplicate API Portal main menu for each language.

1. In the JAI left menu, click **Menus > Manage** and create a new main menu for the new language.
2. Enter a **Title** (for example, **Main Menu - FR**) and the **Unique Name** (for example, **mainmenu-fr**) for the new main menu.
3. Click **Save & Close**.

### Clone the main menu items

Clone all available menu items in **Main Menu** to the newly created main menu. To clone all menu items at once:

1. Click **Menus > Main Menu**.
2. Click the **Check All Items** check box, and from the **Actions** drop-down list, click **Batch**. A dialog box is shown.
3. Set the following settings in the dialog box:
   * In **Set Language**, select the correct content language.

      If you do not see the **Set Language** drop-down, ensure that **[System - Language Filter](#enable-system-language-plugins)** plugin is enabled.

   * In **To Move or Copy**, locate the correct main menu for the language and select **Add to this menu**.
   * Select **Copy** to make a copy of the menu items.
   * Click **Process**.
4. In the JAI left menu, click **Menus** and select the main menu you cloned the menu items to.
5. Click **Save**.

{{< alert title="Note" color="primary" >}}You must **Duplicate the main menu** and **Clone the main menu items** also for the **User Menu** and the **Legal Menu**.
{{< /alert >}}

### Set a new default home page

To set a new default home page for the corresponding language:

1. Click the newly duplicated main menu (for example, **Main Menu - FR**).
2. Click the star in the row for the **Home (2)** menu item.
3. To rename the cloned menu items, click each menu item to edit it, and change **Title** to the correct translation (for example, `Home (2)` to `Accueil`).
4. Click **Save & Close**.

### Duplicate site modules for the new language

Since modules are language specific, you must translate each module for each supported language as follows:

1. In JAI, click **System > Site Modules** from the **Manage** card.
2. Click the module you wish to duplicate.
3. In the top bar, click the arrow of the **Save & Close** button and click **Save as Copy**.
4. Change the **Title**.
5. In the **Module** tab:
   * Ensure that any properties **Link to _some_ page** are pointing to menu items for the correct language.
   * Manually translate _any_ static strings (for example the value of **Placeholder** property in **API Portal Search Box** module).
6. From the right sidebar, set the **Status** of the new module to **Published** and choose the right language from the **Language** drop-down list.
7. Click **Save & Close**.

Repeat these steps for all the other modules.

### Duplicate the T4 default page template

You must also duplicate page template styles for each language. By default, API Portal uses the **T4 - Default** template style for all pages which are not Page Builder pages, for example articles, EasyBlog, EasyDiscuss and so on.

To localize those pages, you must make a copy of the **T4 - Default** template style for each installed language as follows:

1. In JAI, click **System > Site Template Styles** from the **Templates** card.
2. Check the **T4 - Default** checkbox in the list and click the **Duplicate** button on the top controls panel.
3. Click the newly duplicated style. This opens the T4 style editor.
4. Click **Overview** and change the style template name to something more informative than the default generated one.
5. Set the new language as a default language for this template.
6. Click the down arrow of the **Save** button on the top right and click **Save & Close**.

### Configure the navigation bar for the new T4 Default template style

After you duplicate the T4 Default template style, you must configure the navigation bar of your portal.

1. In JAI, open the newly duplicated template style and click **Navigation**.
2. Click **Clone Selected Navigation** and enter a name for the newly cloned navigation
3. Click **Save**.
4. Edit the newly created navigation.
5. Under **Megamenu**, select the Main Menu for the corresponding language (for example **mainmenufr**).
6. In the panel of all listed menu items belonging to the specified Main Menu, select the default menu item for this Main Menu (for example **Home (2)**).
7. Click **Save** next to the navigation name to save the navigation for that specific language.
8. Click the down arrow of the **Save** button on the top right and click **Save & Close**.

### Configure layout settings for the new T4 Default template style

After you configure the navigation bar of your portal, you must create a new page header layout to change the **Sign In** and **Register** buttons, located in the header of the page, for the corresponding language.

1. In JAI, open the newly duplicated template style.
2. Click **Layouts Settings** to open the **Main Layout** of the page.
3. Click the icon to clone the existing **axway** layout, then enter a name for the new layout (for example **axway-fr**).
4. Click **Save**.
5. Click **Edit** icon next to the new layout.
6. Click the **gear** icon next to the **axway** header field to create a new header for the corresponding language. A dialog box is shown.
7. From the dialog box, click **Add new** to create a new block column.
8. Enter a title for the new block.
9. For the block content, paste the following code:

    ```
    <!-- HEADER BLOCK -->
    <header class="header-block header-axway">
    <div class="header-wrap">
    <jdoc:include type="element" name="logo" data-home="index.php" />
        <div class="t4-navbar" id="nav-main-site">
    <jdoc:include type="element" name="offcanvas-toggle" />
    <jdoc:include type="element" name="megamenu" />
    <jdoc:include type="module" name="menu" title="$PLACEHOLDER" />
    <jdoc:include type="modules" name="language-switcher" />
    </div>
    </div>
    </header>
    <!-- // HEADER BLOCK -->
    ```

10. On the block code, change the **title** of the **jdoc:include** element of type **module** to point to the desired **User Menu** module, and click **Save**.

    The following image shows the dialog box where you create a new block.

    ![T4 Default New Axway layout](/Images/APIPortal/APIPortal_localization/t4_default_layout_new_block_layout.png)
11. Click **Apply** to close the dialog box.
12. Click **Save** to save the main layout.
   {{< alert title="Note" color="primary" >}}Not all layout changes are loaded correctly at this point. You must save the whole T4 Default template at the end of this procedure to see the changes correctly applied.{{< /alert >}}
13. Click the down arrow of the **Save** button on the top right and click **Save & Close**.

### Duplicate the Page Builder template

All API Portal pages are T4 Page Builder pages. Thus, to translate them you must make a copy of the **T4 - Page Builder** template for each installed language as follows:

1. In JAI, click **System > Site Template Styles** from the **Templates** card.
2. Check the **T4 - Page Builder** checkbox in the list and click the **Duplicate** button on the top control panel.
3. Click the newly duplicated style. This opens the T4 style editor.
4. Click **Overview** and change the style template name to something more informative than the default generated one.
5. Ensure that the default language for this template style is set to **No**.
6. Click the down arrow of the **Save** button on the top right and click **Save & Close**.

### Configure the navigation bar for the new Page Builder template

After you duplicate the Page Builder template style, you must configure the navigation bar of your portal. The navigation element is shared between templates, so before you perform the steps on this section, you must first complete the [Configure the navigation bar for the new T4 Default template style](#configure-the-navigation-bar-for-the-new-t4-default-template-style) section.

To select the new navigation to your portal, follow these steps:

1. In JAI, open the newly duplicated Page Builder template style and click **Navigation**.
2. Select the navigation for the corresponding language.
3. Click the down arrow of the **Save** button on the top right and click **Save & Close**.

### Configure the layout settings and menu assignments for the new Page Builder template

After you duplicate the Page Builder template style, you must configure the layout settings to change the **Sign In** and **Register** buttons for the corresponding language.

1. In JAI, open the newly duplicated Page Builder template style.
2. Click **Layouts Settings** to open the **Main Layout** of the page.
3. Click the icon to clone the existing **axway-pagebuilder** layout, then enter a name for the layout (for example **axway-pagebuilder-fr**).
4. Click **Save**.
5. Click **Edit** icon next to the new layout.
6. Click the **gear** icon next to the **axway** header field to create a new header for the corresponding language. A dialog box is shown.
7. From **Select Block** drop-down select the newly created block (for example **axway-fr**).

    The following image shows the dialog box where you create a new block.

    ![T4 Page Builder select already existing header layout](/Images/APIPortal/APIPortal_localization/t4_pageBuilder_layout_update.png)

8. Click **Apply** to close the dialog box.
9. Click **Save** to save the main layout.
   {{< alert title="Note" color="primary" >}}Not all layout changes are loaded correctly at this point. You must save the whole T4 Page Builder template at the end of this procedure to see the changes correctly applied.{{< /alert >}}
10. Click **Menu Assignment** and select all menu items from **Main Menu**, **User Menu**, and **Legal Menu** of the corresponding language.
11. Click the down arrow of the **Save** button on the top right and click **Save & Close**.

### Duplicate API Portal pages

To duplicate a page:

1. In JAI, click **Components > T4 Page Builder > All Pages**.
2. Click the page you wish to duplicate.
3. In the top bar, click the arrow of the **Save** button and click **Save as Copy**.
4. Change the **Title** of the new page.
5. Ensure that the **Language** for the duplicate page is set to **All**.
6. Click the down arrow of the **Save** button on the top right and click **Save & Close**.

Repeat these steps for all other pages.

### Translate page content

API Portal pages may consist of one or more modules, static text strings and/or redirect links. In order to have a proper localization and navigation the administrator needs to update the content of those elements.
To translate the content of a duplicated page:

1. In JAI, click **Components > T4 Page Builder > All Pages**.
2. Click on the newly duplicated page.
3. Under **Page Content** tab, click **Edit** to edit a page which you wish to translate. This opens the T4 Page Builder Editor.
4. Click the existing module on the page you wish to replace.
5. In the panel on the right, in **Settings > Module**, select the newly created module from the drop-down list.
6. To translate the static content of the page, double-click the text and enter a properly translated string.
7. Translate the text of a link and to where the link will redirect the user. Specify a redirect link corresponding to the menu item created for the particular language.

    The following image shows where to translate the API Portal registration link:

    ![Page builder link translate and update](/Images/APIPortal/APIPortal_localization/page_builder_link_updating.png)

    The following image shows where to translate the welcome link:

      ![Page builder link translate and update](/Images/APIPortal/APIPortal_localization/page_builder_link_updating_2.png)

8. Change each article with its corresponding one created for the particular language.
9. Click **Save** and **Close** on the top right corner of the page.
10. In the top bar, click the arrow of the **Save** button and click **Save and Close**.

Repeat these steps for all other pages.

### Link API Portal pages with its corresponding menu items

Once a page is translated to the corresponding language the administrator needs to link it to its corresponding menu item.

1. In JAI, click **Menus > All Menu Items** and click the menu item you wish the corresponding translated page to be assigned to.
2. In the **Details** tab, click the **Clear** button of the **Choose your page** field. The content is cleared and the **Select** button is shown.
3. Click the **Select** button and choose the newly created page from the list.
4. Click **Save & Close**.

Repeat these steps for all other duplicated menu items.

## Enable language switcher

Language switcher module allows site visitors to switch between languages by way of country flags based on the module configuration.

Before enabling language switcher, you must perform all the steps described in [Localize API Portal to multiple languages](#localize-api-portal-to-multiple-languages) for each language, including English.

{{< alert title="Note" color="primary" >}}To use the language switcher, you must also follow the [Localize API Portal to multiple languages](#localize-api-portal-to-multiple-languages) procedure to the English language, otherwise, you will not be able to switch your site from other languages back to English.
{{< /alert >}}

To enable the language switcher, follow these steps:

1. In the JAI left menu, click **System > Site Modules** and click **New**.
2. Click **Language Switcher**.
3. Enter a **Title**, and for **Position** select `Language Switcher`.
4. Click **Save & Close**.
5. In the JAI left menu, click **System > Plugins** and ensure the **System - Language Filter** plugin is enabled.
6. Click the **System - Language Filter** plugin to open it.
7. Set **Automatic Language Change** to `No`.
8. Click **Save & Close**.

The language switcher is now available on the header of each API Portal page, as the following image shows:

![Language switcher on header](/Images/APIPortal/APIPortal_localization/language_switcher_flag.png)