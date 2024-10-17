# Chrome Extension APIs

In the process of building a Chrome extension, several **Chrome Extension APIs** are used to interact with the browser, tabs, and web content. Below is an overview of the APIs used in the example above, along with explanations of their functionality and how they work.

### 1. **chrome.runtime API**

This API is used to interact with the background processes of your extension, respond to installation events, and handle messaging between different parts of the extension.

#### Example: `chrome.runtime.onInstalled`

- **Purpose**: This event is triggered when the extension is installed or updated. It can be used for setup actions, such as initializing data.

```javascript
chrome.runtime.onInstalled.addListener(() => {
  console.log("Extension installed");
});
```

#### Example: `chrome.runtime.sendMessage`

- **Purpose**: This is used for message passing between different parts of the extension, like from a popup to a background script.

```javascript
// Send a message from popup.js to background.js
chrome.runtime.sendMessage({ greeting: "hello" }, (response) => {
  console.log(response.farewell); // Logs the response from background.js
});
```

#### Example: `chrome.runtime.onMessage`

- **Purpose**: This event listener in the background script listens for messages sent from other parts of the extension (e.g., content scripts, popups).

```javascript
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  console.log(request.greeting); // Logs "hello"
  sendResponse({ farewell: "goodbye" });
});
```

### 2. **chrome.action API** (Previously `chrome.browserAction` in MV2)

This API controls the extension’s icon in the Chrome toolbar, providing the functionality to display a popup, change the icon, or trigger specific actions.

#### Example: `chrome.action.default_popup`

- **Purpose**: Specifies the HTML file to be shown as a popup when the extension icon is clicked.

```json
"action": {
  "default_popup": "popup.html",
  "default_icon": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  }
}
```

### 3. **chrome.tabs API**

The `chrome.tabs` API is used to interact with the browser’s tab system. It can manipulate tabs, retrieve information about them, or execute scripts within them.

#### Example: `chrome.tabs.query`

- **Purpose**: Used to get information about the current tab (e.g., the active tab in the current window).

```javascript
chrome.tabs.query({ active: true, currentWindow: true }, (tabs) => {
  console.log(tabs[0].url); // Logs the URL of the active tab
});
```

#### Example: `chrome.scripting.executeScript`

- **Purpose**: This is the MV3 way to inject JavaScript into a tab (previously done with `chrome.tabs.executeScript` in MV2). It allows you to dynamically execute a function within the context of a webpage.

```javascript
chrome.scripting.executeScript({
  target: { tabId: tabs[0].id },
  func: () => {
    document.body.style.backgroundColor = "lightblue";
  },
});
```

### 4. **chrome.scripting API**

The `chrome.scripting` API replaces the older `chrome.tabs.executeScript` function in MV3 and is used to programmatically inject JavaScript and CSS into web pages.

#### Example: `chrome.scripting.executeScript`

- **Purpose**: Injects JavaScript into the specified tab to manipulate the web page's DOM or interact with the page in other ways.

```javascript
chrome.scripting.executeScript({
  target: { tabId: tabs[0].id },
  func: () => {
    document.body.style.backgroundColor = "lightgrey";
  },
});
```

### 5. **chrome.storage API**

This API is used to store, retrieve, and manage data within your extension. It supports both local storage (specific to the device) and sync storage (syncs data across devices if the user is logged into Chrome).

#### Example: `chrome.storage.sync.set`

- **Purpose**: Store data that can be synced across devices using the user's Chrome account.

```javascript
chrome.storage.sync.set({ color: "blue" }, () => {
  console.log("Color is set to blue");
});
```

#### Example: `chrome.storage.sync.get`

- **Purpose**: Retrieve data that has been stored in Chrome’s synced storage.

```javascript
chrome.storage.sync.get("color", (data) => {
  console.log("Color currently is " + data.color);
});
```

### 6. **chrome.declarativeNetRequest API** (For MV3 Ad Blocking)

The `chrome.declarativeNetRequest` API is used to block or modify network requests. This is a replacement for the `webRequestBlocking` API in **Manifest Version 3** (MV3), which offers better performance and security.

#### Example: Blocking ads with `declarativeNetRequest`

```javascript
chrome.declarativeNetRequest.updateDynamicRules({
  addRules: [
    {
      id: 1,
      priority: 1,
      action: { type: "block" },
      condition: {
        urlFilter: "*ad*",
        resourceTypes: ["script", "image", "xmlhttprequest"],
      },
    },
  ],
  removeRuleIds: [1],
});
```

### 7. **chrome.contentScripts API**

In **Manifest Version 3**, the `chrome.contentScripts` API is used to inject content scripts into web pages that match specified URLs or conditions.

#### Example: Declaring a Content Script in `manifest.json`

```json
"content_scripts": [
  {
    "matches": ["<all_urls>"],  // Runs the content.js script on all web pages
    "js": ["content.js"]
  }
]
```

#### Example: content.js (Manipulating Web Page DOM)

This script runs within the web page and interacts with its DOM.

```javascript
// Change the background color of every webpage to light grey
document.body.style.backgroundColor = "lightgrey";
```

### 8. **chrome.permissions API**

This API is used to request permissions dynamically from the user (in addition to the ones declared in `manifest.json`).

#### Example: Requesting Permissions

```javascript
chrome.permissions.request(
  {
    permissions: ["tabs", "storage"],
    origins: ["https://www.example.com/"],
  },
  (granted) => {
    if (granted) {
      console.log("Permissions granted");
    } else {
      console.log("Permissions denied");
    }
  }
);
```

### Overview of APIs Used

| API                            | Purpose                                                                                                         |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| `chrome.runtime`               | For managing background processes, installation events, and messaging between different parts of the extension. |
| `chrome.action`                | Controls the extension’s icon and popup in the toolbar.                                                         |
| `chrome.tabs`                  | Allows interaction with the browser's tabs (e.g., querying and manipulating tabs).                              |
| `chrome.scripting`             | Injects scripts into web pages (content scripts or dynamically executed code).                                  |
| `chrome.storage`               | Stores data locally or syncs across devices using the user's Chrome account.                                    |
| `chrome.declarativeNetRequest` | Blocks or modifies network requests (e.g., for ad-blocking).                                                    |
| `chrome.contentScripts`        | Automatically injects scripts into web pages based on URL patterns.                                             |
| `chrome.permissions`           | Manages permissions requested by the extension.                                                                 |

### Summary

These APIs work together to give you the power to:

- Modify the behavior of web pages.
- Communicate between different parts of the extension (like popups and background scripts).
- Handle network requests, storage, and permissions.
- Provide a user-friendly interface through a popup or background processing.

If you want more detailed information about any specific API or how to implement a feature, feel free to ask!
