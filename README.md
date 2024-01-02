# Microsoft Learn eShopLite Microservices Demo

<!-- vscode-markdown-toc -->
* 1. [About](#About)
* 2. [Docker Setup](#DockerSetup)
* 3. [How to Run Locally](#HowtoRunLocally)
* 4. [Notes](#Notes)
* 5. [Content Security Policy (CSP) Issues](#ContentSecurityPolicyCSPIssues)
	* 5.1. [Great Advice from AI on CSP Issues](#GreatAdvicefromAIonCSPIssues)
* 6. [References](#References)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

##  1. <a name='About'></a>About

This is a simple microservices demo with a frontend web app (razor) and backend web api (asp.net core). The frontend utilizes the Blazor WebAssembly script in the browser.

The backend web api simply delivers a Product listing and serves images for those products.

![backend web api demo screen capture](/images/webapidemo.jpg)

The frontend web app queries the backend web api for products, then displays each individual product record including an image.
![frontend demo screen capture](/images/frontenddemo.jpg)

##  2. <a name='DockerSetup'></a>Docker Setup 

There are two projects in the solution: Store (frontend) and Products (backend) that run as individual containers with distinct docker files.

![project setup](/images/projectsetup.jpg)

These projects are composed into one docker-compose.yml file shown below.

```
version: '3.4'

services: 

    frontend:
        image: storeimage
        build:
            context: .
            dockerfile: ./Store/Dockerfile
        environment: 
           - ProductEndpoint=http://backend:8080
           - ImagePrefix=http://localhost:32001/images
        ports:
           - "32000:8080"
        depends_on: 
           - backend
    backend:
        image: productservice
        build: 
            context: .
            dockerfile: ./Products/Dockerfile
        ports: 
           - "32001:8080"
```

##  3. <a name='HowtoRunLocally'></a>How to Run Locally 

First fork this project, then open up a terminal window in the root of your own repo directory. Download and install Docker Desktop (if needed). 

Build the docker image

```docker compose build```

Run the docker composition

```docker compose up```

Verify the containers are runing in Docker Desktop and that you can view the frontend app.

![docker desktop demo](/images/dockerdesktopdemo.jpg)

##  4. <a name='Notes'></a>Notes

This site only runs with http protocol. In the future, I plan on making a project tutorial for adding https capability with a trusted certificate.

##  5. <a name='ContentSecurityPolicyCSPIssues'></a>Content Security Policy (CSP) Issues

When loading the Products page in the web app, you may notice the page does not load or it says "Loading.." and stops. If you check the browser developer tools console window, you will probably see this error:

```
Refused to load the script 'http://localhost:32000/_framework/blazor.web.js' because it violates the following Content Security Policy directive: "script-src 'none'". Note that 'script-src-elem' was not explicitly set, so 'script-src' is used as a fallback.
```

The quick fix for me was to disable my browser extensions and reload (I think it was the ad blocker). I asked Microsoft Copilot about this error and received that helpful hint and additional information. If you do not wish to disable your extensions, if you load the page incognito mode, it should work and allow you to verify the app is working. 

###  5.1. <a name='GreatAdvicefromAIonCSPIssues'></a>Great Advice from AI on CSP Issues

It seems that you are seeing a different Content Security Policy (CSP) directive in normal mode and incognito mode of your browser. This could be due to one of the following reasons:

- You have an extension or add-on installed in your browser that modifies the CSP header or meta tag of the web page in normal mode, but not in incognito mode. Some extensions may do this to inject their own scripts or features into the web page¹. To check if this is the case, you can disable all your extensions and see if the problem persists. You can also compare the CSP header or meta tag of the web page in normal mode and incognito mode using the developer tools of your browser².
- You have a different browser setting or preference in normal mode and incognito mode that affects the CSP enforcement or reporting. For example, some browsers may have a setting to block third-party cookies or scripts in normal mode, but not in incognito mode³. To check if this is the case, you can review your browser settings and preferences and see if there is any difference between normal mode and incognito mode. You can also try to reset your browser settings to the default values and see if the problem persists⁴.
- You have a different network or firewall configuration in normal mode and incognito mode that alters the CSP header or meta tag of the web page. For example, some antivirus software or proxy servers may have a feature to scan or filter HTTPS traffic and modify the CSP header or meta tag of the web page in normal mode, but not in incognito mode⁵. To check if this is the case, you can disable your antivirus software or proxy server and see if the problem persists. You can also try to access the web page from a different network or device and see if the problem persists⁶.

If none of the above reasons apply to your situation, you may have encountered a bug or an anomaly in the web page or the browser. You can try to contact the web page owner or the browser support team and report the issue. You can also try to update your browser to the latest version and see if the problem persists.

Source: Conversation with Bing, 1/1/2024
(1) Fix connection errors - Google Chrome Help. https://support.google.com/chrome/answer/6098869?hl=en-incognito-mode-works.
(2) Problems accessing sites except in InPrivate mode. https://techcommunity.microsoft.com/t5/discussions/problems-accessing-sites-except-in-inprivate-mode/td-p/2702957.
(3) Fix Chrome only works in Incognito Mode on Windows 11/10 - The Windows Club. https://www.thewindowsclub.com/chrome-only-works-in-incognito-mode.
(4) Chrome shows new page only in incognito mode - Stack Overflow. https://stackoverflow.com/questions/35308542/chrome-shows-new-page-only-in-incognito-mode.
(5) Enable, Disable, or Force InPrivate Mode in Microsoft Edge Chromium. https://www.tenforums.com/tutorials/161967-enable-disable-force-inprivate-mode-microsoft-edge-chromium.html.
(6) Browse in private - Computer - Google Chrome Help. https://support.google.com/chrome/answer/95464?hl=en&co=GENIE.Platform%3DDesktop.

##  6. <a name='References'></a>References

This project was spawned from from a Microsoft Learn tutorial project: [Build your first microservice with .NET](https://learn.microsoft.com/en-us/training/modules/dotnet-microservices/). However, I carved out a small portion of the tutorial repo code into my own repo and made some modifications and improvements to the docker setups. I created this README to easily explain and demo the setup of the project.


