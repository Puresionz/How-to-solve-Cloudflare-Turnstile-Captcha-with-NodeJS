# How-to-solve-Cloudflare-Turnstile-Captcha-with-NodeJS
How to solve Cloudflare Turnstile Captcha with NodeJS



## ⚙️ Prerequisites
- NodeJs installed
- Capsolver API key

## 🤖 Step 1: Install Necessary Packages
Execute the following commands to install the required packages:

```python
npm i axios
```

## 👨‍💻 Step 2: NodeJS Code for solve Cloudflare Turnstile Captcha

Here's a Python sample script to accomplish the task:

```js
const axios = require('axios');
const CAPSOLVER_API_KEY = "your api key";
const PAGE_URL = "site ";
const WEBSITE_KEY = "site key";

function solvecf(metadata_action = null, metadata_cdata = null) {
    const url = "https://api.capsolver.com/createTask";
    const task = {
        type: "AntiTurnstileTaskProxyLess",
        websiteURL: PAGE_URL,
        websiteKey: WEBSITE_KEY,
    };
    if (metadata_action || metadata_cdata) {
        task.metadata = {};
        if (metadata_action) {
            task.metadata.action = metadata_action;
        }
        if (metadata_cdata) {
            task.metadata.cdata = metadata_cdata;
        }
    }
    const data = {
        clientKey: CAPSOLVER_API_KEY,
        task: task
    };
    return axios.post(url, data)
        .then(response => {
            console.log(response.data);
            return response.data.taskId;
        });
}

function solutionGet(taskId) {
    const url = "https://api.capsolver.com/getTaskResult";
    let status = "";
    const checkStatus = () => {
        const data = { clientKey: CAPSOLVER_API_KEY, taskId: taskId };
        return axios.post(url, data)
            .then(response => {
                console.log(response.data);
                status = response.data.status || "";
                console.log(status);
                if (status === "ready") {
                    return response.data.solution;
                }
                return new Promise(resolve => setTimeout(resolve, 2000)).then(checkStatus);
            });
    };
    return checkStatus();
}

async function main() {
    try {
        const taskId = await solvecf();
        const solution = await solutionGet(taskId);
        if (solution) {
            const user_agent = solution.userAgent;
            const token = solution.token;
            console.log("User_Agent:", user_agent);
            console.log("Solved Turnstile Captcha, token:", token);
        }
    } catch (error) {
        console.error("Error in CAPSOLVER API interaction:", error);
    }
}

main();


```

## ⚠️ Change these variables
- **CAPSOLVER_API_KEY**: Obtain your API key from the [Capsolver Dashboard](https://capsolver.com/dashboard).
- **PAGE_URL**: Replace with the URL of the website for which you wish to solve the CloudFlare Turnstile Captcha.
- **WEBSITE_KEY**: Replace with the site key of the website 

### What the CloudFlare Turnstile Captcha Looks Like
![Cloudflare Turnstile Captcha](https://assets.capsolver.com/prod/images/post/2024-05-13/aafc0187-0920-4e61-8000-5347209fb122.png)


Meanwhile, if you'd like to test your scripts for bot characteristics, BrowserScan's [Bot Detection](https://www.browserscan.net/bot-detection) tool can help you identify and refine bot-like behavior in your scripts.
