# TubeGrab

TubeGrab provides a native Windows interface for saving YouTube videos and playlists as MP4 video or MP3 audio. Analyze a link, select the entries you want, choose format and quality, and manage the work through a queue instead of constructing a separate command for each download.

Playlist selection helps avoid downloading unwanted entries, and the queue, history, and interrupted-job state make longer batches easier to follow. Completed output is checked with ffprobe before being recorded as successful. MP4 selection targets H.264 video and AAC audio for compatibility, subject to what the source offers.

TubeGrab is built with C++17 and Win32; yt-dlp, FFmpeg, ffprobe, and Deno provide the external media-processing tools. Use it for content you are authorized to save.

## Features

- Analyze a video or playlist and choose the entries to download.
- Select MP4 or MP3 output, quality limits, and the destination folder.
- Manage a download queue, pause/resume work, and review history.
- Verify completed output with ffprobe and retain interrupted-job state.

## Run the release

Extract `TubeGrab-2.3.0-win64.zip`, run `scripts\Setup-Tools.ps1` once to install the verified external engines, then open `TubeGrab.exe`. Visual Studio is not needed to run the release. Keep the extracted folder writable for engine updates.

## Build and run

Requirements: Windows 10 version 1703 or later / Windows 11, x64; Visual Studio 2022 with **Desktop development with C++**, MSVC v143 and a Windows 10/11 SDK.

1. Open `TubeGrab.sln`, set **Release / x64**, and build the solution. The application is `bin\x64\Release\TubeGrab.exe`. The JSON header is vendored; compilation does not need internet access or NuGet.
2. Install the external engines from PowerShell:

   ```powershell
   .\scripts\Setup-Tools.ps1
   ```

   This fetches the official current yt-dlp and Deno releases and yt-dlp's FFmpeg build, verifies their GitHub SHA-256 asset digests, and installs `yt-dlp.exe`, `ffmpeg.exe`, `ffprobe.exe`, and `deno.exe` in `tools`. It downloads executable dependencies, not media. If a release does not publish a digest, the script stops without installing it. Close TubeGrab before replacing existing tools.
3. Build again to copy the engines beside the application, or use **Tools → Tools folder…** to select the repository's `tools` directory. Keep this folder writable for updates. No administrator privileges are needed.

Command-line build and tests:

```powershell
.\scripts\Build.ps1 -Configuration Release -Test
```

The static C++ runtime is linked into the application. External tools have their own requirements. The application itself can launch without them and explains which dependencies are missing when you analyze/download.

Manual installation, including offline installations: put the four executables together in a folder and select it in the app. Sources: [yt-dlp releases](https://github.com/yt-dlp/yt-dlp/releases), [FFmpeg builds for yt-dlp](https://github.com/yt-dlp/FFmpeg-Builds/releases), and [Deno releases](https://github.com/denoland/deno/releases). Use the standalone Windows x64 yt-dlp release; it bundles the YouTube challenge solver. A recent Deno runtime is also required by [yt-dlp's current YouTube support](https://github.com/yt-dlp/yt-dlp/wiki/EJS).

## Use

1. Paste a YouTube video or playlist URL and select **Analyze**. Remove `list=` from a watch URL if you want only that video.
2. Select the entries to save, then open **Download options…**.
3. Choose MP4 or MP3, a quality limit, and an output folder.
4. Select **Download selected** and follow progress in the queue.

MP4 uses H.264 video and AAC audio; the requested resolution is an upper limit. **Best** selects the best compatible combination available. Private, removed, or restricted content may be unavailable; account/cookie authentication is not supported.

**Pause/Resume** retains partial downloads where the source supports resuming. **Cancel** stops selected jobs; **Cancel all** stops the queue. Closing preserves unfinished jobs, which require Resume after relaunch. Existing output files and partial downloads are not automatically deleted.

**Skip downloaded videos** uses history to avoid downloading the same video and format again. **Forget selected** removes a history record without deleting media. To save another quality, choose a different destination or move the existing file first.

Use **Show file** to locate output and **Tools → Open log** to inspect failures. If extraction stops working after a YouTube change, update through **Tools → Update yt-dlp** while the queue is idle. Refresh FFmpeg and Deno with the setup script.

## Local data and recovery

Settings, queue, and history are stored in `%LOCALAPPDATA%\ClearFetch\state.json`. The original folder name preserves upgrades from ClearFetch. `state.json.bak` provides a recovery copy; damaged files are preserved rather than overwritten.

If state cannot be saved, the queue pauses with recovery instructions. Logs can contain video URLs, titles, paths, and engine diagnostics; review them before sharing. Only one instance can write the profile at a time.

## Validation

Run the build and test command above for automated checks. Additional local media and recovery checks require the installed engines and Python 3:

```powershell
.\scripts\Verify-Media.ps1 -Verifier .\bin\x64\Release-2.3\TubeGrab.Tests.exe
python scripts\Verify-Recovery.py
```

These use local fixtures rather than YouTube downloads. See [VALIDATION.md](VALIDATION.md) for results and remaining checks.

## Distribution

Distribute `TubeGrab.exe` with the `tools` directory if desired. The downloader is separate from its external engines. Third-party executables are not part of the source project; their licensing and source obligations apply if you redistribute them. See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md). Use the downloader for content you are authorized to save.

`scripts\Package.ps1` refreshes `dist\TubeGrab-source.zip` with the solution, source, tests, build/setup scripts, vendored JSON header and approved design reference. Generated test profiles/media and engine binaries are excluded.

`scripts\Package-Release.ps1` creates `dist\TubeGrab-2.3.0-win64.zip`, containing the executable, dependency setup script, documentation, licenses for the vendored header, and SHA-256 manifest. External engines are installed separately with `scripts\Setup-Tools.ps1`. The release is unsigned. No installer or system-wide changes are needed.
