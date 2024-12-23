### How to enable Google Sheet API and get Google Client id and Secret Id

#### Step 1: Enable Google Sheets API

1. Visit the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project or select an existing one.

![1-create-project](https://github.com/user-attachments/assets/e890e598-31db-423d-9c50-42fa1ca43ef0)

3. Click on __Go to APIs overview__.

![2-go-to-apis](https://github.com/user-attachments/assets/f4550457-b244-4dd1-bc88-729558c390a7)

4. Select __Enable APIs and Services__.

![3-enable-apis-and-services](https://github.com/user-attachments/assets/838e48bd-d179-47e4-bf4c-121c995a2299)

5. Search for and select __Google Sheets API__, then click __Enable__.

![4-search-for_google-sheets-api](https://github.com/user-attachments/assets/43b7fb49-1762-416a-b520-d8a470909d5e)
![5-enable-google-sheets_api](https://github.com/user-attachments/assets/5e5caa32-973b-459c-b8a5-8c8554590036)

#### Step 2: Obtain Credentials

1. On the top Google Sheets API screen, click on __Create Credentials__.

![6-create-credentials](https://github.com/user-attachments/assets/9018736f-c58f-41af-89cf-3f136f095abe)

2. Choose __Google Sheets API__, __User data__, then click __Next__.

![7-credential-type](https://github.com/user-attachments/assets/e6b4d0bd-5f7e-42da-90d0-659f21bbc84a)

3. Add the information for OAuth Consent Screen, then click __Save And Continue__
4. In the Scopes section, click on __Add or remove scopes__.

![8-add-scopes](https://github.com/user-attachments/assets/83f154be-add2-4ee2-b327-f1b1dcffd231)

5. Find and select the __Google Sheets API__ (description: "See all your Google Sheets Spreadsheets"), click __Update__ then __Save and Continue__.

![9-add-google-sheets-api-scope](https://github.com/user-attachments/assets/566f0f84-2ea6-41f8-9da3-7356d9a14514)

6. In the OAuth Client ID section, select Application Type as Desktop App, enter any name, then click __Create__.

![10-create-desktop-app](https://github.com/user-attachments/assets/2fe08124-6428-4965-9032-a1932d6b07d9)

7. Click __Done__.

#### Step 3: Accessing Your Client ID and Client Secret

1. On the Google Sheets API screen, go to the __Credentials__ tab, you will find the new Client ID.

![11-find-credentials](https://github.com/user-attachments/assets/d750be76-2586-4f33-87bf-87569056006c)

2. Click on the Edit button to find the Client ID and Client Secret.
3. Copy the __Client ID__ and __Client Secret__, and paste them into the corresponding settings in the __Sheets Exporter Settings__ Window

![12-copy-client-ids-and-secret-id](https://github.com/user-attachments/assets/e1ef2ec2-a447-4842-814e-fcefad162977)