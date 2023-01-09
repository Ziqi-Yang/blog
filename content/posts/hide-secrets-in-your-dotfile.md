+++
title = "Hide Secrets in Your Dot Files on Public Repository"
author = ["Zarkli Leonardo"]
description = "When upload your dot files repo to host, secrets in some files(maybe one or two lines) could make you feel crazy about whether or not should you make the repo public. However, there is a trick to handle the awkward situation."
date = 2023-01-09T00:00:00+08:00
lastmod = 2023-01-09T00:00:00+08:00
tags = ["rice", "dotfile", "shell"]
categories = ["Rice"]
draft = false
+++

I am using [dotdrop](https://github.com/deadc0de6/dotdrop) to manage my dot files. If you don't use it, it's fine, but you may need to do a some extra work to make things done. For those who manually copy and paste their dot files, I highly recommend you to try it out. It can automate your copy-paste flow, and it is highly customizable. It can save you a lot of time dealing with chores. (Nix OS users should ignore what I said)

The conditions can be divided into two:

1.  Secrets are only some small pieces of code in your dot files. For example, you write a web spider to collect your personal information on a website, but the script file contains your cookie. It can be annoying that you want to share your code and protect your privacy at the same time while using the current dot file automatic workflow. You are in an awkward position.
2.  Secrets are individual files.

The problem can be solved with `encryption` technology. We will use `gpg` to encrypt secrets. For those who haven't set their `gpg keys`, I recommend them to read through [GnuPG - ArchWiki](https://wiki.archlinux.org/title/GnuPG), which is comprehensive and practical.


## Solve the First Condition {#solve-the-first-condition}

To deal with those few but annoying secrets, we can use `sed` to replace the original secrets with encrypted code. For example, we have a file whose content look like this:

```plain
char * pass = "notapassword";

printf("What's your name?");
printf("My name is %s.", name);
```

We want to change the string `notapassword` in the `first` line to an encrypted string which can only be decrypted by ourselves. What should we do?


### Encrypt Secrets {#encrypt-secrets}


#### First, Get the secret needed to be encrypted {#first-get-the-secret-needed-to-be-encrypted}

To get the secret string, we can either manually copy the content if we can ensure that it will not change in the later update or using `sed` command. Here I'd like to briefly introduce the second option.

If your match regex is robust(deal with all input and only output a single pattern), like this regex:

```bash
char \* pass = "\(.*\)";
```

Then the following command will only print out the single password `notapassword`

```bash
sed -n 's/char \* pass = "\(.*\)";/\1/p'
# test it with:
# printf 'char * pass = "notapassword";\n"hello"\n"world"\n' | sed -n 's/char \* pass = "\(.*\)";/\1/p'
```

or If you want to restrict the output to a specific range, you can execute the following command:

```bash
sed -n '1,3s/char \* pass = "\(.*\)";/\1/p' # processing input is 1st to 3st lines
sed -n '1s/char \* pass = "\(.*\)";/\1/p' # processing input is the first line
```


#### Second, encrypt the secret using GPG and base64 {#second-encrypt-the-secret-using-gpg-and-base64}

Continuing with the above example, here we already have the secret string `notapassword`, we then need to encrypt it into a non-breaking string:

```bash
echo "notapassword" | gpg -ea -r 389582ABC15D64CCFB74D82F934AE9B6B6E9FF34
```

Note `389582ABC15D64CCFB74D82F934AE9B6B6E9FF34` is the `keyid` of the key which you want to use to encrypt the string. Toe know your `keyid`, please execute command `gpg --list-keys`:

```bash
$ gpg --list-keys
/home/zarkli/.gnupg/pubring.kbx
-------------------------------
pub   rsa3072 2023-01-06 [SC]
      389582ABC15D64CCFB74D82F934AE9B6B6E9FF34
uid           [ultimate] Ziqi Yang <mr.ziqiyang@gmail.com>
sub   rsa3072 2023-01-06 [E]
```

Since we use `gpg` to encrypt the string(actually use the public key to encrypt string), we can confirm that unless our private key has leaked out, only ourselves can decrypt the string.

Now that we have gotten the encrypted string, which should be like:

```plain
-----BEGIN PGP MESSAGE-----

hQGMA/6ct2lNSe5lAQv+Ik4O4O6gBT8rVDPS/96xHYGUxeokFPKEgrl9QQiAjSTY
D9wcmbxLe9319fI05dO6oJSsam9dgjUKBt42M4PkRjHX3PwZex+O3TYmrxo3zFfg
S4RWxGp2yWJ0/7wbILj1bFLo3bg3LKkkDmPZzK5KWosb0N0G/lR+mvzzDzm6yVYw
e8oR3vs/UefPxF0LiOo9kD4vTI0MgGb6i9dcgLbg08TspeDwiAcStH8QkQFnH8N/
ZQgK1mlblccpTUTsVOuUsslgw/sXpElbBUBUS6ftOzVlqbQMJR0lWYJzl5t/sz+a
4FqkINYp55mQpdpbaTBZ32c8BQvCK6HDZLFc20rhQIg7KPraiDzboHBQIc7pWmml
6QyrkZAxyh3GORuiSPtk74JuUHfO9NbGWn8kwBhkIyR7ecd6BGQYjvsRUW3385Vb
4DZd5rf2j1cVTlx854uwkmb6KE+5bmcjUd/CYcJ1oDPLNQcPBJYZ2wdojnvCJBAV
nnfzy1kAv+eYlUYtl2qS0kgBIe0B8h/cUU2wsWf4mCukT7aBRjdJH0pWjZrkrTUX
urhaA1bQuYSERtQ5DsAxh0TGvXLp4G1y8Hg5J3d7YPSTBc+b/cynG/A=
=xiNF
-----END PGP MESSAGE-----
```

To get one line string, we can either extract the content in the middle of the encrypted data, or use `base64` algorithm to encrypt it. I will introduce the second method.

```bash
echo "notapassword" | gpg -ea -r 389582ABC15D64CCFB74D82F934AE9B6B6E9FF34 | base64 -w 0
```

`-w 0` means don't add line wrap in the `base64` encrypted data. By default, `base64` will add a line wrap after every 76 characters.


#### Third, replace the secret with the encrypted string. {#third-replace-the-secret-with-the-encrypted-string-dot}

We can use a variable to store the previous encrypted string, and the insert the content into the original place.

```bash
sed 's/char \* pass = "\(.*\)";/char \* pass = "'$a'";/'
# test it with:
# a=$(echo "notapassword" | gpg -ea -r 389582ABC15D64CCFB74D82F934AE9B6B6E9FF34 | base64 -w 0)
# printf 'char * pass = "notapassword";\n"hello"\n"world"\n' | sed 's/char \* pass = "\(.*\)";/char \* pass = "'$a'";/'
```

Note that under bash environment, string between single quote is raw(e.g. `'$a'` means exactly `'$a'` ), and if you want to insert variables into the string, use nested single quote(e.g. `''$a''` means `'hello'` if `$a` equals to `'hello'`).


#### Sidenote: Decryption {#sidenote-decryption}

```bash
echo "notapassword" | gpg -ea -r 389582ABC15D64CCFB74D82F934AE9B6B6E9FF34 | base64 -w 0 | base64 -d | gpg -d
```


### Automate Dot Files Management Workflow {#automate-dot-files-management-workflow}

Please refer to [Handle secrets](https://dotdrop.readthedocs.io/en/latest/howto/sensitive-dotfiles/) and [Transformations entry](https://dotdrop.readthedocs.io/en/latest/config/config-transformations/) sections of `dotdrop`.

> Side Note: For write transformations, {0} and {1} in the 'transformations entry' section means the source file path and the temporary file path. The temporary file will be firstly copied from source file, and then you can modify it in the script. Afterward dotdrop will import it into your repo.


## Second Condition {#second-condition}

I have no energy to write more (`____`). Please refer to the first condition.


## Links {#links}


### GPG {#gpg}

1.  [GnuPG - ArchWiki](https://wiki.archlinux.org/title/GnuPG)
2.  [GPG encrypting a string into another single-line string](https://disjoint.ca/til/2017/09/23/gpg-encrypting-a-string-into-another-single-line-string/)


### DotDrop Docs {#dotdrop-docs}

1.  [Handle secrets](https://dotdrop.readthedocs.io/en/latest/howto/sensitive-dotfiles/)
2.  [Transformations entry](https://dotdrop.readthedocs.io/en/latest/config/config-transformations/)
