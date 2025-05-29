# FFmpeg

## Concatenate video files

Videos should probably be encoded with similar codec etc.

```
ffmpeg -f concat -safe 0 -i <(for f in ./*.mp4; do echo "file '$PWD/$f'"; done) -c copy output.mp4
```
