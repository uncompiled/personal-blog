---
date: 2015-06-27T15:00:00Z
tags: ["Docker", "Containers", "Open Container Project"]
title: DockerCon 2015
---

I just got back from [DockerCon 2015](https://blog.docker.com/2015/06/dockercon-2015-keynote-videos/), which happened earlier this week. I still can't believe how many people were there. Containers are [not a new idea](https://en.wikipedia.org/wiki/Solaris_Containers), but Docker makes it really easy to **CONTAINERIZE ALL THE THINGS** and I think that's the main reason it has become so popular. Out of the sea of announcements, there were two things that stood out to me...

## Open Container Project

Docker has become the de facto container standard over the past few years, but in late 2014, CoreOS introduced an alternative called [rkt](https://github.com/coreos/rkt) (Rocket) and the [appc](https://github.com/appc) (App Container Specification). If containerization is the future (or the present), a container war would be counterproductive toward industry adoption.

Enter the [Open Container Project](http://opencontainers.org/). With oversight from the Linux Foundation and a ton of industry partners (including Docker, CoreOS, and the appc maintainers), this creates a positive path for user adoption without worrying about vendor lock-in or whether their particular architectural choices will become deprecated at some point in the future. The establishment of the OCP is a huge step forward in delivering on the promise of containers, such as portability and interoperability.

Building on top of this announcement, Docker announced that they're essentially refactoring the Docker platform to focus on a clean separation of concerns. The first component is the OS container runtime, [runC](https://blog.docker.com/2015/06/runc/), which was donated to the Open Container Project and will likely serve as the basis for Docker, rkt, and future OCP tools.

## Customer #1: GSA

During the Day 2 keynote, Docker announced that the General Services Administration (GSA) was the first customer for [Docker commercial solutions](http://blog.docker.com/2015/06/docker-ready-for-production/)! Booz Allen Hamilton presented a session on ["How to Build a Secure DevOps Environment for the Government"](http://www.slideshare.net/Docker/dockercon-sf-2015-how-to-build-a-secure-devops-environment-for-the-government), which went into some detail about how the GSA is using Docker to build a secure PaaS.

This is a pretty revolutionary thing to see within the US Government. The availability of an accredited PaaS would eliminate the process delays for getting the infrastructure approved. Software could be delivered in weeks or days instead of months. I'm a big proponent of Civic Tech and have been following the GSA's work lately, including 18F's relentless insistence on open source and transparency. If there is any industry or organization worth disrupting, it's the government (at all levels). If these efforts are successful, it creates a win-win situation for everyone: citizens, government employees, and tax payers.
