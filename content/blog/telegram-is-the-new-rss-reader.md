---
title: "Telegram Is the New Rss Reader"
date: 2021-03-11T10:10:30+02:00
draft: false
---
With Google killing its Reader an era ended for me. I loved its old design and couldn't get used to the new alternatives.
As a result, I stopped reading blogs via RSS for some time. There was also a problem of information overload: too many
new posts, sort of like an infinite scroll in Facebook or Instagram.

Eventually a lot of interesting people I follow started creating channels in Telegram. And as of today, Telegram has
become a de-facto social network and news aggregator for me. Some of the things I have there:

- Channels/blogs from people who post on Telegram
- Group chats about product management and information security (muted notifications as I check them out whenever I want)
- Custom bot which identifies mushrooms on the picture (I do foraging during summer and autumn)
- And now RSS feed from the blogs I follow

Previously I used IFTTT to send me latest posts, however after their latest update it's no longer possible which made
me look for alternatives. Meet [FlexGet](https://flexget.com/).

FlexGet is a "multipurpose automation tool for all of your media". It supports torrents, nzbs, podcasts, comics, TV,
movies, RSS, HTML, CSV, and more. The most popular use-case is to fetch torrent files and notify your client to start
the download. However it also plays nicely with blog posts and Telegram.

The first step is to create a Telegram bot. Find @BotFather account and send it `/newbot` command. After filling in
all the necessary information you will receive API token.

Install FlexGet on your machine or your server where you can run it periodically, e.g. via cron, launchd, or any other
way. Create `config.yml` which contains your Telegram configuration, notification template, and a list of blogs:

```
templates:
  common:
    notify:
      entries:
        message: |+
          *{{title}}*

          {{ url }}
        via:
          - telegram:
              bot_token: <your telegram bot token>
              parse_mode: markdown
              disable_previews: no
              recipients:
                - username: <your telegram account>

tasks:
  macwright:
    template: common
    accept_all: yes
    rss:
      url: https://macwright.com/rss.xml
      all_entries: no

  foragerchef:
    template: common
    accept_all: yes
    rss:
      url: https://foragerchef.com/feed/
      all_entries: no

  erikbern:
    template: common
    accept_all: yes
    rss:
      url: https://erikbern.com/atom.xml
      all_entries: no
```

During the first run FlexGet populates its database with all of the posts, so you might receive them all (or simply
remove the `template` setting from each of the tasks to avoid that). On subsequent runs you will be notified of the new
updates.

Telegram is usually helpful in generating nice previews from the URL which is why I don't send the full post contents.
