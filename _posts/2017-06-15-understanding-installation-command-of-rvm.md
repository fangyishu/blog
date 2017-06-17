---
layout: post
title:  "Understanding Installation Command of RVM"
date:   2017-06-15 21:55:31 +0800
categories: ruby rvm bash
---
[RVM][rvm] is Ruby Version Manager for Ruby like [NVM][nvm] is Node Version Manager for node.

In the [homepage of RVM][rvm], here is how to install it:

    $ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
    $ \curl -sSL https://get.rvm.io | bash -s stable
    
Those two commands often make people very confused: 

* What's the meaning of the `gpg` command and why it is needed?
* Why we need a leading backlash (`\`) before `curl`?
* What's the meaning of `-sSL` option? Is it related with `https` or `SSL` / `TLS`?
* Why that pipe and what is `-s` option for `bash`?

Let's look at them in detail.

## The `gpg` Command

`gpg` (GnuPG) is defined to be an implementation of the OpenPGP standard (RFC4880) from its [website][gpg]. 
Looks like it is a security related command: RSA, public key / private key, authentication, signature, etc. 

Yes, you are right. When used here in a software installation process, it is dealing with digital signature.
This command will try to fetch two public keys from the specified server. Those public keys will be used later
to verify the software have not been modified by somebody else through digital signature.

During the execution of the second command, two files will be fetched, say 
[1.29.1.tar.gz][rvm-tar] and
[1.29.1.tar.gz.asc][rvm-asc]. The first file is RVM 
itself and the second file is a signature, something like this:

    -----BEGIN PGP SIGNATURE-----
    
    iQIzBAABCAAdFiEEYsnl9NowDZSsNhZr4gbCn78E/xcFAlip+ecACgkQ4gbCn78E
    /xdZ3g/+K31QpjaaIfKuLh6G46ng/a0mUournpH9IUl4t0stjj5YNsNW9uC8ZecZ
    de8nDnheTYXAZwdwjACwvjirRqAjt3RVOR6Oz1shOpQNinHvg57it1EVUrSBNRg3
    rpMt4c0Kc8iebySYUn8C9UedbWK7YaNCngVkdWtpob3cFzU5+/XS77cYW8wyGrwq
    5PEt1J+UtO3ZCKb1NqpzKdRE9PZwBBNqElAj8TbpRolkNdDMxm28ZgRAibjNvKPG
    wxix7aP41EHXjT5A9lXjMiH49OhjmikpX+TNhnmoeEkBRU5eB+jFW209hWf6qI2d
    dgEOriQm63F2/TognWoq/xY4ebFMJDd7pST3R9Drk6W4oyjdj5d70rYrkUGAM8P2
    wC/ZAju2D2fSSUfHBMw+0ekwmNs9ExGbroPu0CjyOS6yrClpJ5bZlUjJNVaDVTcx
    6p6iy0m8j8uxKO8dQ+UW2yfj3LyhVQW1H3bJ3ukzX4BYTFVLPLHQutYd5Jepcf29
    7xj17za/d7oVKOAL/Z/3UQzk8i0dAZOz5JT0XZQGpZMNvWvXx5vxyE8yqbbUMITG
    vKjOB8YFkf1NW/qX5NCYGGGJ4h1RpYXhi35uAUxz6DFACZ+bTFsSQ7mj3vApiHNE
    E/QYedTiPJUP3eq0pCClbRQP5iuUxLxtUQT82DiOfp97MtVFJ3I=
    =6X4h
    -----END PGP SIGNATURE-----

`gpg` and the public key will be utilized to verify the integrity of the RVM tar ball.

## The leading backslash (`\`)

Because `curl` is often being aliased. The `\` is to void using a `curl` alias if any exist.
This can prevent unwanted options and making sure only options `-sSL` are applied
([serverfault question][serverfault-backslash]).

## `-sSL` options

My first impression of `-sSL` is something related with SSL, TLS, HTTPS... 
But if you take a closer look, there is one problem. 
For most of the Linux commands, there is one convention:
two dash prefix (`--sSL`) and one dash prefix (`-sSL`) have different meanings.
If only one dash is used, `-sSL` means three different options:

* `-s, --silent`: silent mode, don't show progress meter or error messages.
* `-S, --show-error`: when used with `-s` it makes curl show an error message if it fails.
* `-L, --location`: make `curl` follow redirection (3XX response code).

## `-s` option for `bash`

* `-s`: commands means reading from the standard input. In this case the script downloaded with 
`curl` will be executed with first positional parameter set to `stable` ([superuser Question][bash-s]).

## Followup Commands

    # rvm install 2.4.1
    # rvm --default use 2.4.1
    
## Appendix

### Executing the Second Command without `gpg`

    Downloading https://github.com/rvm/rvm/archive/1.29.1.tar.gz
    Downloading https://github.com/rvm/rvm/releases/download/1.29.1/1.29.1.tar.gz.asc
    gpg: directory `/root/.gnupg' created
    gpg: new configuration file `/root/.gnupg/gpg.conf' created
    gpg: WARNING: options in `/root/.gnupg/gpg.conf' are not yet active during this run
    gpg: keyring `/root/.gnupg/pubring.gpg' created
    gpg: Signature made Mon 20 Feb 2017 04:02:47 AM CST using RSA key ID BF04FF17
    gpg: Can't check signature: No public key
    Warning, RVM 1.26.0 introduces signed releases and automated check of signatures when GPG software found. Assuming you trust Michal Papis import the mpapis public key (downloading the signatures).
    
    GPG signature verification failed for '/usr/local/rvm/archives/rvm-1.29.1.tgz' - 'https://github.com/rvm/rvm/releases/download/1.29.1/1.29.1.tar.gz.asc'! Try to install GPG v2 and then fetch the public key:
    
        sudo gpg2 --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    
    or if it fails:
    
        command curl -sSL https://rvm.io/mpapis.asc | sudo gpg2 --import -
    
    the key can be compared with:
    
        https://rvm.io/mpapis.asc
        https://keybase.io/mpapis
    
    NOTE: GPG version 2.1.17 have a bug which cause failures during fetching keys from remote server. Please downgrade or upgrade to newer version (if available) or use the second method described above.
    
### Result for the First Command

    gpg: requesting key D39DC0E3 from hkp server keys.gnupg.net
    gpg: requesting key 39499BDB from hkp server keys.gnupg.net
    gpg: key D39DC0E3: public key "Michal Papis (RVM signing) <mpapis@gmail.com>" imported
    gpg: key 39499BDB: public key "Piotr Kuczynski <piotr.kuczynski@gmail.com>" imported
    gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
    gpg: depth: 0  valid:   2  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 2u
    gpg: Total number processed: 2
    gpg:               imported: 2  (RSA: 2)

### Executing the Second Command after `gpg` Command

    Downloading https://github.com/rvm/rvm/archive/1.29.1.tar.gz
    Downloading https://github.com/rvm/rvm/releases/download/1.29.1/1.29.1.tar.gz.asc
    gpg: Signature made Mon 20 Feb 2017 04:02:47 AM CST using RSA key ID BF04FF17
    gpg: Good signature from "Michal Papis (RVM signing) <mpapis@gmail.com>"
    gpg:                 aka "Michal Papis <michal.papis@toptal.com>"
    gpg:                 aka "[jpeg image of size 5015]"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: 409B 6B17 96C2 7546 2A17  0311 3804 BB82 D39D C0E3
         Subkey fingerprint: 62C9 E5F4 DA30 0D94 AC36  166B E206 C29F BF04 FF17
    GPG verified '/usr/local/rvm/archives/rvm-1.29.1.tgz'
    Creating group 'rvm'
    
    Installing RVM to /usr/local/rvm/
    Installation of RVM in /usr/local/rvm/ is almost complete:
    
      * First you need to add all users that will be using rvm to 'rvm' group,
        and logout - login again, anyone using rvm will be operating with `umask u=rwx,g=rwx,o=rx`.
    
      * To start using RVM you need to run `source /etc/profile.d/rvm.sh`
        in all your open shell windows, in rare cases you need to reopen all shell windows.
    
    # Administrator,
    #
    #   Thank you for using RVM!
    #   We sincerely hope that RVM helps to make your life easier and more enjoyable!!!
    #
    # ~Wayne, Michal & team.
    
    In case of problems: https://rvm.io/help and https://twitter.com/rvm_io

[rvm]: https://rvm.io/
[nvm]: https://github.com/creationix/nvm
[gpg]: https://www.gnupg.org/
[bash-s]: https://superuser.com/questions/519882/seeking-to-upgrade-my-bash-magic-help-decipher-this-command-bash-s-stable
[rvm-tar]: https://github.com/rvm/rvm/archive/1.29.1.tar.gz
[rvm-asc]: https://github.com/rvm/rvm/releases/download/1.29.1/1.29.1.tar.gz.asc
[serverfault-backslash]: https://serverfault.com/questions/480271/
