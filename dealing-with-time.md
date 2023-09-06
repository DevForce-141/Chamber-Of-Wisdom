### Source: https://www.tinybird.co/docs/guides/best-practices-for-timestamps.html#the-10-datetime-commandments-title
Best Practices for Timestamps and Time Zones
In this guide you’ll learn Best Practices for Timestamps and Time Zones in Tinybird

## The 10 DateTime Commandments
The old joke goes that there are only two hard things in Computer Science: naming variables and cache invalidation and off-by-one errors. But it is my contention that nothing makes experienced developers want to delegate more than reworking the DateTime handlers in the analytics codebase.

Of course, the real problem is that it is both annoying to solve correctly and doesn’t add any net value to the service you are building - you just need it to work so you can report accurately.

Well, fear not, for this is a guide on best practices for working with DateTime and Timezones in your data processing and analytics systems. This guide is all about general best practices, with a follow-on ‘Working with Time’ guide on specific functions for different tasks and pre-written queries and test data for common use cases.

So without further ado, let’s get started with the 10 DateTime Commandments.

## Thou shalt default to UTC
“Simplicity is the ultimate form of sophistication; it saves time and prevents unnecessary complications.” - Leonardo da Vinci

No matter what the timezone of the server where an event is created, when you store it for later analysis you will first convert the timestamp into UTC. You will ideally also keep the relevant timezone offset in seconds, the name of the timezone, and possibly the local timestamp as a string - but always, always, make your primary storage of a historical event timestamps in UTC.

Why? UTC is an absolute point in time because it is based on a highly accurate and stable standard that is recognized and used worldwide. It is not affected by changes in timezones or other adjustments at the whim of the latest local government.

Ultimately, UTC is always constant - every other modern time is relative.

Note that we specifically call out historical timestamps here - because you are storing information about something that has already occurred. When describing something that might happen in the future, like scheduling an event, you should consider the appropriate context relative to the information - UTC is great for coordinating something to happen at exactly the same moment across many timezones, but if you want everyone to do something at 0900 in their own local time - then that is the better way to schedule it.

## Thou shalt only do that which is necessary
“Too much analysis leads to paralysis.” - Robert Herjavec

It can be tempting to process timestamp data in a way that seems comprehensive or exhaustive, but this can lead to wasted effort and unnecessary complexity. This is why you should only do what is needed to serve the use case in front of you.

It is important to clearly define the use case and determine what specific precision, calculations, or aggregations are necessary to support it. If you’re not sure, use the recommended defaults and then stray only as needed.

This is also part of why we store data in UTC with metadata about where it came from - it is already in a common standard and usually only one aggregation and/or conversion away from how our users want to see it represented. It is why we default to storing DateTime with accuracy in seconds (rather than milliseconds), and monetary values in cents as Integers rather than Floats - there are known to be fewer edge cases and more standard functions work with them out-of-the-box, reducing your complexity and test footprint.

This is not to say that you shouldn’t use millisecond and cent-fraction accuracy if you really need it (hello AdTech), but being fancy for its own sake just makes more work. This also means if you are finding that a great many use cases require the data to be in a particular representation, for example if all of your business analysts work out of a particular timezone and always view data relative to that timezone - then also precalculating the data to that timezone for efficiency is perfectly reasonable and a good idea, but you should still canonically store it in UTC.

## Thou shalt be unambiguous
“Ambiguity is the enemy of clarity.” - John F. Kennedy

This means you should always display timestamps in standard formats, specifically ISO8601 formats by default, and only use regional formats (I’m looking at you Americans and your mm-dd-YYYY travesty) if absolutely necessary.

Why? Because a user should never have to guess what a timestamp represents. Any plain timestamp presented in a UI will be assumed to be local time, and any plain timestamp stored in a database is assumed to be UTC - if it is anything different you absolutely should be providing the timezone information with it, or explaining it in metadata.

There are good technical reasons for this also - such as efficient sorting and indexing by date in these standard formats, but also most people assume UTC by default (Commandment the First), and it is also the least confusing way to present data to users.

More generally, avoiding ambiguity means using the right data Type for the right purpose, and thus leads us to the fourth commandment…

## Thou shalt not cross thy Types
“Don’t. Cross. The Streams.” - Egon Spengler

This means use the Type that matches your data, and don’t compare across dissimilar Types. Use a Date when you have a Date, and a DateTime when it’s a Date and a Time, etc. This might seem glaringly obvious but we see people still making this mistake with great regularity.

Remember: a Date refers to the period of a calendar day. A DateTime refers to a specific point in time which might be in a particular Day in one timezone, and another day elsewhere.

Why? If you store a Date as a DateTime with some arbitrary time of day selected (most systems would set it to midnight or midday by default), and it is then converted to a Timezone with a sufficiently large offset (like New Zealand in +13), it could be a completely different calendar day and thus not match the Date you were expecting.

And it’s similar when naively comparing across other Types.

Don’t do it.

## Thou shalt know whence you came
“You can’t know where you’re going until you know where you’ve been.” - James Burke

