# Notes About My DeepRacer Setup

## Motivation

In June 2022, I attended an AWS Summit and had a chance to learn about and build a model for [DeepRacer](https://aws.amazon.com/deepracer/). I was struck by how it made a complex topic (machine learning) simple, approachable, and fun. I felt it would be a perfect way to introduce my son's class, most of whom have a keen interest in technology, to it.

I realized that the cost of iterating on models and training them could get prohibitively expensive for an entire class, so I looked into a way to train them using a local computer. That lead me to the DeepRacer local training project(s) and the community around it.

## Setup

### Hardware

I had an old Mac Pro (MacPro3,1) gathering dust and thought it would make an ideal (albeit slow) budget training machine since it was designed to be run 24/7 (has server-grade hardware and good cooling). It only had 10GB of RAM and mechanical hard drives, so I purchased another 16GB of RAM + a 1TB SSD for better performance. Here are the technical specs:

```MacPro3,1
2 x 2.8GHz Quad Core Xeon CPUs (E5462)
26GB DDR2 800MHz ECC RAM
1TB SATA III SSD (connected via a SATA II connector on the motherboard)
ATI Radeon HD 2600 XT w/ 256 MB GDDR3 SDRAM (obviously not useful for GPU training)
```

### Software

After discussion on the #dr-training-local Slack channel, I decided to go the route of using Ubuntu Linux (22.04 LTS). I did a basic/minimal install to save disk space, so here are the packages I needed to add post-install to get it going:

* git
* docker
* jq
* awscli
* python3-pip

Note that I used `apt` to install these rather than `snap` since I ran into [this issue with jq](https://stackoverflow.com/questions/58128001/could-not-open-file-lol-json-permission-denied-using-jq) on my first attempt.

### Additional Setup

I followed the instructions for a local installation on [this webpage](https://aws-deepracer-community.github.io/deepracer-for-cloud/installation.html) and ran into the following problems.

By default, `docker` requires system permissions/access which regular user accounts don't have. To fix this, you need to add your user account to the `docker` group as follows:

```sudo usermod -aG docker ${USER}```

The Python `boto3` module is required so I used [pip](https://pypi.org/project/pip/) to install it:

```pip3 install boto3```

One the first run of `dr-start-training`, I saw the following crash (core dump) after it started Sagemaker:

```Illegal instruction (core dumped)```

Upon further investigation, I discovered that this is due to the CPUs in the Mac Pro not having [AVX instructions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions). It turns out that these are [required by Robomaker](https://hub.docker.com/r/awsdeepracercommunity/deepracer-robomaker).

My final attempt to get it working will be to install a modern GPU in the Mac Pro and use that with Robomaker (to bypass the CPU requirements). I've been able to successfully install an NVIDIA GTX 1660 Super graphics card in the Mac Pro and boot into Ubuntu (using a PC PSU to power it). Now I just need to see how stable it is before I commit to [tapping the Mac Pro PSU](https://thehouseofmoth.com/mac-pro-pixlas-mod/) for power.
