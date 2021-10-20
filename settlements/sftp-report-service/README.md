# Vipps SFTP Report Service

This service allows for retrieval of settlement files with SFTP.

SFTP can be used manually (interactively), or automatically/programmatically
by using SFTP as part of an integration.

See
[Settlements](https://github.com/vippsas/vipps-developers/tree/master/settlements)
for more information about settlements.

- [Reports](#reports)
  * [Availability](#availability)
- [SFTP Service](#sftp-service)
  * [Security](#security)
  * [SFTP users](#sftp-users)
  * [How to set up users for connecting to the SFTP service](#how-to-set-up-users-for-connecting-to-the-sftp-service)
  * [Connecting to the SFTP server](#connecting-to-the-sftp-server)
  * [Directory structure](#directory-structure)
- [How to use it](#how-to-use-it)
  * [Example SFTP session](#example-sftp-session)
- [Questions?](#questions-)

Document version: 3.0.0.

# Reports

Each MSN (Merchant Serial Number, the unique id of a sale unit) merchant has
its own settlement reports.
It is not possible to aggregate reports from multiple sale units into one report.

There will never be more than one new file per sales unit each
day (and there may be none).

Note that the reports are generated on-demand, which is why the file size is
reported as zero (the size is unknown at the time of listing).

**Please note:** There will be no settlement reports for dates without completed
payments. In these cases, neither the settlement files nor the directories that
should have contained settlement files will exist.
Please see
[Availability](https://github.com/vippsas/vipps-developers/tree/master/settlements#availability)
for details.

## Availability

Settlements are created every day, but only as long as the balance is positive.

See [Availability](https://github.com/vippsas/vipps-developers/tree/master/settlements#availability)
for more details.

# SFTP Service

Vipps can not help with SFTP basics, but recommend this for an overview: https://www.ssh.com/ssh/sftp/

The SFTP report service is used for downloading settlement reports in
[several formats](https://github.com/vippsas/vipps-developers/tree/master/settlements#settlement-report-formats).

## Security

SFTP (SSH File Transfer Protocol) is a network protocol that provides
(among other things) file transfer and file management over any reliable data stream.
SFTP is based on SSH.
SSH (Secure Shell) is a cryptographic network protocol for operating network
services securely over an unsecured network.

For Vipps' SFTP service the public SSH key must be added by logging in with BankID on
[portal.vipps.no](https://portal.vipps.no).

More information about SFTP:
[SSH File Transfer Protocol](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol).

## SFTP users

SFTP users are created, associated with a public SSH key, and given access to the reports of
one or more MSNs (Merchant Serial Numbers, typically "sale units").

It possible to use the same public SSH key for multiple MSNs.

Partners may provide all their merchants with a common public SSH key
that the merchants can use to give the partner access their settlements.

One merchant may have multiple MSNs, and give several partners access to
one or more of them independently.

## How to set up users for connecting to the SFTP service

All merchants can set up SFTP:

1. Log in with BankID on
  [portal.vipps.no](https://portal.vipps.no)
  and select `Utvikler` in the menu.

![Velg profil](images/01_velg_profil.png "Velg profil")

2. Click the tab `SFTP Access` to add users for SFTP access.

3. You can add the public keys of the user(s).
   We support RSA (minimum 2048-bit),
   EdDSA and Ed25519 keys in OpenSSH format (and reject DSA keys).
   After this you should see the newly created user.
   For help creating SSH keys, the GitHub documentation may be helpful:
   https://help.github.com/articles/connecting-to-github-with-ssh/

An example of a public SSH key:
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOu1WvDcTWwZumZZwTvfqUKMA4ouG3mniNsvpNWorm5m user@example.com
```
This is what it looks like on portal.vips.no:

![Bruker opprettet](images/02_bruker_opprettet.png "bruker opprettet")

**Please note:** Vipps can not add the SSH key for you.
If you do not have BankID, or you do not have the required permissions
to add the SSH key, you will need the administrator to do it for you, or
to update your permissions on
[portal.vipps.no](https://portal.vipps.no).
The administrator can create additional users on
[portal.vipps.no](https://portal.vipps.no).
If you don't know who the administrator is, you can check
[Brønnøysundregistrene](https://www.brreg.no)
and see who has the right to sign for the company.

## Connecting to the SFTP server

This is a standard SFTP service, and the address of the SFTP server is `sftp.vipps.no`.

When connecting to the SFTP server you need to supply the username you created.
In the picture above this is "hakon".

Vipps can not help with SFTP basics, but recommend this for an overview:
https://www.ssh.com/ssh/sftp/

**Please note:** Vipps may change the IP addresses of `sftp.vipps.no`.
To ensure that you are whitelisting the correct IP addresses please use the
hostname and DNS, and automatically update your firewall rules if there are DNS changes.

## Directory structure

This is the directory structure:
```
/settlements/[inbox|archive]/[file extension]/[orgno.]/[merchant serial number]/[merchant serial number]-[settlement number].[file extension]
```
The `orgno.` is the company's organization number, nine digits.
The `merchant serial number` (also called MSN) is the unique five or six digit
id for the sale unit.

In the examples on this page, the `orgno.` is 998724341, and the `merchant serial number` is 16655.

Example files, with full path:
```
/settlements/inbox/xml/998724341/16655/16655-2000001.xml
/settlements/inbox/pdf/998724341/16655/16655-2000001.pdf
/settlements/archive/csv/998724341/16655/16655-2000001.csv
```

# How to use it

Reports under `/settlements/inbox` can be "deleted" (actually hidden)
with the SFTP client in order to keep track of already processed reports.

Reports are deleted by using the `rm` command in SFTP or the "delete" function
in your SFTP interface.

Reports under `/settlements/archive` cannot be removed.

**Important:** The reports are generated on-demand.
There are no real files on the server, all data is generated dynamically.
Some SFTP clients check the file size with a `ls` command.
Since the files have not yet been created, the file size is reported as zero.
The service can not provide correct size information.
It is therefore not possible to check the size of a file with `ls`.

## Example SFTP session

Below is an example of an SFTP session, using the command-line,
with line breaks added for readability.

```
$ sftp sftp.vipps.no
Connected to sftp.vipps.no.

sftp> ls
settlements  

sftp> ls settlements
settlements/archive   
settlements/inbox     

sftp> ls settlements/inbox
settlements/inbox/csv        
settlements/inbox/pdf        
settlements/inbox/xlsx       
settlements/inbox/xml        
settlements/inbox/xml.zip    

sftp> ls settlements/inbox/xml
settlements/inbox/xml/998724341   

sftp> ls settlements/inbox/xml/998724341
settlements/inbox/xml/998724341/16655    

sftp> ls settlements/inbox/xml/998724341/16655
settlements/inbox/xml/998724341/16655/16655-2000001.xml             
settlements/inbox/xml/998724341/16655/16655-2000002.xml            
settlements/inbox/xml/998724341/16655/16655-2000003.xml             
settlements/inbox/xml/998724341/16655/16655-2000004.xml             

sftp> mget settlements/inbox/xml/998724341/16655/*
Fetching /settlements/inbox/xml/998724341/16655/16655-2000001.xml to 16655-2000001.xml
Fetching /settlements/inbox/xml/998724341/16655/16655-2000002.xml to 16655-2000002.xml
Fetching /settlements/inbox/xml/998724341/16655/16655-2000003.xml to 16655-2000003.xml
Fetching /settlements/inbox/xml/998724341/16655/16655-2000004.xml to 16655-2000004.xml

sftp> !ls -1
16655-2000001.xml
16655-2000002.xml
16655-2000003.xml
16655-2000004.xml
sftp> quit
```

# Questions?

We're always happy to help with code or other questions you might have!
Please create an [issue](https://github.com/vippsas/vipps-developers/issues),
a [pull request](https://github.com/vippsas/vipps-developers/pulls),
or [contact us](https://github.com/vippsas/vipps-developers/blob/master/contact.md).
