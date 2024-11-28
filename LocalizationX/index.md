# LocalizationX Document

__Download and import the [Example](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/LocalizationX/LocalizationXExample.unitypackage)__

## 1. Settings

Navigate to the main menu and select: `Window > LocalizationX > Settings`

![settings_tab](https://github.com/user-attachments/assets/538a5a8b-50c9-4c21-a04d-092c8de4f4f3)

- __Scripts Output Folder:__ Stores exported C# scripts, including ID constants, Localization Components, and Localization API.
- __Localization Output:__ Stores Localization Data, which should be inside the Resources folder for loading via Resources, or in the Localizations folder for loading via Addressable Asset System.
- __Namespace:__ Defines the namespace for the exported C# files.
- __Separate IDs: Sheets__
  - TRUE: Exports _[%IDs]_ sheets to individual C# files named _[SheetName] + IDs.cs_.
  - FALSE: Merges all _[%IDs]_ sheets from all Excel files into a single C# file named _IDs.cs._
- __Separate Localization Sheets:__
  - TRUE (default): Exports _[Localization%]_ sheets to separate groups, each containing Localization Data, Component, and API, with the following file name structure:
    - Localization Data: _[SheetName]\_[language].txt_
    - Component: _[SheetName] + Text.cs_
    - API: _[SheetName].cs_
  - FALSE: Merges all _[Localization%]_ sheets from all Excel files into a single group, with the following file name structure:
    - Localization Data: _Localization\_ + [language].txt_
    - Component: _LocalizationText.cs_
    - API: _Localization.cs_
- __Only enum as IDs:__ For _[%IDs]_ sheets, columns with the extension _[enum]_ will be exported as enums and will not include the Integer Constant form.
- __Language Char Sets:__ Used in Localization with TextMeshPro to compile the character table of a language, mainly applied for Korean, Japanese, and Chinese due to their extensive character systems.
- __Google Client ID:__ Enter your Google Client ID (retrieved from Credentials in Google Console).
- __Google Client Secret:__ Enter your Google Secret (retrieved from Credentials in Google Console).

## 2. Export from Excel Spreadsheets

Navigate to the main menu and select: `Window > LocalizationX > Excel Spreadsheets`

![excel_tab](https://github.com/user-attachments/assets/a4a6b06e-c0ea-4eee-9630-e3e109085ddd)
![edit_excel_sheets](https://github.com/user-attachments/assets/0717b9b5-a857-466e-8aa2-1115d8e3ba91)

To manage complex Localizations across multiple Excel files, use this feature. It helps you handle and export all your Excel files with one click:

1. Add all the Excel files you want to process.
2. For each Excel file, you have the option to choose which sheets to include or exclude.
3. Click the `Export All` button to process.

## 3. Export from Google Spreadsheets

Prefer using Google Spreadsheets? No problem.

Navigate to the main menu and select: `Window > LocalizationX > Google Spreadsheets`

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
3. Copy the __Client ID__ and __Client Secret__, and paste them into the corresponding settings in the __Settings__

### 3.2. Export Google Spreadsheets

![google_tab](https://github.com/user-attachments/assets/9bfaeca9-3735-4eea-91be-7f8e936a6509)
![edit_google_sheets](https://github.com/user-attachments/assets/a04f35d1-1b1f-4e54-9625-f6fd9c0d92bb)

Click on `Add Google Spreadsheets`, then enter the Google Sheet ID in the popup. Click `Download`, then select the sheets you want to process.

The Google Sheet ID is in the URL of the Google Sheet, like this:

```url
https://docs.google.com/spreadsheets/d/[GOOGLE_SHEET_ID]/edit?......
```

## 4. Rules in Spreadsheet

### 4.1. IDs

| Hero   |     |         | Building      |     |         | Pet      |     |         | Gender[enum]      |     |
| ------ | --- | ------- | ------------- | --- | ------- | -------- | --- | ------- | ----------------- | --- |
| HERO_1 | 1   | comment | BUILDING_NULL | 0   | comment | PET_NULL | 0   | comment | GENDER_NONE       | 0   |
| HERO_2 | 2   | comment | BUILDING_1    | 1   |         | PET_1    | 1   |         | GENDER_MALE       | 1   |
| HERO_3 | 3   | comment | BUILDING_2    | 2   |         | PET_2    | 2   |         | GENDER_FEMALE     | 2   |
|        |     |         | BUILDING_3    | 3   |         | PET_3    | 3   |         | GENDER_HELICOPTER | 3   |
|        |     |         | BUILDING_4    | 4   |         | PET_4    | 4   |         |                   |     |
|        |     |         | BUILDING_5    | 5   |         | PET_5    | 5   |         |                   |     |
|        |     |         | BUILDING_6    | 6   |         | PET_6    | 6   |         |                   |     |
|        |     |         | BUILDING_7    | 7   |         | PET_7    | 7   |         |                   |     |
|        |     |         | BUILDING_8    | 8   |         |          |     |         |                   |     |

ID Sheets, named with the suffix `IDs` are used to compile all IDs into Integer Constants. The design rules are:

- The sheet name must end with `IDs`.
- Only the Integer data type is allowed.
- Each group is organized in 3 consecutive columns.
- The first row contains the group name for easy reference.
- The first column holds the Key Name, and the next column holds the Key Value.
- Key Value must be an integer.
- By default, all IDs in a column will be exported as Integer Constants. Add the suffix [enum] to the group name to export them as an enum.
- To only export enums and skip Integer Constants, select `Only enum as IDs` in the Settings.

```a
| Group | Key | Comment |
| ----- | --- | ------- |
```

### 4.2. Localization

| idstring     | relativeId | english                   | spanish                        |
| ------------ | ---------- | ------------------------- | ------------------------------ |
| message_1    |            | this is english message 1 | este es el mensaje en ingles 1 |
| message_2    |            | this is english message 2 | este es el mensaje en ingles 2 |
| message_3    |            | this is english message 3 | este es el mensaje en ingles 3 |
|              |            |                           |                                |
| content      | 1          | this is english message 1 | este es el mensaje en ingles 1 |
| content      | 2          | this is english message 2 | este es el mensaje en ingles 2 |
| content      | 3          | this is english message 3 | este es el mensaje en ingles 3 |
|              |            |                           |                                |
| title_1      |            | this is english title 1   | este es el titulo 1 en ingles  |
| title_2      |            | this is english title 2   | este es el titulo 2 en ingles  |
| title_3      |            | this is english title 3   | este es el titulo 3 en ingles  |
|              |            |                           |                                |
| whatever_msg |            | this is a sample message  | este es un mensaje de muestra  |
|              |            |                           |                                |
| hero_name    | HERO_1     | hero name 1               | nombre del héroe 1             |
| hero_name    | HERO_2     | hero name 2               | nombre del héroe 2             |
| hero_name    | HERO_3     | hero name 3               | nombre del héroe 3             |

Localization Sheets are named with the prefix `Localization` and follow these rules:

- TThe sheet name must start with `Localization`.
- Each sheet has two key columns: the main key `idString` and an additional key `relativeId`.
- The following columns contain localized content.
- The key for each row is a combination of `idString` and `relativeId`.
- `relativeId` can reference an ID from the IDs sheets.

```a
| idString | relativeId | english | spanish | japan | .... |
| -------- | ---------- | ------- | ------- | ----- | ---- |
```

## 5. How to integration

__Download and import the [Example](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/LocalizationX/LocalizationXExample.unitypackage)__

First, open the Excel file at `/Assets/LocalizationX/Example/Example.xlsx`. This file has example data to help you understand how to create Localization sheets

![Excel File](https://github.com/user-attachments/assets/2b4c8fe3-3c58-42bc-a85b-dea33c8122cf)

### 5.1. Create folders for exporting files

Create 2 directories to store the files that will be exported:

- A folder to store the C# scripts (IDs, Localization Component, Localization API).
- A folder to store the Localization data.

  - There are two ways to set up the folder for Localization data, depending on how you want to load Localizations:
    - The easiest method is to load from the Resources folder. Create a folder inside the Resources folder to store Localization data. You can name this folder anything you like.
    - Alternatively, use the Addressable Asset System. In this case, create a "Localizations" folder outside the Resources folder and set it as an Addressable Asset. It's recommended to name this folder "Localizations".

- Navigate to `Window > LocalizationX > Settings`
- In Settings, set up the paths for the "__Scripts Output Folder__", and "__Localization Output Folder__" using the folders you just created.

Create 2 folders:

- `Assets\LocalizationXExample\Scripts\Generated`: for C# scripts
- `Assets\LocalizationXExample\Resources\Localizations`: for Localization data

### 5.2. Scripting

- Initialization

```cs
LocalizationManager.Init();
```

- Change the language.

```cs
// Set the language japanese
LocalizationsManager.CurrentLanguage = "jp";
```

- Register an event handler for the language change event.

```cs
// Register an action when language changed
LocalizationsManager.OnLanguageChanged += OnLanguageChanged;
```

- You can retrieve localized content using three different methods.

  1. Retrieve localized content using a Key. Note that the text will not automatically refresh when the language changes using this method.

      ```cs
      // Retrieve localized text using an integer key
      m_simpleText1.text = LocalizationExample2.Get(LocalizationExample2.GO_TO_SHOP).ToString();
      // Retrieve localized text using an integer key with an argument
      m_simpleText2.text = LocalizationExample2.Get(LocalizationExample2.REQUIRED_CITY_LEVEL_X, 10).ToString();
      // Retrieve localized text using a string key with an argument
      m_simpleText3.text = LocalizationExample2.Get("REQUIRED_CITY_LEVEL_X", 25).ToString();
      ```

  2. Link a GameObject containing a Text or TextMeshProUGUI component with a key so that the text automatically updates when the language changes.

      ```cs
      // Register dynamic localized text using an integer key
      LocalizationExample2.RegisterDynamicText(m_dynamicText1.gameObject, LocalizationExample2.TAP_TO_COLLECT);
      // Register dynamic localized text using an integer key with an argument
      LocalizationExample2.RegisterDynamicText(m_dynamicText2.gameObject, LocalizationExample2.REQUIRED_LEVEL_X, "3");
      // Register dynamic localized text using a string key with an argument
      LocalizationExample2.RegisterDynamicText(m_dynamicText3.gameObject, "REQUIRED_LEVEL_X", "30");
      // Unregister the gameObject
      Localization.UnregisterDynamicText(m_textGameObject1);
      Localization.UnregisterDynamicText(m_textGameObject2);
      Localization.UnregisterDynamicText(m_dynamicText3);
      ```

  3. Using Localization Component.

      ![Using Localization Component](https://github.com/user-attachments/assets/0f0214b9-51ed-44bf-9b27-f2a210e6f0f6)

#### Combine Localizations

If you want to combine all Localization Sheets, simply deselect the __Separate Localization Sheets__ checkbox in the __Settings__. Next, delete all generated files and re-export everything.

Then, replace instances of __LocalizationExample1__ and __LocalizationExample2__ with __Localization__. Also, replace component __LocalizationExample1Text__ and __LocalizationExample2Text__ with __LocalizationText__.

#### Creating TextMeshPro Fonts for Different Languages

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

#### Loading Localization Using the Addressable Assets System

To utilize this feature, follow these steps:

- Install the Addressable Assets System.
- Add `ADDRESSABLES` to the directives list in the Build __Settings__.
- Move the Localizations folder out of the Resources folder. Additionally, relocate the Output folder in the __Settings__.
- Set the Localizations folder as an Addressable Asset.

![LocalizationX Settings](https://github.com/user-attachments/assets/ee17fdaa-c951-4f9c-8a6b-a5e2614db546)

![Localizations Folder](https://github.com/user-attachments/assets/1ecf2ae1-00e9-4c9f-9056-2867d04e8ee1)

![Build Settings](https://github.com/user-attachments/assets/229da607-da10-4b87-b799-5d9549e5620d)