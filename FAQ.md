# Frequently Asked Questions (FAQ)

## GitHub Copilot Integration

### Q: Where do I find the initial full request that I made to Copilot with the instructions for this PR?

**A:** Finding your original Copilot request depends on how you initiated the interaction. Here are the most common locations to check:

#### 1. **GitHub Issues Tab**
- Go to the [Issues tab](https://github.com/serpapps/loom-video-downloader/issues) of this repository
- Look for issues you created that might contain your original request
- Check both open and closed issues

#### 2. **Pull Request Description and Comments**
- The original request is often preserved in the PR description under "Original description:"
- Check the PR comments for any preserved context from your initial request
- Look at the first commit message which might contain relevant information

#### 3. **GitHub Notifications**
- Check your GitHub notifications (bell icon in the top right)
- Email notifications from GitHub often contain the original context
- GitHub sends emails when Copilot creates PRs on your behalf

#### 4. **Browser History**
- If you made the request through GitHub's web interface, check your browser history
- Look for GitHub URLs from around the time you made the request

#### 5. **GitHub Activity Feed**
- Check your GitHub activity feed on your profile page
- Look for recent activity related to this repository

#### 6. **GitHub Copilot Interface**
- If you used GitHub Copilot Chat or another Copilot interface, check that interface's history
- Some Copilot integrations maintain conversation history

#### 7. **Repository Discussions**
- Check the [Discussions tab](https://github.com/orgs/serpapps/discussions) if enabled
- Your request might be in a discussion thread

### Pro Tips for Future Copilot Requests:
- **Save important requests**: Copy your detailed requests to a text file or note-taking app
- **Use clear issue titles**: Create GitHub issues with descriptive titles for complex requests
- **Reference PRs in issues**: Link PRs back to the original issue for better tracking
- **Tag your requests**: Use consistent tags or labels to make requests easier to find

---

## Common Usage Questions

### Q: How do I download a Loom video?

**A:** Follow these steps:
1. Install the browser extension from the [Chrome Web Store](https://serp.ly/loom-video-downloader)
2. Navigate to any page with a Loom video
3. Click the extension icon in your browser toolbar
4. Click the "Download" button when the video details appear

### Q: The extension isn't detecting videos. What should I do?

**A:** Try these troubleshooting steps:
1. Make sure you're on a page that actually contains a Loom video
2. Try clicking the PLAY button on the Loom video first
3. Refresh the page and try again
4. Check that the extension has the necessary permissions enabled

### Q: What video formats are supported?

**A:** The extension supports:
- MP4 files (recommended)
- WebM files
- HLS streams (.m3u8)
- Various quality levels (240p to 4K depending on source)

### Q: Is this extension free to use?

**A:** Yes, this extension is completely free to use with no hidden costs, subscriptions, or premium features.

### Q: Does this work with private/restricted Loom videos?

**A:** The extension respects Loom's access controls. You can only download videos that you have permission to view. Make sure you're logged into Loom if required.

---

## Technical Questions

### Q: Where can I find technical documentation?

**A:** Check the [CONTRIBUTING.md](CONTRIBUTING.md) file which contains comprehensive technical analysis of Loom's streaming infrastructure and download methods.

### Q: How do I report bugs or request features?

**A:** 
- 🐛 **Report bugs**: [Create an issue here](https://github.com/serpapps/loom-video-downloader/issues)
- 🆕 **Request features**: [Submit a feature request here](https://github.com/serpapps/loom-downloader/issues)

### Q: What permissions does the extension need and why?

**A:** The extension requires minimal permissions for core functionality:
- **activeTab**: To interact with the current Loom page
- **downloads**: To save videos to your computer  
- **storage**: To remember your preferences
- **notifications**: To update you on download progress

For a complete list with detailed justifications, see the [Installation Instructions](README.md#installation-instructions) section.

---

## Getting Help

If you can't find the answer to your question here:

1. **Search existing issues**: Check if someone else has asked the same question
2. **Community support**: Join our [community discussions](https://serp.ly/@serp/community)
3. **Create a new issue**: If your question isn't covered, [open a new issue](https://github.com/serpapps/loom-video-downloader/issues/new)

---

*Last updated: September 2024*