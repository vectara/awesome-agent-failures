# McDonald's AI Drive-Thru Partnership Failure - 2021-2024

## Incident Overview

**Company**: McDonald's Corporation (in partnership with IBM)  
**Date**: 2021-2024 (end of test announced to franchisees June 2024)  
**Failure Mode**: [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md)  
**Impact**: Test ended, viral social media videos of wrong orders  
**Scale**: 100+ restaurant locations tested

## What Happened

McDonald's began testing an Automated Order Taker (AOT) for drive-thru voice ordering in partnership with IBM in 2021. The system was based on McD Tech Labs (formerly Apprente), which McDonald's sold to IBM in 2021 as part of the partnership.

In June 2024, McDonald's told franchisees in a memo that it would end the test, which was running in more than 100 restaurants, and shut the system off no later than July 26, 2024. McDonald's did not cite order errors as its reason. The memo said: "While there have been successes to date, we feel there is an opportunity to explore voice ordering solutions more broadly." A BTIG analyst note put the system's accuracy in the "low-to-mid 80% range", and videos of wrong orders had circulated on TikTok.

## The Viral Failures

### The McNuggets Order
A TikTok video showed the system adding hundreds of dollars' worth of Chicken McNuggets to an order (Futurism: "over $250 worth"; Restaurant Online: "£166 worth"). Later coverage (Vive USA, June 21, 2024) described the order climbing from 240 to 260 McNuggets in seconds.

### Ice Cream and Bacon Combination
**Incident**: Restaurant Online reported "one customer receiving an ice cream topped with bacon."

### Multiple Drink Orders
**Incident**: One video showed the system adding nine sweet teas to an order (Fortune).

### Complex Order Failures
**Incident**: In a TikTok video, a woman trying to order vanilla ice cream and a bottle of water was given "a number of ice creams, ketchup sachets, and two portions of butter" (Restaurant Online). In the video she says: "Cream packet? No! I just want a large water and a cup of ice cream. Not a ketchup packet! Oh, my God." (Fortune)

## Technical Analysis

The public sources show the outcome (wrong items entered into orders) but not the internal cause. The failure mode is classified as goal misinterpretation because the system entered items the customer did not ask for. Neither McDonald's nor IBM published a technical root cause.

## Company Response

### Official Statement
"As we move forward, our work with IBM has given us the confidence that a voice ordering solution for drive-thru will be part of our restaurants' future." McDonald's said it would keep evaluating options and expected to decide on a voice ordering solution by the end of 2024.

### Timeline
- **2021**: Partnership launched with IBM
- **2021-2024**: Testing in 100+ locations
- **June 2024**: McDonald's memo to franchisees announces the end of the test (reported June 17)
- **July 26, 2024**: Deadline to shut the system off

### IBM's Response

IBM said it would keep working with McDonald's on "a variety of other projects" and was in discussions and pilots with other quick-service restaurant clients about the technology. IBM did not publicly detail technical changes based on the McDonald's test.

### No Public Technical Details

McDonald's did not publicly disclose specific technical reasons for the errors.

## Lessons Learned

1. **Accuracy bar**: The BTIG analyst said the technology "will have to be at least 95% accurate" to be worthwhile; the test reportedly fell short of that.
2. **Customer-visible errors spread fast**: Individual wrong orders became widely shared TikTok videos.
3. **Error handling**: A voice ordering system needs a way for customers or staff to correct wrong items before the order is placed.

## References

- **Primary Announcement**: [CNBC - McDonald's to end AI drive-thru test with IBM](https://www.cnbc.com/2024/06/17/mcdonalds-to-end-ibm-ai-drive-thru-test.html)
- **Syndicated copy of the CNBC story**: [NBC News - McDonald's to end AI drive-through test with IBM](https://www.nbcnews.com/business/business-news/mcdonalds-end-ai-drive-test-ibm-rcna157603)
- **Viral Videos**: [Futurism - McDonald's Abandoning AI-Powered Drive Thrus](https://futurism.com/the-byte/mcdonalds-abandoning-ai-drive-thrus)
- **Viral Videos**: [Fortune - McDonald's ends AI order taking at drive-thrus](https://fortune.com/2024/06/17/mcdonalds-ai-order-taking-drive-thru-ends)
- **260 McNuggets video (Spanish)**: [Vive USA - 260 nuggets entre los errores de los pedidos con inteligencia artificial de McDonald's](https://www.viveusa.mx/noticias/260-nuggets-entre-los-errores-de-los-pedidos-con-inteligencia-artificial-de-mcdonalds/)
- **Industry Analysis**: [Axios - McDonald's kills AI drive-thru ordering after mistakes](https://www.axios.com/2024/06/17/mcdonalds-ai-drive-thru-orders)
- **Technical Coverage**: [Restaurant Online - McDonald's ends AI drive-thru trial after order mistakes](https://www.restaurantonline.co.uk/Article/2024/06/19/McDonald-s-ends-AI-drive-thru-trial-in-US-after-order-mistakes/)
