# Project-Voucher
Voucher Internet

# jun/05/2024 09:36:55 by RouterOS 6.49.10
# software id = 8V43-HUFL
#
# model = RB750Gr3
# serial number = CC210E153BD1
/interface ethernet
set [ find default-name=ether1 ] comment=ISP1
set [ find default-name=ether2 ] comment=LOKAL
set [ find default-name=ether4 ] comment=HOTSPOT
/interface l2tp-client
add connect-to=103.31.132.90 disabled=no name=l2tp-out1 password=\
    rb750gr3voucher user=rb750gr3voucher
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip hotspot profile
set [ find default=yes ] dns-name=nhtvoucheran.com hotspot-address=10.20.30.1 \
    html-directory=flash/hotspot5/hotspot5 login-by=\
    http-chap,http-pap,mac-cookie
add dns-name=nhtvoucheran.com hotspot-address=10.20.30.1 html-directory=\
    flash/hotspot5/hotspot5 login-by=http-chap,http-pap,mac-cookie name=\
    hsprof1
/ip hotspot user profile
set [ find default=yes ] rate-limit=3M/5M
add name=Mingguan on-login=":put (\",remc,25000,7d,25000,,Disable,\"); {:local\
    \_comment [ /ip hotspot user get [/ip hotspot user find where name=\"\$use\
    r\"] comment]; :local ucode [:pic \$comment 0 2]; :if (\$ucode = \"vc\" or\
    \_\$ucode = \"up\" or \$comment = \"\") do={ :local date [ /system clock g\
    et date ];:local year [ :pick \$date 7 11 ];:local month [ :pick \$date 0 \
    3 ]; /sys sch add name=\"\$user\" disable=no start-date=\$date interval=\"\
    7d\"; :delay 5s; :local exp [ /sys sch get [ /sys sch find where name=\"\$\
    user\" ] next-run]; :local getxp [len \$exp]; :if (\$getxp = 15) do={ :loc\
    al d [:pic \$exp 0 6]; :local t [:pic \$exp 7 16]; :local s (\"/\"); :loca\
    l exp (\"\$d\$s\$year \$t\"); /ip hotspot user set comment=\"\$exp\" [find\
    \_where name=\"\$user\"];}; :if (\$getxp = 8) do={ /ip hotspot user set co\
    mment=\"\$date \$exp\" [find where name=\"\$user\"];}; :if (\$getxp > 15) \
    do={ /ip hotspot user set comment=\"\$exp\" [find where name=\"\$user\"];}\
    ;:delay 5s; /sys sch remove [find where name=\"\$user\"]; :local mac \$\"m\
    ac-address\"; :local time [/system clock get time ]; /system script add na\
    me=\"\$date-|-\$time-|-\$user-|-25000-|-\$address-|-\$mac-|-7d-|-Mingguan-\
    |-\$comment\" owner=\"\$month\$year\" source=\"\$date\" comment=\"mikhmon\
    \"}}" parent-queue=none rate-limit=2M/2M
add name=Harian on-login=":put (\",remc,5000,1d,5000,,Disable,\"); {:local com\
    ment [ /ip hotspot user get [/ip hotspot user find where name=\"\$user\"] \
    comment]; :local ucode [:pic \$comment 0 2]; :if (\$ucode = \"vc\" or \$uc\
    ode = \"up\" or \$comment = \"\") do={ :local date [ /system clock get dat\
    e ];:local year [ :pick \$date 7 11 ];:local month [ :pick \$date 0 3 ]; /\
    sys sch add name=\"\$user\" disable=no start-date=\$date interval=\"1d\"; \
    :delay 5s; :local exp [ /sys sch get [ /sys sch find where name=\"\$user\"\
    \_] next-run]; :local getxp [len \$exp]; :if (\$getxp = 15) do={ :local d \
    [:pic \$exp 0 6]; :local t [:pic \$exp 7 16]; :local s (\"/\"); :local exp\
    \_(\"\$d\$s\$year \$t\"); /ip hotspot user set comment=\"\$exp\" [find whe\
    re name=\"\$user\"];}; :if (\$getxp = 8) do={ /ip hotspot user set comment\
    =\"\$date \$exp\" [find where name=\"\$user\"];}; :if (\$getxp > 15) do={ \
    /ip hotspot user set comment=\"\$exp\" [find where name=\"\$user\"];};:del\
    ay 5s; /sys sch remove [find where name=\"\$user\"]; :local mac \$\"mac-ad\
    dress\"; :local time [/system clock get time ]; /system script add name=\"\
    \$date-|-\$time-|-\$user-|-5000-|-\$address-|-\$mac-|-1d-|-Harian-|-\$comm\
    ent\" owner=\"\$month\$year\" source=\"\$date\" comment=\"mikhmon\"}}" \
    parent-queue=none rate-limit=1M/1M
