# fb_messages_search

> Have you ever scrolled your chat history for hours just to find that one youtube video your friend sent you? You don't have to anymore!

Facebook Messages Search is a [Vue.js](https://vuejs.org/) application for viewing and searching your Facebook chat threads. The application works locally with exported data from Facebook (see [Exporting your data](#exporting-your-data)).

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

## Exporting your data

The process is explained in the [Facebook Help Center](https://www.facebook.com/help/212802592074644). Log in to Facebook and navigate to **Settings**. Click **Your Facebook Information** in the left column and then **Download Your Information**. 

In the **Download Your Information** page you may click **Deselect All** in the **Your Information** list and select **Messages** only. Finally change the **Format** to **JSON** and click **Create File**.

After some time you will get an email with the information that your export is ready. After that head to the **Download Your Information** page again and download the data in **Available Copies** section. You will get a zip file named `facebook-yourusername.zip`, in it there will be subfolders with `.json` files. Those are your chat histories you can drag-and-drop into this application.