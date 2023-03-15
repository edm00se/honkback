# honkback

[![Build 11ty and Deploy](https://github.com/edm00se/honkback/actions/workflows/build.yml/badge.svg)](https://github.com/edm00se/honkback/actions/workflows/build.yml)

An implementation of [mojoaxel/tootback](https://github.com/mojoaxel/tootback) to host an archival copy of my Mastodon archive from my now deactivated single-user Mastodon instance, honk.farm.

## Why?

Like everything in this universe Mastodon servers are ephemeral. They often come to life in a highly energetic moment, glow for some time and than vanish out of existence again because some update goes wrong or human things happen.
If you want your toots to be hosted after your server is long gone you could use this repository to generate a static website with your content.

## Customizations

### Fixes

There were a couple of things I had to account for to get my archive file to work.

#### 1. Move Up `media-attachments` Sub-Directory

My archive was generated with a deeper nested folder structure to the `media-attachments` sub-directory. The json files reference media assuming they're in a top-level `media-attachments` sub-dir, so I moved that up from its nested location.

To repackage the archive file, I did the following:

- extracted the archive
- made my changes (including the one below)
- navigated into the extracted folder path `archive-.....`
- removed `.DS_Store` files, as I'm on macOS and they were otherwise being included
  - command used: [`find . -name ".DS_Store" -depth -exec rm {} \;`](https://explainshell.com/explain?cmd=find+.+-name+%22.DS_Store%22+-depth+-exec+rm+%7B%7D+%5C%3B)
- packaged it back up into a `.tar.gz` of the same name, a level up
  - command used: [`tar -czvf ../archive-20230310144132-4f925242d5076a130f3d9f33ad3401d5.tar.gz .`](https://explainshell.com/explain?cmd=tar+-czvf+..%2Farchive-20230310144132-4f925242d5076a130f3d9f33ad3401d5.tar.gz+.)
  - note: there appears to be an issue with un-tarring on linux from what was tarred by bsd tar. I solved this on my MacBook Pro by installing gnu-tar from homebrew (`brew install gnu-tar`) then doing the same command with `gtar` in place of `tar`

#### 2. Add `movedTo` to `actor.json`

I was able to follow the example and instructions provided by [mojoaxel/tootback](https://github.com/mojoaxel/tootback) with the exception that I was running into some errors around ~"unable to resolve URL". The source file cited was `pageHeader.njk` and, on reviewing the URLs constructed there, found that is relies on the `movedTo` property from `actor.json`, that my archive was missing as it was built before I migrated my account.

### Additions

Additionally I chose to add/update the following:

- updated 11ty to latest (v2)
- swapped out `peaceiris/actions-gh-pages` for `actions/deploy-pages` in the GitHub action to deploy the site
- handle various attachment formats in the toots on the main page (aka show GIFs/videos instead of breaking an `img` tag) (see the `src/_includes/toot/attachments.njk` file, it mirrors what was already done in the `src/media.njk` file)
- added alt text to toot attachment `img`s and within `video` tags
- styled the toot attachments, so as to display them at full width
- styled the account header image to match up with how mastodon shows it (more banner like)
- added each toot as part of a collection (much like you can navigate to a tweet from tweetback, you can view an individual toot, clicking either the published date or navigating to `/<id>`)
- adds a js script to only play videos that are scrolled into view (reduce browser usage when they're offscreen)
- ensures ios video autoplay for GIFs (that are converted to mp4)

## License

Per the original license [AGPL-3.0-or-later]([./LICENSE](https://github.com/mojoaxel/tootback/blob/main/LICENSE)), my changes are published [in their open source GitHub repository](https://github.com/edm00se/honkback).
