---
layout: post
title: 'Lessons Learned: Deploying MLFlow with NFS Artifact Store'
date: 2024-10-30 10:30 +0100
description: Learn from my experience setting up MLFlow with an NFS artifact store.
image:
category: [Lessons Learned]
tags: [mlflow, nfs]
published: true
sitemap: true
---
## TL;DR

If you're deploying an MLFlow Tracking Server on Kubernetes (or another machine separate from your MLFlow client) using an NFS mount for artifact storage, ensure that the `ARTIFACT_ROOT` path is accessible and consistent across both client and server machine. Misconfigurations can lead to read-only errors or result in artifacts not being logged as expected. Below, I’ll share my experiences, challenges, and solutions.

## My Setup and Initial Steps

This was my first time deploying an application to Kubernetes outside of a tutorial. I considered several options, but MLFlow appeared to be the easiest. After creating the manifest and PVC (without a PV, as I intended to use an NFS provisioner), I believed I was ready to deploy to my k3s cluster. At first, everything seemed fine: I could access the UI, and experiments and runs were tracked as expected.

However, when I updated my ML script to include `mlflow.log_artifacts(<model_file>)`, I encountered an error: `OSError: [Errno 30] Read-only file system: '/mlflow'`.

## Diagnosing the Issue

Initially, I questioned whether I had misconfigured my deployment. I checked the permissions on `/mlflow` inside the pod, and everything seemed fine. I even created a file (`touch /mlflow/test.txt`) and a directory (`mkdir /mlflow/test`) within the pod without any issues, indicating that the pod had the necessary permissions and the NFS connection was functional.

What could be the problem? I experimented with various values for `ARTIFACT_ROOT`, but the error persisted. Eventually, I reached the following conclusion:

---

<center><b>RTFM</b><br><b>READ THE F*#KING MANUAL</b></center>

---

In my defense, the information I needed was not easy to find. However, the MLFlow documentation states:

> _To store artifacts in an NFS mount, specify a URI as a standard file system path, e.g., `/mnt/nfs`. This path must be the same on both the server and the client—you may need to use symlinks or remount the client to enforce this consistency._

[Read it for yourself here.](https://mlflow.org/docs/latest/tracking/artifacts-stores.html)

The crucial part is: _This path must be the same on **both** the server and the client._ That’s why I encountered the `OSError`. The issue wasn’t with the pod; it was with my client! The macOS root file system is read-only, preventing the necessary directories and files from being created.

In hindsight, this experience was beneficial because it made me aware of a critical detail. As long as the client has write access to the `ARTIFACT_ROOT` location, no error occurs. The only way to notice artifacts aren’t being saved is by checking the UI and realizing that there are no artifacts. The client writes them to the specified `ARTIFACT_ROOT` location, but when the NFS share is mounted, they can become temporarily hidden.

## Solution: Using a Shared Path for `ARTIFACT_ROOT`

According to the documentation, I only needed to use a file path that exists on both the client and the tracking server and set that as `ARTIFACT_ROOT`. On macOS, reasonable options are `/opt` and `/tmp`. I’m curious how this works on Windows—has anyone tried it?

From what I understand, the client is responsible for writing files to the NFS share. The Tracking server provides the option to download the file via the UI and stores the `ARTIFACT_ROOT` in the SQL database.

In my setup, I switched from using an NFS provisioner to a PV for simplicity, allowing me to determine the mounting point myself.

## Documentation Gaps and Proposal

While it was my mistake not to read the documentation thoroughly, I’m not alone in facing challenges with this setup. Here are [one example](https://github.com/mlflow/mlflow/issues/3476) (a Windows user facing a similar issue) and [another example](https://github.com/mlflow/mlflow/issues/3463). At one point, there may have been better documentation, as indicated in this [issue](https://github.com/mlflow/mlflow/issues/3108) and the corresponding PR, but that information seems to be missing now. To be fair, both the issues and the PR are over three years old.

The current documentation lists only three “Common Setups,” and while one could argue that an NFS store is similar to a local file system, the interaction between the MLFlow tracking server, NFS, and the Tracking API is quite different. Therefore, I propose adding a fourth setup (an idea not originally mine, as seen in the last issue):

![MLFlow Setup Proposal](assets/img/posts/MLFlow_4.png)  
_Modified form from [here](https://mlflow.org/docs/latest/tracking.html#components)_

Perhaps the assumption is that users of NFS or other network-attached storage solutions with MLFlow already know what they’re doing, which might be why the current documentation is considered sufficient.

## Final Thoughts

Ultimately, my configuration oversight taught me valuable lessons about artifact storage in MLFlow. While my mistake stemmed from missing a small detail, I hope sharing my experience helps others avoid similar issues.

In the future, I will definitely explore a cloud solution as an artifact store, just to see if that experience goes more smoothly.
