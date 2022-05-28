---
author: "Marcin"
title: "Deploy"
date: 2022-03-10T20:50:29Z
description: "Sample article showcasing basic Hugo commands"
aliases: []
draft: false
categories: ['deploy']
tags: ['deploy']
ShowToc: true
TocOpen: false
searchHidden: false
ShowRssButtonInSectionTermList: true

# dziala image for post in posts
cover:
    image: "https://gohugo.io/opengraph/gohugoio-card-base-1_huf001e7df4fd9c00c4355abac7d4ca455_242906_filter_11551428109277559587.png"
    # can also paste direct link from external site
    # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
    alt: "Hugo Deploy | Hugo"
    caption: "Hugo Deploy | Hugo"
   # To use relative path for cover image, used in hugo Page-bundles
    relative: false 
---
### A quick way to make a new post
Pass this code to your config.yml
```yaml
#https://blog.cavelab.dev/2021/08/deploying-hugo-blog-to-s3/
deployment:
  targets:
    - name: Name the Tagrets 
      URL: 's3://marcinmitruk.link?region=your-region-2or1'
      # cloudFrontDistributionID: <CF-Distribution-ID>
  matchers:
    - pattern: ^.+\.(js|css|svg|ttf|woff|woff2|eot|png|jpg|gif|svg|ttf)$
      cacheControl: 'max-age=630720000, no-transform, public'
      gzip: true
    - pattern: ^.+\.(html|xml|json)$
      gzip: true
```
Create new post...

```shell
hugo new posts/some-title.md
```
and

run local server tape ```hugo server``` to deploy on AWS s3 after the previous configuration
type. 
```shell
hugo && hugo deploy
```

<!--more-->
Yes, This is simple.




