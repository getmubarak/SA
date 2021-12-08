### 1. Context

A URL shortener is used to create a short alternative URL to a perhaps a long URL for a resource. Both the short and the long URL point to the same resource. 

Below is an example of a long URL and its short URL created using a bitly and tinyurl service:
Long URL: https://www.codercrunch.com/post/1763802291/build-a-stock-market-app-in-android

Bitly:  https://bit.ly/38h5WMU

TinyURL: https://tinyurl.com/wdk2fsw

Some of the benefits of the short URL are:
- Accuracy : 
A short URL is to promote accuracy. It is easier to simply copy, share in tweets, messages, etc. The short URL most like will not get truncated or somehow not get misinterpreted due to special characters or encoding differences when pasted or shared across apps or devices.

- Optimized for Space : 
A short URL occupies less space compared to a long URL. In some cases, the short URL is 80%+ shorter than the long URL. Service like the Twitter limit tweet to 140 chars, so having a short URL instead of a long URL in a tweet message is needed to save on the allowed character limit per message.

- Branding : 
Instead of using domain names associated with the shortening service with you can create a short URL associated with your domain names. This further ensures that the destination page is associated with the domain (brand) and the user will not be taken to any random or unknown location when they click the short URL.  e.g. the branded short URL for this page. 
Long URL: https://www.codercrunch.com/design/1270253716/design-a-url-shortner-service-like-bity-ly 
Short URL: https://www.codercrunch.com/_vx60gv

- Optimized for Apps : 
It is easy to encode a short URL into a QR code rather than using a long URL. Likewise, it is easier for applications to read and process short URLs rather than a complex long URL.

- Aesthetically Pleasing : 
Short URLs are easy on the eyes as they hide all the path and dynamic query parameters in the URL that are required by the landing page to function.

### 2. Functional Requirements 
- Users should be able to create a short URL by providing a long URL (anonymous or as authenticated user).
- Users should be able to view the original page by entering the short URL.
- Users should be able to specify an expiration time while creating the short URL(defaults to no expiration). Also, the system should be able to delete expired URLs.
- The service keeps track of URL usage and analytics.
- Bulk creation of short URLs via API 

### 3. Quality Requirements 

- Assume the total users = 100 million. 
- Let total monthly active users  (MAU) = 60 % = 60 million.
- Daily active users = 60 million / 30 days  = 2 million per day.
- Assuming that each user creates 2 links on an average, so total links created in a day= 2 x 2 million   = 4 million.
- Since the read: write ratio is 90: 10, total read transactions = 4 million x 90/10 = 36 million.
- Thus total TPS (transactions per second) : 4 million + 36 million = 40 million per day ~ 450 per second.
- Assume each short URL data is 1k, so total storage per day is 4 million x 1k ~ 4 GB per day ~ 120GB per month.
- Total links storage for 10 years =  10 years x  12 months a year x  120 GB per month ~ 14 TB.
- Assuming 1k entry per reading for storing analytics with a 90: 10 ratio, total storage per day  120GB x 90/10 ~ 1 TB per month.
- Total links storage for 10 years =  10 years x  12 months a year x  1 TB per month ~ 120 TB.
- Total capacity needed for 10 years = 14 TB + 120 TB = 134 TB
- Monthly upload bandwidth = 30 (days) x  ( 120 GB ) ~ 360 GB per month.
- Monthly download bandwidth = 360 GB (Monthly Upload capacity)  x  90 / 10  = 3240 GB per month ~ 4 TB per month.

### Reference
https://www.codercrunch.com/design/1270253716/design-a-url-shortner-service
https://github.com/puncsky/system-design-and-architecture/blob/master/en/84-designing-a-url-shortener.md
https://youtu.be/AVztRY77xxA
