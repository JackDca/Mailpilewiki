This is a proposal for a Mailpile Analytics Reporting System, which reports back usage in an anonymized, nontrackable way. This provides us with some kind of idea of how many users Mailpile has and where, roughly, they are. This can help us with Mailpile's fundraising and promotion, as well as just generally making it easier to gauge our impact and make reasonable assumptions about where to focus our development acivities. Having this kind of information is useful, but we want to be very sure that were not violating people's privacy by doing this kind of collection. We can do this by collecting very small amounts of information that is not personally identifiable, but is sufficient to give us some kind of ideas of where we stand. As a design guideline, we want to acheive a similar level of anonymity as Tor does with its CollecTor system. 

We want to know:

 * How many people are using Mailpile
 * Rough bucketed information about where they are
 * An estimation of whether they are continuous users or new users
 * The Mailpile version number

We do not want to know, or even be able to find out:

 * The user's IP address
 * The user's name, e-mail addresses, or other personal details
 * The user's exact geographic location
 * Any information about the user's Mailpile setup


## Setup

On setup, Mailpile stores information about its creation datetime.

This can at any time be changed by the user in order to alter her reporting identity. This should not be valuable to users in practice though, as the reporting data is not very granular.

## Reporting

On a random interval of between 3 and 10 days, Mailpile will connect to a Tor hidden service operated by the Mailpile Team. It is a hidden service to guarantee that it cannot be accessed without using the Tor network, and to reduce the ability of Exit nodes to harvest raw data or expose user identities.

To this service, it will send a data structure containing the following:

 * round(log10(install_age))
 * The country code as determined by GeoIP's Geolite2.
 * Mailpile's version number

A HTTP GET request is made:
     http://tracker/?ia=100&cc=IS&v=0.1.5

The response is a PGP signed JSON structure which can tell the user useful stuff, such as news from us, info about available updates, and other helpful things the team may want to communicate to users. May as well use the request if we're generating traffic. 

This structure will contain certain fixed fields, but also a optional fields which will be specified later.

    {
        "reportback_next": 0, 
        "reportback_server": "https://xxxxxxxxx.onion",
        "news": "",
        "newest_version": "0.1.0",
    }
    
 * reportback_next: Number of seconds until next reportback. Clients should add up to 3 days of randomness to this value.
 * reportback_server: The server to report to next.
 * news: Textual updates from Mailpile Team.
 * newest_version: The newest released version of Mailpile.

Well, if we have a billion users, according to the default 3-10 day window, we're expecting about 1653 requests per second. I think if we ever get to the point where we have a billion users we can handle that rate, but the reportback_next option allows us to space that out more if necessary.

## Display

Mailpile operates an analytics.mailpile.is website, which displays usage graphs and other data. It floors reported data to the closest 10, so as not to show that Mailpile usage in certain countries is lower than 10, as this may expose social graph information. Obviously the flaw here is that Mailpile is actually receiving more granular data than is being displayed, but not by much.

## Draft implementation

 * Draft implementation available at https://github.com/mailpile/analytics