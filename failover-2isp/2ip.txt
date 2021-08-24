:local device [/system identity get name]
:global ISP1STATUS
:global ISP2STATUS
:global ISPNOW

if ([:len $ISPNOW] = 0) do={ :set $ISPNOW 0 }
:local PingIP [:toarray "8.8.8.8, 1.1.1.1"]
:local PingInterval 1s
:local PingCount 10
:local PingResult1 [/ping [:pick $PingIP 0] interval=$PingInterval count=$PingCount]
:local PingResult2 [/ping [:pick $PingIP 1] interval=$PingInterval count=$PingCount]

if ($PingResult1 <= ($PingCount / 2 + 1)) do={ :set $ISP1STATUS 0 } else { :set $ISP1STATUS 1 }
if ($PingResult2 <= ($PingCount / 2 + 1)) do={ :set $ISP2STATUS 0 } else { :set $ISP2STATUS 1 }

if ($ISP1STATUS = 0 && $ISP2STATUS = 1) do={
   if ($ISPNOW != 2) do={
      :log warning "Switch to ISP2"
      :foreach j in=[/interface l2tp-client find] do={
         /interface l2tp-client disable number=$j
         :log warning ">>> Disable l2tp-client"
      }
      /ip route set disable=yes [find comment=isp1]
	  /ip route set disable=yes [find comment=isp3]
      :log warning ">>> Disabled route ISP1 and ISP3"  
      /ip route set disable=no [find comment=isp2]
      :log warning ">>> Enabled route ISP2"
      :foreach j in=[/interface l2tp-client find] do={
         /interface l2tp-client enable number=$j
         :log warning ">>> Enable l2tp-client"
      }
	  :set $ISPNOW 2
      /tool e-mail send from="tester213@yandex.ru" server=smtp.yandex.ru to=logs@yandex.ru subject="$device - Switch to isp2"  body="----"
   }
}

if ($ISP1STATUS = 0 && $ISP2STATUS = 0) do={
   if ($ISPNOW != 3) do={
      :log warning "Switch to ISP3"
      :foreach j in=[/interface l2tp-client find] do={
         /interface l2tp-client disable number=$j
         :log warning ">>> Disable l2tp-client"
      }
      /ip route set disable=yes [find comment=isp1]
	  /ip route set disable=yes [find comment=isp2]
      :log warning ">>> Disabled route ISP1 and ISP2"  
      /ip route set disable=no [find comment=isp3]
      :log warning ">>> Enabled route ISP3"
      :foreach j in=[/interface l2tp-client find] do={
         /interface l2tp-client enable number=$j
         :log warning ">>> Enable l2tp-client"
      }
	  :set $ISPNOW 3
      /tool e-mail send from="tester213@yandex.ru" server=smtp.yandex.ru to=logs@yandex.ru subject="$device - Switch to isp2"  body="----"
   }
}

if ($ISP1STATUS = 1) do={
   if ($ISPNOW != 1) do={
      :log warning "Switch to ISP1"
	  :foreach j in=[/interface l2tp-client find] do={
         /interface l2tp-client disable number=$j
         :log warning ">>> Disable l2tp-client"
      }
      /ip route set disable=yes [find comment=isp2]
	  /ip route set disable=yes [find comment=isp3]
      :log warning ">>> Disabled route ISP2 and ISP3"  
      /ip route set disable=no [find comment=isp1]
      :log warning ">>> Enabled route ISP1"
      :foreach j in=[/interface l2tp-client find] do={
         /interface l2tp-client enable number=$j
         :log warning ">>> Enable l2tp-client"
      }
	  :set $ISPNOW 1
      /tool e-mail send from="tester213@yandex.ru" server=smtp.yandex.ru to=logs@yandex.ru subject="$device - Switch to isp1"  body="----"
   }
}
