---
title: "Faster FFmpeg on Graviton3"
date: 2023-01-23
draft: false
description: "Benchmarking on AWS"
tags: [aws, linux]
---

## Overview

I was recently investigating the performance of [FFmpeg](https://ffmpeg.org/) on Intel instances, and was curious how much faster ARM instances could perform after reading [this blog post](https://aws.amazon.com/blogs/opensource/optimized-video-encoding-with-ffmpeg-on-aws-graviton-processors/) from AWS. At the time of this writing, [AWS Graviton3](https://aws.amazon.com/ec2/instance-types/c7g/) claimed to offer the best price to performance in EC2 for compute-intensive workloads, and was also one of their relatively newer offerings.

To take advantage of these improvements, you will need to be using v6.0 or newer of FFmpeg. The base instance that I compared this ARM instance to was a [3rd Generation Intel Xeon](https://aws.amazon.com/ec2/instance-types/c6i/). I also utilized [GNU parallel](https://www.gnu.org/software/parallel/) to efficiently pipeline some of my commands.

## Benchmark

First, I needed to extract the frames from my source piece of media on each machine:

```bash
#!/bin/bash
ffmpeg \
	-i 'output.mjpeg' \
  -c copy \
  -map 0  \
  -segment_time 00:00:03 \
  -f segment frames/output%03d.mjpeg
```

Finally, I applied a quick [colorbalance](http://underpop.online.fr/f/ffmpeg/help/colorbalance.htm.gz) filter in parallel to our `.jpeg` frames:

```bash
#!/bin/bash
time parallel 'ffmpeg -i {} -vf colorbalance=gm=0.25 -y {}.mp4' ::: frames/*.mjpeg
```

## Results

Honestly, I was expecting about a 20-30% speed boost. The `c6i` took 9.4 seconds averaging 10 runs, and the `c7g` took 3.9 seconds averaging 10 runs, for a **~240% performance boost**. Not bad at all!
