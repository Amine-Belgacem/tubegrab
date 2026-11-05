# TubeGrab

A native Windows application for downloading YouTube videos and playlists as MP4 video or MP3 audio. Built with C++17 and Win32, using yt-dlp and FFmpeg for media processing.

## Features

- Analyze a video or playlist and choose the entries to download.
- Select MP4 or MP3 output, quality limits, and the destination folder.
- Manage a download queue, pause/resume work, and review history.
- Verify completed output with ffprobe and retain interrupted-job state.

## Build and run

Requires Windows 10 version 1703 or later / Windows 11 x64 and Visual Studio 2022 with **Desktop development with C++**, MSVC v143, and a Windows SDK.

1. Open [TubeGrab.sln](TubeGrab.sln) and build **Release / x64**.
2. Install the external engines:

   ```powershell
   .\scripts\Setup-Tools.ps1
   ```

3. Build again to copy the engines beside `bin\x64\Release\TubeGrab.exe`, then open it. Alternatively, select their folder through **Tools > Tools folder…**.

The setup script downloads yt-dlp, FFmpeg, ffprobe, and Deno and verifies published asset digests. Close TubeGrab before replacing tools, and keep their folder writable for updates. See the [detailed reference](docs/reference.md) for offline setup and portable packaging.

## Use

Paste a YouTube HTTPS link and choose **Analyze**. Select entries, open **Download options…** to choose format and destination, then start the download.

MP4 output uses H.264 video and AAC audio; quality depends on compatible streams available from the source. Settings, queue, and history use the retained `%LOCALAPPDATA%\ClearFetch\state.json` profile. Use the downloader for content you are authorized to save.

[Detailed reference](docs/reference.md) · [Validation](VALIDATION.md) · [Third-party notices](THIRD_PARTY_NOTICES.md)
