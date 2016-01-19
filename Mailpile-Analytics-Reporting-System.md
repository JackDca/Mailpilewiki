![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

The Mailpile Analytics Reporting System (MARS), reports back usage in an anonymized, nontrackable way. This provides us with some kind of idea of how many users Mailpile has, which version of the software they use and what language they speak. This can help us with Mailpile's fund-raising and promotion, as well as just generally making it easier to gauge our impact and make reasonable assumptions about where to focus our development activities.

Having this kind of information is useful, but we want to be very sure that were not violating people's privacy by doing this kind of collection. We can do this by collecting very small amounts of information that is not personally identifiable, but is sufficient to give us some kind of ideas of where we stand. As a design guideline, we want to achieve a similar level of anonymity as Tor does with its CollecTor system. 

We want to know:

 * How many people are using Mailpile
 * The Mailpile version number
 * The platform Mailpile runs on (operating system and Python version)

We do not want to know, or even be able to find out:

 * The user's IP address
 * The user's name, e-mail addresses, or other personal details
 * The user's exact geographic location
 * Any information about the user's Mailpile setup

## Implementation

### Collecting information

The metrics we would like to collect overlap almost perfectly with how we would like to target announcements to different groups of users; for example there is little or no reason to inform Mac OS X users about a security vulnerability which is specific to Linux, nor to bother Linux users with the information that new Windows packages are available for download.

This insight has led to the current implementation of MARS piggy-backing upon Mailpile's Message Of The Day (MOTD), which is how Mailpile downloads information about whether upgrades are available, or whether there is any critical news from the development team to the users of the software (such as warnings about software vulnerabilities).

The Message Of The Day is a simple JSON file, which is available from the following URLs:

   * https://www.mailpile.is/motd/latest/motd.json
   * https://www.mailpile.is/motd/[VERSION]-[OS]/motd.json

The first URL allows MOTD updates which carry no MARS information at all. The second allows more targeted announcements and allows the required metrics to be extracted from the web server logs. Additional variables for tracking (the Python version and the installed language) may be appended to the URL using the query-string.

The MOTD is updated roughly once per day, which gives us a lower bound for daily active users of the software.

### Avoiding collection of user data

Most of the data we wish to avoid collecting is afforded basic protection by simply not baking that information into the MOTD/MARS protocol.

The exception to this is the IP address is the only concern; it may indirectly reveal the user's location and worse, it facilitates tracking and correlation with other data sources, potentially leaking other information.

We therefore will make MOTD/MARS updates use Tor by default whenever possible, and that the updates be made opt-in when Tor is unavailable.

### Processing the data

Data will be processed by analyzing the Mailpile site's web server logs. Further details are TBD.

------------------------------------------------------------------------------------------------

# Original Proposal

**Note:** The original proposal for the Mailpile Analytics Reporting System is below. The actual implementation differs slightly (is simpler), but the original proposal is reproduced in its entirety below for posterity.

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