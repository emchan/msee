# msee (m-see)

**msee** — “mc with the ability to *see*” — is a fork of
[GNU Midnight Commander (mc)](https://midnight-commander.org/) that adds an integrated file
previewer.  Previews are rendered inside a bordered pane in the lower third of the right
panel using **sixel** graphics, so they work in sixel-capable terminals such as
**Windows Terminal**.

## Features added on top of Midnight Commander

- **Preview pane** in the lower third of the right panel, surrounded by a border, with the
  selected file's name shown as a caption.
- **Image previews** for common formats (PNG, JPEG, GIF, BMP, WebP, TIFF, XPM, PPM/PGM/PBM,
  HEIC/HEIF, AVIF) rendered as sixel by ImageMagick.
- **Video previews** for MP4/MOV/MKV/AVI/WebM/M4V: a looping animated preview streamed from
  a single `ffmpeg` process at 3 fps, sped up 5×, with duration cached via `ffprobe`.
- **Document/text previews** for PDF (via `pdftotext`) and other text-like files (via `head`).
- **Automatic resize/recalculation** of the preview when the terminal window is resized.
- **Aspect-ratio-preserving fit**: images are scaled to fit the pane without distortion and
  centered with ImageMagick, with unused space filled by a background colour.
- Preview tracks the selection of the left panel, updating as you navigate.

## Building

```sh
./autogen.sh
./configure
make
sudo make install
```

The resulting binary is installed as `msee` (typically `/usr/local/bin/msee`), along with
`mc` (typically `/usr/local/bin/mc`), which is installed as a symlink to `msee`.

## Runtime requirements

- ImageMagick (`convert`) (format conversion, scaling/fitting and sixel output)
- `ffmpeg` / `ffprobe` (animated video preview streaming and duration lookup)
- `pdftotext` (PDF previews, optional)

## Performance notes

- Video previews use a single persistent `ffmpeg` process per movie instead of launching
  `ffmpeg` for every frame.
- `ffprobe` duration lookup is cached per file.
- Frames are streamed at low resolution, scaled to the pane's pixel size, and looped with
  `-stream_loop -1`.
- Frame conversion invokes ImageMagick directly (no shell wrapper per frame).
- Frames are staged in an anonymous `/dev/shm` buffer instead of being written to disk.
- `ffmpeg` skips audio/subtitle/data streams and uses fast bilinear scaling with a single
  decoding thread.
- Preview processes are stopped when the selection changes, the preview is toggled off, or
  the file type is unsupported.

## Development notes

- The initial preview-pane port was AI-generated.
- Human debugging was required to make the preview viewport fill correctly: the sixel
  pixel sizing and the preview/mouse coordinate calculations were wrong.
- The author also had to ask the AI to optimize the video pipeline so `ffmpeg` is not
  launched repeatedly for each displayed frame. It now uses one persistent decoder per
  movie.
- Total effort for the preview work was about one day.

## Notes

m-see retains all standard Midnight Commander functionality; the preview is an additional
pane and does not replace the normal file listing.



> **Note:** This fork started as an **AI-generated** port and is provided for
> **educational purposes only**. It retains the original licensing and qualities of GNU
> Midnight Commander. We make a best effort to make the previewer work, but there may be
> corner cases and rough edges.