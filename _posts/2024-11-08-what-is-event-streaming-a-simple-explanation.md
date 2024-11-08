---
layout: post
title: What is Event Streaming? A Simple Explanation
date: 2024-11-08 16:56 +0100
description:
image:
category: [Event Streaming] 
tags: [event streaming, kafka]
published: true
sitemap: true
---
## TL;DR

Describes how events from event sources form event streams, which are processed using event stream processing and delivered to event sinks. This requires an event streaming platform to manage and process each part of the flow.

## Intention

I'm currently diving into Apache Kafka, and one of the first things I was confronted with was event streaming. Obviously, I had an idea of what event streaming is, since it came up back in my studies, but I wasn't able to explain it to someone else, at least not with high confidence. But you know what they say: "To know if you understood something, try to explain it to someone else." So here we go.

## Explanation

### Where events are born!

Event streaming begins at event sources. It's a high-level term for publishers, or in the case of Kafka, producers. Basically, processes or applications that generate events and send them out to the world.

### What are events, though?

An event, also called a record or message, is immutable and represents a fact. They hold information about the **what**, the **where**, and the **when** something happened. Typical events include sensor readings, financial transactions, etc.

### When are they useful?

By themselves, events are just records of something that happened. It's only when they form an event stream—a continuous flow of events over time—that they become valuable. Event streams can be used to track changes or reactions in a system.

### Where do they go?

To make event streams usable, we need something called event stream processing. It can accept event streams from one or multiple event sources and can join, transform, or store them. This is how event stream processing enables real-time analytics and pattern detection, which makes it really powerful.

### What comes next?

On the other side of event processing are data sinks, also called subscribers, or in the case of Kafka, consumers. These are processes or applications that are interested in the event streams, processed or unprocessed.

### Summary

There you have it—not quite toddler-ready, but I bet most people will now have an understanding of event streaming. I certainly do, and I think I'm ready to dive deeper into Kafka and learn more about my first event streaming platform.  
I didn’t cover topics like messages, topics, partitions or the details of publishers and subscribers—these will likely be explored in future posts on Kafka.  
If you notice something I missed or have additional insights, feel free to reach out!

Cheers!
