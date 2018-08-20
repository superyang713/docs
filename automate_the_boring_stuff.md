# Automating the boring stuff

There were times when I needed to access a brand new remote server, it was a
pain to configure it to the same way as my local machine. Every time I manually
transfered my dot files and installed those packages, I kept asking me, 'there
gotta be a simpler way to do this'. And yes indeed, shell script comes to the
rescue.

I am by no means an expert of shell scripting language, but for my purpose,  it
is insanely easy to pick up with some help from Google and Stackoverflow. Here
is the end result: just simply type the following command,

```
bash <(curl -s https://raw.githubusercontent.com/superyang713/vimrc-and-plugins/master/install.sh)
exit
zsh
```

boom, the new server will be configured in the exactly the way as my local
machine. How awesome is that!

There are certainly many places that can be improved in my shell script, but so
far it has already drastically improved my productivity already. If you are
interested in how it is done, you can check out my repo [here](https://github.com/superyang713/vimrc-and-plugins).

PS. The reason why I didn't wrap everything into the script and left separate
command `exit` and `zsh` hanging there, is because a new zsh shell is installed
when the script is running. After zsh shell is installed, it will automatically
started, which cause the script to stop running. To solve this problem, I need
to exit the zsh shell and re-enter it again. I might improve this in the near
future.
