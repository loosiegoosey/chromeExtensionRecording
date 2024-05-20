## Overview

Overview

Chrome Extension Recording is a project designed to record user interactions with a web page in the form of events. These recorded events can be stored, managed, and potentially replayed. This tool can be useful for testing, user experience research, or automation purposes.

##
## Features

Features

- **Event Recording**: Records various user events such as clicks, double-clicks, changes, keydown, etc.
- **Storage**: Stores recorded events using IndexedDB.
- **Communication**: Utilizes Chrome's messaging API for communication between different extension components.
- **Easy Setup**: Simple setup and installation procedure.
- **Popup Interface**: Provides an interactive popup to manage recordings.

##
## Installation Instructions

Installation Instructions

1. **Clone the repository**:
    ```sh
    git clone https://github.com/RenaultZC/chromeExtensionRecording.git
    cd chromeExtensionRecording
    ```

2. **Install Dependencies**:
    ```sh
    npm install
    ```

3. **Build the Project**:
    ```sh
    npm run build
    ```

4. **Load the Extension**:
    - Open Chrome and navigate to `chrome://extensions/`.
    - Enable "Developer mode".
    - Click "Load unpacked" and select the `build` directory from this project.

##
## Usage Examples

Usage Examples

1. **Start Recording**:
    - Navigate to a web page where you want to record events.
    - Click on the extension's icon in the toolbar and start recording.

2. **Interacting with Elements**:
    ```javascript
    // This event will be captured by the event recorder
    document.querySelector('button').click();
    ```

3. **Check Recorded Events**:
    - Open the extension popup to see the list of recorded events.
    - You can clear, delete or replay these events as needed.

##
## Code Summary

Code Summary

### webpack.config.js
Configures Webpack for the project:
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');
const CopyPlugin = require('copy-webpack-plugin');

module.exports = {
  entry: {
    background: './src/background/background.js',
    page: './src/page/page.js',
    popup: './src/popup/popup.js',
    'content-script': './src/page/eventRecorder.js'
  },
  output: {
    path: path.join(__dirname, '/build'),
    filename: '[name].js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /no
```

### app\src\app.js
Sets up a Koa server and uses Puppeteer for browser automation:
```javascript
const Koa = require('koa');
const Router = require('koa-router');
const Cors = require('@koa/cors');
const koaBody = require('koa-body');
const puppeteer = require('puppeteer');

const app = new Koa();
const router = new Router();

app.use(Cors({ credentials: true }));
app.use(koaBody());

// Additional logic and route handlers...
```

### src\background\background.js
Handles background scripts and message passing:
```javascript
let recordingController = [];

chrome.runtime.onMessage.addListener((msg, sender) => {
  if (msg === 'clear') {
    console.log('clear');
    recordingController = [];
  } else {
    recordingController.push(msg);
    msg.frameId = sender ? sender.frameId : null;
    msg.frameUrl = sender ? sender.url : null;
  }
  chrome.storage.local.set({ recording: recordingController }, () => {
    console.debug('stored recording updated', recordingController);
  });
});
```

### src\page\eventRecorder.js
Records user events on the web page:
```javascript
import finder from '@medv/finder';

const eventsToRecord = {
  CLICK: 'click',
  DBLCLICK: 'dblclick',
  CHANGE: 'change',
  KEYDOWN: 'keydown',
  SELECT: 'select',
  SUBMIT: 'submit',
  LOAD: 'load',
  UNLOAD: 'unload'
};

class EventRecorder {
  constructor() {
    this.eventLog = [];
    this.previousEvent = null;
    this.dataAttribute = null;
    this.isTopFrame = (window.location === window.parent.location);
  }

  start() {
    this._initializeRecorder();
  }

  _initializeRecorder() {
    // Recorder initialization logic...
  }
}
```

### src\page\indexdb.js
Handles IndexedDB operations:
```javascript
class indexDB {
  constructor(datebaseName, version, store) {
    this.indexedDB = 
      window.indexedDB ||
      window.webkitindexedDB ||
      window.msIndexedDB ||
      window.mozIndexedDB;

    this.datebaseName = datebaseName;
    this.version = version;
    this.store = store;
  }

  init = async () => {
    this.request = this.indexedDB.open(this.datebaseName, this.version);
    this.request.onerror = () => {
      console.log("打开数据库失败");
    };
    this.request.onsuccess = () => {
      console.log("打开数据库成功");
    };
  };
}
```

### src\page\page.js
Interface for handling storage and event data:
```javascript
import indexDB from './indexdb';

let indexdb = new indexDB('record', 2, {
  'recordHistroy': {
    name: 'recordHistory',
    key: 'key',
    cursorIndex: []
  }
});
indexdb.init();

let data = { flag: false };

let getStroage = async () => {
  return new Promise((res, rej) => {
    try {
      chrome.storage.local.get(['recording'], ({ recording }) => {
        res(recording);
      });
    } catch (e) {}
  });
};

let cleartroage = () => {
  return new Promise((res, rej) => {
    try {
      chrome.storage.local.clear(() => res());
    } catch (e) {}
  });
};
```

### src\popup\popup.js
Manages the extension popup interface:
```javascript
let initPage = (dom, html) => {
  html = html.map(value => {
    return `
      <tr>
        <td>${value.key}</td>
        <td>${value.name}</td>
        <td><button class="btn btn-outline-success">使用</button></td>
        <td><button class="btn btn-outline-danger">删除</button></td>
      </tr>
    `;
  });

  html.unshift(`
    <tr>
      <th>序号</th>
      <th>名称</th>
      <th>使用</th>
      <th>删除</th>
    </tr>
  `);

  dom.innerHTML = html.join('');
};

document.addEventListener('DOMContentLoaded', () => {
  // Initialize the popup
});
```

##
## License

License

This project is licensed under the MIT License. See the `LICENSE` file for details.

---

Feel free to contribute, report issues, or suggest features by creating an issue or pull request in the [repository](https://github.com/RenaultZC/chromeExtensionRecording).