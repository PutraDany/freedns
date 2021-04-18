# freedns

###ddclient

Client Requirement: Perl
ddclient supports many services, including 'freedns'

Sample config:

daemon=5m
timeout=10
syslog=no # log update msgs to syslog
#mail=root # mail all msgs to root
#mail-failure=root # mail failed update msgs to root
pid=/var/run/ddclient.pid # record PID in file.
ssl=yes # use ssl-support. Works with
# ssl-library

use=if, if=eth0
server=freedns.afraid.org
protocol=freedns
login=login_name
password=the_password
somedomain.mooo.com

# Sample #2 (Provided 2013-08-20 by Anki Borgh who reported difficulty with example above)
# use=your_router #If supported, can be listed with ddclient --help
# server=freedns.afraid.org
# protocol=dyndns1
# login=login_name
# password=the_password
# somedomain.mooo.com

Update #3 2013-09-17 as noted by user Harald Brinkmann:
Using ddclient for afraid.org requires ddclient version 3.8.1 (that's the latest version available) and "protocol" *must* be set to "freedns". Using ddclient 3.8.0 produces weird results and a couple of protocol options sort-of work, but not really. I guess that that is the source of the problem Anki Borgh reported. Maybe you can update your info for the benefit of the next person struggling with setting ddclient.

Update #4 2014-07-23 Pásztor Szilárd reports that "username" must be lowercase or else you will receive "could not authenticate" error.

###Dynamic Ip Script
################### 
##
##  FreeDNS.Afraid.org Dynamic IP script
##  v1.0
##  April 9, 2010
##
###################

use Sys::Hostname;
use LWP::UserAgent;

## set log file and ip file names
## default path is current folder, can change it to whatever... ie: /var/log or c:/temp
$logfile   = 'fda-ip.log';
$ipfile    = 'fda-ip.txt';

#clear the logfile
clear();

mark("START","Starting dynamic IP program");

while (1) {
    #read the previous ip address
    open S, "$ipfile";
    $prev_ip = <S>;
    close S;

    #get the current ip address
    ($name,$aliases,$addrtype,$length,@addrs) = gethostbyname(Sys::Hostname::ghname());

    ($a,$b,$c,$d) = unpack('C4',$addrs[0]);

    $ip = "$a.$b.$c.$d";

    if (!($ip eq $prev_ip)) {

        mark("CHANGE","Updating dynamic IPs");

        ###################################################
        # CUSTOM RECORD UPDATES GO HERE!!!
        ###################################################
        ## repeat for each A Record to be updated
        update("http://freedns.afraid.org/dynamic/update.php?your-update-url-here");
        ###################################################

        #update ip file
        open S, ">$ipfile";
        print S $ip;
        close S;
    }
    
    sleep(60);
}


sub update {
    my ($url) = @_;

    $type = "UPDATE";
    
    $ua = new LWP::UserAgent;
    $request = new HTTP::Request('GET', $url);
    $response = $ua->request($request);
    $result = $response->content();

    open  E, ">>$logfile";
    print localtime()."\t$type\t".$result."\n";
    print E localtime()."\t$type\t".$result."\n";
    close E;
}


sub mark {
    my ($type, $message) = @_;
    
    open  E, ">>$logfile";
    print localtime()."\t$type\t".$message."\n";
    print E localtime()."\t$type\t".$message."\n";
    close E;
}

sub clear {
    open  E, ">$logfile";
    close E;
}!



The SHA-1 string is your SHA hashed "username|password" (without quotes).

Important Notes:
Username must be LOWERCASE.
Password is CASE SENSITIVE.
Password is maximum length of 16 characters.

If you change your password, your API key and update URLs will also change.

Sample XML Output:

[xmlexport](https://user-images.githubusercontent.com/31497130/115133658-8254a780-a03c-11eb-9965-9434fc711a1a.gif)

