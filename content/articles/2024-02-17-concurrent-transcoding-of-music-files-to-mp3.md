```json
{
  "author": "Andreas Linz",
  "title": "Concurrently transcode audio files to MP3 using ffmpeg and Python",
  "description": "A Python script that concurrently calls ffmpeg to transcode audio files to MP3.",
  "created_at": "2024-02-17"
}
```
# Concurrently transcode audio files to MP3 using ffmpeg and Python

With the start of this year my Spotify subscription has ended and I'm now using Apple Music, mainly because it's part of my [Apple One](https://www.apple.com/de/apple-one/) subscription, which not only makes it a lot cheaper than Spotify, Apple Music also allows me to hear my local music everywhere if it's on iCloud. However, that's where the advantages end, since Apple Music really sucks from a software quality standpoint. On iOS it's acceptable, but on macOS there were numerous crashes, it's kind of slow, and adding a single album to a playlist often ends in the album being added like a hundred times. Is there no QA for Apple desktop software anymore? I digress.

One limitation of Apple Music is, that it only supports a handful of audio file formats, i.e. there's no support for FLAC, OGG, OPUS and all the other good stuff. Some weeks ago I took NPR's [audio quality test](https://www.npr.org/sections/therecord/2015/06/02/411473508/how-well-can-you-hear-audio-quality), and found that I couldn't distinguish between lossless and 128kbps MP3 in most cases. In the early days of MP3 this was easy, especially for acoustic music. I remember that hi-hats for example sounded "watery", due to the bad codecs back then. However, those days are over, and modern MP3 codecs have gotten a lot better. So I decided to "bite in the sour apple" (that's not an English phrase, is it?) and convert my local library to MP3, so I can listen to it on the go in Apple Music.

I'm not a fan of bash scripts, at least not when they're longer than 5 lines. For me the natural choice is to use Python for such a scripting task. Below are the contents of `convert.py`, the script I wrote to convert the files concurrently. 

```python
#!/usr/bin/env python3

import concurrent
from concurrent.futures import ThreadPoolExecutor
import os
from pathlib import Path
import subprocess
import sys
from typing import List

source_dir = Path(sys.argv[1])
dest_dir = Path(sys.argv[2])
known_extensions = set([".m4a", ".flac", ".ogg"])
source_files = []
for dirpath, _, filenames in os.walk(source_dir):
    for filename in filenames:
        p = Path(dirpath).relative_to(source_dir).joinpath(filename)
        if p.suffix in known_extensions:
            source_files.append(p)

# 1/3
```

In this first step we're collecting the file paths relative to the source directory of all audio files, if they're matching one of the `known_extensions`. It's important that we have the relative path, because this will simplify the path handling of the destination path.

```python
args_list = []
for source_file in source_files:
    source = source_dir.joinpath(source_file)
    dest = dest_dir.joinpath(source_file).with_suffix(".mp3")
    try:
        os.makedirs(dest.parent)
    except FileExistsError:
        print(f"{dest.parent} already exists...")
    # https://trac.ffmpeg.org/wiki/Encode/MP3
    args_list.append(
        [
            "ffmpeg",
            "-i",
            str(source),
            "-c:a",
            "libmp3lame",
            "-qscale:a",
            "2",  # Use high quality encoding, 4 is default
            "-abr",
            "1",  # Enable average bitrate
            str(dest),
        ]
    )

# 2/3
```

In this section we build a list of arguments that we want to pass to the task function we use to schedule the work onto a thread pool. The speedup is almost linear, since `ffmpeg` will only use a single CPU core to transcode a file, hence this workload scales well with the number of available CPU cores. I decided to use average bit-rate (ABR) encoding, and the resulting files vary around 180kbps +/- 20kbps.
In a blind-test I could never tell that this is not 320kbps. If you disagree because you've golden ears, fine, I don't believe you can actually hear a difference 😛.

```python
def run(args: List[str]):
    subprocess.run(
        args=args,
        check=True,
    )


with ThreadPoolExecutor(max_workers=4) as executor:
    futures = [executor.submit(run, args) for args in args_list]
    for future in concurrent.futures.as_completed(futures):
        future.result()
```

Lastly we're defining the task function called `run` (could've named it `task` of course) and then use a [`ThreadPoolExecutor`](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor) to schedule the workload onto 4 CPU cores. If you want to sature all your available CPU cores, use `multiprocessing.cpu_count()`.

Python is ~~fin~~ fun 🥳
