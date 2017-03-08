What Developers Should Know About Email
---------------------------------------

_Nick Canzoneri, Laracon 2017_

### Introduction

A bit of reflection on email, Postmark, Mojang... 'everyone needs email'.

### What are MX Records?

From the domain in an email address, the server needs to figure out where the recipient is.
This, of course, requires DNS. A subdomain is treated completely separately from a primary
domain, and needs its own separate MX records to receive mail.

The records themselves consist of a server name and priority level (lower numbers are used
first). The process of priority selection (in reality) is a little fuzzy and not
cut-and-dried.

### How is An Email Formatted?

**Headers are metadata**

As an email gets relayed, each server that passes it on adds received headers.

- Where did it come from?
- Who has it now?
- Where is it going?

Google has a tool to parse received headers at toolbox.googleapps.com/apps/messageheader.

**X-Headers**

You can use these to add your own diagnostic info, and they are commonly used by spam
filters, etc.

**Body**

After the newline (\r\n), comes the body.

**HTML and Text**

Email content is split up by boundaries.

An HTML email should be a full HTML document, rather than just a snippet.

**SMTP Attachments**

These are also set off by boundaries.  Attachments are base64-encoded, which can increase
attachment size by up to 30%.

### Why Do I Need Email Authentication?

You can't trust everyone, and bad actors are attempting to compromise systems all the time.

**Methods: SPF, DKIM & DMARC**

These provide both _spoofing protection_ and _deliverability_. Your mail is more likely
to get there if it passes one of these.

**SPF: openspf.org**

- Whitelist and a blacklist (what's allowed, and what isn't)
- The receiver of the mail actually does the evaluation based on the Return-Path
- It looks at the spf record (+ is allowed, - is not allowed...)
- Domain-based

~~~~
v=spf1 ip4:192.30.252.0/22
    include:_spf.google.com     // these lines allow google and zendesk to send
    include:mail.zendesk.com    // from your domain;
    ~all                        // soft-fail: log a fail, but deliver it
~~~~

Gotchas:

- You can only have 1 DNS record for spf. If you have more, this will fail.
- There can only be 10 lookups. (Use less than 10 include statements.)

**DKIM (DomainKeys Identified Mail)**

Uses public-key encryption to add a crypto signature to your email. Message-based.

The public key is in DNS:

- google.\_domainkey.github.com (last bit is the signing domain)

This is put together with other info into an SMTP header that is sent with the mail.

**DMARC (Domain-based Message Authentication and Conformancy - RFC 7489**

The latest. The sender gets to decide criteria for pass or fail. The receiver decides
what to do on pass or fail.

In DNS: \_dmarc.gmail.com

~~~~
v=DMARC1\;
p=none\;        // policy
rua=mailto:mailauth-reports@google.com // reporting email address, reports are XML
~~~~

dmarc.postmarkapp.com - can send a weekly review / digest of DMARC email reports

### How Do I Get More Features Out of Email?

- Bounces       // don't build any of this yourself
- Opens
- Inbound

Postmark, SES and others provide these additional features in a nice API.

**Bounces**

The email you sent didn't get delivered. This is an important stat to track (make sure 
people are getting your mail.) You can set up custom features via X-Headers.

**Opens**

Don't use receipt tracking in creepy ways. Use it for stats, responsibly. :)

Example feature: HelpScout (lets you know when customer viewed a support email)

**Inbound Processing**

Turn email into JSON. So your customer can email your app. Of course this is complex.
The classic example of this is project management tools (BaseCamp, JIRA).

Again, dmarc.postmarkapp.com uses this Inbound feature to build its reports.

### Questions

**Q. What about PGP?**

A. Not really dealt with at Postmark.

**Q. When sending email on behalf of customers, and you can only send Reply-To...?**

A. If you can't touch the DNS records, the only option is to use a Reply-To. Email
reputation is getting more and more domain-based.

**Q. Is Open tracking reliable, or no?**

A. The common tactic is to use an image... it is not 100% reliable. Depending on the
receiver, you may not get as much information (about open client / browser, etc.).
Gmail typically handles Open tracking well, and they are the gorilla in the room in
terms of email market share.
