### 1.Context
Instagram is a social media app that allows users to share photos and videos with others. The visibility of these posts (photos/videos) can be set as private and public by the original poster. Users can like posts and comment on the posts. Users can follow other users and view their News Feed (a collection of posts from the users they are following).
Also, users can search for posts across the platform. Instagram also supports other features like image editing, Location tagging, Private messaging, Push notifications, Group messaging, Hashtags, Filters and more.
Instagram reported 500+ million daily active Stories users worldwide, Stories is a feature of the app allowing users post photo and video sequences that disappear 24 hours after being posted. Most of the interactions on the platform happen through the Android and iOS mobile apps instead of their website.

### 2. Functional Requirements 
- Users should be able to upload their photos and videos.
- Users should be able to like a post posted by other users.
- Users should be able to follow other users.
- Users should be able to view NewsFeed from other users they are following.

### 3. Quality Requirements

- Assume the total users = 100 million. 
- Let total monthly active users  (MAU) = 60 % = 60 million.
- Daily active users = 60million / 30 days  = 2million per day.
- Assuming that each user uploads 2 photos on average, so total photos upload in day = 2 x 2million   = 4 million.
- Assuming that each user uploads 0.5 videos on average, so total videos upload in day = 0.5 x 2million = 1million.
- Total write transactions in day  = 4 million photos + 1 million videos = 5 million.
- Since the read: write ratio is 80: 20, total read transactions = 5 million x 80/20 = 20 million.
- Thus total TPS (transactions per second) : 5 million + 20 million = 25 million per day ~ 300 per second.
- Assume each photo is 200kb, so total storage for photos per day is 4 million x 200k ~ 800 GB per day.
- Total photo storage for 10 years =  10 years x ~ 300 days a year x  800 GB per day ~ 2,400 TB ~ 3 PB
- Assume each video is 50MB, so total storage for videos per day is 1million  x 50MB ~ 50 TB per day. 
- Total video storage for 10 years =  10 years x ~ 300 days a year x  50 TB  per day ~ 150 PB. 
- Total Storage = 3 PB (Photos) + 150 PB (Videos) = 153 PB in next 10 years
- Monthly upload bandwidth = 30 (days) x  (  3 TB (Photos) + 50 TB( Vidoes)) ~ 1.6 PB ~ 2PB per month.
- Monthly download bandwidth = 2PB  (Monthly Upload capacity)  x  80 / 20  = 8 PB per month.

### Reference
- https://www.codercrunch.com/design/634265/designing-instagram
- https://youtu.be/9-hjBGxuiEs
- https://www.educative.io/courses/grokking-the-system-design-interview/m2yDVZnQ8lG
