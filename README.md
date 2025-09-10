---
title: Loom Video Downloader | How to Download Loom Videos Even if You Have a Free Account

---


# Loom Video Downloader App (Browser Extension for Chrome & Firefox)

Easily download videos hosted by loom.com on any website in just a click for conenient offline viewing with this Loom video downloader browser extension.

- Download loom videos from your account where the DL button was removed
- Download loom videos embedded on other web pages

![loom video downloader](https://raw.githubusercontent.com/serpapps/loom-video-downloader/refs/heads/assets/images/loom-video-downloader.gif)

> **💡 Looking for your original Copilot request?** If you're trying to find the initial request you made to GitHub Copilot that created a PR, check out our [FAQ section](FAQ.md#q-where-do-i-find-the-initial-full-request-that-i-made-to-copilot-with-the-instructions-for-this-pr) for detailed guidance on where to locate your original instructions.

## 🔗 Links

- 🎁 Get it [here](https://serp.ly/loom-video-downloader)
- ❓ Check FAQs [here](FAQ.md) | [Community FAQs](https://github.com/orgs/serpapps/discussions/categories/faq)
- 🐛 Report bugs [here](https://github.com/serpapps/loom-video-downloader/issues)
- 🆕 Request features [here](https://github.com/serpapps/loom-downloader/issues)

### Resources

- 💬 [Community](https://serp.ly/@serp/community)
- 💌 [Newsletter](https://serp.ly/@serp/email)
- 🛒 [Shop](https://serp.ly/@serp/store)
- 🎓 [Courses](https://serp.ly/@serp/courses)
- 📚 [Technical Research Document](LOOM_RESEARCH.md) - Comprehensive analysis of Loom's streaming infrastructure and download methods

## Table of Contents
- [Features](#features)
- [Screenshots](#screenshots)
- [Videos](#videos)
- [Installation Instructions](#installation-instructions)
  - [How to install \& setup (MAC)](#how-to-install--setup-mac)
  - [How to use (MAC)](#how-to-use-mac)
- [How to install \& setup (Windows)](#how-to-install--setup-windows)
- [How to use (Windows)](#how-to-use-windows)
- [FAQ](FAQ.md) - Frequently Asked Questions
- [Technical Documentation](CONTRIBUTING.md) - Advanced usage and troubleshooting

## Features

- One-click download from any video page
- 100% privacy-friendly – no tracking or data collection
- Auto-detect videos on the page
- Smart Page Scan
- Embedded Video Support
- Full HD Downloads
- Lightning fast downloads (no re-encoding)
- Original quality preserved (up to 4K)
- No registration or personal data required
- No watermarks or branding added
- Zero Ads
- Regular Updates
- Thumbnail Preview
- Minimal Permissions
- Download Progress Bar
  
---

<!-- ## Screenshots -->

## Videos

<a href="https://www.youtube.com/watch?v=bbkhxMpIH4w" target="_blank">
  <img src="https://raw.githubusercontent.com/devinschumacher/uploads/refs/heads/main/images/loom-video-downloader-installation-and-use-instructions-how-to-download-loom-videos.jpg" width="700px" />
</a>

<br><br>

<a href="https://www.youtube.com/watch?v=EY8549tG34c" target="_blank">
<img src="https://raw.githubusercontent.com/devinschumacher/uploads/refs/heads/main/images/how-to-download-loom-videos-for-free-without-a-loom-subscription-mp4-video-embed-example.jpg" width="700px">
</a>

<br><br>

<a href="https://www.youtube.com/watch?v=4MnyU0kPxlE" target="_blank">
<img src="https://raw.githubusercontent.com/devinschumacher/uploads/refs/heads/main/images/how-to-download-loom-videos-for-free-dl-without-subscription-10-examples-mp4-hls-m3u8.jpg" width="700px">
</a>

<br><br>
00:00 Introduction and Loom Free Plan Limitations  
01:00 Why You Can't Download After Downgrade  
01:28 Tool You Need: YT-DLP Setup  
02:35 How to Find Loom Video URLs  
03:47 Checking Available File Formats  
05:12 How to Download with YT-DLP  
07:20 Handling WebM and Forcing MP4  
09:18 Downloading from Embedded Loom Videos  
11:20 Stitching Audio and Video Streams  
12:40 Choosing Resolution and Subtitles  
14:30 Exporting VTT Subtitles for AI Use  
15:01 Wrap Up and Final Tips  




---

## Installation Instructions

### How to install

1. "Star ⭐" this repository <a href="https://public-files.gumroad.com/fgqglcvq4v0u32yc0x0jvsllk4x6" target="_blank">click the button that looks like this</a>
2. Download the latest version (`.zip`) from the [Releases](https://github.com/serpapps/loom-video-downloader/releases) area
3. Double click the `.zip` file on your computer to unzip it
4. Open Chrome and go to `chrome://extensions/`
5. Enable "developer mode" by clicking the toggle switch on the top right
6. Install the 'extension' by clicking "Load unpacked" and choosing the 'extension folder' on your computer (the FOLDER, not the .zip)
7. Pin the extension to chrome by clicking the puzzle looking icon thing and then the 'pin' icon
8. When you click on the extension for the first time, you will need to enter your `email` & `license key` associated with the extension

> Note: You can find your license key in your email confirmation from purchasing the product

### How to use

1. Navigate to a page where there is a Loom video & click on the extension to see the video details populate
2. Click the "Download" button

> Note: If the video auto-discovery isn't working, try clicking PLAY on the loom video (sometimes that helps)


<br><br><br>

<details>
  <summary>
## Permissions Justifications
</summary>
### Single purpose description  
This extension allows users to download Loom videos directly from the Loom website to their local computer with a single click, making it easy to save and access videos offline.


### downloads justification  
The "downloads" permission is required to save Loom videos from the web directly to the user's computer. Without this, the extension would not be able to transfer video files to the user’s local storage.

### activeTab justification  
The "activeTab" permission is necessary to interact with the Loom website that the user is currently viewing. It enables the extension to detect and download videos only when the user activates the extension on an appropriate tab.

### storage justification  
The "storage" permission is used to save user preferences and extension settings locally. This ensures a smooth and personalized user experience each time the extension is used.

### notifications justification  
The "notifications" permission is used to inform users when a video download has started, completed, or if there is an error during the process. This keeps users updated about the status of their downloads.

### contextMenus justification  
The "contextMenus" permission allows the extension to add options to the right-click menu, making it more convenient for users to download Loom videos directly from the context menu without having to use the main extension popup.

### clipboardRead justification  
The "clipboardRead" permission may be used to allow users to quickly paste Loom video URLs from their clipboard into the extension for downloading, streamlining the user workflow.

### tabs justification  
The "tabs" permission is required to access information about the user's open tabs, such as the current URL, to ensure the extension only operates on Loom video pages and manages downloads efficiently.

### scripting justification  
The "scripting" permission allows the extension to execute scripts on Loom pages to detect video elements and facilitate the download functionality.

### offscreen justification  
The "offscreen" permission is used to process video files in the background, ensuring that downloads can be completed smoothly without interrupting the user’s browsing experience.

### cookies justification  
The "cookies" permission may be required to access authentication cookies for Loom, ensuring the extension can download videos that may require the user to be logged in.

### webNavigation justification  
The "webNavigation" permission helps the extension monitor navigation to Loom video pages, enabling it to offer download functionality only when appropriate.

### Host permission justification
Host permissions are requested for loom.com and its subdomains to enable the extension to detect and download Loom videos directly from the Loom website. No other hosts are accessed


### Remote code justification  
No, I am not using Remote code. All code is packaged within the extension and does not execute any external scripts or resources.




</details>