In order to compare things across different time zones, you usually need to know which timezone you started with. Generally for smaller datasets you would store this in an additional column (see Commandment the First), but for larger datasets it is typical to have a Fact table with all your time stamped data, and one of your Dimension tables being the relevant location and timezone information, as this avoids bloating your Fact table with redundant data.

Most of the time we are processing data from sources that don’t move around much, like physical stores, or digital services that typically operate in UTC for simplicity, like online shopping. Truly mobile data sources like ships and airplanes have highly sophisticated time and positioning systems and will also usually give you data in UTC.

See how putting data in UTC is so handy? That’s why it’s the first commandment.

Ultimately many analytics queries require you to know where the data came from (event timezone), how it is stored right now (UTC, right?), and where it is going (query timezone) - when then naturally leads us to…

## Thou shalt transform carefully
“A child learns that a day is 24hrs long, a developer needs to know that is only true in UTC.” - Me, probably

When naively converting between time zones it is very easy to get tripped up by Timezone or DST changes - resulting in data that is only correct for ~363 days per year, or always mistakenly 30mins offset for one region. Part of the problem of working with timestamp data is wrong and right can look remarkably similar, and it is easy to make mistakes.

This is particularly true when aggregating over longer time periods - when aggregating by day you must first consider - which relative day do I care about? Each local site? Some global service centre? Maybe you want to normalize all the data relative to the business day across locations, so you can compare intersite event patterns on an hourly basis?

In some cases, the tradeoff of simplicity in data processing against strict accuracy is acceptable for the use case (remember the 2nd Commandment), but if you are going for accuracy then you should try to do all your conversions either at the very beginning or very end of your processing based on your required output, or in such a way that it doesn’t cause problems.

Currently, all active DST changes and Timezone offsets are a multiple of 15 mins, so it is therefore the largest period you can pre-aggregate by that won’t need complex adjustments at query time - this is very handy and leveraged by many tools, we’ll show you how to do it yourself in the companion guide ‘Working with Time’.

## Thou shalt understand Timezone relationships
“It’s no use going back to yesterday, because I was a different person then.” - Lewis Carroll

It is important to understand how timezones and offsets relate to each other. Timezones are labels used by governments that change infrequently, like ‘Pacific/Tokyo’. Sometimes a timezone is retired, changed, or a new one introduced.

Any given timezone will have one or more offsets. An offset is usually expressed in seconds in programming and represents the number of seconds that the timezone is ahead or behind of UTC for a given period of a given year. These can also be changed in modern times, and have historically been very weird indeed (The Netherlands was +19:32:13, yes nineteen and a bit minutes, for nearly 28 years).

Generally, it is good to think of timezones and offsets as a collection of 1:N relationships which slowly morph over time, and that when you fetch the offset for a timestamp it will be given for that particular point in time and may not apply to all other timestamps for that timezone in that dataset.

Most systems, including the one we use in Tinybird, are based on the IANA Time Zone Database which is scrupulously kept up to date - this means the native conversion functions within the system are probably better at it than your custom solution, which brings us to…

## Thou shalt trust the computer
“Don’t reinvent the wheel, just realign it.” - Anthony J. D’Angelo

You should default to using the system provided conversion functions, if possible. One of the often lauded positives of Open Source Software is that you get the benefit of usually a very large number of people using the same functionality and therefore detecting and fixing bugs (hopefully) long before you encounter them. One of the downsides of OSS is sometimes the system has very strong opinions about how you should do things which get in your way, so we have to also have carefully considered workarounds to achieve the outcome we need.

For example, ClickHouse (and therefore Tinybird) has a large collection of functions for common tasks like interpreting different kinds of Strings into a DateTime (parseDateTimeBestEffort), converting toDateTime (including changing Timezone), handling different levels of precision (now() vs now64()), or writing a timestamp to many specific kinds of String formats (formatDateTime).

What it also has is a requirement that a Timezone is associated with DateTime fields at the column level, preventing mixed timezone data from being stored together in this native format. In our companion guide we show you ways to achieve typical analytics use cases while working with this constraint, and the impact it has on other queries you may run.

So the computer will probably do what you want, but it might have constraints or opinions that you need to work around, so…

## Thou shalt also verify the computer
“In God we trust; all others must bring data.” - W. Edwards Deming

Even if you use the system provided Date and DateTime manipulation functions, and especially if you write your own, you should have golden test data and tests for your specific use cases as a part of your analytics platform.

An upgrade could change a default you unknowingly relied upon, an administrator might accidentally change the server from UTC to BST for a total of 13 minutes on some idle Tuesday morning, a new version of a tool could introduce a new optional positional parameter to a function you use and it now silently gives a different answer in 3% of your data.

In our companion guide , we’ll provide an overview of the different functions you can use along with examples of test data using the extremely tricky Pacific/Chatham timezone, and how you can run various kinds of checks using it.

## Thou shalt put this into practice
If you’ve read this far, then cement your understanding by running through the practical examples in the companion guide. They’re implemented in Tinybird, which uses standard SQL running on ClickHouse. It’s free, and instant, to sign up and the example data and queries are already there for you to review. Have fun!
