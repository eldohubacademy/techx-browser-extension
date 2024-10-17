# Building a Chrome extension

Building a Chrome extension is a great way to extend the browser's functionality, customize your browsing experience, or provide users with powerful new tools. In this workshop we will create a basic Chrome extension,write its functionality, and load it into your browser for testing.

### Step-by-Step Guide to Building a Chrome Extension

### 1. **Plan Your Extension**

- **Define the Purpose**: What functionality will your extension add? For example, you might want to create a tool for productivity, block certain content, or customize how websites appear.
- **Choose the Core Features**: Will the extension modify content on websites, add a browser action (a clickable icon), or provide background processing?

### 2. **Understand Chrome Extension Structure**

Every Chrome extension has a basic structure, which includes these key components:

- **manifest.json**: The configuration file that tells Chrome about the extension (permissions, scripts, etc.).
- **JavaScript files**: Code for the logic, interaction, and dynamic features.
- **HTML/CSS files**: These are for the popup window, options page, or any UI part of the extension.
- **Icons and images**: Used to represent the extension in the toolbar and menu.

### 3. **Set Up Your Extension’s Directory**

Create a directory where your extension's files will be stored. Let’s name it `my-extension/`. Inside that directory, you’ll have the following basic files:

```
my-extension/
│
├── manifest.json
├── popup.html (optional for popup UI)
├── popup.js (optional for popup logic)
├── background.js (optional for background processes)
├── content.js (optional for modifying web pages)
├── icons/ (icon files)
```

### 4. **manifest.json**

This is the most important file that describes your extension to Chrome. It must follow the **JSON** format and include important fields like permissions, version, and script locations.

Here's a basic example of `manifest.json`:

```json
{
  "manifest_version": 3, // Manifest version 3 is the current standard for Chrome extensions
  "name": "My Extension",
  "version": "1.0",
  "description": "A simple Chrome extension",
  "action": {
    "default_popup": "popup.html", // Optional: HTML popup that opens when the extension icon is clicked
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "permissions": [
    "tabs", // Allows interaction with browser tabs
    "storage" // Allows saving data to the browser's local storage
  ],
  "background": {
    "service_worker": "background.js" // A background script to run processes
  },
  "content_scripts": [
    {
      "matches": ["<all_urls>"], // Runs the content.js script on all web pages
      "js": ["content.js"]
    }
  ],
  "icons": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  }
}
```

### 5. **Creating Key Files**

#### **popup.html**

This is the file that will show up when you click on your extension’s icon in the toolbar. It’s like a small webpage that can display UI elements and interact with JavaScript.

Here’s an example `popup.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My Extension</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        padding: 10px;
      }
    </style>
  </head>
  <body>
    <h1>Hello, Extension!</h1>
    <button id="changeColor">Change Background Color</button>
    <script src="popup.js"></script>
  </body>
</html>
```

#### **popup.js**

This JavaScript file contains the logic that runs in the popup. For example, it might change the background color of the current tab when a button is clicked:

```javascript
document.getElementById("changeColor").addEventListener("click", () => {
  chrome.tabs.query({ active: true, currentWindow: true }, (tabs) => {
    chrome.scripting.executeScript({
      target: { tabId: tabs[0].id },
      func: () => {
        document.body.style.backgroundColor = "lightblue";
      },
    });
  });
});
```

#### **background.js**

The background script runs in the background to handle events like opening new tabs, responding to messages, or tracking browsing activity. In **Manifest V3**, this script runs as a service worker.

Here’s a simple example:

```javascript
chrome.runtime.onInstalled.addListener(() => {
  console.log("Extension installed");
});
```

#### **content.js**

This script runs directly on web pages and can modify their content. For example, you might use it to remove ads or change the background color of any website:

```javascript
// Change the background color of every webpage to light grey
document.body.style.backgroundColor = "lightgrey";
```

### 6. **Icons**

Every extension needs an icon that appears in the browser toolbar and menu. You should have different sizes for different UI areas:

- **16x16**: Small icon for the toolbar.
- **48x48**: Medium icon for the extensions page.
- **128x128**: Large icon for when the extension is showcased.

Place your icon files in the `icons/` directory and reference them in `manifest.json`.

### 7. **Testing Your Extension**

1. Open Chrome and go to `chrome://extensions/`.
2. Enable **Developer Mode** by toggling the switch in the top-right corner.
3. Click **Load unpacked** and select the folder containing your extension (`my-extension/`).
4. Your extension will now be loaded into Chrome. You should see its icon in the toolbar.

   - Click on the icon to see your popup.
   - Open the **Console** (right-click -> Inspect -> Console) to check if your background or content scripts are logging any messages.

### 8. **Additional Features**

#### **Storage API**

If you need to save and retrieve data, you can use Chrome’s storage API. Here’s how to store and retrieve a value:

```javascript
// Store data
chrome.storage.sync.set({ color: "blue" }, () => {
  console.log("Color is set to blue");
});

// Retrieve data
chrome.storage.sync.get("color", (data) => {
  console.log("Color currently is " + data.color);
});
```

#### **Message Passing**

You can send messages between your background script, content scripts, and popup scripts. This is useful when you need to share data or coordinate actions across different parts of the extension.

Here’s an example of how to send a message from `popup.js` to `background.js`:

**popup.js**:

```javascript
chrome.runtime.sendMessage({ greeting: "hello" }, (response) => {
  console.log(response.farewell);
});
```

**background.js**:

```javascript
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  console.log(request.greeting); // Logs "hello"
  sendResponse({ farewell: "goodbye" });
});
```

### 9. **Publishing Your Extension**

Once your extension is working and thoroughly tested:

1. **Zip the Extension Folder**: Make sure all necessary files (manifest.json, HTML, JavaScript, icons) are in the directory and zip it.
2. **Chrome Web Store**: Create a developer account in the [Chrome Web Store Developer Dashboard](https://chrome.google.com/webstore/developer/dashboard).
3. **Upload the Extension**: Provide information about your extension, including a detailed description, screenshots, and the zipped file.
4. **Submit for Review**: Google will review your extension before it gets published.

### Summary of Steps

1. Create the `manifest.json` file.
2. Add the popup (optional), background script (optional), and content scripts to handle different functionalities.
3. Test the extension by loading it into Chrome via `chrome://extensions/`.
4. Debug the extension and ensure it works as expected.
5. Package and submit it to the Chrome Web Store.

By following these steps, you’ll have a working Chrome extension ready for use and publication. Let me know if you need more details on a specific feature or help debugging!
