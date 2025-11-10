+++
chapter = true
title = "3. CAL Coding Guidelines"
weight = 150
tags = ["C/AL"]
categories = ["Best Practice"]
+++
We've decided to publish our current C/AL coding guidelines. They are actual, as per January 2015 when this is published (but might fall out of sync as time goes by). 

You can also [download the C/AL coding guidelines as pdf,][anchor0] all in one document. In contrast, on this wiki, the coding guidelines are published individually. The reason is: give you the chance to comment and share your oppinion on each one. Thanks to [waldo][anchor1] for this idea and for helping out.

The guidelines are debatable - and it is good when they are generating debate. There is variation of opinion on the rules at Microsoft too. The plan is to simply expose what we use now. And more important, to say that guidelines could be used. Debate on individual guidelines can become heated for any programming language, but the benefit of using some guidelines stays.

For us, those guidelines are enforced at check-in time - we are using a tool which verifies and only allows compliant check-ins. While this tool is internal and not ready to publish, we had anyways decided to open up and present the rules we use to the community, as inspiration. 

Question: Since we're having the guidelines, how come there is still C/AL code in NAV which doesn't respect them?

Answer: all new C/AL code is bound to follow the guidelines (else it cannot be checked-in). However, the code that existed before the rules - it does not. We had done cleanup in a certain degree. Now we're gradually improving the old code base as we visit various objects in order to add new functionality, however chances are that code we didn't touch in a long time had remained in its old form.

We're looking forward to your comments. Where you can, do provide concrete examples of the alternatives, Good and Bad.

{{< youtube z6skKy0pkmU >}}



[anchor0]: /files/CAL-Coding-Guidelines-at-Microsoft-Development-Center-Copenhagen.pdf "download the C/AL coding guidelines as pdf"
[anchor1]: /members/waldo/default.aspx "waldo"
[anchor2]: https://www.youtube.com/watch?v=z6skKy0pkmU&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=26
