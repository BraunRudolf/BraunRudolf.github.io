---
layout: post
title: 'Learning Path: Diving into Kafka Basics'
date: 2024-12-10 10:53 +0100
description: 'This blog post documents my intentions for learning Kafka, my first steps in the Kafka world, and the starting point for my future Kafka endeavors.'
image: 
category: [Learning Path]
tags: [kafka]
published: True
sitemap: false
---

### **Why Kafka?**

More and more data is produced every year, much of it being event-driven. Whether it’s clicks on a website or sensor data from a machine, Kafka is a useful tool to have under one’s belt. Since I have a personal use case for it (which might be overkill), I figured I should at least take a look and deploy it in my local environment.

Currently, I have a few smart sockets that track the energy usage of certain devices in my home, as well as the energy production of my solar panel. Additionally, I plan to deploy multiple Moisture Sensor Stations to monitor the soil of most of the plants in my apartment. The sockets' firmware makes it easy to publish their data via MQTT to my broker, as will the Sensor Stations, for now.

My ultimate goal is to build a streamlined data pipeline:  
**Sensor → MQTT broker → Kafka → Event-stream database → Dashboard.**
![IoT Data Pipeline](/assets/img/posts/kafka_data_pipeline.png)
*influxdb and Grafana are currently placeholder


---

### **Step 1: exploring kafka’s basics**

i started my journey on the apache kafka website. it presented some marketing "blabla," but i also got my first understanding of what kafka is:  
"...an open-source distributed event streaming platform..."

...and what it’s used for:  
"...high-performance data pipelines, streaming analytics, data integration..."[^1].

i learned about kafka’s core capabilities:

- **high throughput**
- **scalability**
- **permanent storage**
- **high availability**

and its key features:

- **guaranteed ordering**
- **zero message loss**
- efficient **exactly-once processing**

additionally, kafka includes built-in stream processing and a "connect interface" that integrates with many other systems out of the box. it also supports a wide range of programming languages and has a large ecosystem of open-source tools, driven by an extensive user community.

---

### **Step 2: diving into the documentation**

next, i continued my journey by reading the documentation, which gave me a basic understanding of kafka's terms and workings.

To my surprise, the documentation focused on three core "capabilities" of Kafka as a streaming platform, which differed from those on the starting page. this time, the focus was on:

- Publishing and subscribing to streams of events
- Storing these streams in a durable and reliable way
- Real-time and retrospective processing

All of this was described as being distributed, highly scalable, elastic, fault-tolerant, and secure.

I found this to be a broader and more holistic view of Kafka. The article clearly defined the main concepts and terminologies of Kafka, and how everything works together. Here’s a list of those concepts:

- **Server**
- **Clients**
- **Events**
- **Topics**
- **Producer**
- **Consumer**

Here’s a **TL;DR** of the documentation. In a Kafka context:

- **Servers** run Kafka Broker in their storage layer and Kafka Connect as an integration layer between Kafka Broker and Clients.
- Kafka **Brokers** handle the management of Topics, under which Events are stored.
- **Clients** can either produce or consume Events, acting as Data Sources or Data Sinks, respectively. They either write to or read from a Topic.

I skipped the "Use Cases" section since I already knew what I wanted to use Kafka for, and dived straight into the Quick Start guide.

---

### **Step 3: Initial Setup Choices**

Right at the beginning, after setting up the Kafka environment, I encountered some confusion. I was presented with two choices and a lack of explanations:

- **KRaft**
- **ZooKeeper**

I took a quick detour to read about both and their differences (1, 2, 3).

**Long story short:**  
KRaft is the new kid on the block, simplifying Kafka deployment. So, I decided to stick with it for now and read up on ZooKeeper later (another post in the future?).

Next, I faced another decision: **Code versus Docker**. Docker even had two images!

---

### **Step 4: Docker Image Dilemma**

**According to GPT:**

- **VM-Based Kafka:** Ideal for high-throughput, long-running workloads where JVM optimizations can be fully leveraged, and for traditional environments where compatibility and stability are priorities.
- **GraalVM Native Kafka:** Suitable for cloud-native, serverless, and containerized environments where startup time, memory efficiency, and lightweight scaling are crucial.

**Memory efficiency and lightweight scaling?** Perfect for K3s!  
But, as they say, not everything that glitters is gold. Further down in the documentation, there’s a note about the GraalVM-based image:

> **NOTE:** This image is experimental and intended for local development and testing purposes only; it is not recommended for production use.

---

### **Step 5: Quick Start Success**

Everything after that went smoothly and was mostly copy-paste. First, I started the Broker, then created a topic, wrote something to it, and finally read messages from the topic.

Step six was what I was most interested in: using **Kafka Connect** to import and export data. Kafka Connect allows for continuous ingestion of data from external systems into Kafka and vice versa.

Unfortunately, the example was quite shallow—it only connected to a local file and wrote to another local file. However, it did provide initial information on adding a plugin to a local Kafka Connect deployment.

---

### **Next Steps**

After the Quick Start guide, the documentation delves deeper into the configuration of various parts and general design principles. At this point, though, I decided to try applying my newly learned knowledge to my specific use case.

I hope you enjoyed reading about my journey through the first two parts of the Kafka documentation. In future blog posts, I’ll continue my Kafka learning path and recap how I went from the Quick Start guide to a single-node deployment with a connected MQTT broker via Kafka Connect, and where I currently stand with my K3s deployment.

Cheers!
