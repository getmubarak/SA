### 1. Context
Design a simple model of Facebook where people can add other people as friends. In addition, where people can post messages and that messages are visible on their friend's page. The design should be such that it can handle 10M of people. There may be, on an average 100 friends each person has. Every day each person posts around 10 messages on an average.


### 2. Functional Requirements
A user can create their own profile.
A user can add other users to his friend list.
Users can post messages to their timeline.
The system should display posts of friends to the display board/timeline.
People can like a post.
People can share their friends post to their own display board/timeline.

### 3. Quality Requirements
- Total number of distinct users / nodes: 10 million
- Total number of distinct friend’s relationship / edges in the graph: 100 * 10 million
- Number of messages posted by a single user per day: 10
- Total number of messages posted by the whole network per day: 10 * 10 million
-  Approximate some 1000 request are posted per second.
-   Let us suppose each user is using Facebook for 5 to 6 years, so the total number of posts that a user had
produced in this whole time is approximately 20,000 million or 20 billion.
- Let us suppose each message consists of 100 words or 500 characters. Let us assume each character take 2 bytes. 
- Total memory required = 20 * 500 * 2 billion bytes.
= 20,000 billion bytes
= 20, 000 GB
= 20 TB
1 gigabyte (GB) = 1 billion bytes
1000 gigabytes (GB) = 1 Terabytes 


### Reference
https://leetcode.com/discuss/interview-question/system-design/719253/design-facebook-system-design-interview
https://youtu.be/9-hjBGxuiEs
https://liuzhenglaichn.gitbook.io/system-design/news-feed/design-a-news-feed-system
