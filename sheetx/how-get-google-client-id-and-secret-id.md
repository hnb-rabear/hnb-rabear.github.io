### How to enable Google Sheet API and get Google Client id and Secret Id

#### Step 1: Enable Google Sheets API

1. Visit the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project or select an existing one.
3. Click on __Go to APIs overview__.
4. Select __Enable APIs and Services__.
5. Search for and select __Google Sheets API__, then click __Enable__.

#### Step 2: Obtain Credentials

1. On the top Google Sheets API screen, click on __Create Credentials__.
2. Choose __Google Sheets API__, __User data__, then click __Next__.
3. Add the information for OAuth Consent Screen, then click __Save And Continue__
4. In the Scopes section, click on __Add or remove scopes__.
5. Find and select the __Google Sheets API__ (description: "See all your Google Sheets Spreadsheets"), click __Update__ then __Save and Continue__.
6. In the OAuth Client ID section, select Application Type as Desktop App, enter any name, then click __Create__.
7. Click __Done__.

#### Step 3: Accessing Your Client ID and Client Secret

1. On the Google Sheets API screen, go to the __Credentials__ tab, you will find the new Client ID.
2. Click on the Edit button to find the Client ID and Client Secret.
3. Copy the __Client ID__ and __Client Secret__, and paste them into the corresponding settings in the __Sheets Exporter Settings__ Window
