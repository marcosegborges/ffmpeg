# FFmpeg

[FFmpeg](https://ffmpeg.org/ffmpeg.html) is a command line toolbox to manipulate, convert and stream multimedia content such as audio, video, subtitles and related metadata.

## Installing FFmpeg

> Windows

* Download and install from [FFmpeg.org](https://ffmpeg.org/)

> GNU/Linux

* CentOS / Red Hat
```
$ sudo yum install ffmpeg
```

* Debian / Ubuntu
```
$ sudo apt-get install ffmpeg
```

> Mac OS 

* Install using [Homebrew](https://brew.sh/)! Check [brew_install_ffmpeg.sh](brew_install_ffmpeg.sh) out to do this easily.

## FFmpeg Documentation

The documentation is available on [ffmpeg.org](https://ffmpeg.org)
and on the [wiki](https://trac.ffmpeg.org).


# Homebrew for MacOS X

[Homebrew](https://brew.sh/) is a free and open-source software package management system that simplifies the installation of software on Apple's MacOS operating system.

Features, usage and installation instructions are summarized on [brew.sh](https://brew.sh/).

## What packages are available on Homebrew?

1. You can browse the Homebrew Packages on [formulae.brew.sh](http://formulae.brew.sh/).
2. Or type `$ brew search` for a list of formulae.

## Homebrew Documentation

`$ brew help` or `$ man brew` or check brew documentation on [docs.brew.sh](https://docs.brew.sh).


# High Definition Gif with FFmpeg

After getting FFmpeg installed, let's try it out on a video.

> In this example, I am going to use a personal video of my New Year's Eve 2017 in Paris with my fiancée. See more amazing photos on our Instagram: [@MarcosGomesBorges](https://www.instagram.com/MarcosGomesBorges/) and [@musikasia_flute](https://www.instagram.com/musikasia_flute/).

```
ffmpeg -i video.m4v gif_1.gif
```

`FFmpeg` reads from an arbitrary number of input "files" specified by the `-i` option, and writes to an arbitrary number of output "files." In our example, we are calling the FFmpeg program and telling it that `video.m4v` is our input file, and `gif_1.gif` is our output file.

> video.m4v 2.3 MB => gif_1.gif 25.1 MB :cry:

![Gif 1](gif_1.gif?raw=true)

## Simple Rescaling

The file size `gif_1.gif` with 25.1 MB is too large. We can rescale the gif to reduce its size. To rescale an image/video output it is necessary only to specify the size (e.g., 640x480, 1024x768, 1280x960). You can use the scale filter in its most basic form using the `vf` option:

```
ffmpeg -i video.m4v -vf scale=720:400 gif_2.gif
```

> video.m4v 2.3 MB => gif_2.gif 14 MB :cry:

![Gif 2](gif_2.gif?raw=true)

## Keeping the Aspect Ratio

As you can see, the aspect ratio is not the same as in the original image, so the image appears stretched. If we would like to keep the aspect ratio, we need to specify only one component, either `width` or `height` and set the other component to `-1`:

```
ffmpeg -i video.m4v -vf scale=480:-1 gif_3.gif
```

> video.m4v 2.3 MB => gif_3.gif 11.4 MB

![Gif 3](gif_3.gif?raw=true)

## Specify the duration in seconds

Pretty cool, and the gif has now 11.4 MB. But I bet I can make it loop nicely by just using the first few seconds, so we use the duration flag `-t` and specify the duration of the input/output in seconds:

* When used as an input option (`-t` before `-i`), limit the duration of data read from the input file. Example: `ffmpeg -t 1.5 -i input.mp4 output.gif`.

* When used as an output option (`-t` before an output file), stop writing the output after its duration reaches the determined time. Example: `ffmpeg -i input.mp4 -t 1.5 output.gif`.

See this example:

```
ffmpeg -t 1.2 -i video.m4v gif_4.gif
```

> video.m4v 2.3 MB => gif_4.gif 5.9 MB :unamused:

![Gif 4](gif_4.gif?raw=true)

## Framerate (fps)

So it loops! Quite slow too, maybe we can drop every other frame? Aha, thanks to the `-r` flag, we can choose a frame rate for the output. Note that this option does different things based on its order. Here is an example about how to force the frame rate of the `output file` to 24 fps:

```
ffmpeg -t 1.2 -i video.m4v -r 24 gif_5.gif
```

> video.m4v 2.3 MB => gif_5.gif 5.3 MB :satisfied:

![Gif 5](gif_5.gif?raw=true)

## Start Position

If you want to specify a time from the video to start the gif, you can use the `-ss` flag. Oh yeah, and your seconds can be decimals. If you have a longer video and want to define the starting position in hours, minutes, seconds, you can use `hh:mm:ss` format, like `00:00:07` instead of `7`.

```
ffmpeg -t 1.2 -ss 1.5 -i video.m4v -r 24 gif_6.gif
```

> video.m4v 2.3 MB => gif_6.gif 5.1 MB 

![Gif 6](gif_6.gif?raw=true)

## High Definition Gif

It is possible to produce a `high-quality gif` with `reduced file size` using color palette algorithms. Discover more on [this blog](http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html).

1. First, we have to generate a custom color palette, so we do not waste space while storing colors that we do not use (the gif file format is limited to 256 colors):

```
ffmpeg -t 2.3 -ss 1.5 -i video.m4v -vf \
fps=24,scale=480:-1:flags=lanczos,palettegen palette.png
```

This command generates `palette.png` which we can be used in our 2nd line in terminal. By the way, the backslash at the end of the line is useful if you run out of space and need to keep typing. You should recognize what `-t`, `-ss`, and `-i` do here. The `-vf` is a way to invoke filters on our video. Also, we can describe fps and scale. The flags describe what algorithm to use; more info on [this blog](http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html).

2. After creating the `palette.png` file, we can `generate the gif` with the following command:

```
ffmpeg -t 2.3 -ss 1.5 -i video.m4v -i palette.png \
-filter_complex "fps=24,scale=480:-1:flags=lanczos[x];[x][1:v]paletteuse" gif_HD.gif
```

> video.m4v 2.3 MB => gif_2.gif 7.2 MB :smirk:

![Gif HD](gif_HD.gif?raw=true)

Now, let's compare the `HD Gif` with the `standard Gif` generated using FFmpeg. To do this, we will create a gif with the similar time and scale of the `HD Gif`:

```
ffmpeg -t 2.3 -ss 1.5 -i video.m4v \
-vf scale=480:-1 gif_Standard.gif
```

![Gif HD](gif_HD.gif?raw=true)
![Gif HD](gif_Standard.gif?raw=true)

> The HD Gif is impressive! Much sharper than the standard Gif.

![Gif HD](std_vs_hd_gif.png?raw=true)

> Let me know if there is anything I can explain in more detail, but do not ask me for help to install  things. Just google it!

# How to create a video from images with FFmpeg

For this example, we have four images from [@musikasia_flute](https://www.instagram.com/musikasia_flute/):

* image_01.jpg
* image_02.jpg
* image_03.jpg
* image_04.jpg

To take a list of images and create a video we can use the following command:

```
ffmpeg -f image2 -framerate 2.0 -i image_%02d.jpg -s 640x480 my_video.mp4
```

* `-f image2` is necessary to create a video from many images.

* `-s 640x480` specifies the size of the video (resolution).

* `image_%02d.jpg` specifies to use a decimal number composed of two digits padded with zeroes to express the sequence number. It is the same syntax supported by the `C printf function`, but it only accepts integer values.

![Kasia video](my_video.gif)

# Adding a mp3 to a video

Adding sound to a video is straightforward:

```
ffmpeg -f image2 -framerate 2.0 -i image_%02d.jpg -s 640x480 \
-i audio.mp3 -acodec copy my_video.mp4
```

* `-i audio.mp3`  the audio filename.

* `-acodec copy` copies the audio from the input stream to the output stream.


# Playback Issues for Quicktime/Other Codecs

Quicktime and some other codecs have trouble playing certain pixel formats, such as `4:4:4 Planar` and `4:2:2 Planar` while 4:2:0 seems to work fine.

Add the following flag to force the pixel format:

```
-pix_fmt yuv420p
```

## Finer Bitrate control (to control size and quality)

```
 -b 4M
```

You can use the `-b` flag to specify the target bitrate. In this case, it runs at `4 megabits per second`.

## Using `-vpre` with a setting file

```
 -vpre normal
```

 `-vpre` is the quality setting; better quality takes longer to encode. Some alternatives are default, normal, hq, or max. Note that the `-vpre` command only works if the corresponding setting file is available.

# Author

I am [Marcos Gomes-Borges](http://www.marcosgomesborges.com), a R&D Computer Vision Engineer.

Feel free to contact me on my twitter [@MGomesBorges](http://twitter.com/MGomesBorges) to discuss any idea if you wish.


Instagram: [@MarcosGomesBorges](https://www.instagram.com/MarcosGomesBorges/) and [@musikasia_flute](https://www.instagram.com/musikasia_flute/).


Let me know if there is anything I can explain in more detail, but do not ask me for help to install  things. Just google it!