# Notes About My DeepRacer Setup

## Motivation

In June 2022, I attended an AWS Summit and had a chance to learn about and build a model for [DeepRacer](https://aws.amazon.com/deepracer/). I was struck by how it made a complex topic (machine learning) simple, approachable, and fun. I felt it would be a perfect way to introduce my son's class, most of whom have a keen interest in technology, to it.

I realized that the cost of iterating on models and training them could get prohibitively expensive for an entire class, so I looked into a way to train them using a local computer. That lead me to the DeepRacer local training project(s) and the community around it.

## Setup

### Hardware

I had an old Mac Pro (MacPro3,1) gathering dust and thought it would make an ideal (albeit slow) budget training machine since it was designed to be run 24/7 (has server-grade hardware and good cooling). It only had 6GB of RAM and mechanical hard drives, so I purchased another 16GB of RAM and a 1TB SSD for it. Here are the technical details:

```
MacPro3,1
2 x 2.8GHz Quad Core Xeon CPUs (E5462)
22GB DDR2 800MHz ECC RAM
1TB SATA III SSD (connected via a SATA II connector on the motherboard)
ATI Radeon HD 2600 XT w/ 256 MB GDDR3 SDRAM (obviously not useful for GPU training)
```

### Software

After discussion on the #dr-training-local Slack channel, I decided to go the route of using Ubuntu Linux (22.04 LTS). I did a basic/minimal install to save disk space, so here are the packages I needed to add post-install to get it going:

* git
* docker
* jq
* awscli

Note that I used `apt` to install these rather than `snap` since I ran into [this issue with jq](https://stackoverflow.com/questions/58128001/could-not-open-file-lol-json-permission-denied-using-jq) on my first attempt.

### Additional Setup

I followed the instructions for a local installation on [this webpage](https://aws-deepracer-community.github.io/deepracer-for-cloud/installation.html) and ran into the following problems.

By default, `docker` requires system permissions/access which regular user accounts don't have. To fix this, you need to add your user account to the `docker` group as follows:
```
sudo usermod -aG docker ${USER}
```

Additionally, because my Mac Pro has multiple network devices, `docker` needed to know which interface to bind to. Since I don't currently need multiple machines in my swarm, I used this command to bind it to the loopback (local only) interface:
```
docker swarm init --advertise-addr 127.0.0.1
```

Still working through the AWS setup...
