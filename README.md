# script-ficha-1-hora
script para ficha una hora autoeliminada mirotick routerOS
{
:local tdias 0
:local atime [ /system clock get time ]
:local adate [ /system clock get date ]
:local temp1 [:pick $adate 0 3] 
:local temp2 [:pick $adate 4 6]
:local temp3 [:pick $atime 0 5]
:local temp4 ($temp2."/".$temp1." - ".$temp3."  Dias: ".$tdias);
:local thoras 1
:if ([ /ip hotspot user get $user comment ]="") do={ [ /ip hotspot user set $user comment=$temp4 ] }

 /log info "Usuario= $user fecha=$adate hora=$atime Dias=$tdias Plan=Mr_x1Hora www.mrfichas.com ";

:local mesarray ("jan","feb","mar","apr","may","jun","jul","aug","sep","oct","nov","dec")
:local diaarray ("31","28","31","30","31","30","31","31","30","31","30","31")
:local day [:pick $adate 4 6]
:local monthtxt [:pick $adate 0 3]
:local year [:pick $adate 7 11]
:local months ([:find $mesarray $monthtxt])
:local dia ([:pick $diaarray $months])
:local fhora [:pick $atime 0 2]
:local fminutos [:pick $atime 3 5]
:local fsegundos [:pick $atime 6 9]
:local mayorhora (($fhora+$thoras)-24) 

 :if (($fhora+$thoras)>23) do={
     :set atime ("0".$mayorhora.":".$fminutos.":".fsegundos);
	 :set $tdias ($tdias+1);
	} else={
     :set atime (($fhora+$thoras).":".$fminutos.":".fsegundos) ;
 }
 

 :if (($day+$tdias)>$dia) do={
    :if ($months=11) do={
        
     :set months 0;
        
     :set year ($year+1);
        
     :set day (($day+$tdias)-$dia);
        
     :local mes ([:pick $mesarray $months]);
        
     :set adate ($mes."/".$day."/".$year);
        
     } else={
           
       :set months ($months+1);
           
       :set day (($day+$tdias)-$dia);
           
       :local mes ([:pick $mesarray $months]);
           
       :set adate ($mes."/".$day."/".$year);
        
       }
    
        } else={
        
          :set day ($day+$tdias);
        
          :local mes ([:pick $mesarray $months]);
        
          :set adate ($mes."/".$day."/".$year);
    
     }

[/system scheduler add interval=5m name=$user on-event="/ip hotspot user remove \"$user\" \r\
  \n/system scheduler remove [find name=\"$user\"] \r\
  \n/ip hotspot active remove [find user=\"$user\"] \r\
  \n/\r\
" start-date=$adate start-time=$atime]
 
}
