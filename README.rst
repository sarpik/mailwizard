==========================================
mw(1) Version 2.0.0 \| mailwizard man page
==========================================

..   To test man page:
..
..     pandoc README.rst -s -t man | /usr/bin/man -l -
..
..   The generate:
..
..     pandoc README.rst -s -t man -o mw.1


NAME
====

**mw** — mailwizard - add, list, remove email configurations for mbsync, getmail and mutt. Sync email without mutt.

SYNOPSIS
========

**mw** [<command>|<email>]

| **mw**  Same as *mw sync*
| **mw add**  Add and configure an email address (9 max.)
| **mw list**  List configured accounts
| **mw remove**  Pick an account to remove
| **mw purge**  Remove all accounts and settings
| **mw cron**  Enable or disable an autosync via cronjob
| **mw sync**  Sync mail based on config in *.config/isync/mbsyncrc* and *.config/getmail/\**
| **mw –help\|-h**  Print this message
| **mw –version\|-v**  Print version

DESCRIPTION
===========

**mw** configures **mbsync**, **getmail**, **msmtp** and **mutt** in one go.
**mutt** stands for **mutt** or **neomutt**.

**mw** asks the user for the information it needs.
They cannot be provided as parameters to the command.
It is still possible to script **mw** by defining variables. 
See the commands section.

*mailwizard.muttrc* is linked in your *muttrc*.
Have this line there, if you prefer your own settings::

  # source /usr/share/mailwizard/mailwizard.muttrc

You will need to keep the binding of *i,g,C,M* to *noop*, though,
because of the generated bindings in the account muttrc.

COMMANDS
========

Without command parameter **sync** is assumed.
If the parameter contains @ an email is assumed and synced.

*add*

    Add new email

    First you decide, whether you want to configure

    - **mbsync**, **getmail**, **msmtp** to sync emails independently from **mutt** using **mw**,
    - or just **mutt**.
      For an IMAP server **mutt** becomes very slow.
      So normally you enter **yes** here.

    **mw** asks you the **email address**.

    **mw** has a **database** of IMAP4/POP3 and SMTP servers and ports.
    If you are lucky, your email server is there.
    Else **mw** will prompt you.

    Add an email without questions, e.g.::

      a=x.y@gmail.com mwtype=offline mwaddr=$a mwlogin=$a mwpass=mailwizard-$a mwserverinfo="gmail.com,imap.gmail.com,993,smtp.gmail.com,587" mwname="your name" mw add

    *mwserverinfo* can be omitted, if the email domain is in the accompanied *domains.csv*.

*ls|list*

    List all email accounts configured by mailwizard

*rm|remove*

    Remove the configuration files for an already configured email

    Remove without questions the email whose generated muttrc starts with 1::

      mwpick=1 mw rm <<<y

*purge*

    Removes all mw generated mutt settings and

    | *.config/mutt/accounts/1-\**
    | *.config/isync/mbsyncrc*
    | *.config/getmail/\**
    | *.config/msmtp/config*

    Files are under *.config* or *$XDG_CONFIG_HOME*

    If you don't want questions::

      mw purge <<<y

*cron*

    Toggle a cronjob that periodically syncs mail

    Without questions::

      mwcronremove=yes mw cron
      mwcronminutes=99 mw cron

*sync*

    Syncs mail for all email accounts managed by **mw** (whose paths end in the email),
    and pops up notifications for new mail.

    **mw** alone, without *sync*, syncs without notifications.

    If an email address is given, only that email is synced.

    Every **mw** sync run will re-generate the **mutt** configuration
    from the configuration files for **mbsync**, **getmail** and **msmtp**.
    So you could edit them after or not use *mw add* at all.

    Just keep the *Path*, *path* and *account* ending in the email address.

DEPENDENCIES
============

Required for email setup or operation:

- *pass* - `pass <https://www.passwordstore.org/>`__ safely encrypts passwords
- *gnupg* - needed by *pass*
- *mutt/neomutt* - the email client (*mutt* untested).
- *isync’s mbsync* - syncs the mail
- *getmail* - used for POP3
- *msmtp* - sends the email

Optional:

- *w3m* - view HTML email and images in **mutt**.

- *notmuch* - index and search mail.
  If the configuration file *$NOTMUCH_CONFIG* is not there,
  *mw add* will create it.

- *libnotify* - allows notifications when syncing mail with **mw**

- *abook* - a terminal-based address book.

- A cron manager (e.g. *cronie*) - if you want to enable the auto-sync feature.

- *pam-gnupg* - Forwards your login password to the
  `gpg-agent <https://www.gnupg.org/documentation/manuals/gnupg/Agent-Options.html>`__.
  Login password and GPG password must be the same.
  *default-cache-ttl* and *max-cache-ttl* in *gpg-agent.conf* still apply.
  Increasing them to avoid password requests too soon after login.

- *urlscan* - outputs urls in mail

