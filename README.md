# Count unread messages in your Vue chat app

Read the full tutorial here:

>> Not yet published

This application detailed the approriate steps required to count unread messages in your Vue chat app. Furthermore, it also covers the procedure involved in marking a message as sent, delivered and read respectively in a group chat powered by CometChat

Take a look at the screenshot below:

<img src="screenshots/screenshot_1.png">


## Technology

This demo uses:
* [CometChat](https://cometchat.com/)
* [Vuejs](https://vuejs.org/)


## Running the demo
To run the demo follow these steps:

1. Head to the [CometChat dashboard](https://app.cometchat.com/) (you'll need to create a free account if you haven't already)
2. From the dashboard, create a new app called "vue-count-app"
3. Once created, click the button **Explore**
4. Click **API Keys** on the left-hand-side and note the automatically-generated Full access API Key and the application ID as well
5. Go to the **Groups** tab and note the GUID of the group automatically created by CometChat
6. Download the repository [here](https://github.com/cometchat-pro-tutorials/vue-read-indicators-tut/archive/master.zip) or by running `git clone https://github.com/cometchat-pro-tutorials/vue-read-indicators-tut.git`
7. Run `npm install` to install all the project's dependencies
8. Create a `.env` file with the root folder of the project and paste the following content in it:

```
VUE_APP_COMMETCHAT_API_KEY=YOUR_API_KEY	
VUE_APP_COMMETCHAT_APP_ID=YOUR_APP_ID
VUE_APP_COMMETCHAT_GUID=YOUR_GROUP_GUID
```
Replace `YOUR_API_KEY`, `YOUR_APP_ID` and `YOUR_GROUP_GUID` with your API KEY, APP ID and GUID as obtained from your CometChat dashboard.

9. Run `npm run serve` to start the application and navigate to `http://localhost:8080` to view the app

<img src="screenshots/screenshot_4.png">

10. Open the application in two separate windows and log in from one of the windows with any two of the test users: superhero1, superhero2, or superhero3 and type one or two messages

<img src="screenshots/screenshot_2.png">

11. Now, log in from the other windows and observe the status of the messages indicated as delivered:

<img src="screenshots/screenshot_3.png">


## Useful links
* 🏠 [CometChat Homepage](https://www.cometchat.com/pro)
* 🚀 [Create your free account](https://app.cometchat.com/#/apps)
* 📚 [Documentation](https://prodocs.cometchat.com/docs)
* 👾 [GitHub](https://github.com/CometChat-Pro)
