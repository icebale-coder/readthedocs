title: Descrambler

Love is...

```perl
#!/usr/bin/perl -w
use strict;

     my$f=           $[;my
   $ch=0;sub       l{length}
 sub r{join"",   reverse split
("",$_[$[])}sub ss{substr($_[0]
,$_[1],$_[2])}sub be{$_=$_[0];p
 (ss($_,$f,1));$f+=l()/2;$f%=l 
  ();$f++if$ch%2;$ch++}my$q=r
   ("\ntfgpfdfal,thg?bngbj".    
    "naxfcixz");$_=$q; $q=~
      tr/f[a-z]/ [l-za-k] 
        /;my@ever=1..&l
          ;my$mine=$q
            ;sub p{
             print
              @_;
               }
                         
       be $mine for @ever
```