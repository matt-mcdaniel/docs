# Setting Up Git Deploy to Push Live

## From Server

Install necessary dependencies
```
sudo apt-get update
sudo apt-get install git
```

Create bare git directory (for hooking)
```
cd ~
mkdir example.git && cd example.git
git init --bare
```

Create "post-receive" hook
```
cd hooks
touch post-receive
sudo nano post-receive
```

Enter the following code to run *after* push has completed
```
#!/bin/bash

# testing/debugging
# e.g., git commit --allow-empty -m 'PUSH to post-receive'
# git push live test-branch

# can push multiple git branches in one command
# loop through branches
# checkout last in list
while read oldev newrev ref
do
  branch=`echo $ref | cut -d/ -f3`
  sudo GIT_WORK_TREE=/var/app/clientreach-portal-test git checkout -f $branch
done

# perform service restarts
# e.g., sudo service nginx restart
```

** **Important**

Make sure you have execute privelages on the `post-receive` file you just created (`chmod 700 post-receive`).

### Enabling push access for a group
By default, the user that access the server is the only one who can execute the `git push live`. Enabling pushing by a group requires changing the permissions of the bare .git repo.

```
sudo chmod -R g+ws example-com.git
sudo chgrp -R mygroup example-com.git

git config core.sharedRepository true
```

This will allow all users part of `mygroup` to properly push and take advantage of the post-receive hook.

## From Local Machine

Make sure public key exists on your server
```
cat ~/.ssh/id_rsa.pub | ssh -i ~/.ssh/your_pemfile.pem username@55.555.555.555 "cat>> .ssh/authorized_keys"
```

Add "live" remote to your repo that points to bare git repo.
```
cd example-com
git remote add live ssh://username@55.555.555.555/home/username/example-com.git
```

To push to multiple "live" remotes (in instances where multiple servers are running the application), add multiple `pushurl`s to `.git/config`.
```
[remote "live"]
        url = ssh://mmcdaniel@55.555.555.555/home/username/example-com.git
        fetch = +refs/heads/*:refs/remotes/live/*
        pushurl = ssh://username@55.555.555.555/home/username/example-com.git
        pushurl = ssh://username@66.666.666.666/home/username/example-com.git
```

You should now be able to push to the `live` remote, effectively updating the server files.


