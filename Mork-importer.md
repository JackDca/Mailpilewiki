**Mork** is perhaps the most insane file format ever invented. [That's saying a lot](http://origin.arstechnica.com/news.media/200/ooxml_2.jpg). It's used in Thunderbird and some other applications as a datastore, often for contacts. In Thunderbird, your two address books (managed addresses + collected addresses) is typically available in ~/.(thunderbird|icedove)/*.default/(abook|history).mab

Our importer is based on Demork by Mike Hoye, which in turn is based on Mindy by Kumaran Santhanam. 

To understand the insanity that is Mork, read these:
* http://www-archive.mozilla.org/mailnews/arch/mork/primer.txt
* http://www.jwz.org/blog/2004/03/when-the-database-worms-eat-into-your-brain/
