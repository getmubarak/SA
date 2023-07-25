### 1. Context
Twitter is a microblogging and social networking service on which users post and interact with messages known as "tweets". Users can follow other users and get tweet notifications from the users they follow. Tweets are short messages limited to 280 characters earlier the limit was 140 characters.

### 2. Functional Requirements 
- Users should be able to post a tweet.
- Users should be able to like a tweet posted by other users.
- Users should be able to follow or unfollow other users.
- Users should be able to view Timeline i.e. NewsFeed from other users they are following.

### 3. Quality Requirements 
- Let's assume the total users = 1 billion.
- Total monthly active users  (MAU) = 40 % = 400 million.
- Let's assume 25% of MAU as active daily, So daily active users (DAU) = 25% of 400 million  = 100 million per day.
- Let's assume that each user has 200 followers on average.
- Let's assume that 50% of users post one tweet every day (with or without a photo), so total tweets per day = 1 x 100 million   = 100 million.
- Total write transactions in day  = 100 million
- Since the read: write ratio is 80: 20, total read transactions = 100 million x 80/20 = 400 million.
- Thus total TPS (transactions per second) : 100 million + 400 million = 500 million per day ~ 6000 per second.
- Assume each tweet and its metadata takes about 1kb on average, so total storage for tweet per day is 100 million x 1k ~ 100 GB per day.
- Total photo storage for 10 years =  10 years x ~ 300 days a year x  100 GB per day ~ 300 TB. 
- Monthly upload bandwidth = 30 (days) x   100 GB (Tweets) ~ 3 TB per month.
- Monthly download bandwidth = 3 TB (Monthly Upload capacity)  x  80 / 20  = 12 TB per month.


### Reference
- https://www.codercrunch.com/design/1766092915/design-twitter
- http://highscalability.com/blog/2013/7/8/the-architecture-twitter-uses-to-deal-with-150m-active-users.html
- http://www.timdeboer.eu/paper_publishing/Twitter_An_Architectural_Review.pdf


### notes
- The Twitter 2010 dataset, is a social network of Twitter users, consisting of 12.71 million vertices and 0.23 billion edges.
- 
