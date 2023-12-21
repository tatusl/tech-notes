# FFmpeg

## Reduce noise from spoken audio using trained neural networks

Download trained model from https://github.com/GregorR/rnnoise-models/tree/master/conjoined-burgers-2018-08-28

Apply audio filter, for example with command:

```
ffmpeg -i orig.ext -af arnndn=m=cb.rnnn -c:v cleaned_audio.ext
```

Parameter `-c:v` just copies the videostream without processing.

I used this to clean recorded audio from screen recording and it worked really fine.

## Resources:

* https://superuser.com/a/1739632
