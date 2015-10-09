---
layout: post
title: "Many to One: The Email Conundrum Solution"
published: true
---

Personal, school, professional Gmail accounts, an iCloud account, and an account related specifically to my personal app. The amount of email accounts I had to manage was getting increasingly difficult. Not to mention, I'm an old time guy who likes email clients natively on my computer. I refuse to have multiple tabs open to try and check my emails. Being on OS X, my default app of choice is Apple Mail (though it was Postbox until 10.11, but that no longer works currently). Apple Mail has given me issues for years, and I have tried so hard to work with it, but again and again it has screwed up when I have multiple Gmail Accounts Linked. Time and again it would send from my Personal, but the receiver would see it as from my Professional. Absolutely aggravating. So I was thinking, how do I fix this? My options:

* Pick a single provider (out of Google, Microsoft, and Apple) and use only that provider
* Find a way to forward emails and respond appropriately. 

I chose option 2.

-----

## Gmail, Gmail, Gmail
Gmail is the solution for a majority of these problems. Within Gmail you can add what are known as "aliases". Aliases allow people to reach you at a different email address then you actually have. So for example if I have an account named `joe.smith`, I could add an alias `j-smith`, and give people that email at gmail.com, and it would be delivered to `joe.smith`. This is a very powerful concept, and Gmail does a great job of taking it to the next level. So let's get started:

1. Log into your main Gmail account. The one you want to check every time you want to check your email.
2. Click on the gear icon (top right as of this writing) and click `Settings`
3. Click on the `Accounts and Imports` tab.
4. In the `Send email as` area, you will see a couple of options. You will want to do the following: Under `When replying to messages` select "Reply from same address the message was sent to". Next, click `Add another email address you own`. In my experience, I have been only able to get Gmail addresses to work with this (I haven't tried Outlook, but I did have iCloud and couldn't get it to login). Follow the prompts and setup your alias. You can leave the "use as alias" option checked. If you check it to "send as alias" your email will say "from address *via* some address". I recommend not doing this.

Now that we have done that, you are set to forward your email to your primary email (once we have the email coming in, we can respond with no issues since we added the aliases).

To forward your emails, it may vary per provider. For me, I will be stepping through how to do with with Gmail.

1. Same as above, login and go to the settings of **the account you wish to forward**
2. Click on the `Forwarding and POP/IMAP` tab
3. In the `Forwarding` area, click `add a forwarding address`. Click through the steps and set it to your liking

-----

## Sorting your newfound unified email
Google is super cool. It has Gmail extensions you can install as part of their "Labs" feature in Gmail. What we are going to do is make it *seem* like you have more then one inbox, even though you don't. **PLEASE NOTE**: This only works with normal Gmail, and not `inbox.gmail.com`.

1. Like above, go to your settings on your primary account (the one that is now receiving all your forwarded email).
2. Click on `Labs`
3. Enable `Multiple Inboxes`

This also only works if you don't have Inbox sorting setup already. If you do have Inbox sorting setup, go over to the `Inbox` tab and disable everything that isn't "Primary". Now, there is a catch here. Because this is a Google Lab plugin, this is not guaranteed to continue to work in the future. You should keep that in mind when setting this up. Maybe make some extra filters just in case. 

Once you have multiple inboxes enabled, go over to that tab in your settings and set the filters you want and the names for the inboxes. In my case I set the filter to `to: @school.edu in: inbox`. This means any email that comes to my school account and is in my inbox will get sorted to this inbox. Not perfect, but a work in progress.

-----

## Consolidating your devices
Now comes the fun part. You can go to your mobile devices (in my case, an iPhone and an iPad, as well as my Mac) and remove the email accounts you have forwarding to your mail account. Now I have only two accounts in my mail apps instead of four. It is wonderful! It is a work in progress, but it lowers the complexity of managing mulptiple emails. It also helps prevent me from sending from the wrong account.

And that's it! Hopefully this helps some of you out there wrangle in your crazy amounts of email accounts!

** *changed info about aliases on 9/19* **
