---
title: "How Notion handles data ?"
datePublished: Fri Jun 20 2025 14:23:24 GMT+0000 (Coordinated Universal Time)
cuid: cmc4wh0cd000102jm2l9wd4g9
slug: notion-system-design
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745415381899/492d79e1-0929-437e-9e73-06d3b292b544.avif
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1750429373856/ad381f6a-e294-4f74-a209-070f5caae804.avif
tags: blog, blogging, software-development, system-design, notion

---

### About Notion -

**Notion** is one of the world’s fastest growing user application . It was created by **Ivan Zhao** , a cognitive science graduate at University of British Columbia Currently , releasing its first version {Notion 1.0} in **August 2016** ,it soon became a sensation and people started referring it as a “**Second Brain**“. In 2025 , it has a large user base of 100 million. It is now a widely used tool for project management and workflows , .But how does it handles trillions lines of data is still a matter of amazement. Let’s find out :-

### Building Blocks -

Whenever we open a new Notion Page , each block is rendered in a separate database row, for example -

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745416218208/a49caa4d-4c0d-4dfb-b75a-c0e1bbc6f509.png align="center")

Here in the above picture you may see that a single block is backed with certain unique ids , parents ids etc.

Till 2021 , Notion was using a **single Postgres database** because it has a small user base of 1K users only . But soon as it touches its data limit , they started looking for something better and stable .

Notion implemented **Sharding** to handle the queries and traffic. ***Sharding is splitting your data across multiple “Databases“ so that no single one gets overwhelmed.*** Let’s understand sharding using an example - Imagine you run a super popular pizza delivery service. Business is booming, and now you have **too many orders** for just one kitchen to handle. So, what do you do? You **split** the work between multiple kitchens. Each kitchen handles orders for a different part of the city. This way, things move faster, and nothing gets overloaded.

What Notion does is it splits the data based on the “**Workspace-id”** , means for every new workspace a new sharding layer is implemented .So now because all the data of a single workspace is at a single place , performance increased .Notion keeps a **map** of which workspace lives on which shard. So when you open a workspace , they know exactly where to fetch you data from .

Practically , they used 32 Postgres databases all hosting 15 shards within them (480 logical shards). So to add them , Notion used something called **Double Write Method** . Double Write Method is a strategy used when **migrating from an old database to a new one**, especially in **live systems** where **downtime is not acceptable** (like Uber's ride platform). The idea is to **Write to both the old and new databases at the same time**, but **read from only one** until the new system is ready.

So , first Notion writes the data to both the new DB and the old DB (At this stage the read is still only done through old DB ). Later they used **96 CPUs** to sync the data between them. Refer to the following figure -

![Double Write Method](https://cdn.hashnode.com/res/hashnode/image/upload/v1745418387215/d9cf133d-c343-44c5-ac6a-63a698ed298b.png align="center")

Currently at this stage , their architecture was , **that first data is being extracted from the Postgres shards then the data is put into a snowflake datalake and after this it is transformed into a Notion page.**

But what is now a **Datalake** ? **Datalake is like a repository where the developers put a large amount of unorganized raw data for later analytics** . But for Notion there was a issue , as initially Notion was using snowflake’s datalake ,it was helpful for inserting new data , but not for updating old data .For any note taking application like Notion , there are tremendous amount of updates every time. So now Notion must do something fast as its user base is doubling every 6 months …

Notion decided to built its own datalake . The goal was to create a system that can : **Store raw and processed data, Work quickly with update -heavy block data and Utilize modern features like AI and search.**

The following architecture was implemented -

![Source : https://www.youtube.com/watch?v=NwZ26lxl8wU&list=PLFfiqW2vNuFMAlhQ3BLu3tOLG2d0W2Elp](https://cdn.hashnode.com/res/hashnode/image/upload/v1745419966477/2d7d8b88-a3c3-4e30-9fed-09f8f298ba29.png align="center")

Let’s breakdown each thing step by step -

**Amazon S3** - was the datalake that Notion used , that way they can use Elastic search , Vector databases (Databases which helps in search based on meaning not words) and key value store .

**Spark** - used to handle large computational queries

**Kafka** - to send large amount of data consistently

**CDC (Change data capture)** - this brings the updated data from Postgres to Kafka.

If you would have noticed the huge part of the above components is open- sourced which saved much dollars for Notion and helped it to keep up with increasing user base .

### After 2 years -

This worked great until 2 years later , Pg Bouncer was reporting 90%+ data capacity .Know the engineers have to go back to square one , the tripled the 32 dB machines to 96 machines , So now 15 Shards per machine will go down to 5 Shards per machine . They first copied all the data using postgres replication logic and yes they were up again .

**How does it work -**

First the data collected will be divided into the groups according to the dB they have to be sent , and then the PG bouncer will distribute the data among multiple databases.

But for single PG bouncer to handle such a data was not possible , so they increased the PG bouncer to 4 , each one will handle 24 databases

**Time for transition -**

They used “**Darkread- a technique used during data migration or new feature rollouts where data is read from both the old and new locations simultaneously to verify correctness and performance**” to see that DB can handle the same request and does not lost any data.

Now they have four things to confirm before the deployment of each database

1- Stop accepting connections

2- Verify new database hasn’t lost any data

3- Update pgBouncer to point new databases

4- Resume traffic to new shards .

And that’s it Notion is now completely ready to handle vast data easily .

Thank you for reading this blog , Please share your thoughts over this , its my first blog and will definitely work on your feedbacks .Here are my credentials -

X - [https://x.com/ParthSohaney04](https://x.com/ParthSohaney04)

LinkedIn - [https://www.linkedin.com/in/parthsohaney/](https://www.linkedin.com/in/parthsohaney/)