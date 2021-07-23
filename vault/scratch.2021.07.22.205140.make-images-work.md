---
id: qO90vP3WYbe1grtB
title: Make Images Work
desc: ''
updated: 1627080747351
created: 1627012300156
---


## Tasks
- [x] create s3 in personal account 
- [x] sync assets online
- [x] rewrite image links
- [x] check broken links

## Command
```sh
link-checker --http-status-ignore 2xx,301 \
  --allow-hash-href \
  --http-redirects 2 \
  --http-cache link-checker.cache \
  build/site/notes | tee /tmp/out.txt
```