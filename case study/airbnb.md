### 1. Context
Airbnb is a online platform which associates individuals who need to rent out their houses through individuals who are looking for lodgings and rooms in a place/city.
Estimated revenue of $900 million.

### 2. Functional Requirements

  1.	A host lists his/her property details along with pricing, rules, facilities available, etc sometimes even with nearby attractions
	2.	A user that is looking for a vacation rental searches for a place on the app. They typically set destination, zip codes, a search radius, price range and other preferences
	3.	Upon finding a perfect place to rent, a user then makes a booking request
	4.	The host receives a booking request and then decides whether to approve or not
	5.	If a host approves a request, an amount gets deducted from user’s bank via a payment gateway
	6.	After the stay, the payment automatically gets paid to the host
	7.	User and host both review each other

#### Search
There are more users that will use “search” than users that will make a booking. You will have to make sure that the search is performant, but at the same time, you will have to ensure that the failure of search services doesn’t take down your app’s booking functionality. Booking is a core feature, and if I were to prioritize, I would prioritize this above everything else.

#### Trip Cancellation at the last minute
	•	Adding a cancellation fee: For every last minute cancellation by the host, the host will incur a cancellation fee.
	•	Add # of cancellations to the profile of host: This will not only just improve the trustworthiness of a legit host but would also force them to only cancel a booking when they absolutely have no other options
	•	Block calendar dates for last minute cancellations: Another way in which on-demand rental apps have been dealing with last minute cancellations is by blocking the calendar dates for which last-minute cancellations happened. By doing so, the host won’t be able to leverage surge pricing at the last moment and will be blocked from accepting any request on those dates.
  •	provide a rental place nearby as an alternative when a host cancels out of the blue.

#### User support, tickets and customer service
SDKs such as Intercom and Zendesk will allow your customers to directly connect with your customer’s support team. The best part is these SDKs made it available by eliminating any type of needs for these shady webviews and email clients. Both Intercom and Zendesk can be customized on the basis of design needs for your user needs.

#### Processing Payment 
If you want to integrate a payment functionality in your app, then there are many payment gateways.
<br>
Stripe – Once in place, Stripe processes the payment through its own servers – so you don’t have to store sensitive data or worry about being PCI compliant – takes a small percentage and checks for fraud. Stripe accepts all major debit and credit cards from more than 135 currencies. It charges a fixed $.30 fee + 2.9% of every transaction that is made on a card or digital wallet.
<br>
Braintree – Braintree also accepts all major debit and credit cards from more than 130 currencies. Similar to Stripe, Braintree also charges 2.9% + $0.3 per transaction that is made on a card or digital wallet. Braintree also provides a split payment functionality which is an absolute necessity when a group of people are using the same service ex: sharing a rental space in the case of Airbnb

#### identity verification
You can simply use providers like Jumio, Shufti Pro, Onfido, etc to enable automatic identity verification within your app.
<br>
Providers | Facial Recognition | Document Verification | Tampered document detection | Internal database of verified users
<br> ---------- <br>
Onfido
Shufti Pro
Jurnio

#### push notifications
you may need to notify a host about the latest booking on his account or a recent message by Guest.A push notification service should be scalable enough to send push notifications to over millions of subscribers. Urban Airship could be used for push notifications

#### In-app chat functionality between Hosts and Guests
Whenever a guest books a house with an app like Airbnb, he would like to message the host either to say an informal ‘hi’ or to converse something important about his trip. Or a host may want to converse with his guest to know more details about him. This requires a real-time in-app messaging feature so that both guests and hosts can converse and chat with each other.

Well, to build a real-time in-app chat feature in an app like Airbnb, you can use
	•	Socket Programming
	•	Backend as a Service (Firebase)
	•	Third party SDKs or APIs such as Layer and Twilio

#### Dealing with Payment scam and Fake Listings

Duplicate listings with different addresses
Fake listing photos and/or address
Fake reviews by friends or family of host
An illegal listing that causes problems for guests
Host demanding extra cash
Host blackmailing guests
Host falsifies damages
Demands offsite payment
Fake scam emails
Host blackmails guest for a good review
Fake reviews by friends or family of host

Machine learning algorithms can be used to detect fake listings before they ever go live on platform. The technology can actually evaluate fake listings on the basis of several of hundreds of risk signals such as host reputation, template messaging, duplicate photos and other discrepancies. When a listing is predicted as fake by these algorithms, it can be blocked immediately before appearing on the app.



### 3. Define Non-Functional Requirements 
- Over 100 million users.
- 640,000 hosts
- Around 2 million listings
- 500,000 guests stay per night via Airbnb
- 191 countries and 65,000 cities Airbnb are actively involved in.
- Total monthly active users  (MAU) = 40 % 100 million = 40 million
- Let's assume 25% of MAU as active daily, So daily active users (DAU) = 25% of 40 million = 10 million per day.


### Reference
- https://hackernoon.com/how-to-build-an-app-like-airbnb-405f3c5872f9
- https://medium.com/@himanishaik48/airbnb-system-design-most-frequently-asked-question-in-technical-interviews-262e999f437
- https://enqueuezero.com/airbnb-architecture.html
- https://www.codekarle.com/system-design/Airbnb-system-design.html
- https://github.com/puncsky/system-design-and-architecture/blob/master/en/177-designing-Airbnb-or-a-hotel-booking-system.md






