# tac_plus

```
tac_plus(8)                                  System Manager's Manual                                  tac_plus(8)

NAME
       tac_plus - tacacs plus daemon

SYNOPSIS
       tac_plus -C <configfile> [-GghiLPSstv] [-B <bind_address>] [-d <level>] [-l <logfile>] [-p <tcp_port>] [-u
       <wtmpfile>] [-w <wholog>]

DESCRIPTION
       By default, tac_plus listens on tcp port 49 and provides network  devices  (normally  routers  and  access
       servers) with authentication, authorization and accounting services.

       A configuration file controls the details of authentication, authorization and accounting.

COMMAND-LINE OPTIONS
       -C <configfile>

              Specify the configuration file name.  The -C option is required.

       -B <bind address>

              Specify  the  address  on  which  the  daemon  should bind(2).  Successive instances of -B override
              previous instances.  By default, the daemon listens on all addresses.  Note: this changes the  name
              of the pid file created by the daemon.

       -G     Remain in the foreground, but not single-threaded nor logging to the tty.

       -d <level>
              Switch on debugging.  By default the output will appear in the log file and syslog(3).

              NOTE: The -g flag will cause these messages to also appear on stdout.  The -t flag will cause these
              messages to also be written to /dev/console.

              The value of level is as described below.  These values represent bits that can be  logically  OR'd
              together.  The daemon logically ORs successive occurrences of the -d option.

              Value   Meaning
              2       configuration parsing debugging
              4       fork(1) debugging
              8       authorization debugging
              16      authentication debugging
              32      password file processing debugging
              64      accounting debugging
              128     config file parsing & lookup
              256     packet transmission/reception
              512     encryption/decryption
              1024    MD5 hash algorithm debugging
              2048    very low level encryption/decryption
              32768   max session debugging
              65536   lock debugging

       -g     Single  threaded  mode.   The  daemon  will  only  accept and service a single connection at a time
              without forking and without closing file descriptors.  All log messages appear on standard output.

              This is intended only for debugging and not for normal service.

              This option does not work with single-connection sessions.

       -h     Display help message.

       -i     tac_plus will be run from inetd(8).  In inetd mode, the configuration file  is  parsed  every  time
              tac_plus starts.

              If  the configuration is large or the frequency of connections is high, this negatively will affect
              the responsiveness of the daemon.

              If the config file is small, connections are infrequent,  and  authentication  is  being  done  via
              passwd(5)  files  or  SKEY  (which  are not cached), running in inetd mode should be tolerable, but
              still is not recommended.

              This option does not work with single-connection sessions.

       -l <logfile>
              Specify an alternate log file location.  This file is only used when the -d option  is  used.   The
              logs are still posted to syslog.

       -L     Lookup  DNS PTR (Domain Name System PoinTeR) record of client addresses.  The resulting FQDN (Fully
              Qualified Domain Name), if it resolves, will  be  used  in  log  messages,  libwrap  (tcp_wrappers)
              checks, and for matching host clauses of the configuration file.  Also see tac_plus.conf(5).

       -P     Parse  the  configuration  file, echo it to standard output while parsing, and then exit.  tac_plus
              will exit non-zero when a parser error occurs.

              Useful for debugging configuration file syntax.

       -p <port>
              Listen on the specified port number instead of the default port 49 for  incoming  tcp  connections.
              Note: this changes the name of the pid file created by the daemon.

       -S     Enables or allows client single-connection mode, where-by the client will create one connection and
              interleave queries.

              Note: this is broken in IOS and IOS-XE.

              Note: this is currently only partially supported in the daemon.

       -s     Causes the daemon to always reject authentication requests which contain a minor version number  of
              zero  (SENDPASS).   This enhances security in the event that someone discovers your encryption key.
              SENDPASS requests permit requesters to obtain CHAP, PAP and ARAP passwords from the daemon, iff the
              encryption key is known.

              Note: IOS versions preceding 11.2 will fail.

       -t     Log  all  informational,  debugging  or  error  messages  to /dev/console in addition to logging to
              syslogd. Useful for debugging.

       -u <wtmpfile>
              Write wtmp entries to the specified wtmp file.

       -v     Display version information and exit.

       -w <wholog>
              Specify the location of the max session file.

STARTING
       tac_plus is normally invoked by root, as follows:

           # tac_plus -C <configfile>

       where <configfile> is a full path to the configuration file.  Tac_plus will background  itself  and  start
       listening on port 49 for incoming tcp connections.

       Tac_plus  must  be  invoked  as  root  to  obtain  privileged  network socket 49 and to read the protected
       configuration file, which may contain confidential information  such  as  encryption  keys  and  cleartext
       passwords.

       After  the  port is acquired and the config file is read, root privileges are no longer required.  You can
       arrange that tac_plus will change its user and group IDs to a  more  innocuous  user  and  group  via  the
       configuration file.

       NOTE:  The new user and group still needs permission to read any passwd(5) (and shadow(5)) files and S/KEY
       database if these are being used.

TCP WRAPPERS
       If tac_plus was compiled with libwrap (aka. tcp_wrappers) support, upon connection the daemon will consult
       with  tcp_wrappers on whether the client has permission to connect.  The daemon name used in a daemon list
       of the access control file is the name of the executable, normally "tac_plus".  See hosts_access(5).

PERMISSIONS
       The configuration file should be unreadable  and  unwriteable  by  anyone  except  root,  as  it  contains
       passwords and keys.

SIGNALS
       If  the  daemon is receives a SIGHUP or SIGUSR1, it will reinitialize itself and re-read its configuration
       file.

       Note: if an error is encountered in the configuration file or the file can not be opened for reading, such
       as  due to insufficient permissions resulting from process ownership and file permissions, the daemon will
       exit.

       Likewise, if the daemon is configured to send accounting records to a file and that file can not be opened
       for  writing,  such  as  due  to  insufficient  permissions  resulting  from  process  ownership  and file
       permissions, the daemon will exit.

LOG MESSAGES
       tac_plus logs error and informational messages to syslog facility LOG_DAEMON.

FILES
       /var/log/tac_plus.acct        Default accounting file.

       /var/log/tac_plus.log         Default log file used when the -d option is used.

       /var/run/tac_plus.pid         Pid file.  If the -B option is used, ".bind_address" is appended.  If the -p
                                     option is used, ".port_number" is appended.

SEE ALSO
       tac_plus.conf(5), tac_pwd(8)

       Also  see  the  tac_plus User Guide (user_guide) that came with the distribution.  The user guide does not
       cover all the modifications to the original Cisco version.

HISTORY
       There are at least 3 versions of the authentication protocol that people commonly refer to as "TACACS".

       The first is ordinary tacacs, which was the first one offered on Cisco boxes and has been in use for  many
       years.  The second is an extension to the first, commonly called Extended Tacacs or XTACACS, introduced in
       1990.

       The third one is TACACS+ (or T+ or tac_plus) which is what is documented here.  TACACS+ is NOT  COMPATIBLE
       with any previous versions of tacacs.

AUTHOR
       The  tac_plus  (tacacs+)  developer's  kit  is  a  product  of  Cisco Systems, written by Lol Grant.  Made
       available at no cost and with no warranty of any kind.  See the file COPYING and source  files  that  came
       with the distribution for specifics.

       Though  heavily  modified from the original Cisco manual pages, much of the modifications are derived from
       the tacacs IETF draft and the Cisco user guide.

                                                   28 July 2009                                       tac_plus(8)
```

