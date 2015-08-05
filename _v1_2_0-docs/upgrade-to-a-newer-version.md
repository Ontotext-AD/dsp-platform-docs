---
title: Upgrade to a newer version
layout: default
prev_section: user-actions
next_section:
category: HowTo's
permalink: v1_2_0-docs/upgrade-to-a-newer-version/
---
## Five Step Guide

1. Pull the new artifacts from http://maven.ontotext.com/content/repositories/publishing-releases
2. Stop your Tomcat (or your webapp container) and replace the `recommendations-web.war`. Then delete the unpacked war directory.
3. Go to your `solr.home` (see where you set it) and delete everything except the data directory.
4. Unpack `recommendations-solr-core.zip` in `solr.home`.
5. Start Tomcat.
