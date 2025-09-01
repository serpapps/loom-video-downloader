# Product Data JSON Files

This directory contains JSON objects for three SerpApps repositories, formatted according to a standardized product information schema.

## Files

- `loom-video-downloader.json` - Product data for the Loom Video Downloader browser extension
- `skool-downloader.json` - Product data for the Skool Video Downloader browser extension  
- `vimeo-video-downloader.json` - Product data for the Vimeo Video Downloader browser extension

## Schema

Each JSON file follows a consistent structure with the following key fields:

- **id**: Unique identifier for the product
- **name**: Product name
- **tagline**: Short descriptive tagline
- **description**: Detailed product description
- **features**: Array of key features
- **github_repo_url**: GitHub repository URL
- **purchase_url**: Product purchase URL
- **installation_instructions**: How to install the product
- **usage_instructions**: How to use the product
- **supported_operating_systems**: Compatible operating systems
- **technologies**: Technologies used in the product
- **categories**: Product categories
- **keywords**: SEO keywords

## Data Source

The information in these JSON files was extracted from the README.md files of each respective repository:

- [Loom Video Downloader](https://github.com/serpapps/loom-video-downloader)
- [Skool Downloader](https://github.com/serpapps/skool-downloader)
- [Vimeo Video Downloader](https://github.com/serpapps/vimeo-video-downloader)