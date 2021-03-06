---
layout: page
title: "FFmpeg Camera"
description: "Instructions how to integrate a Video fees with FFmpeg as cameras within Home Assistant."
date: 2016-08-13 08:00
sidebar: true
comments: false
sharing: true
footer: true
logo: ffmpeg.png
ha_category: Camera
ha_release: 0.26
---


The `ffmpeg` platform allows you to use every video feed with [FFmpeg](http://www.ffmpeg.org/) as camera in Home Assistant. The input for ffmpeg need to support that could have multiple connection to source (input) in same time. For every user in UI and all 10 seconds (snapshot image) it make a new connection/reading to source. Normally that should never be a trouble only in strange selfmade constructs can be make mistakes.

<p class='note'>
You need a `ffmpeg` binary in your system path. On Debain 8 you can install it from backports. If you want Hardware support on a Raspberry Pi you need tobuild from source by yourself. Windows binary are avilable on the [FFmpeg](http://www.ffmpeg.org/) website.
</p>

To enable your FFmpeg feed in your installation, add the following to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
camera:
  - platform: ffmpeg
    input: FFMPEG_SUPPORTED_INPUT
    name: FFmpeg
    ffmpeg_bin: /usr/bin/ffmpeg
    extra_arguments: -q:v 2
```

Configuration variables:

- **input** (*Required*): A ffmpeg compatible input file, stream or feed.
- **name** (*Optional*): This parameter allows you to override the name of your camera.
- **ffmpeg_bin** (*Optional*): Default 'ffmpeg'.
- **extra_arguments** (*Optional*): Extra option they will pass to `ffmpeg`. i.e. image quality or video filter options.

### {% linkable_title Image quality %}

You can control the `image quality` with [`extra_arguments`](https://www.ffmpeg.org/ffmpeg-codecs.html#jpeg2000) `-q:v 2-32` or with lossless option `-pred 1`.

### {% linkable_title Troubleshooting %}

In most of case, `ffmpeg` autodetect all needed options to read a video/audio stream or file. But it is possible in rare cases that's needed to set a option to help `ffmpeg`. Per default `ffmpeg` use 5 seconds to detect all options or abort.

First check, if your stream playable by `ffmpeg` with (use option `-an` or `-vn` to disable video or audio stream):

```
$ ffmpeg -i INPUT -an -f null -
```

Now you can see what going wrong. Following list could be help to solve your trouble:

- `[rtsp @ ...] UDP timeout, retrying with TCP`: You need to set RTSP transport in the configuration with: `input: -rtsp_transport tcp -i INPUT`
- `[rtsp @ ...] Could not find codec parameters for stream 0 (Video: ..., none): unspecified size`: FFmpeg need more data or time for autodetect. You can set the `analyzeduration` and/or `probesize` option, play with this value. If you know the needed value you can set it  with: `input: -analyzeduration xy -probesize xy -i INPUT`. More information about that can be found on [FFmpeg](https://www.ffmpeg.org/ffmpeg-formats.html#Description).

