# LocalizationX Lite Document

__Download and import the [Example](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/LocalizationXLite/LocalizationXLiteExample.unitypackage)__

Navigate to the main menu and select: `Window > LocalizationX Lite`

## 1. Settings

- __Scripts Output Folder:__ Stores exported C# scripts, including Localization Components, and Localization API.
- __Localization Output:__ Stores Localization Data, which should be inside the Resources folder for loading via Resources, or in the Localizations folder for loading via Addressable Asset System.
- __Language Char Sets:__ Used in Localization with TextMeshPro to compile the character table of a language, mainly applied for Korean, Japanese, and Chinese due to their extensive character systems.
- __Google Client ID:__ Enter your Google Client ID (retrieved from Credentials in Google Console).
- __Google Client Secret:__ Enter your Google Secret (retrieved from Credentials in Google Console).

## 2. Export from Excel Spreadsheets

![lxl_tab_excel](https://github.com/user-attachments/assets/22e325c4-d84b-4fab-87c2-bb40abd58b78)

1. Select the Excel file you want to process.
2. Click the `Export All` button to process.

## 3. Export from Google Spreadsheets

![lxl_tab_google](https://github.com/user-attachments/assets/f1211007-5b32-432d-8cf8-3dbbae746349)

### 3.1. Setup Google Client ID and Client Secret

#### Step 1: Enable Google Sheets API

1. Visit the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project or select an existing one.
3. Click on __Go to APIs overview__.
4. Select __Enable APIs and Services__.
5. Search for and select __Google Sheets API__, then click __Enable__.

#### Step 2: Obtain Credentials

1. On the top Google Sheets API screen, click on __Create Credentials__.
2. Choose __Google Sheets API__, __User data__, then click __Next__.
3. In the Scopes section, click on __Add or remove scopes__.
4. Find and select the __Google Sheets API__ (description: "See all your Google Sheets Spreadsheets"), then __Save and Continue__.
5. In the OAuth Client ID section, select Application Type as Desktop App, enter any name, then click __Create__.
6. Click __Done__.

#### Step 3: Accessing Your Client ID and Client Secret:

1. On the Google Sheets API screen, go to the __Credentials__ tab, you will find the new Client ID.
2. Click on the Edit button to find the Client ID and Client Secret.
3. Copy the __Client ID__ and __Client Secret__, and paste them into the text boxes.

### 3.2. Access Google Spreadsheets

Enter the Google Sheet Id.
The Google Sheet Id look like this.

```url
https://docs.google.com/spreadsheets/d/[GOOGLE_SHEET_ID]/edit?......
```

## 4. Rules in Spreadsheet

Naming Conventions for Localization Sheets:

- The sheet name must start with `Localization`.
- Each sheet has two key columns: the main key `idString` and an additional key `relativeId`.
- The following columns contain localized content.
- The key for each row is a combination of `idString` and `relativeId`.
- `relativeId` is an optional column that helps organize localization data.

```a
| idString | relativeId | english | spanish | japan | .... |
| -------- | ---------- | ------- | ------- | ----- | ---- |
```

For more details, please refer to the example Excel file.

## 5. How to integration

__Download and import the [Example](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/LocalizationXLite/LocalizationXLiteExample.unitypackage)__

First, open the Excel file at `/Assets/LocalizationXLite/Example/Example.xlsx`. This file has example data to help you understand how to create Localization sheets

![example_excel_file](https://github.com/user-attachments/assets/5330cedb-cc67-4a38-9678-5c74839cd328)

__For the example using Google Sheets, you can view the file__ [__here__](https://docs.google.com/spreadsheets/d/128bv-Nek9n_8tpc4vgkZcNfMZWs61zxk1FW_Ig_WMSA/edit?usp=sharing).

### 5.1. Create folders for exporting files

Create 2 directories to store the files that will be exported:

- A folder to store the C# scripts (Localization Component and Localization API).
- A folder to store the Localization data.

  - There are two ways to set up the folder for Localization data, depending on how you want to load Localizations:
    - The easiest method is to load from the Resources folder. Create a folder inside the Resources folder to store Localization data. You can name this folder anything you like.
    - Alternatively, use the Addressable Asset System. In this case, create a "Localizations" folder outside the Resources folder and set it as an Addressable Asset. It's recommended to name this folder "Localizations".

- Set up the paths for the "__Scripts Output Folder__", and "__Localization Output Folder__" using the folders you just created.

Example for 2 folders:

- `Assets\LocalizationXLiteExample\Scripts\Generated`: for C# scripts
- `Assets\LocalizationXLiteExample\Resources\Localizations`: for Localization data

### 5.2. Scripting

- Initialization

```cs
Localization.Init();
```

- Change the language.

```cs
// Set the language japanese
Localization.CurrentLanguage = "jp";
```

- Register an event handler for the language change event.

```cs
// Register an action when language changed
Localization.OnLanguageChanged += OnLanguageChanged;
```

- You can retrieve localized content using three different methods.

  1. Retrieve localized content using a Key. Note that the text will not automatically refresh when the language changes using this method.

      ```cs
      // Retrieve localized text using an integer key
      m_simpleText1.text = Localization.Get(Localization.NOT_ENOUGH_GOLD).ToString();
      // Retrieve localized text using an integer key with an argument
      m_simpleText2.text = Localization.Get(Localization.LEVEL_UP_X, 10).ToString();
      // Retrieve localized text using a string key with an argument
      m_simpleText3.text = Localization.Get("LEVEL_UP_X", 25).ToString();
      ```

  2. Link a GameObject containing a Text or TextMeshProUGUI component with a key so that the text automatically updates when the language changes.

      ```cs
      // Register dynamic localized text using an integer key
      Localization.RegisterDynamicText(m_dynamicText1.gameObject, Localization.DOUBLE_TAP);
      // Register dynamic localized text using an integer key with an argument
      Localization.RegisterDynamicText(m_dynamicText2.gameObject, Localization.REQ_LVL_X, "3");
      // Register dynamic localized text using a string key with an argument
      Localization.RegisterDynamicText(m_dynamicText3.gameObject, "REQ_LVL_X", "30");
      // Unregister the gameObject
      Localization.UnregisterDynamicText(m_dynamicText1.gameObject);
      Localization.UnregisterDynamicText(m_dynamicText2.gameObject);
      Localization.UnregisterDynamicText(m_dynamicText3.gameObject);
      ```

  3. Using Localization Component.

      ![localization_component](https://github.com/user-attachments/assets/af75b6ca-afe3-4045-8225-9a69b8761572)

### 5.3. Creating TextMeshPro Fonts for Extensive Languages

To create TextMeshPro fonts for Japanese, Korean, and Chinese, follow these steps using the respective character set files __characters_set_jp__, __characters_set_ko__, and __characters_set_cn__, which include all characters from the localization sheets:

Fonts to use in this example:

- Japanese: NotoSerif-Bold
- Korean: NotoSerifJP-Bold
- Chinese: NotoSerifTC-Bold

Creating TextMeshPro Fonts:

- For each language font, create a TextMeshPro font asset.
- Open the Font Asset Creator window in Unity.
- Under the _Character Set_ section, select _Character From File_.
- Choose the appropriate character set file (e.g., characters_set_jp) in the Character File section.

![Create Japanese font](https://github.com/user-attachments/assets/7bc98c77-9994-4551-8e5a-dae51eba9f45)

![Create Korean font](https://github.com/user-attachments/assets/dc14fbbb-b38f-4f56-89b0-844d94b825cb)

![Create Chinese font](https://github.com/user-attachments/assets/08020e00-14b1-47cd-a9f2-be3d4321ca48)

### 5.4. Loading Localization Using the Addressable Assets System

To utilize this feature, follow these steps:

- Install the Addressable Assets System.
- Add `ADDRESSABLES` to the directives list in the Build __Settings__.
- Move the Localizations folder out of the Resources folder. Additionally, relocate the Output folder in the __Settings__.
- Set the Localizations folder as an Addressable Asset.

![Build Settings](https://github.com/user-attachments/assets/229da607-da10-4b87-b799-5d9549e5620d)