/ip pool
add name=dhcp_pool1 ranges=192.12.0.10-192.12.0.254
add name=hs-pool-4 ranges=10.20.30.10-10.20.30.254
/ip dhcp-server
add address-pool=dhcp_pool1 disabled=no interface=ether2 name=dhcp1
add address-pool=hs-pool-4 disabled=no interface=ether4 lease-time=1h name=\
    dhcp2
/ip hotspot
add address-pool=hs-pool-4 addresses-per-mac=1 disabled=no interface=ether4 \
    name=hotspot1 profile=hsprof1
/ip neighbor discovery-settings
set discover-interface-list=!dynamic
/ip address
add address=192.12.0.1/24 interface=ether2 network=192.12.0.0
add address=10.20.30.1/24 interface=ether4 network=10.20.30.0
/ip dhcp-client
add disabled=no interface=ether1
/ip dhcp-server network
add address=10.20.30.0/24 comment="hotspot network" gateway=10.20.30.1
add address=192.12.0.0/24 gateway=192.12.0.1
/ip dns
set allow-remote-requests=yes servers=8.8.8.8,8.8.4.4,1.1.1.1,1.0.0.1
/ip firewall filter
add action=passthrough chain=unused-hs-chain comment=\
    "place hotspot rules here" disabled=yes
/ip firewall mangle
add action=change-ttl chain=postrouting new-ttl=set:1 out-interface=ether4 \
    passthrough=no
/ip firewall nat
add action=passthrough chain=unused-hs-chain comment=\
    "place hotspot rules here" disabled=yes
add action=masquerade chain=srcnat out-interface=ether1
add action=masquerade chain=srcnat comment="masquerade hotspot network" \
    src-address=10.20.30.0/24
/ip hotspot user
add name=admin password=admin
add comment=up-507-05.22.24- limit-uptime=1d name=piux password=6298 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=pfxx password=3364 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=eynx password=8998 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=cgkt password=3429 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=czjc password=2865 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=yvdv password=2533 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=imin password=2789 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=cvyb password=8594 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=ymke password=9562 profile=\
    Harian
add comment=up-507-05.22.24- limit-uptime=1d name=rgup password=4542 profile=\
    Harian
add comment=up-911-05.22.24- limit-uptime=1w name=yrbs password=3996 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=etht password=5353 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=wxdf password=9957 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=pvfz password=5778 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=ffkr password=6584 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=kyjb password=4868 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=cszx password=4683 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=vevz password=3633 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=jetz password=9465 profile=\
    Mingguan
add comment=up-911-05.22.24- limit-uptime=1w name=rmjt password=2868 profile=\
    Mingguan
