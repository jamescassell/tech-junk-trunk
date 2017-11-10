---
layout:     post
title:      Add a private key to your ssh agent from a variable.
date:       2017-11-10 12:47
summary:    How to add an SSH key from an enviroment variables instead of file.
categories: bash
thumbnail: bash
tags:
 - bash
 - ssh
---

I always have a hard time remembering how to add an ssh key (`ssh-add`) from an environment variable containing the private key.  "In a variable!?"  You may exclaim, don't worry it's typically a 'secret' variable on a continous integration system that supports masking sensitive variable key/values.  I wouldn't recommend doing this unless you're certain the value of your variable is not going to accidently leak in plain text .

To redirect the contents of the environment variable into `ssh-add` instead of a real file simply use:

```
eval $(ssh-agent -s) 
ssh-add <(echo "$PRIVATE_KEY") 

# List out your new key's fingerprint
ssh-add -l

# Don't forget to cleanup your agent after you're done using it if you're not on an ephemeral build server.
ssh-agent -k
```

The more general lesson here is `<(echo "$VAR")` is good for redirecting variables instead of a file incases where a good old pipe `|` just won't work.
