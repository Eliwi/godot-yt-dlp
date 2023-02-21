![logo_light](https://user-images.githubusercontent.com/30960698/176983082-18bf15ee-3144-4a54-bab9-bbb9650e63a3.png#gh-light-mode-only)
![logo_dark](https://user-images.githubusercontent.com/30960698/176983087-022d7ccd-d94c-43da-a8ff-f8f5736d9c3b.png#gh-dark-mode-only)

An implementation of [yt-dlp](https://github.com/yt-dlp/yt-dlp) for the **Godot engine** that works on Linux, OSX and Windows.\
This project provides a simple API for downloading videos from YouTube (and other websites).

## Features
 - [x] Automatic [yt-dlp](https://github.com/yt-dlp/yt-dlp) and [ffmpeg](https://www.ffmpeg.org/) setup (the latter only on Windows).
 - [x] Downloading single videos from YouTube.
 - [x] Converting videos to audio.
 - [ ] Tracking download progress. *(yet to be implemented)*
 - [ ] Downloading playlists of videos from YouTube. *(yet to be implemented)*
 - [ ] Searching YouTube videos. *(yet to be implemented)*
 
## Installation

Clone the repository or [download a release](https://github.com/Nolkaloid/godot-yt-dlp/releases/latest/download/godot-yt-dlp.zip) and place the `yt-dlp` folder somewhere in your project.

> If you're using Linux or exporting to Linux make sure that **ffmpeg** is installed on the system  
> Same goes for OSX (undocumented)

## How to use

### Setup

Create a new `YtDlp` object like this:

```gdscript
var yt_dlp = YtDlp.new()
```

Usually you'll want to connect its signals immediately like this:

```gdscript
yt_dlp.connect("ready", self, "some_function")
yt_dlp.connect("download_completed", self, "some_other_function")
```

 - The `ready` signal is emitted when YtDlp has finished the initial setup and is ready to download videos. 
 - The `download_completed` signal is emitted when YtDlp has finished downloading a video/audio.

> You could also use [`await`](https://docs.godotengine.org/en/latest/tutorials/scripting/gdscript/gdscript_basics.html#awaiting-for-signals) to await the signals

### Usage

To download a YouTube video use the `download` function:

```gdscript
yt_dlp.download(
  url: String,
  destination: String,
  file_name: String,
  convert_to_audio: bool = false,
  video_format: int = Video.WEBM,
  audio_format: int = Audio.VORBIS
)
```
 - `String` **url:** The video url 
 - `String` **destination:** The folder where you want the video to be downloaded
 - `String` **file_name:** Specify the filename without extension, can be leaved blank
 - `bool` **convert_to_audio:** If true the video will be converted to audio
 - `int`  **video_format:** Used to specify the video format to download, use the built-in enum `YtDlp.Video`.
 - `int` **audio_format:** Used to specify the audio format for conversion, use the built-in enum `YtDlp.Audio`.
 
 #### Supported audio formats:
 - `MP3`
 - `FLAC`
 - `AAC`
 - `VORBIS` *(default)*
 - `OPUS`
 - `M4A`
 - `WAV`
 
 #### About video:
 Currently there are two video formats available: 
 - `WEBM`
 - `MP4`

However, playback is NOT natively supported by Godot 4. You have to write your own GDExtension to play these files.
 
## Examples:

#### Downloading a video, converting it to audio and playing it using a `AudioStreamPlayer` (Fully supported)

```gdscript
var yt_dlp := YtDlp.new()
await yt_dlp.ready()

yt_dlp.download("https://youtu.be/PSPbY00UZ9w",
		OS.get_user_data_dir(), "audio_clip", true, 1, YtDlp.Audio.MP3)

await yt_dlp.download_completed()

var mp3_file := FileAccess.open("user://audio_clip.mp3", FileAccess.READ)

var stream := AudioStreamMp3.new()
stream.data = mp3_file.get_buffer(mp3_file.get_length())

mp3_file.flush()

$AudioStreamPlayer.stream = stream
$AudioStreamPlayer.play()
```
 
 #### Downloading a video and playing it in using a `VideoPlayer` (Currently not supported without own extensions)
```gdscript
var yt_dlp := YtDlp.new()
await yt_dlp.ready

yt_dlp.download("https://youtu.be/dQw4w9WgXcQ",
		OS.get_user_data_dir(), "video_clip")

await yt_dlp.download_completed

var stream := VideoStream.new()
stream.set_file("user://video_clip.webm")

$VideoPlayer.stream = stream
$VideoPlayer.play()
```


## Social
Original Creator:
- https://twitter.com/NoeGameDev
- https://www.youtube.com/c/Nolkaloid
