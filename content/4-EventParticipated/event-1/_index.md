---
title: "4.1 Company Visit: Amazon Web Services – November 14, 2026"
date: 2026-11-14
weight: 1
chapter: false

---
#Riot Games Journey
On November 14, 2026, I was present on the 7th floor of the Grand Teera building, Cát Linh, Hà Nội, to attend a session presented by Mr. Nhân, a member of FCAJ. I heard about Riot Games’ development journey—from its early days as a small studio with a few bold ideas to becoming one of the world’s leading game entertainment companies.

![Event picture](/images/event1/event1a.jpg)

One of the things that makes Riot’s growth story special is how they built the technical infrastructure to operate globally scaled games. Riot placed almost the entire operational “burden” on Amazon Web Services (AWS) and began its cloud migration in 2016.

The core of Riot’s architecture is a clear separation between two types of servers: platform servers, responsible for out-of-match features (interface, store, player information, etc.), and game servers, which directly simulate matches. Game servers have strict latency requirements and often have to handle thousands of concurrent matches with small, high-frequency UDP packets.

![EVent picture](/images/event1/event1b.jpg)

To manage this enormous workload, Riot developed the Game Provisioning Platform (GPP)—a common game provisioning platform for all titles, capable of scaling globally. They also built an internal abstraction layer called R-Cluster to optimize resource allocation, helping reduce latency to under 35 ms for Valorant and under 70 ms for League of Legends in many regions.

link(https://greenwich.edu.vn/event/hn-company-visit-amazon-web-services/)