INSTALLATION
============

::

   git clone https://github.com/rpuntaie/mailwizard
   cd mailwizard
   sudo make install

MUTT CONFIGURATION
==================

Once everything is setup, you’ll use **mutt** to access your mail.

The accompanied *mailwizard.muttrc* modifies some **mutt** defaults.
Look there for a complete list.

Here an overview:

- *?* - see all keyboard shortcuts

**syncing**

- *gm / gM* - call mailwizard’s *mw sync* for one / all mail accounts

**mailboxes,accounts**

- *ixy* - To go to **mailbox**.
- *Mxy*, *Cxy* - For Move and Copy to the according mailbox,
  e.g. *Msp* means "move to Spam".
- *i[1-9]* - go to another **account**.

  *xy* are

  - the two first letters of a mailbox or
  - first letter of first + second letter of second path entry

**searching**

- *S* - search for a mail using *notmuch*
- *gl* - limit by substring of subject
- *gL* - undo limit

**composing**

- *ga* - to add address/person to *abook* and *Tab* while typing
  address to complete one from book.
- *m/r/gr/f* - new/reply/group reply/forward **message**,
  using your default *$EDITOR* to write.
  Then you enter the **compose screen**.
- *a* - to add attachments
- *s/t/c/b/d* - to change the subject/to/CC/BCC/description.
- *S* - to change the signature/encryption
- *y* - to send the mail.

**delete,undelete,save**

- *dd* - delete mail
- *u* - undelete
- *$* - apply the mailbox changes *set trash* is set per default.
  Deleted mails will land there.
- *s* - save selected mail or selected attachment

**moving around**

- *gu* - open a menu to select a url you want to open in you browser
  (needs urlscan).
- *j*/*k* - next/previous mail, *J/K* same, without skipping deleted,
  and also when viewing mails
- *ctrl-d/f*/*ctrl-u/b* - down and up a half page / full page
- *l* - open mail, or attachment page or attachment
- *h* - the opposite of *l*

**sidebar**

- *B* - toggles
- *ctrl-j*/*ctrl-k* - move up and down
- *ctrl-l/o* - opens mailbox

**input field/command line**

- *ctrl-u* clears the line
- *ctrl-a*, *ctrl-e* go to beginning or end
- *ctrl-g* aborts

DETAILS
=======

**Encoding/Language**

    *isync* is not fully UTF-8 compatible.
    **mw** assumes english mailbox names.
    Set your email language to English on your mail server.

**Mail location**

    Mail is downloaded to a folders named after your emails in *$MAILDIR*.
    *$MAILDIR* defaults to *$HOME/Mail/*.
    Neither **mw remove** nor **mw purge** will delete downloaded mail.

**Gmail accounts**

    For Gmail allow 
    `less-secure applications <https://support.google.com/accounts/answer/6010255>`__
    Do this before running mailwizard.

    Gmail uses labels instead of folders.
    To avoid local message duplication, remove labels on the
    `Gmail web interface <https://mail.google.com/mail/u/0/#settings/labels>`__
    or hide them from IMAP
    and remove the according local folders.
    To keep a (local) folder from being synced one must exclude it in *mbsyncrc*.
    **mw** generates this default::

      Patterns * !"_/*" !"[Gmail]" !"[Gmail]/All Mail"

**Protonmail accounts**

    Protonmail users must use the 
    `Protonmail Bridge <https://protonmail.com/bridge/>`__
    to access their IMAP and SMTP servers.
    Do this before running mailwizard.

FILES
=====

*/user/bin/mw*
   The main script to manage and sync emails.

*/user/bin/mwimage*, */user/bin/mwopen*
   Used by the mailcap file that comes with mailwizard.

*/usr/share/mailwizard/mailwizard.muttrc*
   Default mutt settings.

*/usr/share/mailwizard/mailcap*
   Default mailcap file.

*/usr/share/mailwizard/domains.csv*
   Email server database.

BUGS
====

`GitHub Issues <https://github.com/rpuntaie/mailwizard/issues>`__

AUTHORS
=======

*Luke Smith* <luke@lukesmith.xyz>
   Original author, started in 2018. See
   `Github <https://github.com/lukesmithxyz/mutt-wizard>`__,
   `Gitlab <https://gitlab.com/lukesmithxyz/mutt-wizard>`__

*Roland Puntaier* <roland.puntaier@gmail.com>
   Bugfixes, Improvements in 2019.
   Too many changes for Luke to accept the PR.
   Credit to Luke for his initiative.
   New name: mailwizard.
   See `GitHub <https://github.com/rpuntaie/mailwizard>`__

LICENSE
=======

GPLv3

SEE ALSO
========

**neomutt**\ (1), **neomuttrc**\ (1), **mbsync**\ (1), **msmtp**\ (1),
**getmail**\ (1), **notmuch**\ (1), **abook**\ (1)
