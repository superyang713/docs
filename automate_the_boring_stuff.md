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
