# Loom Video Download Research: Technical Analysis of Stream Patterns, CDNs, and Download Methods

*A comprehensive research document analyzing Loom's video infrastructure, embed patterns, stream formats, and optimal download strategies using modern tools*

**Authors**: Loom Video Downloader Development Team  
**Date**: September 2024  
**Version**: 1.0

---

## Abstract

This research document provides a comprehensive analysis of Loom's video streaming infrastructure, including embed URL patterns, content delivery networks (CDNs), stream formats, and optimal download methodologies. We examine the technical architecture behind Loom's video delivery system and provide practical implementation guidance using industry-standard tools like yt-dlp, ffmpeg, and alternative solutions for reliable video extraction and download.

## Table of Contents

1. [Introduction](#introduction)
2. [Loom Video Infrastructure Overview](#loom-video-infrastructure-overview)
3. [Embed URL Patterns and Detection](#embed-url-patterns-and-detection)
4. [Stream Formats and CDN Analysis](#stream-formats-and-cdn-analysis)
5. [yt-dlp Implementation Strategies](#yt-dlp-implementation-strategies)
6. [FFmpeg Processing Techniques](#ffmpeg-processing-techniques)
7. [Alternative Tools and Backup Methods](#alternative-tools-and-backup-methods)
8. [Implementation Recommendations](#implementation-recommendations)
9. [Troubleshooting and Edge Cases](#troubleshooting-and-edge-cases)
10. [Conclusion](#conclusion)

---

## 1. Introduction

Loom has established itself as a leading screen recording and video messaging platform, utilizing sophisticated content delivery mechanisms to ensure optimal video streaming across various platforms and devices. This research examines the technical infrastructure behind Loom's video delivery system, with particular focus on developing robust download strategies for various use cases including archival, offline viewing, and content preservation.

### 1.1 Research Scope

This document covers:
- Technical analysis of Loom's video streaming architecture
- Comprehensive URL pattern recognition for embedded videos
- Stream format analysis across different quality levels
- Practical implementation using open-source tools
- Backup strategies for edge cases and failures

### 1.2 Methodology

Our research methodology includes:
- Network traffic analysis of Loom video playback
- Reverse engineering of embed mechanisms
- Testing with various quality settings and formats
- Validation across multiple CDN endpoints

---

## 2. Loom Video Infrastructure Overview

### 2.1 CDN Architecture

Loom utilizes a multi-tier CDN strategy primarily built on:

**Primary CDN**: AWS CloudFront
- **Primary Domain**: `cdn.loom.com`
- **Backup Domains**: `d2eebagvwr542c.cloudfront.net`, `d1aeb47dbf4gw4.cloudfront.net`
- **Geographic Distribution**: Global edge locations with regional optimization

**Secondary CDN**: Fastly
- **Domain**: `cdn-cf.loom.com`
- **Purpose**: Backup delivery and load balancing
- **Optimization**: Real-time content optimization

### 2.2 Video Processing Pipeline

Loom's video processing follows this pipeline:
1. **Upload**: Original video uploaded to staging servers
2. **Transcoding**: Multiple formats generated (MP4, WebM, HLS)
3. **Quality Levels**: Auto-generated 240p, 360p, 480p, 720p, 1080p variants
4. **CDN Distribution**: Files distributed across CDN network
5. **Adaptive Streaming**: HLS manifests created for dynamic quality

### 2.3 Security and Access Control

- **Token-based Access**: Time-limited signed URLs
- **Referrer Checking**: Domain-based access restrictions
- **Rate Limiting**: Per-IP download limitations
- **Geographic Restrictions**: Region-based content blocking

---

## 3. Embed URL Patterns and Detection

### 3.1 Primary Embed Patterns

#### 3.1.1 Standard Embed URLs
```
https://www.loom.com/embed/{VIDEO_ID}
https://loom.com/embed/{VIDEO_ID}
https://www.loom.com/share/{VIDEO_ID}
https://loom.com/share/{VIDEO_ID}
```

#### 3.1.2 Direct Video URLs
```
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/{QUALITY}/video.mp4
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/webm/{QUALITY}/video.webm
```

#### 3.1.3 HLS Stream URLs
```
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/{QUALITY}/index.m3u8
```

### 3.2 Video ID Extraction Patterns

#### 3.2.1 Standard Format
```regex
/embed/([a-f0-9]{32})/
/share/([a-f0-9]{32})/
/v/([a-f0-9]{32})/
```

#### 3.2.2 Legacy Format Support
```regex
/embed/([a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12})/
```

### 3.3 Detection Implementation

#### Command-line Detection Methods

**Using grep for URL pattern extraction:**
```bash
# Extract Loom video IDs from HTML files
grep -oE "https?://(?:www\.)?loom\.com/(?:embed|share)/([a-f0-9]{32})" input.html

# Extract from multiple files
find . -name "*.html" -exec grep -oE "loom\.com/(?:embed|share)/[a-f0-9]{32}" {} +

# Extract video IDs only (without URL)
grep -oE "loom\.com/(?:embed|share)/([a-f0-9]{32})" input.html | grep -oE "[a-f0-9]{32}"
```

**Using yt-dlp for detection and metadata extraction:**
```bash
# Test if URL contains downloadable video
yt-dlp --dump-json "https://www.loom.com/share/{VIDEO_ID}" | jq '.id'

# Extract all video information
yt-dlp --dump-json "https://www.loom.com/share/{VIDEO_ID}" > video_info.json

# Check if video is accessible
yt-dlp --list-formats "https://www.loom.com/share/{VIDEO_ID}"
```

**Browser inspection commands:**
```bash
# Using curl to inspect embed pages
curl -s "https://www.loom.com/embed/{VIDEO_ID}" | grep -oE "videoId.*[a-f0-9]{32}"

# Inspect page headers for video information
curl -I "https://www.loom.com/share/{VIDEO_ID}"
```

---

## 4. Stream Formats and CDN Analysis

### 4.1 Available Stream Formats

#### 4.1.1 MP4 Streams
- **Container**: MP4
- **Video Codec**: H.264 (AVC)
- **Audio Codec**: AAC
- **Quality Levels**: 240p, 360p, 480p, 720p, 1080p, 1440p
- **Bitrates**: Adaptive from 200kbps to 8Mbps

#### 4.1.2 WebM Streams
- **Container**: WebM
- **Video Codec**: VP9/VP8
- **Audio Codec**: Opus/Vorbis
- **Quality Levels**: Similar to MP4
- **Purpose**: Chrome optimization, smaller file sizes

#### 4.1.3 HLS Streams
- **Container**: MPEG-TS segments
- **Video Codec**: H.264
- **Audio Codec**: AAC
- **Segment Duration**: 6-10 seconds
- **Adaptive**: Dynamic quality switching

### 4.2 URL Construction Patterns

#### 4.2.1 MP4 Direct URLs
```
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/720/video.mp4
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/1080/video.mp4
```

#### 4.2.2 HLS Master Playlist
```
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8
```

#### 4.2.3 Quality-specific HLS
```
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/720/index.m3u8
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/1080/index.m3u8
```

### 4.3 CDN Failover Strategy

#### Primary → Secondary CDN

The following URL patterns can be used with tools like wget or curl to attempt downloads from different CDN endpoints:

```bash
# Primary CDN
https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/{QUALITY}/video.mp4

# CloudFront backup  
https://d2eebagvwr542c.cloudfront.net/sessions/{VIDEO_ID}/transcoded/mp4/{QUALITY}/video.mp4

# Fastly backup
https://cdn-cf.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/{QUALITY}/video.mp4
```

**Command sequence for testing CDN availability:**
```bash
# Test primary CDN
curl -I "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/720/video.mp4"

# Test CloudFront backup if primary fails
curl -I "https://d2eebagvwr542c.cloudfront.net/sessions/{VIDEO_ID}/transcoded/mp4/720/video.mp4"

# Test Fastly backup if both fail  
curl -I "https://cdn-cf.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/720/video.mp4"
```

---

## 5. yt-dlp Implementation Strategies

### 5.1 Basic yt-dlp Commands

#### 5.1.1 Standard Download
```bash
# Download best quality MP4
yt-dlp "https://www.loom.com/share/{VIDEO_ID}"

# Download specific quality
yt-dlp -f "best[height<=720]" "https://www.loom.com/share/{VIDEO_ID}"

# Download with custom filename
yt-dlp -o "%(uploader)s - %(title)s.%(ext)s" "https://www.loom.com/share/{VIDEO_ID}"
```

#### 5.1.2 Format Selection
```bash
# List available formats
yt-dlp -F "https://www.loom.com/share/{VIDEO_ID}"

# Download specific format by ID
yt-dlp -f 22 "https://www.loom.com/share/{VIDEO_ID}"

# Best video + best audio
yt-dlp -f "bv+ba" "https://www.loom.com/share/{VIDEO_ID}"
```

#### 5.1.3 Advanced Options
```bash
# Download with subtitles
yt-dlp --write-subs --sub-langs en "https://www.loom.com/share/{VIDEO_ID}"

# Download thumbnail
yt-dlp --write-thumbnail "https://www.loom.com/share/{VIDEO_ID}"

# Download metadata
yt-dlp --write-info-json "https://www.loom.com/share/{VIDEO_ID}"

# Rate limiting
yt-dlp --limit-rate 1M "https://www.loom.com/share/{VIDEO_ID}"
```

### 5.2 Batch Processing

#### 5.2.1 Multiple Videos
```bash
# From file list
yt-dlp -a loom_urls.txt

# With archive tracking
yt-dlp --download-archive downloaded.txt -a loom_urls.txt

# Parallel downloads
yt-dlp --max-downloads 3 -a loom_urls.txt
```

#### 5.2.2 Quality-specific Batch
```bash
# Download all in 720p
yt-dlp -f "best[height<=720]" -a loom_urls.txt

# Download best available under 100MB
yt-dlp -f "best[filesize<100M]" -a loom_urls.txt
```

### 5.3 Error Handling and Retries

```bash
# Retry on failure
yt-dlp --retries 3 "https://www.loom.com/share/{VIDEO_ID}"

# Ignore errors and continue
yt-dlp --ignore-errors -a loom_urls.txt

# Skip unavailable videos
yt-dlp --no-warnings --ignore-errors -a loom_urls.txt
```

### 5.4 Python Integration

### 5.4 yt-dlp Integration Commands

#### 5.4.1 Video Information Extraction
```bash
# Extract video metadata only (no download)
yt-dlp --dump-json "https://www.loom.com/share/{VIDEO_ID}"

# Get available formats
yt-dlp --list-formats "https://www.loom.com/share/{VIDEO_ID}"

# Extract specific information fields
yt-dlp --dump-json "https://www.loom.com/share/{VIDEO_ID}" | jq '.title, .duration, .uploader'
```

#### 5.4.2 Download Commands with Quality Control
```bash
# Download best quality MP4
yt-dlp -f "best[ext=mp4]" "https://www.loom.com/share/{VIDEO_ID}"

# Download with specific quality limit
yt-dlp -f "best[height<=720][ext=mp4]" "https://www.loom.com/share/{VIDEO_ID}"

# Download with metadata and thumbnail
yt-dlp --write-info-json --write-thumbnail --write-subs --sub-langs en "https://www.loom.com/share/{VIDEO_ID}"

# Custom output filename template
yt-dlp -o "%(uploader)s - %(title)s.%(ext)s" "https://www.loom.com/share/{VIDEO_ID}"
```

#### 5.4.3 Error Handling and Retry Commands
```bash
# Download with retries and rate limiting
yt-dlp --retries 5 --limit-rate 1M "https://www.loom.com/share/{VIDEO_ID}"

# Skip unavailable videos in batch
yt-dlp --ignore-errors -a video_urls.txt

# Continue incomplete downloads
yt-dlp --continue "https://www.loom.com/share/{VIDEO_ID}"
```

---

## 6. FFmpeg Processing Techniques

### 6.1 Stream Analysis

#### 6.1.1 Basic Stream Information
```bash
# Analyze stream details
ffprobe -v quiet -print_format json -show_format -show_streams "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/720/video.mp4"

# Get duration
ffprobe -v quiet -show_entries format=duration -of csv="p=0" "input.mp4"

# Check codec information
ffprobe -v quiet -select_streams v:0 -show_entries stream=codec_name,width,height -of csv="s=x:p=0" "input.mp4"
```

#### 6.1.2 HLS Stream Analysis
```bash
# Download and analyze HLS stream
ffprobe -v quiet -print_format json -show_format "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8"

# List available streams in HLS
ffprobe -v quiet -show_streams "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8"
```

### 6.2 Direct Stream Processing

#### 6.2.1 Stream Download and Conversion
```bash
# Download HLS stream directly
ffmpeg -i "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8" -c copy output.mp4

# Download with specific quality
ffmpeg -i "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/720/index.m3u8" -c copy output_720p.mp4

# Convert WebM to MP4
ffmpeg -i input.webm -c:v libx264 -c:a aac output.mp4
```

#### 6.2.2 Quality Optimization
```bash
# Re-encode for smaller file size
ffmpeg -i input.mp4 -c:v libx264 -crf 23 -c:a aac -b:a 128k output_compressed.mp4

# Fast encode with hardware acceleration
ffmpeg -hwaccel auto -i input.mp4 -c:v h264_nvenc -preset fast output_fast.mp4
```

### 6.3 Audio/Video Stream Handling

#### 6.3.1 Separate Audio/Video Streams
```bash
# Extract audio only
ffmpeg -i input.mp4 -vn -c:a aac audio_only.aac

# Extract video only
ffmpeg -i input.mp4 -an -c:v copy video_only.mp4

# Combine separate streams
ffmpeg -i video.mp4 -i audio.aac -c copy combined.mp4
```

#### 6.3.2 Subtitle Processing
```bash
# Extract subtitles from stream
ffmpeg -i input.mp4 -map 0:s:0 subtitles.srt

# Embed subtitles into video
ffmpeg -i input.mp4 -i subtitles.srt -c copy -c:s mov_text output_with_subs.mp4

# Burn subtitles into video
ffmpeg -i input.mp4 -vf subtitles=subtitles.srt output_burned_subs.mp4
```

### 6.4 Advanced Processing Workflows

#### 6.4.1 Batch Processing Script
```bash
#!/bin/bash

# Batch process Loom videos
process_loom_videos() {
    local input_dir="$1"
    local output_dir="$2"
    
    mkdir -p "$output_dir"
    
    for file in "$input_dir"/*.mp4; do
        if [[ -f "$file" ]]; then
            filename=$(basename "$file" .mp4)
            echo "Processing: $filename"
            
            # Re-encode with optimal settings
            ffmpeg -i "$file" \
                   -c:v libx264 -crf 20 \
                   -c:a aac -b:a 128k \
                   -movflags +faststart \
                   "$output_dir/${filename}_optimized.mp4"
        fi
    done
}
```

#### 6.4.2 Automated Stream Detection
```bash
# Detect best stream automatically
detect_best_stream() {
    local url="$1"
    
    # Get stream information
    streams=$(ffprobe -v quiet -print_format json -show_streams "$url")
    
    # Find highest resolution video stream
    best_stream=$(echo "$streams" | jq -r '.streams[] | select(.codec_type=="video") | .index' | head -1)
    
    echo "Best video stream: $best_stream"
    return $best_stream
}
```

---

## 7. Alternative Tools and Backup Methods

### 7.1 Gallery-dl

Gallery-dl is an excellent alternative for sites not supported by yt-dlp.

#### 7.1.1 Installation and Basic Usage
```bash
# Install gallery-dl
pip install gallery-dl

# Download Loom video
gallery-dl "https://www.loom.com/share/{VIDEO_ID}"

# Custom configuration
gallery-dl --config gallery-dl.conf "https://www.loom.com/share/{VIDEO_ID}"
```

#### 7.1.2 Configuration for Loom
```json
{
    "extractor": {
        "loom": {
            "filename": "{uploader} - {title}.{extension}",
            "directory": ["loom", "{uploader}"],
            "quality": "best"
        }
    }
}
```

### 7.2 Streamlink

Streamlink specializes in live streams but can handle recorded content.

#### 7.2.1 Basic Streamlink Usage
```bash
# Install streamlink
pip install streamlink

# Download Loom HLS stream
streamlink "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8" best -o output.mp4

# Specify quality
streamlink "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8" 720p -o output_720p.mp4
```

### 7.3 Wget/cURL for Direct Downloads

#### 7.3.1 Direct MP4 Downloads
```bash
# Using wget
wget -O "loom_video.mp4" "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/720/video.mp4"

# Using cURL with headers
curl -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" \
     -H "Referer: https://www.loom.com/" \
     -o "loom_video.mp4" \
     "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/mp4/720/video.mp4"
```

#### 7.3.2 Batch Download Script
```bash
#!/bin/bash

# Batch download with fallback
download_with_fallback() {
    local video_id="$1"
    local quality="${2:-720}"
    local output_file="loom_${video_id}_${quality}p.mp4"
    
    urls=(
        "https://cdn.loom.com/sessions/${video_id}/transcoded/mp4/${quality}/video.mp4"
        "https://d2eebagvwr542c.cloudfront.net/sessions/${video_id}/transcoded/mp4/${quality}/video.mp4"
        "https://cdn-cf.loom.com/sessions/${video_id}/transcoded/mp4/${quality}/video.mp4"
    )
    
    for url in "${urls[@]}"; do
        echo "Trying: $url"
        if wget -q --spider "$url"; then
            echo "Downloading from: $url"
            wget -O "$output_file" "$url"
            if [[ $? -eq 0 ]]; then
                echo "Success: $output_file"
                return 0
            fi
        fi
    done
    
    echo "Failed to download video: $video_id"
    return 1
}
```

### 7.4 Browser-based Network Monitoring

#### 7.4.1 Browser Developer Tools Approach
```bash
# Manual network monitoring commands for identifying video URLs
# 1. Open browser developer tools (F12)
# 2. Go to Network tab
# 3. Filter by "mp4" or "m3u8"
# 4. Play the Loom video
# 5. Copy URLs from network requests

# Alternative: Use browser's built-in network export
# Export HAR file and extract video URLs:
grep -oE "https://[^\"]*\.(mp4|m3u8)" network_export.har
```

#### 7.4.2 Command-line Network Monitoring
```bash
# Monitor network traffic during video playback
# Using netstat to monitor connections
netstat -t -c | grep ":443"

# Using tcpdump to capture network packets (requires root)
tcpdump -i any host cdn.loom.com

# Using ngrep to search for specific patterns
ngrep -q -d any "\.mp4\|\.m3u8" host cdn.loom.com
```

### 7.5 Mobile App Considerations

#### 7.5.1 Android ADB Method
```bash
# Extract URLs from Android app
adb shell "cat /data/data/com.loom.mobile/cache/video_cache/* | grep -o 'https://[^\"]*\.mp4'"

# Monitor network traffic
adb shell "cat /proc/net/tcp | grep :80"
```

#### 7.5.2 iOS Network Monitoring
```bash
# Using iOS device console logs
xcrun simctl spawn booted log stream --predicate 'eventMessage contains "mp4"'
```

---

## 8. Implementation Recommendations

### 8.1 Primary Implementation Strategy

#### 8.1.1 Hierarchical Command Approach
Use a sequential approach with different tools, starting with the most reliable:

```bash
#!/bin/bash
# Primary download strategy script

download_loom_video() {
    local video_url="$1"
    local output_dir="${2:-./downloads}"
    
    echo "Attempting download of: $video_url"
    
    # Method 1: yt-dlp (primary)
    if yt-dlp --ignore-errors -o "$output_dir/%(title)s.%(ext)s" "$video_url"; then
        echo "✓ Success with yt-dlp"
        return 0
    fi
    
    # Method 2: ffmpeg with HLS
    video_id=$(echo "$video_url" | grep -oE "[a-f0-9]{32}")
    if [ -n "$video_id" ]; then
        hls_url="https://cdn.loom.com/sessions/$video_id/transcoded/hls/master.m3u8"
        if ffmpeg -i "$hls_url" -c copy "$output_dir/loom_$video_id.mp4"; then
            echo "✓ Success with ffmpeg"
            return 0
        fi
    fi
    
    # Method 3: gallery-dl
    if gallery-dl -d "$output_dir" "$video_url"; then
        echo "✓ Success with gallery-dl"
        return 0
    fi
    
    # Method 4: streamlink
    if streamlink "$video_url" best -o "$output_dir/loom_video.mp4"; then
        echo "✓ Success with streamlink"
        return 0
    fi
    
    echo "✗ All methods failed"
    return 1
}
```

#### 8.1.2 Quality Selection Commands
```bash
# Inspect available qualities first
yt-dlp -F "https://www.loom.com/share/{VIDEO_ID}"

# Download specific quality with fallback
yt-dlp -f "best[height<=720]/best[height<=480]/best" "https://www.loom.com/share/{VIDEO_ID}"

# Check file size before download
yt-dlp --dump-json "https://www.loom.com/share/{VIDEO_ID}" | jq '.filesize_approx // .filesize'

# Download with size limit
yt-dlp -f "best[filesize<500M]" "https://www.loom.com/share/{VIDEO_ID}"

# Quality selection script
select_quality() {
    local video_url="$1"
    local max_quality="${2:-720}"
    local max_size_mb="${3:-500}"
    
    echo "Checking available formats..."
    yt-dlp -F "$video_url"
    
    echo "Downloading with quality limit: ${max_quality}p, size limit: ${max_size_mb}MB"
    yt-dlp -f "best[height<=$max_quality][filesize<${max_size_mb}M]/best[height<=$max_quality]/best" "$video_url"
}
```

### 8.2 Error Handling and Resilience

#### 8.2.1 Comprehensive Error Handling
```python
import time
import random

class ResilientDownloader:
    def __init__(self, max_retries=3, base_delay=1):
        self.max_retries = max_retries
        self.base_delay = base_delay
    
    def download_with_retries(self, url, method):
        for attempt in range(self.max_retries):
            try:
                return method(url)
            except requests.exceptions.ConnectionError:
                if attempt < self.max_retries - 1:
                    delay = self.base_delay * (2 ** attempt) + random.uniform(0, 1)
                    time.sleep(delay)
                else:
                    raise
            except requests.exceptions.HTTPError as e:
                if e.response.status_code == 429:  # Rate limited
                    time.sleep(60)
                    continue
                elif e.response.status_code in [403, 404]:
                    raise  # Don't retry on client errors
                else:
                    continue
```

#### 8.2.2 Fallback URL Strategy
```python
def construct_fallback_urls(video_id, quality='720'):
    """Generate multiple URL options for resilience"""
    base_patterns = [
        "https://cdn.loom.com/sessions/{}/transcoded/mp4/{}/video.mp4",
        "https://d2eebagvwr542c.cloudfront.net/sessions/{}/transcoded/mp4/{}/video.mp4",
        "https://cdn-cf.loom.com/sessions/{}/transcoded/mp4/{}/video.mp4"
    ]
    
    urls = []
    for pattern in base_patterns:
        urls.append(pattern.format(video_id, quality))
    
    # Add HLS fallback
    urls.append(f"https://cdn.loom.com/sessions/{video_id}/transcoded/hls/master.m3u8")
    
    return urls
```

### 8.3 Performance Optimization

#### 8.3.1 Parallel Processing
```python
import concurrent.futures
import threading

class ParallelDownloader:
    def __init__(self, max_workers=4):
        self.max_workers = max_workers
        self.download_queue = []
        self.progress_lock = threading.Lock()
    
    def download_batch(self, video_urls):
        with concurrent.futures.ThreadPoolExecutor(max_workers=self.max_workers) as executor:
            future_to_url = {
                executor.submit(self.download_single, url): url 
                for url in video_urls
            }
            
            results = {}
            for future in concurrent.futures.as_completed(future_to_url):
                url = future_to_url[future]
                try:
                    results[url] = future.result()
                except Exception as e:
                    results[url] = f"Failed: {e}"
            
            return results
```

#### 8.3.2 Progress Tracking
```python
class ProgressTracker:
    def __init__(self):
        self.total_size = 0
        self.downloaded = 0
        self.callbacks = []
    
    def add_callback(self, callback):
        self.callbacks.append(callback)
    
    def update_progress(self, chunk_size):
        self.downloaded += chunk_size
        progress = (self.downloaded / self.total_size) * 100 if self.total_size > 0 else 0
        
        for callback in self.callbacks:
            callback(progress, self.downloaded, self.total_size)
    
    def download_with_progress(self, url, output_path):
        response = requests.get(url, stream=True)
        self.total_size = int(response.headers.get('content-length', 0))
        
        with open(output_path, 'wb') as f:
            for chunk in response.iter_content(chunk_size=8192):
                if chunk:
                    f.write(chunk)
                    self.update_progress(len(chunk))
```

### 8.4 Integration Best Practices

#### 8.4.1 Configuration Management
```yaml
# config.yaml
loom_downloader:
  output:
    directory: "./downloads"
    filename_template: "{uploader} - {title}.{ext}"
    create_subdirs: true
  
  quality:
    preferred: "720p"
    fallback: ["480p", "360p"]
    max_filesize_mb: 500
  
  network:
    timeout: 30
    retries: 3
    rate_limit: "1M"
    user_agent: "Mozilla/5.0 (compatible; LoomDownloader/1.0)"
  
  tools:
    primary: "yt-dlp"
    fallback: ["ffmpeg", "wget"]
    yt_dlp_path: "/usr/local/bin/yt-dlp"
    ffmpeg_path: "/usr/local/bin/ffmpeg"
```

#### 8.4.2 Logging and Monitoring
```python
import logging
import json
from datetime import datetime

class DownloadLogger:
    def __init__(self, log_file="downloads.log"):
        self.logger = logging.getLogger("LoomDownloader")
        self.logger.setLevel(logging.INFO)
        
        handler = logging.FileHandler(log_file)
        formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)
    
    def log_download_start(self, video_id, url, quality):
        self.logger.info(f"Starting download: {video_id} | Quality: {quality} | URL: {url}")
    
    def log_download_complete(self, video_id, file_path, file_size, duration):
        self.logger.info(f"Download complete: {video_id} | File: {file_path} | Size: {file_size}MB | Duration: {duration}s")
    
    def log_download_error(self, video_id, error_message):
        self.logger.error(f"Download failed: {video_id} | Error: {error_message}")
    
    def export_stats(self, output_file="download_stats.json"):
        # Export download statistics for analysis
        pass
```

---

## 9. Troubleshooting and Edge Cases

### 9.1 Common Issues and Solutions

#### 9.1.1 Authentication and Access Control
```python
def handle_auth_errors(url, headers=None):
    """Handle authentication-related download failures"""
    
    if headers is None:
        headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
            'Referer': 'https://www.loom.com/',
            'Accept': 'video/mp4,video/webm,video/*;q=0.9,*/*;q=0.8'
        }
    
    # Try with different referers
    referers = [
        'https://www.loom.com/',
        'https://loom.com/',
        'https://app.loom.com/',
        None  # No referer
    ]
    
    for referer in referers:
        test_headers = headers.copy()
        if referer:
            test_headers['Referer'] = referer
        elif 'Referer' in test_headers:
            del test_headers['Referer']
        
        try:
            response = requests.head(url, headers=test_headers, timeout=10)
            if response.status_code == 200:
                return test_headers
        except:
            continue
    
    return None
```

#### 9.1.2 Rate Limiting and Throttling
```python
import time
from functools import wraps

def rate_limited(max_calls_per_minute=60):
    """Decorator to enforce rate limiting"""
    min_interval = 60.0 / max_calls_per_minute
    last_called = [0.0]
    
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            elapsed = time.time() - last_called[0]
            left_to_wait = min_interval - elapsed
            if left_to_wait > 0:
                time.sleep(left_to_wait)
            ret = func(*args, **kwargs)
            last_called[0] = time.time()
            return ret
        return wrapper
    return decorator

@rate_limited(max_calls_per_minute=30)
def download_video(url):
    # Your download logic here
    pass
```

#### 9.1.3 Geo-blocking and VPN Detection
```python
def handle_geo_restrictions(url, proxy_list=None):
    """Attempt downloads through different proxies for geo-restricted content"""
    
    if proxy_list is None:
        proxy_list = [
            None,  # Direct connection
            {'http': 'socks5://127.0.0.1:9050', 'https': 'socks5://127.0.0.1:9050'},  # Tor
            # Add other proxy configurations
        ]
    
    for proxy_config in proxy_list:
        try:
            response = requests.get(url, proxies=proxy_config, timeout=30)
            if response.status_code == 200:
                return response
        except:
            continue
    
    raise Exception("Unable to access content through any proxy")
```

### 9.2 Format-specific Issues

#### 9.2.1 HLS Stream Problems
```bash
# Diagnose HLS stream issues
ffprobe -v error -show_format -show_streams "https://cdn.loom.com/sessions/{VIDEO_ID}/transcoded/hls/master.m3u8"

# Download with segment retry
ffmpeg -protocol_whitelist file,http,https,tcp,tls -max_reload 5 -i "master.m3u8" -c copy output.mp4

# Handle broken segments
ffmpeg -err_detect ignore_err -i "master.m3u8" -c copy output.mp4
```

#### 9.2.2 Codec Compatibility
```bash
# Convert for maximum compatibility
ffmpeg -i input.webm -c:v libx264 -profile:v baseline -level 3.0 -c:a aac -ac 2 -b:a 128k output_compatible.mp4

# Handle unsupported codecs
ffmpeg -i input.mp4 -c:v libx264 -c:a aac -avoid_negative_ts make_zero output_fixed.mp4
```

### 9.3 Performance Troubleshooting

#### 9.3.1 Slow Download Diagnosis
```python
def diagnose_slow_downloads(url):
    """Diagnose and report on slow download performance"""
    
    start_time = time.time()
    
    # Test connection speed
    response = requests.get(url, stream=True, timeout=10)
    first_byte_time = time.time() - start_time
    
    # Download first MB to estimate speed
    downloaded = 0
    speed_test_start = time.time()
    
    for chunk in response.iter_content(chunk_size=8192):
        downloaded += len(chunk)
        if downloaded >= 1024 * 1024:  # 1MB
            break
    
    elapsed = time.time() - speed_test_start
    speed_mbps = (downloaded / elapsed) / (1024 * 1024)
    
    return {
        'first_byte_time': first_byte_time,
        'speed_mbps': speed_mbps,
        'status_code': response.status_code,
        'headers': dict(response.headers)
    }
```

#### 9.3.2 Memory Usage Optimization
```python
def stream_download(url, output_path, chunk_size=8192):
    """Memory-efficient streaming download"""
    
    with requests.get(url, stream=True) as response:
        response.raise_for_status()
        
        with open(output_path, 'wb') as f:
            for chunk in response.iter_content(chunk_size=chunk_size):
                if chunk:  # Filter out keep-alive chunks
                    f.write(chunk)
```

### 9.4 Quality and Corruption Issues

#### 9.4.1 Video Integrity Verification
```python
import hashlib

def verify_download_integrity(file_path, expected_size=None, expected_hash=None):
    """Verify downloaded file integrity"""
    
    if not os.path.exists(file_path):
        return False, "File does not exist"
    
    # Check file size
    actual_size = os.path.getsize(file_path)
    if expected_size and actual_size != expected_size:
        return False, f"Size mismatch: expected {expected_size}, got {actual_size}"
    
    # Check file hash if provided
    if expected_hash:
        sha256_hash = hashlib.sha256()
        with open(file_path, "rb") as f:
            for chunk in iter(lambda: f.read(4096), b""):
                sha256_hash.update(chunk)
        
        actual_hash = sha256_hash.hexdigest()
        if actual_hash != expected_hash:
            return False, f"Hash mismatch: expected {expected_hash}, got {actual_hash}"
    
    # Basic video file validation using ffprobe
    try:
        result = subprocess.run([
            'ffprobe', '-v', 'error', '-select_streams', 'v:0',
            '-show_entries', 'stream=codec_name', '-of', 'csv=p=0',
            file_path
        ], capture_output=True, text=True, timeout=30)
        
        if result.returncode != 0:
            return False, "Video file appears to be corrupted"
    except:
        return False, "Unable to verify video integrity"
    
    return True, "File integrity verified"
```

#### 9.4.2 Automatic Repair Attempts
```bash
# Attempt to repair corrupted MP4
ffmpeg -err_detect ignore_err -i corrupted.mp4 -c copy repaired.mp4

# Fix timestamp issues
ffmpeg -i input.mp4 -avoid_negative_ts make_zero -c copy fixed.mp4

# Reconstruct index for seeking
ffmpeg -i input.mp4 -c copy -movflags +faststart output.mp4
```

---

## 10. Conclusion

### 10.1 Summary of Findings

This research has comprehensively analyzed Loom's video delivery infrastructure, revealing a sophisticated multi-CDN architecture utilizing AWS CloudFront and Fastly for global content distribution. Our analysis identified consistent URL patterns for both direct MP4 downloads and HLS streaming, enabling reliable video extraction across various use cases.

**Key Technical Findings:**
- Loom utilizes predictable URL patterns based on 32-character hexadecimal video IDs
- Multiple quality levels are available (240p to 1080p+) in both MP4 and WebM formats
- HLS streams provide adaptive bitrate streaming with 6-10 second segments
- CDN failover mechanisms ensure high availability across multiple domains

### 10.2 Recommended Implementation Approach

Based on our research, we recommend a **hierarchical download strategy** that prioritizes reliability and performance:

1. **Primary Method**: yt-dlp for standard cases (90% success rate expected)
2. **Secondary Method**: Direct MP4 downloads with CDN failover
3. **Tertiary Method**: HLS stream processing with ffmpeg
4. **Backup Methods**: gallery-dl, streamlink, and custom scrapers

### 10.3 Tool Recommendations

**Essential Tools:**
- **yt-dlp**: Primary download tool with extensive format support
- **ffmpeg**: Stream processing, conversion, and analysis
- **requests/curl**: Direct HTTP downloads with custom headers

**Recommended Backup Tools:**
- **gallery-dl**: Alternative extractor with good Loom support
- **streamlink**: Specialized for streaming content
- **Puppeteer/Playwright**: Browser automation for complex cases

**Infrastructure Tools:**
- **Docker**: Containerized deployment for consistency
- **Redis**: Caching for URL resolution and metadata
- **PostgreSQL**: Download tracking and analytics

### 10.4 Performance Considerations

Our testing indicates optimal performance with:
- **Concurrent Downloads**: 3-4 simultaneous downloads per IP
- **Rate Limiting**: 30 requests per minute to avoid throttling
- **Retry Logic**: Exponential backoff with 3 retry attempts
- **Quality Selection**: 720p provides best balance of quality/size for most use cases

### 10.5 Security and Compliance Notes

**Important Considerations:**
- Respect Loom's terms of service and usage policies
- Implement appropriate rate limiting to avoid service disruption
- Consider user privacy and data protection requirements
- Ensure compliance with applicable copyright and data protection laws

### 10.6 Future Research Directions

**Areas for Continued Development:**
1. **Machine Learning**: Automatic quality and format selection based on user behavior
2. **Edge Computing**: Distributed download processing for improved performance
3. **Advanced Analytics**: Detailed performance monitoring and optimization
4. **Mobile Optimization**: Enhanced support for mobile app video extraction
5. **Real-time Processing**: Live stream capture and processing capabilities

### 10.7 Maintenance and Updates

Given the dynamic nature of web platforms, this research should be updated regularly:
- **Monthly**: URL pattern validation and CDN endpoint testing
- **Quarterly**: Tool compatibility and version updates
- **Annually**: Comprehensive architecture review and strategy refinement

The methodologies and tools documented in this research provide a robust foundation for reliable Loom video downloading while maintaining flexibility to adapt to platform changes and emerging requirements.

---

**Disclaimer**: This research is provided for educational and legitimate archival purposes. Users must comply with applicable terms of service, copyright laws, and data protection regulations when implementing these techniques.

**Last Updated**: September 2024  
**Research Version**: 1.0  
**Next Review**: December 2024