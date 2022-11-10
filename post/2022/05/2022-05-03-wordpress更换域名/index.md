---
title: "wordpress更换域名"
date: "2022-05-03"
---

```sql
USE wordpress;
UPDATE wp_options SET option_value = replace(option_value, 'old.com','new.com') ;
UPDATE wp_posts SET post_content = replace(post_content, 'old.com','new.com') ;
UPDATE wp_comments SET comment_content = replace(comment_content, 'old.com','new.com') ;
UPDATE wp_comments SET comment_author_url = replace(comment_author_url, 'old.com','new.com')
```
