---
layout: post
title: Should Toronto Increase the 30-Minute Time Limit on Bike Share Rides? An analysis of Bike Share Toronto 2018 Data Using Tableau
---

I have become a big fan of Bike Share Toronto after recently moving to the city. The annual membership I purchased allows unlimited 30-minute bike trips for only $90 a year, but you can also purchase a Day Pass for $7 as a casual member. The catch is that if your bike trip exceeds 30 minutes you get charged extra fees. 

A few days ago, I read [an article in The Star](https://www.thestar.com/opinion/contributors/2020/08/24/bike-share-toronto-is-finally-thriving-and-those-electric-bikes-are-awesome-too.html) about how the bike sharing service is thriving in Toronto. At the end of the article, the writer requested the city to increase the time limit for bike trips to 45 minutes arguing the 30 minutes time limit "is starting to feel real tight". The request even got the attention of Toronto City Councillor James Pasternak who tweeted that he would be happy to discuss the proposal this and bring it to his committee.


{::options parse_block_html="false" /}

<div class="center">

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I think this is something we should discuss. I’d be happy to bring it to my committee. <a href="https://twitter.com/BikeShareTO?ref_src=twsrc%5Etfw">@BikeShareTO</a> <a href="https://twitter.com/hashtag/Toronto?src=hash&amp;ref_src=twsrc%5Etfw">#Toronto</a> <a href="https://t.co/tWfQuFlqpc">https://t.co/tWfQuFlqpc</a></p>&mdash; James Pasternak (@PasternakTO) <a href="https://twitter.com/PasternakTO/status/1297993765186134016?ref_src=twsrc%5Etfw">August 24, 2020</a></blockquote> 
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> 

</div>


I was curious to see if other users of Bike Share Toronto were also experiencing difficulty due to the 30-minute limit for bike trips. So I downloaded the Bike Share Toronto Ridership Data from City of Toronto's Open Data Portal and analyzed it using Tableau. (2018 is the most recent year for which the data is available on the website.)

So what does the data say? *Overwhelmingly, the 30-minute time limit on rides is not a binding constraint for regular users of the service.*

Here are my main findings:

**First, riders with an annual membership are by far the primary users of the service.** They undertook more the 80 percent of the 1.9 million bike share trips in 2018.

<iframe src="
https://public.tableau.com/shared/CY2NB8GMJ?:showVizHome=no&:embed=true"
width="600" height="600"></iframe>

**Second, riders with an annual membership use the service very differently than riders who are casual members.** The data suggests that annual members use the bike sharing service mostly to commute to work whereas casual members use it recreationally. We can see this from peak usage times for annual members which are morning and evening rush hours and week days; for riders who are casual members, peak usage times are evenings and weekends. Moreover, popular starting stations for annual members are mostly located in the downtown area whereas popular starting stations for casual members are along the scenic trail parallel to the Lake Shore Boulevard. A significant percentage of annual members continue using the bikeshare service even during Toronto's harsh winters whereas casual members stop using the bikeshare service in the winters.

<iframe src="
https://public.tableau.com/views/BikeShareTorontoPeakUsagesTimes/RidershipbyHoursandMonths?:showVizHome=no&:embed=true"
width="600" height="600"></iframe>

**Third, the data strongly suggests that the 30-minute time limit on rides is not a binding constraint for regular users of the service.** *The median bike trip duration is only 10 minutes for annual members*. 75 percent of the bike trips by annual members are less than 15 minutes long and 95 percent are less than 20 minutes long. 

<iframe src="
https://public.tableau.com/views/BikeShareTorontoDistributionofTripDurationsforAnnualMembers/DistributionTripDurationsAnnual?:showVizHome=no&:embed=true"
width="600" height="600"></iframe>

**Lastly, casual members often mistake a "Day Pass" to mean a day-long bike rental rather than unlimited 30 minute rides.** The median bike trip duration for casual members is 20 minutes. However, only 75 percent of the bike trips by casual members are within the 30 minute time limit. This means that one out of every four bike trips by casual members is late and incurs extra fees. Online reviews and [Bike Share Toronto's two-star rating on TripAdvisor](https://www.tripadvisor.ca/Attraction_Review-g155019-d7071915-Reviews-Bike_Share_Toronto-Toronto_Ontario.html) and [GooglePlay](https://play.google.com/store/apps/details?id=com.altairapps.bikesharetoronto&hl=en) suggest that this is primarily because casual members do not understand the 30-minute time limit. Even if the time limit were increased to 45 minutes, 15 percent of bike trips by casual members would still be late and get charged extra fees.

<iframe src="
https://public.tableau.com/views/BikeShareTorontoDistributionofTripDurationsforCasualMembers/DistributionofTripDurationsCasual?:showVizHome=no&:embed=true"
width="600" height="600"></iframe>

In conclusion, the ridership data for Bike Share Toronto does not suggest that there is a need for an increase in the 30-minute time limit. There is a possibility that it might actually do harm if longer trips for recreational users lead to less bikes available for regular users of the service during the evening rush hours.