/ip hotspot walled-garden
add dst-host=raudahwisata.co.id/im3-point/front/konfirmasi/001/00120
add dst-host=raudahwisata.co.id/Im3-point/front/simpan_konfirmasi
add dst-host=raudahwisata.co.id/im3-point/
add dst-host=raudahwisata.co.id
add dst-host=raudahwisata.co.id/im3-point/001/
add dst-host=raudahwisata.co.id/im3-point/002/
add dst-host=raudahwisata.co.id/Im3-point/admin/dashboard
add dst-host=nhtvoucheran.com
add dst-host=*raudahwisata.co.id/Im3-point/001*
add dst-host=raudahwisata.co.id/Im3-point/front/konfirmasi/001/00128
add dst-host=oase.co.id
add dst-host=google.com
/ip route
add distance=1 dst-address=192.11.0.0/24 gateway=l2tp-out1
/system clock
set time-zone-name=Asia/Jakarta
/system logging
add action=disk prefix=-> topics=hotspot,info,debug
/system scheduler
add comment="Monitor Profile Mingguan" interval=2m29s name=Mingguan on-event="\
    :local dateint do={:local montharray ( \"jan\",\"feb\",\"mar\",\"apr\",\"m\
    ay\",\"jun\",\"jul\",\"aug\",\"sep\",\"oct\",\"nov\",\"dec\" );:local days\
    \_[ :pick \$d 4 6 ];:local month [ :pick \$d 0 3 ];:local year [ :pick \$d\
    \_7 11 ];:local monthint ([ :find \$montharray \$month]);:local month (\$m\
    onthint + 1);:if ( [len \$month] = 1) do={:local zero (\"0\");:return [:to\
    num (\"\$year\$zero\$month\$days\")];} else={:return [:tonum (\"\$year\$mo\
    nth\$days\")];}}; :local timeint do={ :local hours [ :pick \$t 0 2 ]; :loc\
    al minutes [ :pick \$t 3 5 ]; :return (\$hours * 60 + \$minutes) ; }; :loc\
    al date [ /system clock get date ]; :local time [ /system clock get time ]\
    ; :local today [\$dateint d=\$date] ; :local curtime [\$timeint t=\$time] \
    ; :foreach i in [ /ip hotspot user find where profile=\"Mingguan\" ] do={ \
    :local comment [ /ip hotspot user get \$i comment]; :local name [ /ip hots\
    pot user get \$i name]; :local gettime [:pic \$comment 12 20]; :if ([:pic \
    \$comment 3] = \"/\" and [:pic \$comment 6] = \"/\") do={:local expd [\$da\
    teint d=\$comment] ; :local expt [\$timeint t=\$gettime] ; :if ((\$expd < \
    \$today and \$expt < \$curtime) or (\$expd < \$today and \$expt > \$curtim\
    e) or (\$expd = \$today and \$expt < \$curtime)) do={ [ /ip hotspot user r\
    emove \$i ]; [ /ip hotspot active remove [find where user=\$name] ];}}}" \
    policy=ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon \
    start-date=may/22/2024 start-time=03:32:41
add comment="Monitor Profile Harian" interval=2m39s name=Harian on-event=":loc\
    al dateint do={:local montharray ( \"jan\",\"feb\",\"mar\",\"apr\",\"may\"\
    ,\"jun\",\"jul\",\"aug\",\"sep\",\"oct\",\"nov\",\"dec\" );:local days [ :\
    pick \$d 4 6 ];:local month [ :pick \$d 0 3 ];:local year [ :pick \$d 7 11\
    \_];:local monthint ([ :find \$montharray \$month]);:local month (\$monthi\
    nt + 1);:if ( [len \$month] = 1) do={:local zero (\"0\");:return [:tonum (\
    \"\$year\$zero\$month\$days\")];} else={:return [:tonum (\"\$year\$month\$\
    days\")];}}; :local timeint do={ :local hours [ :pick \$t 0 2 ]; :local mi\
    nutes [ :pick \$t 3 5 ]; :return (\$hours * 60 + \$minutes) ; }; :local da\
    te [ /system clock get date ]; :local time [ /system clock get time ]; :lo\
    cal today [\$dateint d=\$date] ; :local curtime [\$timeint t=\$time] ; :fo\
    reach i in [ /ip hotspot user find where profile=\"Harian\" ] do={ :local \
    comment [ /ip hotspot user get \$i comment]; :local name [ /ip hotspot use\
    r get \$i name]; :local gettime [:pic \$comment 12 20]; :if ([:pic \$comme\
    nt 3] = \"/\" and [:pic \$comment 6] = \"/\") do={:local expd [\$dateint d\
    =\$comment] ; :local expt [\$timeint t=\$gettime] ; :if ((\$expd < \$today\
    \_and \$expt < \$curtime) or (\$expd < \$today and \$expt > \$curtime) or \
    (\$expd = \$today and \$expt < \$curtime)) do={ [ /ip hotspot user remove \
    \$i ]; [ /ip hotspot active remove [find where user=\$name] ];}}}" \
    policy=ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon \
    start-date=may/22/2024 start-time=02:11:11
