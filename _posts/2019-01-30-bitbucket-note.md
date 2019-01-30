---
layout: post
title: "Bitbucket ssh note"
date: 2019-01-30
---

## Pull and Push with bitbucket via ssh
---

For some reason, I am using bitbucket to memo a project document (and code in future). I think use pull and push via ssh like github will be much 
easier. And very begining, it succed, but after played around with something setting, I failed to push /pull again, coz permission issue.
After search, this issue cause bad document of Bitbucket. 

In case for later encouter same issue, I keep a note here:

For each repository, there is a setting page there, and exist two key settings, one for read-only key /deployment key, the other is 
ssh key. At beginning, I am thinking it is my key's issue, since the read-only key cannot get push permission. Trying turn to the ssh key, 
I found maybe more stuff is required to use ssh key (may turn to that in future).

```
$ git clone git@bitbucket.org:WanliXue/xx.git
Cloning into 'xxs'...
git@bitbucket.org: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

After check this again [link](https://stackoverflow.com/questions/13306435/repository-access-denied-access-via-a-deployment-key-is-read-only), 
got noticed that there is a ssh key setting page under the account page (not under repository page), generate key and adding key there will 
grant you pull and push permission.
