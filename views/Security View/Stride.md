Stride in Detail
Overview
The following sections look at the different STRIDE hint categories, and some suggested items to discuss for each category. These points are not exclusive lists, but instead ideas to start relevant discussion.

The STRIDE book has even more items in each category - or see the “Elevation of Privilege Game” card deck.

Spoofing
This hint encourages the analysis team to think about whether an attacker can pretend to be:

an unauthenticated user
an authenticated user
a trusted partner system
another component of the system
a database or filesystem relied on by the system
etc
Of course, anyone can pretend to be an “unauthenticated user” - the question here is more “are unauthenticated users correctly limited?”.

This topic is tightly linked to the concept of authentication - how does the system know who is interacting with it?

Example questions include:

Does the component being evaluated read or write files? If so, is the identity of that filesystem verified?
Does the component read config settings on startup? If so, is the identity of that data provider verified?
Does the component listen on a network connection? If so:
How does the system ensure that no other application opens a connection on that same port first?
Can an attacker redirect the DNS name for the system to their own address (spoof that component)?
Does the component provide a server-side certificate? If so, is it properly signed (trustable by clients)?
Does the component open network sockets to external services (including local ones)? If so, how does it validate that the target is trustworthy?
Does the component accept requests from other components? If so:
How does it verify the identity of the sender of each request?
Is that verification vulnerable to programmer errors (eg requires code in every rest endpoint)?
Are multiple kinds of authentication supported? If so, are all equally secure?
Are there “bypasses” to authentication (eg account-recovery, “authenticated by callcenter”, etc)?
Are credentials persisted client-side? If so, is that appropriate and are users aware of their responsibilities?
Are requests accepted on alternate ports with different authentication rules?
Is there any rate-limit to attempts to present different credentials?
Does the component support remote administration? If so, are remote administrator credentials properly verified?
Where a risk of implementation error is identified, the mitigation should not be just “fix line 17 of file X” or “be more careful”, but instead define a method that prevents or catches the problems. Examples include using a framework where authentication is “verified by default” so that every new entrypoint added during implementation automatically gets maximum protection (possibly to the point of being useless); where less protection is needed then the developer must actively enable that. Automated report generation and automated security tests are also good solutions.

Tampering
This hint encourages the analysis team to think about whether an attacker can modify data or bypass authorization:

Does the component being evaluated read files? If so, is access to that filesystem properly controlled?
Does the component being evaluated write files? If so, can they be modified by something else after writing?
Does the component read config settings on startup? If so, is access to that info properly controlled?
Can data in a database be modified directly, bypassing the system checks?
Is the system relying on data in the client which can be modified?
Where encryption is used, are standard algorithms used and is the implementer or reviewer suitably qualified?
Is the mapping from authentication info (id) to authentication info (rights) secure and reliable?
Does the implementation consistently check authorization? Is the approach vulnerable to programmer errors (eg requires code in every rest endpoint)?
Can URL parameters be modified by clients with unexpected results?
Are “replay attacks” possible, eg an encrypted or signed message from an authenticated user be intercepted and sent again?
Can data be modified after validation but before use?
If running this system in the cloud, then you might also need to think about which other users might be running software on the same physical host, and whether that raises tampering (or info-disclosure or denial-of-service) risks.

Repudiation
This hint encourages the analysis team to think about whether an attacker can perform an operation untraceably. As noted earlier, this is tightly linked to authentication:

Are there any important operations which are accessible to anonymous users?
Are all important operations logged, with the requester’s id?
Will requests still be processed when logs cannot be written (eg due to out-of-disk-space)?
Can logging be truncated by forcing too many log messages to be output in a short time?
Can logging be corrupted by passing weird strings which end up in log messages?
Is the logged information sufficient to describe exactly what operation the (identified) user performed?
Can you tell if a logfile has been deleted?
Can you tell if a logfile has been altered?
Information Disclosure
This hint encourages the analysis team to think about what information an attacker may be able to obtain which was not intended:

Is network traffic encrypted where appropriate?
Are man-in-the-middle attacks blocked?
Are files written by the component? If so:
Is access to that filesystem properly controlled?
Are digital signatures used to detect modification?
Are backups properly secured?
Do URLs include sensitive data useful for an observer?
Do error messages include sensitive data?
Do file-listings or similar results reveal information about the existence of other data, even when the content is not available?
Do logfiles (or logservers) reveal sensitive information?
Where encryption is used, are standard algorithms used and is the implementer or reviewer suitably qualified?
Do responses to requests leak information in any circumstances?
A somewhat related topic is “reconnaissance protection”, ie ensuring all components exposed to attackers reveal as little info as possible. While security-through-obscurity is not a good approach, it is nevertheless useful to deny attackers easy access to information about the system being protected. Thought should be given to removing product-version-strings from standard responses, using firewalls to block port-mapping, etc.

Denial of Service
This hint encourages the analysis team to think about the business value of “uptime” for the system, and what an attacker might possibly do to reduce system uptime:

Can filesystems or databases be filled up via malicious requests?
Are there specific requests that will take large amounts of time to process? If so, are there limits on the number of such requests?
Can an attacker register a DNS name to interfere with the system?
Does the system rely on external servers which are more vulnerable than the system being analysed?
Can a user be “locked out” by deliberately making requests with incorrect authentication information?
And the usual “flood the network” attacks of course (if relevant) - including whether logging is sufficient to identify the source
This hint is also a good point to think about various natural disasters, from floods and earthquakes to cleaners unplugging servers by accident.

Escalation of Privilege
This hint encourages the analysis team to think about whether an attacker might be able to provide their own logic which is executed with rights associated with some other user.

Does the system include third-party executable content in its responses, leading client to process third-party content with the trust associated with the system being analysed (eg cross-site-scripting (XSS) attacks)?
If the implementation uses an interpreted language such as Python, Perl or PHP, then what development processes are in place to ensure that developers do not use “eval” or similar methods?
Do requests include references to data in external stores? If so, can an attacker modify the referenced data and thus execute requests as the original user but with data controlled by them?
Can the component’s code (including required libraries) be modified or substituted?
