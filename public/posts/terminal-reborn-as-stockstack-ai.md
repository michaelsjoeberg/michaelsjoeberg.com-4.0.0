Terminal reborn as Stockstack.AI
Michael Sjöberg
Jul 8, 2023
Jul 8, 2023


## New beginnings

I recently re-launched Terminal as Stockstack.AI, available here: [stockstack.ai](https://stockstack.ai). I might change domain again since nothing seems to beat .com, so sorry in advance! If you can't stand the .AI hype, it's also available here: [trystockstack.com](https://www.trystockstack.com/).

Stockstack.AI is a slimmed down version of Terminal with a more narrow focus on distraction-free stock market data. The goal is to beat Yahoo Finance in data coverage and user experience. To hone in on this goal (and to reduce workload!), I removed several features, including portfolio tracker, batch requests, and query.

## Updated features

The updated version shipped with an improved grid for organizing data widgets. Widgets now have scalable text, support multiple columns (great for financials), and are freely resizable and movable around the workspace. I also updated most data in widgets and added several new sources. If you add your own API key for Seeking Alpha (requires an active subscription), you can basically have five different news widgets, which should be more than enough to give a fairly comprehensive overview of the market.

Other new and interesting widgets show interest rates by various institutions, market status, upcoming holidays, and company filings (both today and historical for ticker). The old chart widget is replace by four widgets with charts for different ranges, so can view all at same time.

## AI-assistant

There's an AI-assistant widget, obviously. It's not as good or fast as looking at data widgets for pricing data, but great for providing wikipedia-style information about less known companies or CEOs.

Here's a few example responses:

> Capcom Co., Ltd. (9697.T) is a Japanese company that is primarily known for its video game development and publishing. They are best known for creating iconic game franchises such as Resident Evil, Street Fighter, and Monster Hunter. In addition to developing and publishing games, Capcom also operates amusement arcades in Japan. Here's an unexpected trivia about Capcom: Did you know that the company's name, "Capcom," is an abbreviation of "Capsule Computers"? This name was chosen to reflect the company's focus on creating innovative games that are like capsules filled with fun and excitement.

> Kakao Corp. (035720.KS) is a South Korean company primarily involved in the technology and communication sector. Their business model revolves around providing various services and platforms to users in different areas. [...] 1. Messaging Services: Kakao Corp. operates messaging services, similar to WhatsApp, Facebook Messenger, LINE, and WeChat. 2. Mobile Content and Gaming: Kakao Corp. competes in the mobile content and gaming industry. They develop and publish popular mobile games and digital content. Competitors in this space include companies like Netmarble, NCSoft, and Nexon. 3. Payments: Kakao Corp. also offers financial services, including digital payment solutions. They compete with companies like PayPal, Alipay, and Apple Pay in the payments sector. 4. Advertising: Kakao Corp. operates in the advertising space as well. They provide digital advertising platforms that help businesses reach their target audience. Competitors in this area include Google Ads, Facebook Ads, and domestic South Korean advertising companies.

This kind of information is not available as structured data via some API and could previously only be found via google, no more.

## Pricing

Pricing starts at USD 10.00, but will increase to USD 29.00 in a few weeks. I am still thinking about adding a proper free plan.


