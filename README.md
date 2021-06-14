 <img width="56" alt="Screen Shot 2021-06-14 at 11 36 21 AM" src="https://user-images.githubusercontent.com/54046179/121941988-d01c2200-cd04-11eb-8638-3cfde3dc74dc.png"> 
 
# google-sheets-qrcodes


## What does this template allow me to do ❔

#### Follow along this README and you'll be able to write QRCodes to Google Sheets via GraphQL mutations, using StepZen.

## Step 1 - Get Set Up

The first thing you'll need to do is [sign up for a StepZen account](https://stepzen.com/request-invite). 

We also assume that you've got a [Google Cloud account](https://cloud.google.com/gcp/?utm_source=google&utm_medium=cpc&utm_campaign=na-US-all-en-dr-bkws-all-all-trial-e-dr-1009892&utm_content=text-ad-none-any-DEV_c-CRE_491414382407-ADGP_Desk%20%7C%20BKWS%20-%20EXA%20%7C%20Txt%20~%20Storage%20~%20Cloud%20Storage_Cloud_General-KWID_43700060017821807-kwd-46560699950&utm_term=KW_google%20cloud%20account-ST_google%20cloud%20account&gclid=CjwKCAjw_JuGBhBkEiwA1xmbRWtFVfXdbClosETqtU2n3Gp4_5xkUwdhjkiJj4qDY7HEeGS1Hl5cSBoCkSsQAvD_BwE&gclsrc=aw.ds), and that you've [forked this repo](https://docs.github.com/en/github/getting-started-with-github/quickstart/fork-a-repo) and opened it in your IDE.

Now, click https://sheets.new and create a Google Sheet. Name the first column "Text" and the next "QRCode". If you want, you could put in different names, but they need to be valid GraphQL names; no spaces. [Turn on Link Sharing to "Anyone With the Link"](https://uca.edu/it/knowledgebase-legacy/google-drive-sharing-a-file-using-a-link/#:~:text=Open%20a%20file%20in%20Google,to%20Anyone%20with%20the%20link.).

## Step 2 - Create a Google Cloud Service Account and Grant Access to your Google Sheet

You'll need to create a service account, download the service account json file, grant access to the sheet for that service account, and then tell StepZen about the service account. You'll only need to do this step once.

First, log into https://console.cloud.google.com. Free trial should work fine, if you don’t have one already.

- Next, in the search bar at the top of the Google Cloud Platform console, type “Service Accounts” and select the entry called “Service Accounts”. 
- Click the “+ Create service account” link at the top. 
- Give the service account any name. 
- Click the "create" button, then click the "done" button.

In the next step we create a JSON keyfile for this service account. You’ll download this to your computer and use it later. 

- Now click on the service account you just created in the list of service accounts. It will be something like “accountname@projectid.iam.gserviceaccount.com” 
- Under the KEYS tab on the resulting screen, click the Add Key button and select “Create New Key”
- Leave the default selected (JSON) and click CREATE. A JSON file will be saved to your computer. We’ll use this file later when configuring StepZen to do support mutations on the sheet, so don’t lose it.
- Finally, click the DETAILS tab for the service account, and copy the Email value (it’s just a regular email address).

Now on your Google Sheet, [grant Editor access to the email address for your service account](https://blog.sheetgo.com/google-sheets-features/google-sheets-permission-levels/#Permissionlevel#2:Edit). 

Now that service account can write to your sheet! Hooray.

> Note: Make sure you've turned on the Sheets API for the Google project you’re in.

## Step 3 - Obtaining Your Authorization Value

You'll need to get you authorization value for connecting the dots between StepZen and your Cloud Service.

Remember that json file you downloaded earlier? Your authorization value is the base64-encoded contents of that file.

To encode it, you execute a command like so in a terminal:

```bash
base64 -w 0 ~/path/to/file.json
```

You'll get your authorization value printed out, ending with `==`. 

## Step 4 - Connecting to Google Cloud and Deploying to StepZen


Notice that you've got a `.gitignore` file in your fork with `config.yaml` in it. 

Create a `config.yaml` in your working directory, and add the following:

```yaml
configurationset:
  - configuration: 
      name: QRCodeSheets
      authorization: Bearer {{authorization_value_here}}
```

Now in your terminal, run 

```bash
stepzen start
```

You'll be prompted to enter your StepZen account name and admin key, which you can find on your account page once you [sign in](https://login.stepzen.com/login?state=hKFo2SBoWjFYZ3Npb212TmRwZEtWOEtpS05KR1RHNGowTnNfZqFupWxvZ2luo3RpZNkgSzlTaTU0cEliYjJVeVBoaU05ZXRrV3V3NXdNcFQtTy2jY2lk2SA3UG9mU2I3NnBXNFRZdEg2T01jNFEwQWZ2bW96N20xUg&client=7PofSb76pW4TYtH6OMc4Q0Afvmoz7m1R&protocol=oauth2&scope=openid%20profile%20email&response_type=code&redirect_uri=https%3A%2F%2Fmy.stepzen.com%2Fapi%2Fauth%2Fcallback&nonce=2NKijFmXpknSVyweYGLAzr1IzzIJuh_Ca6KyOBDvN5w&code_challenge=FjWyKls61QwBb6GTlu59gxl12qyzl5pJhPUcW4vm7xo&code_challenge_method=S256). 

You'll also be prompted to name your endpoint-- use the suggested value or name it what you want. 

Then, our StepZen Code Explorer in the form of our custom graphiql browser will pop up, available on `localhost:5000`.

Click on the tab that says "Explorer" and select the dropdown under "addQRCode", then tick off all the boxes beneath it. This will automatically fill out your query like so:

```graphql
mutation myMutation {

  addQRCode(
    QRCode__B: 
    ""
    Text__A: 
   "") {
    QRCode__B
    Text__A
  }
}

```


Now, you'll need to add the parameter values to the query. 

In text__B you'll need to copy and paste the following (a Sheets equation for generating a QR Code): 

`=image(\"https://image-charts.com/chart?chs=150x150&cht=qr&choe=UTF-8&chl=\"&ENCODEURL(A2))`

In text__A you can add whatever string you want for the equation to use.

You'll end up with something like:

```graphql
mutation myMutation {

  addQRCode(
    QRCode__B: 
    "=image(\"https://image-charts.com/chart?chs=150x150&cht=qr&choe=UTF-8&chl=\"&ENCODEURL(A2))"
    Text__A: 
   "random_string") {
    QRCode__B
    Text__A
  }
}

```

Now, hit the play button. If you get the equation and the random string returned to you in JSON form, you know your query worked! 

<img width="1789" alt="Screen Shot 2021-06-14 at 11 21 44 AM" src="https://user-images.githubusercontent.com/54046179/121940296-c85b7e00-cd02-11eb-89fa-51bcab4dfa9f.png">

Check out your Google Doc to see the result! 

<img width="814" alt="Screen Shot 2021-06-14 at 11 28 40 AM" src="https://user-images.githubusercontent.com/54046179/121941096-b5957900-cd03-11eb-83f7-58bb15d7eaf7.png">




