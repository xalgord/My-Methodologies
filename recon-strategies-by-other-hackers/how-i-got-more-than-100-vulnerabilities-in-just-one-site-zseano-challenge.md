---
description: >-
  source:
  https://0xm5awy.medium.com/how-i-got-more-than-100-vulnerabilities-in-just-one-site-zseano-challenge-19bd783fdebf
---

# How i got more than 100 vulnerabilities in just one site? (zseano-challenge)

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*V6FT8k2xPmhkEoNR92KixA.png" alt="" height="697" width="700"><figcaption></figcaption></figure>

In this article, I will talk about how I managed to discover over 100 vulnerabilities in a program on HackerOne. I also continued working on it for over a year even after the program was closed and moved to GitHub. Today, I bring you some tips, especially considering that when I joined HackerOne, it had only been 5 months since I started, so I was very lucky to have this fresh program. As a side note, I haven’t found another program like it since then, so let’s begin our story.

## **A Quick Disclaimer** <a href="#b4b5" id="b4b5"></a>

This article is non-technical. I am here just talking about how I found more than 100 vulnerabilities in one site and completing the 12-month zseano challenge. I also tell you the steps that I did and also some important tips because it will be difficult for me to mention how I got all of the 100 vulnerabilities! Step by step, so I will include for you the names of the vulnerabilities and also the titles of the reports as i can . I hope this helps (:

On March 16, 2021, I received an invitation on HackerOne. As I mentioned before, I had only been on this platform for 5 months, so I didn’t know much. I used to be a person who copied and pasted payloads without understanding anything. I would take from here and paste it there, watch some tutorials, and search for CSRF and XSS vulnerabilities. If I couldn’t find them, I would move on to another target. However, on March 16, everything changed for me. I changed my mindset, approach, and planning, and most importantly, my roadmap. I started focusing more on access control and business logic, and that’s where the story begins.

Let’s explain how the website works, its functions, and more.

It’s an online gaming entertainment website. The site offers a variety of free games that users can access through their browsers. The available games range from puzzles to adventure games, sports games, and many other types. It’s a popular platform for online gaming, allowing users to have fun and enjoy the online gaming experience without the need to download or install games.

> _There were only two roles: User and Streamer._

The User could write messages on the live chat, post updates, send private messages, and to do all that, they had to purchase tokens. They could also send tokens to the Streamer during the live stream.

The Streamer could do everything the User could do, and they could also open live streams, had their own dashboard, and this is where most of the vulnerabilities were found.

Now that you understand what the website is and what roles are available, let’s dive into our story!

> On this day, I was watching a video by “ZSeano” ([link](https://www.youtube.com/watch?v=0QFByAYQ\_p0\&t=6s)), where he said, “You should challenge yourself for 12 months on a single target.” This resonated with me, and I decided to take up this challenge with this particular program. I never expected to discover all the vulnerabilities that I will mention below because the scope was very limited, only “my/www/api” domain, and everything else was out of scope. So it was quite a shock for me!

**Here are all the vulnerabilities I discovered:**

> _- 30x IDORS_\
> _- 5x XSS_\
> _- 7x Access Users/Streamers data_\
> _- 4x Able to got free tokens by sending negative tokens (I could purchase tokens and send them as support to the Streamer, but when I sent a negative value, it increased the tokens in my account)_\
> _- 5x CSRF_\
> _- 4x DoS_\
> _- 4x CORS_\
> _- 5x Race condition_\
> _- 10x 2FA and 2FA bypasses_\
> _- 8x Open redirects and open redirect bypasses_\
> _- 5x No rate limit_\
> _- 1x Ability to execute commands on the Streamer’s chat as a moderator from a normal user’s account_\
> _- 4x HTML injection_\
> _- 3x Account takeover_\
> _- 1x Private messages could be seen even if they were still locked (need to buy to unlock them)_\
> _- 1x Send unlimited private messages without decreasing the remaining messages (if you bought 10 messages and sent one, your messages would still be 9, and so on)_\
> _- 1x Broadcasters could approve their own documents (I could accept my own application to become a Streamer without going through the admin)_\
> _- 1x Password check at account termination flow could be bypassed (you needed to know the account’s password to close it)_\
> _- 1x Disabled moderators were still able to invite others to VIP shows_\
> _- 1x Broadcaster could make their own page unavailable by setting a specific allowed value on a post_\
> _- 1x Streamers could change their identity information_\
> _- 1x API endpoint returned all the deleted posts_\
> _- 1x Streamers were able to edit the tokens’ posts from the users_\
> _- 1x Add performers without the need for confirmation_\
> _- 1x View all the top tippers \[“userId”, “tokens”, “username”] for all the streamers_\
> _- 1x Streamer could trick users into unlocking unnecessary videos_\
> _- 1x Users could delete anyone’s comments on live chats without needing to be a moderator_\
> _- 1x Disable the login attempt of any user_\
> _- 1x Bypass the alert screen in the live chat (similar to HackerOne’s alert screen when clicking on a link)_\
> _- 1x Streamers could bypass the \[User not present on your stream] to invite any user to their stream_\
> _- 1x Reset password link did not expire after changing our email_\
> _- 1x Streamers/users could use other streamers’ emotes on their own channel and other channels by using the emote handle_\
> _- 1x Users could use Streamer Emotes without subscribing to the streamer using the disclosed \`Handles\` in the HTML CODE_\
> _- 1x Broken OTP authentication_\
> _- 1x Streamers/users could increase their level from the “xx” endpoint to an unlimited level without buying/sending tokens to anyone. They could unlock gifts for free and more!_\
> _- 1x Users could change their username to an unlimited number_\
> _- 1x Metrics file leaked with 580 disclosed emails for \`_[_@test_](http://twitter.com/test)_.com\`, 975 for \`_[_@test_](http://twitter.com/test)_.net\`, and 14700 for \`_[_@gmail_](http://twitter.com/gmail)_\`. Internal endpoints were also leaked._\
> _- 1x Streamers could change their “Earning Plan” themselves without contacting support._

This is all the vulnerabilities that I remember, and I think there are more than this, but I didn’t add them :DD

Now, I will give you tips and also how I discovered these vulnerabilities. Of course, it will be difficult to explain each vulnerability separately, so I will provide tips and also tell you about my mindset in the beginning and how it changed in the end.

In the beginning, let’s talk about my mindset when I started on March 16, 2021. When I received the invitation, I didn’t find any vulnerabilities for a week because, as I mentioned, I was only searching for XSS/CSRF without understanding the website. However, when I discovered two vulnerabilities, I honestly didn’t know how they accepted them. I considered myself lucky, but this was the reason that encouraged me to work more! Then, I decided to start understanding the application, how it works, its advantages, disadvantages, what it offers, what a user can do, what a streamer can do, and so on.

Gradually, I started to understand everything, so after a month, I found vulnerabilities like leaves on a tree in every aspect. Every day, I would read new write-ups and try them on this application. It was like a lab where I trained myself and, surprisingly, I found every vulnerability I learned new on this website and received bounties for it!

When I finished working on the website, I left the program for a while until I learned about something called an API! I noticed that they had an API domain in scope, so I decided to work on it. Fortunately, there was documentation available for all the available endpoints, and this was a treasure for me. I downloaded it to my device for fear that it might be deleted. I would try each endpoint individually, and even if I didn’t know how it worked, they would help me. It was truly amazing.

When I finished with the API, I left the program again until I was asked to become a streamer, and I got it! Now, it was time to work on the streamer’s dashboard on the domain “my.” Here, I decided to pause for a while until I finished working on access control and business logic for the following user and user role:

> _user / user role_\
> _user / streamer role_

When I finished, I discovered many vulnerabilities, such as tokens, users’ data, and more, as mentioned in the vulnerability names above. Now, it was time to work on the streamer and streamer role.

I discovered more than 50% of my vulnerabilities here. I think they believed that the streamers would be verified, so there would be no danger, and they didn’t secure it well. I found vulnerabilities in every available function here too. It was amazing!

This was all by January 1, 2023. But today, February 7, 2023, did I stop there? From the first date to the second, I discovered many vulnerabilities that I hadn’t seen before or my focus wasn’t good enough. So, it’s always good to retest periodically from the beginning. Here, I mean that you should treat yourself as if you are still new to the website because every day, the source code is renewed, and new things are added or modified. Therefore, there will be new errors :DD. This was my story in simplicity, what I did, and I think it’s time for the tips.

1. Watch this video: \[[link](https://www.youtube.com/watch?v=0QFByAYQ\_p0\&t=6s)]
2. Most of my bugs in this site was protected on the front-end but when i try it on back-end its works so don’t trust The front-end only you must check the back-end as well .
3. Tools matter a lot less than you think. ( all my bugs are 100% manual )
4. Don’t stop learning. Challenge yourself! As soon as a hobby gets boring or repetitive, it’s now become work. Try something new, pick up a challenging program that you think might be impossible. You will be surprised!
5. Manual approach recommended: Lean towards manual techniques rather than relying solely on automation but acknowledge the advantages of automation as well.
6. Understand the application — Understand the application — Understand the application. Even if it takes a month to understand it, it will pay off positively.
7. Invest sufficient time: Be prepared to spend 40–80 hours on a specific target before significant progress is achieved. my case was more then a year :D
8. Focus on one target only until you finish it. Don’t distract yourself with multiple targets.
9. Select a target: Choose a specific application or area to delve deep into and immerse yourself completely.
10. Learn/read every day, as I mentioned in the article. Every time I learned something new, I would try it on the website and get a bounty for it!
11. Avoid common pitfalls: Ensure you have exhaustively explored the application before moving on, including pulling endpoints from JS files, utilizing brute force methods, studying documentation, and iterating multiple times on attack vectors
12. Don’t let money be the motivator for bug bounty. I know this is probably controversial and of course bug bounty has made a huge difference in peoples’ lives across the world – however if money is your primary motivator it will add stress, disappointment, and anxiety that will absolutely not help you focus. Consider bug bounty an amazing hobby that happens to pay money once you know what you’re doing, and leave it that way for a while – maybe forever.
13. Don’t be hasty to finish the target. Give it enough time. No one is chasing you.
14. Avoid excessive education and focus on actual hacking.
15. Be respectful and understanding with the team. This really helped me become friends with them :D
16. Master the fundamentals of hacking before obsessing over automation
17. Choose bug bounty programs that genuinely interest you.
18. When you finish, leave it for a while, then come back. You will find completely new things and new vulnerabilities because, as I mentioned, every day the source code is updated, and new things are added.
19. Remember that success in hacking is not solely based on time spent.
20. When you finish, don’t say you’re done until you finish it once, twice, and three times. This helped me discover 20 new vulnerabilities that I didn’t know or see.
21. Minimize distractions and avoid automating tasks mid-hacking.
22. Try to bypass the fixes. This really helped me get more than 25 vulnerabilities :DDD just by bypassing the vulnerabilities after they were fixed.
23. Don’t get consumed by Reconnaissance; focus on finding vulnerabilities.
24. Establish boundaries between work and personal life.
25. Take regular breaks during hacking sessions to avoid burnout.
26. Seek support from the hacking community and fellow hackers.
27. Use the payout phase to explore and investigate additional bugs.
28. Don’t rely solely on a single payout; continue searching for vulnerabilities.
29. Stay engaged and proactive even during the payout phase.
30. Use time effectively to
